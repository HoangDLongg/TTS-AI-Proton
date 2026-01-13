#  Banking Database System Documentation

Tài liệu chi tiết về cấu trúc, mối quan hệ và ý nghĩa nghiệp vụ của hệ thống cơ sở dữ liệu ngân hàng.

---

## 1. Table Definitions (Danh sách các bảng)

Hệ thống được cấu thành từ 9 bảng chính, chia thành 3 nhóm chức năng:

### Nhóm Khách hàng & Tài khoản
- **`Customers`**: Lưu trữ hồ sơ định danh khách hàng. Sử dụng `SSN` (Số định danh duy nhất) để kiểm soát trùng lặp thông qua `UNIQUE INDEX`.
- **`Accounts`**: Quản lý thông tin tài khoản, loại tài khoản (AccountType), số dư hiện tại (Balance) và trạng thái (AccountStatus).
- **`Cards`**: Lưu trữ thông tin thẻ vật lý/ảo liên kết với khách hàng, bao gồm ngày hết hạn và trạng thái thẻ.

### Nhóm Vận hành chi nhánh
- **`Branches`**: Quản lý các điểm giao dịch, thông tin liên lạc và giờ mở cửa.
- **`Employees`**: Hồ sơ nhân sự, chức vụ và trình độ chuyên môn của nhân viên tại từng chi nhánh.
- **`ATM`**: Quản lý mạng lưới máy rút tiền, vị trí lắp đặt và các tính năng hỗ trợ.

### Nhóm Giao dịch & Kiểm toán
- **`Transactions`**: Ghi nhận các biến động tài chính gốc (Số tiền, thời gian, loại giao dịch).
- **`TransactionDetails`**: Lưu trữ chi tiết về đối tác thụ hưởng (Merchant), danh mục chi tiêu và loại tiền tệ.
- **`TransactionStatus`**: Theo dõi lịch sử trạng thái của giao dịch (Thành công, Thất bại, Đang xử lý).

---

## 2. Relationships (Mối quan hệ)

Cơ sở dữ liệu được thiết kế theo mô hình quan hệ chặt chẽ (RDBMS) để đảm bảo tính toàn vẹn dữ liệu:



- **Phân cấp Tổ chức**: 
  - `Branches` (1) ➔ `Customers`, `Employees`, `ATM` (N): Mọi thực thể vận hành đều trực thuộc một chi nhánh quản lý.
- **Sở hữu Tài sản**: 
  - `Customers` (1) ➔ `Accounts`, `Cards` (N): Một khách hàng có quyền sở hữu nhiều tài khoản và thẻ khác nhau.
- **Vòng đời Giao dịch**: 
  - `Accounts` (1) ➔ `Transactions` (N): Tài khoản là nguồn phát sinh các giao dịch.
  - `Transactions` (1) ➔ `TransactionDetails` (1): Liên kết 1-1 để lưu trữ thông tin mở rộng của giao dịch.
  - `Transactions` (1) ➔ `TransactionStatus` (N): Cho phép theo dõi nhiều bước trạng thái cho cùng một mã giao dịch.

---

## 3. Business Meaning (Ý nghĩa Nghiệp vụ)

###  Quản lý Định danh và Bảo mật
- **Identity Logic**: Việc đánh chỉ mục `UNIQUE` trên các trường `SSN`, `Email`, `Phone` giúp hệ thống ngăn chặn tài khoản ảo và hỗ trợ định danh khách hàng nhanh chóng trong các quy trình xác thực.
- **Referential Integrity**: Sử dụng `FOREIGN KEY` xuyên suốt giúp ngăn chặn dữ liệu "mồ côi" (Ví dụ: Không thể tạo giao dịch cho một tài khoản không tồn tại).

###  Tối ưu hóa Hiệu suất truy xuất
- **Search Optimization**: Các `INDEX` chiến lược trên `AccountNumber`, `DateTime` và `StatusID` giúp việc truy xuất sao kê và lịch sử giao dịch diễn ra tức thời ngay cả khi dữ liệu lên tới hàng triệu bản ghi.
- **Data Normalization**: Việc tách bảng `TransactionDetails` giúp bảng `Transactions` tinh gọn, tăng tốc độ ghi dữ liệu cho các giao dịch tại quầy và máy ATM.

###  Phân tích và Kiểm toán (Audit)
- **Categorization**: Trường `TransactionCategory` hỗ trợ ngân hàng phân tích hành vi tiêu dùng để đưa ra các gợi ý tài chính cá nhân hóa cho khách hàng.
- **Status Tracking**: Giúp bộ phận vận hành dễ dàng tra soát các giao dịch lỗi thông qua lịch sử trạng thái mà không cần can thiệp vào dữ liệu tài chính gốc.

---

## 4. Technical Metadata

| Ràng buộc | Áp dụng | Mục đích |
| :--- | :--- | :--- |
| **Primary Key** | ID của tất cả các bảng | Đảm bảo tính duy nhất của bản ghi. |
| **Unique Index** | `SSN`, `Email`, `Phone`, `AccountNumber` | Bảo mật và chống trùng lặp định danh. |
| **Foreign Key** | `BranchID`, `CustomerID`, `TransactionID` | Duy trì liên kết logic giữa các thực thể. |
| **Index** | `DateTime`, `Timestamp`, `StatusID` | Tăng tốc độ báo cáo và tra cứu lịch sử. |
