# 07 — Giai đoạn 4: Game #2 — Combat 2D kiểu Shadow Fight (Tuần 17–28, ~108h)

> Dự án lớn nhất năm. 12 tuần. Đây là nơi bạn học **gameplay programming** thật sự: animation, hitbox theo frame, input buffer, AI, cân bằng chiến đấu.

**Tên tạm:** *Silhouette Duel*
**Repo:** `~/projects/silhouette-duel`

---

## 1. Phân tích: Shadow Fight thực chất là gì?

Bóc tách để biết cái gì cần làm, cái gì bỏ:

| Thành phần Shadow Fight | Độ khó cho solo | Quyết định |
|---|---|---|
| Nhân vật bóng đen (silhouette) | Dễ ⭐ | ✅ **Giữ — đây là món quà cho người không biết vẽ.** Chỉ cần hình đen, không cần chi tiết, không cần shading |
| Ragdoll physics + skeletal animation | Rất khó ⭐⭐⭐⭐⭐ | ❌ Bỏ ragdoll. Dùng **Skeleton2D + Polygon2D** hoặc sprite frame-by-frame |
| Combo, đòn nhẹ/nặng, đá/đấm | Trung bình ⭐⭐⭐ | ✅ Giữ, rút gọn: 4 đòn (đấm nhẹ, đấm nặng, đá, đòn đặc biệt) |
| Block, parry, dodge | Trung bình ⭐⭐⭐ | ✅ Giữ — đây là chiều sâu chiến thuật, rẻ nhất về mặt art |
| Vũ khí đa dạng, trang bị | Dễ về code, tốn về art ⭐⭐ | ⚠️ Rút gọn: 3 vũ khí, mỗi cái đổi frame data + tầm đánh |
| Bản đồ, cốt truyện, thế giới | Tốn ⭐⭐⭐⭐ | ❌ Bỏ. Thay bằng **chuỗi 10 trận đấu (arcade ladder)** |
| Nâng cấp chỉ số / trang bị | Dễ ⭐⭐ | ✅ Giữ, đơn giản: 4 chỉ số |
| PvP online | Rất khó ⭐⭐⭐⭐⭐ | ⏸ Hoãn sang [P5](08-phase-5-server-side.md) như prototype riêng |

**GDD 1 câu:** *Đấu tay đôi 2D dạng bóng đen, 4 đòn + block/parry, leo thang 10 đối thủ AI có tính cách khác nhau, nâng cấp chỉ số giữa các trận.*

---

## 2. Lịch 12 tuần

### Tuần 17–18 — Nền tảng nhân vật & animation

**Quyết định kỹ thuật đầu tiên (ghi ADR):** chọn giữa

| Cách | Ưu | Nhược | Khuyến nghị |
|---|---|---|---|
| **Sprite frame-by-frame** (`AnimatedSprite2D`) | Đơn giản, kiểm soát chính xác frame data, dễ debug | Tốn art, khó sửa | ✅ **Chọn cái này nếu dùng asset có sẵn** |
| **Skeleton2D + Polygon2D** (cutout) | Ít art (vẽ 8 mảnh cơ thể), animation sửa dễ, dùng lại cho mọi nhân vật | Học đường cong dốc hơn, hitbox theo bone phức tạp hơn | ✅ **Chọn cái này** — hợp phong cách silhouette, tiết kiệm art khổng lồ cho solo |

Với phong cách bóng đen, Skeleton2D là lựa chọn đúng: bạn vẽ **8 hình đa giác đen** (đầu, thân, 2 cánh tay trên/dưới, 2 chân trên/dưới) rồi animate bằng xoay xương. Có thể tự làm trong Inkscape trong 1 buổi.

Nhiệm vụ:
- Rig nhân vật với Skeleton2D (8–12 bone)
- 6 animation trong `AnimationPlayer`: idle, walk, jab, heavy, kick, hurt
- Hệ tọa độ đấu: khóa trục Y (mặt đất), chỉ di chuyển trái/phải + nhảy

### Tuần 19–20 — Combat core: hitbox, hurtbox, frame data

Đây là **trái tim của thể loại đối kháng**. Học kỹ khái niệm frame data:

```
Một đòn đánh = 3 pha:
  Startup  (khởi động)  : n frame trước khi hitbox bật
  Active   (hiệu lực)   : hitbox bật, có thể trúng
  Recovery (thu hồi)    : hitbox tắt, chưa nhận input mới

Ví dụ đấm nhẹ:  4 / 3 / 8   (tổng 15 frame @60fps = 0.25s)
       đấm nặng: 12 / 5 / 20 (chậm, mạnh, rủi ro cao)
```

```gdscript
class_name AttackData
extends Resource

@export var anim_name: StringName
@export var startup_frames: int = 4
@export var active_frames: int = 3
@export var recovery_frames: int = 8
@export var damage: float = 5.0
@export var hitstun_frames: int = 12      # nạn nhân bị khựng
@export var blockstun_frames: int = 6     # khựng khi đỡ
@export var knockback: Vector2 = Vector2(120, -40)
@export var hitbox_offset: Vector2
@export var hitbox_size: Vector2
@export var can_cancel_into: Array[StringName] = []   # combo
```

Nhiệm vụ:
- Bật/tắt hitbox theo **Animation Track method call** (dùng key trong AnimationPlayer, đúng chuẩn ngành)
- Hurtbox thay đổi theo animation (cúi người thì hurtbox thấp)
- **Hitstop** khi trúng đòn (dừng 3–6 frame) — cực kỳ quan trọng cho cảm giác đấm
- Knockback + hitstun + juggle cơ bản
- **TDD:** transition state machine và tính damage sau giáp/block đều là logic thuần → test trước

### Tuần 21 — Input: buffer, combo, chống ức chế

Đây là thứ phân biệt game đối kháng "cảm giác tốt" và "cảm giác dở":

- **Input buffer** 6–8 frame: bấm đòn tiếp theo trong recovery vẫn được ghi nhận
- **Cancel window**: đòn nhẹ → đòn nặng chỉ trong khoảng frame nhất định
- **Command pattern** để ghi input (đã học ở [P3](06-phase-3-engineering.md)) → dùng luôn cho replay và cho AI
- Hỗ trợ gamepad + bàn phím, cho đổi phím

```gdscript
# Input buffer đơn giản mà hiệu quả
var _buffer: Array[Dictionary] = []
const BUFFER_FRAMES := 8

func push_input(action: StringName) -> void:
	_buffer.append({"action": action, "frame": Engine.get_physics_frames()})

func consume(action: StringName) -> bool:
	var now := Engine.get_physics_frames()
	for i in range(_buffer.size() - 1, -1, -1):
		var e: Dictionary = _buffer[i]
		if now - e.frame > BUFFER_FRAMES:
			_buffer.remove_at(i)
		elif e.action == action:
			_buffer.remove_at(i)
			return true
	return false
```

### Tuần 22 — Phòng thủ & chiều sâu chiến thuật
- **Block**: giữ hướng lùi, giảm damage 80%, vẫn nhận chip damage
- **Parry**: bấm block đúng 5 frame trước khi trúng → đối thủ bị choáng dài
- **Dodge/roll** với i-frame
- **Stamina**: đòn nặng và dodge tốn stamina → tránh spam
- Cân bằng: bảng ma trận đòn nào thắng đòn nào (viết vào `docs/combat-matrix.md`)

> ⚠️ **Tuần 22–24 là vùng nguy hiểm động lực nhất của cả năm** (theo [00-roadmap-overview.md](00-roadmap-overview.md) §6). Nếu thấy chán: dừng 3 ngày, làm một game jam mini, rồi quay lại.

### Tuần 23–24 — AI đối thủ

Học 3 kiến trúc AI, implement 2:

| Kiến trúc | Dùng cho | Implement? |
|---|---|---|
| **Finite State Machine** | Enemy đơn giản (aggressive, defensive) | ✅ |
| **Behavior Tree** | Enemy phức tạp, tái dùng node hành vi | ✅ — đây là kỹ năng có giá trị nhất |
| Utility AI / GOAP | Đọc hiểu | ❌ |

- 10 đối thủ = **3 archetype** (hung hãn / thủ / cơ hội) × biến thể chỉ số + vũ khí
- AI phải có **độ trễ phản ứng nhân tạo** (150–300ms) — AI phản ứng tức thì không vui, chỉ gây ức chế
- AI đọc "khoảng cách" và chọn đòn theo tầm đánh (spacing) — đây là bản chất của game đối kháng

### Tuần 25 — Progression & meta
- 4 chỉ số: Sức mạnh / Máu / Tốc độ / Stamina
- Điểm nâng cấp sau mỗi trận thắng
- 3 vũ khí: tay không (nhanh, tầm ngắn) / kiếm (cân bằng) / búa (chậm, mạnh, tầm xa)
- Màn hình arcade ladder, lưu tiến độ

### Tuần 26 — Art & audio pass
- Silhouette nhân vật: đen tuyền + viền sáng khi bị trúng đòn (dùng shader outline từ P3)
- Nền: parallax 3 lớp, gradient hoàng hôn + bóng cây — làm được hoàn toàn bằng Inkscape trong vài giờ
- VFX: slash trail (dùng `Line2D` + gradient), impact particle, dust khi tiếp đất
- Audio: whoosh, impact, block clang, ambient gió, 2 track nhạc
- **Chi tiết đắt giá nhất:** rung màn hình + hitstop + slash trail — ba thứ này làm game trông đắt tiền hơn thực tế nhiều lần

### Tuần 27 — Balance & playtest
- Bảng cân bằng trong Resource/JSON, chỉnh không cần build
- Playtest 5+ người, đo: tỉ lệ thắng mỗi trận, thời gian mỗi trận, chỗ người chơi bỏ cuộc
- Sửa: đối thủ số 3 thường là chỗ người chơi bỏ — làm mượt đường cong độ khó
- Tutorial ngắn: 3 màn hình dạy đòn cơ bản (không làm tutorial dài)

### Tuần 28 — Ship
- Export Web + Linux (+ Android nếu muốn thử, Godot export APK không khó)
- Trang itch.io mới, trailer 30 giây quay bằng OBS
- Đăng lên r/godot, r/IndieDev, Godot Discord, TIGSource
- Postmortem

---

## 3. Kỹ năng gặt được (ánh xạ roadmap.sh/game-developer)

- ✅ Gameplay programming (nhánh trọng tâm của roadmap gốc)
- ✅ Animation systems & state machines
- ✅ Collision/hitbox, frame-perfect timing
- ✅ AI: FSM + Behavior Tree
- ✅ Input handling nâng cao
- ✅ Game balancing & tuning
- ✅ VFX/shader ứng dụng
- ✅ Data-driven design

## 4. Deliverable P4

- [ ] Game publish trên itch.io, chơi được trên web
- [ ] 10 trận đấu, 3 archetype AI, 3 vũ khí
- [ ] Trailer 30s
- [ ] `docs/combat-matrix.md` + `docs/balance.md`
- [ ] Behavior Tree tái dùng đã đưa vào `gdlib_core`
- [ ] Postmortem: so sánh với dự án #1 — cái gì đã tốt lên?
