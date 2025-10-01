# Cấu hình Child Domain

![Thiết kế chưa có tên.png](Thit_k_cha_c_tn.png)

### **1. Cài đặt một Child Domain trên Server Core**

### **Mục tiêu**

- Một trong những lựa chọn triển khai phổ biến khi một tổ chức mở rộng là tạo ra một miền con (Child Domain).
- Bài này phù hợp khi cần phân chia quyền quản trị cho một chi nhánh hoặc một phòng ban lớn, trong khi vẫn duy trì một mối quan hệ tin cậy (trust) hai chiều và tự động với miền cha (parent domain).
- Trong bài này, chúng ta tiếp tục kế thừa các bài tập trước về hệ thống mạng và các cấu hình DHCP, DNS và nâng cấp AD.
- Bài hôm nay, chúng ta thêm một máy server mới, với vai trò là Child Domain nằm trong Domain của AD trong bài lab 2 hôm trước chúng ta đã làm.

### **Các bước Thực hiện bằng PowerShell**

```powershell
# Bước 1: Cài đặt các tệp nhị phân của vai trò AD DS (nếu chưa có)
Install-WindowsFeature AD-Domain-Services

# Bước 2: Lấy thông tin đăng nhập của một tài khoản có quyền Enterprise Admin
# Lệnh này sẽ mở một cửa sổ pop-up để nhập username và password một cách an toàn.
$credential = Get-Credential

# Thực hiện thay đổi theo 
#labtdtu.com\Administrator
#Password: P@ssw0rd

# Bước 3: Yêu cầu người dùng nhập mật khẩu cho Directory Services Restore Mode (DSRM)
$safemodePassword = Read-Host -AsSecureString -Prompt "Enter the DSRM password"

#Password: P@ssw0rd

# Bước 4: Thực thi lệnh cài đặt Child Domain
# Lưu ý rằng tham số -InstallDns đã được loại bỏ.
Install-ADDSDomain -NewDomainName "hcm" `
    -ParentDomainName "labtdtu.com" `
    -Credential $credential `
    -SafeModeAdministratorPassword $safemodePassword `
    -Force
```

### **Phân tích Kỹ thuật các Tham số**

- *-NewDomainName "hcm"**: Chỉ định tên DNS nhãn đơn (single-label) của miền con mới. Tên miền đầy đủ (FQDN) sẽ tự động được tạo thành hcm.labtdtu.com.
- *-ParentDomainName "labtdtu.com"**: Chỉ định tên DNS của miền cha mà miền con này sẽ trực thuộc.
- **Loại bỏ -InstallDns**: Đây là điểm khác biệt quan trọng nhất. Bằng cách không sử dụng tham số này, chúng ta ra lệnh cho quá trình cài đặt **không** cài đặt vai trò DNS Server trên máy chủ này. Thay vào đó, máy chủ sẽ cố gắng liên lạc với máy chủ DNS hiện có (được cấu hình trong thiết lập IP của máy) để tự động đăng ký các bản ghi dịch vụ (SRV records) cần thiết cho hoạt động của miền con mới.
- *-Credential $credential**: Cung cấp thông tin xác thực của một tài khoản có đủ quyền hạn (thường là thành viên của nhóm Enterprise Admins) để thực hiện việc thêm một miền mới vào rừng.
- *-Force**: Tùy chọn này cho phép quá trình cài đặt tự động xác nhận các thông báo và tự động khởi động lại máy chủ sau khi hoàn tất, rất hữu ích cho việc triển khai tự động.

---

### **2. Gia nhập Máy trạm vào Child Domain**

Sau khi tạo thành công một Domain Controller, bước xác minh cơ bản nhất là thử gia nhập một máy tính trạm (workstation) vào miền đó. Quá trình này xác nhận rằng:

1. Máy trạm có kết nối mạng tới Domain Controller.
2. Dịch vụ DNS đang hoạt động chính xác, cho phép máy trạm tìm thấy các bản ghi dịch vụ (SRV records) của DC.

Trước khi thực hiện, cần đảm bảo máy trạm đã được cấu hình DNS trỏ về máy chủ DNS có khả năng phân giải tên miền hcm.labtdtu.com.

### Các bước thực hiện với giao diện: [https://youtu.be/KUwGVOt0ZIY](https://youtu.be/KUwGVOt0ZIY) (đã thực hiện ở bài lab 2 trước)

### **Các bước Thực hiện bằng PowerShell**

Trên máy trạm, mở PowerShell với quyền quản trị và thực thi lệnh sau:

```powershell
# Lệnh Add-Computer để gia nhập máy vào miền "hcm.labtdtu.com"
# -Credential sẽ yêu cầu nhập tài khoản và mật khẩu của một tài khoản Domain Admin
# -Restart sẽ tự động khởi động lại máy để áp dụng thay đổi
Add-Computer -DomainName "hcm.labtdtu.com" -Credential (Get-Credential) -Restart
```

### **Xác minh sau khi Gia nhập**

Sau khi máy trạm khởi động lại, bạn có thể đăng nhập bằng một tài khoản thuộc miền hcm.labtdtu.com. Để kiểm tra lại trạng thái gia nhập miền bằng PowerShell, sử dụng lệnh sau:

```powershell
# Lấy thông tin về trạng thái miền của máy tính
(Get-ComputerInfo).Domain
```

Nếu thành công, lệnh trên sẽ trả về tên miền mà máy tính đã gia nhập, ví dụ: hcm.labtdtu.com.