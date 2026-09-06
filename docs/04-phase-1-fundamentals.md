# 04 — Giai đoạn 1: Nền tảng Godot + toán game (Tuần 1–4, ~36h)

**Triết lý giai đoạn này:** mỗi tuần làm **một prototype hoàn chỉnh nhưng xấu**, dưới 300 dòng, xong là vứt (nhưng commit lại). Bạn không xây game ở đây — bạn xây *phản xạ*.

**Mỗi prototype đều nằm trong `prototypes/`, có README ngắn kèm ảnh GIF.**

---

## Tuần 1 — Pong: vòng lặp game, input, va chạm cơ bản

**Kiến thức mới:** Scene/Node tree, `_process` vs `_physics_process`, `CharacterBody2D`, `Area2D` vs `CollisionShape2D`, signal, Input Map, `Vector2`.

**Nhiệm vụ (chia theo buổi):**

| Buổi | Việc | Xong khi |
|------|------|----------|
| 1 | Đọc/xem về Node & Scene; tạo scene Ball + Paddle | Bóng di chuyển thẳng, paddle điều khiển được |
| 2 | Va chạm bóng–paddle, phản xạ theo pháp tuyến | Bóng nảy lại đúng hướng |
| 3 | Điểm số + UI Label + reset bóng | Có điểm hiển thị |
| 4 | AI paddle đối thủ (đơn giản: đuổi theo y của bóng, có giới hạn tốc độ) | Chơi được với máy |
| 5 | Juice: screen shake nhẹ, SFX từ jsfxr, tăng tốc bóng theo thời gian | Cảm giác "đã" hơn hẳn |
| 6 | README + GIF + commit + devlog | Prototype đóng gói xong |

**Toán học cần nắm (thực hành, không lý thuyết suông):**
- `Vector2`: cộng, nhân vô hướng, `length()`, `normalized()`, `dot()`
- Phản xạ: `velocity = velocity.bounce(normal)` — rồi tự viết lại công thức `v - 2*(v·n)*n` để hiểu
- Vì sao góc nảy nên phụ thuộc vị trí chạm trên paddle (thiết kế, không phải vật lý thật)

**Bẫy thường gặp:** dùng `_process` cho physics → giật; quên `move_and_slide()`; đặt collision layer/mask sai.

---

## Tuần 2 — Breakout: quản lý entity, spawn/despawn, tile, state

**Kiến thức mới:** instantiate scene runtime (`PackedScene.instantiate()`), `queue_free()`, nhóm (`add_to_group`), signal bus, chuyển scene, cơ bản về `Tween`.

**Nhiệm vụ:**
- Sinh lưới gạch bằng vòng lặp code (không đặt tay trong editor) — đây là bài học về **data-driven**
- Gạch nhiều máu (2–3 hit) đổi màu theo máu
- Power-up rơi xuống: paddle to ra / bóng nhân đôi
- Màn hình Win / Game Over, nút Retry
- Lưu high score bằng `FileAccess` + JSON vào `user://`

**Toán:**
- Lưới: `pos = origin + Vector2(col * (w + gap), row * (h + gap))`
- Nội suy (lerp) cho hiệu ứng power-up: `scale = scale.lerp(target, 1.0 - exp(-rate * delta))` — hiểu vì sao dùng dạng exponential thay vì `lerp(a, b, delta)` thẳng (frame-rate independent)

**Thực hành TDD đầu tiên:**

```gdscript
# test/unit/test_grid.gd  — logic thuần, test được không cần engine
func test_grid_sinh_dung_so_gach():
    assert_eq(GridLayout.positions(Vector2(10, 10), 8, 5, Vector2(64, 24), 4).size(), 40)
```

---

## Tuần 3 — Top-down shooter: object pool, spawn wave, damage system

Đây là **bản nháp trực tiếp cho game bắn gà** ở P2. Làm nghiêm túc.

**Kiến thức mới:** object pooling, `Timer` vs đếm thủ công, component composition, `Resource` tùy biến, `Area2D` hitbox/hurtbox, camera 2D.

**Nhiệm vụ:**
1. Player bắn đạn — **triển khai object pool** ngay từ đầu (đạn là thứ chắc chắn cần pool)
2. `HealthComponent` (script tái dùng, gắn được vào cả player và enemy)
3. `HitboxComponent` / `HurtboxComponent` bằng `Area2D` + collision layer rõ ràng
4. Enemy spawn theo wave, định nghĩa wave bằng **Resource `.tres`** chứ không hardcode
5. Bảng layer/mask viết vào `docs/adr/0002-collision-layers.md`

**Mẫu object pool tối giản:**

```gdscript
# scripts/pool.gd
class_name Pool
extends Node

@export var scene: PackedScene
@export var initial_size: int = 64

var _free: Array[Node] = []

func _ready() -> void:
	for i in initial_size:
		_free.append(_make())

func _make() -> Node:
	var n := scene.instantiate()
	n.process_mode = Node.PROCESS_MODE_DISABLED
	n.hide()
	add_child(n)
	return n

func acquire() -> Node:
	var n: Node = _free.pop_back() if not _free.is_empty() else _make()
	n.process_mode = Node.PROCESS_MODE_INHERIT
	n.show()
	return n

func release(n: Node) -> void:
	n.process_mode = Node.PROCESS_MODE_DISABLED
	n.hide()
	_free.append(n)
```

**Đo lường (bài học performance đầu tiên):** bật Godot Profiler, so sánh FPS + thời gian frame giữa "instantiate/queue_free mỗi viên đạn" và "pool", với 500 viên đạn cùng lúc. Ghi kết quả vào devlog. Trên iGPU Iris Xe khác biệt sẽ thấy rõ.

---

## Tuần 4 — Platformer nhỏ: game feel & animation

**Kiến thức mới:** `AnimationPlayer`, `AnimatedSprite2D`, state machine cho nhân vật, tilemap (`TileMapLayer`), `Camera2D` với limit + smoothing.

**Nhiệm vụ:**
1. Di chuyển + nhảy dùng công thức nhảy theo *thiết kế*, không theo gravity tuỳ ý:

```gdscript
# Thiết kế theo: độ cao nhảy và thời gian lên đỉnh -> suy ra gravity
@export var jump_height: float = 64.0        # pixel
@export var jump_time_to_peak: float = 0.4   # giây
@export var jump_time_to_fall: float = 0.3   # rơi nhanh hơn -> cảm giác tốt hơn

@onready var jump_velocity: float = (2.0 * jump_height) / jump_time_to_peak * -1.0
@onready var jump_gravity: float = (-2.0 * jump_height) / (jump_time_to_peak ** 2) * -1.0
@onready var fall_gravity: float = (-2.0 * jump_height) / (jump_time_to_fall ** 2) * -1.0
```

2. **Ba kỹ thuật game feel bắt buộc phải cài** (đây là kiến thức đắt giá nhất tuần này):
   - **Coyote time** (~0.1s): vẫn nhảy được sau khi rời mép
   - **Jump buffer** (~0.12s): bấm nhảy sớm trước khi chạm đất vẫn được ghi nhận
   - **Variable jump height**: thả phím sớm → nhảy thấp (`velocity.y *= 0.5`)
3. State machine tường minh: `Idle / Run / Jump / Fall / Land` — implement bằng pattern State (mỗi state một class con)
4. Tilemap một màn nhỏ, camera bám theo có `position_smoothing`

**So sánh phụ (2h, tuỳ chọn nhưng khuyên làm):** viết lại *chỉ phần di chuyển* bằng **Phaser 3 + TypeScript**. Mục đích: tự trả lời "engine giúp mình được gì". Sau bài này bạn sẽ không còn phân vân đổi stack nữa.

---

## Deliverable cuối P1

- [ ] 4 thư mục trong `prototypes/`, mỗi cái có `README.md` + GIF
- [ ] Ít nhất 3 unit test đã viết bằng GUT
- [ ] `docs/adr/0002-collision-layers.md` đã có
- [ ] 4 devlog tuần
- [ ] Tự trả lời được (viết vào devlog): *"Trong 4 prototype, cái nào vui nhất và tại sao?"*

## Nguồn học cho P1 (miễn phí)

- **Godot Docs — "Your first 2D game" (Dodge the Creeps)**: làm nguyên bài trong Tuần 1, đây là tutorial chính thức tốt nhất
- **GDQuest** (YouTube + tài liệu FOSS): loạt Godot 4 2D
- **HeartBeast** (YouTube): loạt action RPG — rất hợp thể loại bạn muốn làm
- **Game Programming Patterns** (Robert Nystrom) — đọc miễn phí online: chương *Game Loop*, *Update Method*, *State*, *Object Pool*
- **"Game Feel" / GDC talk "Juice it or lose it"** (Martin Jonasson & Petri Purho) — xem trong Tuần 1, 15 phút, thay đổi cách bạn nhìn game
