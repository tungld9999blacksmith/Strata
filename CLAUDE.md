# CLAUDE.md — Strata

Hướng dẫn cho trợ lý AI khi làm việc trong repo này.

## Dự án

**Strata** là workspace nối công việc với bằng chứng cho dự án phần mềm: mỗi chức năng có một hồ sơ gom yêu cầu, công việc, commit/PR, kết quả test và quyết định, cập nhật từ GitHub và CI. MVP chỉ phục vụ dự án phần mềm.

Repo hiện chứa tài liệu (chưa có mã ứng dụng).

## Tài liệu chuẩn

| Nội dung | File |
|---|---|
| Tổng quan và sơ đồ | [README.md](README.md) |
| Yêu cầu sản phẩm (PRD) | [docs/product/PRD_Strata_MVP.md](docs/product/PRD_Strata_MVP.md) |
| Kế hoạch và các phase | [docs/product/PLAN_Strata.md](docs/product/PLAN_Strata.md) |
| Nguyên tắc UI/UX, màu, phông | [docs/design/Strata_UI_UX_Guidelines.md](docs/design/Strata_UI_UX_Guidelines.md) |
| Kiến trúc hệ thống | [docs/background/system-architecture.md](docs/background/system-architecture.md) |

Khi có mâu thuẫn, ưu tiên theo thứ tự: PRD, sau đó tài liệu UI/UX, rồi README.

## Quy ước làm việc

- **Ngôn ngữ tài liệu:** tiếng Việt. Thuật ngữ kỹ thuật và mã (`KEY-n`, `snapshot`, `gate_run`) giữ nguyên tiếng Anh.
- **Ngôn ngữ giao diện:** tiếng Anh mặc định, có chuyển sang tiếng Việt. Nhãn trong wireframe và mockup viết tiếng Anh trước.
- **Quyết định UI/UX đã chốt** (03/11/2026): mở đầu bằng Trang chủ theo vai trò; mobile chỉ đọc và xem; giữ bảng màu đất và phông trong tài liệu UI/UX. Xem mục 10 của tài liệu đó.
- **Sơ đồ:** ưu tiên SVG đặt trong `assets/` và nhúng bằng `![...](./assets/...)`. Không thêm sơ đồ Mermaid mới cho mind map hoặc sitemap.
- **Thiết kế giao diện:** trước khi đề xuất màu, phông, bố cục hay thành phần, đọc [Strata_UI_UX_Guidelines.md](docs/design/Strata_UI_UX_Guidelines.md) và dùng skill `strata-ui-design`.
- **Màu:** chỉ dùng token trong bảng 2.3 của tài liệu UI/UX. Không thêm màu mới khi chưa được duyệt.
- **Hệ ẩn dụ:** tầng địa chất cho màu; đá (dữ liệu thô) và tinh thể (bằng chứng đã chuẩn hóa) cho biểu tượng; đá quý chỉ là icon ý tưởng (mục 5.1 của tài liệu UI/UX).
- **Tương phản:** mọi cặp chữ–nền phải đạt WCAG AA trước khi chốt.
- **Quyền riêng tư:** không đề xuất giao diện xếp hạng hoặc giám sát cá nhân (README mục 2.3.4, BR11; PRD mục 7).
- **Không lưu mã nguồn** trong Strata; BA/QA không thấy diff (README mục 2.3.4, BR10; PRD mục 7).

## Lưu ý khi sửa file

- README.md dùng kiểu xuống dòng CRLF. Khi sửa bằng script, giữ nguyên CRLF.
- Không commit hoặc push khi chưa được yêu cầu.
- Không xóa hoặc đổi tên các mã PRD (F1–F10, AC-Fx-xx, US, BR) vì tài liệu khác tham chiếu tới chúng.
