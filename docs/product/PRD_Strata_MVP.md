# PRD — Strata MVP (Workspace cho dự án phát triển phần mềm)

> Strata là workspace nối công việc với bằng chứng: mỗi chức năng của một dự án phần mềm có một hồ sơ gom yêu cầu, công việc, commit/PR, kết quả test và quyết định, cập nhật tự động từ GitHub và CI.
> **Phạm vi MVP:** chỉ dự án phần mềm. Dự án nghiên cứu, viết báo khoa học, dự án AI/ML và AI agent được giữ trong kế hoạch dài hạn ([PLAN_Strata.md](PLAN_Strata.md)) và Phụ lục B.
> **Nguồn chuẩn:** các quyết định kiến trúc nằm ở [system-architecture.md](../background/system-architecture.md). Các pain point chưa kiểm chứng được gắn nhãn "Giả định" và cần phỏng vấn thực địa (mục 14).

---

## 0. Thông tin tài liệu

| Field | Value |
|---|---|
| Product | Strata — workspace hợp nhất cho dự án phần mềm, nghiên cứu và AI (MVP: phần mềm) |
| Version | v0.1 |
| Status | Draft — chờ chốt nhóm người dùng đầu tiên và quy mô đội build |
| Product Owner | TBD |
| Project Manager | TBD |
| Tech Lead | TBD |
| Ngày cập nhật | 03/11/2026 |
| Khung thời gian | T0 → T0 + 10 tuần (T0 = ngày đủ nhân sự bắt đầu build, chưa chốt) |

### Change log

| Version | Ngày | Thay đổi |
|---|---|---|
| v0.1 | 03/11/2026 | Bản đầu. Thu hẹp phạm vi từ "phần mềm · nghiên cứu · AI" xuống phần mềm. Giữ nghiên cứu/AI làm phase sau (Phụ lục B). |

---

## 1. Tóm tắt

**Vấn đề:** thông tin về một chức năng phần mềm nằm rải rác ở công cụ quản lý công việc, Git, CI và tài liệu. BA, QA, PO phải hỏi dev hoặc tổng hợp tay để biết chức năng đang ở đâu, đã được kiểm chứng chưa, vì sao yêu cầu đổi.

**Giải pháp MVP:** Strata gom mọi thứ quanh **chức năng (feature)**. Commit, PR và kết quả test (JUnit) tự đến từ GitHub/CI, được lưu thành **snapshot bất biến**. Mỗi chức năng có hồ sơ và timeline. Một **gate chất lượng** đơn giản đánh giá snapshot để quyết định "đủ điều kiện hoàn thành".

**Thành công của MVP:** một nhóm phần mềm 3–10 người dùng Strata thật trong ít nhất 4 tuần, hầu hết liên kết commit/PR–item không cần nhập tay, và BA/QA trả lời được "chức năng X đang ở đâu" mà không phải hỏi dev.

```
GitHub/CI → webhook/upload → snapshot bất biến → liên kết với item → hồ sơ chức năng + timeline → gate → quyết định hoàn thành
```

**Thuật ngữ:** *chức năng (feature)* là đơn vị công việc của người dùng, có hồ sơ riêng. *F-n* (F1…F10) là tính năng của chính Strata trong tài liệu này.

---

## 2. Vấn đề (Pain points)

Nhãn bằng chứng: **Có nguồn** = có khảo sát/bài viết nhưng phần lớn từ nhà cung cấp công cụ nên chỉ có giá trị định hướng; **Giả định** = suy luận từ brainstorm, chưa phỏng vấn.

### 2.1 Phía người làm (Dev, QA)

| ID | Pain point | Bằng chứng | Tính năng |
|---|---|---|---|
| PP-01 | Thông tin một chức năng rải rác ở nhiều công cụ, phải tổng hợp thủ công | Có nguồn (khảo sát Atlassian 2025, định hướng) | F5, F6, F7 |
| PP-02 | Quy trình nặng nề, nhiều thủ tục không tạo giá trị; lãnh đạo không hiểu khó khăn của developer | Có nguồn (Atlassian: 63% developer; InfoWorld) | F3, F10 |
| PP-03 | Nhập liệu tay để "cập nhật trạng thái" mà dữ liệu đã có sẵn ở Git/CI | Giả định | F5, F6 |
| PP-04 | Kiểm tra "đủ chất lượng để phát hành" dựa trên cảm tính, kiểm tay, khó tái lập | Giả định | F6, F8 |

### 2.2 Phía điều phối (PO, PM, BA)

| ID | Pain point | Bằng chứng | Tính năng |
|---|---|---|---|
| PP-05 | Không thấy tình trạng thật của yêu cầu so với code/test nếu không hỏi dev; BA/QA không cần xem code nhưng cần biết "đã kiểm chứng chưa" | Giả định | F7, F9, F10 |
| PP-06 | Mất lịch sử vì sao yêu cầu đổi, quyết định nào đã chốt | Giả định | F4, F7 |
| PP-07 | Công cụ cấu hình phức tạp, cần người chuyên trách ("Jira thứ hai") | Có nguồn (bài viết quan điểm, định hướng) | F2, F3 |

---

## 3. Mục tiêu & phi mục tiêu

### 3.1 Mục tiêu

| # | Mục tiêu | Pain point |
|---|---|---|
| G1 | Một chức năng có một nơi duy nhất cho yêu cầu, công việc, commit/PR, kết quả test và quyết định | PP-01, PP-06 |
| G2 | Bằng chứng đến tự động từ GitHub và CI; nhập tay chỉ là ngoại lệ | PP-03 |
| G3 | BA/QA/PO biết trạng thái kiểm chứng của chức năng mà không cần xem code | PP-05 |
| G4 | Quyết định "đủ điều kiện hoàn thành" dựa trên snapshot, tái lập được | PP-04 |
| G5 | Mặc định đơn giản: nhóm bắt đầu dùng trong dưới 30 phút, không cần cấu hình workflow | PP-02, PP-07 |

### 3.2 Phi mục tiêu

Không thay thế Jira/Linear cho mọi trường hợp (không có cấu hình trường/workflow tự do); không lưu hay hiển thị mã nguồn; không đo hay xếp hạng cá nhân; không có AI agent, nghiên cứu, AI/ML, ước lượng, phân tích nghẽn trong MVP.

---

## 4. Người dùng

| Persona | Mô tả | Kênh | Spec |
|---|---|---|---|
| Workspace Admin | Tạo workspace, mời thành viên, kết nối GitHub | Web app | F1, F2, F5 |
| PO | Quyết định giá trị, ưu tiên, chấp nhận chức năng | Web app | F3, F7, F8, F10 |
| PM | Tiến độ sprint, rủi ro, cấu hình gate | Web app | F3, F8, F10 |
| BA | Viết yêu cầu và tiêu chí chấp nhận (AC), truy vết | Web app | F3, F7, F9 |
| QA | Test, bug, duyệt gate | Web app | F6, F8, F9 |
| Dev | Làm việc, liên kết commit/PR, xem kết quả build/test | Web app, GitHub | F3, F5, F6 |

Một người có thể giữ nhiều vai trò (ví dụ vừa PO vừa PM).

### Use case tiêu biểu

- **UC-A — "Chức năng KEY-5 đang ở đâu?"** BA mở hồ sơ KEY-5 → thấy 3 AC, 2 AC đã có test pass ở snapshot mới nhất, 1 AC chưa phủ, 1 PR đang review.
- **UC-B — "Đủ điều kiện hoàn thành chưa?"** Dev chuyển KEY-5 sang `done` → gate đánh giá snapshot → `needs_review` → QA duyệt → `done`, ghi người duyệt và snapshot.
- **UC-C — "Vì sao yêu cầu đổi?"** PO mở timeline KEY-5 → thấy comment đánh dấu *Quyết định* và lịch sử thay đổi AC.

---

## 5. Phạm vi

| Ưu tiên | ID | Tính năng | Mốc |
|---|---|---|---|
| Must | F1 | Workspace, tenancy và đăng nhập | M1 |
| Must | F2 | Vai trò và phân quyền | M1 |
| Must | F3 | Dự án, item, link, sprint, bug | M2 |
| Must | F4 | Bình luận, mention, thông báo | M2 |
| Must | F5 | Kết nối GitHub | M3 |
| Must | F6 | Kết quả test JUnit và snapshot | M4 |
| Must | F7 | Hồ sơ chức năng và timeline | M4 |
| Should | F8 | Quality gate cơ bản | M5 |
| Should | F9 | Truy vết AC ↔ test ↔ kết quả | M5 |
| Could | F10 | Góc nhìn theo vai trò | M5 |

**Out of scope (MVP):** workflow designer đầy đủ; AI agent; space private/public và publish; dự án nghiên cứu, vòng đời bài báo, giả thuyết/thí nghiệm; dự án AI/ML và experiment tracking; ước lượng dạng khoảng, sổ rủi ro; phân tích nghẽn và coaching; version tree, diff lịch sử yêu cầu; ABAC, phân quyền cấp artifact; DB riêng theo tenant (silo); GitLab, CI/SonarQube/Figma/Confluence connector; mobile app. Chi tiết ở Phụ lục B.

---

## 6. Yêu cầu tính năng

### F1 — Workspace, tenancy và đăng nhập

- Đăng nhập OIDC (GitHub, Google). Không lưu mật khẩu trong MVP.
- Workspace là đơn vị tenant. Một người thuộc nhiều workspace; mời bằng email hoặc link có hạn 7 ngày.
- Mọi bảng có `tenant_id` và Row-Level Security (RLS).

**Acceptance criteria**
- AC-F1-01: _Given_ người dùng chưa có tài khoản, _when_ đăng nhập GitHub lần đầu, _then_ tạo `app_user` và yêu cầu tạo hoặc tham gia workspace.
- AC-F1-02: _Given_ thành viên workspace A, _when_ gọi API với id item của workspace B, _then_ trả 404 và ghi log truy cập bị từ chối.
- AC-F1-03: _Given_ truy vấn thiếu điều kiện tenant (trong bộ test), _when_ chạy bằng role của ứng dụng, _then_ RLS trả 0 dòng.
- AC-F1-04: _Given_ lời mời quá 7 ngày, _when_ mở link, _then_ báo hết hạn và không tạo membership.

### F2 — Vai trò và phân quyền

- Vai trò: Workspace Admin, PO, PM, BA, QA, Dev, Viewer. Gán nhiều vai trò cho một người, theo từng dự án.
- Quyền = hành động × loại đối tượng, lưu dạng dữ liệu (`role`, `permission`, `role_permission`). MVP chỉ dùng RBAC.
- Mọi thay đổi vai trò ghi event.

**Acceptance criteria**
- AC-F2-01: _Given_ người có hai vai trò PO và PM, _when_ kiểm tra quyền, _then_ quyền hiệu lực là hợp của hai vai trò.
- AC-F2-02: _Given_ Viewer, _when_ gọi API tạo item, _then_ trả 403 và UI không hiện nút tạo.
- AC-F2-03: _Given_ Admin đổi vai trò của thành viên, _when_ lưu, _then_ ghi event `role_assignment.changed` kèm actor, trước, sau.
- AC-F2-04: _Given_ chỉ còn một Admin, _when_ Admin đó gỡ vai trò của chính mình, _then_ bị từ chối.

### F3 — Dự án, item, link, sprint, bug

- Project có `key` 2–6 ký tự in hoa và cột `kind` (MVP cố định `software`).
- Item type: `epic`, `story`, `task`, `bug`, `feature` (chức năng, neo của hồ sơ F7). Vòng đời mặc định: `backlog → in_progress → review → done`, có `cancelled`.
- Link: `parent_of`, `blocks`, `relates_to`, `implements`, `tests`, `duplicates`.
- Backlog xếp hạng kéo-thả, board Kanban, sprint (ngày bắt đầu/kết thúc, gán item). Bug có severity S1–S4.
- Mã item `KEY-n`, tăng nguyên tử. Xóa mềm.

**Acceptance criteria**
- AC-F3-01: _Given_ hai người cùng tạo item, _when_ lưu đồng thời, _then_ hai mã khác nhau, không trùng, không bỏ số hỏng.
- AC-F3-02: _Given_ item ở `backlog`, _when_ chuyển thẳng sang `done`, _then_ bị từ chối theo vòng đời mặc định.
- AC-F3-03: _Given_ tạo bug, _when_ thiếu severity, _then_ bị từ chối với thông báo trường bắt buộc.
- AC-F3-04: _Given_ xóa item, _when_ xác nhận, _then_ xóa mềm, event ghi lại, PM khôi phục được trong 30 ngày.
- AC-F3-05: _Given_ kéo thả đổi thứ tự backlog, _when_ thả, _then_ thứ tự lưu và người khác thấy trong ≤ 2 giây.

### F4 — Bình luận, mention, thông báo

- Bình luận Markdown, `@mention`, đánh dấu một bình luận là **Quyết định**.
- Thông báo trong app và email tóm tắt; mỗi người cấu hình riêng.

**Acceptance criteria**
- AC-F4-01: _Given_ bình luận có `@An`, _when_ gửi, _then_ An nhận thông báo ≤ 10 giây; người không có quyền xem item không nhận nội dung.
- AC-F4-02: _Given_ sửa hoặc xóa bình luận, _when_ lưu, _then_ giữ lịch sử thay đổi trong event.
- AC-F4-03: _Given_ tắt email, _when_ có mention, _then_ vẫn thấy trong app, không gửi email.
- AC-F4-04: _Given_ đánh dấu Quyết định, _when_ lưu, _then_ bình luận hiện ở mục Quyết định của hồ sơ và trên timeline.

### F5 — Kết nối GitHub

- Cài **GitHub App** với quyền tối thiểu: đọc metadata, PR, checks, workflow runs. Strata không đọc nội dung file mã nguồn.
- Nhận webhook `push`, `pull_request`, `check_run`, `workflow_run`. Xác thực chữ ký HMAC. Xử lý idempotent theo delivery id và `external_ref`.
- Polling đối soát mỗi 15 phút để bù webhook thiếu.
- Tự liên kết theo mã item (`KEY-12`) trong tên nhánh, commit message, tiêu đề PR. Cho phép liên kết/gỡ liên kết thủ công; lưu nguồn gốc liên kết (`auto` / `manual`).
- Artifact: `commit`, `pull_request`, `ci_run`.

**Acceptance criteria**
- AC-F5-01: _Given_ push commit "KEY-12 sửa đăng nhập", _when_ webhook đến, _then_ tạo artifact `commit` liên kết KEY-12 và hiện trên timeline.
- AC-F5-02: _Given_ webhook cùng delivery id gửi 3 lần, _when_ xử lý, _then_ chỉ một artifact và một event.
- AC-F5-03: _Given_ webhook chữ ký sai, _when_ nhận, _then_ trả 401 và không xử lý.
- AC-F5-04: _Given_ webhook bị mất, _when_ polling chạy, _then_ tạo artifact còn thiếu trong ≤ 15 phút với nguồn `poll`.
- AC-F5-05: _Given_ liên kết tự động sai, _when_ người có quyền gỡ, _then_ liên kết bị gỡ, event ghi lại, liên kết không tự tạo lại cho cùng cặp.

### F6 — Kết quả test JUnit và snapshot

- Upload JUnit XML qua API (token CI theo dự án) hoặc GitHub Action mẫu. Parse `testsuite`/`testcase`: pass, fail, skip, thời gian, thông báo lỗi.
- Mỗi upload tạo `source_snapshot` bất biến: commit SHA, run id, hash file. File gốc lưu object storage; DB lưu tóm tắt và tham chiếu.
- Liên kết với item qua commit SHA, PR, nhánh.

**Acceptance criteria**
- AC-F6-01: _Given_ JUnit hợp lệ, _when_ upload, _then_ tạo snapshot có tổng pass/fail/skip khớp file và gắn commit SHA.
- AC-F6-02: _Given_ upload lại cùng (SHA, run id, hash), _when_ xử lý, _then_ không tạo snapshot thứ hai.
- AC-F6-03: _Given_ XML hỏng, _when_ upload, _then_ trả 422 kèm lý do, không tạo snapshot.
- AC-F6-04: _Given_ file lớn hơn 20 MB, _when_ upload, _then_ trả 413.
- AC-F6-05: _Given_ snapshot đã tạo, _when_ gọi API sửa hoặc xóa, _then_ không có endpoint nào cho phép.

### F7 — Hồ sơ chức năng và timeline

- Hồ sơ gom: mô tả/yêu cầu, AC, item con, commit/PR (chỉ metadata và liên kết, không diff), kết quả test mới nhất, quyết định, timeline.
- Timeline là event lọc theo chức năng và con cháu, sắp theo thời gian, lọc theo loại (trạng thái, code, test, bình luận, quyết định).
- Hiển thị theo hành động và vai trò, không theo xếp hạng cá nhân.

**Acceptance criteria**
- AC-F7-01: _Given_ chức năng có 500 event, _when_ mở hồ sơ, _then_ trang tải ≤ 2 giây (p95) và hiện item con, PR, kết quả test mới nhất.
- AC-F7-02: _Given_ commit liên kết mới, _when_ webhook xử lý xong, _then_ xuất hiện trên timeline ≤ 30 giây.
- AC-F7-03: _Given_ BA mở hồ sơ, _when_ xem commit/PR, _then_ chỉ thấy tiêu đề, trạng thái, tác giả, link GitHub; không có diff hay mã nguồn.
- AC-F7-04: _Given_ lọc timeline theo "test", _when_ áp dụng, _then_ chỉ hiện sự kiện test của chức năng.

### F8 — Quality gate cơ bản

- Gate gắn với chuyển trạng thái `review → done` của một chức năng, hoặc với một release. Không có workflow designer.
- Quy tắc là **dữ liệu có phiên bản**, ví dụ `tests.failed == 0`, `tests.passed_ratio >= 0.95`, `bugs.open(severity<=S2) == 0`, `approved_by has role QA`.
- Kết quả: `pass`, `fail`, `needs_review`. Gate đánh giá snapshot mới nhất. Mỗi lần chạy lưu `gate_run` bất biến kèm snapshot và phiên bản luật.
- Người đề nghị không được tự duyệt.

**Acceptance criteria**
- AC-F8-01: _Given_ snapshot có 2 test fail và luật `failed == 0`, _when_ gate chạy, _then_ `fail`, liệt kê test fail, chức năng không sang `done`.
- AC-F8-02: _Given_ luật cần QA duyệt và test pass, _when_ gate chạy, _then_ `needs_review`; khi QA duyệt thì `pass` và ghi người duyệt.
- AC-F8-03: _Given_ cùng snapshot và cùng phiên bản luật, _when_ chạy lại, _then_ cùng kết quả.
- AC-F8-04: _Given_ sửa luật, _when_ lưu, _then_ tạo phiên bản mới; `gate_run` cũ giữ phiên bản luật cũ.
- AC-F8-05: _Given_ người đề nghị chuyển `done`, _when_ tự duyệt, _then_ bị từ chối.

### F9 — Truy vết AC ↔ test ↔ kết quả

- AC có ID ổn định (`KEY-5/AC-3`). Test ánh xạ AC bằng tag trong tên test hoặc classname (ví dụ `AC:KEY-5/AC-3`), hoặc liên kết thủ công.
- Ma trận AC × test × kết quả snapshot mới nhất; độ phủ AC.
- Đổi nội dung AC đánh dấu liên kết test là `stale` cho đến khi QA xác nhận.

**Acceptance criteria**
- AC-F9-01: _Given_ test có tag `AC:KEY-5/AC-3` pass ở snapshot mới nhất, _when_ mở ma trận, _then_ AC-3 "đã kiểm chứng" kèm tham chiếu snapshot.
- AC-F9-02: _Given_ AC không có test, _when_ mở ma trận, _then_ "chưa phủ"; độ phủ = AC có test / tổng AC.
- AC-F9-03: _Given_ sửa nội dung AC-3, _when_ lưu, _then_ liên kết test của AC-3 là `stale` cho đến khi QA xác nhận.

### F10 — Góc nhìn theo vai trò

- Trang chủ theo vai trò: PO (backlog ưu tiên, chức năng chờ chấp nhận), PM (sprint, item bị chặn), BA (yêu cầu thiếu AC), QA (gate chờ duyệt, bug mở), Dev (việc của tôi, trạng thái PR/CI).
- BA/QA ẩn thông tin code mặc định.

**Acceptance criteria**
- AC-F10-01: _Given_ QA mở trang chủ, _when_ có gate `needs_review` chờ QA, _then_ danh sách hiện đầu trang.
- AC-F10-02: _Given_ Dev mở trang chủ, _when_ có item được giao, _then_ hiện kèm trạng thái PR và CI mới nhất.

---

## 7. Nguyên tắc dữ liệu và bằng chứng

| Nguyên tắc | Quy tắc |
|---|---|
| Bằng chứng bất biến | Snapshot gắn commit SHA và run id; không sửa, không xóa. Gate chỉ đánh giá snapshot, không đánh giá "nhánh hiện tại". |
| Event chỉ thêm | Mọi thay đổi (trạng thái, quyền, luật gate) ghi event; không có API sửa hoặc xóa event. |
| Dữ liệu tự đến trước | Ưu tiên nguồn tự động (Git, CI). Nhập tay là ngoại lệ và ghi nguồn gốc. |
| Đo quy trình, không đo người | Không bảng xếp hạng cá nhân, không dùng timeline để chấm điểm. |
| Gate là đề xuất có người duyệt | Gate đạt không đồng nghĩa chất lượng đúng; luật cần duyệt người cho thay đổi quan trọng. |
| Tối thiểu quyền | Token GitHub chỉ đọc; BA/QA không cần thấy code; Strata không lưu mã nguồn. |
| AI | MVP không có AI agent. Khi thêm (Phụ lục B), agent đề xuất và gate quyết định; xem [PLAN_Strata.md](PLAN_Strata.md). |

---

## 8. Yêu cầu phi chức năng

Các con số là **đề xuất**, cần xác nhận khi có số liệu tải thật.

| Nhóm | Yêu cầu |
|---|---|
| Hiệu năng | API đọc p95 ≤ 300 ms; board 200 item tải ≤ 1,5 s; hồ sơ chức năng 500 event ≤ 2 s (p95); webhook → timeline ≤ 30 s. |
| Cô lập dữ liệu | `tenant_id` mọi bảng + RLS; bộ test tự động chứng minh truy vấn thiếu tenant trả 0 dòng, kể cả qua tìm kiếm. |
| Bảo mật | OIDC; kiểm quyền phía server cho mọi API; token tích hợp mã hóa khi lưu; chữ ký HMAC webhook; tuân thủ OWASP Top 10; audit trail thay đổi quyền. |
| Độ tin cậy | Webhook và upload idempotent; polling đối soát; sao lưu hằng ngày; RPO ≤ 24 giờ, RTO ≤ 4 giờ. |
| Quan sát | Log có cấu trúc, trace id xuyên webhook → worker → timeline; metric độ trễ ingest. |
| Mở rộng | Đăng ký `kind` và item type bằng dữ liệu; `attrs` JSONB có schema đăng ký; kiến trúc modular monolith với ranh giới module rõ. |
| Quyền riêng tư | Không thu thập số liệu theo cá nhân để xếp hạng; xuất và xóa dữ liệu workspace theo yêu cầu. |
| Khả dụng | Web, desktop-first, dùng được trên màn hình ≥ 360 px; tiếng Việt và tiếng Anh. |

---

## 9. Kiến trúc và công nghệ

| Thành phần | Lựa chọn | Vai trò |
|---|---|---|
| Web app | SPA TypeScript | Board, hồ sơ chức năng, timeline |
| API | Modular monolith TypeScript | Logic nghiệp vụ, kiểm quyền |
| CSDL | PostgreSQL (RLS, JSONB, full-text) | Dữ liệu chính, event |
| Object storage | S3-compatible | File JUnit gốc, log |
| Hàng đợi | Bảng job trong PostgreSQL (ban đầu) | Ingest webhook, polling, gate |
| Worker | Tiến trình riêng cùng codebase | Xử lý webhook, parse JUnit, đánh giá gate |
| Xác thực | OIDC (GitHub, Google) | Đăng nhập |
| Tích hợp | GitHub App | Webhook + polling đối soát |

Ngăn xếp là đề xuất mặc định, chốt theo kỹ năng nhóm (PQ-05). Sơ đồ C4 chi tiết ở [README.md](../../README.md#5-kiến-trúc-c4).

### 9.1 Quyết định: mô hình dữ liệu item (ADR-01)

**Trạng thái:** Đề xuất. **Bối cảnh:** item có cột chung và trường riêng theo kind/type; MVP chỉ cần `software` nhưng phase sau thêm research và AI.

| Tiêu chí | A. Cột chung + `attrs` JSONB có schema đăng ký | B. Bảng riêng theo kind |
|---|---|---|
| Thêm kind mới | Đăng ký schema, không migration | Migration và code mới |
| Truy vấn xuyên kind | Một bảng | Nhiều bảng, union |
| Kiểm tra dữ liệu | Cần kiểm schema ở tầng ứng dụng | Ràng buộc DB mạnh |
| Hiệu năng lọc | Index biểu thức khi cần | Tốt hơn trên cột riêng |

**Quyết định đề xuất:** A. Cột chung (`id`, `tenant_id`, `project_id`, `type`, `state`, `title`, `seq`…) và `attrs` JSONB; trường hay lọc được đưa ra cột khi đo thấy cần.

### 9.2 Quyết định: cô lập tenant (ADR-02)

**Trạng thái:** Đề xuất. Pooled với `tenant_id` + RLS cho MVP; bảng `tenant` làm tầng điều phối (tenant → nơi chứa dữ liệu) để sau này chuyển khách lớn sang DB riêng không đổi code ứng dụng. Tên miền chỉ định tuyến, không phải cơ chế bảo mật.

---

## 10. Chỉ số thành công

Mục tiêu là **đề xuất**, chưa có baseline.

| Nhóm | Chỉ số | Mục tiêu | Cách đo |
|---|---|---|---|
| Giá trị | Tỷ lệ commit/PR liên kết tự động với item | ≥ 80% | Đếm liên kết `auto` / tổng commit trong nhóm dùng thử |
| Giá trị | Thời gian trả lời "chức năng X đang ở đâu" | ≤ 1 phút | Quan sát có hướng dẫn với BA/QA, so với baseline phỏng vấn |
| Áp dụng | Số nhóm dùng thật | ≥ 3 nhóm | Hoạt động hằng tuần trong 4 tuần |
| Áp dụng | Thành viên hoạt động hằng tuần | ≥ 60% | Phiên đăng nhập có thao tác |
| An toàn | Rò rỉ chéo tenant | 0 | Bộ test RLS trong CI |
| Độ tin cậy | Webhook trùng/lệch thứ tự tạo bản sao | 0 | Test tự động + giám sát |
| Gate | Số nhóm dùng gate cho phát hành thật | ≥ 2 nhóm | Số `gate_run` gắn release |
| Hiệu năng | Hồ sơ chức năng 500 event | ≤ 2 s (p95) | APM |

---

## 11. Kế hoạch phát hành

Thời gian tính theo tuần kể từ T0. Số tuần giả định đội 3–4 người, cần xác nhận (PQ-01).

| Mốc | Thời gian | Nội dung | Tiêu chí hoàn thành |
|---|---|---|---|
| M0 — Khám phá | Song song, 4–6 tuần | Phỏng vấn 8–10 người mỗi nhóm; spike RLS, webhook, gate | Chốt nhóm đầu tiên; AC-F1-03, AC-F5-02 đạt trên spike |
| M1 — Lõi | Tuần 1–2 | F1, F2, event store | AC-F1, AC-F2 đạt |
| M2 — Công việc | Tuần 3–4 | F3, F4 | AC-F3, AC-F4 đạt; dùng nội bộ được |
| M3 — GitHub | Tuần 5–6 | F5 | AC-F5 đạt trên repo thật |
| M4 — Test và hồ sơ | Tuần 7–8 | F6, F7 | AC-F6, AC-F7 đạt; demo 1 chức năng đầu-cuối |
| M5 — Gate và dogfood | Tuần 9–10 | F8, F9, F10 (Could) | AC-F8, AC-F9 đạt; ≥ 1 nhóm dùng thử bắt đầu |

---

## 12. Rủi ro

| Rủi ro | Mức | Giảm thiểu |
|---|---|---|
| Phạm vi lan sang nghiên cứu/AI trước khi MVP chứng minh giá trị | Cao | Giữ Phụ lục B; chỉ mở phase sau khi đạt tiêu chí thoát của MVP ([PLAN](PLAN_Strata.md)) |
| Phần lớn dữ liệu không tự đến (thiếu mã item trong commit) → lặp lại thất bại "không ai ghi chép" | Cao | Gợi ý quy ước tên nhánh; liên kết thủ công 1 bước; đo tỷ lệ `auto` |
| Cạnh tranh: Jira, Linear, GitHub Projects, Plane, OpenProject có thể thêm tính năng tương tự | Cao | Tập trung vào snapshot bất biến + gate + truy vết; kiểm chứng giá trị sớm bằng phỏng vấn (chưa kiểm chứng cạnh tranh) |
| Pain point chưa kiểm chứng (nhiều "Giả định") | Cao | M0: phỏng vấn trước khi cam kết; điều chỉnh PRD |
| Trở thành "Jira thứ hai" do cấu hình phức tạp | Trung bình | Mặc định tốt; workflow cố định trong MVP; không cho tùy biến trường |
| Gate bị Goodhart (đạt gate ≠ chất lượng đúng) | Trung bình | Luật cần duyệt người; hiển thị lý do gate; không dùng để chấm điểm cá nhân |
| Rò rỉ chéo tenant | Cao | RLS + test tự động trong CI; review truy vấn thô |
| Timeline thành công cụ giám sát cá nhân | Trung bình | Hiển thị theo hành động/vai trò; không bảng xếp hạng |

---

## 13. Phụ thuộc và giả định

**Phụ thuộc:** GitHub App và webhook hoạt động ổn định; nhóm dùng thử dùng GitHub và có CI sinh JUnit XML; có nhân sự full-stack TypeScript và PostgreSQL; hạ tầng container và object storage.

**Giả định:** nhóm mục tiêu 3–10 người dùng GitHub; quy ước mã item trong commit/PR chấp nhận được; JUnit XML đủ phổ biến cho giai đoạn đầu (pytest, Go test, Jest đều xuất được); đội build 3–4 người; chưa có số liệu baseline về thời gian tổng hợp thông tin.

---

## 14. Câu hỏi mở

| ID | Câu hỏi | Đề xuất |
|---|---|---|
| PQ-01 | Quy mô và kỹ năng đội build, ngày T0? | Chốt trước khi cam kết lịch ở mục 11 |
| PQ-02 | Nhóm người dùng đầu tiên: nhóm sản phẩm nhỏ, outsource, hay lớp/đồ án sinh viên? | Chọn nhóm có CI + JUnit sẵn và 1 BA/QA |
| PQ-03 | Chỉ GitHub hay thêm GitLab trong MVP? | Chỉ GitHub; GitLab ở phase 2 |
| PQ-04 | Xây từ đầu hay dựng trên Plane/OpenProject? | Xây lõi mỏng riêng; đánh giá lại sau M0 |
| PQ-05 | Ngăn xếp công nghệ (TypeScript hay Python)? | TypeScript mặc định, chốt theo kỹ năng |
| PQ-06 | Triển khai SaaS hay tự host? | SaaS trước; tự host khi có khách yêu cầu |
| PQ-07 | Mô hình kinh doanh (thương mại, nội bộ, học thuật)? | Chưa quyết định; ảnh hưởng quyết định silo/self-host |
| PQ-08 | Iteration = sprint hay chu kỳ qua gate? | MVP dùng sprint; chu kỳ gate xét ở phase 2 |
| PQ-09 | "Strata" là tên mã nội bộ hay tên thương mại? Cần kiểm tra trùng tên và tên miền trước khi công bố | Dùng làm tên mã; kiểm tra thương hiệu trước khi ra mắt |
| PQ-10 | Chính sách lưu dữ liệu test (thời hạn lưu file JUnit gốc)? | 12 tháng, cấu hình theo workspace |

---

## 15. Truy vết

| Mục PRD | Tài liệu gốc |
|---|---|
| 2 Vấn đề | `docs/background/brainstorm-summary.md` mục 2–3 |
| 3, 5 Mục tiêu, phạm vi | `docs/background/brainstorm-summary.md` mục 12 (danh sách chức năng P0), 14 (lộ trình) |
| 6 F1, F2 | `docs/background/brainstorm-summary.md` mục 4.4–6; `docs/background/system-architecture.md` mục Dữ liệu và đa tenant |
| 6 F3 | `docs/background/brainstorm-summary.md` mục 4 |
| 6 F5, F6 | `docs/background/brainstorm-summary.md` mục 7.3 |
| 6 F7 | `docs/background/brainstorm-summary.md` mục 8 |
| 6 F8, F9 | `docs/background/brainstorm-summary.md` mục 7.2, 7.4 |
| 9 Kiến trúc | `docs/background/system-architecture.md` |
| Phụ lục B | `docs/background/brainstorm-summary.md` mục 11, 12 (P1, P2); `docs/background/research-landscape-report.md` |
| Kế hoạch dài hạn | [PLAN_Strata.md](PLAN_Strata.md) |

---

## Phụ lục A — Các điểm đã thay đổi so với bản brainstorm rộng

### A.1 Pain point

| Pain point cũ | Đã sửa |
|---|---|
| Gộp pain point của phần mềm, nghiên cứu và AI/ML | MVP chỉ giữ pain point phần mềm (mục 2). Pain point nghiên cứu/AI chuyển sang Plan, cần phỏng vấn trước khi dùng. |
| Số liệu từ khảo sát nhà cung cấp coi như sự thật | Gắn nhãn "Có nguồn (định hướng)" hoặc "Giả định". |
| Chưa có bằng chứng trực tiếp cho PM nghiên cứu và trưởng nhóm AI | Ghi rõ là khoảng trống; phỏng vấn thuộc phase Khám phá của Plan. |

### A.2 Sản phẩm và kỹ thuật

| # | Bản brainstorm | MVP hiện tại | Lý do |
|---|---|---|---|
| 1 | Ba kind: software, research, ai | Chỉ `software`; cột `kind` vẫn giữ | Một lát cắt hẹp có dữ liệu tự động |
| 2 | Workflow designer đầy đủ (source/input/process/switch/gate/agent/output) | Gate cơ bản gắn với chuyển trạng thái/release | Tránh "Jira thứ hai" |
| 3 | AI agent mức 0–3 | Không có agent | Chờ gate và đo chất lượng; Plan phase 3 |
| 4 | Space private/public + publish | Chưa có | Cần cho nghiên cứu và agent |
| 5 | Ước lượng khoảng, rủi ro, nghẽn | Chưa có | Cần dữ liệu lịch sử từ dùng thật |
| 6 | Tìm kiếm có lọc quyền (FTS + vector) | Tìm theo mã và tiêu đề | Vector/RAG cần space và phân loại dữ liệu |

---

## Phụ lục B — Backlog tính năng phase sau

Thứ tự và điều kiện vào/thoát ở [PLAN_Strata.md](PLAN_Strata.md).

| ID | Tính năng | Hiện trạng MVP | Phạm vi dự kiến | Ghi chú kỹ thuật |
|---|---|---|---|---|
| WF-01 | Workflow designer: nút source/input/process/switch/gate/output | Gate cố định (F8) | Phase 2 | Token + dataflow; luật gate là dữ liệu có phiên bản; vòng lặp có giới hạn |
| WF-02 | Lan truyền stale khi upstream đổi | Chỉ `stale` ở AC (F9) | Phase 2 | Đồ thị phụ thuộc giữa nút |
| HIS-01 | Lịch sử thay đổi có diff, version tree | Chưa có | Phase 2 | `parent_version_id` cho artifact |
| INT-01 | Connector GitLab, CI (Jenkins, GitHub Actions nâng cao), SonarQube, Figma, Confluence | Chỉ GitHub + JUnit | Phase 2 | Adapter chuẩn hóa về schema snapshot |
| AGT-01 | Node `agent`, input contract, output schema, vùng nháp, `agent_run` | Không có | Phase 3 | Qua MCP tool gateway; agent không ghi thẳng dữ liệu chính |
| AGT-02 | Guardrail: allowlist công cụ, ngân sách, sandbox, nút dừng khẩn cấp | Không có | Phase 3 | Chống prompt injection: nội dung ngoài là dữ liệu |
| AGT-03 | Mức tự chủ 2–3, số liệu chất lượng agent | Không có | Sau phase 3 | Chỉ mở khi có tỷ lệ chấp nhận ổn định |
| SPC-01 | Space private/public cá nhân, dự án, tổ chức; publish tường minh | Mặc định dự án | Phase 3 | `publication` + tham chiếu ngược; thu hồi được |
| SRC-01 | Tìm kiếm FTS + vector có lọc quyền | Tìm mã/tiêu đề | Phase 3 | Mỗi đoạn gắn space + mức hiển thị; pgvector |
| AUTHZ-01 | ABAC, phân quyền cấp artifact | RBAC theo dự án | Phase 3 | Policy decision point dùng chung UI/API/search/agent |
| EST-01 | Ước lượng dạng khoảng, spike, sổ rủi ro | Chưa có | Phase 4 | Monte Carlo trên thông lượng lịch sử |
| FLOW-01 | Cycle time, WIP, CFD, quy tắc phát hiện nghẽn | Chưa có | Phase 4 | Đo quy trình, không đo cá nhân |
| COACH-01 | Gợi ý thực hành kèm bằng chứng, PDCA, Andon | Chưa có | Phase 7 | Gợi ý là đề xuất, nhóm có quyền từ chối |
| RSC-01 | Kind `research`: giả thuyết, thí nghiệm, kết quả, vòng đời trạng thái | Chưa có | Phase 5 | Sau khi phỏng vấn PM nghiên cứu |
| RSC-02 | Truy vết claim trong bản thảo ↔ run/thí nghiệm gốc | Chưa có | Phase 5 | Chuỗi giả thuyết → thí nghiệm → số liệu → bản thảo |
| RSC-03 | Liên kết Zotero, Overleaf, OSF, ORCID/Crossref | Chưa có | Phase 5 | Adapter; kiểm chứng trích dẫn |
| RSC-04 | Quản lý vòng đời viết bài báo: bản thảo, phản biện, nộp, xuất bản | Chưa có | Phase 5 | Phụ thuộc chính sách AI của nhà xuất bản |
| ML-01 | Kind `ai`: experiment run, tham số, metric, dataset version, checkpoint | Chưa có | Phase 6 | Ghi nhận thí nghiệm bị hủy kèm lý do |
| ML-02 | Connector MLflow, Weights & Biases, DVC; so sánh run, model registry cơ bản | Chưa có | Phase 6 | Adapter, không thay thế công cụ tracking |
| ENT-01 | DB riêng theo tenant (silo), vùng dữ liệu, khóa theo tenant | Pooled + RLS | Phase 6 | Bảng `tenant` điều phối đã có từ MVP |
| MOB-01 | Ứng dụng mobile | Web responsive | Chưa xếp lịch | Theo nhu cầu người dùng |
