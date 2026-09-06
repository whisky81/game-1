# 11 — Art & Audio cho người không biết vẽ, không biết nhạc

> Đây là rào cản tâm lý lớn nhất của lập trình viên khi làm game. Giải pháp không phải "học vẽ 6 tháng" mà là **chọn phong cách né được điểm yếu** + **mượn asset hợp pháp** + **học đúng 5 kỹ thuật**.

---

## 1. Chiến lược ba tầng

| Tầng | Khi nào | Cách làm |
|------|---------|----------|
| **1. Programmer art** | Từ đầu đến khi gameplay được xác nhận là vui | Hình chữ nhật, hình tròn, màu phẳng. `ColorRect`, `Polygon2D`. **Không xin lỗi vì điều này** |
| **2. Asset miễn phí** | Sau khi cơ chế đã ổn (P2 Tuần 7 trở đi) | Kenney, OpenGameArt, itch.io free packs. Giữ **một** phong cách nhất quán |
| **3. Tự làm có chọn lọc** | Chỉ cho những gì asset không có: nhân vật chính, boss, UI đặc thù | Áp dụng 5 kỹ thuật ở §4 |

**Sai lầm phổ biến:** trộn asset từ 5 nguồn khác nhau. Game trông rẻ tiền không phải vì art xấu mà vì **không nhất quán**. Một game toàn hình chữ nhật cùng bảng màu trông chuyên nghiệp hơn game trộn 5 phong cách.

---

## 2. Nguồn asset miễn phí (kiểm tra license kỹ)

### Đồ họa

| Nguồn | Nội dung | License | Ghi chú |
|---|---|---|---|
| **Kenney.nl** | 40.000+ asset 2D/3D/UI/audio | **CC0** (tự do tuyệt đối) | ⭐ Nguồn tốt nhất. Bắt đầu ở đây. Rất nhất quán về phong cách |
| **OpenGameArt.org** | Sprite, tileset, nhạc | Hỗn hợp: CC0/CC-BY/GPL | ⚠️ **Phải đọc license từng file** |
| **itch.io — Free Game Assets** | Rất nhiều pack chất lượng cao | Hỗn hợp | Lọc theo tag `free` + đọc điều khoản |
| **Game-icons.net** | 4000+ icon SVG | CC-BY 3.0 | Tuyệt vời cho UI, skill icon |
| **CraftPix (free section)** | Sprite 2D, có bộ fighting | Free license riêng | Đọc kỹ điều kiện |
| **Quaternius** | 3D low-poly | CC0 | Cho năm 2 |
| **Lospec Palette List** | Bảng màu pixel art dựng sẵn | Free | ⭐ Xem §4.1 |

### Âm thanh

| Nguồn | Nội dung | License |
|---|---|---|
| **jsfxr / ChipTone** (web) | Tự sinh SFX retro trong 10 giây | Bạn sở hữu output |
| **Freesound.org** | Âm thanh thật | CC0/CC-BY (lọc theo license) |
| **OpenGameArt (audio)** | Nhạc nền, SFX | Hỗn hợp |
| **Incompetech** (Kevin MacLeod) | Nhạc nền | CC-BY (phải ghi credit) |
| **BeepBox / Bosca Ceoil** (web) | Tự sáng tác chiptune | Bạn sở hữu output |
| **Kenney Audio packs** | SFX game | CC0 |

### Font

| Nguồn | Ghi chú |
|---|---|
| **Google Fonts** | OFL, dùng thoải mái |
| **DaFont** (lọc "100% free") | ⚠️ Nhiều font chỉ free cho cá nhân |
| **Font pixel: Kenney Fonts, m5x7, Press Start 2P** | CC0/OFL, hợp game retro |

### Quản lý license — bắt buộc

Tạo `CREDITS.md` **ngay từ file asset đầu tiên**, không để đến cuối:

```markdown
# Credits

## Đồ họa
- Space Shooter Redux — Kenney (kenney.nl) — CC0
- Explosion particles — tự làm

## Âm thanh
- SFX bắn/nổ — tự tạo bằng jsfxr — CC0
- Nhạc nền "Cyber Chase" — Tác giả X (link) — CC-BY 4.0

## Font
- Press Start 2P — CodeMan38 — OFL 1.1
```

> ⚠️ CC-BY **bắt buộc ghi credit**. GPL asset khiến game bạn có thể phải theo GPL. **CC0 là an toàn nhất** — ưu tiên tuyệt đối CC0 nếu có ý định thương mại.

---

## 3. Chọn phong cách "né" được điểm yếu

Xếp theo độ dễ cho lập trình viên không biết vẽ:

| Phong cách | Độ khó | Vì sao dễ | Hợp game nào |
|---|---|---|---|
| **Silhouette / bóng đen** | ⭐ | Không cần shading, không cần chi tiết mặt, không cần màu. Chỉ cần **hình dáng đúng** | Silhouette Duel (#2) — chính là Shadow Fight |
| **Hình học tối giản** (Geometry Wars, Thomas Was Alone) | ⭐ | Chỉ dùng shape + màu + glow + particle | Bullet Heaven, Rhythm Dodge |
| **Pixel art 16x16 / 32x32** | ⭐⭐ | Độ phân giải thấp giấu được kỹ năng vẽ kém. Ràng buộc = dễ hơn | Chicken Blaster, TD |
| **Flat vector** (Inkscape) | ⭐⭐ | Hình khối phẳng, dễ chỉnh, scale vô hạn | UI, nền |
| **Hand-drawn** | ⭐⭐⭐⭐ | Cần kỹ năng thật | ❌ Tránh |
| **Pixel art độ phân giải cao (64x64+)** | ⭐⭐⭐⭐ | Nhiều pixel = nhiều chỗ để sai | ❌ Tránh năm 1 |

**Kết luận cho hai game của bạn:** Chicken Blaster dùng **pixel art 32x32 từ Kenney**; Silhouette Duel dùng **silhouette tự làm** (vì đó là phần bạn *có thể* tự làm tốt).

---

## 4. Năm kỹ thuật đủ để tự làm art ở mức "chấp nhận được"

### 4.1. Dùng bảng màu có sẵn — kỹ thuật hiệu quả nhất

Đừng tự chọn màu. Lên **Lospec.com/palette-list**, chọn một bảng (ví dụ *Endesga 32*, *Pico-8*, *Sweetie 16*), và **chỉ dùng màu trong đó**. Ràng buộc này khiến art của bạn tự động hài hòa. Đây là mẹo tạo khác biệt lớn nhất với chi phí bằng 0.

### 4.2. Silhouette test
Tô đen hoàn toàn sprite. Vẫn nhận ra nó là gì? Nếu không, hình dáng sai — sửa hình dáng trước khi thêm chi tiết. Đọc-được-qua-bóng quan trọng hơn chi tiết.

### 4.3. Squash & stretch bằng code, không bằng art
Một sprite tĩnh + `Tween` scale = animation. Bạn được 80% cảm giác sống động mà không vẽ thêm frame nào:

```gdscript
func juice_hit() -> void:
	var t := create_tween()
	t.tween_property(self, "scale", Vector2(1.3, 0.7), 0.06)
	t.tween_property(self, "scale", Vector2(1.0, 1.0), 0.12) \
	 .set_trans(Tween.TRANS_ELASTIC).set_ease(Tween.EASE_OUT)
```

### 4.4. Palette swap để nhân bản nội dung
Một sprite gà → 3 loại gà bằng shader đổi màu. Tiết kiệm 2/3 công vẽ:

```glsl
shader_type canvas_item;
uniform vec4 tint : source_color = vec4(1.0);
uniform float amount : hint_range(0.0, 1.0) = 1.0;
void fragment() {
    vec4 c = texture(TEXTURE, UV);
    COLOR = vec4(mix(c.rgb, c.rgb * tint.rgb, amount), c.a);
}
```

### 4.5. Hiệu ứng che khuyết điểm
Particle, glow, screen shake, motion trail, hit flash, vignette — người chơi nhìn vào chuyển động chứ không soi từng pixel. **Một sprite tầm thường + VFX tốt > một sprite đẹp không có VFX.**

---

## 5. Audio: 90 phút để có bộ âm thanh đủ dùng

| Bước | Thời gian | Công cụ |
|---|---|---|
| 1. Sinh 10 SFX cơ bản (bắn, trúng, nổ, nhặt, nhảy, click, thắng, thua, lên cấp, lỗi) | 30' | **jsfxr** — bấm nút preset, tinh chỉnh, tải `.wav` |
| 2. Normalize + cắt im lặng đầu/cuối | 15' | Audacity (`Effect → Normalize`, `Truncate Silence`) |
| 3. Chuyển sang `.ogg` (nhẹ hơn nhiều cho web) | 5' | `for f in *.wav; do ffmpeg -i "$f" -q:a 5 "${f%.wav}.ogg"; done` |
| 4. Nhạc nền: 1 track loop 60–90 giây | 30' | **BeepBox** (dễ nhất) hoặc lấy CC0 từ OpenGameArt |
| 5. Cấu hình Audio Bus trong Godot: Master / Music / SFX, thêm Limiter vào Master | 10' | Godot Audio panel |

**Ba nguyên tắc audio quan trọng hơn chất lượng âm thanh:**
1. **Biến đổi cao độ ngẫu nhiên** cho SFX lặp lại — nếu không, tai sẽ thấy khó chịu ngay:
   ```gdscript
   player.pitch_scale = randf_range(0.92, 1.08)
   ```
2. **Giới hạn số SFX cùng lúc** (ví dụ tối đa 8 kênh) — game bắn súng không giới hạn sẽ vỡ tiếng.
3. **Âm thanh phản hồi phải tức thì** (< 30ms từ lúc bấm). Trễ audio phá hủy cảm giác điều khiển.

---

## 6. Ngân sách thời gian art/audio theo giai đoạn

| Giai đoạn | % thời gian cho art/audio | Ghi chú |
|---|---|---|
| P1 (Tuần 1–4) | **0%** | Hình chữ nhật. Tuyệt đối không |
| P2 Tuần 5–6 | 0% | Vẫn programmer art |
| P2 Tuần 7 | ~40% (một tuần duy nhất) | Kenney + jsfxr + juice |
| P2 Tuần 8–10 | ~10% | Chỉ sửa vặt |
| P3 | ~5% | Tập trung kỹ thuật |
| P4 Tuần 17–25 | ~10% | Silhouette tự làm dần |
| P4 Tuần 26 | ~70% (một tuần duy nhất) | Art & audio pass tập trung |
| P6 | ~30% | Trailer, capsule, ảnh store |

**Nguyên tắc:** gom art vào các "tuần art" tập trung thay vì rải đều. Chuyển đổi ngữ cảnh giữa code và vẽ rất tốn năng lượng.
