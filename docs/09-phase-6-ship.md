# 09 — Giai đoạn 6: Hoàn thiện, phát hành, và kế hoạch năm 2 (Tuần 41–48, ~72h)

**Mục tiêu:** biến hai game đã có thành sản phẩm portfolio thật sự, học phần "phi kỹ thuật" mà mọi indie dev đều thiếu, và lập kế hoạch năm tiếp theo dựa trên dữ liệu thật.

---

## Tuần 41–42 — Polish pass cuối cho *Silhouette Duel*

Danh sách polish theo thứ tự ROI (lợi ích / công sức):

| Hạng mục | Công sức | Tác động | Ghi chú |
|---|---|---|---|
| Menu chuyển cảnh mượt (fade, không giật) | Thấp | Cao | Ấn tượng đầu tiên |
| Settings đầy đủ: âm lượng, đổi phím, fullscreen, rung màn hình bật/tắt | Thấp | Cao | Nhiều người bỏ game vì không đổi được phím |
| Tutorial 3 màn hình | Trung bình | Rất cao | Người chơi bỏ ở 30 giây đầu là phổ biến nhất |
| Accessibility: cỡ chữ, tắt screen shake, colorblind-safe, chỉnh tốc độ game | Trung bình | Cao | Cũng là điểm cộng lớn khi review |
| Hỗ trợ gamepad | Trung bình | Trung bình | Game đối kháng gần như bắt buộc |
| Bảng "controls" trong pause menu | Thấp | Trung bình | |
| Đa ngôn ngữ (VI + EN) qua CSV translation của Godot | Trung bình | Trung bình | Mở rộng đối tượng |
| Đảm bảo 60fps ổn định trên chính máy bạn (i5-1235U/Iris Xe = máy tầm thấp → tốt cho testing) | Trung bình | Cao | Máy bạn chính là "máy tối thiểu" tốt |

**Bug bash:** dành trọn 2 buổi chỉ để cố phá game — resize cửa sổ giữa trận, alt-tab, spam mọi phím, pause đúng lúc trúng đòn, mất mạng khi nộp điểm.

---

## Tuần 43 — Trang phát hành & tài sản marketing

Bạn không cần biết marketing. Bạn cần làm đủ 6 thứ:

1. **Trailer 30–45 giây** (OBS + Kdenlive/Shotcut, đều FOSS)
   - 0–3s: cảnh đánh nhau ấn tượng nhất (hook ngay, không có logo mở đầu)
   - 3–25s: cắt nhanh các cơ chế: parry, combo, boss
   - 25–30s: tên game + link
2. **GIF động** cho trang store (quan trọng hơn ảnh tĩnh rất nhiều)
3. **5 ảnh chụp màn hình** chất lượng, không có UI debug
4. **Capsule/thumbnail** — làm bằng Inkscape, chữ to, tương phản cao, đọc được ở kích thước nhỏ
5. **Mô tả**: 1 câu hook → 3 gạch đầu dòng tính năng → GIF → controls → credits asset
6. **Tag đúng**: `fighting`, `2d`, `godot`, `singleplayer`, `pixel-art`/`silhouette`

**Nơi đăng (miễn phí):** itch.io (chính), GameJolt, Newgrounds (web game), IndieDB.
**Nơi chia sẻ:** r/godot, r/IndieDev, r/playmygame, Godot Discord `#showcase`, TIGSource forums, Mastodon/Bluesky `#screenshotsaturday`, cộng đồng Gamedev VN trên Facebook/Discord.

> **Quy tắc `#screenshotsaturday`:** từ P4 trở đi, mỗi thứ Bảy đăng 1 GIF tiến độ. Chi phí 10 phút, xây dựng khán giả dần. Bắt đầu càng sớm càng tốt — đây là lời khuyên marketing giá trị nhất cho indie solo.

---

## Tuần 44 — Analytics & học từ người chơi thật

Dùng backend Tuần 30 để thu thập (tôn trọng quyền riêng tư, ẩn danh, có tùy chọn tắt):

| Sự kiện | Câu hỏi trả lời được |
|---|---|
| `session_start`, `session_end` | Chơi bao lâu một lượt? |
| `tutorial_step_completed` | Bỏ ở bước nào? |
| `match_start` / `match_end {opponent_id, result, duration}` | Đối thủ nào là bức tường chặn người chơi? |
| `player_death {opponent_id, remaining_hp_of_enemy}` | Trận thua sát nút hay bị nghiền nát? |
| `quit_at {screen}` | Rời game ở đâu? |
| `upgrade_chosen` | Build nào phổ biến → cân bằng lại |

Sau 2 tuần dữ liệu → viết `docs/analytics-findings.md` và sửa cân bằng dựa trên số liệu, không dựa trên cảm tính. Đây là kỹ năng "live-ops" trong roadmap server-side gốc.

---

## Tuần 45 — Đóng gói portfolio

- **README GitHub cho từng repo**: GIF ở đầu, mô tả kiến trúc, sơ đồ, hướng dẫn build/test, badge CI
- **Trang portfolio tĩnh** (GitHub Pages, HTML/CSS thuần hoặc Astro — bạn thạo web): 3 dự án, mỗi cái có GIF + link chơi + link code + 3 dòng "kỹ thuật đáng chú ý"
- **Devlog công khai**: đăng bộ devlog 48 tuần lên itch.io devlog hoặc blog cá nhân
- **Bài viết kỹ thuật 1 bài** (giá trị portfolio rất cao): ví dụ *"Replay verification chống cheat leaderboard trong game Godot"* hoặc *"Frame data và hitstop: làm combat 2D có cảm giác đấm thật"*

---

## Tuần 46 — Postmortem toàn năm

Dùng `templates/postmortem-template.md`, nhưng cho cả 12 tháng. Trả lời trung thực:

1. Tổng số giờ thực tế đã bỏ ra so với 450h dự kiến?
2. Giai đoạn nào bị trượt tiến độ nhiều nhất? Vì sao?
3. Kỹ năng nào tiến bộ nhanh nhất / chậm nhất?
4. Trong 3 sản phẩm, cái nào bạn tự hào nhất và vì sao?
5. Điều gì bạn sẽ **không lặp lại**?
6. Bạn *thực sự* thích phần nào: gameplay programming, engine tech, backend, hay design? → định hướng năm 2.

---

## Tuần 47–48 — Lập kế hoạch năm 2

Chọn **một** hướng, không chọn hai:

| Hướng | Nếu bạn nhận ra mình thích… | Kế hoạch năm 2 |
|---|---|---|
| **A. Indie thương mại** | Làm game hoàn chỉnh, muốn có doanh thu | 1 game duy nhất 8–12 tháng, scope vừa, phát hành **Steam** (phí $100 Steam Direct — khoản chi duy nhất đáng cân nhắc), wishlist campaign, demo Next Fest |
| **B. Kỹ thuật sâu / xin việc studio** | Engine, rendering, tối ưu, netcode | GDExtension C++, tự viết engine 2D nhỏ bằng raylib/SDL3, học đồ họa (OpenGL → Vulkan cơ bản), rollback netcode hoàn chỉnh, đóng góp PR cho Godot |
| **C. Backend/multiplayer chuyên biệt** | Server, hạ tầng, hệ thống phân tán | Game server framework của riêng bạn, dedicated server orchestration, học Rust/Go cho server, đóng góp cho Nakama/Colyseus |
| **D. Đa dạng & học rộng | Thích thử nhiều thứ | 6 game jam (Ludum Dare, GMTK, Godot Wild Jam), mỗi cái một thể loại khác — cách học game design nhanh nhất |

> **Gợi ý dựa trên hồ sơ của bạn:** nền IT mạnh + thích thể loại action → hướng **A** với điểm mạnh kỹ thuật từ **C** là kết hợp thực tế nhất. Một game hành động 2D có leaderboard/PvP online là một sản phẩm indie có sức cạnh tranh thật, và bạn có lợi thế hiếm ở phần backend mà đa số indie dev không có.

## Deliverable P6

- [ ] Trailer + trang store hoàn chỉnh cho cả 2 game
- [ ] Trang portfolio công khai
- [ ] 1 bài viết kỹ thuật đã xuất bản
- [ ] `docs/postmortem-year-1.md`
- [ ] `docs/year-2-plan.md`
