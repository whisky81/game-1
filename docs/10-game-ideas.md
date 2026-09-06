# 10 — Ý tưởng game khả thi cho giai đoạn đầu

Tiêu chí lọc: **2D**, làm được trong 1–4 tuần với 9h/tuần, ít art, cơ chế lõi rõ ràng, và mỗi ý tưởng dạy một nhóm kỹ năng khác nhau.

Cột **Ưu tiên** cho biết ý tưởng đó hợp với giai đoạn nào trong lộ trình.

---

## #1 — Chicken Blaster ⭐ (dự án chính thức của [P2](05-phase-2-first-release.md))

| | |
|---|---|
| **Thể loại** | Arcade shoot-em-up dọc, wave-based |
| **Thời gian** | 5–6 tuần |
| **Gameplay** | Di chuyển 8 hướng ở nửa dưới màn hình, bắn tự động. Gà bay xuống theo đội hình, thả trứng. Bắn hạ để rơi "đùi gà", nhặt đủ thì nâng cấp súng. 10 wave rồi tới Gà Trùm 2 phase. |
| **Kỹ năng học** | Object pooling, wave spawner data-driven, đội hình bằng toán, damage system, boss pattern, juice/game feel, save/load, export web |
| **Art cần** | 3 sprite gà + 1 boss + player + 3 loại đạn + particle. **Có thể dùng 100% Kenney free assets** |
| **Vì sao hợp** | Đúng thể loại bạn thích, scope kiểm soát được, có điểm dừng rõ ràng, dễ tạo cảm giác "đã" |
| **Ưu tiên** | ⭐⭐⭐ Làm ở P2 |

---

## #2 — Silhouette Duel ⭐ (dự án chính thức của [P4](07-phase-4-combat-game.md))

| | |
|---|---|
| **Thể loại** | Đối kháng 1v1, arcade ladder |
| **Thời gian** | 10–12 tuần |
| **Gameplay** | Hai bóng đen đấu tay đôi trên nền hoàng hôn. 4 đòn (jab/heavy/kick/special), block, parry, dodge, stamina. Leo thang 10 đối thủ AI, nâng cấp chỉ số giữa các trận, 3 vũ khí. |
| **Kỹ năng học** | Skeletal animation 2D, frame data (startup/active/recovery), hitbox/hurtbox theo animation, input buffer & cancel, hitstop, Behavior Tree AI, cân bằng chiến đấu |
| **Art cần** | **Cực ít** — nhân vật là đa giác đen, nền là gradient + bóng cây. Vẽ được bằng Inkscape |
| **Vì sao hợp** | Đúng thể loại Shadow Fight bạn thích, và phong cách silhouette là "mẹo" số một để game trông đẹp mà không cần biết vẽ |
| **Ưu tiên** | ⭐⭐⭐ Làm ở P4 |

---

## #3 — One-Button Runner

| | |
|---|---|
| **Thể loại** | Endless runner, một nút |
| **Thời gian** | 1–2 tuần |
| **Gameplay** | Nhân vật tự chạy, một phím nhảy. Chướng ngại sinh theo procedural với độ khó tăng dần. Combo khi né sát. Chỉ có điểm số và high score. |
| **Kỹ năng học** | Procedural generation cơ bản, difficulty curve, game feel cho nhảy (coyote time, jump buffer, variable height), tối ưu spawn/despawn, vòng lặp "chỉ một lần nữa" |
| **Art cần** | Hình chữ nhật cũng chơi được. Parallax 2 lớp |
| **Vì sao hợp** | **Dự án dự phòng lý tưởng** — nếu P1 chậm hoặc bạn cần một thắng lợi nhanh để lấy lại động lực, làm cái này trong 2 tuần và publish |
| **Ưu tiên** | ⭐⭐ Dự phòng / xen kẽ khi mất động lực |

---

## #4 — Bullet Heaven ("Vampire Survivors" thu nhỏ)

| | |
|---|---|
| **Thể loại** | Auto-battler survival, top-down |
| **Thời gian** | 3–4 tuần |
| **Gameplay** | Chỉ di chuyển, vũ khí tự bắn. Sóng quái tăng dần trong 10 phút. Lên cấp → chọn 1 trong 3 nâng cấp. Kết hợp nâng cấp tạo build. |
| **Kỹ năng học** | Xử lý **hàng nghìn entity** (bài học tối ưu tốt nhất — rất hợp để đo trên iGPU), spatial partitioning, hệ thống nâng cấp/synergy, RNG có kiểm soát, đường cong độ khó |
| **Art cần** | Rất ít — quái là sprite 16x16 lặp lại |
| **Vì sao hợp** | Tỉ lệ "vui / công sức" cao nhất trong danh sách. Cũng là bài tập performance thực chiến |
| **Ưu tiên** | ⭐⭐ Thay thế cho #1 nếu bạn muốn, hoặc làm sau P3 |

---

## #5 — Tower Defense mini

| | |
|---|---|
| **Thể loại** | Tower defense, 1 map |
| **Thời gian** | 3–4 tuần |
| **Gameplay** | Một đường đi cố định, 4 loại tháp, 10 wave, tài nguyên hạn chế, có thể nâng cấp hoặc bán tháp. |
| **Kỹ năng học** | Pathfinding (A* của Godot `AStarGrid2D`), targeting logic (nearest/strongest/first), UI phức tạp (đặt tháp, tooltip), quản lý kinh tế trong game, cân bằng số học |
| **Art cần** | Tilemap + 4 tháp + 3 quái. Kenney có bộ TD miễn phí hoàn chỉnh |
| **Vì sao hợp** | Dạy nhiều **hệ thống** cùng lúc, và cân bằng số học là kỹ năng chuyển giao tốt sang mọi game |
| **Ưu tiên** | ⭐⭐ Tùy chọn |

---

## #6 — Grid Puzzle Roguelike (kiểu "Into the Breach" thu nhỏ)

| | |
|---|---|
| **Thể loại** | Chiến thuật theo lượt, lưới 6x6 |
| **Thời gian** | 3–4 tuần |
| **Gameplay** | 3 đơn vị của bạn, quái báo trước hành động của lượt sau. Bạn phải đẩy/chặn để chúng đánh nhầm nhau. Thắng 5 trận liên tiếp. |
| **Kỹ năng học** | **Turn-based state machine**, undo/redo (Command pattern), logic thuần rất dễ **TDD triệt để** — đây là ý tưởng tốt nhất để thực hành TDD, AI theo luật, telegraphing |
| **Art cần** | Gần như không — ô vuông, mũi tên, icon |
| **Vì sao hợp** | Nếu bạn muốn một dự án gần với sở trường lập trình logic của mình nhất và ít phụ thuộc art nhất |
| **Ưu tiên** | ⭐⭐ Tùy chọn — rất hợp để làm giữa P3 khi đang học TDD |

---

## #7 — Rhythm Dodge

| | |
|---|---|
| **Thể loại** | Âm nhạc + né đạn |
| **Thời gian** | 2–3 tuần |
| **Gameplay** | Đạn sinh theo nhịp bài nhạc. Người chơi né trong khung hình nhỏ. Sống hết bài là thắng. |
| **Kỹ năng học** | **Đồng bộ audio–gameplay** (khó và thú vị: dùng `AudioStreamPlayer.get_playback_position()` + độ trễ output, không dùng `delta`), beatmap data-driven, pattern design |
| **Art cần** | Hình học thuần + hiệu ứng màu |
| **Vì sao hợp** | Dạy một kỹ năng chuyên biệt (audio timing) mà ít dev có. Nhạc miễn phí sẵn nhiều trên OpenGameArt/Incompetech |
| **Ưu tiên** | ⭐ Game jam / thử nghiệm |

---

## #8 — Physics Sandbox Golf

| | |
|---|---|
| **Thể loại** | Puzzle vật lý, kéo–thả–bắn |
| **Thời gian** | 2 tuần |
| **Gameplay** | Kéo để ngắm, thả để bắn bóng vào lỗ. 20 màn với chướng ngại: lò xo, gió, nam châm, cổng dịch chuyển. |
| **Kỹ năng học** | `RigidBody2D`, vật liệu vật lý, thiết kế màn chơi (level design thật sự), editor màn chơi tự làm, đường đạn dự đoán |
| **Art cần** | Cực ít |
| **Vì sao hợp** | Dự án ngắn nhất trong danh sách vẫn cho ra sản phẩm hoàn chỉnh; dạy level design — kỹ năng mà cả #1 và #2 đều không dạy |
| **Ưu tiên** | ⭐ Game jam / xen kẽ |

---

## Bảng tổng hợp

| # | Tên | Tuần | Art | Dạy chính | Vị trí trong lộ trình |
|---|-----|------|-----|-----------|----------------------|
| 1 | Chicken Blaster | 5–6 | Thấp | Juice, pooling, wave system | **P2 — chính thức** |
| 2 | Silhouette Duel | 10–12 | Rất thấp | Combat, animation, AI | **P4 — chính thức** |
| 3 | One-Button Runner | 1–2 | Rất thấp | Game feel, procedural | Dự phòng động lực |
| 4 | Bullet Heaven | 3–4 | Thấp | Performance, upgrade synergy | Tùy chọn sau P3 |
| 5 | Tower Defense mini | 3–4 | Trung bình | Pathfinding, UI, kinh tế | Tùy chọn |
| 6 | Grid Puzzle Roguelike | 3–4 | Gần như không | **TDD, turn-based, undo** | Tùy chọn trong P3 |
| 7 | Rhythm Dodge | 2–3 | Rất thấp | Audio sync | Game jam |
| 8 | Physics Golf | 2 | Rất thấp | Level design, physics | Game jam |

## Quy tắc chọn dự án

1. **Không bao giờ làm 2 game cùng lúc.**
2. Nếu một ý tưởng không mô tả được trong **1 câu**, nó chưa đủ chín để bắt đầu.
3. Trước khi bắt đầu bất kỳ ý tưởng nào ngoài danh sách này, viết GDD 1 trang và tự hỏi: *"Cắt được một nửa scope không?"* — nếu có, cắt.
4. Ý tưởng mới xuất hiện giữa dự án → ghi vào `docs/devlog/idea-icebox.md`, không đụng vào.
