# Strata — Nguyên tắc thiết kế UI/UX

> Tài liệu này là **nguồn chuẩn** cho màu sắc, phông chữ, thành phần và tương tác của Strata. Khi có mâu thuẫn với mục 4 của [README](../../README.md), tài liệu này thắng và README cần được cập nhật theo.
> Trạng thái: **v0.2, đã chốt ngày 03/11/2026** (quyết định UX-Q1 đến UX-Q5 ở mục 10). Các giá trị màu đã kiểm tra tương phản WCAG.

---

## 1. Nguyên tắc UX cốt lõi

| # | Nguyên tắc | Cách thể hiện |
|---|---|---|
| U1 | **Bằng chứng nhìn thấy được** | Mọi trạng thái "đã kiểm chứng" kèm nhãn chữ và liên kết tới snapshot hoặc nguồn. |
| U2 | **Chức năng là trung tâm** | Từ item, PR hay test luôn có đường về Hồ sơ chức năng. |
| U3 | **Mặc định đơn giản** | Workflow cố định, ít cấu hình. Người mới bắt đầu dùng trong 30 phút. |
| U4 | **Đo quy trình, không đo người** | Không bảng xếp hạng cá nhân. Chỉ số hiển thị theo nhóm, chức năng hoặc hành động. |
| U5 | **Giải thích được** | Gate hiển thị từng điều kiện, giá trị đo, ngưỡng và lý do pass/fail/needs_review. |
| U6 | **Ẩn code mặc định với BA/QA** | Chỉ hiển thị metadata, tiêu đề, trạng thái và link GitHub. Không có diff. |
| U7 | **Mở đầu theo vai trò** | Sau đăng nhập, người dùng vào Trang chủ theo vai trò (P02) với "việc cần làm ngay" của vai trò đó. |
| U8 | **Một hệ ẩn dụ thống nhất** | Tầng địa chất cho **màu** (mục 2.2); đá thô → tinh thể cho **hình khối và biểu tượng dữ liệu**; đá quý chỉ là **icon ý tưởng** (mục 5.1). |

---

## 2. Màu sắc

### 2.1. Nguyên tắc phối màu

1. **Trung tính chiếm 80–90% diện tích.** Nền, card, viền và chữ là trung tính có tông ấm. Màu nhấn chỉ dùng cho hành động chính và trạng thái.
2. **Chữ thường đạt tương phản ≥ 4.5:1.** Chữ lớn (≥ 24px hoặc ≥ 18.7px đậm), icon và viền control đạt ≥ 3:1. Kiểm bằng công cụ, không đoán bằng mắt.
3. **Chữ trên nền màu chọn theo ngưỡng.** Chữ trắng khi nền đủ tối; chữ đen `#0B0B0B` khi nền sáng (ví dụ ochre).
4. **Một màu, một nghĩa.** Màu trạng thái không dùng để trang trí. Cùng một màu không được đổi nghĩa giữa các màn hình.
5. **Không dựa vào màu đơn thuần.** Trạng thái luôn có nhãn chữ hoặc icon. Không dùng cặp đỏ–xanh lục làm cách phân biệt duy nhất (khoảng 8% nam giới bị mù màu đỏ–xanh lục).
6. **Mỗi token có cặp sáng và tối.** Chế độ tối không đảo màu. Màu nhấn sáng hơn một chút, và mọi cặp chữ–nền được kiểm lại.
7. **Không thêm màu ngoài bảng token.** Nếu cần màu mới, đề xuất vào mục 2.4 trước rồi mới dùng.

### 2.2. Ý nghĩa màu theo tầng địa chất

Mỗi màu gắn với một **tầng** trong hệ thống. Nguyên tắc: tầng càng sâu càng bất biến, tầng càng nổi càng đang thay đổi.

| Tầng (màu gốc) | Ý nghĩa trong Strata | Dùng cho | Ví dụ hiển thị |
|---|---|---|---|
| **Đá gốc** (đá xám than) | Bằng chứng bất biến, không sửa được | Snapshot, event, gate_run, nhật ký | Nhãn "Bất biến" kèm icon khóa |
| **Cát** (kem ấm) | Nền tài liệu, nội dung đọc | Card mô tả, khối trích dẫn, quyết định | Nền khối "Quyết định" |
| **Đất nâu** (umber) | Công việc đang làm | Item, board, `in_progress` | Thẻ item đang làm |
| **Đất sét nung** (terracotta) | Cổng và hành động chính | Nút chính, gate, duyệt, CTA | Nút "Đề nghị hoàn thành" |
| **Đất phong hóa** (ochre) | Chờ xử lý hoặc đã cũ | `needs_review`, `stale`, chờ QA | Chip "Cần duyệt" |
| **Khoáng xanh** (mineral) | Đã kiểm chứng, đạt | `pass`, AC đã kiểm chứng | Chip "Đã kiểm chứng" |
| **Đỏ đá** | Lỗi, bị chặn | `fail`, test lỗi, vi phạm quy tắc | Chip "Không đạt" |
| **Tím đá** (plum) | Quyết định và truy vết | Bình luận Quyết định, liên kết AC–test | Dấu "Quyết định" |

### 2.3. Bảng token

Giá trị hex là giá trị đã chốt cho v0.1. Cột tỷ lệ là tương phản với nền cùng chế độ.

**Trung tính (nền, viền, chữ)**

| Token | Sáng | Tối | Dùng cho |
|---|---|---|---|
| `bg` | `#F9F9F7` | `#0B0B0B` | Nền trang |
| `surface` | `#FFFFFF` | `#1A1A19` | Card, panel |
| `surface-raised` | `#FCFCFB` | `#20201F` | Popover, drawer |
| `surface-sunken` | `#F3F3F0` | `#151515` | Vùng lồng, bảng sọc |
| `border` | `#D2D1C7` | `#383835` | Viền thường |
| `border-strong` | `#A5A49A` | `#5F5E5A` | Viền control, ô nhập |
| `text` | `#0B0B0B` (18.7:1) | `#F0EFEC` (17.1:1) | Chữ chính |
| `text-2` | `#52514E` (7.5:1) | `#A5A49A` (7.0:1 trên `surface`) | Chữ phụ |
| `text-3` | `#6F6D67` (5.2:1 trên `surface`) | `#8A8880` (4.9:1 trên `surface`) | Chữ mờ, nhãn, siêu dữ liệu |

**Tầng màu (fill và chữ)**

| Token | Fill sáng | Chữ sáng (trên `bg`) | Fill tối | Chữ tối (trên `bg`) | Ghi chú |
|---|---|---|---|---|---|
| `bedrock` | `#3E444B` | — | `#B4B3A8` | — | Chữ trắng trên fill sáng: 9.8:1 |
| `sand` | `#F5F1E8` | — | `#2C2C2A` | — | Chỉ làm nền, không làm chữ |
| `umber` | `#7A4E2D` | — | `#C08A5B` | `#C08A5B` (6.6:1) | Chữ trắng trên fill sáng: 7.1:1 |
| `terracotta` | `#A3482A` | `#A3482A` (5.7:1) | `#D97757` | `#D97757` (6.3:1) | Chữ trắng trên fill sáng: 6.0:1; chữ đen trên fill tối: 6.3:1 |
| `ochre` | `#F0AC54` | `#8F5B12` (5.4:1) | `#F0AC54` | `#F0AC54` (10.1:1) | Trên fill sáng dùng chữ `#0B0B0B` (10.1:1) |
| `mineral` | `#3F7A67` | `#3F7A67` (4.8:1) | `#8FBFAE` | `#8FBFAE` (9.6:1) | Chữ trắng trên fill sáng: 5.0:1 |
| `fail` | `#B23A2E` | `#B23A2E` (5.6:1) | `#E07A6E` | `#E07A6E` (6.7:1) | Chữ trắng trên fill sáng: 5.9:1 |
| `plum` | `#6B66A8` | — | `#A7A2D6` | `#A7A2D6` (8.2:1) | Chữ trắng trên fill sáng: 5.1:1 |

**Trọng tâm (focus ring):** sáng `#7A4E2D` (6.7:1 trên `bg`), tối `#C08A5B` (6.6:1 trên `bg`). Vòng 2px, cách control 2px.

> **Lưu ý về màu slide:** `#C6613F` (clay trong slide) không dùng làm chữ trên nền sáng vì chỉ đạt 3.8:1. Dùng `#A3482A` cho chữ và fill có chữ trắng.

### 2.4. Chip trạng thái

Mỗi trạng thái có **màu + icon + nhãn chữ**. Không bỏ bớt thành phần nào.

| Trạng thái | Tầng | Icon | Nhãn | Fill sáng / chữ | Fill tối / chữ |
|---|---|---|---|---|---|
| Đã kiểm chứng (`pass`) | mineral | ✓ | Đã kiểm chứng | `#3F7A67` / trắng | `#8FBFAE` / `#0B0B0B` |
| Cần duyệt (`needs_review`) | ochre | ◐ | Cần duyệt | `#F0AC54` / `#0B0B0B` | `#F0AC54` / `#0B0B0B` |
| Cũ, cần xác nhận (`stale`) | ochre | ⟳ | Cần xác nhận | `#F0AC54` / `#0B0B0B` | `#F0AC54` / `#0B0B0B` |
| Không đạt (`fail`) | fail | ✕ | Không đạt | `#B23A2E` / trắng | `#E07A6E` / `#0B0B0B` |
| Chưa phủ | bedrock nhạt | ○ | Chưa phủ | viền `border-strong`, chữ `text-2` | viền `border-strong`, chữ `text-2` |
| Bất biến | bedrock | khóa | Bất biến | `#3E444B` / trắng | `#B4B3A8` / `#0B0B0B` |
| Quyết định | plum | cờ ⚑ | Quyết định | `#6B66A8` / trắng | `#A7A2D6` / `#0B0B0B` |

Icon trạng thái không dùng hình đá, tinh thể hay đá quý (dành cho loại đối tượng và ý tưởng, xem mục 5.1). Không dùng emoji trong giao diện; dùng icon vector.

### 2.5. Màu trong sơ đồ (SVG)

Sơ đồ trong `assets/` (mind map, sitemap) dùng cùng bảng token. Mỗi nhóm lấy màu của tầng có nghĩa gần nhất, để màu trong sơ đồ không mâu thuẫn với màu trong giao diện.

| Nhóm trong sơ đồ | Token | Hex | Lý do |
|---|---|---|---|
| Hệ thống chung, Nền tảng, Quản trị | `text-2` (trung tính) | `#52514E` | Hạ tầng, không mang trạng thái |
| Bằng chứng | `bedrock` | `#3E444B` | Bằng chứng bất biến |
| Công việc | `umber` | `#7A4E2D` | Công việc đang làm |
| Chức năng | `terracotta` | `#A3482A` | Trung tâm sản phẩm, hành động chính |
| Chất lượng | `mineral` | `#3F7A67` | Kiểm chứng; nghĩa trùng với trạng thái "đã kiểm chứng" nên được phép |
| Tích hợp | `plum` | `#6B66A8` | Truy vết nguồn bằng chứng |

Quy tắc:

- Tiêu đề nhóm: chữ trắng trên fill đặc. Mọi fill trong bảng đạt ≥ 5:1 với chữ trắng.
- Ô con: nền là màu nhóm với độ đậm 6–12% trên `surface`, viền là màu nhóm, chữ `text` (`#0B0B0B`).
- Đường nối: `border-strong` (`#A5A49A`) cho cây; màu nhóm cho nhánh mind map.
- Tiêu đề sơ đồ dùng EB Garamond (dự phòng Noto Serif, Times New Roman); chữ còn lại IBM Plex Sans. Không dùng Georgia làm dự phòng vì phông này hiển thị sai dấu tiếng Việt.
- Không dùng `fail` hay `ochre` cho nhóm trong sơ đồ vì hai màu này chỉ mang nghĩa trạng thái.
- Nền sơ đồ là `surface` (`#FFFFFF`).

---

## 3. Phông chữ

### 3.1. Họ chữ

| Vai trò | Phông | Lý do | Trọng số |
|---|---|---|---|
| Tiêu đề | **EB Garamond** | Cảm giác học thuật, hợp với hình ảnh tầng đất và lưu trữ. Đã dùng trong slide. | 500–600 |
| Giao diện và nội dung | **IBM Plex Sans** | Hỗ trợ tiếng Việt, nhiều trọng số, dễ đọc ở cỡ nhỏ. | 400, 500, 600 |
| Mã và số liệu | **IBM Plex Mono** | Độ rộng cố định cho `KEY-12`, SHA, run id và số liệu test. | 400 |

Không thêm họ chữ thứ tư.

### 3.2. Thang cỡ chữ (desktop trước)

| Token | Phông | Cỡ / line-height | Trọng số | Dùng cho |
|---|---|---|---|---|
| `display` | EB Garamond | 32 / 1.2 | 600 | Tên trang (H1) |
| `h2` | EB Garamond | 24 / 1.25 | 600 | Khối nội dung (H2) |
| `h3` | EB Garamond | 20 / 1.3 | 500 | Card, drawer (H3) |
| `body` | IBM Plex Sans | 16 / 1.5 | 400 | Chữ thân bài, mô tả, AC |
| `ui` | IBM Plex Sans | 14 / 1.4 | 400–500 | Nhãn, bảng, menu, form |
| `caption` | IBM Plex Sans | 12 / 1.4 | 500 | Chú thích, siêu dữ liệu |
| `mono` | IBM Plex Mono | 14 / 1.4 | 400 | Mã item, SHA, số liệu test |

### 3.3. Quy tắc

- Chữ nhỏ nhất là **12px**. Nội dung chính trên mobile giữ **16px**.
- EB Garamond chỉ dùng từ **20px** trở lên. Không dùng cho nhãn, nút hay bảng.
- Số liệu trong bảng và card dùng `font-variant-numeric: tabular-nums` để các cột thẳng hàng.
- Độ dài dòng tối đa khoảng **70 ký tự** cho đoạn mô tả và tiêu chí chấp nhận.
- Tải Google Fonts đúng subset tiếng Việt và chỉ những trọng số đang dùng.

---

## 4. Bố cục, khoảng cách và hình khối

| Thuộc tính | Giá trị |
|---|---|
| Lưới cơ sở | 4px. Khoảng cách dùng bội số của 4 (4, 8, 12, 16, 24, 32, 48). |
| Bán kính | Control 6px; card 8px; chip 999px (dạng viên). |
| Đổ bóng | Tối thiểu. Card dùng viền thay cho bóng; drawer và popover có bóng nhẹ. |
| Chiều rộng | Desktop trước. Từ 360px trở lên không cuộn ngang. |
| Lề mobile | 16px hai bên. |
| Mobile | **Chỉ đọc và xem.** Xem trang chủ, hồ sơ chức năng, timeline, kết quả test và trạng thái gate. Không kéo-thả, không sửa item, không duyệt gate, không sửa luật trên mobile; các nút này ẩn kèm ghi chú "Mở trên desktop để thao tác". |
| Sidebar | Theo vai trò (xem README mục 4.2). Không hiển thị mục không có quyền. |

---

## 5. Thành phần chính

| Thành phần | Quy tắc |
|---|---|
| **Nút chính** | Fill `terracotta`, chữ trắng (sáng) hoặc `#0B0B0B` (tối). Mỗi màn hình chỉ có một nút chính. |
| **Nút phụ** | Viền `border-strong`, nền `surface`, chữ `text`. |
| **Nút nguy hiểm** | Fill `fail`, luôn có bước xác nhận. |
| **Chip trạng thái** | Theo mục 2.4. Không bao giờ chỉ có màu. |
| **Thẻ item** | Mã `KEY-n` (mono), tiêu đề, người được giao, chip PR/CI, severity nếu là bug. |
| **Hồ sơ chức năng** | Tab có deep link. Thứ tự tab cố định: Tóm tắt, AC, Công việc, PR/CI, Test, Quyết định, Timeline. |
| **Gate** | Panel mở rộng: danh sách điều kiện, giá trị đo, ngưỡng, kết quả từng điều kiện. |
| **Bằng chứng** | Mọi số liệu test hoặc AC "đã kiểm chứng" là liên kết tới snapshot. |
| **Drawer** | Chi tiết item không rời danh sách. Phím `Esc` đóng. |
| **Toast hoàn tác** | Kéo-thả và đổi trạng thái có hoàn tác trong 5 giây. |

### 5.1. Ngôn ngữ biểu tượng: đá, tinh thể, đá quý

Bên cạnh tầng địa chất (dùng cho màu), Strata dùng ba khái niệm khoáng vật cho **hình khối và biểu tượng**. Chúng kể cùng một câu chuyện với luồng dữ liệu của sản phẩm: dữ liệu thô từ GitHub/CI (**đá**) được chuẩn hóa thành bằng chứng có cấu trúc (**tinh thể**); còn **đá quý** chỉ đại diện cho ý tưởng.

```text
Đá thô (dữ liệu nguồn) ──chuẩn hóa──▶ Tinh thể (bằng chứng có cấu trúc) ──gate──▶ quyết định
Đá quý = ý tưởng (đứng riêng, không nằm trong luồng dữ liệu)
```

| Khái niệm | Hình khối | Ý nghĩa | Dùng cho | Không dùng cho |
|---|---|---|---|---|
| **Đá** (stone) | Khối đa giác không đều, góc bo nhẹ (2px), không đối xứng | Dữ liệu thô, đến từ nguồn, chưa xử lý | Icon artifact: commit, PR, CI run, file JUnit gốc; artifact "chưa liên kết"; minh họa trạng thái rỗng | Trạng thái, kết quả gate |
| **Tinh thể** (crystal) | Lục giác hoặc đa diện đối xứng, cạnh sắc, có mặt cắt | Bằng chứng đã chuẩn hóa, có cấu trúc, tái lập được | Icon snapshot, test result, ma trận AC, gate_run; minh họa khi đã có bằng chứng | Trạng thái đạt/không đạt (dùng chip mục 2.4) |
| **Đá quý** (gem) | Viên đá cắt mài (mặt bàn và các mặt vát) | **Chỉ là icon ý tưởng** | Ghi chú ý tưởng, đề xuất chức năng mới, gợi ý và mẹo hướng dẫn | Trạng thái, điểm số, phần thưởng, xếp hạng, độ ưu tiên |

Quy tắc:

1. **Biểu tượng chỉ nói loại đối tượng, không nói trạng thái.** Trạng thái luôn là chip (màu + icon trạng thái + nhãn) theo mục 2.4.
2. **Đá quý không mang giá trị.** Không dùng đá quý cho thành tích, huy hiệu hay "điểm" cá nhân (theo U4: không đo người).
3. **Một màu.** Icon dùng `currentColor` theo màu chữ xung quanh. Chỉ minh họa lớn (trạng thái rỗng, onboarding) được tô bằng token tầng địa chất; không tạo màu mới cho đá hay tinh thể.
4. **Lưới icon:** 16, 20 và 24px; nét 1.5px; đá bo góc 2px, tinh thể và đá quý góc sắc để phân biệt bằng hình chứ không bằng màu.
5. **Tối thiểu hóa.** Mỗi màn hình không quá một minh họa khoáng vật; icon trong bảng và danh sách giữ đơn giản.
6. **Truy cập.** Icon có ý nghĩa phải kèm nhãn chữ hoặc `aria-label`; icon trang trí đặt `aria-hidden="true"`.
7. **Nguồn icon:** dùng một bộ icon nét (ví dụ Lucide, có sẵn `gem` và `hexagon`) cho icon thường; vẽ riêng icon đá và tinh thể theo quy tắc 4 để cùng độ dày nét.

---

## 6. Trạng thái giao diện

| Trạng thái | Biểu hiện | Hành động |
|---|---|---|
| Đang tải | Skeleton đúng bố cục | Không cần |
| Rỗng | Giải thích ngắn và nút hành động | Ví dụ: "Kết nối GitHub" |
| Lỗi | Thông báo kèm mã lỗi, không kèm chi tiết kỹ thuật | Thử lại; liên hệ Admin nếu là quyền |
| Không có quyền | Trang 403 nêu rõ vai trò cần có | Yêu cầu quyền từ Admin |
| Tích hợp suy giảm | Banner `degraded` | Xem sức khỏe; chờ polling bù |
| Bằng chứng cũ | Chip `stale` | QA xác nhận hoặc cập nhật |

---

## 7. Khả năng truy cập

- Mọi cặp chữ–nền đạt tương phản theo mục 2.1.
- Mọi thao tác dùng được bằng bàn phím. Thứ tự Tab theo thứ tự đọc.
- Vòng focus luôn hiển thị (mục 2.3).
- Vùng bấm tối thiểu 24×24px; nên 40px cho nút chính.
- Tôn trọng `prefers-reduced-motion`: tắt chuyển động trang trí.
- Ngôn ngữ: **tiếng Anh mặc định**, có chuyển sang tiếng Việt (xem PRD mục 8). Lựa chọn ngôn ngữ lưu theo người dùng.
- Không có thông tin chỉ truyền bằng màu, âm thanh hoặc vị trí.

---

## 8. Nội dung và giọng văn

- Nhãn giao diện viết tiếng Anh trước, bản tiếng Việt dùng cùng khóa chuỗi. Nhãn ngắn, dùng động từ khi là nút (ví dụ "Create item" / "Tạo item", "Review gate" / "Duyệt gate").
- Thiết kế bố cục chừa chỗ cho bản tiếng Việt, thường dài hơn tiếng Anh khoảng 20–30%.
- Thuật ngữ thống nhất với PRD: chức năng, item, snapshot, gate, quyết định, AC.
- Không dùng từ đánh giá con người ("kém", "chậm"). Mô tả quy trình ("đang chờ duyệt").
- Mọi thông báo lỗi nêu việc cần làm tiếp theo.

---

## 9. Danh sách kiểm trước khi duyệt thiết kế

- [ ] Dùng đúng token trong mục 2.3; không có màu ngoài bảng.
- [ ] Mọi cặp chữ–nền đã kiểm tương phản.
- [ ] Mỗi trạng thái có màu, icon và nhãn chữ.
- [ ] Đá, tinh thể, đá quý dùng đúng nghĩa ở mục 5.1; đá quý chỉ dùng cho ý tưởng.
- [ ] Chỉ có một nút chính trên mỗi màn hình.
- [ ] Phông đúng mục 3; không có họ chữ thứ tư.
- [ ] Màn hình hoạt động từ 360px và chữ không bị cắt.
- [ ] Có đủ trạng thái tải, rỗng, lỗi, không có quyền.
- [ ] BA/QA không thấy diff hoặc mã nguồn.
- [ ] Không có bảng xếp hạng hay chỉ số theo cá nhân.
- [ ] Có bản sáng và bản tối.

---

## 10. Quyết định đã chốt

| ID | Câu hỏi | Quyết định (03/11/2026) | Áp dụng ở |
|---|---|---|---|
| UX-Q1 | Màn hình mở đầu mặc định | Trang chủ theo vai trò | Mục 1 (U7) |
| UX-Q2 | Ngôn ngữ mặc định | Tiếng Anh, có chuyển sang tiếng Việt | Mục 7, 8 |
| UX-Q3 | Giữ hay làm lại thương hiệu thị giác | Giữ bảng màu đất và phông trong tài liệu này | Mục 2, 3 |
| UX-Q4 | Mức hỗ trợ mobile | Chỉ đọc và xem; thao tác trên desktop | Mục 4 |
| UX-Q5 | Chuyển các SVG trong `assets/` sang token mới | Có, sau khi chốt bảng token | Mục 2.5; đã áp dụng cho 3 SVG trong `assets/` |

---

## Tài liệu liên quan

- [README](../../README.md) mục 4 (nguyên tắc UI/UX ban đầu, sitemap, wireframe)
- [PRD](../product/PRD_Strata_MVP.md) mục 4 (persona), mục 6 (yêu cầu tính năng)
- [CLAUDE.md](../../CLAUDE.md) (hướng dẫn cho trợ lý AI trong repo)
- Skill: `.claude/skills/strata-ui-design/SKILL.md`
