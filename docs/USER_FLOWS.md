# Thiết kế luồng người dùng (User Flows)

Tập trung vào các luồng chính cho MVP: đăng ký/login, nạp, chuyển, rút, xem lịch sử.

## 1. Luồng đăng ký & xác thực
1. Người dùng mở `Đăng ký` → nhập email, mật khẩu, tên
2. Hệ thống tạo user (status=PENDING)
3. Gửi OTP xác thực vào email/ SMS (lưu code vào Redis TTL 5 phút)
4. Người dùng nhập OTP → verify → kích hoạt tài khoản
5. Hệ thống trả về access token + refresh token (httpOnly cookie)

## 2. Luồng đăng nhập
1. Người dùng mở `Đăng nhập` → nhập email + mật khẩu
2. Hệ thống xác thực → trả access token + refresh token
3. Nếu phát hiện thiết bị lạ → yêu cầu OTP

## 3. Luồng xem số dư & lịch sử
1. Người dùng đăng nhập → vào Dashboard
2. Frontend gọi `GET /wallets` và `GET /transactions/history`
3. Hiển thị thẻ số dư, danh sách giao dịch (mới nhất lên đầu)
4. Socket.IO kết nối để nhận sự kiện `balance:updated` và `transaction:created`

## 4. Luồng chuyển tiền (P2P)
1. Người gửi mở `Chuyển tiền` → chọn ví nguồn, nhập email/phone người nhận hoặc scan QR
2. Nhập số tiền, mô tả, xác nhận
3. Hệ thống yêu cầu OTP giao dịch (nếu vượt ngưỡng hoặc policy)
4. Verify OTP → Backend bắt đầu transaction: chạy trong transaction session (MongoDB)
   - Kiểm tra số dư
   - Ghi transaction với status=PENDING
   - Trừ số dư gửi vào hold hoặc trực tiếp trừ
   - Cộng số dư người nhận
   - Cập nhật status=SUCCESS và ghi audit log
5. Emit sự kiện Socket.IO cho cả sender & receiver
6. Trả response cho client với transaction_id

## 5. Luồng nạp tiền (Deposit)
1. Người dùng chọn `Nạp tiền` → chọn bank/truy cập thông tin chuyển khoản
2. Hệ thống tạo deposit request với payment_reference (PENDING)
3. Người dùng chuyển khoản từ ngân hàng ngoài, dùng payment_reference
4. Bank gửi webhook xác nhận thanh toán
5. Backend nhận webhook → verify amount & reference → cập nhật wallet và transaction status=SUCCESS → emit notification

## 6. Luồng rút tiền (Withdraw)
1. Người dùng chọn `Rút tiền` → chọn bank đã liên kết, nhập số tiền
2. Hệ thống validate balance & tạo withdrawal request (PENDING)
3. Admin kiểm tra & approve (thời gian đầu có thể tự động approve)
4. Sau khi approve → hệ thống call bank API hoặc tạo batch payout → cập nhật status
5. Notify user

## 7. Luồng quản trị (Admin)
1. Admin login → access `/admin`
2. Xem danh sách giao dịch PENDING → filter theo type
3. Mở chi tiết giao dịch → approve/reject với note
4. Hệ thống gửi thông báo cho user liên quan

---

## Notes
- Tất cả luồng giao dịch quan trọng phải có audit log
- Dùng Redis cho OTP, rate-limit và queue chỉ định background job xử lý payout
- Sử dụng Socket.IO rooms theo `user:<userId>` để gửi thông báo riêng