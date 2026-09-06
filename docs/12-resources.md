# 12 — Tài nguyên học tập (100% miễn phí)

> Nguyên tắc 1:3 — mỗi 1 giờ xem/đọc phải kèm 3 giờ code. Danh sách này để **tra cứu khi cần**, không phải để học hết.

---

## Tài liệu nền tảng (dùng suốt 12 tháng)

| Tài nguyên | Loại | Vì sao quan trọng |
|---|---|---|
| **Godot Docs** (docs.godotengine.org) | Tài liệu chính thức | Chất lượng cao hiếm có. "Step by step" và "Your first 2D game" là bắt buộc |
| **Game Programming Patterns** — Robert Nystrom (gameprogrammingpatterns.com) | Sách, đọc online free | ⭐ Cuốn quan trọng nhất cho một lập trình viên chuyển sang game |
| **Godot Recipes** (kidscancode.org/godot_recipes) | Công thức theo vấn đề | Tra cứu "làm sao để X" |
| **Red Blob Games** (redblobgames.com) | Bài giảng tương tác | ⭐ Toán game giải thích tuyệt vời: A*, hex grid, curve, noise |
| **Godot Shaders** (godotshaders.com) | Thư viện shader free | Copy về học và dùng |

---

## Theo giai đoạn

### P0–P1 (Tuần 0–4): Nền tảng Godot

| Nguồn | Nội dung |
|---|---|
| Godot Docs — *"Your first 2D game" (Dodge the Creeps)* | Làm nguyên bài trong Tuần 1 |
| **GDQuest** (YouTube + gdquest.com, nhiều tài liệu FOSS) | Loạt Godot 4 2D, chất lượng cao nhất |
| **HeartBeast** (YouTube) | Loạt Action RPG / Platformer — hợp thể loại bạn thích |
| **Brackeys — Godot series** (YouTube) | Ngắn gọn, sản xuất chỉn chu, hợp người mới |
| **"Juice it or lose it"** — GDC talk 15' | ⭐ Xem trong Tuần 1. Thay đổi hoàn toàn cách bạn nghĩ về game feel |
| **"Math for Game Developers"** — Jorge Rodriguez (YouTube) | Vector/toán 2D theo nhu cầu |
| **Freya Holmér** (YouTube) | ⭐ "The Continuity of Splines", "Math for Game Devs" — giải thích trực quan bậc nhất |

### P2 (Tuần 5–10): Ship game đầu tiên

| Nguồn | Nội dung |
|---|---|
| **"The Art of Screenshake"** — Jan Willem Nijman (Vlambeer), GDC 20' | ⭐⭐ Bắt buộc xem trước Tuần 7. Đây là toàn bộ tuần juice của bạn cô đọng trong 20 phút |
| **Kenney.nl assets** | Asset CC0 cho Tuần 7 |
| **jsfxr** (sfxr.me) | SFX |
| **itch.io — "How to make a good game page"** | Hướng dẫn trang store |
| **r/godot, r/IndieDev, r/gamedev** | Đăng và nhận phản hồi |
| **Godot Discord** (discord.gg/godotengine) | `#2d-help`, `#showcase` |

### P3 (Tuần 11–16): Kỹ thuật

| Nguồn | Nội dung |
|---|---|
| **GUT** (github.com/bitwes/Gut) — wiki | Unit test cho Godot |
| **gdtoolkit** (github.com/Scony/godot-gdscript-toolkit) | `gdformat`, `gdlint` |
| **godot-ci** (github.com/abarichello/godot-ci) | Docker image cho GitHub Actions |
| **Godot Docs — Optimization** | "Optimization using Servers", "General optimization tips" |
| **The Book of Shaders** (thebookofshaders.com) | ⭐ Học GLSL từ số 0, tương tác |
| **Godot Docs — Shading Language** | Cú pháp riêng của Godot |
| **Refactoring Guru** | Design pattern (bạn đã biết, dùng để đối chiếu) |

### P4 (Tuần 17–28): Combat & gameplay

| Nguồn | Nội dung |
|---|---|
| **"Fighting Game Netcode / frame data" — Infil's Netcode Guide** | Nền tảng khái niệm fighting game |
| **Sajam, Core-A Gaming (YouTube)** | Thiết kế game đối kháng — vì sao đòn này thắng đòn kia |
| **"Behavior Trees for AI" — Chris Simpson (Gamasutra/GameDeveloper)** | ⭐ Bài viết kinh điển về BT |
| **Game AI Pro** (gameaipro.com — sách miễn phí online) | AI chuyên sâu, tra cứu |
| **Godot Docs — AnimationPlayer, Skeleton2D** | Skeletal animation 2D |
| **"Secrets of Game Feel and Juice"** — GameMaker's Toolkit | Hitstop, screen shake, animation curve |
| **Bevouliin / Mixamo-style 2D rigging tutorials** | Rig 2D bằng Godot |

### P5 (Tuần 29–40): Server-side

| Nguồn | Nội dung |
|---|---|
| **Gabriel Gambetta — "Fast-Paced Multiplayer"** (4 phần, demo tương tác) | ⭐⭐ Tài liệu tốt nhất về client prediction & reconciliation. Đọc trước tiên |
| **Gaffer On Games** — Glenn Fiedler | ⭐ "Networked Physics", "Reliable UDP", "Snapshot Compression", "Fix Your Timestep" |
| **Valve Developer Wiki — "Source Multiplayer Networking"** | Lag compensation kinh điển |
| **Godot Docs — High-level multiplayer + WebRTC** | API cụ thể |
| **GGPO** (github.com/pond3r/ggpo) | Mã nguồn rollback netcode |
| **"Netcode: Rollback vs Delay" — Infil** | So sánh mô hình cho fighting game |
| **Nakama** (heroiclabs, OSS) / **Colyseus** (OSS) | Tham khảo kiến trúc game server — đọc code |
| **PostgreSQL Docs, Redis Docs** | Bạn đã quen, chỉ tra khi cần |
| **Fastify docs, Zod docs, Vitest docs** | Backend stack |
| **"Designing Data-Intensive Applications"** (nếu mua được) hoặc **Martin Kleppmann's talks** (free) | Nền tảng hệ thống phân tán |

### P6 (Tuần 41–48): Ship & grow

| Nguồn | Nội dung |
|---|---|
| **Chris Zukowski — howtomarketagame.com** | ⭐ Blog marketing indie tốt nhất, nhiều bài free |
| **"How to make a trailer" — Derek Lieu** (blog + YouTube) | Trailer game |
| **GDC Vault — free talks** | Postmortem của các indie thành công |
| **Kdenlive / Shotcut** | Dựng video FOSS |
| **itch.io devlog + Steam "Marketing your game"** | Kênh phát hành |

---

## Cộng đồng

| Cộng đồng | Dùng để |
|---|---|
| **Godot Discord chính thức** | Hỏi kỹ thuật — trả lời nhanh nhất |
| **r/godot** | Hỏi, khoe tiến độ |
| **r/gamedev, r/IndieDev, r/devblogs** | Thảo luận rộng |
| **r/playmygame** | Xin playtest |
| **Godot Forums (forum.godotengine.org)** | Câu hỏi dài, tìm kiếm được |
| **TIGSource Forums** | Devlog dài hạn, cộng đồng chất lượng |
| **Gamedev Việt Nam** (Facebook/Discord) | Cộng đồng tiếng Việt, dễ tìm playtester |
| **Mastodon / Bluesky `#gamedev` `#screenshotsaturday`** | Xây khán giả |

---

## Game jam (nên tham gia từ P3 trở đi)

| Jam | Tần suất | Vì sao nên tham gia |
|---|---|---|
| **Godot Wild Jam** | Hàng tháng, 9 ngày | ⭐ Cộng đồng Godot, quy mô vừa, thân thiện |
| **Ludum Dare** | 3 tháng/lần, 48–72h | Kinh điển, phản hồi tốt |
| **GMTK Game Jam** | Năm 1 lần, 48h | Lớn nhất, chủ đề hay |
| **Brackeys Game Jam** | ~2 lần/năm, 7 ngày | Thân thiện với người mới |
| **js13kGames** | Tháng 9, 1 tháng | Nếu muốn thử JS/TS + ràng buộc 13KB |

> **Giá trị thật của game jam với bạn:** ép hoàn thành trong thời gian ngắn — đúng thứ kỹ năng bạn cần rèn nhất. Tham gia 1 jam sau P3 và 1 jam trong "tuần nguy hiểm" 22–24 của P4.

---

## Cách sử dụng danh sách này

1. **Không mở quá 2 nguồn cùng lúc.** Bookmark, đừng đọc hết.
2. Mỗi giai đoạn, chọn **1 nguồn chính** (thường là Godot Docs) + **1 nguồn bổ trợ**.
3. Video: xem **1 lần với tốc độ 1.5x**, rồi tự làm lại **không xem lại**. Nếu kẹt mới tua lại.
4. Ghi lại điều học được vào devlog tuần — viết ra là cách kiểm tra mình có thật sự hiểu.
5. **Tutorial hell là có thật.** Nếu tuần nào bạn xem nhiều hơn code, tuần sau cấm xem video.
