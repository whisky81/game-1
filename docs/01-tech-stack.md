# 01 — Phân tích cấu hình máy & lựa chọn công nghệ

## 1. Cấu hình máy thực tế (đo ngày 2026-09-06)

| Thành phần | Giá trị | Ý nghĩa cho game dev |
|-----------|---------|----------------------|
| Máy | Dell Inspiron 15 3520 (laptop phổ thông) | Không có tản nhiệt tốt → tránh build kéo dài, tránh engine ngốn CPU |
| CPU | Intel Core i5-1235U — 2 P-core + 8 E-core, 12 threads, 0.4–4.4 GHz | Đủ mạnh cho compile/editor. **Nhưng là chip U-series 15W**: hiệu năng đơn luồng bị giới hạn nhiệt khi chạy lâu |
| RAM | **14 GiB tổng, ~6.7 GiB khả dụng** (8.1 GiB đang bị chiếm) | ⚠️ Đây là ràng buộc lớn nhất. Unity Editor + IDE + trình duyệt dễ chạm trần |
| GPU | Intel Iris Xe (iGPU, chia sẻ RAM hệ thống) | Thừa cho 2D. Chỉ làm được 3D nhẹ (low-poly, stylized). **Không đủ cho Unreal 5 Nanite/Lumen** |
| Ổ đĩa | NVMe 468 GB, còn trống 416 GB | Thoải mái. NVMe giúp import asset / build nhanh |
| OS | Ubuntu 26.04 LTS, GNOME trên **Wayland** | ⚠️ Một số công cụ (Unity, vài phần mềm cũ) còn lỗi vặt trên Wayland; Godot 4 hỗ trợ Wayland native tốt |
| Đã có sẵn | Node 22, Python 3.14, GCC 15, Git 2.53, Docker 29, VS Code | Backend + CI đã sẵn sàng, không cần cài gì thêm cho P5 |
| Còn thiếu | `pip`, `cmake`, Godot, Blender, công cụ pixel art | Cài ở [P0](03-phase-0-setup.md) |

### Ba kết luận rút ra từ phần cứng

1. **RAM là nút thắt, không phải GPU.** Với 6.7 GiB khả dụng, một editor ngốn 3–4 GiB (Unity) sẽ khiến bạn swap liên tục khi mở thêm VS Code + Chrome + Docker. Godot 4 editor thường chỉ dùng **300–700 MB** cho dự án 2D → đây là khác biệt quyết định.
2. **iGPU Iris Xe = làm 2D.** Iris Xe chạy 2D pixel/vector ở 60fps không vấn đề, chạy 3D low-poly ở 1080p tạm ổn, nhưng render lighting hiện đại thì không. Bạn nên **bắt đầu và ở lại 2D ít nhất 12 tháng** — điều này cũng đúng về mặt học tập, không chỉ vì phần cứng.
3. **Linux + Wayland loại bớt lựa chọn.** Unreal trên Linux phải build from source cho nhiều thứ; Unity Linux editor là bản "hỗ trợ hạn chế". Godot là engine duy nhất trong nhóm lớn coi Linux là công dân hạng nhất.

---

## 2. Bảng so sánh engine (đánh giá riêng cho cấu hình + bối cảnh của bạn)

| Tiêu chí | **Godot 4.x** | Unity 6 | Unreal 5 | Phaser 3 / Excalibur (JS) | LÖVE 2D (Lua) | raylib (C/C++) | Bevy (Rust) |
|---|---|---|---|---|---|---|---|
| Giấy phép | MIT, hoàn toàn FOSS | Đóng, free có điều kiện | Đóng, royalty 5% | MIT | zlib | zlib | MIT/Apache |
| RAM editor (dự án 2D) | **~0.4–0.7 GB** ✅ | ~3–4 GB ❌ | ~6–8 GB ❌❌ | 0 (dùng VS Code) ✅ | 0 ✅ | 0 ✅ | 0 ✅ |
| Chạy tốt trên Iris Xe | ✅ Rất tốt | ⚠️ Được, ì ạch | ❌ Không thực tế | ✅ | ✅ | ✅ | ⚠️ Compile chậm |
| Chất lượng trên Linux/Wayland | ✅ Native, hạng nhất | ⚠️ Hỗ trợ hạn chế | ⚠️ Cần build nhiều | ✅ | ✅ | ✅ | ✅ |
| Ngôn ngữ | GDScript (giống Python), C#, C++ | C# | C++/Blueprint | TS/JS ✅ | Lua | C/C++ ✅ | Rust (phải học mới) |
| Khớp với ngôn ngữ bạn biết | ✅ GDScript ≈ Python; C# dễ với TS | ⚠️ Phải học C# | ⚠️ C++ nhưng framework khổng lồ | ✅✅ TS bạn đã thạo | ❌ Lua mới | ✅✅ C bạn đã biết | ❌ |
| Có editor trực quan (scene, tilemap, animation) | ✅ Đầy đủ | ✅ Đầy đủ | ✅ | ❌ Code hết | ❌ | ❌ | ❌ |
| Tốc độ ra prototype | ✅✅ Rất nhanh | ⚠️ Trung bình | ❌ Chậm | ⚠️ Phải tự viết nhiều hệ thống | ⚠️ | ❌ Tự viết mọi thứ | ❌ |
| Export Web (HTML5) | ✅ Tốt (Godot 4.3+) | ⚠️ Build nặng | ❌ | ✅✅ Bản chất là web | ✅ | ✅ (emscripten) | ⚠️ |
| Export Android | ✅ | ✅ | ⚠️ | ✅ (Capacitor) | ✅ | ⚠️ | ⚠️ |
| Tài liệu & cộng đồng tiếng Việt/Anh | ✅ Rất nhiều, miễn phí | ✅✅ Nhiều nhất | ✅ | ✅ | ⚠️ | ⚠️ | ❌ |
| Hợp cho 2D combat/shmup | ✅✅ | ✅ | ⚠️ Quá nặng | ✅ | ✅ | ⚠️ | ⚠️ |
| Networking tích hợp (cho P5) | ✅ High-level Multiplayer API + ENet + WebSocket | ✅ Netcode for GameObjects | ✅ | ⚠️ Tự làm | ⚠️ | ❌ | ⚠️ |
| **Điểm tổng cho bạn** | **9.5/10** | 5/10 | 2/10 | 7/10 | 5/10 | 5/10 | 3/10 |

### Khuyến nghị chốt

> ### 🎯 **Godot 4.x + GDScript** làm engine chính. **2D**, không 3D, trong toàn bộ năm đầu.

**Vì sao Godot thắng ở trường hợp của bạn:**
- Nhẹ hơn Unity ~6 lần về RAM → là engine *duy nhất* trong nhóm "engine đầy đủ" chạy thoải mái trên 6.7 GiB khả dụng cùng lúc với VS Code + Docker.
- Editor 130 MB, tải về là chạy, không cần Hub/tài khoản/license.
- GDScript có cú pháp gần Python — bạn viết được ngay ngày đầu, và nó được thiết kế riêng cho scene tree nên ngắn hơn C# rất nhiều cho cùng một việc.
- Node/Scene composition dạy bạn tư duy component tự nhiên, ánh xạ thẳng vào nhánh "Design Patterns" của roadmap gốc.
- FOSS 100% → thỏa ràng buộc "chỉ dùng miễn phí/mã nguồn mở" một cách tuyệt đối, không có bẫy license về sau khi bạn muốn kiếm tiền.

**Vì sao KHÔNG chọn các lựa chọn khác (dù chúng hấp dẫn):**
- *Phaser/TypeScript* — cám dỗ lớn nhất vì bạn đã giỏi TS. Nhưng nó **không có editor**: bạn sẽ tự viết animation system, tilemap editor, particle, audio bus... Thời gian đó lẽ ra dùng để học *thiết kế game*. Vẫn dùng Phaser trong 1 ý tưởng nhỏ ở P1 để so sánh (xem [10-game-ideas.md](10-game-ideas.md)).
- *Unity* — hệ sinh thái tốt nhất nhưng sai phần cứng, sai OS, và mô hình license đã từng thay đổi bất lợi (2023). Học lại sau nếu muốn xin việc studio.
- *raylib/C++* — bạn biết C/C++ nên sẽ thấy quen, nhưng làm game hoàn chỉnh bằng raylib nghĩa là tự viết cả engine. Đó là dự án học tập tuyệt vời cho **năm 2**, không phải năm 1.

### Khi nào được phép đổi ý
Chỉ sau khi **release xong game thứ 2** (cuối P4). Trước đó, mọi ham muốn đổi engine đều là dạng trì hoãn trá hình. Ghi vào [adr/](adr/) nếu thật sự đổi.

---

## 3. Ngôn ngữ trong từng phần dự án

| Phần việc | Ngôn ngữ | Lý do |
|-----------|----------|-------|
| Gameplay, UI, hầu hết mọi thứ | **GDScript** | Tốc độ lặp cao nhất, hot-reload, tích hợp sâu editor |
| Đoạn tính toán nặng (pathfinding lớn, procedural gen) | **GDExtension C++** — chỉ khi profiler chứng minh cần | Bạn có sẵn GCC 15. Nhưng đừng tối ưu sớm |
| Công cụ nội bộ (đóng gói asset, sinh atlas, kiểm tra dữ liệu) | **Python 3 / Bash** | Bạn đã thạo, không cần liên quan engine |
| Backend game server (P5) | **TypeScript + Node 22** | Bạn thạo nhất → dành năng lượng học *khái niệm netcode*, không phải học ngôn ngữ |
| CSDL | **PostgreSQL** (bền vững) + **Redis** (phiên/matchmaking) | Chuẩn công nghiệp, FOSS, chạy qua Docker đã có |
| CI | **GitHub Actions** | Free cho repo public |

---

## 4. Bộ công cụ đầy đủ — tất cả miễn phí/FOSS

| Nhu cầu | Công cụ | Cài trên Ubuntu 26.04 | Ghi chú |
|---------|---------|----------------------|---------|
| Engine | Godot 4.x (bản .NET không cần) | Tải từ godotengine.org hoặc `flatpak install flathub org.godotengine.Godot` | Dùng bản standard, không phải Mono |
| Code editor | VS Code + ext. `godot-tools` | Đã có | Bật LSP port 6005 |
| Pixel art | **LibreSprite** (fork FOSS của Aseprite) hoặc **Pixelorama** (viết bằng Godot!) | `flatpak install flathub com.github.libresprite.LibreSprite` | Pixelorama chạy nhẹ, hợp máy bạn |
| Ảnh vector / UI | **Inkscape** | `sudo apt install inkscape` | Xuất SVG → Godot đọc trực tiếp |
| Ảnh bitmap / xử lý | **GIMP 3** / **Krita** | `sudo apt install gimp krita` | Krita tốt hơn cho vẽ tay |
| Âm thanh SFX | **jsfxr** (web), **ChipTone** (web), **sfxr-qt** | Không cần cài — dùng web | Tạo SFX retro trong 10 giây |
| Nhạc | **LMMS** hoặc **BeepBox** (web) | `sudo apt install lmms` | BeepBox dễ hơn nhiều cho người mới |
| Sửa audio | **Audacity** / **Tenacity** | `sudo apt install audacity` | Cắt, normalize, fade |
| Sprite atlas | **Free TexturePacker** hoặc chức năng sẵn trong Godot | — | Godot 4 tự gộp được |
| 3D (chỉ khi cần, năm 2) | **Blender 4.x** | `flatpak install flathub org.blender.Blender` | Nặng với iGPU — dùng chế độ Solid |
| Quản lý task | **GitHub Projects** (Kanban) | Web, free | Xem [02-workflow.md](02-workflow.md) |
| Ghi chú | Markdown trong `docs/` + VS Code | Đã có | Không cần Notion |
| Quay video devlog | **OBS Studio** | `flatpak install flathub com.obsproject.Studio` | Dùng từ P2 để làm trailer |
| Test framework | **GUT** (Godot Unit Test) | Asset Library trong Godot | Xem [P3](06-phase-3-engineering.md) |
| Profiling | Godot built-in profiler + `perf` | `sudo apt install linux-tools-generic` | |

---

## 5. 2D hay 3D? — Trả lời dứt khoát

**2D. Trong ít nhất 12 tháng.** Ba lý do, xếp theo mức quan trọng:

1. **Lý do học tập (quan trọng nhất):** 2D loại bỏ toàn bộ lớp phức tạp về camera, lighting, rigging, LOD, UV — để bạn tập trung vào thứ thực sự khó: *làm cho game vui*. 90% kỹ năng game design học từ 2D chuyển thẳng sang 3D được.
2. **Lý do sản xuất:** một solo dev tạo được 200 sprite trong thời gian tạo 5 model 3D có rig. 3D là nơi các dự án solo chết.
3. **Lý do phần cứng:** Iris Xe + 14GB RAM. Blender + Godot 3D cùng lúc sẽ swap.

Hai thể loại bạn thích đều thuộc 2D một cách tự nhiên:
- *Bắn gà (Galaga/Chicken Invaders)* → shoot-em-up 2D. **Hoàn hảo cho dự án đầu tiên.**
- *Shadow Fight* → thực chất là **2.5D**: nhân vật 3D có rig nhưng gameplay khóa trên mặt phẳng 2D. Bản clone khả thi cho bạn là **2D skeletal animation** (Godot Skeleton2D + Polygon2D, hoặc sprite frame-by-frame). Xem [P4](07-phase-4-combat-game.md).

---

## 6. Ràng buộc vận hành cho máy 14GB

Ghi nhớ để tránh khổ sở:

```bash
# Trước phiên làm việc: đóng trình duyệt nhiều tab, dừng container không dùng
docker ps -q | xargs -r docker stop

# Theo dõi RAM khi Godot chạy
watch -n 2 free -h

# Nếu hay swap: tăng vm.swappiness thấp lại để ưu tiên giữ RAM
cat /proc/sys/vm/swappiness   # mặc định 60; cân nhắc 10 cho desktop
```

- Không mở Godot editor + Blender + Chrome (>20 tab) cùng lúc.
- Bật **"Convert Text Resources To Binary"** khi export để build nhanh hơn.
- Đặt project trên NVMe (đã đúng: `/home/wk/projects`), không trên ổ mạng.
- Với dự án 2D, giữ texture ≤ 2048px; iGPU dùng chung RAM hệ thống.
