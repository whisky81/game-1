# ADR-0001: Chọn Godot 4 + GDScript, làm 2D trong năm đầu

- **Ngày:** 2026-09-06
- **Trạng thái:** Đã chấp nhận
- **Bối cảnh dự án:** Toàn bộ roadmap năm 1

## Bối cảnh

Bắt đầu học phát triển game từ con số 0 với các ràng buộc:
- Phần cứng: Dell Inspiron 15 3520, i5-1235U (15W), **14 GiB RAM (chỉ ~6.7 GiB khả dụng)**, Intel Iris Xe iGPU, không GPU rời
- OS: Ubuntu 26.04, GNOME/Wayland
- Thời gian: 1–2h/ngày, làm solo hoàn toàn
- Ràng buộc: chỉ dùng công cụ miễn phí / mã nguồn mở
- Nền tảng sẵn có: JS/TS, Python, C/C++, Bash, Git, Docker, VS Code
- Thể loại mong muốn: shoot-em-up (bắn gà) và đối kháng 2D (Shadow Fight)

## Các lựa chọn đã cân nhắc

| Lựa chọn | Ưu | Nhược |
|---|---|---|
| **Godot 4 + GDScript** | Editor ~0.4–0.7 GB RAM; FOSS/MIT tuyệt đối; Linux hạng nhất, Wayland native; GDScript ≈ Python; export Web tốt; multiplayer API sẵn | Hệ sinh thái asset/plugin nhỏ hơn Unity; ít cơ hội việc làm studio hơn |
| Unity 6 + C# | Hệ sinh thái lớn nhất, nhiều tutorial, cơ hội việc làm | Editor 3–4 GB RAM → **không khả thi với 6.7 GiB khả dụng**; Linux editor hỗ trợ hạn chế; license đóng, đã từng thay đổi bất lợi |
| Unreal 5 + C++ | Mạnh nhất về đồ họa | 6–8 GB RAM editor, cần GPU rời, royalty 5%, quá nặng cho 2D solo |
| Phaser 3 + TypeScript | Dùng đúng ngôn ngữ đang thạo nhất, không tốn RAM editor | **Không có editor** — phải tự viết animation/tilemap/particle/audio system, tiêu tốn thời gian lẽ ra dành cho học game design |
| raylib + C/C++ | Quen ngôn ngữ, cực nhẹ | Phải tự xây gần như toàn bộ engine |
| Bevy + Rust | Kiến trúc ECS hiện đại | Phải học ngôn ngữ mới; compile chậm trên CPU 15W |

## Quyết định

Chọn **Godot 4.x (bản standard, không .NET) + GDScript**, làm **game 2D** trong toàn bộ 12 tháng đầu, renderer **Compatibility**.

Lý do quyết định (theo thứ tự trọng số):
1. **RAM là nút thắt thật sự của máy này**, không phải GPU. Godot là engine đầy đủ duy nhất chạy thoải mái cùng lúc với VS Code + Docker trong 6.7 GiB khả dụng.
2. Linux + Wayland là môi trường hạng nhất của Godot, hạng hai của các engine khác.
3. GDScript cho tốc độ lặp cao nhất, cú pháp gần Python đã quen.
4. FOSS hoàn toàn → thỏa ràng buộc, không có rủi ro license khi thương mại hóa.
5. 2D loại bỏ lớp phức tạp không cần thiết (rig, lighting, LOD, UV) và phù hợp iGPU; cả hai thể loại mong muốn đều tự nhiên là 2D.

## Hệ quả

**Tích cực:**
- Thời gian khởi động dự án tính bằng phút, không phải giờ
- Máy không swap khi làm việc → phiên làm việc 1–2h không bị lãng phí
- Toàn bộ pipeline (editor, CI, export) chạy được trên Linux, headless được → CI dễ
- 2D cho phép tập trung vào game design và game feel — thứ thực sự đang thiếu

**Tiêu cực / chi phí chấp nhận:**
- Ít asset store phong phú như Unity — bù bằng Kenney/OpenGameArt (xem [11-assets-art-audio.md](../11-assets-art-audio.md))
- GDScript chậm hơn C#/C++ ở vòng lặp nóng — bù bằng static typing, object pooling, và GDExtension C++ nếu profiler chứng minh cần
- Nếu sau này muốn xin việc studio AAA, có thể phải học thêm Unity/Unreal

**Khi nào xem xét lại:**
- Sau khi **phát hành xong game thứ hai** (cuối Tuần 28). Trước mốc đó, mọi ý định đổi engine được coi là trì hoãn trá hình.
- Hoặc khi có yêu cầu cụ thể bắt buộc (ví dụ port sang console, hoặc yêu cầu công việc).
