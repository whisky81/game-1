# 02 — Quy trình làm việc cá nhân

## 1. Chọn quy trình: Kanban cá nhân + nhịp Lean

**Không dùng Scrum đầy đủ.** Scrum cần team, sprint planning, retro, story point — với 1 người và 9h/tuần, chi phí nghi thức lớn hơn lợi ích.

**Dùng: Personal Kanban (giới hạn WIP) + vòng lặp Build–Measure–Learn của Lean cho phần thiết kế game.**

| Thành phần | Cách áp dụng |
|-----------|-------------|
| **Board** | GitHub Projects (board view) trên chính repo — task đi kèm code, miễn phí, không cần công cụ thứ ba |
| **Cột** | `Icebox` → `Backlog` → `Ready` → `In Progress` → `Review/Test` → `Done` |
| **Giới hạn WIP** | **In Progress ≤ 2 thẻ.** Đây là luật quan trọng nhất. Solo dev chết vì làm 6 thứ dở dang |
| **Nhịp** | Chu kỳ 1 tuần (T2 → CN), không gọi là sprint mà gọi là **"tuần"** |
| **Lễ nghi duy nhất** | 30 phút Chủ Nhật: review tuần + viết devlog + kéo thẻ cho tuần sau |
| **Kích thước thẻ** | Mỗi thẻ ≤ **1 buổi làm việc (~1.5h)**. Nếu ước lượng > 2 buổi → chẻ nhỏ |
| **Lean loop** | Với mỗi cơ chế gameplay mới: Build (bản thô xấu) → Measure (tự chơi + cho 1 người chơi) → Learn (giữ/sửa/vứt). Không polish trước khi biết cơ chế có vui không |

### Nhãn (label) đề xuất cho issue

`type:feature` `type:bug` `type:chore` `type:learn` `type:art` `type:audio` `type:design`
`size:S` (≤1h) `size:M` (~1.5h) `size:L` (cần chẻ nhỏ)
`prio:must` `prio:should` `prio:nice`

Mọi thứ `prio:nice` phát sinh giữa tuần → ném vào `Icebox`, **không làm ngay**. Đây là hàng rào chống scope creep.

---

## 2. Nghi thức 30 phút Chủ Nhật (bắt buộc)

```
1. (5')  Mở board, kéo thẻ Done sang cột lưu trữ, đếm số thẻ hoàn thành
2. (10') Viết docs/devlog/YYYY-Www.md theo templates/devlog-template.md
3. (5')  Chạy game 5 phút, tự đánh giá: "có vui hơn tuần trước không?"
4. (10') Kéo 5–6 thẻ từ Backlog sang Ready cho tuần tới. KHÔNG nhiều hơn.
```

Chỉ thế. Không họp với chính mình 2 tiếng.

---

## 3. Git & quản lý phiên bản

### Chiến lược nhánh (đơn giản hóa cho solo)

```
main            ← luôn chạy được, luôn build được. Bảo vệ bằng CI.
 └── feat/<ten> ← mỗi feature một nhánh ngắn (1–3 ngày), merge bằng PR tự-review
 └── fix/<ten>
```

**Vì sao vẫn dùng PR khi làm một mình:** PR ép bạn đọc lại diff của chính mình, chạy CI trước khi merge, và tạo lịch sử có mô tả — chính là thứ nhà tuyển dụng / cộng tác viên tương lai nhìn vào. Tốn thêm 2 phút mỗi lần.

### Conventional Commits

```
feat(player): thêm dash với i-frame 0.2s
fix(spawner): sửa lỗi enemy sinh ngoài màn hình khi resize
perf(bullet): dùng object pool, giảm 40% alloc mỗi frame
test(damage): thêm test cho công thức crit
chore(ci): thêm workflow export web
docs(adr): ghi lại quyết định chọn skeletal animation
art(enemy): sprite gà cấp 2, 4 frame
```

### `.gitignore` cho Godot 4

```gitignore
# Godot 4
.godot/
/android/
export_presets.cfg      # chứa đường dẫn tuyệt đối & keystore
*.translation

# Build output
build/
*.zip
*.x86_64
*.wasm

# OS / editor
.DS_Store
.vscode/settings.json
```

### Git LFS cho asset nhị phân

Từ khi thêm ảnh/âm thanh (Tuần 3 trở đi):

```bash
git lfs install
git lfs track "*.png" "*.aseprite" "*.ase" "*.wav" "*.ogg" "*.mp3" "*.psd"
git add .gitattributes
```

> ⚠️ GitHub free chỉ cho 1 GB LFS storage + 1 GB băng thông/tháng. Với game 2D pixel art thì thừa. Nếu vượt: nén PNG bằng `oxipng`, dùng `.ogg` thay `.wav`.

### Gắn thẻ phiên bản

Mỗi lần build cho người khác chơi: `git tag -a v0.3.0-alpha -m "Playtest lần 2"`. Semantic-ish: `0.x` cho pre-release, `1.0.0` khi phát hành thật.

---

## 4. Test-Driven Development cho game — dùng đúng chỗ

TDD rất giá trị trong game, nhưng **không phải mọi chỗ**. Áp dụng nguyên tắc phân tầng:

| Tầng | Ví dụ | TDD? | Công cụ |
|------|-------|------|---------|
| **Logic thuần (pure)** | Công thức damage, crit, XP curve, cost nâng cấp, RNG loot table, state machine transition, parser dữ liệu, thuật toán pathfinding | ✅✅ **TDD triệt để** — viết test trước | GUT |
| **Hệ thống có phụ thuộc engine** | Inventory, save/load, quản lý wave spawn | ✅ Test sau khi viết (test-after), dùng mock/scene tối giản | GUT + scene test |
| **Physics / va chạm** | Hitbox trúng đúng không | ⚠️ Integration test bằng scene nhỏ, chạy vài frame | GUT `simulate()` |
| **Game feel / cảm giác** | Nhảy có "đã" không, hit-stop bao lâu | ❌ **Không TDD.** Chỉ tay chơi + chỉnh số | Bảng tinh chỉnh, hot-reload |
| **Đồ họa / shader** | Hiệu ứng trông đúng không | ❌ Kiểm tra bằng mắt / ảnh chụp so sánh | Thủ công |

### Vòng TDD thực tế của bạn (dùng GUT)

```gdscript
# test/unit/test_damage.gd
extends GutTest

func test_crit_nhan_doi_sat_thuong():
    var atk := AttackData.new(10.0, 0.0)     # base 10, crit_chance 0
    assert_eq(DamageCalc.compute(atk, 1.0), 10.0)

func test_giap_giam_theo_cong_thuc_giam_dan():
    # 100 giáp => giảm 50%
    assert_almost_eq(DamageCalc.compute_with_armor(100.0, 100.0), 50.0, 0.01)

func test_sat_thuong_khong_bao_gio_am():
    assert_gte(DamageCalc.compute_with_armor(1.0, 99999.0), 0.0)
```

Chạy trong terminal (và trong CI):

```bash
godot --headless -s addons/gut/gut_cmdln.gd -gdir=res://test -gexit
```

**Quy tắc thực dụng:** nếu một bug xuất hiện lần thứ hai, **bắt buộc** viết test cho nó trước khi sửa (regression test). Đây là cách xây bộ test hữu ích mà không tốn công đoán trước.

---

## 5. Definition of Done

Một thẻ chỉ được sang `Done` khi **tất cả** đúng:

- [ ] Code chạy, không lỗi đỏ trong Output console
- [ ] Logic thuần có unit test (nếu thuộc tầng TDD ở trên)
- [ ] Không còn `print()` debug rác; dùng logger có cấp độ
- [ ] Không hardcode magic number trong logic gameplay — đưa vào `@export` hoặc Resource
- [ ] Đã tự chơi thử ít nhất 1 lượt để chắc không phá thứ khác
- [ ] Commit theo Conventional Commits, đã merge vào `main`
- [ ] Nếu là feature người chơi thấy được → ghi 1 dòng vào devlog tuần

---

## 6. Clean code trong bối cảnh game (khác web/backend)

Bạn có nền IT nên dễ mắc bẫy over-engineering. Vài chỉnh sửa tư duy:

| Nguyên tắc quen thuộc | Điều chỉnh cho game |
|---|---|
| DRY tuyệt đối | Cho phép **lặp lại có kiểm soát** ở code gameplay. Prototype trùng lặp còn hơn abstraction sai. Refactor sau khi cơ chế đã được xác nhận là vui |
| Interface/abstraction sớm | Chỉ trừu tượng khi có **3 trường hợp thật**. Nguyên tắc "Rule of Three" |
| Kiến trúc phân tầng nghiêm ngặt | Ưu tiên **composition qua Node/Component** của Godot: `HealthComponent`, `HitboxComponent`, `StateMachine` — gắn vào bất kỳ entity nào |
| Config trong code | Đưa số liệu cân bằng ra **Resource (`.tres`)** hoặc JSON để chỉnh không cần build lại. Đây là đòn bẩy lớn nhất cho solo dev |
| Tối ưu sớm | **Cấm.** Chỉ tối ưu khi profiler chỉ ra điểm nóng. Ngoại lệ duy nhất: object pooling cho đạn (biết trước là cần) |

### Quy ước đặt tên trong dự án Godot

```
res://
├── scenes/          # PascalCase.tscn      -> Player.tscn, MainMenu.tscn
├── scripts/         # snake_case.gd        -> player_controller.gd
├── components/      # component tái dùng    -> health_component.gd
├── resources/       # dữ liệu .tres        -> enemy_chicken_lv1.tres
├── assets/
│   ├── sprites/
│   ├── audio/{sfx,music}/
│   └── fonts/
├── ui/
├── autoload/        # singleton: game_state.gd, audio_manager.gd, event_bus.gd
├── test/{unit,integration}/
└── addons/          # gut, v.v.
```

Node trong scene: PascalCase (`PlayerSprite`). Biến/hàm: `snake_case`. Hằng: `SCREAMING_SNAKE`. Signal: quá khứ, `health_depleted`, `wave_cleared`.

---

## 7. Tài liệu hóa — vừa đủ, không thừa

| Tài liệu | Khi nào viết | Độ dài |
|----------|-------------|--------|
| **GDD 1 trang** (`templates/gdd-template.md`) | Trước mỗi dự án game | Đúng 1 trang. Nếu dài hơn = scope quá lớn |
| **ADR** (`adr/NNNN-*.md`) | Khi ra quyết định kỹ thuật khó đảo ngược (chọn engine, chọn cách làm animation, chọn kiến trúc netcode) | ~20 dòng |
| **Devlog tuần** (`devlog/YYYY-Www.md`) | Chủ Nhật hằng tuần | 10–15 dòng |
| **Postmortem** | Sau mỗi game phát hành | 1–2 trang |
| **README dự án** | Ngay từ đầu, cập nhật dần | Ảnh GIF + cách build + cách chạy test |
| Comment trong code | Chỉ giải thích **tại sao**, không giải thích *cái gì* | — |

---

## 8. Đo lường tiến độ (chống ảo tưởng)

Theo dõi 4 con số trong devlog mỗi tuần:

1. **Giờ thực làm** (dùng `git log` + cảm nhận, không cần app đếm giờ)
2. **Số thẻ Done**
3. **Có build chạy được không?** (Y/N) — nếu 2 tuần liên tiếp là N, có vấn đề nghiêm trọng
4. **Cảm giác vui khi chơi: 1–5** — chỉ số quan trọng nhất mà không ai đo

Cuối mỗi giai đoạn, xem lại 4 con số này để quyết định có cần cắt scope giai đoạn sau không.
