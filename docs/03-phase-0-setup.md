# 03 — Giai đoạn 0: Dựng môi trường (Tuần 0 — 5 buổi, ~7h)

**Mục tiêu:** kết thúc tuần này, bạn có môi trường hoàn chỉnh, repo có CI xanh, và đã chạy được một scene Godot tự viết. Không học lý thuyết ở giai đoạn này.

---

## Buổi 1 (1.5h) — Cài Godot & công cụ nền

```bash
# Cách 1 (khuyến nghị): tải binary trực tiếp, dễ giữ nhiều phiên bản
mkdir -p ~/apps/godot && cd ~/apps/godot
# Tải bản Linux x86_64 (standard, KHÔNG phải .NET) mới nhất từ https://godotengine.org/download/linux/
# ví dụ: Godot_v4.x-stable_linux.x86_64.zip
unzip Godot_v4.*_linux.x86_64.zip
chmod +x Godot_v4.*_linux.x86_64
ln -sf ~/apps/godot/Godot_v4.*_linux.x86_64 ~/.local/bin/godot
godot --version

# Cách 2: Flatpak
# flatpak install flathub org.godotengine.Godot
```

Kiểm tra chạy headless (cần cho CI về sau):

```bash
godot --headless --quit
```

Cài phần còn thiếu:

```bash
sudo apt update
sudo apt install -y python3-pip cmake inkscape gimp krita audacity lmms linux-tools-generic
flatpak install -y flathub com.obsproject.Studio
# Pixel art: Pixelorama (nhẹ, viết bằng Godot) hoặc LibreSprite
flatpak install -y flathub com.orama_interactive.Pixelorama
```

**Ghi chú Wayland:** Godot 4 chạy Wayland native. Nếu gặp lỗi hiển thị lạ, ép X11: `godot --display-driver x11`.

---

## Buổi 2 (1.5h) — VS Code + Godot LSP

1. Cài extension: `geequlim.godot-tools` (Godot Tools), `alfish.godot-files` (tuỳ chọn).
2. Trong Godot: **Editor → Editor Settings → Text Editor → External**
   - Use External Editor: ✅
   - Exec Path: `/usr/bin/code` (kiểm tra: `command -v code`)
   - Exec Flags: `{project} --goto {file}:{line}:{col}`
3. Trong Godot: **Editor Settings → Network → Language Server** — cổng mặc định `6005`.
4. Trong VS Code, settings cho workspace:

```jsonc
// .vscode/settings.json  (không commit file này nếu chứa đường dẫn máy)
{
  "godotTools.editorPath.godot4": "/home/wk/.local/bin/godot",
  "godotTools.lsp.serverPort": 6005,
  "files.exclude": { "**/.godot": true }
}
```

5. Test: mở một `.gd`, gõ `Node2D.` → phải có autocomplete.

---

## Buổi 3 (1.5h) — Khởi tạo repo & cấu trúc dự án

> **Mục tiêu:** hiểu rõ *cái gì nằm ở đâu và vì sao*, rồi tạo commit đầu tiên sạch sẽ, đẩy lên GitHub.

### 3.0. Bức tranh tổng thể — hai tầng thư mục

Đây là chỗ gây nhầm lẫn nhiều nhất. Repo này có **hai tầng** với vai trò khác nhau:

```
/home/wk/projects/game-1/          ← TẦNG 1: REPO (một repo git duy nhất)
├── .git/                          
├── .gitignore                     ← ignore cho toàn repo
├── .gitattributes                 ← khai báo Git LFS
├── docs/                          ← roadmap, devlog, ADR
└── prototypes/                    ← nhiều dự án Godot nhỏ, mỗi cái độc lập
    ├── 00-hello/                  ← TẦNG 2: MỘT GODOT PROJECT
    │   ├── project.godot          ← file này định nghĩa "đây là 1 project Godot"
    │   ├── .godot/                ← cache do Godot sinh (KHÔNG commit)
    │   ├── .gitignore             ← Godot tự sinh, chỉ cho project này
    │   ├── .gitattributes         ← Godot tự sinh (eol=lf)
    │   ├── .editorconfig          ← Godot tự sinh
    │   ├── icon.svg
    │   └── main.gd, main.tscn ...  ← code của bạn
    ├── 01-pong/                   ← Tuần 1 sẽ tạo
    └── 02-breakout/               ← Tuần 2 sẽ tạo
```

Ba điều rút ra:

1. **Một repo git, nhiều Godot project.** `git init` chỉ chạy ở `game-1/`, không chạy trong từng prototype.
2. **`project.godot` là ranh giới của một Godot project.** Mọi đường dẫn `res://` trong code đều tính từ thư mục chứa file này. `res://main.gd` nghĩa là `prototypes/00-hello/main.gd`.
3. **Có 2 file `.gitignore` là bình thường.** Godot tự sinh một cái trong project khi bạn chọn *Version Control Metadata: Git* lúc tạo project. Git gộp cả hai: file ở thư mục con áp dụng cho thư mục đó, file ở root áp dụng cho toàn repo.

---

### 3.1. Cấu hình danh tính Git (bắt buộc, làm trước tiên)

Không có bước này thì `git commit` sẽ báo lỗi.

```bash
git config --global user.name "Tên của bạn"
git config --global user.email "anh666666anh@gmail.com"

# Đặt luôn để mọi repo sau này dùng nhánh 'main' (khớp GitHub)
git config --global init.defaultBranch main

# Kiểm tra
git config --global --list | grep -E "user\.|init\."
```

### 3.2. Đổi nhánh `master` → `main`

Repo đang ở nhánh `master`, nhưng GitHub mặc định `main` và **workflow CI ở Buổi 4 được cấu hình trigger trên `main`**. Đổi ngay khi chưa có commit nào để khỏi rắc rối:

```bash
cd /home/wk/projects/game-1
git branch -m master main
git branch --show-current      # phải in ra: main
```

### 3.3. `.gitignore` ở gốc repo

Godot đã tạo `.gitignore` trong `prototypes/00-hello/` rồi, nhưng nó chỉ có 2 dòng và chỉ áp dụng cho project đó. Cần một file ở gốc repo áp dụng cho mọi prototype tương lai:

```bash
cd /home/wk/projects/game-1
cat > .gitignore <<'GIT'
# ── Godot ─────────────────────────────────────────
# Cache import + dữ liệu tạm, Godot tự sinh lại được. Rất nặng, không commit.
.godot/
# Thư mục build Android
/android/
# Chứa đường dẫn tuyệt đối của máy bạn + keystore ký app. KHÔNG BAO GIỜ commit.
export_presets.cfg

# ── Build output ──────────────────────────────────
build/
*.zip
*.x86_64
*.wasm
*.pck
*.apk

# ── Hệ điều hành / editor ────────────────────────
.DS_Store
.vscode/settings.json
*.swp
GIT
```

**Giải thích 3 mục dễ nhầm:**

| Mục | Vì sao ignore |
|---|---|
| `.godot/` | Cache import: mỗi ảnh `.png` được Godot chuyển sang định dạng nội bộ và lưu ở đây. Sinh lại tự động khi mở project. Có thể phình tới hàng trăm MB. **Nhưng file `*.import` cạnh mỗi asset thì PHẢI commit** — nó chứa UID để scene tham chiếu đúng asset |
| `export_presets.cfg` | Chứa đường dẫn tuyệt đối (`/home/wk/...`) và có thể chứa mật khẩu keystore ký APK. Commit vào là rò rỉ thông tin |
| `.vscode/settings.json` | Chứa đường dẫn máy cá nhân (`/home/wk/.local/bin/godot`) → vô nghĩa với máy khác. Xem §3.5 |

### 3.4. Git LFS

**LFS là gì:** Git lưu toàn bộ lịch sử mọi file. Với file text điều đó rẻ (chỉ lưu phần khác biệt). Với file nhị phân (PNG, WAV) mỗi lần sửa là lưu một bản đầy đủ mới → repo phình rất nhanh. LFS thay file nhị phân bằng một con trỏ text nhỏ, nội dung thật lưu ở kho riêng.

**Vì sao thiết lập ngay dù `00-hello` chưa có asset nào:** LFS chỉ áp dụng cho file được thêm **sau khi** track. Nếu đợi tới lúc có 200 ảnh mới bật, phải chạy `git lfs migrate` viết lại lịch sử — phiền hơn nhiều. Bật ở commit đầu tiên là đúng thời điểm.

```bash
sudo apt install -y git-lfs
cd /home/wk/projects/game-1
git lfs install                 # cài hook vào repo này

git lfs track "*.png" "*.jpg" "*.ase" "*.aseprite" "*.psd" "*.kra"
git lfs track "*.wav" "*.ogg" "*.mp3"
git lfs track "*.ttf" "*.otf"

cat .gitattributes              # kiểm tra các dòng vừa thêm
```

> ⚠️ **Đừng track `*.svg`.** SVG là file text — Git xử lý tốt hơn LFS, và `icon.svg` của Godot cần đọc được dạng text.
>
> ⚠️ GitHub free cho 1 GB LFS storage + 1 GB băng thông/tháng. Pixel art 2D dùng không hết. Nếu gần chạm hạn: nén PNG bằng `oxipng`, dùng `.ogg` thay `.wav`.

### 3.5. VS Code workspace — trả lời "có cần `.vscode/settings.json` trong `00-hello` không?"

**Không cần — và file `.vscode/settings.json` hiện đang nằm sai chỗ.**

Lý do: extension `godot-tools` tìm `project.godot` **ở thư mục gốc của workspace đang mở**. Nếu bạn mở VS Code tại `game-1/`, nó không thấy `project.godot` (file đó nằm sâu trong `prototypes/00-hello/`) → không nhận diện đây là Godot project.

**Quy tắc:** mở VS Code **tại thư mục Godot project**, không phải tại repo root.

```bash
code /home/wk/projects/game-1/prototypes/00-hello     # khi code game
code /home/wk/projects/game-1                          # khi chỉ sửa docs/
```

Vậy đặt cấu hình ở đâu? Cả 3 setting đều **giống hệt nhau ở mọi Godot project** (đường dẫn Godot là cố định, port luôn 6005, `.godot/` luôn cần ẩn). Nên đưa lên **User settings** — dùng chung cho mọi project, không phải lặp lại trong từng prototype, và không bao giờ lọt vào git:

```bash
mkdir -p ~/.config/Code/User
cat > ~/.config/Code/User/settings.json <<'JSON'
{
  "godotTools.editorPath.godot4": "/home/wk/.local/bin/godot",
  "godotTools.lsp.serverPort": 6005,
  "files.exclude": { "**/.godot": true }
}
JSON

# Xóa file đặt sai chỗ ở repo root
rm -rf /home/wk/projects/game-1/.vscode
```

> Khi nào *mới* cần `.vscode/settings.json` trong project? Khi có cấu hình **đặc thù cho riêng project đó** và bạn muốn commit để dùng lại trên máy khác — ví dụ `editor.formatOnSave` cho GDScript, hoặc task chạy test. Chưa cần ở Buổi 3.

### 3.6. Tạo `main.gd` + `main.tscn` (chuẩn bị cho Buổi 5)

Buổi 4 (CI) cần một script để kiểm tra cú pháp, nên tạo file tối thiểu ngay:

```bash
cat > /home/wk/projects/game-1/prototypes/00-hello/main.gd <<'GD'
extends Node2D

@export var speed: float = 300.0

func _ready() -> void:
	print("Hello, game loop!")
GD
```

Scene `main.tscn` và việc gán làm scene khởi động thì làm trong GUI Godot ở Buổi 5 — không cần lúc này.

### 3.7. Commit đầu tiên

```bash
cd /home/wk/projects/game-1

git add -A
git status              # ĐỌC KỸ trước khi commit: không được thấy .godot/ hay export_presets.cfg

git commit -m "chore: khởi tạo repo, docs roadmap, dự án Godot 00-hello

- Thêm bộ tài liệu roadmap 12 tháng trong docs/
- Tạo Godot project 00-hello (renderer Compatibility)
- Cấu hình .gitignore và Git LFS cho asset nhị phân"
```

Nếu `git status` lỡ thấy `.godot/` đã được thêm (do add trước khi có `.gitignore`):

```bash
git rm -r --cached prototypes/00-hello/.godot
```

### 3.8. Đẩy lên GitHub

Chọn **public** — repo này là portfolio, và GitHub Actions chỉ miễn phí không giới hạn với repo public.

```bash
# Cách A: dùng gh CLI (gọn nhất)
sudo apt install -y gh
gh auth login                                    # chọn GitHub.com -> HTTPS -> đăng nhập trình duyệt
gh repo create game-1 --public --source=. --remote=origin --push

# Cách B: tạo repo thủ công trên github.com rồi:
# git remote add origin git@github.com:<username>/game-1.git
# git push -u origin main
```

Kiểm tra LFS hoạt động sau khi push:

```bash
git lfs ls-files        # hiện trống là đúng (chưa có asset nhị phân nào)
git lfs env | head -5   # xác nhận endpoint LFS đã trỏ đúng remote
```

---

### Checklist nghiệm thu Buổi 3

**Cấu hình**
- [ ] `git config --global user.name` và `user.email` có giá trị
- [ ] `git branch --show-current` → `main`
- [ ] `.gitignore` ở gốc repo tồn tại, có `.godot/` và `export_presets.cfg`
- [ ] `git lfs version` chạy được; `.gitattributes` ở gốc có các dòng `filter=lfs`
- [ ] `~/.config/Code/User/settings.json` có 3 khóa godotTools
- [ ] `game-1/.vscode/` đã xóa

**Repo sạch**
- [ ] `git status` → clean, không còn file untracked
- [ ] `git ls-files | grep '\.godot/'` → **không có kết quả nào** (cache không lọt vào git)
- [ ] `git ls-files | grep export_presets` → không có kết quả
- [ ] `git log --oneline` → có đúng 1 commit
- [ ] `du -sh .git` → dưới 5 MB

**GitHub**
- [ ] Repo public, mở được trên trình duyệt
- [ ] `git remote -v` trỏ đúng, `git push` thành công
- [ ] Trang repo hiển thị thư mục `docs/` và `prototypes/00-hello/`

**Kiểm chứng nốt Buổi 2** (giờ mới làm được vì đã có project)
- [ ] Mở Godot, load project `prototypes/00-hello`
- [ ] Terminal: `ss -ltn | grep 6005` → **có tiến trình lắng nghe**
- [ ] `code prototypes/00-hello`, mở `main.gd`, gõ `Node2D.` → hiện danh sách method
- [ ] Status bar godot-tools hiển thị **Connected**
- [ ] Ctrl+Click vào `Node2D` → nhảy tới định nghĩa
- [ ] Trong Godot double-click `main.gd` ở FileSystem → VS Code bật lên đúng file

---

## Buổi 4 (1.5h) — CI đầu tiên

`.github/workflows/ci.yml` — bước đầu chỉ kiểm tra project mở được và script không lỗi cú pháp:

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:

jobs:
  check:
    runs-on: ubuntu-latest
    container:
      image: barichello/godot-ci:4.3.0   # đổi tag theo phiên bản Godot bạn dùng
    steps:
      - uses: actions/checkout@v4
        with: { lfs: true }

      - name: Import project (tạo .godot cache)
        run: |
          cd prototypes/00-hello
          godot --headless --import || true

      - name: Kiểm tra lỗi script
        run: |
          cd prototypes/00-hello
          godot --headless --check-only --script res://main.gd
```

> Ở [P3](06-phase-3-engineering.md) bạn sẽ mở rộng workflow này thành: chạy GUT test + export build Web + upload lên itch.io tự động.

---

## Buổi 5 (1.5h) — "Hello, Game Loop"

Mục tiêu: hiểu 3 khái niệm cốt lõi trước khi học bất cứ điều gì khác — **scene tree**, **`_process` vs `_physics_process`**, **input**.

Tạo `main.gd` gắn vào một `Node2D`, thêm một `Sprite2D` con:

```gdscript
extends Node2D

@export var speed: float = 300.0
@onready var sprite: Sprite2D = $Sprite2D

func _physics_process(delta: float) -> void:
	var dir := Input.get_vector("ui_left", "ui_right", "ui_up", "ui_down")
	sprite.position += dir * speed * delta
```

**Bài tập (bắt buộc làm hết, mỗi cái ~5 phút):**
1. Đổi `_physics_process` thành `_process` — quan sát khác biệt khi kéo cửa sổ / đổi FPS.
2. Bỏ `* delta` — chạy game ở 30fps và 144fps, hiểu vì sao `delta` bắt buộc.
3. Dùng `dir.normalized()` — vì sao đi chéo lại nhanh hơn nếu không normalize? (gợi ý: định lý Pythagoras)
4. In `sprite.position` mỗi 60 frame bằng biến đếm — làm quen Output console.

**Deliverable của P0:** repo public trên GitHub, CI badge xanh, một sprite di chuyển được bằng phím mũi tên.

---

## Checklist kết thúc P0

- [ ] `godot --version` chạy, `godot --headless --quit` không lỗi
- [ ] VS Code autocomplete GDScript hoạt động
- [ ] Repo public, `.gitignore` + LFS đã cấu hình
- [ ] GitHub Actions chạy xanh
- [ ] Hiểu được sự khác nhau giữa `_process` và `_physics_process`, và vì sao cần `delta`
- [ ] Đã tạo `docs/devlog/2026-W37.md` từ template
