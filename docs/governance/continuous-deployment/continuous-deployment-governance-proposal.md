# Continuous Deployment Governance Proposal

- Version: 1.0
- Status: Proposed
- Source Task: TASK-009
- Source Project State: Official Project State v1.1.3
- Date: 2026-07-17
- Author: Development Workspace
- Review Authority: Project State Manager


1. Problem Statement
Current State

Theo Official Project State v1.1.3:

Đã hoàn thành:

Backend Continuous Integration Pipeline
Frontend Continuous Integration Pipeline
Backend Continuous Delivery Artifact Pipeline

Backend hiện có khả năng:

Build thành công
Verify trên PostgreSQL
Package executable Spring Boot JAR
Upload artifact lên GitHub Actions

Tuy nhiên:

Continuous Deployment vẫn ở trạng thái Not Started và chưa có bất kỳ governance baseline nào xác định:

Deployment target
Environment policy
Approval policy
Secret management
Database migration strategy
Rollback strategy
Health verification
Operational ownership

Điều này khiến việc triển khai deployment là không an toàn và có nguy cơ thay đổi Engineering Baseline nếu Development Workspace tự đưa ra quyết định.


2. Scope of This Proposal

Proposal này chỉ nhằm xác định các quyết định governance cần thiết trước khi triển khai Continuous Deployment.

Proposal này không:

triển khai workflow
tạo infrastructure
deploy application
cấu hình cloud
tạo secret
thay đổi source code
thay đổi GitHub Actions


3. Continuous Delivery vs Continuous Deployment
Continuous Delivery

Kết thúc tại:

Build

↓

Test

↓

Package

↓

Artifact

Deployment vẫn cần thao tác thủ công.

Đây là trạng thái hiện tại của dự án.

Continuous Deployment

Mở rộng thêm:

Build

↓

Test

↓

Artifact

↓

Deploy

↓

Health Check

↓

Verification

↓

Running Environment

Đây là phạm vi sẽ được xem xét sau khi governance được phê duyệt.

4. Governance Objectives

Proposal này phải xác định:

deployment scope
deployment target
deployment trigger
approval policy
branch policy
secret policy
artifact source
migration policy
rollback policy
health check
logging
operational evidence

5. Required Baseline Decisions

Các quyết định dưới đây bắt buộc phải được xác định trước khi bất kỳ deployment workflow nào được tạo.

Decision	Status
Deployment Target	Open Decision
Hosting Platform	Open Decision
First Deployment Environment	Open Decision
Artifact Source	Required
Trigger Policy	Required
Branch Policy	Required
Secret Management	Required
Workflow Permissions	Required
Database Migration	Required
Health Check	Required
Rollback Strategy	Required
Logging Policy	Required
Deployment Evidence	Required


6. Open Decisions

Hiện tại chưa có đủ Evidence để kết luận các nội dung sau:

Deployment Target

Ví dụ:

VPS
Docker Host
Kubernetes
Azure
AWS
GCP
Railway
Render

Không được tự lựa chọn.

First Environment

Có thể là:

Development
Integration
Staging
Production

Chưa có Governance Decision.

Deployment Ownership

Chưa xác định:

Developer
DevOps
Manual Operator
GitHub Environment Approver
Production Strategy

Chưa xác định:

Blue Green
Rolling
Recreate
Canary
Approval Policy

Chưa xác định:

Automatic
Manual Approval
Environment Protection Rules

7. Assumptions NOT Allowed

Proposal này nghiêm cấm sử dụng các giả định sau:

Production là môi trường đầu tiên.
AWS là nền tảng mặc định.
Docker là phương thức triển khai mặc định.
Flyway luôn chạy tự động.
Deployment luôn được kích hoạt sau mỗi lần merge.
Main branch luôn được phép deploy.
Artifact mới nhất luôn được sử dụng.
Không cần rollback.
Không cần health check.
Secret có thể lưu trực tiếp trong repository.


8. Candidate Deployment Approaches & Trade-off Analysis
8.1 Objective

Mục tiêu của phần này là:

Xác định các mô hình Continuous Deployment khả thi.
Phân tích ưu và nhược điểm của từng phương án.
Chỉ ra tác động tới Engineering, Security và Operations.
Không mặc định bất kỳ nền tảng triển khai nào khi chưa có Governance Decision.
8.2 Evaluation Criteria

Mọi phương án được đánh giá theo cùng một bộ tiêu chí để đảm bảo khách quan.

Evaluation Criteria	Description
Simplicity	Mức độ đơn giản khi triển khai và vận hành.
Operational Cost	Chi phí hạ tầng và vận hành lâu dài.
Security	Khả năng bảo vệ secret, quyền truy cập và môi trường triển khai.
Maintainability	Mức độ dễ bảo trì của pipeline và môi trường.
Scalability	Khả năng mở rộng khi quy mô dự án tăng.
Recovery Capability	Khả năng rollback hoặc khôi phục khi deployment thất bại.
Learning Curve	Độ phức tạp đối với nhóm phát triển.
Automation Potential	Khả năng tự động hóa toàn bộ quy trình deployment.
8.3 Candidate Approach A — Traditional Virtual Machine (VM / VPS)
Overview

Artifact được triển khai trực tiếp lên một máy chủ ảo hoặc VPS.

Ví dụ:

Linux VM
VPS
Dedicated Server

Backend JAR được copy lên máy chủ và chạy như một service.

Advantages
Đơn giản.
Ít thành phần.
Phù hợp với dự án nhỏ hoặc MVP.
Dễ debug trực tiếp trên máy chủ.
Không yêu cầu container orchestration.
Disadvantages
Phụ thuộc cấu hình hệ điều hành.
Khó đảm bảo tính nhất quán giữa các môi trường.
Rollback thường phải thực hiện thủ công.
Khả năng mở rộng hạn chế.
Dễ phát sinh "configuration drift" theo thời gian.
Suitable When
Một môi trường triển khai duy nhất.
Lưu lượng thấp.
Hạ tầng đơn giản.
Chưa có nhu cầu mở rộng nhanh.
8.4 Candidate Approach B — Container-based Deployment
Overview

Artifact được đóng gói thành Docker Image và triển khai trên một container host.

Có thể chạy trên:

Docker Engine
Docker Compose
Container Host
Advantages
Môi trường chạy nhất quán.
Dễ tái triển khai.
Dễ rollback bằng image version.
Phù hợp với CI/CD hiện đại.
Giảm khác biệt giữa Development và Production.
Disadvantages
Cần quản lý image registry.
Tăng độ phức tạp vận hành.
Yêu cầu kiến thức về container.
Cần quản lý image lifecycle.
Suitable When
Muốn chuẩn hóa môi trường.
Có kế hoạch mở rộng sau này.
Muốn giảm rủi ro cấu hình khác nhau giữa các máy.
8.5 Candidate Approach C — Managed Platform (PaaS)
Overview

Artifact hoặc container được triển khai lên một nền tảng Platform as a Service.

Ví dụ có thể bao gồm nhiều dịch vụ PaaS khác nhau, nhưng Proposal không mặc định lựa chọn bất kỳ nhà cung cấp nào.

Advantages
Không cần quản trị máy chủ.
Triển khai nhanh.
Tự động quản lý nhiều tác vụ vận hành.
Giảm khối lượng công việc DevOps.
Disadvantages
Phụ thuộc vào nền tảng.
Giới hạn khả năng tùy biến.
Có nguy cơ "vendor lock-in".
Chi phí có thể tăng theo quy mô.
Suitable When
Nhóm phát triển nhỏ.
Muốn ưu tiên tốc độ triển khai.
Chấp nhận đánh đổi quyền kiểm soát hạ tầng.
8.6 Candidate Approach D — Kubernetes-based Deployment
Overview

Artifact được triển khai trên một cụm Kubernetes.

Advantages
Khả năng mở rộng cao.
Tự động self-healing.
Rolling update mạnh mẽ.
Hỗ trợ nhiều chiến lược deployment.
Phù hợp với hệ thống lớn.
Disadvantages
Độ phức tạp rất cao.
Chi phí vận hành lớn.
Không phù hợp cho Foundation Phase.
Yêu cầu kiến thức chuyên sâu.
Suitable When
Hệ thống lớn.
Nhiều microservices.
Nhiều môi trường.
Đội ngũ DevOps chuyên trách.
8.7 Comparative Analysis
Criteria	VM / VPS	Container	Managed Platform	Kubernetes
Simplicity	High	Medium	High	Low
Operational Cost	Low	Medium	Medium	High
Security Control	Medium	High	Medium	High
Maintainability	Medium	High	Medium	High
Scalability	Medium	High	Medium	Very High
Recovery Capability	Medium	High	Medium	Very High
Learning Curve	Low	Medium	Low	High
Automation Potential	Medium	High	High	Very High
8.8 Trade-off Summary
Approach	Main Strength	Main Trade-off
Traditional VM	Đơn giản, ít thành phần	Khó mở rộng và dễ lệch cấu hình
Container-based	Nhất quán, dễ tái triển khai	Cần quản lý image và container runtime
Managed Platform	Triển khai nhanh, ít vận hành	Phụ thuộc nhà cung cấp và giới hạn tùy biến
Kubernetes	Mạnh về mở rộng và tự động hóa	Độ phức tạp và chi phí cao
8.9 Governance Conclusion

Dựa trên Official Project State v1.1.3 và phạm vi của TASK-009:

Proposal đã xác định các mô hình Continuous Deployment khả thi và phân tích trade-off của từng phương án.
Không có phương án nào được chọn làm baseline vì hiện chưa có bằng chứng hoặc quyết định governance cho deployment target, hosting platform hoặc environment đầu tiên.
Việc lựa chọn một phương án cụ thể phải được thực hiện thông qua ADR hoặc Approved Change Request, sau đó mới có thể tạo task triển khai Continuous Deployment.


9. Security Considerations
9.1 Objective

Mục tiêu của phần Security là đảm bảo rằng quá trình Continuous Deployment:

Không làm lộ thông tin nhạy cảm.
Không mở rộng quyền truy cập vượt quá nhu cầu thực tế.
Có khả năng truy vết (Traceability).
Có thể kiểm toán (Auditability).
Tuân thủ nguyên tắc Least Privilege.
Có thể mở rộng an toàn khi dự án phát triển.
9.2 Security Objectives

Continuous Deployment phải đảm bảo các mục tiêu sau:

Objective	Description
Confidentiality	Secret và credential không bị lộ trong source code, log hoặc artifact.
Integrity	Chỉ artifact đã được xác minh mới được phép triển khai.
Availability	Deployment không làm mất khả năng phục vụ nếu xảy ra lỗi.
Accountability	Mọi deployment phải xác định được ai, khi nào và từ nguồn nào thực hiện.
Traceability	Có thể truy vết từ runtime về đúng commit, workflow và artifact tương ứng.
9.3 Security Assets

Các tài sản cần được bảo vệ bao gồm:

Deployment credentials.
Runtime credentials.
Database credentials.
API keys.
Signing keys (nếu được áp dụng trong tương lai).
SSH keys (nếu mô hình triển khai yêu cầu).
Access tokens.
Environment secrets.
Deployment logs.
Deployment artifacts.
GitHub Actions permissions.
9.4 Security Principles

Proposal đề xuất các nguyên tắc sau làm Security Baseline.

Principle 1 — Least Privilege

Deployment Workflow chỉ được cấp đúng quyền cần thiết để thực hiện deployment.

Không cấp quyền rộng hơn chỉ để "thuận tiện".

Principle 2 — Separation of Duties

Các hoạt động sau nên được tách biệt:

Build.
Test.
Artifact creation.
Deployment approval.
Runtime operation.

Việc tách biệt giúp giảm rủi ro và tăng khả năng kiểm soát.

Principle 3 — Immutable Artifact

Artifact đã được xác minh trong Continuous Delivery phải được triển khai nguyên trạng.

Không build lại trong bước deployment.

Điều này giúp đảm bảo tính nhất quán giữa artifact đã kiểm thử và artifact được triển khai.

Principle 4 — Secret Isolation

Secret không được:

hard-code trong source code,
commit vào Git,
lưu trong artifact,
xuất hiện trong log,
ghi trong tài liệu công khai.

Secret phải được quản lý bởi cơ chế quản lý secret phù hợp với nền tảng triển khai sau khi governance được phê duyệt.

Principle 5 — Secure Audit Trail

Mọi deployment cần có khả năng ghi nhận:

thời điểm triển khai,
người hoặc workflow thực hiện,
commit được triển khai,
artifact được sử dụng,
kết quả triển khai.

Điều này phục vụ kiểm toán và điều tra sự cố.

9.5 Secret Management Considerations
Required Decisions

Các nội dung sau bắt buộc phải được xác định trước khi triển khai Continuous Deployment:

Decision	Status
Secret Storage Mechanism	Open Decision
Secret Rotation Policy	Open Decision
Secret Access Policy	Open Decision
Secret Ownership	Open Decision
Secret Backup Strategy	Open Decision
Assumptions NOT Allowed

Không được mặc định rằng:

Secret sẽ lưu trong repository.
Secret sẽ lưu trong file cấu hình.
Secret sẽ lưu trên máy cá nhân.
Secret không cần thay đổi định kỳ.
Mọi workflow đều được phép đọc toàn bộ secret.
9.6 Workflow Permission Considerations

Deployment Workflow cần được thiết kế theo nguyên tắc quyền tối thiểu.

Các nhóm quyền cần được xác định gồm:

Permission Area	Status
Repository Access	Required
Artifact Access	Required
Environment Access	Required
Deployment Permission	Required
Secret Access	Required
Approval Permission	Required

Chi tiết từng quyền sẽ phụ thuộc vào nền tảng triển khai được phê duyệt trong tương lai.

9.7 Artifact Integrity

Continuous Deployment chỉ được phép sử dụng artifact đáp ứng các điều kiện sau:

Được tạo từ Continuous Delivery Pipeline.
Đã vượt qua toàn bộ bước xác minh.
Có thể truy vết đến commit nguồn.
Có thể xác định workflow tạo artifact.
Không bị thay đổi sau khi phát hành.

Proposal không quy định cơ chế xác minh cụ thể (ví dụ checksum hoặc ký số), vì đây là quyết định kỹ thuật cần được xác định trong ADR nếu dự án lựa chọn áp dụng.

9.8 Deployment Authorization

Trước khi deployment được phép thực hiện, cần xác định:

Required Decision	Status
Ai được phép kích hoạt deployment	Open Decision
Deployment có cần phê duyệt thủ công không	Open Decision
Điều kiện để deployment được phép chạy	Open Decision
Có phân biệt quyền giữa các môi trường không	Open Decision

Những nội dung này ảnh hưởng trực tiếp đến Security Baseline và không được tự giả định.

9.9 Logging and Audit Requirements

Mỗi lần deployment cần ghi nhận tối thiểu:

Deployment ID.
Deployment Time.
Source Repository.
Source Branch hoặc Tag.
Commit SHA.
Artifact Version.
Artifact Identifier.
Deployment Target.
Deployment Environment.
Deployment Result.
Trigger Type (Manual hoặc Automatic).
Approval Information (nếu có).

Proposal chỉ xác định yêu cầu ghi nhận, không quy định công cụ hoặc nền tảng ghi log.

9.10 Security Risks
Risk	Mitigation Requirement
Secret Leakage	Secret Isolation Policy
Unauthorized Deployment	Approval Policy + Least Privilege
Artifact Tampering	Artifact Traceability + Integrity Verification
Privilege Escalation	Workflow Permission Review
Missing Audit Trail	Mandatory Deployment Logging
Deploying Unverified Artifact	Chỉ sử dụng artifact từ Continuous Delivery Pipeline
9.11 Open Security Decisions

Các quyết định sau hiện chưa có đủ Evidence và phải được giữ ở trạng thái Open Decision:

Cơ chế quản lý secret.
Chính sách xoay vòng secret (rotation).
Mô hình xác thực giữa workflow và môi trường triển khai.
Cơ chế xác minh tính toàn vẹn của artifact.
Mô hình phân quyền cho deployment workflow.
Chính sách phê duyệt deployment.
Chính sách phân quyền theo từng môi trường.
9.12 Security Conclusion

Governance Proposal đã:

Xác định các mục tiêu bảo mật của Continuous Deployment.
Xác định các tài sản cần bảo vệ.
Đề xuất các nguyên tắc bảo mật nền tảng (Least Privilege, Separation of Duties, Immutable Artifact, Secret Isolation và Audit Trail).
Liệt kê các quyết định bảo mật bắt buộc trước khi triển khai.
Chỉ rõ các Open Security Decisions chưa đủ bằng chứng để kết luận.



# 10. Database Migration Considerations

## 10.1 Objective

Mục tiêu của phần này là xác định các nguyên tắc và quyết định bắt buộc liên quan đến database migration trước khi Continuous Deployment được triển khai.

Database migration phải được quản lý như một hoạt động có rủi ro độc lập, không được xem đơn thuần là một bước kỹ thuật mặc định trong deployment workflow.

Proposal này không:

* Chạy Flyway trên staging hoặc production.
* Thay đổi migration hiện tại.
* Tạo production database.
* Cấu hình production credential.
* Quyết định Flyway phải chạy tự động.
* Phê duyệt migration strategy.

---

## 10.2 Current Database Baseline

Theo baseline hiện tại của dự án:

* Backend sử dụng PostgreSQL.
* Flyway được sử dụng để quản lý database migration.
* CI backend đã được xác minh với PostgreSQL theo ADR-001.
* Backend artifact chỉ được tạo sau khi quá trình verify trên PostgreSQL hoàn tất.
* Chưa có staging database hoặc production database được phê duyệt.
* Chưa có policy chính thức cho việc thực thi migration trong deployment environment.

Như vậy, việc Flyway hoạt động thành công trong CI không đồng nghĩa Flyway đã được phép tự động chạy trong staging hoặc production.

---

## 10.3 Migration Governance Principles

### Principle 1 — Explicit Migration Policy

Mỗi environment phải có chính sách migration được phê duyệt rõ ràng.

Không được mặc định rằng cùng một migration policy sẽ áp dụng cho:

* Development.
* CI.
* Staging.
* Production.

---

### Principle 2 — Forward-Only by Default

Migration đã được áp dụng trên shared environment không nên bị sửa nội dung hoặc thay thế.

Các thay đổi tiếp theo nên được thực hiện bằng migration mới.

Nguyên tắc này giúp:

* Duy trì lịch sử schema.
* Giảm sai lệch giữa các environment.
* Cho phép truy vết thay đổi.
* Tránh checksum mismatch.

Việc áp dụng chính thức nguyên tắc forward-only vẫn cần governance approval.

---

### Principle 3 — Backward Compatibility

Thay đổi schema phải được đánh giá về khả năng tương thích với phiên bản ứng dụng đang chạy và phiên bản ứng dụng sắp triển khai.

Đặc biệt cần kiểm tra:

* Xóa cột.
* Đổi tên cột.
* Thay đổi kiểu dữ liệu.
* Thêm ràng buộc `NOT NULL`.
* Thêm unique constraint.
* Thay đổi foreign key.
* Thay đổi dữ liệu quy mô lớn.
* Thay đổi làm code cũ không còn hoạt động.

Migration phá vỡ tương thích không được triển khai như một bước thông thường mà không có kế hoạch chuyển đổi phù hợp.

---

### Principle 4 — Migration Before Application Compatibility

Thứ tự thực thi phải bảo đảm application và database luôn ở trạng thái tương thích.

Tùy loại thay đổi, migration có thể cần:

* Chạy trước application deployment.
* Chạy cùng deployment.
* Chạy sau application deployment.
* Chia thành nhiều giai đoạn.

Không được mặc định một thứ tự duy nhất cho mọi migration.

---

### Principle 5 — No Destructive Migration Without Explicit Approval

Các migration có khả năng gây mất dữ liệu hoặc khó đảo ngược phải được xem là high-risk.

Ví dụ:

* `DROP TABLE`.
* `DROP COLUMN`.
* Xóa dữ liệu hàng loạt.
* Thu hẹp kiểu dữ liệu.
* Thay đổi làm mất độ chính xác dữ liệu.
* Thêm constraint có thể làm migration thất bại trên dữ liệu hiện hữu.

Các migration này phải yêu cầu review và approval riêng.

---

## 10.4 Required Migration Decisions

Các quyết định sau phải được phê duyệt trước khi tạo deployment workflow:

| Decision                                            | Status        |
| --------------------------------------------------- | ------------- |
| Migration execution owner                           | Open Decision |
| Migration execution mechanism                       | Open Decision |
| Automatic hoặc manual execution                     | Open Decision |
| Environment-specific migration policy               | Open Decision |
| Migration timing relative to application deployment | Open Decision |
| Approval requirement for schema changes             | Open Decision |
| Destructive migration policy                        | Open Decision |
| Data migration policy                               | Open Decision |
| Backup requirement                                  | Open Decision |
| Failure recovery policy                             | Open Decision |
| Migration evidence requirements                     | Required      |
| Migration timeout policy                            | Open Decision |
| Concurrent deployment protection                    | Open Decision |

---

## 10.5 Migration Execution Models

### Option A — Application-Startup Migration

Application khởi động và Flyway tự động kiểm tra, sau đó áp dụng migration.

#### Advantages

* Quy trình đơn giản.
* Ít bước deployment riêng biệt.
* Phù hợp với môi trường nhỏ và ít instance.
* Migration gắn liền với application version.

#### Risks and Trade-offs

* Application startup có thể thất bại nếu migration lỗi.
* Khó phân tách lỗi database và lỗi application.
* Nhiều instance có thể đồng thời cố gắng khởi động.
* Credential của application phải có quyền thay đổi schema.
* Migration dài có thể kéo dài downtime.
* Rollback application không tự động rollback schema.

#### Governance Status

Không được chọn mặc định.

---

### Option B — Dedicated Migration Job

Migration được thực thi bởi một bước hoặc job riêng trước khi deploy application.

#### Advantages

* Phân tách migration khỏi runtime application.
* Có thể cấp quyền database riêng cho migration.
* Dễ ghi nhận evidence.
* Có thể chặn application deployment nếu migration thất bại.
* Phù hợp hơn với separation of duties.

#### Risks and Trade-offs

* Pipeline phức tạp hơn.
* Cần quản lý migration credential riêng.
* Cần xác định thứ tự và dependency chặt chẽ.
* Vẫn cần chiến lược recovery nếu migration đã áp dụng nhưng application deployment thất bại.

#### Governance Status

Có thể là phương án ưu tiên để xem xét, nhưng chưa được chọn làm baseline.

---

### Option C — Manual Migration Approval and Execution

Migration được một người có quyền thực thi thủ công trước deployment.

#### Advantages

* Mức kiểm soát cao.
* Phù hợp với migration rủi ro lớn.
* Cho phép kiểm tra backup và readiness trước khi chạy.

#### Risks and Trade-offs

* Dễ xảy ra sai sót thao tác.
* Giảm mức độ tự động hóa.
* Có thể tạo inconsistency nếu không có runbook.
* Phụ thuộc người vận hành.
* Khó mở rộng khi deployment thường xuyên.

#### Governance Status

Có thể phù hợp với production hoặc migration rủi ro cao, nhưng chưa có evidence để quyết định.

---

### Option D — Hybrid Migration Policy

Migration thông thường được tự động hóa; migration rủi ro cao yêu cầu manual approval hoặc thao tác riêng.

#### Advantages

* Cân bằng tốc độ và kiểm soát.
* Giảm thao tác thủ công cho migration an toàn.
* Tăng kiểm soát đối với destructive migration.
* Có thể áp dụng policy theo environment.

#### Risks and Trade-offs

* Cần tiêu chí phân loại migration rõ ràng.
* Quy trình phức tạp hơn.
* Có nguy cơ phân loại sai mức rủi ro.
* Cần governance và review discipline tốt.

#### Governance Status

Là phương án hợp lý để ADR xem xét, nhưng chưa được phê duyệt.

---

## 10.6 Comparative Analysis

| Criteria                            | Startup Migration | Dedicated Job | Manual Execution | Hybrid Policy |
| ----------------------------------- | ----------------: | ------------: | ---------------: | ------------: |
| Simplicity                          |              High |        Medium |           Medium |           Low |
| Automation                          |              High |          High |              Low |          High |
| Separation of Duties                |               Low |          High |             High |          High |
| Operational Control                 |               Low |          High |        Very High |          High |
| Auditability                        |            Medium |          High |           Medium |          High |
| Runtime Credential Risk             |              High |           Low |              Low |           Low |
| Human Error Risk                    |               Low |           Low |             High |        Medium |
| Suitability for High-Risk Migration |               Low |        Medium |             High |          High |

---

## 10.7 Schema Migration and Data Migration

Schema migration và data migration phải được phân biệt.

### Schema Migration

Thay đổi cấu trúc database, ví dụ:

* Tạo bảng.
* Thêm cột.
* Thêm index.
* Thêm constraint.
* Thay đổi foreign key.

### Data Migration

Thay đổi hoặc chuyển đổi dữ liệu hiện có, ví dụ:

* Backfill dữ liệu.
* Chuyển đổi format.
* Gộp hoặc tách giá trị.
* Di chuyển dữ liệu giữa các bảng.
* Chuẩn hóa dữ liệu cũ.

Data migration thường có rủi ro cao hơn vì:

* Có thể chạy lâu.
* Có thể khóa bảng.
* Có thể tiêu thụ tài nguyên lớn.
* Có thể gây mất hoặc sai dữ liệu.
* Có thể khó rollback.

Do đó, data migration không được mặc định xử lý giống schema migration.

---

## 10.8 Expand-and-Contract Strategy

Đối với thay đổi schema không tương thích, Proposal đề xuất ADR xem xét chiến lược expand-and-contract.

### Expand

Thêm cấu trúc mới mà chưa xóa cấu trúc cũ.

Ví dụ:

* Thêm cột mới.
* Cho phép code cũ và code mới cùng hoạt động.
* Backfill dữ liệu nếu cần.

### Migrate

Triển khai application sử dụng cấu trúc mới và xác minh dữ liệu.

### Contract

Chỉ xóa cấu trúc cũ sau khi:

* Không còn code sử dụng.
* Dữ liệu đã được chuyển đổi.
* Có evidence xác nhận.
* Có approval phù hợp.

Chiến lược này giảm rủi ro nhưng làm quá trình thay đổi kéo dài qua nhiều deployment.

---

## 10.9 Backup and Recovery Requirements

Trước migration có rủi ro cao, cần xác định:

* Có bắt buộc backup không.
* Loại backup được chấp nhận.
* Thời điểm backup.
* Thời gian lưu giữ.
* Người chịu trách nhiệm kiểm tra backup.
* Khả năng restore đã được thử nghiệm hay chưa.
* Recovery Point Objective nếu được áp dụng.
* Recovery Time Objective nếu được áp dụng.

Một backup chưa từng được kiểm tra restore không nên được xem là bằng chứng recovery đầy đủ.

Hiện chưa có đủ baseline để xác định chính sách backup cụ thể.

---

## 10.10 Migration Failure Scenarios

Governance policy phải xử lý tối thiểu các tình huống sau:

### Migration Fails Before Any Change

* Dừng deployment.
* Không deploy application mới.
* Ghi nhận log và evidence.
* Điều tra nguyên nhân.

### Migration Partially Applies

* Dừng deployment.
* Không tự động chạy lại nếu chưa đánh giá trạng thái schema.
* Kiểm tra Flyway history.
* Thực hiện recovery theo runbook được phê duyệt.

### Migration Succeeds but Application Deployment Fails

* Đánh giá application version cũ có còn tương thích không.
* Không mặc định rollback schema.
* Có thể rollback application nếu schema backward-compatible.
* Nếu không tương thích, cần recovery procedure riêng.

### Application Starts but Post-Deployment Validation Fails

* Dừng traffic hoặc rollback application theo policy.
* Đánh giá schema đã thay đổi có cho phép rollback application không.
* Không tự động thực hiện destructive reverse migration.

---

## 10.11 Rollback Limitations

Database rollback khác application rollback.

Application artifact thường có thể thay bằng phiên bản trước, nhưng database migration có thể:

* Không thể đảo ngược an toàn.
* Đã làm mất dữ liệu.
* Đã thay đổi dữ liệu theo cách code cũ không hiểu.
* Đã chạy quá lâu hoặc ảnh hưởng nhiều bảng.

Vì vậy, governance không được sử dụng giả định:

> “Rollback deployment sẽ tự động rollback database.”

Phương án an toàn hơn thường là:

* Forward-fix.
* Restore từ backup khi thật sự cần thiết.
* Sử dụng backward-compatible migration.
* Tách destructive change sang giai đoạn sau.

Việc chọn chính sách chính thức vẫn là Open Decision.

---

## 10.12 Migration Security Considerations

Migration credential phải được kiểm soát riêng.

Cần xem xét:

* Có tách migration credential khỏi runtime credential không.
* Ai được quyền truy cập credential.
* Workflow nào được phép sử dụng.
* Credential có bị giới hạn theo environment không.
* Credential có được rotate không.
* Log có thể làm lộ connection string hoặc password không.
* Migration role có quyền vượt quá nhu cầu không.

Theo nguyên tắc least privilege:

* Runtime application không nên mặc định có quyền tạo, sửa hoặc xóa schema.
* Migration workflow không nên mặc định có quyền quản trị toàn bộ database.
* Production credential không được dùng cho CI hoặc non-production.

---

## 10.13 Migration Concurrency and Locking

Governance policy phải ngăn nhiều deployment cùng thực thi migration không kiểm soát.

Cần xác định:

* Có cho phép nhiều deployment đồng thời không.
* Có concurrency lock ở workflow level không.
* Có environment-level deployment lock không.
* Hành vi khi một migration đang chạy.
* Timeout và cancellation policy.
* Cách xử lý khi workflow bị hủy giữa migration.

Flyway có cơ chế quản lý lịch sử migration, nhưng điều đó không thay thế deployment concurrency policy.

---

## 10.14 Migration Validation Requirements

Trước deployment, cần xác minh tối thiểu:

* Migration file hợp lệ.
* Migration history không có checksum mismatch.
* Migration đã vượt qua CI trên PostgreSQL.
* Artifact và migration thuộc cùng commit hoặc release identity.
* Không có migration bị sửa sau khi đã áp dụng.
* Các migration pending được xác định.
* Migration risk đã được phân loại.
* Approval đã đạt nếu policy yêu cầu.

Sau migration, cần xác minh:

* Flyway migration hoàn tất thành công.
* Schema version đúng với release.
* Không có migration pending ngoài dự kiến.
* Application có thể kết nối database.
* Readiness check đạt.
* Critical query hoặc smoke test đạt.
* Không xuất hiện database error nghiêm trọng trong log.

---

## 10.15 Required Migration Evidence

Mỗi migration deployment cần cung cấp evidence tối thiểu:

* Environment được migration.
* Database identifier không chứa secret.
* Commit SHA hoặc release identifier.
* Artifact identifier.
* Danh sách migration dự kiến.
* Schema version trước migration.
* Schema version sau migration.
* Thời điểm bắt đầu và kết thúc.
* Workflow hoặc người thực hiện.
* Approval record nếu được yêu cầu.
* Migration result.
* Post-migration validation result.
* Backup confirmation nếu policy yêu cầu.
* Recovery action nếu có lỗi.

Evidence không được chứa:

* Password.
* Access token.
* Secret connection string.
* Private key.
* Dữ liệu nhạy cảm của người dùng.

---

## 10.16 Open Database Migration Decisions

Các nội dung sau chưa đủ evidence và phải giữ trạng thái Open Decision:

* Environment đầu tiên được phép migration.
* Flyway chạy khi application startup hay bằng dedicated job.
* Migration tự động hay cần manual approval.
* Runtime application có quyền thay đổi schema hay không.
* Migration credential được quản lý bằng cơ chế nào.
* Production migration có yêu cầu backup bắt buộc hay không.
* Tiêu chí phân loại high-risk migration.
* Policy cho destructive migration.
* Policy cho data migration.
* Migration timeout.
* Concurrency protection.
* Recovery và restore ownership.
* Forward-fix hay reverse migration trong từng loại sự cố.
* Yêu cầu RPO và RTO.
* Quy trình kiểm tra restore.

---

## 10.17 Recommended Direction for Governance Review

Dựa trên rủi ro và nguyên tắc separation of duties, Proposal khuyến nghị ADR xem xét hướng sau:

* Sử dụng migration artifact từ cùng source revision với application artifact.
* Không build hoặc tạo migration mới trong deployment stage.
* Ưu tiên dedicated migration step hoặc hybrid policy thay vì mặc định chạy migration khi application startup.
* Áp dụng backward-compatible và forward-only migration.
* Chặn application deployment nếu migration thất bại.
* Không tự động rollback database.
* Sử dụng forward-fix hoặc restore theo runbook đã phê duyệt.
* Yêu cầu approval riêng cho destructive migration và high-risk data migration.
* Tách migration credential khỏi runtime credential khi nền tảng cho phép.
* Ghi nhận đầy đủ migration evidence.

Đây chỉ là **recommended direction**, không phải Selected Approach chính thức.

---

## 10.18 Database Migration Conclusion

Governance Proposal đã:

* Phân biệt application deployment với database migration.
* Xác định các nguyên tắc quản lý migration.
* Phân tích bốn mô hình thực thi migration.
* Đánh giá trade-off của từng mô hình.
* Phân biệt schema migration và data migration.
* Xác định backward compatibility và expand-and-contract strategy.
* Xác định backup, recovery và failure scenarios.
* Làm rõ rằng application rollback không đồng nghĩa database rollback.
* Xác định migration security, concurrency và evidence requirements.
* Liệt kê đầy đủ các Open Decision.

Không có migration nào được thực hiện và không có policy nào được tự phê duyệt. Database migration baseline vẫn chưa thay đổi và phải được quyết định thông qua governance artifact phù hợp.


# 11. Operational Considerations

## 11.1 Objective

Mục tiêu của phần này là xác định các yêu cầu vận hành bắt buộc trước khi Continuous Deployment được triển khai.

Operational baseline phải bảo đảm rằng mỗi deployment:

* Có người hoặc vai trò chịu trách nhiệm rõ ràng.
* Có trạng thái và kết quả có thể quan sát.
* Không gây xung đột với deployment khác.
* Có quy trình xử lý khi thất bại.
* Có khả năng dừng, khôi phục và điều tra.
* Có evidence đầy đủ để kiểm toán.
* Không phụ thuộc vào thao tác không được ghi nhận.

Proposal này không:

* Tạo deployment workflow.
* Provision infrastructure.
* Cấu hình monitoring platform.
* Tạo production runbook chính thức.
* Thực hiện deployment.
* Xác định nhà cung cấp hosting.
* Tự phê duyệt operational baseline.

---

## 11.2 Operational Ownership

Trước khi deployment đầu tiên được thực hiện, phải xác định rõ các vai trò sau:

| Operational Role         | Responsibility                                      | Status        |
| ------------------------ | --------------------------------------------------- | ------------- |
| Deployment Owner         | Chịu trách nhiệm về deployment process              | Open Decision |
| Environment Owner        | Chịu trách nhiệm đối với environment đích           | Open Decision |
| Application Owner        | Xác nhận application hoạt động đúng sau deployment  | Open Decision |
| Database Migration Owner | Chịu trách nhiệm về migration execution và recovery | Open Decision |
| Approval Authority       | Phê duyệt deployment nếu policy yêu cầu             | Open Decision |
| Incident Owner           | Điều phối xử lý khi deployment gây sự cố            | Open Decision |
| Rollback Authority       | Quyết định rollback hoặc recovery                   | Open Decision |

Một workflow tự động không loại bỏ nhu cầu xác định ownership.

---

## 11.3 Environment Model

Continuous Deployment phải phân biệt rõ các environment.

Ví dụ có thể gồm:

* Development.
* Integration.
* Testing.
* Staging.
* Production.

Tuy nhiên, TASK-009 chưa có đủ evidence để xác định:

* Bao nhiêu environment sẽ tồn tại.
* Environment nào được triển khai đầu tiên.
* Có staging riêng hay không.
* Non-production có dùng hạ tầng giống production hay không.
* Dữ liệu giữa các environment được quản lý như thế nào.

Các nội dung này phải giữ ở trạng thái Open Decision.

---

## 11.4 Environment Isolation

Mỗi environment phải được cô lập phù hợp về:

* Credential.
* Database.
* Runtime configuration.
* Network access.
* Secret.
* Deployment approval.
* Logging.
* Artifact history.

Không được mặc định:

* Dùng chung production secret cho non-production.
* Dùng chung database giữa staging và production.
* Dùng chung deployment credential cho mọi environment.
* Mọi branch đều được deploy vào mọi environment.

Mức độ isolation cụ thể phụ thuộc vào deployment platform được phê duyệt.

---

## 11.5 Deployment Trigger Operations

Deployment trigger phải được xác định rõ và có thể truy vết.

Các mô hình có thể gồm:

### Automatic on Branch Merge

Deployment tự động sau khi merge vào một branch được chỉ định.

#### Advantages

* Tốc độ cao.
* Ít thao tác thủ công.
* Phù hợp với non-production.
* Giảm nguy cơ quên deployment.

#### Risks

* Một merge có thể lập tức ảnh hưởng environment.
* Cần branch protection và test gate mạnh.
* Không phù hợp cho production nếu chưa có approval policy.

---

### Automatic on Tag or Release

Deployment chỉ chạy khi có tag hoặc release hợp lệ.

#### Advantages

* Release identity rõ ràng.
* Dễ audit.
* Giảm deployment ngoài ý muốn.
* Phù hợp với production governance.

#### Risks

* Cần quản lý version và tag discipline.
* Có thêm bước phát hành.
* Tag sai có thể dẫn tới deployment sai.

---

### Manual Workflow Dispatch

Người có quyền chủ động kích hoạt deployment.

#### Advantages

* Kiểm soát cao.
* Phù hợp với giai đoạn đầu.
* Dễ kết hợp manual approval.

#### Risks

* Phụ thuộc thao tác người dùng.
* Có thể xảy ra chọn sai artifact hoặc environment.
* Giảm mức độ tự động hóa.

---

### Scheduled Deployment

Deployment chạy theo lịch định trước.

#### Advantages

* Có deployment window rõ ràng.
* Dễ bố trí người hỗ trợ.
* Giảm rủi ro triển khai ngoài giờ kiểm soát.

#### Risks

* Có thể trì hoãn bản sửa lỗi.
* Không phù hợp với mọi loại release.
* Cần xử lý thay đổi khẩn cấp ngoài lịch.

---

## 11.6 Recommended Trigger Direction

Proposal khuyến nghị governance review xem xét:

* Non-production có thể cho phép automatic deployment sau khi quality gates đạt.
* Production nên yêu cầu release identity rõ ràng, environment protection và approval phù hợp.
* Không dùng artifact “mới nhất” không định danh.
* Mỗi deployment phải gắn với commit SHA, artifact ID và environment cụ thể.

Đây là recommended direction, không phải policy đã được phê duyệt.

---

## 11.7 Deployment Concurrency

Hệ thống phải ngăn các deployment xung đột.

Cần xác định:

* Có cho phép hai deployment cùng lúc vào một environment không.
* Deployment mới có hủy deployment cũ không.
* Có queue hay reject deployment mới không.
* Database migration có khóa deployment khác không.
* Có environment-level concurrency group không.
* Hành vi khi workflow bị hủy giữa chừng.

Proposal khuyến nghị:

* Chỉ một deployment active trên mỗi environment.
* Không cho phép application deployment vượt qua migration đang chạy.
* Không tự động hủy deployment đang ở giai đoạn migration hoặc recovery nếu chưa đánh giá tác động.

---

## 11.8 Deployment Sequence

Một deployment tiêu chuẩn có thể gồm:

1. Xác định release identity.
2. Kiểm tra approval và environment protection.
3. Truy xuất đúng immutable artifact.
4. Kiểm tra artifact traceability.
5. Kiểm tra environment readiness.
6. Thực hiện backup nếu migration policy yêu cầu.
7. Thực hiện database migration theo policy.
8. Deploy application.
9. Kiểm tra startup.
10. Chạy health check.
11. Chạy post-deployment validation.
12. Quan sát log và metric trong thời gian xác minh.
13. Đánh dấu thành công hoặc bắt đầu recovery.
14. Lưu completion evidence.

Thứ tự chính thức phải được ADR xác định theo deployment platform và migration strategy.

---

## 11.9 Availability Considerations

Deployment phải đánh giá tác động tới khả năng phục vụ.

Các yêu cầu cần xác định gồm:

* Downtime có được chấp nhận không.
* Thời gian downtime tối đa.
* Có cần zero-downtime deployment không.
* Có cần maintenance window không.
* Hành vi đối với request đang xử lý.
* Hành vi đối với background job.
* Connection tới database khi restart.
* Session hoặc token có bị ảnh hưởng không.

Hiện chưa có SLA, SLO hoặc availability target chính thức, nên các giá trị cụ thể là Open Decision.

---

## 11.10 Deployment Strategies

### Recreate Deployment

Dừng phiên bản cũ rồi khởi động phiên bản mới.

#### Advantages

* Đơn giản.
* Chi phí thấp.
* Phù hợp với một instance.

#### Risks

* Có downtime.
* Recovery có thể chậm.
* Không phù hợp khi yêu cầu availability cao.

---

### Rolling Deployment

Thay thế từng instance theo từng bước.

#### Advantages

* Giảm downtime.
* Có thể kiểm tra từng phần.

#### Risks

* Cần nhiều instance.
* Code cũ và mới có thể chạy đồng thời.
* Database phải backward-compatible.

---

### Blue-Green Deployment

Duy trì hai environment hoặc hai runtime set: active và candidate.

#### Advantages

* Chuyển đổi nhanh.
* Rollback application nhanh.
* Có thể kiểm thử candidate trước khi chuyển traffic.

#### Risks

* Chi phí hạ tầng cao hơn.
* Quản lý database phức tạp.
* Cần cơ chế chuyển traffic.

---

### Canary Deployment

Triển khai phiên bản mới cho một phần traffic.

#### Advantages

* Giảm blast radius.
* Quan sát hành vi thực tế trước khi mở rộng.

#### Risks

* Độ phức tạp cao.
* Cần routing và observability tốt.
* Không phù hợp khi chưa có traffic management platform.

---

## 11.11 Operational Strategy Assessment

| Strategy   | Complexity | Infrastructure Cost | Downtime Risk | Recovery Speed | Foundation Suitability |
| ---------- | ---------: | ------------------: | ------------: | -------------: | ---------------------: |
| Recreate   |        Low |                 Low |          High |         Medium |                   High |
| Rolling    |     Medium |              Medium |           Low |         Medium |                 Medium |
| Blue-Green |       High |                High |      Very Low |           High |          Low to Medium |
| Canary     |  Very High |                High |      Very Low |           High |                    Low |

Không có strategy nào được chọn trong TASK-009.

---

## 11.12 Pre-Deployment Checks

Trước mỗi deployment, cần xác minh tối thiểu:

* Source revision hợp lệ.
* Required checks đã đạt.
* Artifact tồn tại.
* Artifact thuộc đúng source revision.
* Artifact không bị sửa đổi.
* Environment được phép nhận deployment.
* Không có deployment khác đang chạy.
* Required approval đã đạt.
* Required secrets có sẵn.
* Infrastructure đang reachable.
* Database readiness đạt.
* Backup đã hoàn tất nếu policy yêu cầu.
* Migration risk đã được phân loại.
* Rollback hoặc recovery path khả dụng.
* Người chịu trách nhiệm đã được xác định nếu deployment rủi ro cao.

---

## 11.13 Post-Deployment Observation

Deployment không nên được đánh dấu thành công ngay khi process khởi động.

Cần có một observation period phù hợp để kiểm tra:

* Application vẫn running.
* Health endpoint ổn định.
* Không có restart loop.
* Không có error rate bất thường.
* Database connection ổn định.
* Không có migration error.
* Critical endpoint hoạt động.
* Resource usage không bất thường.
* Log không xuất hiện lỗi nghiêm trọng.

Thời lượng observation period là Open Decision.

---

## 11.14 Monitoring and Observability Requirements

Continuous Deployment cần tối thiểu ba nhóm tín hiệu:

### Logs

* Application startup log.
* Deployment log.
* Migration log.
* Error log.
* Audit log.

### Metrics

* Availability.
* Error rate.
* Request latency.
* CPU.
* Memory.
* Database connection status.
* Restart count.

### Health Signals

* Liveness.
* Readiness.
* Dependency health.
* Critical business smoke check.

Công cụ monitoring cụ thể chưa được lựa chọn.

---

## 11.15 Alerting Requirements

Cần xác định alert cho các tình huống:

* Deployment thất bại.
* Migration thất bại.
* Health check thất bại.
* Application restart liên tục.
* Error rate tăng.
* Database unavailable.
* Rollback thất bại.
* Environment không thể xác định trạng thái.

Các quyết định còn mở:

* Alert gửi tới đâu.
* Ai nhận alert.
* Mức độ severity.
* Thời gian phản hồi.
* Escalation path.
* Giờ trực hoặc on-call policy.

---

## 11.16 Incident Handling

Nếu deployment gây sự cố, quy trình tối thiểu phải gồm:

1. Xác nhận sự cố.
2. Dừng deployment đang tiếp tục.
3. Xác định blast radius.
4. Xác định application, database hay infrastructure là nguyên nhân.
5. Quyết định rollback hoặc recovery.
6. Khôi phục service.
7. Xác minh service sau recovery.
8. Lưu evidence.
9. Tạo incident record nếu policy yêu cầu.
10. Thực hiện review sau sự cố đối với sự cố nghiêm trọng.

Không được tiếp tục deployment mới vào cùng environment khi trạng thái hiện tại chưa rõ ràng.

---

## 11.17 Deployment Cancellation

Workflow cancellation phải có policy rõ ràng.

Không được mặc định cancellation luôn an toàn.

Cần phân biệt:

* Trước khi migration bắt đầu.
* Trong khi migration đang chạy.
* Sau migration nhưng trước application deployment.
* Trong application deployment.
* Trong post-deployment validation.
* Trong rollback hoặc recovery.

Một workflow bị đánh dấu “cancelled” không chứng minh environment đã trở lại trạng thái an toàn.

---

## 11.18 Configuration Management

Runtime configuration phải:

* Tách khỏi application artifact.
* Có version hoặc audit history khi khả thi.
* Không chứa secret trong repository nếu chưa có cơ chế mã hóa được phê duyệt.
* Phân tách theo environment.
* Có validation trước deployment.
* Không bị chỉnh thủ công mà không có record.

Các thay đổi configuration có thể gây rủi ro tương đương code deployment và phải được audit.

---

## 11.19 Configuration Drift

Nếu sử dụng VM, VPS hoặc host được cấu hình thủ công, cần có biện pháp phát hiện configuration drift.

Configuration drift có thể gồm:

* Khác Java version.
* Khác environment variable.
* Khác firewall rule.
* Khác service configuration.
* Khác file permission.
* Khác database connection setting.
* Thay đổi thủ công không được ghi nhận.

Phương thức kiểm soát drift phụ thuộc deployment platform và vẫn là Open Decision.

---

## 11.20 Capacity and Resource Checks

Trước deployment cần xem xét:

* Dung lượng disk.
* Bộ nhớ.
* CPU.
* Database connection capacity.
* Log storage.
* Artifact storage.
* Network access.
* Port availability.

Deployment không nên tiếp tục nếu environment thiếu resource tối thiểu.

Threshold cụ thể chưa được xác định.

---

## 11.21 Maintenance Window

Governance cần quyết định:

* Deployment có giới hạn theo thời gian không.
* Production có maintenance window không.
* Ai được phép override.
* Emergency deployment được xử lý thế nào.
* Người dùng có cần được thông báo không.

Không được mặc định deployment lúc nào cũng được phép.

---

## 11.22 Operational Evidence Requirements

Mỗi deployment phải lưu tối thiểu:

* Deployment ID.
* Environment.
* Deployment target.
* Trigger actor hoặc workflow.
* Approval record.
* Start time.
* End time.
* Source branch hoặc tag.
* Commit SHA.
* Artifact identifier.
* Artifact checksum nếu policy yêu cầu.
* Migration result.
* Application deployment result.
* Health-check result.
* Smoke-test result.
* Observation result.
* Final deployment state.
* Rollback hoặc recovery action nếu có.
* Link hoặc vị trí của log liên quan.

Evidence không được chứa secret.

---

## 11.23 Deployment State Model

Proposal khuyến nghị chuẩn hóa trạng thái deployment:

* Pending.
* Awaiting Approval.
* In Progress.
* Migration In Progress.
* Application Deploying.
* Verifying.
* Succeeded.
* Failed.
* Recovery In Progress.
* Rolled Back.
* Recovered.
* Cancelled.
* State Unknown.

`Cancelled` và `State Unknown` không được xem là trạng thái an toàn mặc định.

---

## 11.24 Operational Risks

| Risk                              | Required Mitigation                      |
| --------------------------------- | ---------------------------------------- |
| Concurrent deployment conflict    | Environment-level concurrency control    |
| Unknown runtime state             | Explicit state verification              |
| Configuration drift               | Configuration management and audit       |
| Silent failure                    | Health check, monitoring and alerting    |
| Insufficient capacity             | Pre-deployment resource checks           |
| Human deployment error            | Approval, immutable artifact and runbook |
| Untraceable deployment            | Mandatory evidence and release identity  |
| Failed cancellation               | Phase-aware cancellation and recovery    |
| Deployment outside support window | Maintenance-window policy                |
| Missing owner                     | Explicit operational ownership           |

---

## 11.25 Open Operational Decisions

Các nội dung sau vẫn là Open Decision:

* Environment đầu tiên.
* Deployment owner.
* Environment owner.
* Approval authority.
* Incident owner.
* Deployment trigger.
* Branch hoặc tag policy.
* Deployment strategy.
* Downtime allowance.
* Availability target.
* Maintenance window.
* Concurrency behavior.
* Observation period.
* Monitoring platform.
* Alert destination.
* Escalation policy.
* Configuration management mechanism.
* Infrastructure capacity threshold.
* Cancellation behavior.
* Emergency deployment policy.
* Retention period cho deployment evidence.

---

## 11.26 Recommended Operational Direction

Proposal khuyến nghị Governance Review xem xét hướng sau:

* Deployment đầu tiên nên giới hạn ở non-production, trừ khi có evidence và approval khác.
* Chỉ một deployment active cho mỗi environment.
* Artifact phải immutable và định danh rõ.
* Deployment phải có pre-check, health check, smoke test và observation period.
* Production deployment, nếu được áp dụng sau này, nên có environment protection và approval.
* Deployment thất bại phải chuyển sang trạng thái recovery rõ ràng, không chỉ kết thúc workflow.
* Không đánh dấu success trước khi post-deployment validation hoàn tất.
* Mọi deployment phải tạo evidence đủ để truy vết.
* Recreate deployment có thể được xem xét cho Foundation Phase nếu downtime được chấp nhận, nhưng chưa được chọn làm baseline.

Đây chỉ là recommended direction.

---

## 11.27 Operational Conclusion

Governance Proposal đã:

* Xác định ownership cần thiết.
* Phân tích environment isolation.
* Phân tích trigger và deployment strategy.
* Xác định concurrency control.
* Xác định pre-deployment và post-deployment checks.
* Xác định monitoring, alerting và incident handling.
* Làm rõ rủi ro của workflow cancellation.
* Xác định configuration, capacity và maintenance considerations.
* Chuẩn hóa deployment state và evidence requirements.
* Liệt kê đầy đủ Open Operational Decisions.

Không có deployment strategy, monitoring platform, environment hoặc hosting target nào được tự lựa chọn. Không có deployment hay infrastructure provisioning nào được thực hiện.



# 12. Health-check and Post-deployment Validation Strategy

## 12.1 Objective

Mục tiêu của phần này là xác định cách Continuous Deployment phải đánh giá trạng thái application sau khi triển khai.

Deployment không được xem là thành công chỉ vì:

* Workflow không báo lỗi.
* Process đã khởi động.
* Port đã được mở.
* Artifact đã được copy tới server.
* Container đang ở trạng thái running.
* HTTP endpoint trả về bất kỳ response nào.

Deployment chỉ được xem là thành công khi application:

* Khởi động đúng.
* Sẵn sàng nhận request.
* Kết nối được các dependency bắt buộc.
* Vượt qua các kiểm tra chức năng tối thiểu.
* Duy trì trạng thái ổn định trong observation period.
* Không xuất hiện lỗi nghiêm trọng trong log.

Proposal này không:

* Tạo health endpoint mới.
* Thay đổi Spring Boot Actuator configuration.
* Tạo deployment workflow.
* Thực hiện deployment.
* Lựa chọn monitoring platform.
* Tự phê duyệt health-check baseline.

---

## 12.2 Health-check Principles

### Principle 1 — Process Running Is Not Application Healthy

Một process đang chạy không chứng minh application hoạt động đúng.

Application có thể đang chạy nhưng:

* Không kết nối được database.
* Không nhận request.
* Bị lỗi cấu hình.
* Liên tục restart.
* Không tải được dependency.
* Không thực hiện được nghiệp vụ cơ bản.

Do đó, process status chỉ là một tín hiệu ban đầu.

---

### Principle 2 — Separate Liveness and Readiness

Health-check phải phân biệt:

* **Liveness:** Application process còn sống hay không.
* **Readiness:** Application đã sẵn sàng nhận và xử lý request hay chưa.

Không được dùng duy nhất một kiểm tra để đại diện cho cả hai trạng thái nếu nó không phản ánh đúng mục đích.

---

### Principle 3 — Dependency-aware Verification

Application backend phụ thuộc vào PostgreSQL.

Vì vậy, readiness verification phải xem xét khả năng:

* Kết nối database.
* Thực hiện query cần thiết.
* Sử dụng schema version phù hợp.

Tuy nhiên, health endpoint không nên làm các thao tác nặng hoặc gây tải lớn lên database.

---

### Principle 4 — Health Check Must Be Safe

Health-check phải:

* Không thay đổi dữ liệu.
* Không tạo order hoặc transaction thật.
* Không gọi payment provider thật.
* Không tiết lộ secret.
* Không trả về thông tin hạ tầng nhạy cảm.
* Không gây tải đáng kể.
* Có timeout rõ ràng.

---

### Principle 5 — Success Requires Stability

Một lần health check thành công ngay sau startup chưa đủ để xác nhận deployment ổn định.

Application phải được quan sát trong một khoảng thời gian để phát hiện:

* Restart loop.
* Memory failure.
* Connection pool exhaustion.
* Delayed configuration error.
* Database connectivity instability.
* Error rate tăng sau khi nhận traffic.

---

## 12.3 Health-check Layers

Proposal đề xuất health verification theo nhiều lớp.

### Layer 1 — Runtime Process Check

Xác minh runtime đã khởi động.

Ví dụ về tín hiệu:

* Process đang chạy.
* Service manager báo active.
* Container báo running.
* Application port được bind.

#### Purpose

Phát hiện lỗi khởi động cơ bản.

#### Limitation

Không chứng minh application sẵn sàng phục vụ.

---

### Layer 2 — Liveness Check

Xác minh application process phản hồi và không bị treo.

Liveness check nên:

* Đơn giản.
* Nhanh.
* Không phụ thuộc toàn bộ external service.
* Không thực hiện query nặng.
* Có timeout ngắn.

#### Expected Result

Application phản hồi trong thời gian cho phép và thể hiện process còn hoạt động.

---

### Layer 3 — Readiness Check

Xác minh application sẵn sàng nhận traffic.

Readiness có thể xem xét:

* Application context đã khởi tạo.
* Required configuration đã tải.
* Database connection khả dụng.
* Migration đã hoàn tất.
* Required internal component đã sẵn sàng.

Nếu readiness thất bại, application không nên nhận production traffic.

---

### Layer 4 — Dependency Health Check

Kiểm tra các dependency bắt buộc.

Đối với baseline hiện tại, dependency chính đã được xác định là:

* PostgreSQL.

Các dependency khác chỉ được thêm khi Project State hoặc business module xác nhận.

#### Important Limitation

Không được tự giả định Redis, payment gateway, email provider hoặc external API là dependency bắt buộc trong Foundation Phase.

---

### Layer 5 — Functional Smoke Test

Xác minh application thực sự xử lý được một request hợp lệ.

Smoke test nên:

* Chỉ kiểm tra chức năng tối thiểu.
* Không thay đổi dữ liệu hoặc sử dụng test data được kiểm soát.
* Không chứa credential thật trong log.
* Không thay thế integration test đầy đủ.
* Có kết quả pass/fail rõ ràng.

Ở trạng thái hiện tại, chưa có business module chính thức, nên smoke test ban đầu có thể chỉ kiểm tra endpoint kỹ thuật được phê duyệt.

Endpoint cụ thể vẫn là Open Decision.

---

### Layer 6 — Observation Period

Sau khi các kiểm tra ban đầu đạt, deployment phải được quan sát trong một khoảng thời gian.

Các tín hiệu cần quan sát:

* Process không restart.
* Health check tiếp tục đạt.
* Không có error nghiêm trọng.
* Database connection ổn định.
* Resource usage không tăng bất thường.
* Không có migration error.
* Không có repeated timeout.

Thời lượng observation period chưa được xác định.

---

## 12.4 Liveness Strategy

Liveness check phải trả lời câu hỏi:

> Application process có còn hoạt động và có khả năng phản hồi không?

### Liveness Requirements

* Không phụ thuộc vào business data.
* Không gọi external dependency không cần thiết.
* Không thực hiện database transaction phức tạp.
* Có response nhanh.
* Có timeout.
* Có retry policy giới hạn.
* Không tiết lộ configuration hoặc stack trace.

### Failure Interpretation

Một lần liveness failure không nên luôn dẫn đến rollback ngay lập tức.

Cần xác định:

* Số lần retry.
* Khoảng thời gian giữa các lần retry.
* Failure threshold.
* Hành vi restart.
* Khi nào chuyển sang recovery.

Các giá trị cụ thể là Open Decision.

---

## 12.5 Readiness Strategy

Readiness check phải trả lời câu hỏi:

> Application đã sẵn sàng nhận request từ người dùng hay chưa?

### Readiness Requirements

* Application context đã load hoàn tất.
* Database connection khả dụng.
* Required migration đã hoàn tất.
* Configuration bắt buộc hợp lệ.
* Application không ở trạng thái shutdown.
* Critical internal component sẵn sàng.

### Traffic Policy

Nếu readiness thất bại:

* Không chuyển traffic mới tới version vừa deploy.
* Không đánh dấu deployment thành công.
* Bắt đầu retry hoặc recovery theo policy.
* Không giả định liveness thành công đồng nghĩa readiness thành công.

---

## 12.6 Startup Check

Startup check phục vụ application có thời gian khởi động dài hơn liveness timeout.

Startup check cần xác định:

* Thời gian tối đa cho phép application khởi động.
* Khoảng thời gian trước lần kiểm tra đầu tiên.
* Tần suất retry.
* Tổng số lần retry.
* Điều kiện startup failure.

Nếu application không đạt startup requirement trong giới hạn:

* Deployment phải chuyển sang Failed hoặc Recovery Required.
* Không tiếp tục post-deployment validation.
* Log khởi động phải được lưu làm evidence.

---

## 12.7 Current Backend Health Capability

Baseline hiện tại ghi nhận Spring Boot Actuator đã có trong backend bootstrap.

Tuy nhiên, TASK-009 chưa có evidence đầy đủ để kết luận:

* Endpoint health nào được expose trong deployment environment.
* Liveness và readiness probe đã được bật hay chưa.
* Endpoint có yêu cầu authentication hay không.
* Chi tiết health response có được phép công khai hay không.
* PostgreSQL health indicator có nằm trong readiness hay không.
* Endpoint path chính thức là gì.

Do đó, các nội dung này vẫn là Open Decision và phải được xác minh trong task triển khai sau khi governance được phê duyệt.

---

## 12.8 Health Endpoint Security

Health endpoint cần cân bằng giữa khả năng vận hành và bảo mật.

Không nên công khai các thông tin như:

* Database URL.
* Database username.
* Internal hostname.
* Server path.
* Environment variable.
* Stack trace.
* Framework details không cần thiết.
* Secret.
* Full dependency topology.

Proposal khuyến nghị:

* Public response chỉ thể hiện trạng thái tổng quát khi cần.
* Detailed health information chỉ dành cho actor được phép.
* Deployment workflow có cơ chế truy cập phù hợp.
* Không hard-code credential vào lệnh health check.

Cơ chế authentication cụ thể là Open Decision.

---

## 12.9 Health-check Timing

Health checks phải được áp dụng tại nhiều thời điểm.

### Before Deployment

Kiểm tra environment hiện tại có đang ở trạng thái hợp lệ không.

Mục tiêu:

* Xác định baseline trước deployment.
* Phân biệt lỗi đã tồn tại với lỗi do deployment mới gây ra.
* Không triển khai vào environment đang hỏng mà không có quyết định rõ ràng.

---

### During Deployment

Kiểm tra application candidate có thể khởi động.

Mục tiêu:

* Phát hiện startup failure.
* Phát hiện configuration error.
* Phát hiện database connectivity failure.

---

### Immediately After Deployment

Chạy liveness, readiness và smoke test.

Mục tiêu:

* Xác minh candidate đáp ứng yêu cầu tối thiểu.
* Quyết định có thể tiếp tục observation hay không.

---

### During Observation Period

Lặp lại health check và quan sát tín hiệu vận hành.

Mục tiêu:

* Phát hiện lỗi không xuất hiện ngay lúc startup.
* Xác minh trạng thái ổn định.

---

### After Rollback or Recovery

Chạy lại toàn bộ kiểm tra cần thiết.

Rollback workflow hoàn tất không đồng nghĩa service đã hồi phục.

---

## 12.10 Retry and Timeout Policy

Mỗi health check cần có:

* Request timeout.
* Retry count.
* Retry interval.
* Overall deadline.
* Failure threshold.
* Classification giữa transient và persistent failure.

Không nên:

* Retry vô hạn.
* Chờ vô thời hạn.
* Đánh dấu success sau một response không hợp lệ.
* Che giấu failure bằng retry quá nhiều.
* Rollback ngay do một lỗi mạng tức thời mà chưa có policy.

Các giá trị cụ thể phụ thuộc runtime platform và vẫn là Open Decision.

---

## 12.11 Post-deployment Validation

Post-deployment validation phải rộng hơn health endpoint.

### Required Validation Areas

| Validation Area | Requirement                                 |
| --------------- | ------------------------------------------- |
| Runtime         | Process hoặc container đang hoạt động       |
| Startup         | Application startup hoàn tất                |
| Liveness        | Application phản hồi                        |
| Readiness       | Application sẵn sàng nhận traffic           |
| Database        | Kết nối PostgreSQL thành công               |
| Migration       | Schema version đúng, không có lỗi migration |
| Smoke Test      | Endpoint tối thiểu phản hồi đúng            |
| Logs            | Không có lỗi nghiêm trọng                   |
| Stability       | Không restart trong observation period      |
| Traceability    | Runtime gắn với đúng artifact và commit     |

---

## 12.12 Technical Smoke-test Strategy

Trong Foundation Phase, smoke test nên giữ phạm vi nhỏ.

Có thể kiểm tra:

* Application endpoint được phê duyệt trả đúng HTTP status.
* Response format tối thiểu hợp lệ.
* Application có thể thực hiện dependency check an toàn.
* Runtime version hoặc release identity có thể được xác minh nếu baseline cho phép.

Không được mặc định:

* Tạo user thật.
* Tạo order thật.
* Thực hiện payment thật.
* Gửi email thật.
* Ghi dữ liệu sản phẩm thật.
* Sử dụng production customer data.

Khi business modules xuất hiện, smoke test phải được mở rộng thông qua task riêng và governance phù hợp.

---

## 12.13 Database Verification

Sau deployment, database verification phải kiểm tra:

* Application kết nối được PostgreSQL.
* Connection pool không liên tục lỗi.
* Flyway migration history hợp lệ.
* Schema version phù hợp với release.
* Không có pending migration ngoài dự kiến.
* Critical database query tối thiểu hoạt động.
* Không có authentication hoặc permission error.

Không nên dùng health check để:

* Dump schema.
* Trả dữ liệu database.
* Hiển thị credential.
* Chạy query toàn bảng.
* Thực hiện destructive operation.

---

## 12.14 Log Verification

Post-deployment validation cần kiểm tra log trong giới hạn phù hợp.

Các lỗi cần chú ý:

* Application startup failure.
* Bean initialization failure.
* Database authentication failure.
* Flyway migration failure.
* Port binding failure.
* Missing environment variable.
* Repeated exception.
* Out-of-memory indication.
* Restart loop.
* Connection timeout.
* Permission denied.

Log verification không được dựa hoàn toàn vào tìm một chuỗi “Started” rồi kết luận deployment thành công.

---

## 12.15 Release Identity Verification

Deployment phải xác minh runtime đang chạy đúng release.

Cần có khả năng liên kết:

* Runtime instance.
* Commit SHA.
* Artifact identifier.
* Workflow run.
* Release hoặc tag nếu được áp dụng.
* Deployment ID.

Nếu không xác định được release identity, deployment không đáp ứng traceability requirement.

Cơ chế expose version cụ thể chưa được quyết định và có thể yêu cầu thay đổi source code trong task riêng.

---

## 12.16 Health-check Result Classification

Proposal đề xuất các trạng thái chuẩn:

* Healthy.
* Degraded.
* Unhealthy.
* Unknown.

### Healthy

Toàn bộ kiểm tra bắt buộc đạt.

### Degraded

Application còn phục vụ nhưng một dependency hoặc capability không hoạt động đầy đủ.

Việc Degraded có được chấp nhận hay không phụ thuộc dependency và environment.

### Unhealthy

Application không đáp ứng điều kiện phục vụ.

### Unknown

Không thu thập được đủ bằng chứng để xác định trạng thái.

`Unknown` không được tự động xem là Healthy.

---

## 12.17 Deployment Success Criteria

Deployment chỉ được đánh dấu `Succeeded` khi:

* Artifact đúng đã được triển khai.
* Startup check đạt.
* Liveness check đạt.
* Readiness check đạt.
* Database verification đạt.
* Migration verification đạt nếu có migration.
* Smoke test đạt.
* Không có lỗi nghiêm trọng trong log.
* Observation period đạt.
* Release identity được xác minh.
* Evidence đã được lưu.

Nếu bất kỳ kiểm tra bắt buộc nào thất bại, deployment không được đánh dấu thành công.

---

## 12.18 Failure Handling

### Startup Failure

* Dừng validation tiếp theo.
* Thu thập startup log.
* Không chuyển traffic.
* Bắt đầu rollback hoặc recovery theo policy.

### Liveness Failure

* Retry trong giới hạn.
* Nếu tiếp tục thất bại, đánh dấu candidate unhealthy.
* Bắt đầu recovery.

### Readiness Failure

* Không chuyển traffic.
* Kiểm tra database và configuration.
* Không đánh dấu deployment success.

### Smoke-test Failure

* Xác định lỗi application, routing hay dependency.
* Giữ candidate khỏi traffic nếu có thể.
* Thực hiện rollback hoặc recovery theo policy.

### Observation Failure

* Chuyển deployment sang Failed hoặc Recovery In Progress.
* Không giữ trạng thái Succeeded chỉ vì kiểm tra ban đầu đã đạt.

---

## 12.19 Required Health-check Evidence

Mỗi deployment phải lưu:

* Health-check endpoint hoặc mechanism được sử dụng.
* Thời điểm kiểm tra.
* Deployment environment.
* Deployment ID.
* Commit SHA.
* Artifact ID.
* Startup-check result.
* Liveness result.
* Readiness result.
* Dependency-check result.
* Database verification result.
* Smoke-test result.
* Observation-period result.
* Final health classification.
* Retry count nếu có.
* Failure reason nếu có.
* Recovery result nếu có.

Evidence phải được sanitise và không chứa secret.

---

## 12.20 Open Health-check Decisions

Các nội dung sau chưa đủ evidence:

* Endpoint health chính thức.
* Có tách liveness và readiness endpoint không.
* Health endpoint authentication policy.
* Public health information level.
* PostgreSQL có nằm trong readiness không.
* Startup timeout.
* Request timeout.
* Retry count.
* Retry interval.
* Failure threshold.
* Observation-period duration.
* Smoke-test endpoint.
* Smoke-test data policy.
* Health-check execution location.
* Monitoring integration.
* Degraded-state acceptance policy.
* Release identity exposure mechanism.
* Health evidence retention period.

---

## 12.21 Recommended Health-check Direction

Proposal khuyến nghị Governance Review xem xét:

* Sử dụng riêng startup, liveness và readiness semantics.
* Readiness phải phản ánh PostgreSQL connectivity và migration readiness.
* Không đưa chi tiết nhạy cảm vào public health response.
* Không đánh dấu deployment thành công ngay khi process khởi động.
* Bắt buộc có functional smoke test tối thiểu.
* Bắt buộc có observation period.
* `Unknown` không được xem là success.
* Health check phải read-only và idempotent.
* Post-rollback phải chạy lại health verification.
* Mọi kết quả phải gắn với deployment ID, artifact ID và commit SHA.

Đây là recommended direction, chưa phải baseline được phê duyệt.

---

## 12.22 Health-check Strategy Conclusion

Governance Proposal đã:

* Phân biệt process status, startup, liveness và readiness.
* Xác định dependency-aware health verification.
* Đề xuất mô hình health check nhiều lớp.
* Xác định timing, retry và timeout requirements.
* Xác định post-deployment validation.
* Xác định database, log và release identity verification.
* Chuẩn hóa health status classification.
* Xác định deployment success criteria.
* Xác định failure handling và evidence requirements.
* Liệt kê đầy đủ Open Health-check Decisions.

Không có endpoint, source code, workflow hoặc monitoring configuration nào được tạo hoặc thay đổi trong phần này.


# 13. Rollback / Recovery Strategy

## 13.1 Objective

Mục tiêu của phần này là xác định các nguyên tắc và yêu cầu governance để xử lý các tình huống deployment không thành công hoặc gây ảnh hưởng đến hệ thống.

Rollback và Recovery là hai khái niệm khác nhau.

Proposal này xác định:

* Khi nào rollback phù hợp.
* Khi nào recovery phù hợp.
* Các giới hạn của rollback.
* Các quyết định governance bắt buộc trước khi triển khai Continuous Deployment.

Proposal này không:

* Tạo rollback workflow.
* Thực hiện rollback.
* Khôi phục database.
* Khôi phục infrastructure.
* Chọn deployment strategy.
* Tự phê duyệt rollback policy.

---

# 13.2 Rollback vs Recovery

## Rollback

Rollback là việc đưa **application deployment** trở về một release trước đó.

Ví dụ:

* Deploy lại artifact trước.
* Chuyển traffic về runtime cũ.
* Khởi động lại application version trước.

Rollback không đồng nghĩa:

* Database được khôi phục.
* Dữ liệu được phục hồi.
* Infrastructure trở lại trạng thái cũ.

---

## Recovery

Recovery là toàn bộ hoạt động nhằm đưa hệ thống trở lại trạng thái vận hành chấp nhận được.

Recovery có thể bao gồm:

* Application rollback.
* Forward fix.
* Database restore.
* Data repair.
* Configuration correction.
* Infrastructure repair.
* Manual intervention.

Do đó:

> **Rollback là một kỹ thuật của Recovery, nhưng Recovery không chỉ là Rollback.**

---

# 13.3 Recovery Objectives

Recovery strategy phải hướng tới các mục tiêu sau:

| Objective           | Description                                               |
| ------------------- | --------------------------------------------------------- |
| Service Restoration | Khôi phục khả năng phục vụ của hệ thống.                  |
| Data Protection     | Tránh mất hoặc sai lệch dữ liệu.                          |
| Controlled Recovery | Không tạo thêm rủi ro trong quá trình khôi phục.          |
| Traceability        | Có thể truy vết toàn bộ quá trình rollback hoặc recovery. |
| Auditability        | Có evidence đầy đủ cho việc kiểm toán và phân tích sự cố. |

---

# 13.4 Recovery Principles

## Principle 1 — Do Not Assume Rollback Is Always Safe

Không được mặc định rằng:

* Deploy lại artifact cũ sẽ giải quyết mọi vấn đề.
* Database luôn tương thích với version cũ.
* Rollback không ảnh hưởng dữ liệu.

Application rollback có thể thất bại nếu database đã thay đổi theo hướng không tương thích.

---

## Principle 2 — Preserve Evidence Before Recovery

Trước khi rollback hoặc recovery cần thu thập:

* Deployment logs.
* Application logs.
* Migration logs.
* Health-check results.
* Runtime status.
* Error messages.
* Deployment ID.
* Artifact ID.
* Commit SHA.

Không nên rollback ngay mà không lưu bằng chứng nếu điều đó làm mất khả năng điều tra.

---

## Principle 3 — Minimize Blast Radius

Nếu deployment chỉ ảnh hưởng một environment hoặc một phiên bản candidate thì recovery nên giới hạn trong phạm vi đó.

Không mở rộng phạm vi recovery nếu chưa xác định được nguyên nhân.

---

## Principle 4 — Recovery Must Be Explicit

Mỗi bước recovery phải:

* Có người hoặc workflow chịu trách nhiệm.
* Có trạng thái rõ ràng.
* Có log.
* Có evidence.
* Có kết quả thành công hoặc thất bại.

Không thực hiện recovery bằng các thao tác thủ công không được ghi nhận.

---

## Principle 5 — Recovery Verification Is Mandatory

Recovery chỉ hoàn tất khi:

* Application hoạt động bình thường.
* Health-check đạt.
* Observation period đạt.
* Evidence được lưu.

Hoàn thành rollback không đồng nghĩa recovery đã thành công.

---

# 13.5 Failure Classification

Proposal đề xuất phân loại sự cố để lựa chọn chiến lược xử lý phù hợp.

| Failure Type           | Typical Examples                    | Preferred Initial Response                                                |
| ---------------------- | ----------------------------------- | ------------------------------------------------------------------------- |
| Startup Failure        | Application không khởi động         | Rollback hoặc cấu hình lại                                                |
| Configuration Failure  | Sai environment variable, secret    | Sửa cấu hình và xác minh lại                                              |
| Dependency Failure     | Không kết nối PostgreSQL            | Khôi phục dependency hoặc rollback nếu cần                                |
| Migration Failure      | Flyway lỗi, schema mismatch         | Dừng deployment, đánh giá trạng thái schema trước khi quyết định recovery |
| Runtime Failure        | Crash loop, OutOfMemory             | Điều tra nguyên nhân, rollback nếu phù hợp                                |
| Functional Failure     | Smoke test thất bại                 | Không chuyển traffic, rollback hoặc forward fix                           |
| Performance Regression | Latency tăng mạnh                   | Điều tra, có thể rollback nếu ảnh hưởng nghiêm trọng                      |
| Security Incident      | Credential leak, quyền truy cập sai | Cô lập sự cố, thu hồi credential, recovery theo security policy           |

---

# 13.6 Rollback Scope

Rollback có thể áp dụng cho nhiều thành phần khác nhau.

## Application Rollback

* Deploy lại artifact trước.
* Khởi động lại phiên bản trước.
* Chuyển traffic về phiên bản ổn định.

Đây là rollback phổ biến nhất.

---

## Configuration Rollback

Khôi phục:

* Runtime configuration.
* Environment variables.
* Deployment configuration.

Không được rollback bằng cách chỉnh sửa thủ công mà không có audit.

---

## Database Rollback

Database rollback **không được mặc định**.

Có thể yêu cầu:

* Restore từ backup.
* Forward-fix migration.
* Manual data repair.

Đây là Open Decision.

---

## Infrastructure Rollback

Có thể bao gồm:

* Khôi phục runtime environment.
* Khôi phục networking.
* Khôi phục service configuration.

TASK-009 không xác định chiến lược cụ thể.

---

# 13.7 Recovery Decision Matrix

| Failure               | Application Rollback              | Database Restore | Forward Fix | Manual Recovery |
| --------------------- | --------------------------------- | ---------------- | ----------- | --------------- |
| Startup Failure       | Có thể phù hợp                    | Không thường cần | Có thể      | Có thể          |
| Configuration Failure | Có thể                            | Không            | Có          | Có              |
| Migration Failure     | Có thể nhưng phải đánh giá schema | Có thể           | Có thể      | Có              |
| Runtime Failure       | Có thể                            | Không            | Có thể      | Có              |
| Functional Failure    | Có thể                            | Không            | Có thể      | Có              |
| Data Corruption       | Không đủ                          | Có thể cần       | Có thể      | Có              |
| Security Incident     | Có thể                            | Có thể           | Có          | Có              |

Bảng này chỉ mang tính định hướng, không phải policy đã được phê duyệt.

---

# 13.8 Forward Fix Strategy

Trong nhiều trường hợp, sửa lỗi và triển khai phiên bản mới (forward fix) an toàn hơn rollback.

Forward fix đặc biệt phù hợp khi:

* Database đã thay đổi không thể đảo ngược.
* Đã có dữ liệu mới được tạo.
* Rollback có nguy cơ gây mất dữ liệu.
* Lỗi chỉ nằm trong application logic.

Proposal khuyến nghị ADR đánh giá forward fix như một lựa chọn mặc định cho các trường hợp phù hợp, nhưng chưa xác định đây là baseline.

---

# 13.9 Recovery Preconditions

Trước khi bắt đầu rollback hoặc recovery cần xác minh:

* Failure đã được xác nhận.
* Deployment ID được xác định.
* Artifact ID được xác định.
* Commit SHA được xác định.
* Logs đã được lưu.
* Health-check result đã được ghi nhận.
* Migration status đã được xác minh.
* Recovery owner đã được xác định.
* Recovery strategy đã được lựa chọn.

---

# 13.10 Recovery Verification

Sau khi recovery, cần xác minh tối thiểu:

* Application startup thành công.
* Liveness đạt.
* Readiness đạt.
* Database connectivity đạt.
* Smoke test đạt.
* Không còn lỗi nghiêm trọng trong log.
* Observation period đạt.
* Release identity đúng với trạng thái mong muốn.

Nếu các bước trên chưa hoàn tất thì recovery chưa được xem là hoàn thành.

---

# 13.11 Recovery Evidence Requirements

Mỗi hoạt động rollback hoặc recovery cần lưu:

* Incident ID (nếu có).
* Deployment ID.
* Recovery ID.
* Recovery start time.
* Recovery end time.
* Recovery owner.
* Failure classification.
* Root cause (nếu đã xác định).
* Selected recovery strategy.
* Artifact hoặc release được khôi phục.
* Database action đã thực hiện.
* Health-check results.
* Validation results.
* Final recovery status.

Evidence không được chứa secret.

---

# 13.12 Open Decisions

Các nội dung sau chưa đủ evidence và phải giữ ở trạng thái Open Decision:

* Khi nào rollback tự động được phép.
* Khi nào chỉ được manual rollback.
* Có ưu tiên forward fix hay rollback.
* Database restore criteria.
* Backup retention policy.
* Recovery approval policy.
* Recovery timeout.
* Incident severity classification.
* Recovery ownership.
* Production recovery process.
* Recovery runbook.
* Post-incident review policy.

---

# 13.13 Recommended Direction

Proposal khuyến nghị Governance Review xem xét:

* Không mặc định rollback luôn là lựa chọn đầu tiên.
* Ưu tiên bảo vệ dữ liệu hơn tốc độ rollback.
* Phân biệt rõ application rollback và database recovery.
* Thu thập evidence trước khi recovery nếu không làm tăng rủi ro.
* Xác minh đầy đủ sau recovery.
* Chuẩn hóa failure classification và recovery evidence.
* Xây dựng recovery runbook riêng sau khi deployment platform được phê duyệt.

Đây chỉ là hướng khuyến nghị, không phải baseline chính thức.

---

# 13.14 Rollback / Recovery Strategy Conclusion

Governance Proposal đã:

* Phân biệt rollback và recovery.
* Xác định các nguyên tắc recovery.
* Phân loại các nhóm sự cố.
* Phân tích phạm vi rollback.
* Đề xuất decision matrix cho các loại failure.
* Đưa ra chiến lược forward fix như một phương án cần xem xét.
* Xác định yêu cầu xác minh và evidence sau recovery.
* Liệt kê đầy đủ các Open Decision.

Không có rollback workflow, recovery workflow hoặc cơ chế khôi phục cụ thể nào được tạo hoặc thay đổi trong TASK-009. Việc lựa chọn chiến lược chính thức vẫn phải được quyết định thông qua ADR hoặc Approved Change Request.


# 14. Required Evidence Policy

## 14.1 Objective

Mục tiêu của Evidence Policy là bảo đảm mọi hoạt động Continuous Deployment trong tương lai đều:

* Có bằng chứng cụ thể.
* Có thể truy vết từ source code tới runtime.
* Có thể xác minh độc lập.
* Không dựa trên lời khẳng định chung.
* Không che giấu trạng thái thất bại hoặc chưa xác định.
* Không làm lộ secret hoặc dữ liệu nhạy cảm.
* Có đủ dữ liệu để phục vụ audit, incident review và Project State validation.

Evidence Policy này không xác nhận bất kỳ deployment nào đã xảy ra.

TASK-009 chỉ định nghĩa yêu cầu evidence cho các deployment task trong tương lai.

---

## 14.2 Evidence Principles

### Principle 1 — Evidence Must Be Concrete

Evidence phải là kết quả có thể kiểm tra được, chẳng hạn:

* Workflow run.
* Build output.
* Artifact metadata.
* Deployment log.
* Health-check result.
* Migration result.
* Smoke-test result.
* Runtime release identity.
* Git status.
* File path.
* Configuration diff đã được sanitise.

Không được sử dụng các nội dung sau làm evidence duy nhất:

* “Đã deploy thành công.”
* “Hệ thống hoạt động bình thường.”
* “Không có lỗi.”
* “Pipeline đã chạy.”
* Kế hoạch triển khai.
* Checklist chưa có kết quả.
* Screenshot không thể xác định nguồn hoặc thời điểm.

---

### Principle 2 — Evidence Must Be Traceable

Mỗi deployment phải liên kết được tối thiểu:

```text
Source Repository
    ↓
Branch hoặc Tag
    ↓
Commit SHA
    ↓
CI Workflow Run
    ↓
Verified Artifact
    ↓
Deployment Run
    ↓
Target Environment
    ↓
Runtime Release Identity
```

Nếu chuỗi traceability bị đứt, deployment chưa đáp ứng evidence baseline.

---

### Principle 3 — Evidence Must Be Environment-specific

Evidence phải chỉ rõ:

* Environment nào được triển khai.
* Deployment target nào được sử dụng.
* Database nào được migration nếu có.
* Secret scope nào được dùng mà không tiết lộ giá trị.
* Approval policy nào đã áp dụng.

Không được dùng kết quả từ một environment để chứng minh environment khác đã đạt.

Ví dụ:

* Local success không chứng minh staging success.
* Staging success không chứng minh production success.
* CI migration success không chứng minh production migration success.

---

### Principle 4 — Evidence Must Preserve Failure States

Evidence phải phản ánh đúng trạng thái thực tế, bao gồm:

* Failed.
* Cancelled.
* Timed Out.
* Recovery In Progress.
* State Unknown.
* Rolled Back.
* Partially Completed.

Không được chỉ lưu phần log thành công và bỏ qua lỗi trước đó.

---

### Principle 5 — Evidence Must Be Sanitised

Evidence không được chứa:

* Password.
* Access token.
* Private key.
* Full database connection string có credential.
* Session secret.
* API secret.
* Production customer data.
* Sensitive environment variable.
* Internal credential file.

Nếu log chứa secret, log đó phải được xử lý theo security incident policy và không được đưa nguyên trạng vào Completion Report.

---

## 14.3 Evidence Categories

Required Evidence được chia thành các nhóm sau:

1. Source Evidence.
2. Build and Verification Evidence.
3. Artifact Evidence.
4. Authorization Evidence.
5. Deployment Evidence.
6. Database Migration Evidence.
7. Health-check Evidence.
8. Post-deployment Validation Evidence.
9. Operational Evidence.
10. Rollback or Recovery Evidence.
11. Negative Evidence.
12. Completion Report Evidence.

---

## 14.4 Source Evidence

Mỗi deployment cần ghi nhận:

* Repository.
* Branch hoặc tag.
* Commit SHA đầy đủ.
* Commit timestamp nếu cần.
* Triggering event.
* Triggering actor hoặc automation identity.
* Pull request hoặc release reference nếu được áp dụng.
* Required checks status.

### Minimum Acceptance Conditions

* Commit tồn tại trong repository chính thức.
* Commit SHA không mơ hồ.
* Branch hoặc tag phù hợp policy.
* Required checks đã đạt.
* Không deploy từ working tree chưa commit.
* Không deploy từ local artifact không truy vết được.

---

## 14.5 Build and Verification Evidence

Phải có bằng chứng artifact đã vượt qua các quality gates được phê duyệt.

Evidence có thể gồm:

* CI workflow run ID.
* Build result.
* Unit-test result.
* Integration-test result.
* PostgreSQL-backed verification result.
* Flyway validation result.
* Static analysis result nếu baseline yêu cầu.
* Packaging result.

### Acceptance Conditions

* Workflow kết thúc thành công.
* Không có required job bị skip ngoài policy.
* Test result không bị che bằng `continue-on-error`.
* Artifact chỉ được tạo sau khi required verification hoàn tất.
* Workflow thuộc đúng commit được deployment.

---

## 14.6 Artifact Evidence

Artifact evidence phải xác định duy nhất artifact được triển khai.

Required fields:

* Artifact name.
* Artifact identifier.
* Artifact version.
* Artifact creation time.
* Source commit SHA.
* Workflow run tạo artifact.
* Artifact size.
* Artifact checksum nếu policy áp dụng.
* Artifact retention location.
* Artifact retrieval result.

### Artifact Acceptance Conditions

* Artifact đến từ pipeline được phê duyệt.
* Artifact có thể truy vết tới commit.
* Artifact đã được verify.
* Artifact không bị build lại trong deployment stage.
* Artifact không bị thay đổi sau khi verify.
* Không sử dụng artifact chỉ có tên `latest` mà không có immutable identity.

---

## 14.7 Authorization and Approval Evidence

Nếu deployment policy yêu cầu approval, evidence phải gồm:

* Environment protection result.
* Approver identity.
* Approval timestamp.
* Deployment scope được phê duyệt.
* Artifact hoặc release được phê duyệt.
* Approval outcome.
* Exception hoặc override nếu có.

### Acceptance Conditions

* Approver có đúng quyền.
* Approval diễn ra trước bước được bảo vệ.
* Approval áp dụng đúng environment.
* Không tái sử dụng approval cho release khác nếu policy không cho phép.
* Override phải được ghi nhận rõ.

---

## 14.8 Deployment Execution Evidence

Mỗi deployment phải ghi nhận:

* Deployment ID.
* Workflow run ID.
* Environment.
* Deployment target.
* Deployment strategy.
* Start time.
* End time.
* Trigger type.
* Trigger actor.
* Source commit.
* Artifact ID.
* Previous release identity.
* Candidate release identity.
* Final status.
* Deployment logs đã sanitise.

### Required Final Status

Deployment phải kết thúc bằng một trạng thái rõ ràng:

* Succeeded.
* Failed.
* Rolled Back.
* Recovered.
* Cancelled.
* State Unknown.

Workflow kết thúc mà không xác định runtime state phải được ghi là `State Unknown`, không được ghi `Succeeded`.

---

## 14.9 Database Migration Evidence

Nếu deployment có database migration, cần lưu:

* Migration execution ID.
* Environment.
* Database identifier đã sanitise.
* Flyway version nếu policy cần.
* Migration list dự kiến.
* Migration list thực tế.
* Schema version trước.
* Schema version sau.
* Migration start time.
* Migration end time.
* Execution owner hoặc workflow.
* Approval evidence nếu migration rủi ro cao.
* Backup confirmation nếu policy yêu cầu.
* Migration result.
* Flyway history validation result.
* Recovery action nếu migration lỗi.

### Acceptance Conditions

* Migration thuộc cùng release identity với application.
* Không có migration bị sửa sau khi đã áp dụng.
* Không có checksum mismatch chưa xử lý.
* Schema version sau migration đúng dự kiến.
* Không có pending migration ngoài dự kiến.
* Application deployment không tiếp tục nếu migration policy yêu cầu dừng khi lỗi.

---

## 14.10 Health-check Evidence

Health evidence phải gồm:

* Startup-check result.
* Liveness result.
* Readiness result.
* Dependency health result.
* PostgreSQL connectivity result.
* Health-check timestamps.
* Retry count.
* Timeout result.
* Final health classification.
* Sanitised response hoặc summary.
* Health-check mechanism hoặc endpoint.

### Acceptance Conditions

* Health check chạy trên target environment.
* Health check áp dụng cho candidate release.
* `Unknown` không được xem là success.
* Một lần HTTP 200 không đủ nếu policy yêu cầu nhiều lớp kiểm tra.
* Health response không làm lộ sensitive information.

---

## 14.11 Post-deployment Validation Evidence

Required evidence gồm:

* Smoke-test command hoặc mechanism.
* Smoke-test target.
* Expected result.
* Actual result.
* HTTP status nếu phù hợp.
* Response validation summary.
* Database verification.
* Log review result.
* Observation-period start và end.
* Restart count.
* Error summary.
* Final validation status.

### Acceptance Conditions

* Smoke test kiểm tra chức năng tối thiểu thực tế.
* Smoke test không gây side effect ngoài policy.
* Observation period hoàn tất.
* Không có critical error chưa xử lý.
* Release identity runtime khớp artifact đã triển khai.

---

## 14.12 Operational Evidence

Operational evidence cần ghi nhận:

* Environment readiness check.
* Capacity check.
* Concurrency-control result.
* Configuration validation.
* Deployment window compliance.
* Monitoring status.
* Alert result nếu có.
* Incident reference nếu có.
* Environment owner.
* Deployment owner.
* Final environment state.

Các giá trị chi tiết phụ thuộc deployment platform và policy được phê duyệt.

---

## 14.13 Rollback and Recovery Evidence

Nếu rollback hoặc recovery xảy ra, phải lưu:

* Failure classification.
* Reason for recovery.
* Recovery decision.
* Recovery approver nếu cần.
* Previous release.
* Candidate release.
* Restored release.
* Database action.
* Configuration action.
* Infrastructure action.
* Recovery start và end.
* Post-recovery health result.
* Post-recovery smoke-test result.
* Observation result.
* Final state.
* Remaining risks.

### Acceptance Conditions

* Recovery không chỉ được xác nhận bằng workflow exit code.
* Runtime state sau recovery phải được kiểm tra.
* Database state phải được xác minh nếu có liên quan.
* Evidence trước recovery được bảo tồn khi khả thi.
* Failure không bị xóa khỏi history.

---

## 14.14 Negative Evidence

Governance Proposal yêu cầu bằng chứng không chỉ về việc đã làm mà còn về việc **không thực hiện các thay đổi ngoài phạm vi**.

Đối với TASK-009, negative evidence cần chứng minh:

* Không tạo deployment workflow.
* Không chỉnh sửa deployment workflow.
* Không deploy backend.
* Không deploy frontend.
* Không provision infrastructure.
* Không tạo cloud resource.
* Không tạo production database.
* Không chạy Flyway trên staging hoặc production.
* Không tạo production secret.
* Không publish Docker image.
* Không tạo container registry.
* Không tạo GitHub Release.
* Không thay đổi source code.
* Không cập nhật Official Project State.
* Continuous Deployment vẫn là `Not Started`.

### Possible Negative Evidence Sources

* Git status.
* File list.
* Git diff.
* Repository workflow directory listing.
* Cloud console absence confirmation nếu có access phù hợp.
* Deployment history.
* Environment history.
* Secret configuration review đã sanitise.
* Explicit scope declaration trong Completion Report.

Negative evidence không phải bằng chứng tuyệt đối trong mọi trường hợp, nhưng phải đủ hợp lý để chứng minh task không vượt phạm vi.

---

## 14.15 Evidence Storage and Retention

Governance cần xác định:

* Evidence lưu ở đâu.
* Ai được phép xem.
* Retention period.
* Evidence nào được lưu lâu dài.
* Evidence nào chỉ giữ theo workflow retention.
* Cách bảo vệ evidence khỏi sửa đổi.
* Cách liên kết evidence với deployment ID.
* Cách xử lý evidence chứa dữ liệu nhạy cảm.

Hiện các quyết định này là Open Decision.

Proposal khuyến nghị:

* Evidence quan trọng phải có stable reference.
* Không chỉ dựa vào log có retention ngắn.
* Completion Report nên lưu các trích xuất quan trọng.
* Artifact checksum và deployment metadata nên được lưu cùng release record nếu baseline cho phép.

---

## 14.16 Evidence Quality Levels

Proposal đề xuất phân loại evidence theo chất lượng.

### Level 0 — Assertion Only

Ví dụ:

> “Deployment thành công.”

Không được chấp nhận.

---

### Level 1 — Partial Evidence

Ví dụ:

* Một screenshot.
* Một log fragment.
* Một HTTP response.

Chỉ dùng hỗ trợ, không đủ để xác nhận toàn bộ deployment.

---

### Level 2 — Traceable Evidence

Có:

* Workflow ID.
* Commit SHA.
* Artifact ID.
* Environment.
* Validation result.

Có thể chấp nhận cho deployment thông thường nếu policy cho phép.

---

### Level 3 — Auditable Evidence

Bao gồm đầy đủ:

* Source traceability.
* Artifact integrity.
* Approval.
* Deployment log.
* Migration result.
* Health and smoke test.
* Observation period.
* Final runtime identity.
* Recovery history nếu có.

Đây là mức khuyến nghị cho production deployment.

Mức evidence bắt buộc theo từng environment vẫn là Open Decision.

---

## 14.17 Required Completion Evidence for Future Deployment Tasks

Một future deployment task chỉ được xác nhận Completed khi có tối thiểu:

1. Source repository, branch/tag và commit SHA.
2. Successful CI verification reference.
3. Immutable artifact identity.
4. Deployment workflow run reference.
5. Target environment và deployment target.
6. Approval evidence nếu policy yêu cầu.
7. Secret handling confirmation không làm lộ secret.
8. Database migration result nếu có.
9. Startup-check result.
10. Liveness result.
11. Readiness result.
12. Smoke-test result.
13. Observation-period result.
14. Runtime release identity.
15. Final deployment status.
16. Rollback hoặc recovery evidence nếu có.
17. File và workflow changes.
18. Validation matrix.
19. Exit Criteria verification.
20. Git status hoặc repository state evidence.

Thiếu một required evidence phải được ghi nhận rõ, không được thay bằng suy luận.

---

## 14.18 Required Completion Evidence for TASK-009

Đối với chính TASK-009, Completion Report phải cung cấp:

* Đường dẫn Governance Proposal.
* Nội dung đầy đủ của Governance Proposal.
* Danh sách file được tạo hoặc thay đổi.
* Problem Statement.
* Danh sách Required Baseline Decisions.
* Danh sách Open Decisions.
* Candidate Deployment Approaches.
* Trade-off analysis.
* Security considerations.
* Database migration considerations.
* Operational considerations.
* Health-check strategy.
* Rollback / Recovery strategy.
* Required Evidence Policy.
* Governance artifact recommendation.
* Draft ADR hoặc Change Request nếu được tạo.
* Validation tổng thể.
* Exit Criteria verification.
* Negative evidence.
* Xác nhận Continuous Deployment vẫn `Not Started`.
* Xác nhận Official Project State chưa được cập nhật.

---

## 14.19 Evidence Rejection Conditions

Evidence phải bị từ chối nếu:

* Không xác định được nguồn.
* Không xác định được environment.
* Không xác định được commit hoặc artifact.
* Log bị cắt mất phần kết quả quan trọng.
* Screenshot không có context cần thiết.
* Evidence mâu thuẫn với Completion Report.
* Evidence chứa secret chưa được xử lý.
* Workflow dùng `continue-on-error` để che failure.
* Required check bị skip trái policy.
* Runtime identity không khớp artifact.
* Deployment state là `Unknown`.
* Evidence đến từ môi trường khác.
* Evidence chỉ là kế hoạch hoặc khẳng định.

---

## 14.20 Evidence Consistency Rules

Các evidence phải nhất quán với nhau.

Ví dụ:

* Commit SHA trong CI phải khớp artifact.
* Artifact ID phải khớp deployment.
* Deployment environment phải khớp health check.
* Runtime release identity phải khớp artifact.
* Migration version phải khớp release.
* Completion time phải sau validation time.
* Rollback evidence phải phản ánh candidate release thất bại.
* Final status phải khớp log và health result.

Nếu evidence mâu thuẫn, task chưa đủ điều kiện Completed cho tới khi mâu thuẫn được giải quyết.

---

## 14.21 Open Evidence Decisions

Các quyết định sau chưa đủ baseline:

* Evidence storage location.
* Evidence retention period.
* Required evidence level theo environment.
* Artifact checksum policy.
* Artifact signing requirement.
* Release manifest requirement.
* Log retention.
* Screenshot acceptance policy.
* Deployment record format.
* Incident evidence retention.
* Production audit requirement.
* Evidence access-control policy.
* Evidence immutability mechanism.

---

## 14.22 Recommended Evidence Direction

Proposal khuyến nghị Governance Review xem xét:

* Mỗi deployment phải có deployment ID duy nhất.
* Commit SHA và artifact ID là bắt buộc.
* Không deploy artifact không truy vết được.
* Runtime release identity phải được xác minh.
* Production nên yêu cầu Level 3 — Auditable Evidence.
* Non-production ít nhất cần Level 2 — Traceable Evidence.
* Failure, cancellation và recovery phải được giữ trong history.
* Evidence phải sanitise trước khi đưa vào Completion Report.
* Negative evidence phải được yêu cầu cho governance-only task.
* `State Unknown` không được dùng để xác nhận Completed.

Đây là recommended direction, chưa phải baseline chính thức.

---

## 14.23 Required Evidence Policy Conclusion

Governance Proposal đã:

* Xác định nguyên tắc evidence.
* Phân loại các nhóm evidence.
* Xác định source, artifact, deployment, migration, health và recovery evidence.
* Xác định negative evidence.
* Đề xuất evidence quality levels.
* Xác định required evidence cho future deployment task.
* Xác định riêng required evidence cho TASK-009.
* Xác định điều kiện từ chối evidence.
* Xác định consistency rules.
* Liệt kê các Open Evidence Decisions.

Evidence Policy này không xác nhận có deployment nào đã diễn ra và không làm thay đổi Continuous Deployment Baseline.


# 15. Governance Artifact Recommendation

## 15.1 Objective

Mục tiêu của phần này là xác định loại governance artifact phù hợp để phê duyệt Continuous Deployment Baseline sau khi TASK-009 hoàn thành.

Hai loại artifact được xem xét:

* Architecture Decision Record — ADR.
* Approved Change Request — CR.

Việc lựa chọn phải dựa trên bản chất của quyết định, không dựa trên quy mô tài liệu hoặc mức độ dễ triển khai.

---

## 15.2 Decision Criteria

Governance artifact phải được đánh giá theo các tiêu chí sau:

| Criterion                | ADR phù hợp khi                                      | Change Request phù hợp khi                                          |
| ------------------------ | ---------------------------------------------------- | ------------------------------------------------------------------- |
| Decision nature          | Thiết lập quyết định kiến trúc hoặc baseline dài hạn | Thay đổi đã xác định trong baseline hiện tại                        |
| Alternatives             | Có nhiều phương án và trade-off đáng kể              | Phương án đã được xác định hoặc gần như không có lựa chọn kiến trúc |
| Cross-cutting impact     | Ảnh hưởng nhiều lĩnh vực                             | Ảnh hưởng giới hạn và cục bộ                                        |
| Long-term consequence    | Khó thay đổi hoặc có chi phí thay đổi lớn            | Có thể đảo ngược hoặc thay đổi tương đối dễ                         |
| Governance need          | Cần rationale, alternatives và consequences          | Cần phê duyệt phạm vi, tác động và kế hoạch thay đổi                |
| Baseline status          | Chưa có baseline chính thức                          | Đã có baseline và cần sửa đổi                                       |
| Implementation readiness | Quyết định phải được chốt trước implementation       | Implementation details đã rõ                                        |

---

## 15.3 Characteristics of TASK-009

Continuous Deployment Baseline bao gồm các quyết định như:

* Deployment scope đầu tiên.
* Deployment target.
* Hosting model.
* Environment model.
* Trigger policy.
* Branch hoặc tag policy.
* Approval policy.
* Secret-management policy.
* Workflow permission model.
* Database migration strategy.
* Health-check strategy.
* Rollback hoặc recovery strategy.
* Deployment evidence requirements.
* Operational ownership.
* Security controls.

Các quyết định này:

* Ảnh hưởng trực tiếp tới Engineering Baseline.
* Ảnh hưởng tới Security Baseline.
* Ảnh hưởng tới Operations Baseline.
* Có nhiều phương án hợp lệ với trade-off khác nhau.
* Tác động tới tất cả deployment task trong tương lai.
* Khó thay đổi sau khi infrastructure và workflow đã được triển khai.
* Chưa có baseline hiện tại để sửa đổi.

Do đó, TASK-009 không chỉ đề xuất một thay đổi cục bộ.

---

## 15.4 ADR Assessment

ADR phù hợp vì Continuous Deployment Baseline cần ghi nhận:

* Context.
* Problem.
* Decision drivers.
* Considered alternatives.
* Selected approach.
* Rejected alternatives.
* Security consequences.
* Operational consequences.
* Migration consequences.
* Risks.
* Open follow-up actions.

Một ADR được phê duyệt sẽ trở thành nguồn quyết định chính thức cho:

* Current Task Manager khi tạo deployment task.
* Development Workspace khi triển khai.
* Project State Manager khi cập nhật Decision Register.
* Các task vận hành, security và recovery sau này.

---

## 15.5 Change Request Assessment

Approved Change Request thường phù hợp hơn khi:

* Baseline hiện tại đã tồn tại.
* Một thay đổi cụ thể cần được áp dụng.
* Technology hoặc architecture decision đã được chốt.
* Phạm vi thay đổi tương đối rõ.
* Không cần lựa chọn giữa nhiều architecture alternatives.

Ví dụ phù hợp với Change Request sau này:

* Thay đổi deployment trigger từ manual sang tag-based.
* Thêm staging environment thứ hai.
* Thay đổi secret rotation interval.
* Chuyển health-check timeout từ một giá trị đã duyệt sang giá trị mới.
* Bổ sung frontend deployment vào một baseline backend deployment đã tồn tại.
* Chuyển từ deployment strategy đã duyệt sang strategy khác nếu không làm thay đổi kiến trúc tổng thể đáng kể.

TASK-009 hiện không đáp ứng đặc điểm này vì chưa có Continuous Deployment Baseline chính thức để thay đổi.

---

## 15.6 Comparative Decision

| Evaluation Area                                       |         ADR |                      Change Request |
| ----------------------------------------------------- | ----------: | ----------------------------------: |
| Establishes new baseline                              |  Strong fit |                            Weak fit |
| Captures multiple alternatives                        |  Strong fit |                          Medium fit |
| Records long-term rationale                           |  Strong fit |                          Medium fit |
| Covers Engineering, Security and Operations           |  Strong fit |                            Weak fit |
| Suitable before implementation                        |  Strong fit |                          Medium fit |
| Suitable for scoped modification to existing baseline |  Medium fit |                          Strong fit |
| Suitable for TASK-009                                 | Recommended | Not recommended as primary artifact |

---

## 15.7 Recommended Governance Artifact

### Recommendation

**TASK-009 should result in a Draft Architecture Decision Record.**

Proposed artifact type:

```text
Architecture Decision Record
```

Proposed working title:

```text
ADR-002 — Continuous Deployment Baseline
```

ADR number is provisional and must be confirmed by Project State Manager according to the official Decision Register.

---

## 15.8 Recommendation Rationale

ADR is recommended because:

1. Continuous Deployment has no approved baseline yet.

2. The decision affects multiple governance domains:

   * Architecture.
   * Engineering.
   * Security.
   * Database operations.
   * Runtime operations.
   * Incident recovery.

3. Multiple viable approaches exist:

   * VM or VPS.
   * Container host.
   * Managed platform.
   * Kubernetes.
   * Startup migration.
   * Dedicated migration job.
   * Manual deployment.
   * Automatic deployment.
   * Recreate, rolling, blue-green or canary deployment.

4. The selected decisions will constrain future implementation tasks.

5. The cost of changing the chosen approach later may be significant.

6. The rationale and rejected alternatives must remain traceable.

7. The task explicitly requires determining ADR or Approved Change Request before deployment work can begin.

---

## 15.9 Why Change Request Is Not Recommended as the Primary Artifact

Change Request is not recommended as the primary artifact because:

* There is no existing Continuous Deployment Baseline being modified.
* The deployment target has not been selected.
* The environment model has not been selected.
* The security and migration policies have not been approved.
* The proposal contains architecture-level alternatives.
* The decision will establish, rather than modify, a baseline.

Using only a Change Request could fail to preserve:

* Decision rationale.
* Alternatives considered.
* Long-term consequences.
* Architectural constraints.
* Rejected approaches.

---

## 15.10 Current Draft Readiness

Although ADR is the recommended artifact type, the current proposal does not contain enough approved evidence to produce a decision-ready ADR with a final `Accepted` decision.

The following critical decisions remain open:

* Backend, frontend or both in the first deployment.
* Non-production or production.
* Deployment platform.
* Hosting model.
* First target environment.
* Runtime topology.
* Deployment trigger.
* Branch or tag policy.
* Approval policy.
* Secret-management mechanism.
* Workflow authentication mechanism.
* Database migration execution model.
* Deployment strategy.
* Availability and downtime requirements.
* Monitoring and alerting mechanism.
* Recovery ownership.

Therefore, the ADR may currently be prepared only as:

```text
Draft / Proposed
```

It must not be marked:

```text
Accepted
Approved
Effective
```

---

## 15.11 Recommended ADR Preparation Status

The Governance Proposal supports creating an ADR skeleton containing:

* Context.
* Problem Statement.
* Decision Drivers.
* Required Decisions.
* Considered Alternatives.
* Trade-off Analysis.
* Security Considerations.
* Database Migration Considerations.
* Operational Considerations.
* Health-check Strategy.
* Rollback and Recovery Strategy.
* Evidence Requirements.
* Open Decisions.

The following sections must remain incomplete until governance review resolves the Open Decisions:

* Decision.
* Selected Approach.
* Final Deployment Scope.
* Final Target Environment.
* Final Platform.
* Final Trigger Policy.
* Final Secret Policy.
* Final Migration Policy.
* Final Recovery Policy.
* Consequences.
* Implementation Constraints.

---

## 15.12 Recommended Governance Flow

The recommended governance sequence is:

```text
TASK-009 Governance Proposal
        ↓
Project State Manager Review
        ↓
Resolve Open Decisions
        ↓
Prepare or finalize ADR-002
        ↓
ADR Review
        ↓
ADR Approved or Rejected
        ↓
Project State Manager updates Decision Register
        ↓
Project State Manager updates Baseline References
        ↓
Official Project State new version
        ↓
Current Task Manager creates deployment implementation task
```

No deployment task should be created before the relevant ADR is approved and reflected in Official Project State.

---

## 15.13 When a Change Request May Still Be Required

An ADR approval may later require a separate Change Request when implementation affects an existing approved baseline.

Examples:

* Adding a new hosting provider dependency.
* Modifying repository structure.
* Adding deployment workflow files.
* Creating environment configuration.
* Adding infrastructure-as-code.
* Changing source code to expose release identity.
* Updating Actuator health configuration.
* Introducing a container runtime.
* Changing database runtime permissions.

Whether a separate Change Request is required depends on the Project State Manager's Governance and Change Policy.

Therefore:

```text
ADR defines the architectural decision.
Change Request may authorize specific baseline modifications needed to implement it.
```

The two artifact types are complementary rather than mutually exclusive in all future cases.

---

## 15.14 Decision Ownership

The Development Workspace may:

* Recommend ADR.
* Prepare a draft.
* Document alternatives and trade-offs.
* Identify missing decisions.
* Provide implementation impact analysis.

The Development Workspace must not:

* Approve the ADR.
* Assign an official ADR number without confirmation.
* Update the Decision Register.
* Update Baseline References.
* Mark the decision Effective.
* Treat the proposed approach as approved.
* Begin deployment implementation based only on the draft.

Final authority belongs to the governance role defined by Official Project State, primarily Project State Manager and any required approver.

---

## 15.15 Proposed Artifact Metadata

The following metadata may be used for a draft:

```text
Artifact Type: Architecture Decision Record
Provisional ID: ADR-002
Title: Continuous Deployment Baseline
Status: Proposed
Source Task: TASK-009
Source Project State: Official Project State v1.1.3
Decision Scope: Continuous Deployment
Affected Baselines:
- Engineering
- Security
- Operations
- Database Operations
Author: Development Workspace
Reviewer: Project State Manager
Approver: Project State Manager or designated governance authority
```

`ADR-002` is provisional and must not be treated as officially assigned until confirmed.

---

## 15.16 ADR Minimum Approval Conditions

The ADR should not be approved until it defines at least:

1. Initial deployment scope.
2. First deployment environment.
3. Deployment target and hosting model.
4. Artifact source and traceability rule.
5. Trigger policy.
6. Branch or tag policy.
7. Approval policy.
8. Secret-management policy.
9. Workflow permissions.
10. Database migration policy.
11. Health-check and validation policy.
12. Rollback or recovery policy.
13. Operational ownership.
14. Required deployment evidence.
15. Security consequences.
16. Implementation constraints.
17. Accepted risks.
18. Rejected alternatives.

---

## 15.17 Acceptance Outcomes

Governance review may result in one of the following:

### Accepted

All required baseline decisions are resolved and approved.

Result:

* ADR enters the Decision Register.
* Baseline References may be updated.
* A deployment implementation task may become eligible.

---

### Accepted with Follow-up Actions

Core baseline decisions are approved, but non-blocking actions remain.

Result:

* Implementation may proceed only within explicitly approved boundaries.
* Follow-up tasks must be recorded.

---

### Deferred

Evidence is insufficient or prerequisites are missing.

Result:

* Open Decisions remain active.
* No deployment implementation task is created.

---

### Rejected

The proposal or selected approach is not acceptable.

Result:

* Rejection rationale is recorded.
* Alternatives may be reassessed.
* Continuous Deployment remains `Not Started`.

---

## 15.18 Governance Artifact Recommendation

Final recommendation:

```text
Primary Governance Artifact:
Draft ADR — Continuous Deployment Baseline

Current Status:
Proposed / Not Approved

Change Request:
Not recommended as the primary artifact for TASK-009.
May be required later for implementation-specific baseline changes.
```

---

## 15.19 Governance Artifact Conclusion

The evaluation has determined that:

* TASK-009 establishes a new cross-cutting baseline.
* It is not merely modifying an existing deployment configuration.
* The decision has long-term architecture, security and operations consequences.
* Multiple alternatives and significant trade-offs exist.
* ADR is therefore the appropriate primary governance artifact.
* The current evidence is sufficient to recommend and prepare a Draft ADR.
* The current evidence is not sufficient to finalize the Selected Approach or mark the ADR Approved.
* All unresolved items must remain Open Decisions.
* No deployment implementation may begin until the ADR is approved and incorporated into Official Project State.

No ADR has been self-approved, no Decision Register has been changed, and Continuous Deployment remains `Not Started`.


