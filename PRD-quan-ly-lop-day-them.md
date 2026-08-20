# PRD: Phần mềm Quản lý Lớp Dạy Thêm Tiếng Anh Tiểu Học

> Tài liệu này dùng làm brief đầu vào cho Claude Code để phát triển ứng dụng.
> Ngôn ngữ giao diện: Tiếng Việt. Đối tượng dùng: 1 giáo viên (chủ lớp), không cần multi-tenant.

---

## 1. Bối cảnh & Người dùng

**Persona:** Giáo viên tiếng Anh cấp 1, dạy thêm tại nhà/lớp riêng.

- Dạy học sinh lớp 1–5, chia theo nhiều lớp nhỏ, mỗi lớp **5–12 học sinh**.
- Lịch dạy dày: **1–2 ca/ngày**, mỗi ca **~1h30**.
- Thu học phí **theo buổi** (~40.000đ/buổi), không phải theo tháng cố định.
- Có máy in màu, thỉnh thoảng in tài liệu phát cho học sinh.

**Nỗi đau hiện tại (pain points):**
1. Quên học sinh nào đã đóng tiền / chưa đóng tiền → thất thoát học phí, khó đối chiếu.
2. Học sinh học nhiều buổi rồi mới đóng gộp → khó nhớ nợ bao nhiêu buổi.
3. Không có công cụ điểm danh nhanh, dễ dùng trên điện thoại giữa các ca dạy sát nhau.
4. Soạn giáo án/kế hoạch bài dạy tốn thời gian, không có ngân hàng bài giảng tái sử dụng.
5. Quản lý tài liệu in ấn rời rạc, không biết lớp nào đã phát tài liệu gì.
6. Không có cái nhìn tổng quan (bao nhiêu học sinh, doanh thu, công nợ) theo thời gian thực.

---

## 2. Mục tiêu sản phẩm

Xây dựng một ứng dụng **web-app ưu tiên mobile (mobile-first / PWA)**, giúp giáo viên:

1. Quản lý lớp học & học sinh tập trung.
2. Điểm danh theo buổi + tự động tính học phí, theo dõi công nợ theo từng học sinh.
3. Ghi nhận thanh toán linh hoạt (đóng lẻ từng buổi hoặc đóng gộp nhiều buổi).
4. Hỗ trợ soạn kế hoạch bài dạy/giáo án nhanh (có thể tích hợp AI gợi ý nội dung).
5. Lưu trữ & quản lý tài liệu dạy học, theo dõi tài liệu đã in/phát cho lớp nào.
6. Cung cấp dashboard tổng quan: doanh thu, công nợ, lịch dạy trong ngày/tuần.

**Ngoài phạm vi (out of scope) ở bản đầu:** thanh toán online (chuyển khoản tự động đối soát), đa giáo viên, quản lý phụ huynh qua app riêng.

---

## 3. Phạm vi tính năng

### 3.1 Quản lý Lớp học & Học sinh
- CRUD **Lớp học**: tên lớp, khối lớp (1–5), lịch học cố định (thứ + giờ bắt đầu/kết thúc), học phí/buổi mặc định, sĩ số hiện tại.
- CRUD **Học sinh**: họ tên, lớp trường (1–5), lớp học thêm đang tham gia (có thể học >1 lớp), SĐT phụ huynh, ngày bắt đầu học, trạng thái (đang học/tạm nghỉ/nghỉ hẳn).
- Gán học sinh vào 1 hoặc nhiều lớp học thêm.

### 3.2 Điểm danh & Học phí (tính năng lõi)
- **Màn hình điểm danh nhanh theo buổi**: chọn lớp → chọn ngày (mặc định hôm nay) → danh sách học sinh với toggle Có mặt/Vắng, thao tác 1 chạm, tối ưu cho điện thoại (dùng được ngay giữa 2 ca dạy trong <1 phút).
- Mỗi lượt điểm danh "Có mặt" tự động tạo **1 bản ghi buổi học nợ học phí** (session_fee) gắn với học sinh đó, mặc định = học phí/buổi của lớp (có thể sửa tay nếu buổi đó thu khác).
- **Sổ công nợ theo học sinh**: hiển thị số buổi đã học chưa đóng tiền, tổng tiền nợ, lịch sử từng buổi (ngày học, đã đóng/chưa).
- **Ghi nhận thanh toán**: giáo viên chọn học sinh → nhập số tiền đóng → hệ thống tự động trừ dần vào các buổi nợ cũ nhất trước (FIFO), hoặc cho phép chọn thủ công buổi nào được thanh toán. Hỗ trợ đóng gộp nhiều buổi cùng lúc (vd đóng 200k = 5 buổi x 40k).
- Trạng thái mỗi buổi: `Chưa đóng` / `Đã đóng` / `Đóng một phần` (nếu học phí buổi thay đổi).
- Cảnh báo/nhắc: học sinh nợ quá N buổi (số N cấu hình được) sẽ được đánh dấu nổi bật (badge đỏ) trên danh sách.
- Lịch sử thanh toán: xem lại từng lần đóng tiền, có thể sửa/xoá nếu ghi nhầm.

### 3.3 Soạn Kế hoạch Bài dạy / Giáo án (tiết kiệm thời gian)
- **Ngân hàng bài giảng**: tạo/lưu các bài học theo chủ đề - từ vựng, ngữ pháp, kỹ năng (nghe/nói/đọc/viết), gắn theo khối lớp (1–5).
- **Kế hoạch buổi dạy**: với mỗi buổi trong lịch, chọn nhanh 1 bài giảng có sẵn từ ngân hàng, hoặc tạo giáo án mới (mục tiêu bài học, hoạt động, thời gian, tài liệu kèm theo).
- **Tạo giáo án bằng AI**: form nhập nhanh (chủ đề, khối lớp, thời lượng, kỹ năng trọng tâm) → gọi AI sinh outline giáo án (khởi động – nội dung chính – trò chơi/luyện tập – tổng kết) → giáo viên chỉnh sửa và lưu vào ngân hàng bài giảng để tái sử dụng cho các lớp khác/năm sau.
- Nhân bản (duplicate) giáo án đã có để dùng lại cho lớp khác, tiết kiệm công soạn lại.

### 3.4 Quản lý Tài liệu & In ấn
- Upload/lưu file tài liệu (PDF, hình ảnh, docx) gắn với bài giảng hoặc lớp cụ thể.
- Đánh dấu "đã in" / "chưa in" cho từng tài liệu, ghi số lượng bản cần in theo sĩ số lớp.
- Lịch sử: tài liệu nào đã phát cho lớp nào, ngày nào — tránh in trùng/in thiếu.

### 3.5 Dashboard Tổng quan
- **Lịch dạy hôm nay/tuần này**: các ca dạy sắp tới, giờ, lớp, sĩ số.
- **Doanh thu**: tổng thu theo ngày/tuần/tháng, theo từng lớp.
- **Công nợ tổng**: tổng số tiền học sinh còn nợ toàn hệ thống, danh sách học sinh nợ nhiều nhất.
- **Thống kê lớp**: sĩ số theo lớp, tỉ lệ điểm danh (chuyên cần) từng học sinh.

### 3.6 Nhắc nhở (nice-to-have, có thể phase 2)
- Thông báo đẩy (push notification – nếu làm PWA) nhắc giáo viên trước giờ dạy 15 phút.
- Gợi ý cuối buổi: "Hôm nay X đã học buổi thứ N chưa đóng tiền, tổng nợ Y đồng."

---

## 4. Mô hình dữ liệu đề xuất (Data Model)

```
Class (Lop)
- id, name, grade (1-5), default_fee, schedule (weekday, start_time, end_time), status

Student (HocSinh)
- id, full_name, school_grade (1-5), parent_phone, start_date, status, note

ClassStudent (bảng nối - học sinh thuộc lớp nào)
- id, class_id, student_id, joined_date

Session (BuoiHoc - 1 buổi dạy thực tế của 1 lớp)
- id, class_id, date, lesson_plan_id (nullable), note

Attendance (DiemDanh - gắn học sinh với 1 buổi cụ thể)
- id, session_id, student_id, status (present/absent), fee_amount, payment_status (unpaid/paid/partial)

Payment (ThanhToan)
- id, student_id, amount, paid_date, note
- PaymentAllocation: id, payment_id, attendance_id, amount_allocated  (map tiền đóng vào từng buổi cụ thể)

LessonPlan (GiaoAn / NganHangBaiGiang)
- id, title, grade (1-5), skill_focus, objectives, activities (rich text/json), duration_minutes, created_by_ai (bool), created_at

Document (TaiLieu)
- id, lesson_plan_id (nullable), class_id (nullable), file_url, file_type, printed (bool), print_count, last_printed_date
```

---

## 5. User Flows chính

**Flow 1 – Điểm danh cuối buổi dạy:**
Mở app → chọn lớp đang dạy (hoặc app tự gợi ý lớp theo giờ hiện tại) → danh sách học sinh hiện sẵn, tick "có mặt" từng em (mặc định tất cả có mặt, bỏ tick em vắng) → Lưu → hệ thống tự tạo công nợ buổi học cho các em có mặt.

**Flow 2 – Ghi nhận đóng tiền:**
Vào hồ sơ học sinh → thấy số buổi nợ + tổng tiền → bấm "Ghi nhận đóng tiền" → nhập số tiền → hệ thống tự trừ vào buổi nợ cũ nhất → xác nhận.

**Flow 3 – Soạn giáo án nhanh bằng AI:**
Vào "Ngân hàng bài giảng" → Tạo mới → chọn khối lớp, chủ đề, kỹ năng, thời lượng → bấm "Gợi ý bằng AI" → nhận outline → chỉnh sửa → Lưu → gắn vào buổi dạy tương ứng trên lịch.

**Flow 4 – Xem tổng quan đầu ngày:**
Mở dashboard → thấy các ca dạy hôm nay + học sinh nào đang nợ nhiều để nhắc phụ huynh.

---

## 6. Yêu cầu phi chức năng

- **Mobile-first**: giao diện phải thao tác được thuận tiện bằng ngón tay cái trên điện thoại, nút bấm to, ít bước.
- **Responsive**: dùng tốt cả trên điện thoại lẫn máy tính (khi soạn giáo án dài, gõ trên máy tính tiện hơn).
- **Hoạt động offline cơ bản** (ưu tiên phase 2 nếu làm PWA): điểm danh vẫn ghi được khi mất mạng, đồng bộ lại khi có mạng.
- **Tốc độ**: màn hình điểm danh phải load nhanh (<2s), thao tác tối thiểu.
- **Bảo mật**: đăng nhập bằng tài khoản cá nhân (email/password hoặc OTP), dữ liệu học sinh không public.
- **Sao lưu dữ liệu**: có chức năng export dữ liệu (CSV/Excel) để backup học phí, danh sách học sinh.

---

## 7. Đề xuất công nghệ (gợi ý cho Claude Code)

- **Frontend**: React (Next.js) + Tailwind CSS, thiết kế PWA (installable trên điện thoại, không cần build app native).
- **Backend**: Next.js API routes hoặc Node.js/Express riêng.
- **Database**: PostgreSQL (hoặc SQLite cho bản đơn giản/local trước, dễ nâng cấp).
- **Auth**: đơn giản – 1 tài khoản giáo viên, session-based hoặc JWT.
- **AI giáo án**: gọi Anthropic API (Claude) để sinh outline giáo án từ input của giáo viên.
- **File lưu tài liệu**: lưu local/cloud storage (vd Supabase Storage / S3-compatible) cho file PDF/ảnh.
- **Deploy**: Vercel (frontend+API) + managed Postgres (Supabase/Neon) — dễ triển khai, chi phí thấp, phù hợp 1 người dùng.

---

## 8. Roadmap triển khai (đề xuất theo giai đoạn)

**Giai đoạn 1 – MVP (ưu tiên cao nhất, giải quyết đau đầu nhất là tiền bạc):**
- Quản lý Lớp học & Học sinh (mục 3.1)
- Điểm danh & Công nợ & Thanh toán (mục 3.2)
- Dashboard cơ bản: lịch dạy hôm nay, công nợ tổng (mục 3.5 rút gọn)

**Giai đoạn 2 – Soạn giảng:**
- Ngân hàng bài giảng + Kế hoạch buổi dạy (mục 3.3, chưa cần AI)
- Quản lý tài liệu & in ấn (mục 3.4)

**Giai đoạn 3 – Nâng cao:**
- Tích hợp AI soạn giáo án (mục 3.3 phần AI)
- Push notification nhắc lịch, nhắc công nợ (mục 3.6)
- Offline support đầy đủ (PWA)

---

## 9. Tiêu chí thành công

- Giáo viên điểm danh 1 lớp (5–12 học sinh) trong **dưới 1 phút**.
- Không còn tình trạng "quên ai đã đóng tiền" — mọi công nợ đều tra cứu được tức thì.
- Giảm ít nhất 50% thời gian soạn giáo án nhờ ngân hàng bài giảng tái sử dụng + gợi ý AI.
- Có thể dùng hoàn toàn bằng điện thoại, không bắt buộc phải mở máy tính.
