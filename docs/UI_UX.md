# Phác thảo UI/UX cơ bản (MVP)

Mục tiêu: giao diện đơn giản, rõ ràng, tập trung vào luồng giao dịch.

## 1. Nguyên tắc thiết kế
- Thanh điều hướng đơn giản (Dashboard, Wallet, Transactions, Admin nếu role=ADMIN)
- Trọng tâm: thẻ hiển thị số dư, nút `Nạp`, `Rút`, `Chuyển` rõ ràng
- Thông báo nhỏ (toast) cho feedback nhanh
- Responsive: mobile-first (ưu tiên trải nghiệm trên điện thoại)

## 2. Trang chính (Dashboard)
- Header: Logo, Bell (notifications), Avatar
- Balance Card: tổng số dư, nút hành động (`Nạp`, `Rút`, `Chuyển`)
- Recent Transactions: danh sách 5 giao dịch mới nhất
- Quick Actions: QR pay, Link bank

Wireframe (text):
[Header]
[Balance Card]  [Quick Actions]
[Recent Transactions]

## 3. Trang Wallet
- Danh sách wallets (card list)
- Mỗi wallet: tên, số dư, nút `Chi tiết`, `Chuyển`, `Rút`
- Button `Tạo ví mới`

## 4. Trang Chuyển tiền
- Form: Chọn ví nguồn, tìm người nhận (email/phone), số tiền, nội dung
- Step 1: Nhập thông tin → Step 2: Xác nhận (hiển thị fee & total) → Step 3: OTP (nếu cần)

## 5. Trang Nạp tiền
- Hiển thị thông tin chuyển khoản (account_number, bank_name, payment_reference)
- Button `Tôi đã chuyển` để kích hoạt kiểm tra
- Trạng thái deposit: PENDING / SUCCESS / FAILED

## 6. Trang Rút tiền
- Chọn bank account đã liên kết, nhập amount, OTP nếu cần
- Hiển thị estimated time

## 7. Trang Transactions
- Filter: type, status, date range
- List with pagination
- Click item → modal detail (view receipt, reference)

## 8. Admin UI (cơ bản)
- Left nav: Users, Transactions, Stats
- Users table: search, ban/unban
- Transactions: filter PENDING, approve/reject flow

## 9. Components chính
- `BalanceCard`, `TransactionList`, `TransferForm`, `DepositCard`, `BankAccountForm`, `NotificationCenter`

## 10. Hệ màu & Accessibility
- Màu chính: xanh dương (trust), accent: vàng/đỏ cho cảnh báo
- Contrast cao, font kích thước đủ lớn
- All interactive elements keyboard-accessible

Kết luận: Thiết kế đủ nhẹ để dev nhanh, UX hướng người dùng hoàn thành giao dịch trong 3 thao tác.