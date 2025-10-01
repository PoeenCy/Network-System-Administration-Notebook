# **Bài 2: Triển khai dịch vụ Active Directory**

## **I. Nâng cấp máy chủ FIT-DC-02 lên Domain Controller chính**

Trên máy **FIT-DC-02**, vào cmdlet `sconfig`.

Nhập `8` (Network setting) → Nhập số index card mạng tương ứng.

### **1. Cấu hình mạng cơ bản**

*   Nhập `1` (Set Network Adapter Address).
*   Nhập `s` để chọn static.
*   Nhập IP: `192.168.1.2`
*   Nhập Subnet mask: `255.255.255.0`
*   Nhập Default gateway: `192.168.1.1`

### **2. Cấu hình địa chỉ DNS server**

*   Nhập `2` (Set DNS Servers).
*   Nhập `192.168.1.2` (Máy chủ DNS chính).
*   Nhập IP máy chủ thay thế (Có thể để trống và nhấn Enter).

![](\images\Aspose.Words.3da246a5-8bd-484e-ab30-ef511ec523f1.001.png)

### **3. Cài đặt vai trò DNS trên FIT-DC-02**

Sử dụng cmdlet:
```powershell
Install-WindowsFeature DNS
```

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.002.png)

### **4. Cài đặt và nâng cấp FIT-DC-02 lên Domain Controller**

*   **Cài đặt vai trò Active Directory:**
    ```powershell
    Install-WindowsFeature AD-Domain-Service -IncludeManagementTools
    ```
*   **Nhập các cmdlet vận hành vai trò Active Directory:**
    ```powershell
    Import-Module ADDSDeployment
    ```
*   **Tạo ra Forest với vùng domain chính là `Labtdtu.com` và khai báo vào DNS Server:**
    ```powershell
    Install-ADDSForest -DomainName ‘Labtdtu.com’ -InstallDNS
    ```
*   Sau khi nhập cmdlet, hệ thống sẽ yêu cầu tạo và xác nhận mật khẩu cho chế độ an toàn với user quyền cao nhất là `Administrator`.

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.003.png)

*   Sau khi thực hiện cấu hình, hệ thống sẽ tự động khởi động lại. Nhấn tổ hợp phím **Ctrl + Alt + Del** và nhập mật khẩu user để mở khóa.

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.004.png)

## **II. Cấu hình Client gia nhập vào Domain**

*   **Yêu cầu:** Client phải được cấu hình chung mạng với Domain Controller và phải có địa chỉ DNS chính trỏ về máy chủ Domain Controller.
*   Nhấn tổ hợp phím **Windows + R** → Nhập `Control system`.
*   Click vào **"Change settings"** → **"Change…"**.
*   Chuyển option từ `Workgroup` sang `Domain` và nhập **labtdtu.com**.
*   Nhập tên là `Administrator` cùng với mật khẩu được thiết lập trên **FIT-DC-02**.
*   Hệ thống sẽ yêu cầu khởi động lại ngay hoặc để sau.

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.005.png)

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.006.png)

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.007.png)

## **III. Nâng cấp máy chủ FIT-SDC-04 làm Domain Controller phụ**

Trên máy **FIT-SDC-04**, vào cmdlet `sconfig`.

Nhập `8` (Network setting) → Nhập số index card mạng tương ứng.

### **1. Cấu hình mạng cơ bản**

*   Nhập `1` (Set Network Adapter Address).
*   Nhập `s` để chọn static.
*   Nhập IP: `192.168.1.4`
*   Nhập Subnet mask: `255.255.255.0`
*   Nhập Default gateway: `192.168.1.1`

### **2. Cấu hình địa chỉ DNS server**

*   Nhập `2` (Set DNS Servers).
*   Nhập `192.168.1.2` (Máy chủ DNS chính).
*   Nhập IP máy chủ thay thế (Có thể để trống và nhấn Enter).

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.008.png)

### **3. Cài đặt và nâng cấp FIT-SDC-04 lên Domain Controller phụ**

*   **Cài đặt vai trò Active Directory:**
    ```powershell
    Install-WindowsFeature AD-Domain-Service -IncludeManagementTools
    ```
*   **Join vào domain `labtdtu.com` đã có sẵn:**
    ```powershell
    Install-ADDSDomainController -DomainName ‘labtdtu.com’ -credential (Get-Credential) -force:$true
    ```
*   Hệ thống sẽ hiện cửa sổ yêu cầu nhập username và password của user được ủy quyền trên **labtdtu.com**. Mặc định là `Administrator`.

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.009.png)

*   Sau khi xác thực thành công, hệ thống sẽ yêu cầu tạo và xác nhận mật khẩu cho chế độ an toàn (tương tự máy chủ chính).

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.010.png)

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.011.png)

***