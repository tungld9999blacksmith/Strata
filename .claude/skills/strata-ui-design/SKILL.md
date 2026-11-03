---
name: strata-ui-design
description: Thiết kế hoặc đánh giá UI/UX cho Strata (màu, phông, bố cục, thành phần, trạng thái, sơ đồ SVG) theo bộ nguyên tắc đất và trung tính đã chốt. Dùng khi người dùng yêu cầu thiết kế màn hình, chọn màu, phông, component, wireframe, sitemap, mind map hoặc kiểm tra tương phản cho Strata.
---

# Strata UI/UX design

Dùng skill này để mọi đề xuất giao diện của Strata nhất quán với tài liệu chuẩn.

## Nguồn chuẩn

Đọc trước khi trả lời:

1. [docs/design/Strata_UI_UX_Guidelines.md](../../../docs/design/Strata_UI_UX_Guidelines.md): màu, phông, thành phần, trạng thái, khả năng truy cập.
2. [docs/product/PRD_Strata_MVP.md](../../../docs/product/PRD_Strata_MVP.md): yêu cầu chức năng và quyền hiển thị theo vai trò.
3. [README.md](../../../README.md) mục 4: sitemap, app shell, wireframe.

## Quy trình

1. **Xác định màn hình và vai trò.** Đối chiếu với mục 4.2 của README (sidebar theo vai trò) và mục 4 của PRD (persona). Ghi rõ vai trò nào được thấy gì.
2. **Chọn màu từ token.** Chỉ dùng các token trong mục 2.3 của tài liệu UI/UX. Với mỗi trạng thái, lấy màu theo tầng địa chất (mục 2.2) và chip trạng thái (mục 2.4).
3. **Kiểm tương phản.** Mọi cặp chữ–nền phải đạt 4.5:1 (chữ thường) hoặc 3:1 (chữ lớn, icon, viền control). Nếu không đạt, đổi sang biến thể trong bảng, không tự tạo màu mới.
4. **Kiểm trạng thái.** Mỗi trạng thái có màu, icon và nhãn chữ. Đủ trạng thái tải, rỗng, lỗi và không có quyền (mục 6).
5. **Kiểm phông.** Tiêu đề EB Garamond từ 20px trở lên; nội dung IBM Plex Sans; mã và số liệu IBM Plex Mono.
6. **Kiểm bố cục.** Lưới 4px, không cuộn ngang từ 360px, một nút chính mỗi màn hình. Trên mobile chỉ có chức năng đọc và xem; ẩn các thao tác sửa.
7. **Kiểm ngôn ngữ.** Nhãn giao diện viết tiếng Anh (mặc định); chừa chỗ cho bản tiếng Việt dài hơn khoảng 20–30%.
8. **Kiểm quyền riêng tư.** Không xếp hạng cá nhân; BA/QA không thấy diff hay mã nguồn.
9. **Kiểm biểu tượng.** Theo mục 5.1: đá cho dữ liệu thô (commit, PR, CI run), tinh thể cho bằng chứng đã chuẩn hóa (snapshot, test result, gate_run), đá quý chỉ cho ý tưởng. Không dùng ba hình này làm icon trạng thái, và không dùng đá quý cho điểm số hay thành tích.

## Khi tạo sơ đồ SVG

- Lưu trong `assets/`, nhúng bằng `![...](./assets/...svg)`.
- Dùng màu theo mục 2.5 của tài liệu chuẩn (màu trong sơ đồ), không dùng màu tùy ý.
- Giữ chữ đủ tương phản trên nền sáng và nền tối khi cần.
- Đặt tên file theo nội dung, không dùng khoảng trắng.

## Cách trình bày kết quả

- Nêu rõ màn hình, vai trò và trạng thái đang bàn.
- Liệt kê token đã dùng (tên và mã hex) và tỷ lệ tương phản cho các cặp chữ–nền.
- Nếu có điểm chưa chắc chắn hoặc cần người duyệt, ghi vào mục 10 (việc cần chốt) thay vì tự quyết.

## Không làm

- Không thêm màu, phông hoặc họ chữ ngoài tài liệu chuẩn khi chưa được duyệt.
- Không dùng màu làm cách duy nhất để truyền thông tin.
- Không đề xuất bảng xếp hạng hay giám sát theo cá nhân.
- Không commit hoặc push nếu người dùng không yêu cầu.
