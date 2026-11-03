# Tổng hợp brainstorm: Ứng dụng quản lý dự án (Phần mềm · Nghiên cứu · AI)

> Tài liệu tổng hợp toàn bộ trao đổi trong phiên này. Các nhận định về sản phẩm hiện có (Jira, Backlog, Notion, OSF...) và kiến trúc nội bộ của họ dựa trên hiểu biết chung, **chưa được kiểm chứng lại với tài liệu chính thức**. Các số liệu pain point lấy từ nguồn ở cuối tài liệu, chủ yếu là khảo sát của nhà cung cấp công cụ nên chỉ có giá trị định hướng.

---

## 1. Tầm nhìn

Một nền tảng quản lý dự án hợp nhất cho **dự án phần mềm, dự án nghiên cứu và dự án AI/ML**, xoay quanh các ý tưởng:

1. **Lõi chung, cấu hình theo loại dự án (kind):** cùng một mô hình dữ liệu (item, link, artifact, event), mỗi loại dự án chỉ khác schema trường, vòng đời, template và tích hợp.
2. **Workflow có cổng kiểm định (gate):** dự án là một đồ thị gồm nút nguồn, nút người dùng, switch, gate và output/checkpoint. Chỉ khi vượt qua các gate mới đạt checkpoint.
3. **Chức năng (feature) là đơn vị điều hướng chính**, không phải task hay vai trò: mỗi chức năng có một "hồ sơ" gồm timeline, version tree, các vòng lặp và lịch sử thay đổi.
4. **Bằng chứng và lịch sử là công dân hạng nhất:** mọi thay đổi là event bất biến; mọi kết quả gắn với snapshot của nguồn dữ liệu.
5. **Làm độ bất định nhìn thấy được:** ước lượng dạng khoảng, sổ rủi ro, trạng thái "chưa thể ước lượng", dự báo lại theo vòng.
6. **Ứng dụng là người quan sát có dữ liệu, không phải người chỉ đạo:** phát hiện nghẽn, gợi ý thực hành kèm bằng chứng, nhóm tự quyết định.
7. **AI agent là một loại bên thực hiện của node:** agent đề xuất, gate quyết định; thành viên kiểm soát input và output của agent (xem mục 11).

---

## 2. Pain point đã tìm được

### 2.1. Theo lĩnh vực

| Lĩnh vực | Pain point chính | Mức tin cậy |
|---|---|---|
| **Phần mềm** | Lãnh đạo không hiểu khó khăn của developer (63% developer nói vậy, tăng từ 44%); quy trình nặng nề; deadline không thực tế, họp nhiều, thay đổi giữa chừng | Khảo sát Atlassian khá lớn; một số ý chỉ là bài viết quan điểm |
| **Nghiên cứu** | ELN không làm hài lòng; công cụ PM doanh nghiệp không hợp với học thuật; sổ thí nghiệm không được duy trì; vẫn dùng giấy song song với số | Khảo sát nhỏ, poster, luận văn; không có nghiên cứu lớn |
| **AI/ML** | Thí nghiệm bị bỏ dở (40–60% theo Comet); theo dõi thủ công (58% nhóm); notebook gây khó khăn xuyên suốt; tái lập và cộng tác khó | Phần lớn từ Comet (nhà cung cấp), mẫu nhỏ (~508 người Mỹ) |

### 2.2. Pain point chung

- Thông tin rời rạc, phải tổng hợp thủ công.
- Mất lịch sử quyết định và thí nghiệm, khó tái lập.
- Công cụ không khớp quy trình thực tế.
- Khoảng cách giữa người quản lý và người làm.
- Thất bại (thí nghiệm hủy, giả thuyết bị bác) không được ghi nhận như dữ liệu có giá trị.

### 2.3. Khoảng trống bằng chứng

Chưa có nguồn nào đo trực tiếp pain point của **người quản lý dự án nghiên cứu** hay **trưởng nhóm AI**. Cần phỏng vấn thực địa (xem mục 14).

---

## 3. Hiện trạng sản phẩm trên thị trường

| Nhóm | Ví dụ | Giải quyết tốt | Còn thiếu |
|---|---|---|---|
| Workspace tổng quát | Notion, Obsidian, Asana, ClickUp, Trello | Linh hoạt, cộng tác | Không có cấu trúc khoa học, không có bằng chứng |
| Tài liệu và tổng quan văn liệu | Zotero, Mendeley, Elicit, ResearchRabbit | Quản lý trích dẫn, khám phá paper | Tách khỏi công việc và thí nghiệm |
| ELN | Benchling, LabArchives, RSpace, SciNote, eLabFTW | Ghi chép thí nghiệm, audit trail | Yếu về task, timeline; trải nghiệm bị chê |
| Nền tảng mở | OSF, Overleaf, OpenProject, Plane | Chia sẻ, tiền đăng ký, viết cộng tác | OSF thiếu quản lý công việc |
| Quản trị cấp tổ chức | Pure, Converis, InfoEd, Cayuse | Hồ sơ công bố, tài trợ | Không phục vụ nhà nghiên cứu hằng ngày |
| ML tracking | MLflow, W&B, Comet, DVC | Run, metric, model registry | Không biết giả thuyết, tài liệu, task |

**Điểm chung các sản phẩm giải quyết tốt:** lưu trữ tập trung, template, phiên bản, tích hợp, chia sẻ/phân quyền, báo cáo, tìm kiếm.
**Điểm chung còn yếu:** giải thích *vì sao* (quyết định, lý do hủy), liên kết xuyên lĩnh vực, động lực ghi chép, ghi nhận thất bại.

### Khoảng trống của vòng đời bài báo khoa học

Chưa có công cụ nào quản lý trọn vòng đời. Chuỗi *giả thuyết → thí nghiệm → con số trong bài → phản biện → xuất bản* bị cắt khúc giữa OSF, ELN, W&B, Overleaf, hệ thống nộp bài của tạp chí và CRIS. Truy vết từ claim trong bản thảo về run/thí nghiệm gốc vẫn là thủ công.

---

## 4. Kiến trúc lõi

### 4.1. Năm khái niệm

| Khái niệm | Ý nghĩa |
|---|---|
| **Workspace** | Tổ chức/nhóm, đơn vị tenant |
| **Project** | Một dự án có `kind` (software / research / ai) |
| **Item** | Đơn vị công việc có kiểu: task, bug, story, epic, giả thuyết, thí nghiệm, run, paper... |
| **Link** | Quan hệ có kiểu giữa hai item: `blocks`, `implements`, `tests`, `cites`, `produced_by`... |
| **Artifact** | Bằng chứng: commit, PR, build, file, dataset, checkpoint, PDF |
| **Event** | Nhật ký bất biến: ai đổi gì, khi nào, kết quả |

### 4.2. Kind là cấu hình

Mỗi kind gồm: schema trường, vòng đời (state machine), template/view mặc định, tích hợp mặc định.

- Software: `backlog → in progress → review → done`
- Research: `proposed → approved → running → analyzing → written → submitted`
- AI run: `queued → running → evaluated → promoted / discarded`

### 4.3. Mô hình dữ liệu (PostgreSQL, hướng lai)

Cột chung cố định + `attrs JSONB` cho phần riêng theo kind. Bảng chính: `workspace`, `app_user`, `workspace_member`, `project`, `project_member`, `sprint`, `item`, `link`, `comment`, `artifact`, `item_artifact`, `release`, `release_item`, `event`.

Ghi chú thiết kế:
- Mã item (ví dụ `CHAT-27`) = `project.key` + `item.seq` (tăng nguyên tử).
- Xóa mềm (`deleted_at`) để giữ lịch sử.
- `event` chỉ thêm, không sửa/xóa.
- **Cần sửa:** `project_member.role` hiện là một giá trị đơn; phải đổi thành gán vai trò nhiều-nhiều (xem mục 6).
- **Cần sửa:** thêm `tenant_id` trực tiếp vào **mọi bảng** nếu chọn mô hình pooled (xem mục 5).

### 4.4. Multi-tenant

| Mô hình | Phù hợp |
|---|---|
| Pooled (`tenant_id` + Row-Level Security) | Gói tiêu chuẩn, nhóm nhỏ |
| Schema riêng | Trung gian |
| DB riêng (silo) | Doanh nghiệp, đại học, dữ liệu nhạy cảm, yêu cầu vùng dữ liệu |

Đề xuất: **mô hình lai** với bảng `tenant` làm tầng điều phối (tenant → nơi chứa dữ liệu). Tên miền (`org.app.com`) chỉ để **định tuyến**, không phải cơ chế bảo mật; cô lập thật phải ở tầng dữ liệu.

---

## 5. Phân quyền

Bài học từ Jira và Backlog (theo hiểu biết chung):

- **Jira:** nhiều lớp (global → nhóm/ứng dụng → project role → permission scheme → issue security → workflow/field). Rất chi tiết, cấu hình phức tạp.
- **Backlog:** hai cấp (tổ chức, dự án) với vai trò Administrator/Member/Reporter/Viewer. Đơn giản, ít chi tiết ở cấp issue.

Áp dụng cho sản phẩm:
- Phân quyền tới **cấp artifact** (dữ liệu chưa công bố, dữ liệu nhạy cảm).
- Vai trò gắn với **kind** và với **gate**.
- Tách quyền xem / bình luận / sửa / duyệt.
- Mọi thay đổi quyền ghi vào event.
- Mặc định đơn giản, mở các lớp phức tạp khi cần.

---

## 6. Vai trò và chức năng theo vai trò

Vai trò là **tập quyền gán được nhiều lần** (một người có thể vừa PO vừa PM).

| Vai trò | Trách nhiệm | Gate có quyền duyệt | Tiện ích chính |
|---|---|---|---|
| **PO** | Giá trị, ưu tiên, chấp nhận, phát hành | Chấp nhận nghiệp vụ, phát hành | Backlog ưu tiên, roadmap, bảng chấp nhận AC, dashboard giá trị |
| **PM** | Lịch trình, phạm vi, rủi ro, nguồn lực | Kế hoạch, thay đổi phạm vi | Timeline/Gantt, sổ rủi ro, công suất, báo cáo tự động, yêu cầu thay đổi, cấu hình workflow/gate |
| **BA** | Yêu cầu, tiêu chí chấp nhận, truy vết | Yêu cầu đủ để phát triển | Soạn yêu cầu có ID, ma trận truy vết, phân tích tác động, từ điển thuật ngữ |
| **QA** | Kế hoạch/test case/kết quả/lỗi | Chất lượng, sẵn sàng test | Kho test case, nhận kết quả CI, vòng đời lỗi, độ phủ AC, báo cáo sẵn sàng phát hành |
| **Dev** | Triển khai, ước lượng, liên kết code | Không duyệt gate nghiệp vụ | Hàng việc cá nhân, tự liên kết commit/PR, trạng thái build/test, ADR |

Nguyên tắc: BA và QA **không cần thấy source code**. Tầng nghiệp vụ dùng *deliverable* và *tiêu chí chấp nhận*; code/artifact là bằng chứng nằm phía sau, ẩn mặc định.

---

## 7. Workflow, gate và nguồn dữ liệu

### 7.1. Mô hình

Dự án = đồ thị gồm:
- **Entry:** datasource, input người dùng, hoặc deliverable do con người tạo.
- **Switch:** rẽ nhánh theo điều kiện/trạng thái.
- **Gate:** kiểm định chất lượng.
- **Output/Checkpoint:** điểm đạt cuối, kèm snapshot.

### 7.2. Quyết định kiến trúc

- Kết hợp **dataflow** (dữ liệu chảy qua pipeline) và **token** (đơn vị công việc di chuyển giữa trạng thái); token mang tham chiếu đến snapshot dữ liệu.
- Gate có ba kết quả: `pass` / `fail` (quay lại nút trước kèm lý do) / `needs_review` (chuyển cho người duyệt).
- Quy tắc gate lưu dưới dạng **dữ liệu có cấu trúc**, không phải code tùy ý. Ví dụ: `failed == 0`, `coverage >= 0.80`, `approved_by has role QA`.
- Mỗi kết quả gate là event bất biến (input nào, phiên bản luật nào, ai duyệt).

### 7.3. Nguồn dữ liệu

Git (commit, PR), CI, kết quả test (JUnit XML, pytest, Go test JSON...), phân tích tĩnh, công cụ theo dõi công việc, Figma/Confluence cho thiết kế và yêu cầu.

Nguyên tắc:
- Nguồn tạo **snapshot** gắn định danh bất biến (commit SHA, run ID); gate đánh giá snapshot, không đánh giá "nhánh hiện tại".
- **Adapter** chuẩn hóa mọi nguồn về schema chung; gate viết một lần dùng cho nhiều nguồn.
- Xử lý idempotent, đối soát bằng polling, token tối thiểu quyền, lưu tóm tắt trong DB còn file gốc là artifact.
- Liên kết commit ↔ item qua mã trong tên nhánh/commit, cho phép liên kết thủ công và ghi nguồn gốc liên kết.

### 7.4. Truy vết nghiệp vụ

```
Yêu cầu (BA) → AC-03 → Test case (QA) TC-17 → Kết quả CI (PASS ở ci-8817) → Gate QA → AC-03 đã kiểm chứng
```

Mỗi AC có ID ổn định; thay đổi yêu cầu đánh dấu test và gate liên quan là cần xem lại (lan truyền thay đổi).

### 7.5. Vấn đề phải thiết kế sẵn

Vòng lặp khi gate fail (có giới hạn số lần), lan truyền stale khi upstream đổi, rủi ro Goodhart (gate đạt ≠ chất lượng đúng nên cần phê duyệt người), tái lập (snapshot input/cấu hình/code), tách người tạo và người duyệt.

---

## 8. Hồ sơ chức năng (Feature dossier)

Chức năng là đơn vị điều hướng trung tâm, gom: yêu cầu (nhiều phiên bản), thiết kế, công việc dev, test và kết quả, lỗi, quyết định.

| Góc nhìn | Trả lời | Nguồn dữ liệu | Độ khó |
|---|---|---|---|
| **Timeline** | Chuyện gì xảy ra, theo thời gian, ai làm | Event lọc theo chức năng | Thấp |
| **Lịch sử thay đổi** | Nội dung đổi gì (có diff) | Phiên bản yêu cầu/thiết kế | Trung bình |
| **Version tree** | Phiên bản kế thừa/phân nhánh thế nào | Artifact có `parent_version_id` | Trung bình |
| **Spiral / vòng lặp** | Qua bao nhiêu vòng, mỗi vòng đổi gì và vì sao | Nhóm event theo iteration + kết quả gate | Cao |

Lưu ý: chỉ tạo version tree cho artifact có ý nghĩa (yêu cầu, thiết kế, PR, test case); chỉ sinh phiên bản khi có gate/phê duyệt/thay đổi được đánh dấu; hiển thị spiral như chế độ phụ, danh sách vòng có tóm tắt là chế độ mặc định; mặc định hiển thị theo hành động/vai trò, không theo cá nhân.

Câu hỏi mở: iteration ánh xạ với **sprint** hay với **chu kỳ qua gate chất lượng**? (Nghiêng về gate vì gắn với bằng chứng.)

---

## 9. Ước lượng và quản lý bất định

### 9.1. Hạn chế của công cụ hiện nay

Một con số duy nhất; độ chính xác giả tạo (Gantt tới từng ngày); không gắn với giả định/rủi ro; ước lượng bị dùng để đánh giá nhóm (Goodhart); không lưu lịch sử ước lượng; công việc khám phá bị trộn với công việc thường; bắt buộc điền số nên nhóm "bịa".

### 9.2. Hạn chế cố hữu của con người/nhóm

Planning fallacy (Kahneman & Tversky), Hofstadter's law, *unknown unknowns* (đặc biệt với thiết kế và tích hợp), thiết kế chưa chín, thời gian tích hợp bị bỏ quên, neo giá trị đầu tiên, kiến thức tập trung ở một người, scope creep không được ghi nhận.

### 9.3. Chức năng đề xuất

- Ước lượng dạng **khoảng/xác suất** (lạc quan–khả dĩ–bi quan; Monte Carlo trên thông lượng lịch sử).
- Trạng thái **"chưa thể ước lượng / đang khám phá"** hợp lệ.
- **Spike** có thời hạn, đầu ra là quyết định hoặc rủi ro đã giảm, không phải code.
- **Sổ rủi ro** gắn với chức năng (xác suất, tác động, ước lượng bị ảnh hưởng, người theo dõi).
- **Gate sớm:** "thiết kế đủ ổn định", "đã kiểm chứng tích hợp chính" là điều kiện để ước lượng chi tiết.
- **Lịch sử ước lượng** + sai lệch để hiệu chỉnh theo nhóm.
- **Dự báo lại theo vòng**, hiển thị độ bất định thu hẹp (cone of uncertainty).
- Đo lường để cải thiện quy trình, **không** để xếp hạng cá nhân.

---

## 10. Phát hiện nghẽn và gợi ý thực hành

Nguyên tắc: **phát hiện → giải thích bằng dữ liệu → gợi ý vài thực hành liên quan → nhóm quyết định → app tự kiểm tra kết quả**.

### 10.1. Chỉ số dòng chảy

Cycle time, WIP, thời gian chờ theo trạng thái (queue time), tuổi của item (aging), biểu đồ dòng tích lũy (CFD), tỷ lệ/thời gian bị chặn (và blocker lặp lại).

### 10.2. Khung phương pháp mượn

| Phương pháp | Áp dụng |
|---|---|
| **Theory of Constraints** (5 bước: xác định, khai thác, phụ thuộc, nâng cấp, lặp lại) | Khung hành động cho nghẽn |
| **Kanban / Lean**, WIP limit, Little's law | Giới hạn việc đang làm |
| **Toyota Andon / Kaizen / 5 Whys** | Cho phép "dừng luồng" và tìm nguyên nhân gốc |
| **PDCA (Deming/Shewhart)** | Vòng kiểm chứng gợi ý |
| **OODA (Boyd)** | Ra quyết định động, vòng lặp ngắn |
| **Sun Tzu, Clausewitz** | Thích nghi theo "địa hình" nhóm; chấp nhận ma sát và sương mù |

### 10.3. Ranh giới

Đo nghẽn của quy trình, không đo cá nhân; gợi ý là đề xuất và nhóm có quyền từ chối kèm lý do; mỗi gợi ý có thời hạn đánh giá; ưu tiên quy tắc đơn giản giải thích được trước khi dùng AI.

---

## 11. AI agent tự động hóa công việc trong workflow

> Bổ sung từ side chat về ý tưởng "agent thực hiện một node, thành viên kiểm soát input/output". Các side chat khác (RBAC/ABAC, đồng bộ nhiều nguồn, mô hình phát triển phần mềm, MLflow, W&B...) **chưa được đọc** và chưa gộp vào tài liệu này.

### 11.1. Ý tưởng

Node trong workflow đại diện cho công việc, nên bên thực hiện có thể là **người, công cụ tự động, hoặc agent AI**. Agent nhanh nhưng có thể sai một cách tự tin, nên phần "thành viên kiểm soát input và output" là **tính năng cốt lõi**, không phải phụ.

### 11.2. Bốn nguyên tắc nền

1. **Agent đề xuất, gate quyết định.** Output mặc định là *bản nháp* (draft artifact); chỉ khi qua gate (tự động và/hoặc người duyệt) mới thành deliverable chính thức.
2. **Agent không tự duyệt chính mình** (tách quyền như với người).
3. **Mỗi node agent có một owner là thành viên**, chịu trách nhiệm về output được chấp nhận.
4. **Mọi lần chạy đều truy vết được** như các event khác.

### 11.3. Kiểm soát đầu vào

| Cơ chế | Mô tả |
|---|---|
| **Input contract** | Khai báo agent nhận gì: schema, loại artifact, nguồn được phép |
| **Context package** | Hiển thị cho thành viên trước khi chạy: agent sẽ "thấy" những gì |
| **Xem và chỉnh trước khi chạy** | Bỏ bớt, thêm ghi chú, che dữ liệu nhạy cảm |
| **Phân loại dữ liệu** | Công khai / nội bộ / bí mật / chưa công bố; agent chỉ nhận mức được phép |
| **Snapshot đầu vào** | Lưu băm và bản sao input để tái lập và đối chiếu |

**Lưu ý bảo mật:** nội dung từ nguồn ngoài (PR, comment, tài liệu) là **dữ liệu không đáng tin** và có thể chứa chỉ dẫn độc hại (prompt injection). Phải coi đó là dữ liệu để xử lý, không phải lệnh, và giới hạn quyền của agent để nội dung xấu cũng không gây hại lớn.

### 11.4. Kiểm soát đầu ra

- **Output contract:** định dạng bắt buộc (JSON schema, mẫu tài liệu); sai định dạng bị từ chối tự động.
- **Kiểm tra tự động trước:** chạy được không, lint, schema, khớp tiêu chí chấp nhận, eval nếu có.
- **Duyệt bởi người (`needs_review`):** xem diff input/output; chấp nhận, sửa rồi chấp nhận, hoặc từ chối kèm phản hồi để chạy lại.
- **Ghi lại phần người sửa:** dữ liệu quý để đo chất lượng agent và cải thiện prompt.
- **Không ghi thẳng vào dữ liệu chính:** agent chỉ ghi vào vùng nháp; "promote" là hành động của người hoặc gate.

### 11.5. Mức tự chủ (autonomy) cấu hình theo node

| Mức | Hành vi | Phù hợp |
|---|---|---|
| **0 – Gợi ý** | Agent chỉ đưa ý kiến, người làm | Việc rủi ro cao, mới thử |
| **1 – Soạn thảo + duyệt** | Agent tạo bản nháp, người duyệt từng lần | Mặc định cho phần lớn công việc |
| **2 – Tự chạy + kiểm tra tự động** | Qua gate tự động thì đi tiếp, người xem tóm tắt | Việc lặp lại, có test rõ (sinh test case, định dạng tài liệu) |
| **3 – Tự chủ có lấy mẫu** | Người chỉ kiểm tra ngẫu nhiên | Chỉ khi đã có số liệu tin cậy dài hạn |

Mức nên **tự hạ** khi chất lượng giảm (ví dụ tỷ lệ bị từ chối tăng) và chỉ tăng khi có bằng chứng từ lịch sử.

### 11.6. Guardrail vận hành

- **Allowlist công cụ:** ví dụ đọc repo nhưng không push; API nào được gọi; ghi vào đâu.
- **Giới hạn ngân sách:** token, chi phí, thời gian chạy, số lần thử lại; vượt thì dừng và báo.
- **Sandbox** cho agent chạy code.
- **Danh tính riêng:** agent là một actor có tài khoản và quyền riêng, không dùng quyền của người giao việc.
- **Nút dừng khẩn cấp** (liên hệ Andon): thành viên có quyền dừng được một run.
- **Leo thang:** khi agent không chắc hoặc kiểm tra thất bại nhiều lần, trả việc về cho người kèm lý do.

### 11.7. Truy vết và cấu hình

```
agent_run: id, node_id, token_id, agent_id, agent_version, model,
  prompt_version, input_snapshot_ref, output_artifact_ref,
  tool_calls (trace), cost, duration, status,
  reviewer_id, decision (accepted/edited/rejected), edit_diff_ref
```

```yaml
node: draft_acceptance_criteria
  kind: agent
  owner_role: BA
  agent: ba-assistant@v4
  input: [requirement_v3, glossary]
  output_schema: acceptance_criteria.v1
  autonomy: 1          # soạn thảo + duyệt
  budget: { max_cost: 2.0, max_runs: 3 }
  gate: ba_review      # needs_review bởi vai trò BA
```

### 11.8. Ví dụ theo vai trò

| Vai trò | Agent làm | Con người làm |
|---|---|---|
| **BA** | Soạn tiêu chí chấp nhận từ yêu cầu | Duyệt và sửa |
| **QA** | Sinh test case từ AC, đề xuất ca biên | Chọn và chỉnh |
| **Dev** | Mở PR sửa lỗi nhỏ | Review bởi dev khác + kiểm tra tự động |
| **Nghiên cứu** | Tóm tắt tài liệu, chạy phân tích | Kiểm chứng nguồn trích dẫn trước khi dùng |
| **AI/ML** | Đề xuất cấu hình thí nghiệm | Phê duyệt chi phí GPU trước khi chạy |

### 11.9. Rủi ro riêng của agent

1. **Automation bias:** người duyệt bấm "chấp nhận" theo thói quen. Cần hiển thị phần agent không chắc, so sánh với nguồn, đo thời gian xem xét.
2. **Chi phí kiểm chứng:** nếu duyệt tốn gần bằng tự làm thì agent không có lợi; đo tỷ lệ chấp nhận và mức chỉnh sửa theo node.
3. **Trách nhiệm không rõ:** quy định owner và ghi lại ai chấp nhận.
4. **Rò rỉ dữ liệu** khi gửi ngữ cảnh ra mô hình bên ngoài: chính sách theo phân loại dữ liệu, tùy chọn mô hình tự host.
5. **Không tái lập:** mô hình thay đổi theo thời gian; ghi phiên bản mô hình và prompt, coi output là snapshot.
6. **Dùng dữ liệu agent để đánh giá cá nhân:** tránh.

### 11.10. Gợi ý MVP và câu hỏi mở

- MVP chỉ làm **mức 0 và 1**: input contract, xem/chỉnh context, output schema, gate duyệt bởi người, bản ghi `agent_run`. Chỉ mở mức cao hơn khi có số liệu về tỷ lệ chấp nhận và chỉnh sửa.
- **Câu hỏi mở:** agent do **chính ứng dụng cung cấp** (tích hợp mô hình và quản lý prompt) hay là **agent bên ngoài** do khách hàng tự gắn qua giao diện chuẩn (webhook, MCP)? Hai cách dẫn đến thiết kế bảo mật và trách nhiệm rất khác nhau.

---

## 12. Danh sách chức năng tổng hợp

Ký hiệu ưu tiên: **P0** = MVP, **P1** = giai đoạn 2, **P2** = sau đó.

### A. Nền tảng

| # | Chức năng | Ưu tiên |
|---|---|---|
| A1 | Workspace, project, thành viên, đăng nhập | P0 |
| A2 | Item có kiểu, trường tùy biến (JSONB có schema đăng ký) | P0 |
| A3 | Link có kiểu giữa các item (kể cả khác dự án) | P0 |
| A4 | Event log bất biến, audit trail | P0 |
| A5 | Artifact (commit, PR, build, file, tài liệu) | P0 |
| A6 | Multi-tenant: `tenant_id` mọi bảng + RLS; tầng điều phối tenant | P0 |
| A7 | Silo DB / vùng dữ liệu cho doanh nghiệp, đại học | P2 |
| A8 | Comment, mention, thông báo | P0 |

### B. Quản lý công việc

| # | Chức năng | Ưu tiên |
|---|---|---|
| B1 | Backlog, Kanban, bảng, sprint | P0 |
| B2 | Timeline/Gantt, milestone, phụ thuộc | P1 |
| B3 | Release, changelog, phiên bản | P1 |
| B4 | Template theo kind (software / research / ai) | P1 |
| B5 | Bug: severity, vòng đời | P0 |

### C. Workflow và gate

| # | Chức năng | Ưu tiên |
|---|---|---|
| C1 | Trình thiết kế workflow (nút: source/input/process/switch/gate/output) | P1 |
| C2 | Gate với kết quả pass / fail / needs_review | P1 |
| C3 | Quy tắc gate dạng dữ liệu, có phiên bản | P1 |
| C4 | Checkpoint với snapshot, tái lập | P1 |
| C5 | Vòng lặp có giới hạn, lan truyền stale khi upstream đổi | P1 |
| C6 | Tách người tạo và người duyệt | P1 |

### D. Nguồn dữ liệu và tích hợp

| # | Chức năng | Ưu tiên |
|---|---|---|
| D1 | Connector GitHub/GitLab (webhook + đối soát polling) | P0 |
| D2 | Adapter kết quả test (JUnit XML trước) | P0 |
| D3 | Tự liên kết commit/PR ↔ item (kèm liên kết thủ công) | P0 |
| D4 | Snapshot nguồn với định danh bất biến | P0 |
| D5 | Connector CI, SonarQube, Figma, Confluence, Zotero, MLflow/W&B | P2 |

### E. Hồ sơ chức năng và truy vết

| # | Chức năng | Ưu tiên |
|---|---|---|
| E1 | Hồ sơ chức năng gom yêu cầu/thiết kế/việc/test/lỗi/quyết định | P0 |
| E2 | Timeline theo chức năng | P0 |
| E3 | Lịch sử thay đổi có diff cho yêu cầu/thiết kế | P1 |
| E4 | Version tree (yêu cầu, thiết kế, PR, test case) | P1 |
| E5 | Ma trận truy vết AC ↔ test ↔ kết quả | P1 |
| E6 | Phân tích tác động khi đổi yêu cầu | P1 |
| E7 | Spiral / vòng lặp, danh sách vòng có tóm tắt | P2 |

### F. Theo vai trò

| # | Chức năng | Ưu tiên |
|---|---|---|
| F1 | Vai trò nhiều-nhiều, ma trận quyền (role/permission) | P0 |
| F2 | Góc nhìn riêng: PO, PM, BA, QA, Dev | P1 |
| F3 | Phân quyền cấp artifact | P1 |
| F4 | Kho test case, vòng đời lỗi, độ phủ AC | P1 |
| F5 | Báo cáo sẵn sàng phát hành | P1 |
| F6 | Sổ rủi ro, yêu cầu thay đổi, công suất nhóm | P1 |

### G. Ước lượng và bất định

| # | Chức năng | Ưu tiên |
|---|---|---|
| G1 | Ước lượng dạng khoảng/độ tin cậy | P1 |
| G2 | Trạng thái "chưa thể ước lượng", spike có thời hạn | P1 |
| G3 | Sổ rủi ro gắn chức năng | P1 |
| G4 | Lịch sử ước lượng và hiệu chỉnh theo nhóm | P2 |
| G5 | Mô phỏng Monte Carlo, dự báo lại theo vòng | P2 |

### H. Phát hiện nghẽn và coaching

| # | Chức năng | Ưu tiên |
|---|---|---|
| H1 | Cycle time, WIP, queue time, aging, tỷ lệ chặn | P1 |
| H2 | CFD | P1 |
| H3 | Quy tắc phát hiện nghẽn (điều kiện, ngưỡng, gợi ý) | P1 |
| H4 | Gợi ý thực hành kèm bằng chứng, nhóm chọn/từ chối | P2 |
| H5 | Tự đánh giá kết quả gợi ý sau thời hạn (PDCA) | P2 |
| H6 | Andon: cho phép "dừng luồng" và gắn cảnh báo | P2 |

### I. Nghiên cứu và AI (module theo kind)

| # | Chức năng | Ưu tiên |
|---|---|---|
| I1 | Giả thuyết, thí nghiệm, kết quả (kind research) | P1 |
| I2 | Experiment run: tham số, metric, dataset version, checkpoint (kind ai) | P1 |
| I3 | So sánh các run, model registry cơ bản | P2 |
| I4 | Ghi nhận thí nghiệm bị hủy kèm lý do như dữ liệu | P1 |
| I5 | Truy vết claim trong bản thảo ↔ run/thí nghiệm gốc | P2 |
| I6 | Liên kết Zotero, Overleaf, OSF | P2 |

### J. AI agent trong workflow

| # | Chức năng | Ưu tiên |
|---|---|---|
| J1 | Node loại `agent` với owner, input contract, output schema | P1 |
| J2 | Xem và chỉnh context package trước khi agent chạy | P1 |
| J3 | Output vào vùng nháp, gate duyệt bởi người (xem diff, sửa, từ chối kèm phản hồi) | P1 |
| J4 | Bản ghi `agent_run` (phiên bản mô hình/prompt, snapshot input, trace, chi phí, quyết định) | P1 |
| J5 | Mức tự chủ 0–1 cấu hình theo node | P1 |
| J6 | Guardrail: allowlist công cụ, ngân sách, danh tính riêng cho agent, nút dừng khẩn cấp, leo thang | P1 |
| J7 | Phân loại dữ liệu và chính sách gửi ngữ cảnh ra mô hình ngoài | P1 |
| J8 | Chống prompt injection: coi nội dung nguồn ngoài là dữ liệu, sandbox | P1 |
| J9 | Số liệu chất lượng agent: tỷ lệ chấp nhận, mức chỉnh sửa, thời gian duyệt; tự hạ mức tự chủ | P2 |
| J10 | Mức tự chủ 2–3 (tự chạy có kiểm tra tự động, lấy mẫu) | P2 |
| J11 | Tích hợp agent bên ngoài qua giao diện chuẩn (webhook, MCP) | P2 |

---

## 13. Nhận định

### Điểm mạnh của hướng đi
- Khoảng trống **thật**: công cụ hiện tại hoặc linh hoạt mà không có bằng chứng, hoặc có bằng chứng mà không quản lý công việc. Chưa thấy sản phẩm nối cả giả thuyết ↔ thí nghiệm ↔ bản thảo ↔ task ↔ commit.
- Mô hình lõi (item/link/artifact/event) đủ tổng quát cho ba loại dự án mà không ép một quy trình cứng.
- Kết hợp **gate + bằng chứng tự động + phê duyệt người** có khả năng tạo khác biệt rõ so với công cụ PM thuần túy.
- Hồ sơ chức năng và truy vết giải quyết đúng điểm đau mà BA/QA/PO thực sự có, chứ không chỉ dev.
- Làm bất định nhìn thấy được là hướng ngược với phần lớn công cụ hiện nay.

### Rủi ro lớn
1. **Phạm vi quá rộng:** làm đồng thời ba kind, workflow engine, truy vết, ước lượng và coaching sẽ rất khó. Cần chọn lát cắt hẹp nhất để chứng minh giá trị.
2. **Gate dễ bị Goodhart:** gate đạt ≠ chất lượng đạt; cần phê duyệt người và tránh dùng chỉ số để xếp hạng cá nhân.
3. **Gánh nặng nhập liệu:** nếu phần lớn event không đến tự động (Git, CI, test), sản phẩm sẽ lặp lại thất bại "không ai ghi chép" của ELN.
4. **Độ phức tạp cấu hình:** workflow designer, phân quyền nhiều lớp và quy tắc gate có thể trở thành "Jira thứ hai" nếu không có mặc định tốt.
5. **Giả định chưa kiểm chứng:** bằng chứng pain point chủ yếu từ khảo sát nhà cung cấp; chưa có dữ liệu trực tiếp từ quản lý dự án nghiên cứu và trưởng nhóm AI.
6. **Cạnh tranh và tích hợp:** hướng "lớp liên kết" trên Git/CI/W&B/Zotero phụ thuộc chất lượng connector và có thể bị các nền tảng lớn bắt chước.
7. **Quyền riêng tư của nhóm:** timeline theo cá nhân, ước lượng, và phát hiện nghẽn dễ trở thành giám sát nếu thiết kế không cẩn thận.
8. **Agent làm tăng bề mặt rủi ro:** automation bias, prompt injection, rò rỉ dữ liệu ra mô hình ngoài, output không tái lập, chi phí kiểm chứng ngang tự làm (chi tiết ở mục 11.9). Nên hẹp ở mức tự chủ 0–1 trong MVP.

### Quyết định đã nghiêng nhưng chưa chốt
- Cấu trúc dữ liệu: cột chung + JSONB có schema (nghiêng cách lai).
- Iteration = chu kỳ gate hay sprint (nghiêng gate).
- Pooled trước, silo cho doanh nghiệp.
- Gợi ý thực hành là đề xuất, không phải quy định.

### Quyết định còn mở
1. Đối tượng đầu tiên: nhóm phần mềm, lab nghiên cứu trong trường, hay nhóm AI?
2. Xây từ đầu hay dựng trên nền open-source (Plane, OpenProject)?
3. Mô hình kinh doanh: thương mại, nội bộ, học thuật?
4. Quy mô nhóm mục tiêu (cá nhân, 3–10 người, tổ chức lớn).
5. Nền tảng: web, desktop, hay mobile.
6. Agent do ứng dụng cung cấp hay agent bên ngoài do khách hàng gắn vào (webhook, MCP).

---

## 14. Lộ trình đề xuất

| Giai đoạn | Nội dung | Mục tiêu kiểm chứng |
|---|---|---|
| **0. Khám phá (4–6 tuần)** | Phỏng vấn 8–10 người mỗi nhóm; khảo sát ngắn 50–100 người | Xác nhận pain point nào thật sự đau và ai sẵn sàng dùng |
| **1. MVP hẹp** | Lõi (A), tích hợp GitHub + JUnit (D1–D4), hồ sơ chức năng + timeline (E1–E2), vai trò cơ bản (F1), 1 kind | Nhóm thấy một nơi duy nhất cho một chức năng, không cần nhập tay |
| **2. Gate và truy vết** | Workflow + gate (C), ma trận truy vết (E5), view theo vai trò (F2), nghẽn cơ bản (H1–H3), agent mức 0–1 (J1–J8) | Gate dùng được cho phát hành; BA/QA dùng không cần thấy code |
| **3. Bất định và nghiên cứu/AI** | Ước lượng khoảng, rủi ro (G), module research/ai (I), version tree | Ước lượng bớt "điểm" và mở rộng sang kind khác |
| **4. Coaching và quy mô** | Gợi ý thực hành, PDCA, silo DB, connector bổ sung | Thích nghi theo nhóm; sẵn sàng khách doanh nghiệp |

Lát cắt MVP nên đặt ở **giao điểm của ba thứ có sẵn dữ liệu tự động**: Git + kết quả test + hồ sơ chức năng, cho **một kind**; tránh làm workflow designer đầy đủ ngay từ đầu.

---

## 15. Việc nên làm tiếp

1. Soạn **bộ câu hỏi phỏng vấn** cho PM nghiên cứu, trưởng nhóm AI, và nhóm phần mềm.
2. Chốt **ma trận quyền ở dạng dữ liệu** (`role`, `permission`, `role_permission`) và cập nhật schema (`tenant_id` mọi bảng, vai trò nhiều-nhiều).
3. Viết **kịch bản một sprint hoàn chỉnh** cho dự án chatbot (CHAT) với năm vai trò, thể hiện deliverable, bằng chứng, và gate.
4. Phác thảo **bộ quy tắc phát hiện nghẽn** ban đầu (điều kiện, ngưỡng, gợi ý).
5. Phác thảo **schema chuẩn hóa** cho snapshot GitHub và JUnit XML.
6. (Tùy chọn) Tìm thông tin cập nhật về giá/tính năng các sản phẩm và chính sách AI của nhà xuất bản.
7. Chốt mô hình agent (của ứng dụng hay bên ngoài) và viết `agent_run` + node `agent` vào schema.
8. Đọc các side chat còn lại (RBAC/ABAC, đồng bộ nhiều nguồn, mô hình phát triển phần mềm, MLflow, W&B, quy trình viết bài báo) và gộp vào tài liệu.

---

## Nguồn tham khảo (từ các lần tìm kiếm)

- [Atlassian, State of Developer Experience 2025](https://www.atlassian.com/teams/software-development/state-of-developer-experience-2025)
- [JetBrains, Developer Experience & Productivity 2025](https://lp.jetbrains.com/devex-productivity-report-full-2025-dataviz/)
- [InfoWorld, The process is the problem for developers](https://www.infoworld.com/article/2212803/the-process-is-the-problem-for-developers.html)
- [DEV Community, Why do developers struggle with project management](https://dev.to/pratham_naik_project_manager/why-do-developers-struggle-with-project-management-and-how-to-fix-it-1bgp)
- [CLPMag, Survey: scientists frustrated with ELN systems](https://clpmag.com/lab-management/survey-scientists-frustrated-eln-systems/)
- [Liminal Bios, Why the lab notebook is failing scientists](https://liminalbios.substack.com/p/why-the-lab-notebook-is-failing-scientists)
- [Cureus, Tailoring project management resources for research teams](https://www.cureus.com/posters/3287-tailoring-project-management-resources-for-clinical-and-translational-research-teams-insights-from-user-experience-interviews)
- [Đại học Southampton, luận văn về công cụ số của nhà nghiên cứu](https://eprints.soton.ac.uk/384916/1/Final_Thesis_Combined.pdf)
- [Comet, State of MLOps 2023](https://go.comet.ml/rs/912-JJP-445/images/Comet_State_of_MLOPs_Report-2023_ML_Practitioner_Survey.pdf)
- [Comet, thông cáo báo chí 2022](https://www.comet.com/site/news-and-events/press-releases/2022/comet-releases-new-survey-highlighting-ais-latest-challenges-too-much-friction-too-little-ml/)
- [Chalmers, nghiên cứu quản lý thí nghiệm ML (2022)](https://www.cse.chalmers.se/~bergert/paper/2022-seip-mlexpmgmt.pdf)
