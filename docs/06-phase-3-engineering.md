# 06 — Giai đoạn 3: Kỹ thuật phần mềm cho game (Tuần 11–16, ~54h)

**Bối cảnh:** bạn vừa ship một game bằng cách "cứ code cho xong". Giờ là lúc biến kinh nghiệm đó thành nền móng kỹ thuật, trước khi bước vào dự án lớn 12 tuần ở P4. Đây là giai đoạn tận dụng tối đa nền tảng IT sẵn có của bạn.

**Sản phẩm cuối:** một **addon/thư viện Godot tái sử dụng** (`gdlib-core`) do bạn viết, có test, có CI, được dùng thật trong P4.

---

## Tuần 11 — Design Patterns cho game

Đọc **Game Programming Patterns** (miễn phí online) và **implement lại** từng pattern bằng GDScript trong context game của bạn. Không đọc suông.

| Pattern | Áp dụng thực tế | Bài tập |
|---------|----------------|---------|
| **State** | Nhân vật: Idle/Run/Attack/Hurt/Dead | Viết `StateMachine` node tái dùng + `State` base class, có `enter/exit/update/physics_update` |
| **Observer** | EventBus (đã dùng ở P2) | Refactor lại cho type-safe, thêm cơ chế debug log mọi event |
| **Command** | Input buffering, replay, undo trong editor tool | Ghi lại input mỗi frame → replay được một lượt chơi |
| **Object Pool** | Đạn, particle, damage number | Tổng quát hóa pool ở P1 thành generic, có thống kê hit/miss |
| **Component** | Health, Hitbox, Movement | Chuẩn hóa interface giữa các component |
| **Service Locator** | Autoload trong Godot chính là cái này | Hiểu vì sao lạm dụng singleton gây khó test |
| **Flyweight** | Dữ liệu enemy dùng chung qua Resource | Chuyển stat enemy sang `.tres` chia sẻ |
| **Dirty Flag / Spatial Partition** | Chỉ đọc hiểu, chưa cần | — |

**Sản phẩm tuần:** `addons/gdlib_core/` với `StateMachine`, `Pool`, `EventBus`, `HealthComponent` — tất cả có test.

---

## Tuần 12 — Testing nghiêm túc với GUT

1. Cài GUT từ AssetLib, cấu hình `.gutconfig.json`
2. Viết test cho toàn bộ logic thuần trong `gdlib_core`
3. Học 4 kiểu test trong game:

```gdscript
# 1. Unit thuần
func test_xp_curve_tang_don_dieu():
	for lv in range(1, 50):
		assert_gt(Progression.xp_for(lv + 1), Progression.xp_for(lv))

# 2. Test state machine (transition table)
func test_khong_the_tan_cong_khi_dang_hurt():
	var sm := _make_sm("Hurt")
	sm.handle_input("attack")
	assert_eq(sm.current_state_name, "Hurt")

# 3. Integration bằng scene tạm + mô phỏng frame
func test_dan_bien_mat_khi_ra_khoi_man_hinh():
	var b := add_child_autofree(BulletScene.instantiate())
	b.global_position = Vector2(0, -1000)
	simulate(b, 5, 0.016)     # chạy 5 frame
	assert_true(b.is_queued_for_deletion())

# 4. Property-based (tự viết, không cần thư viện)
func test_damage_luon_nam_trong_khoang_hop_le():
	var rng := RandomNumberGenerator.new()
	for i in 1000:
		var d := DamageCalc.compute(rng.randf_range(0, 999), rng.randf_range(0, 999))
		assert_between(d, 0.0, 99999.0)
```

4. **Đo coverage tư duy, không phải coverage số:** liệt kê trong `docs/test-strategy.md` những gì *có* test và những gì *cố ý không* test (game feel, shader, art).

---

## Tuần 13 — CI/CD hoàn chỉnh

Nâng cấp workflow từ P0 lên pipeline thật:

```yaml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    container: { image: barichello/godot-ci:4.3.0 }
    steps:
      - uses: actions/checkout@v4
        with: { lfs: true }
      - run: godot --headless --import
      - name: Unit tests (GUT)
        run: godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://test -gexit -glog=1
      - name: Kiểm tra style GDScript
        run: gdlint scripts/ components/ systems/ || true

  build-web:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    container: { image: barichello/godot-ci:4.3.0 }
    steps:
      - uses: actions/checkout@v4
        with: { lfs: true }
      - run: |
          mkdir -p ~/.local/share/godot/export_templates/
          # template được cài sẵn trong image
          godot --headless --import
          mkdir -p build/web
          godot --headless --export-release "Web" build/web/index.html
      - uses: actions/upload-artifact@v4
        with: { name: web-build, path: build/web }

  deploy-itch:
    needs: build-web
    if: startsWith(github.ref, 'refs/tags/v')
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
        with: { name: web-build, path: build/web }
      - name: Push lên itch.io bằng butler
        env: { BUTLER_API_KEY: "${{ secrets.BUTLER_API_KEY }}" }
        run: |
          curl -L -o butler.zip https://broth.itch.zone/butler/linux-amd64/LATEST/archive/default
          unzip butler.zip && chmod +x butler
          ./butler push build/web <user>/<game>:html5 --userversion "${GITHUB_REF_NAME}"
```

Thêm: `gdtoolkit` (`pip install gdtoolkit`) cho `gdformat` + `gdlint`, và pre-commit hook:

```bash
cat > .git/hooks/pre-commit <<'HOOK'
#!/usr/bin/env bash
set -e
files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.gd$' || true)
[ -z "$files" ] && exit 0
gdformat --check $files || { echo "Chạy: gdformat $files"; exit 1; }
gdlint $files
HOOK
chmod +x .git/hooks/pre-commit
```

**Kết quả:** đẩy tag `v1.1.0` → game tự động lên itch.io. Đây là thứ khiến portfolio của bạn nổi bật so với đa số indie dev.

---

## Tuần 14 — Hiệu năng & profiling (đặc biệt quan trọng với Iris Xe)

1. **Học đọc profiler Godot**: Frame time, Physics time, Draw calls, Object count, Video memory (Debugger → Profiler / Monitors)
2. Tạo scene stress test: 2000 entity, 5000 đạn — đo trên chính máy bạn
3. Các kỹ thuật tối ưu 2D theo thứ tự hiệu quả:

| Kỹ thuật | Khi nào dùng | Mức lợi |
|---|---|---|
| Object pooling | Luôn dùng cho đạn/particle | Cao |
| Giảm draw call: gộp texture atlas, dùng `MultiMeshInstance2D` | Nhiều sprite giống nhau | Cao trên iGPU |
| Tắt `_process` khi không cần (`set_process(false)`) | Entity ngoài màn hình | Trung bình–cao |
| `VisibleOnScreenNotifier2D` | Tự động bật/tắt xử lý | Cao |
| Dùng `Area2D` thay `RigidBody2D` khi không cần vật lý thật | Đạn, pickup | Cao |
| Giảm số collision shape, dùng shape đơn giản | Luôn | Trung bình |
| Static typing trong GDScript (`var x: int`) | Luôn viết | ~10–30% tốc độ |
| Tránh cấp phát trong `_process` (mảng, Dictionary mới) | Vòng lặp nóng | Trung bình |
| `@onready` cache node thay `get_node()` mỗi frame | Luôn | Trung bình |
| GDExtension C++ | Chỉ khi profiler chứng minh | Cao nhưng tốn công |

4. **Bài tập:** lấy Chicken Blaster, tăng số enemy lên tới khi tụt dưới 60fps trên máy bạn. Ghi con số. Tối ưu 3 thứ. Đo lại. Viết `docs/perf-report.md` có bảng trước/sau.

---

## Tuần 15 — Shader & VFX (mức ứng dụng)

Không học OpenGL/Vulkan. Học **Godot Shading Language** (giống GLSL) chỉ để làm 6 hiệu ứng:

1. **Hit flash** (trắng toàn sprite)
2. **Outline** (viền khi hover/target)
3. **Dissolve** (tan biến khi chết, dùng noise texture)
4. **Palette swap** (đổi màu enemy để tạo biến thể — tiết kiệm art khổng lồ)
5. **Screen distortion** (sóng xung kích khi nổ)
6. **Parallax + scroll** cho nền

```glsl
// Hit flash — shader ngắn nhất có tác dụng lớn nhất
shader_type canvas_item;
uniform float flash : hint_range(0.0, 1.0) = 0.0;
uniform vec4 flash_color : source_color = vec4(1.0);

void fragment() {
    vec4 tex = texture(TEXTURE, UV);
    COLOR = vec4(mix(tex.rgb, flash_color.rgb, flash), tex.a);
}
```

Kèm `GPUParticles2D`: học 5 preset (nổ, khói, tia lửa, bụi chân, máu).

---

## Tuần 16 — Công cụ & dữ liệu (đòn bẩy lớn nhất của solo dev)

1. **Custom Resource** cho mọi dữ liệu cân bằng:

```gdscript
class_name EnemyStats
extends Resource

@export var display_name: String = ""
@export_range(1, 9999) var max_health: float = 10.0
@export var move_speed: float = 60.0
@export var contact_damage: float = 1.0
@export var score: int = 100
@export var sprite_frames: SpriteFrames
@export var drop_table: Array[DropEntry] = []
```

2. **Editor plugin / tool script** đơn giản: nút trong editor để sinh wave ngẫu nhiên, hoặc validate toàn bộ `.tres` (`@tool` + `EditorPlugin`)
3. **Script Python xác thực dữ liệu** trong CI: đọc `.tres`/JSON, kiểm tra không có enemy nào máu = 0, không có drop table rỗng
4. **Hot-reload cân bằng**: đọc JSON từ `user://balance.json` khi ở debug build → chỉnh số khi game đang chạy

---

## Deliverable P3

- [ ] `gdlib_core` addon dùng được, ≥ 25 unit test, CI xanh
- [ ] Pipeline tự động deploy lên itch.io khi tag
- [ ] `docs/perf-report.md` có số liệu trước/sau tối ưu
- [ ] 6 shader tái dùng trong `addons/gdlib_core/shaders/`
- [ ] `docs/test-strategy.md`
- [ ] Chicken Blaster đã được refactor sang dùng `gdlib_core` (chứng minh thư viện dùng được thật)
