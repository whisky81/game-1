# 13 — Checklist kỹ năng (ánh xạ từ 2 roadmap gốc)

Đánh dấu `[x]` khi bạn **đã dùng kỹ năng đó trong một dự án thật**, không phải khi đã xem video về nó.
Review vào cuối mỗi tháng.

---

## A. Từ roadmap.sh/game-developer

### A1. Nền tảng lập trình game
- [ ] Game loop, fixed timestep vs variable timestep, `delta`
- [ ] `_process` vs `_physics_process` — biết chọn đúng
- [ ] Scene tree / node hierarchy / composition
- [ ] Signal / event-driven programming
- [ ] Resource & data-driven design
- [ ] Coroutine / async (`await` trong GDScript)
- [ ] Static typing trong GDScript và lý do dùng

### A2. Toán học (2D — đủ dùng cho năm 1)
- [ ] Vector2: cộng, trừ, scale, `length`, `normalized`
- [ ] Dot product — ứng dụng: góc nhìn, phản xạ, chiếu
- [ ] Cross product 2D (z-component) — ứng dụng: xác định trái/phải
- [ ] Lerp / inverse lerp / remap
- [ ] Lerp độc lập framerate (`1 - exp(-rate * delta)`)
- [ ] Easing curves (`Tween.TRANS_*`)
- [ ] Góc, `atan2`, xoay vector
- [ ] Đường cong Bézier / Curve2D (đường bay đạn, animation path)
- [ ] Noise (Perlin/Simplex) — dùng cho screen shake và procedural
- [ ] Ma trận biến đổi 2D (`Transform2D`) — hiểu ở mức dùng được
- [ ] ⏸ *3D: quaternion, ma trận 4x4 — hoãn sang năm 2*

### A3. Vật lý & va chạm
- [ ] `Area2D` vs `CharacterBody2D` vs `RigidBody2D` — chọn đúng
- [ ] Collision layer & mask — thiết kế bảng rõ ràng
- [ ] AABB, circle collision — hiểu nguyên lý bên dưới
- [ ] Raycast / shapecast
- [ ] Tunneling và cách tránh (continuous collision detection)
- [ ] `move_and_slide` vs `move_and_collide`
- [ ] Hitbox/hurtbox theo animation frame
- [ ] Physics material: bounce, friction

### A4. Đồ họa & render (mức ứng dụng)
- [ ] Sprite, atlas, texture filter (nearest cho pixel art!)
- [ ] Z-index / Y-sort
- [ ] Camera2D: limit, smoothing, drag margin
- [ ] Parallax layer
- [ ] Viewport & resolution scaling (quan trọng cho pixel art)
- [ ] Canvas shader: hit flash, outline, dissolve, palette swap
- [ ] Particle system (`GPUParticles2D`)
- [ ] Light2D & normal map (tùy chọn)
- [ ] Draw call, batching — hiểu vì sao ảnh hưởng iGPU
- [ ] ❌ *OpenGL/Vulkan API trực tiếp — không cần cho lộ trình này*

### A5. Design pattern trong game
- [ ] State (+ hierarchical state machine)
- [ ] Observer / Event bus
- [ ] Command (input buffer, replay, undo)
- [ ] Object Pool
- [ ] Component / Composition over inheritance
- [ ] Service Locator (autoload) — và biết giới hạn của nó
- [ ] Flyweight (shared Resource)
- [ ] Strategy (AI behavior, weapon behavior)

### A6. Gameplay systems
- [ ] Input handling: action map, buffer, gamepad, remap
- [ ] Character controller có game feel (coyote, buffer, variable jump)
- [ ] Animation state machine
- [ ] Damage / health / status effect system
- [ ] Inventory / equipment
- [ ] Progression & XP curve
- [ ] Save/load (JSON + versioning để tương thích ngược)
- [ ] Wave/spawn system data-driven
- [ ] AI: FSM
- [ ] AI: Behavior Tree
- [ ] Pathfinding (`AStarGrid2D`, `NavigationAgent2D`)
- [ ] Difficulty tuning & balance

### A7. Audio
- [ ] Audio bus, volume theo nhóm
- [ ] Pitch variation chống lặp
- [ ] Giới hạn số kênh SFX
- [ ] Nhạc chuyển cảnh mượt (crossfade)
- [ ] Đồng bộ audio–gameplay (nếu làm ý tưởng #7)

### A8. UI/UX
- [ ] Control node, anchor, container — layout responsive
- [ ] Theme resource (đồng nhất giao diện)
- [ ] Điều hướng bằng bàn phím/gamepad trong menu
- [ ] Accessibility: cỡ chữ, tắt shake, colorblind
- [ ] Localization (CSV translation)

### A9. Quy trình & công cụ
- [ ] Git flow cá nhân + Conventional Commits + LFS
- [ ] Unit test cho logic thuần (GUT)
- [ ] Integration test bằng scene
- [ ] CI: test tự động
- [ ] CD: export & deploy tự động
- [ ] Linter/formatter (gdlint/gdformat)
- [ ] Profiling & tối ưu dựa trên số đo
- [ ] Export đa nền tảng (Linux, Web, Android)
- [ ] Editor tooling (`@tool`, EditorPlugin)
- [ ] Tài liệu: GDD, ADR, devlog, postmortem

---

## B. Từ roadmap.sh/server-side-game-developer

### B1. Nền tảng mạng
- [ ] TCP vs UDP — chọn đúng cho từng loại dữ liệu
- [ ] WebSocket / WebRTC (cần cho export Web)
- [ ] RTT, jitter, packet loss — đo và mô phỏng bằng `tc netem`
- [ ] Bandwidth budget, tick rate
- [ ] NAT traversal, relay server — hiểu vấn đề
- [ ] Serialization: JSON vs MessagePack vs Protobuf — biết đánh đổi
- [ ] Nén dữ liệu: delta compression, quantization, bit packing

### B2. Kiến trúc multiplayer
- [ ] Authoritative server — nguyên tắc "không tin client"
- [ ] Client-side prediction
- [ ] Server reconciliation
- [ ] Entity interpolation
- [ ] Lag compensation (rewind)
- [ ] Snapshot vs lockstep vs rollback — biết chọn theo thể loại
- [ ] Determinism & fixed-point/seeded RNG
- [ ] Xử lý disconnect & reconnect
- [ ] ⏸ *Rollback netcode đầy đủ — mục tiêu năm 2*

### B3. Backend services
- [ ] Auth: guest, JWT, refresh token
- [ ] Profile / player data
- [ ] Cloud save + conflict resolution (versioning)
- [ ] Leaderboard (Redis sorted set + persist Postgres)
- [ ] Matchmaking theo MMR
- [ ] Elo/Glicko rating
- [ ] Telemetry / analytics events
- [ ] REST API design + OpenAPI
- [ ] Input validation (Zod), error handling nhất quán

### B4. Dữ liệu
- [ ] PostgreSQL: schema design, index, migration
- [ ] Redis: cache, sorted set, pub/sub, TTL
- [ ] Transaction & idempotency (chống nộp điểm trùng)
- [ ] Backup & restore

### B5. Bảo mật & anti-cheat
- [ ] Server-side validation mọi hành động
- [ ] Rate limiting (IP + user)
- [ ] Replay verification (mô phỏng lại phía server)
- [ ] Phát hiện bất thường thống kê
- [ ] Bảo vệ secret, không đặt key trong client
- [ ] HTTPS/WSS, CORS
- [ ] Xử lý client độc hại: kick / ban / shadow ban

### B6. DevOps
- [ ] Docker + Docker Compose
- [ ] Dockerfile multi-stage
- [ ] CI/CD cho backend
- [ ] Structured logging
- [ ] Healthcheck & graceful shutdown
- [ ] Monitoring & alert cơ bản
- [ ] Deploy lên cloud free tier
- [ ] ⏸ *Kubernetes, autoscaling, dedicated server fleet — chỉ đọc hiểu*

---

## C. Kỹ năng không có trong roadmap gốc nhưng quyết định thành bại của solo dev

- [ ] **Cắt scope** — nói không với ý tưởng của chính mình
- [ ] **Hoàn thành** — đưa một thứ chưa hoàn hảo ra công chúng
- [ ] Game design: nhận biết vì sao một cơ chế vui
- [ ] Playtesting: quan sát mà không giải thích
- [ ] Art direction ở mức chọn phong cách & bảng màu
- [ ] Viết devlog đều đặn
- [ ] Trailer & trang store
- [ ] Xây dựng khán giả (`#screenshotsaturday`)
- [ ] Quản lý năng lượng cá nhân, chống kiệt sức

---

## Bảng theo dõi tiến độ theo tháng

| Tháng | Ngày review | Số mục hoàn thành | Ghi chú |
|-------|------------|-------------------|---------|
| 1 | | / | |
| 2 | | / | |
| 3 | | / | |
| 4 | | / | |
| 5 | | / | |
| 6 | | / | |
| 7 | | / | |
| 8 | | / | |
| 9 | | / | |
| 10 | | / | |
| 11 | | / | |
| 12 | | / | |
