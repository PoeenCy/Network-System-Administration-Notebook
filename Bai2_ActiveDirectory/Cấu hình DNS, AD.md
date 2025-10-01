**Bài 2: Triển khai dịch vụ Active Directory**

Trên máy **FIT-DC-02** vào cmdlet *sconfig* 

Nhập 8 (Network setting) →  Nhập số index card mạng tương ứng 

**\* Cấu hình mạng cơ bản**

Nhập 1 (Set Network Adapter Address) → Nhập s để chọn static → Nhập IP 192.168.1.2 →  Nhập subnetmask 255.255.255.0 → Nhập default gateway 192.168.1.1

**\* Cấu hình địa chỉ DNS server**

Nhập 2 (Set DNS Servers)  →  Nhập 192.168.1.2 (Máy chủ DNS chính) → Nhập IP máy chủ thay thế (Có thể để trống và nhấn enter)

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.001.png)

**\* Cài đặt vai trò DNS trên FIT-DC-02**

Cmdlet “Install-WindowsFeature DNS”

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.002.png)

**\* Cài đặt và nâng cấp FIT-DC-02 lên Domain Controller**

\- Cmdlet “*Install-WindowsFeature AD-Domain-Service -IncludeManagementTools*”: Cài đặt vai trò **Active Directory**

\- Cmdlet “*Import-Module ADDSDeployment*”: Nhập các cmdlet vận hành vai trò **Active Directory**

\- Cmdlet “*Install-ADDSForest -DomainName ‘Labtdtu.com’ -InstallDNS*”: Tạo ra **Forest** với vùng domain chính là **Labtdtu.com** và khai báo vào DNS Server

\- Sau khi nhập cmdlet, hệ thống sẽ yêu cầu tạo và xác nhận mật khẩu cho chế độ an toàn với user quyền cao nhất là Administrator

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.003.png)

\- Sau khi thực hiện cấu hình, hệ thống sẽ tự động khởi động lại, nhấn tổ hợp Ctrl + Alt + Del và nhập mật khẩu user để mở khóa

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.004.png)



**\* Cấu hình Client gia nhập vào Domain**

\- Yêu cầu client phải được cấu hình chung với mạng Domain Controller và phải có địa chỉ nhận DNS chính thuộc về máy chủ Domain Controller

\- Nhấn tỗ hợp phím Windows + R → Nhập “Control system” → Click vào “change settings” cạnh phải → “Change…” → Chuyển option workgroup sang domain và nhập **labtdtu.com** → Nhập tên là Administrator cùng với mật khẩu được thiết lập trên **FIT-DC-02** → Hệ thống sẽ yêu cầu khởi động hoặc để sau

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.005.png)

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.006.png)


![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.007.png)


**\* Nâng cấp máy chủ FIT-SDC-04 làm Domain Controller phụ thay thế**

Trên máy **FIT-SDC-04** vào cmdlet *sconfig* 

Nhập 8 (Network setting) →  Nhập số index card mạng tương ứng 

**\* Cấu hình mạng cơ bản**

Nhập 1 (Set Network Adapter Address) → Nhập s để chọn static → Nhập IP 192.168.1.4 →  Nhập subnetmask 255.255.255.0 → Nhập default gateway 192.168.1.1

**\* Cấu hình địa chỉ DNS server**

Nhập 2 (Set DNS Servers)  →  Nhập 192.168.1.2 (Máy chủ DNS chính) → Nhập IP máy chủ thay thế (Có thể để trống và nhấn enter)


![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.008.png)

**\* Cài đặt và nâng cấp FIT-SDC-04 lên Domain Controller phụ cho labtdtu.com**

\- Cmdlet “*Install-WindowsFeature AD-Domain-Service -IncludeManagementTools*”: Cài đặt vai trò **Active Directory**

\- Cmdlet “*Install-ADDSDomainController -DomainName ‘labtdtu.com” -credential (Get-Credential) -force: $true*”: Join vùng domain có sẵn là labtdtu.com

\- Hệ thống sẽ hiện cửa sổ yêu cầu nhập username và password của user được ủy quyền trên **labtdtu.com.** Mặc định là Administrator


![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.009.png)

\- Sau khi xác thực thành công, hệ thống sẽ yêu cầu tạo và xác nhận mật khẩu cho chế độ an toàn với user quyền cao nhất là Administrator (tương tự máy chủ chính)

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.010.png)

![](\images\Aspose.Words.3da246a5-80bd-484e-ab30-ef511ec523f1.011.png)





































@Cao Thông Thái - Nhóm hỗ trợ quản trị hệ thống mạng
