# 🖥️ Quản Trị Hệ Thống Mạng – Network System Administration

Đây là repository lưu trữ toàn bộ bài thực hành trong môn **Quản trị Hệ thống Mạng**.  
Mỗi bài được triển khai trong một thư mục riêng, bao gồm **hướng dẫn chi tiết** và **hình ảnh minh họa**.  
Mục tiêu là tạo ra một kho lưu trữ giúp dễ dàng theo dõi, ôn tập và áp dụng trong thực tế.

---

## 📚 Danh sách Bài Thực Hành

*Link tổng hợp video: https://www.youtube.com/playlist?list=PLM7YsKegPTXOxKKP-UTLzl7HdBiUWMnzK*

1. **Cài đặt Windows Server**  
   👉 Cài đặt hệ điều hành Windows Server và chuẩn bị môi trường.

2. **Triển khai dịch vụ Active Directory**  
   👉 Cài đặt, cấu hình và quản lý Active Directory Domain Services.

3. **Cài đặt và cấu hình Child Domain**  
   👉 Tạo Child Domain trong forest đã có sẵn.

4. **Tạo OU và Group User**  
   👉 Tổ chức user bằng OU và Group để dễ quản lý.

5. **Tạo OU, Group User và Ủy quyền cho User (Delegation)**  
   👉 Phân quyền quản trị (Delegation of Control) cho user trong OU.

6. **Cài đặt và cấu hình dịch vụ DHCP**  
   👉 Cấp phát địa chỉ IP động trong mạng LAN.

7. **Cài đặt và cấu hình Quản lý Đĩa (Disk Management)**  
   👉 Quản lý phân vùng, dung lượng, ổ đĩa lưu trữ.

8. **Phân quyền và Chia sẻ dữ liệu**  
   👉 Quản lý quyền truy cập và chia sẻ tài nguyên trên mạng.

---

## 📂 Cấu trúc Repository

```bash
Network-System-Administration/
│
├── README.md
│
├── Bai1_WindowsServer/
│   ├── images/
│   └── README.md
│
├── Bai2_ActiveDirectory/
│   ├── images/
│   └── README.md
│
├── Bai3_ChildDomain/
│   ├── images/
│   └── README.md
│
├── Bai4_OU_GroupUser/
│   ├── images/
│   └── README.md
│
├── Bai5_OU_GroupUser_Delegation/
│   ├── images/
│   └── README.md
│
├── Bai6_DHCP/
│   ├── images/
│   └── README.md
│
├── Bai7_DiskManagement/
│   ├── images/
│   └── README.md
│
└── Bai8_SharingPermission/
    ├── images/
    └── README.md
```

## 🚀 Mục Tiêu
- Hiểu rõ quy trình triển khai và quản trị hệ thống mạng trên Windows Server.
- Nắm vững các dịch vụ cốt lõi: AD DS, DHCP, Disk Management, File Sharing…
- Biết cách phân quyền, quản lý user và tài nguyên mạng.
- Có thể áp dụng vào môi trường doanh nghiệp thực tế.

## 🛠️ Công Nghệ & Công Cụ
- VMware Workstation Pro – tạo và quản lý máy ảo.
- Windows Server Core 2019 – triển khai dịch vụ máy chủ.
- Windows 8.1 / Windows 10 – máy trạm, client để kiểm thử.

---

✍️ Người thực hiện: 
- Trần Thanh Nhã
- Bùi Thị Bích Ngọc
- Cao Thông Thái

Thời gian: 2025

Môn học: Quản trị Hệ thống Mạng
