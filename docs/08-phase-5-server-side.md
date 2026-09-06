# 08 — Giai đoạn 5: Server-side game development (Tuần 29–40, ~108h)

> Ánh xạ trực tiếp từ [roadmap.sh/server-side-game-developer](https://roadmap.sh/server-side-game-developer), cắt bỏ phần scaling quy mô lớn (không có người chơi thì không cần shard).
>
> **Lợi thế của bạn:** đây là giai đoạn bạn mạnh nhất — Node/TS, Docker, Linux, Postgres đều đã quen. Toàn bộ năng lượng học dồn vào **khái niệm netcode**, không phải công cụ.

**Hai sản phẩm song song:**
- **A. Backend services** (dễ hơn, giá trị thực tế cao) — auth, leaderboard, cloud save, telemetry
- **B. Multiplayer prototype** (khó hơn, giá trị học tập cao) — 2 client đánh nhau qua Internet

---

## Phần A — Backend services (Tuần 29–33)

### Tuần 29 — Nền tảng mạng cho game (lý thuyết + đo thật)

Đây là tuần lý thuyết duy nhất được phép "học nhiều hơn code".

| Khái niệm | Cần trả lời được |
|---|---|
| TCP vs UDP | Vì sao game real-time gần như luôn dùng UDP (hoặc WebRTC/WebSocket trên web)? Head-of-line blocking là gì? |
| RTT, jitter, packet loss | Ping 80ms nghĩa là gì với một đòn đánh 15 frame? |
| Bandwidth budget | 60 tick/s × 20 byte × 8 người = bao nhiêu KB/s? |
| Serialization | JSON vs MessagePack vs Protobuf — kích thước và chi phí CPU |
| NAT traversal | Vì sao P2P khó, vì sao relay server tồn tại |

**Bài thực hành đo thật (dùng Linux của bạn):**

```bash
# Mô phỏng độ trễ và mất gói trên loopback để test netcode sau này
sudo tc qdisc add dev lo root netem delay 80ms 20ms loss 2%
ping -c 5 127.0.0.1
sudo tc qdisc del dev lo root        # gỡ bỏ

# Đo kích thước payload
node -e "console.log(Buffer.byteLength(JSON.stringify({x:123.456,y:78.9,hp:100})))"
```

### Tuần 30 — Dựng backend cơ bản

Stack: **Node 22 + TypeScript + Fastify + PostgreSQL + Redis**, tất cả trong Docker Compose (bạn đã có Docker 29).

```yaml
# docker-compose.yml
services:
  db:
    image: postgres:17-alpine
    environment:
      POSTGRES_PASSWORD: dev
      POSTGRES_DB: gamedb
    ports: ["5432:5432"]
    volumes: [pgdata:/var/lib/postgresql/data]
  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]
  api:
    build: .
    ports: ["8080:8080"]
    environment:
      DATABASE_URL: postgres://postgres:dev@db:5432/gamedb
      REDIS_URL: redis://redis:6379
    depends_on: [db, redis]
volumes: { pgdata: }
```

Endpoint tối thiểu:

```
POST /v1/auth/guest          -> tạo tài khoản ẩn danh, trả JWT
POST /v1/auth/refresh
GET  /v1/profile/me
PUT  /v1/save                -> cloud save (JSON blob + version để chống ghi đè)
GET  /v1/save
POST /v1/scores              -> nộp điểm (có xác thực)
GET  /v1/leaderboard?limit=100&around=me
POST /v1/telemetry/events    -> batch events
```

**Best practice bắt buộc áp dụng ngay (bạn đã biết từ web dev, chỉ cần dùng):**
- Zod validate mọi input
- Rate limit theo IP + theo user (Redis)
- JWT ngắn hạn + refresh token
- Migration bằng `node-pg-migrate` hoặc Drizzle
- Test API bằng Vitest + Supertest — **TDD ở đây rất tự nhiên, hãy làm triệt để**
- OpenAPI spec sinh tự động

### Tuần 31 — Leaderboard & chống gian lận

Leaderboard là bài toán "server-side game dev" kinh điển:

- **Redis Sorted Set** cho bảng xếp hạng thời gian thực: `ZADD lb:weekly <score> <userId>`, `ZREVRANK` để lấy hạng
- Postgres lưu bền vững, Redis là lớp nhanh
- Bảng theo kỳ: all-time / weekly / daily (key có hậu tố ngày)

**Anti-cheat cho leaderboard (nguyên tắc, không cần hoàn hảo):**

| Kỹ thuật | Cách làm |
|---|---|
| Không tin client | Server không bao giờ nhận "điểm" trần trụi mà không có ngữ cảnh |
| Sanity check | Điểm > kỷ lục lý thuyết? Thời gian chơi < thời gian tối thiểu để đạt điểm đó? |
| Replay verification (mức cao) | Client gửi seed + chuỗi input; server chạy lại mô phỏng headless và so kết quả. **Khả thi vì game bạn deterministic** |
| HMAC payload | Ngăn chỉnh sửa request thô sơ (không chống được reverse-engineer, nhưng lọc 90% kẻ nghiệp dư) |
| Rate limit + phát hiện bất thường | Cùng user nộp 100 điểm/phút → cờ đỏ |
| Shadow ban | Cheater vẫn thấy điểm mình, người khác không thấy |

**Bài tập lớn của tuần:** làm **replay verification** cho Chicken Blaster. Vì game dùng fixed timestep và RNG có seed, server chạy lại được. Đây là một hạng mục portfolio rất ấn tượng.

### Tuần 32 — Tích hợp client Godot ↔ backend
- `HTTPRequest` node, wrapper `ApiClient` với retry + exponential backoff
- Xử lý offline: hàng đợi local, đồng bộ khi có mạng
- Chống chặn UI: mọi request bất đồng bộ, có trạng thái loading
- Lưu token an toàn (`user://`, hiểu rõ giới hạn: client luôn có thể bị đọc)

### Tuần 33 — Deploy & vận hành
- Dockerfile multi-stage, image nhỏ
- Deploy lên tier miễn phí: **Fly.io / Railway / Render / Oracle Cloud Always Free**
- Healthcheck, graceful shutdown, structured logging (pino)
- Monitoring miễn phí: Grafana Cloud free tier hoặc `docker stats` + log
- Backup DB tự động bằng cron + `pg_dump`

---

## Phần B — Multiplayer real-time (Tuần 34–40)

### Tuần 34 — Godot High-Level Multiplayer API
- `ENetMultiplayerPeer`, `MultiplayerSpawner`, `MultiplayerSynchronizer`
- RPC: `@rpc("any_peer", "call_local", "reliable")` vs `"unreliable_ordered"`
- Làm một demo: 2 người chơi di chuyển chấm tròn trong LAN
- Hiểu mô hình: ai là authority của node nào

### Tuần 35 — Authoritative server
Nguyên tắc nền: **client gửi input, server gửi trạng thái. Client không bao giờ nói "tôi trúng đòn rồi".**

```
Client:  thu input -> gửi {frame, input} -> dự đoán cục bộ ngay lập tức
Server:  gom input -> chạy mô phỏng ở tick cố định -> phát snapshot
Client:  nhận snapshot -> so với trạng thái dự đoán -> hòa giải nếu lệch
```

Nhiệm vụ: chuyển demo tuần 34 sang mô hình authoritative, chạy server headless:

```bash
godot --headless -- --server --port 9000
```

### Tuần 36 — Client prediction + reconciliation + interpolation

Ba kỹ thuật kinh điển (Gabler/Valve/Gaffer model):

| Kỹ thuật | Giải quyết vấn đề | Áp dụng cho |
|---|---|---|
| **Client-side prediction** | Input cảm giác tức thì dù ping cao | Nhân vật của chính mình |
| **Server reconciliation** | Sửa sai lệch giữa dự đoán và sự thật | Nhân vật của chính mình |
| **Entity interpolation** | Người chơi khác chuyển động mượt dù snapshot thưa | Mọi entity khác |
| **Lag compensation** (rewind) | Bắn trúng thứ mình *nhìn thấy* dù có độ trễ | Đòn đánh/đạn |

Kiểm thử bằng `tc netem` đã học ở Tuần 29: bật 150ms delay + 3% loss, game vẫn phải chơi được.

### Tuần 37 — Chọn mô hình netcode & implement

| Mô hình | Phù hợp | Ưu | Nhược |
|---|---|---|---|
| **Snapshot interpolation + prediction** | FPS, action, MMO | Chịu mất gói tốt, dễ mở rộng số người | Cần lag compensation, tốn băng thông |
| **Deterministic lockstep** | RTS, fighting game | Băng thông cực thấp | Phải deterministic tuyệt đối, chờ người chậm nhất |
| **Rollback netcode (GGPO-style)** | **Fighting game — chính là game P4 của bạn** | Cảm giác tốt nhất cho đối kháng | Khó nhất, cần save/restore state mỗi frame |

**Khuyến nghị:** implement **snapshot + prediction** trước (dễ hơn, an toàn hơn). Rollback chỉ làm nếu còn thời gian — nó là chủ đề cho năm 2, nhưng hãy đọc hiểu và ghi vào ADR vì sao chưa làm.

### Tuần 38 — Matchmaking & phòng chơi
- Redis lưu hàng đợi: `ZADD queue:1v1 <mmr> <userId>`
- Ghép cặp theo MMR với dải nới rộng dần theo thời gian chờ
- Vòng đời phòng: create → wait → ready check → in-game → result → cleanup
- Elo/Glicko đơn giản cho xếp hạng (logic thuần → **TDD**)
- Xử lý disconnect: grace period, cho reconnect, xử phạt rời trận

### Tuần 39 — Bảo mật & chịu lỗi
- Xác thực mọi input phía server (giới hạn tốc độ di chuyển, cooldown đòn đánh)
- Chống speed hack: server kiểm tra tick rate của client
- Rate limit RPC, giới hạn kích thước packet
- Xử lý client độc hại: kick, log, ban
- Chaos test: giết server giữa trận — client phải xử lý gọn, không treo

### Tuần 40 — Tổng hợp: prototype PvP hoạt động qua Internet
Ghép Phần A + Phần B: đăng nhập → vào hàng đợi → ghép trận → đánh nhau (bản rút gọn của game P4) → kết quả ghi vào leaderboard.

Chơi thử với một người bạn ở mạng khác. **Đó là khoảnh khắc mấu chốt của giai đoạn này.**

---

## Deliverable P5

- [ ] Backend chạy trên cloud free tier, có URL công khai, OpenAPI docs
- [ ] Test API ≥ 80% các endpoint
- [ ] Chicken Blaster có leaderboard online + replay verification
- [ ] Prototype PvP 1v1 chơi được qua Internet với ping 100ms+
- [ ] `docs/adr/00xx-netcode-model.md` giải thích lựa chọn
- [ ] `docs/netcode-notes.md` — ghi chú kỹ thuật của riêng bạn

## Tài nguyên trọng tâm (miễn phí)

- **Gabriel Gambetta — "Fast-Paced Multiplayer"** (4 bài, có demo tương tác): tài liệu tốt nhất cho prediction/reconciliation
- **Glenn Fiedler — Gaffer On Games**: "Networked Physics", "Snapshot Compression", "Reliable UDP"
- **Valve Developer Wiki — "Source Multiplayer Networking"**: lag compensation kinh điển
- **Godot Docs — High-level multiplayer**
- **GGPO** (mã nguồn mở) + bài viết về rollback của Infil ("Fighting Game Netcode")
- **"Making Multiplayer Games" — Ruoyu Sun / gafferongames reading list**
