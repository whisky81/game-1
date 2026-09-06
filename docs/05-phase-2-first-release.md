# 05 — Giai đoạn 2: Game hoàn chỉnh #1 — "Chicken Blaster" (Tuần 5–10, ~54h)

> **Đây là giai đoạn quan trọng nhất trong 12 tháng.** Mục tiêu không phải làm game hay, mà là **hoàn thành và phát hành**. Kỹ năng "ship" khan hiếm hơn kỹ năng code rất nhiều.

**Repo riêng:** `~/projects/chicken-blaster` (tách khỏi `game-1` sandbox).

---

## 1. GDD một trang (viết trước khi code — Buổi đầu tiên của Tuần 5)

| Mục | Nội dung |
|-----|----------|
| **Tên** | Chicken Blaster (tạm) |
| **Thể loại** | Arcade shoot-em-up, dọc, wave-based (Galaga / Chicken Invaders) |
| **Pitch 1 câu** | Bắn hạ từng đợt gà nổ tung thành đùi gà, nhặt đùi để nâng cấp súng, sống sót 10 wave và hạ Gà Trùm. |
| **Nền tảng** | Web (itch.io) + Linux |
| **Thời lượng chơi** | 5–8 phút một lượt |
| **Cơ chế lõi** | Di chuyển 8 hướng, bắn tự động, nhặt vật phẩm, né đạn |
| **Vòng lặp** | Clear wave → nhặt drop → nâng cấp → wave khó hơn → boss |
| **Điều kiện thắng** | Hạ boss ở wave 10 |
| **Điều kiện thua** | Hết 3 mạng |
| **Nội dung tối thiểu** | 3 loại gà + 1 boss + 4 pattern đạn + 3 nâng cấp súng |
| **KHÔNG làm** (viết ra để tự chặn) | Nhiều màn/thế giới, cốt truyện, co-op, shop, achievement, nhiều vũ khí, chế độ endless |

**Quy tắc scope:** nếu tuần 9 chưa xong, cắt boss xuống còn 1 phase và giảm còn 7 wave. **Không lùi ngày phát hành.**

---

## 2. Lịch 6 tuần

### Tuần 5 — Xương sống chơi được (vertical slice)
- Player: di chuyển, giới hạn biên màn hình, bắn có cooldown
- 1 loại gà bay theo đường thẳng, chết khi trúng đạn
- Object pool cho đạn + gà (tái dùng code Tuần 3)
- HUD: mạng, điểm
- ✅ **Xong tuần 5 = có thể chơi 30 giây và thấy vui sơ khai**

### Tuần 6 — Wave system data-driven
- `WaveData` Resource: danh sách entry `{enemy_scene, count, delay, formation}`
- 3 kiểu đội hình: hàng ngang, chữ V, vòng cung (viết bằng toán, không đặt tay)
- 3 loại gà: bay thẳng / bay sin / lao xuống người chơi
- Wave định nghĩa trong `.tres` → chỉnh cân bằng không cần sửa code

```gdscript
# Đội hình chữ V — toán học nhỏ nhưng đáng học
func v_formation(count: int, spacing: float, angle_deg: float) -> Array[Vector2]:
	var out: Array[Vector2] = []
	var a := deg_to_rad(angle_deg)
	for i in count:
		var side := 1 if i % 2 == 0 else -1
		var rank := float((i + 1) / 2)
		out.append(Vector2(cos(a), sin(a)) * spacing * rank * Vector2(side, 1))
	return out
```

### Tuần 7 — Juice (tuần thay đổi cảm giác game nhiều nhất)
Danh sách kiểm tra, làm từ trên xuống, mỗi mục 15–30 phút:
- [ ] **Hit flash**: shader trắng 0.06s khi trúng đòn
- [ ] **Hit stop**: `Engine.time_scale = 0.05` trong 3 frame khi giết boss
- [ ] **Screen shake**: biên độ theo cường độ, dùng noise chứ không random
- [ ] **Particle** khi gà nổ (`GPUParticles2D`, texture 4x4 px cũng đủ)
- [ ] **Squash & stretch** khi gà chết (`Tween` scale)
- [ ] **Muzzle flash** + đạn có trail
- [ ] **Camera punch** nhẹ mỗi phát bắn
- [ ] **SFX**: bắn / trúng / nổ / nhặt đồ / thua — làm bằng **jsfxr** trong 20 phút
- [ ] **Nhạc nền**: 1 track loop từ OpenGameArt hoặc tự làm bằng BeepBox
- [ ] **Audio bus**: Master / Music / SFX + giới hạn số SFX cùng lúc (chống chói tai)

> So sánh video trước/sau tuần này. Đây là bằng chứng thuyết phục nhất cho portfolio.

### Tuần 8 — Nâng cấp, boss, độ khó
- Drop "đùi gà" → nhặt đủ 5 → nâng cấp súng (1 tia → 3 tia → tia xuyên)
- Boss Gà Trùm: 2 phase, mỗi phase 1 pattern đạn khác nhau, thanh máu riêng
- Đường cong độ khó: máu/tốc độ/mật độ đạn tăng theo wave — công thức trong Resource
- **TDD ở đây:** công thức scaling độ khó và điều kiện lên cấp súng là logic thuần → viết test trước

### Tuần 9 — Meta, menu, lưu trữ, đánh bóng
- Main menu / Pause / Game Over / Victory
- Lưu high score + cài đặt âm lượng vào `user://save.json`
- Cài đặt: âm lượng, toàn màn hình, đổi phím
- Chống lỗi: pause đúng cách, không rò rỉ node, kiểm tra bằng Godot Monitor (Object count không tăng vô hạn)
- Icon game, splash screen

### Tuần 10 — Phát hành 🚀
1. Export **Web (HTML5)** + **Linux x86_64**
   ```bash
   godot --headless --export-release "Web" build/web/index.html
   godot --headless --export-release "Linux" build/linux/chicken-blaster.x86_64
   ```
   > Web export cần bật `Extensions Support` = off, dùng renderer **Compatibility**, và itch.io phải bật "SharedArrayBuffer" nếu dùng thread.
2. Kiểm thử trên trình duyệt khác (Firefox + Chromium), kiểm tra kích thước bundle (< 30 MB)
3. **Playtest với 3–5 người thật.** Không hướng dẫn gì cả, chỉ ngồi xem và ghi lại: chỗ nào họ bối rối, chỗ nào họ cười, chỗ nào họ bỏ.
4. Sửa 5 vấn đề nghiêm trọng nhất từ playtest (chỉ 5, không hơn)
5. Trang itch.io: tiêu đề, 1 GIF động, 3 ảnh chụp, mô tả ngắn, tag `shmup`, `arcade`, `godot`
6. Bấm **Publish**. Chia sẻ lên r/godot, r/IndieDev, Godot Discord `#showcase`
7. Viết `docs/postmortem.md` theo template

---

## 3. Kiến trúc đề xuất cho dự án này

```
chicken-blaster/
├── autoload/
│   ├── game_state.gd        # điểm, mạng, wave hiện tại
│   ├── event_bus.gd         # signal toàn cục: enemy_died, wave_cleared...
│   ├── audio_manager.gd     # phát SFX có giới hạn số kênh
│   └── save_manager.gd
├── components/
│   ├── health_component.gd
│   ├── hitbox_component.gd
│   ├── hurtbox_component.gd
│   └── movement_pattern.gd  # Resource: cách bay của gà
├── entities/{player,enemies,bullets,pickups}/
├── systems/
│   ├── pool.gd
│   ├── wave_spawner.gd
│   └── difficulty_curve.gd
├── resources/waves/*.tres
├── ui/
└── test/unit/
```

**EventBus pattern** (tránh coupling chằng chịt giữa các node):

```gdscript
# autoload/event_bus.gd
extends Node
signal enemy_died(enemy_type: StringName, pos: Vector2, score: int)
signal wave_cleared(index: int)
signal player_damaged(remaining: int)
signal weapon_upgraded(level: int)
```

HUD lắng nghe `EventBus`, không cần biết Player tồn tại. Đây chính là pattern **Observer** trong roadmap gốc.

---

## 4. Cạm bẫy của giai đoạn này

| Bẫy | Cách tránh |
|-----|-----------|
| Vẽ art trước khi gameplay vui | Dùng hình chữ nhật màu tới hết Tuần 6. Asset Kenney từ Tuần 7 |
| "Thêm một loại gà nữa" mãi không hết | Chốt cứng 3 loại + 1 boss trong GDD. Ý tưởng mới → `Icebox` |
| Refactor kiến trúc giữa chừng | Chỉ refactor vào đầu tuần, không giữa tuần |
| Tuần 10 trượt vì "chưa hoàn hảo" | Ngày phát hành cố định. Bản 1.0 xấu vẫn hơn bản 0.9 mãi mãi |
| Không ai chơi thử trước khi publish | Playtest là **bắt buộc**, không phải tùy chọn |

## 5. Deliverable

- [ ] Link itch.io công khai, chơi được trên trình duyệt
- [ ] Build Linux tải được
- [ ] README có GIF, hướng dẫn build, hướng dẫn chạy test
- [ ] Postmortem trong `docs/`
- [ ] Ít nhất 5 người ngoài đã chơi
