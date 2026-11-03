# Kiến trúc hệ thống đề xuất: workspace quản lý dự án (phần mềm · nghiên cứu · AI)

## Context

Sản phẩm là một workspace hợp nhất cho dự án phần mềm, nghiên cứu và AI. Từ brainstorm, các yêu cầu kiến trúc cốt lõi là:

- Mô hình dữ liệu chung: item, link, artifact, event; mỗi loại dự án (kind) chỉ là cấu hình.
- Workflow có gate, lấy bằng chứng tự động từ Git/CI/kết quả test, tái lập được qua snapshot.
- Hồ sơ chức năng: timeline, lịch sử thay đổi, version tree, vòng lặp.
- Không gian private/public cá nhân, dự án, tổ chức; phân quyền tới cấp artifact; đa tenant.
- AI agent là một loại actor: thao tác qua lớp công cụ, ghi qua changeset, có guardrail.
- Ước lượng dạng khoảng, sổ rủi ro, phát hiện nghẽn.

Thư mục làm việc hiện tại (finance-manager) không chứa mã liên quan, nên không có code sẵn để tái sử dụng. Đây là thiết kế từ đầu. Chưa đọc các side chat về RBAC/ABAC và đồng bộ nhiều nguồn; nên đọc trước khi chốt mục Phân quyền và Connector.

## Nguyên tắc kiến trúc

1. **Modular monolith trước, tách dịch vụ sau.** Một codebase, ranh giới module rõ; chỉ tách khi có áp lực về tải hoặc cô lập (workflow engine, agent runtime, ingest).
2. **Trạng thái quan hệ + event log bất biến.** Bảng quan hệ giữ trạng thái hiện tại; mọi thay đổi ghi `event` (outbox) làm nguồn cho timeline, báo cáo và projection.
3. **Một điểm quyết định quyền (policy engine)** dùng chung cho UI, API, tìm kiếm và agent.
4. **Mọi thứ truy cập dữ liệu đều qua một lớp công cụ có xác thực** (kể cả agent); không có đường tắt vào DB.
5. **Bằng chứng là snapshot bất biến**, gate chỉ đánh giá snapshot.
6. **Connector là adapter cắm vào**, chuẩn hóa về schema chung.

## Sơ đồ logic

```
Clients: Web app · (mobile/capture) · CLI/API · Agent bên ngoài (MCP)
              │
Edge: API gateway / BFF · OIDC auth · tenant resolver (subdomain→tenant) · rate limit
              │
┌─────────────┴────────────────────────────────────────────────────────┐
│ Modular monolith (module = ranh giới có thể tách sau)               │
│  identity+tenancy · spaces+policy · work graph (item/link/kind)     │
│  workflow engine (node/gate/token) · evidence (artifact/snapshot)    │
│  ideas · estimation+risk · flow analytics · notifications            │
│  search/retrieval (permission-aware) · agent tool gateway (MCP)      │
└─────────────┬────────────────────────────────────────────────────────┘
              │ outbox → job queue
Workers: connector ingest · gate evaluator · projection builders ·
         schedulers · agent runners (sandbox)
              │
Data: PostgreSQL (RLS, JSONB, FTS, pgvector) · object storage (artifact/log)
      · queue (Postgres-based ban đầu) · secrets vault · tenant control plane
```

## Thành phần chính

| Module | Trách nhiệm | Ghi chú thiết kế |
|---|---|---|
| **Identity + tenancy** | Người dùng, tổ chức, SSO, bảng `tenant` (tenant → nơi chứa dữ liệu) | Tên miền chỉ để định tuyến; cô lập thật ở tầng dữ liệu |
| **Spaces + policy** | Space private/public cá nhân, dự án, tổ chức; RBAC theo vai trò + ABAC theo thuộc tính (space, phân loại dữ liệu, kind) | Publish là hành động tường minh, tạo `publication` |
| **Work graph** | `item`, `link`, đăng ký kind (schema trường + vòng đời + template) | Cột chung + `attrs` JSONB có schema đăng ký |
| **Workflow engine** | Nút source/input/process/switch/gate/agent/output; token; kết quả gate pass/fail/needs_review | Quy tắc gate là dữ liệu có phiên bản; thực thi bền vững (có thể khởi lại) |
| **Evidence** | `artifact`, `source_snapshot`, truy vết AC↔test↔kết quả | Snapshot gắn định danh bất biến (commit SHA, run ID) |
| **Event store + projections** | `event` bất biến; timeline, lịch sử thay đổi, version tree, chỉ số dòng chảy | Projection xây lại được từ event |
| **Ideas** | Ý tưởng, giả định, kết quả, mặc định private | Liên kết ngược khi chuyển thành item |
| **Estimation + risk** | Ước lượng khoảng, sổ rủi ro, lịch sử ước lượng | Lưu mọi bản sửa ước lượng |
| **Flow analytics** | Cycle time, WIP, CFD, quy tắc nghẽn → gợi ý | Rule-based trước, đo ở cấp quy trình |
| **Search/retrieval** | Full-text + vector, lọc quyền lúc truy xuất | Mỗi đoạn gắn space + mức hiển thị |
| **Agent tool gateway** | MCP server của chính app; changeset; danh tính agent; ngân sách; sandbox | Agent không ghi thẳng dữ liệu chính |
| **Integrations** | Connector GitHub/GitLab, JUnit, CI; webhook + polling đối soát | Idempotent theo `external_ref`; token tối thiểu quyền trong vault |

## Luồng tiêu biểu

1. **Commit/PR → gate.** Webhook → ingest → chuẩn hóa → `source_snapshot` → liên kết `CHAT-27` → ghi event → gate evaluator đọc snapshot → chuyển token → thông báo.
2. **Node agent.** Dựng context package theo input contract → thành viên xem/chỉnh → runner chạy trong sandbox qua tool gateway → output vào vùng nháp → changeset → gate `needs_review` → người chấp nhận → promote + ghi `agent_run`.
3. **Publish private → public.** Xem trước nội dung → tạo bản sao có tham chiếu ngược → `publication` + event → có thể thu hồi.

## Dữ liệu và đa tenant

- **PostgreSQL** là kho chính. `tenant_id` trên **mọi bảng** + Row-Level Security; đây là điểm sửa so với schema trước (workspace chỉ liên kết gián tiếp).
- **Mô hình lai:** pooled cho gói chuẩn; silo (DB riêng, có thể theo vùng) cho khách doanh nghiệp/đại học; `tenant` control plane ánh xạ nơi chứa để chuyển đổi không đổi code ứng dụng.
- **Object storage** cho log, báo cáo, file gốc; DB chỉ giữ tóm tắt và tham chiếu.
- **Outbox pattern** để event và thay đổi trạng thái nhất quán; queue ban đầu dùng Postgres, chuyển NATS/Kafka khi cần.

## Bảo mật

- Policy engine trung tâm; quyết định quyền cho mọi tầng, kể cả tìm kiếm và agent.
- Quyền hiệu lực của agent = giao của quyền người ủy quyền và danh sách cho phép của agent.
- Quản trị viên tổ chức **không** đọc được space private (ngoại lệ pháp lý đi theo quy trình riêng, có dấu vết).
- Nội dung do người khác tạo luôn là dữ liệu không đáng tin với agent (chống prompt injection); sandbox cho code do agent chạy.
- Mã hóa khi lưu; khóa theo tenant cho gói doanh nghiệp; audit trail mọi thay đổi quyền.
- Phân loại dữ liệu quyết định nội dung nào được gửi ra mô hình bên ngoài; tùy chọn mô hình tự host.

## Quyết định chính và khuyến nghị

| Quyết định | Khuyến nghị | Lý do |
|---|---|---|
| Kiến trúc | Modular monolith | Đội nhỏ, nhiều ý tưởng còn chưa kiểm chứng |
| CSDL | PostgreSQL duy nhất ban đầu (JSONB, FTS, pgvector, RLS) | Giảm số thành phần vận hành |
| Thực thi workflow | Engine tự viết đơn giản + hàng đợi bền vững; xem xét Temporal khi cần | Tránh phụ thuộc nặng ở MVP |
| Công cụ agent | MCP server nội bộ, agent ngoài gắn qua cùng giao diện | Một đường truy cập duy nhất |
| Ngăn xếp | TypeScript (backend + web) là mặc định; Python nếu đội mạnh ML/agent | Chốt theo kỹ năng nhóm |
| Xây hay dựng trên mã nguồn mở | Xây lõi mỏng riêng | Mô hình gate/bằng chứng/agent khác nền tảng PM sẵn có |

## Lộ trình xây theo lát cắt

1. **Lõi + tenancy + RLS + event** (work graph, item/link/artifact, vai trò cơ bản).
2. **Connector GitHub + JUnit**, snapshot, hồ sơ chức năng và timeline.
3. **Workflow + gate** (nút nguồn/gate/output, needs_review, checkpoint).
4. **Agent mức 0–1** qua tool gateway, changeset, `agent_run`.
5. **Space private/public + publish**, tìm kiếm có lọc quyền.
6. Ước lượng khoảng, rủi ro, phân tích nghẽn; module research/AI; silo DB.

## Kiểm chứng kiến trúc (spike trước khi cam kết)

- **Rò rỉ chéo tenant:** test tự động chứng minh RLS chặn truy vấn thiếu `tenant_id`, kể cả qua tìm kiếm vector.
- **Idempotent webhook:** gửi trùng/lệch thứ tự cùng sự kiện, kết quả vẫn một snapshot.
- **Tái lập gate:** chạy lại gate trên cùng snapshot và phiên bản luật cho cùng kết quả.
- **Luồng agent đầu-cuối:** một node agent chạy từ context package đến changeset được chấp nhận, kèm test rằng agent không đọc được space private của người khác.
- **Tải sơ bộ:** ước lượng số event/ngày cho một tenant lớn để xác định lúc nào cần tách ingest và queue.

## Câu hỏi còn mở

1. Nhóm người dùng và kind đầu tiên (ảnh hưởng thứ tự lát cắt).
2. Agent do ứng dụng cung cấp hay agent bên ngoài; mô hình tự host hay dịch vụ ngoài.
3. "Public" là trong tổ chức hay ra ngoài.
4. Ngăn xếp công nghệ theo kỹ năng nhóm.

## Ghi chú về công việc đang chờ

Đợt nghiên cứu sâu về nghiên cứu khoa học còn một nhóm (nhu cầu) đang chạy và bước viết báo cáo chưa thực hiện. Bước viết báo cáo ghi file nên sẽ làm sau khi thoát chế độ lập kế hoạch.
