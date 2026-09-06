# Solo Game Dev Roadmap — Index

> Lộ trình 12 tháng tự học và phát triển game từ con số 0, dành cho lập trình viên đã có nền tảng IT.
> Cá nhân hóa từ [roadmap.sh/game-developer](https://roadmap.sh/game-developer) và
> [roadmap.sh/server-side-game-developer](https://roadmap.sh/server-side-game-developer).

**Ngày khởi tạo:** 2026-09-06
**Quỹ thời gian:** 1–2 giờ/ngày, 6 ngày/tuần (~9 giờ/tuần, ~450 giờ/năm)
**Chế độ làm việc:** solo toàn bộ (code, art, audio, design, release)

---

## Đọc theo thứ tự

| # | File | Nội dung | Khi nào đọc |
|---|------|----------|-------------|
| 00 | [00-roadmap-overview.md](00-roadmap-overview.md) | Bản đồ tổng thể 12 tháng, 6 giai đoạn, các mốc sản phẩm | Đọc đầu tiên |
| 01 | [01-tech-stack.md](01-tech-stack.md) | Phân tích cấu hình máy + bảng so sánh engine/ngôn ngữ + khuyến nghị | Đọc thứ hai |
| 02 | [02-workflow.md](02-workflow.md) | Quy trình cá nhân: Kanban 1 người, Git, TDD, Definition of Done | Trước khi viết dòng code đầu tiên |
| 03 | [03-phase-0-setup.md](03-phase-0-setup.md) | Tuần 0 — dựng môi trường | Tuần 0 |
| 04 | [04-phase-1-fundamentals.md](04-phase-1-fundamentals.md) | Tuần 1–4 — nền tảng Godot + toán game | Tháng 1 |
| 05 | [05-phase-2-first-release.md](05-phase-2-first-release.md) | Tuần 5–10 — game bắn gà, release thật | Tháng 2–3 |
| 06 | [06-phase-3-engineering.md](06-phase-3-engineering.md) | Tuần 11–16 — kiến trúc, TDD, CI, profiling | Tháng 3–4 |
| 07 | [07-phase-4-combat-game.md](07-phase-4-combat-game.md) | Tuần 17–28 — game combat kiểu Shadow Fight | Tháng 5–7 |
| 08 | [08-phase-5-server-side.md](08-phase-5-server-side.md) | Tuần 29–40 — netcode + backend game server | Tháng 8–10 |
| 09 | [09-phase-6-ship.md](09-phase-6-ship.md) | Tuần 41–48 — polish, marketing, phát hành | Tháng 11–12 |
| 10 | [10-game-ideas.md](10-game-ideas.md) | 8 ý tưởng game khả thi giai đoạn đầu | Khi cần chọn dự án |
| 11 | [11-assets-art-audio.md](11-assets-art-audio.md) | Nguồn asset miễn phí + kỹ thuật tự làm art/audio cho người không biết vẽ | Từ Tuần 3 trở đi |
| 12 | [12-resources.md](12-resources.md) | Khóa học, sách, kênh, cộng đồng miễn phí theo từng giai đoạn | Tra cứu liên tục |
| 13 | [13-skills-checklist.md](13-skills-checklist.md) | Checklist kỹ năng ánh xạ từ 2 roadmap.sh gốc — tự đánh dấu tiến độ | Review mỗi cuối tháng |

## Thư mục phụ

```
docs/
├── templates/          # Mẫu tài liệu dùng lại
│   ├── gdd-template.md         # Game Design Doc 1 trang
│   ├── devlog-template.md      # Nhật ký tuần
│   ├── postmortem-template.md  # Tổng kết sau mỗi dự án
│   └── adr-template.md         # Architecture Decision Record
├── devlog/             # Nhật ký thực tế, mỗi tuần 1 file: 2026-W37.md
└── adr/                # Quyết định kỹ thuật đã chốt: 0001-chon-godot.md
```

## Nguyên tắc vàng của lộ trình này

1. **Ship > Perfect.** Mỗi giai đoạn phải kết thúc bằng một file build chạy được, không phải một branch dở dang.
2. **Scope nhỏ đến mức thấy buồn cười.** Với 9 giờ/tuần, một dự án 4 tuần = 36 giờ. Đó là *rất ít*. Luôn cắt scope một nửa so với bản năng ban đầu.
3. **Không học ngang.** Không đụng vào engine thứ hai, ngôn ngữ thứ hai, hay 3D cho tới khi đã release ít nhất 2 game 2D.
4. **Art là thứ đi mượn cho tới Tháng 6.** Không tự vẽ khi chưa biết game có vui hay không.
5. **Mỗi tuần đều commit.** Chuỗi commit là bằng chứng động lực, và là portfolio.
