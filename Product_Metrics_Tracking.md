# Báo cáo Thực hành: Product Metrics & Tracking (Lab Day 20/23)

## 00 — Phạm vi
1. **Dự án:** Ứng dụng AI Study Buddy & Co-learning Matcher.
2. **Persona:** Sinh viên đại học đang trong giai đoạn ôn thi hoặc tự học các môn khó, dễ bị xao nhãng.
3. **Core job:** *"Tôi rất dễ bị phân tâm bởi mạng xã hội và hay bỏ cuộc giữa chừng khi tự học ở nhà, tôi cần một luồng áp lực tích cực và sự giám sát để có thể ngồi học nghiêm túc suốt 45 phút."*

---

## 01 — Core Action (Phase 1)
**Core Action Card**
- **Target user:** Sinh viên đang tự học buổi tối.
- **Core job:** Không bỏ dở việc học, giữ kỷ luật ngồi học đủ thời gian.
- **Core action:** Hoàn thành trọn vẹn một phiên học tập (Study Session) có AI giám sát kéo dài tối thiểu 45 phút.
- **Object:** Phiên học (Session).
- **Preconditions:** User đã chọn môn học và bấm nút "Bắt đầu phiên học".
- **Completion rule:** Đồng hồ đếm ngược hết 45 phút mà user không rời khỏi tab/màn hình, và hoàn thành mini-quiz cuối phiên.
- **Core value:** Vượt qua được sự lười biếng, thực sự nạp được kiến thức trong 45 phút đó.
- **Evidence of value:** Xem báo cáo kết quả phiên học và nhận điểm kinh nghiệm (XP) xác nhận sự tiến bộ.
- **Candidate event:** `session_completed`

**Tự kiểm 5 tiêu chí:** 
(1) Gần core value (Pass) | (2) Có thể lặp lại (Pass) | (3) Có thể quan sát được qua hệ thống (Pass) | (4) Tăng = sản phẩm tốt hơn (Pass) | (5) Team có thể tác động bằng UI/UX (Pass).

---

## 02 — Nature & Cadence (Phase 2)
**Action Nature Card**
- **Actor:** User
- **Intent:** Muốn hoàn thành bài tập về nhà / ôn thi nhưng thiếu kỷ luật.
- **Trigger:** Chủ động (Tự nhớ lịch học) hoặc Hệ thống (Nhắc nhở: "Đã 20h, đối tác AI của bạn đang đợi").
- **Effort:** Cao (45 phút tập trung cao độ).
- **Value timing:** Tích lũy (Học nhiều phiên mới thấy rõ kết quả thi cử).
- **State:** Tích lũy số phiên đã học, điểm XP, chuỗi ngày học (Streak).
- **Dependency:** Phụ thuộc vào thời gian rảnh buổi tối của user.
- **Repeat condition:** User có lộ trình học kéo dài nhiều tuần/tháng.

**Kết luận cadence:**
> Đối với **tiến trình tích lũy**, core action **hoàn thành phiên học 45 phút** thường xuất hiện **2-3 lần/tuần** vì **sinh viên thường có lịch tự học cố định vào buổi tối các ngày trong tuần**. Do đó, nhịp đo phù hợp là **Weekly** ở cấp **User**.

---

## 03 — Metric System & 04 — Retention Definition (Phase 3)

### 1. Activation Metric
- **Start event:** `account_created` (User đăng ký tài khoản).
- **Activation event:** `session_completed` (Hoàn thành phiên học 45 phút đầu tiên).
- **Time window:** Trong vòng **3 ngày** kể từ khi tạo tài khoản (Nếu qua 3 ngày không học thử, tỷ lệ churn cực cao).

### 2. Engagement Metric
- **Frequency:** Số lượng `session_completed` mỗi tuần trên mỗi user.

### 3. Retention Definition
- **Unit:** User
- **Cohort entry:** `account_created`
- **Return event:** `session_completed`
- **Window:** Weekly (Tính theo tuần)
- **Threshold:** Tối thiểu 1 lần / tuần.
- **Segment:** Sinh viên năm 1 - 3.

### 4. North Star Metric (NSM) + Leading & Counter
- **North Star Metric:** Số lượng phiên học > 45 phút có điểm quiz > 80% mỗi tuần. *(Unit: Phiên học | Quality threshold: Quiz > 80% | Frequency: Mỗi tuần)*.
- **Leading indicators:** 
  - `session_started`: Vì user bấm bắt đầu thì xác suất hoàn thành là >70%.
  - `study_goal_created`: Vì user có set mục tiêu rõ ràng từ đầu tuần thì khả năng cao sẽ vào học.
- **Counter-metric:** **Tỷ lệ `session_cancelled` (bỏ dở giữa chừng)**. Lý do: Nếu số session hoàn thành tăng nhưng số session hủy cũng tăng vọt, điều đó có nghĩa AI đang tạo áp lực quá mức hoặc app bị lỗi, khiến user ức chế.

---

## 05 — Product Loop (Phase 4)
- **Vòng lặp (Habit Loop):**
  Natural trigger (Đến 20h tối) ➔ Core action (`session_completed`) ➔ Immediate value (Cảm thấy tự hào vì đã học xong) ➔ Saved state (Nhận điểm XP, Streak tăng thêm 1 ngày) ➔ Next natural trigger (Tối mai lại muốn vào học để không bị mất Streak) ➔ Core action.
- **Metric hypothesis:**
  > Nếu loop này hoạt động, metric **Weekly Retention** sẽ thay đổi theo hướng **tăng từ 20% lên 40%** trong **3 tháng**, vì **user có động lực duy trì chuỗi học tập (streak) để bảo vệ điểm XP đã đầu tư**.

---

## 06 — Tracking Nhanh (Phase 4)
| Tên event | Ý nghĩa | Thời điểm ghi nhận | Metric sử dụng |
| :--- | :--- | :--- | :--- |
| `session_started` | User bắt đầu phiên học | Khi user bấm nút "Start" và timer bắt đầu chạy | Leading indicator |
| `session_cancelled` | User bỏ cuộc giữa chừng | Khi bấm "End early" hoặc đóng trình duyệt trước 45p | Counter-metric |
| `session_completed` | User hoàn thành trọn vẹn 45 phút | Khi đồng hồ về 0 và chuyển sang màn hình Report | Activation, Retention, NSM |
| `quiz_submitted` | User hoàn thành bài test nhỏ để AI đánh giá | Khi bấm "Submit" kết quả câu hỏi cuối phiên | NSM (Quality threshold) |

**Tiêu chí nghiệm thu (Acceptance Criteria):**
1. Với mỗi `session_id`, hệ thống chỉ bắn event `session_completed` **một lần duy nhất** khi state chuyển từ `running` sang `done`. Tải lại trang (F5) ở màn hình xem kết quả không được phép bắn thêm event `session_completed`.
2. Event `session_completed` chỉ được ghi nhận nếu `duration_seconds` >= 2700 (45 phút). Không bắn ngay khi user mới bấm nút Start.

---

## Tự soi lỗi (Phase 5)
- [x] Core action (`session_completed`) là hành vi nhận value thực sự, không phải thao tác UI (như `click_button`).
- [x] Activation không lấy `login` mà lấy `session_completed` đầu tiên.
- [x] Retention chốt là Weekly (Phù hợp với lịch học sinh viên), không ép Daily.
- [x] Có Counter-metric (`session_cancelled`) để chống việc đếm số liệu ảo.
- [x] Các event ở bảng Tracking đều map trực tiếp về hệ thống Metric ở Phase 3.
