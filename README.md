# game-1 — Sandbox học phát triển game

[![CI](https://github.com/whisky81/game-1/actions/workflows/ci.yml/badge.svg)](https://github.com/whisky81/game-1/actions/workflows/ci.yml)

Repo học tập theo lộ trình 12 tháng tự học phát triển game từ con số 0.
Toàn bộ roadmap nằm trong [`docs/`](docs/README.md).

## Ngăn xếp công nghệ

| Thành phần | Lựa chọn |
|---|---|
| Engine | Godot 4.7.2 (renderer Compatibility) |
| Ngôn ngữ | GDScript |
| Nền tảng đích | Web (HTML5) + Linux |
| CI | GitHub Actions + `barichello/godot-ci` |

Lý do chọn Godot: xem [ADR-0001](docs/adr/0001-chon-godot-4-va-2d.md).

## Cấu trúc

```
docs/           Roadmap, devlog, ADR, template
prototypes/     Các dự án Godot nhỏ dùng để học
└── 00-hello/   Prototype đầu tiên — game loop, input, delta
```

Mỗi thư mục con của `prototypes/` là một Godot project độc lập
(có `project.godot` riêng). Repo git thì chỉ có một, ở thư mục gốc.

## Chạy thử

```bash
# Mở project trong editor
godot --path prototypes/00-hello

# Chạy game luôn, không mở editor
godot --path prototypes/00-hello --headless --quit   # kiểm tra project mở được
```

## Kiểm tra như CI làm

```bash
cd prototypes/00-hello
godot --headless --import
godot --headless --check-only --script res://main.gd
```

## Tiến độ

- [x] **P0** — Dựng môi trường (Godot, VS Code + LSP, repo, CI)
- [ ] **P1** — Nền tảng Godot + toán game 2D (Tuần 1–4)
- [ ] **P2** — Game hoàn chỉnh đầu tiên, phát hành itch.io (Tuần 5–10)
- [ ] **P3** — Kiến trúc, TDD, CI/CD, profiling (Tuần 11–16)
- [ ] **P4** — Game combat 2D (Tuần 17–28)
- [ ] **P5** — Netcode + backend game server (Tuần 29–40)
- [ ] **P6** — Polish, marketing, phát hành (Tuần 41–48)
