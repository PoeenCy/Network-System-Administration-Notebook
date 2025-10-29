# Scripts ở đây nhé!!!

```powershell
# --- BAT DAU SCRIPT ---

# ================================================================
# CHUC NANG 1: HIEN THI THONG TIN DIA
# ================================================================
function Show-DiskInfo {
    Clear-Host
    Write-Host "--- THONG TIN O DIA TONG QUAT (Get-Disk) ---" -ForegroundColor Green
    Get-Disk | Format-Table Number, FriendlyName, PartitionStyle, Size, HealthStatus, OperationalStatus -AutoSize
    
    Write-Host "--- THONG TIN O DIA CO THE 'POOL' (Get-PhysicalDisk) ---" -ForegroundColor Cyan
    Get-PhysicalDisk | Format-Table FriendlyName, CanPool, CannotPoolReason, Size, MediaType -AutoSize
    
    Write-Host "--- THONG TIN PHAN VUNG HIEN CO (Get-Volume) ---" -ForegroundColor Yellow
    Get-Volume | Format-Table DriveLetter, FileSystem, FriendlyName, SizeRemaining, Size -AutoSize
    
    Write-Host ""
    Read-Host "Nhan Enter de quay lai Menu chinh..."
}

# ================================================================
# CHUC NANG 2: KHOI TAO DIA BASIC (GPT / MBR)
# ================================================================
function Initialize-BasicDisk {
    Clear-Host
    Get-Disk | Format-Table Number, FriendlyName, PartitionStyle, Size
    Write-Host "--- KHOI TAO DIA BASIC ---" -ForegroundColor Green
    
    try {
        $DiskNumber = Read-Host "=> Nhap 'Number' (so hieu) cua dia ban muon khoi tao (vi du: 1)"
        $Disk = Get-Disk -Number $DiskNumber
        
        if ($Disk.PartitionStyle -ne 'RAW') {
            Write-Warning "Dia nay da duoc khoi tao. Hay dung chuc nang 'Don Sach Dia' (Wipe) truoc."
            Read-Host "Nhan Enter de quay lai..."
            return
        }
        
        $Style = Read-Host "=> Ban muon khoi tao theo 'GPT' hay 'MBR'? (go 'gpt' hoac 'mbr')"
        if ($Style -ne 'gpt' -and $Style -ne 'mbr') {
            Write-Warning "Chi duoc phep go 'gpt' hoac 'mbr'. Huy bo thao tac."
            Read-Host "Nhan Enter de quay lai..."
            return
        }

        # Thuc hien khoi tao
        Initialize-Disk -Number $DiskNumber -PartitionStyle $Style
        Write-Host "DA KHOI TAO DIA $DiskNumber THANH CONG THEO CHUAN $($Style.ToUpper())" -ForegroundColor Green

        # Hoi co muon tao Volume luon khong
        $CreateVol = Read-Host "=> Ban co muon tao 1 Simple Volume (chiem het dia) va format NTFS luon khong? (y/n)"
        if ($CreateVol -eq 'y') {
            New-Partition -DiskNumber $DiskNumber -UseMaximumSize -AssignDriveLetter | Format-Volume -FileSystem NTFS -NewFileSystemLabel "Data"
            Write-Host "Da tao va format Volume moi."
        }

    } catch {
        Write-Warning "Loi: $_.Exception.Message"
    }
    
    Read-Host "Nhan Enter de quay lai Menu chinh..."
}

# ================================================================
# CHUC NANG 3: QUAN LY STORAGE SPACES (HIEN DAI)
# ================================================================
function Manage-StorageSpaces {
    do {
        Clear-Host
        Write-Host "--- MENU CON: QUAN LY STORAGE SPACES (HIEN DAI) ---" -ForegroundColor Cyan
        Write-Host "Cac Pool hien co:"
        Get-StoragePool | Format-Table FriendlyName, HealthStatus, Size
        Write-Host "Cac dia ao hien co:"
        Get-VirtualDisk | Format-Table FriendlyName, ResiliencySettingName, Size, HealthStatus
        
        Write-Host ""
        Write-Host "1. Tao Storage Pool moi"
        Write-Host "2. Tao Virtual Disk (Simple, Mirror, Parity) tu Pool"
        Write-Host "3. Tao Volume (o dia) cho Virtual Disk"
        Write-Host "Q. Quay lai Menu chinh"
        $choice = Read-Host "=> Lua chon cua ban"

        switch ($choice) {
            '1' {
                # Tao Pool
                Write-Host "--- Tao Storage Pool Moi ---"
                Get-PhysicalDisk | Where-Object CanPool -eq $True | Format-Table FriendlyName, CanPool, Size, MediaType
                $disksInput = Read-Host "=> Nhap ten cac dia muon gop (FriendlyName), cach nhau bang dau phay (vi du: PhysicalDisk1,PhysicalDisk2)"
                $diskNames = $disksInput.Split(',') | ForEach-Object { $_.Trim() }
                $disks = $diskNames | ForEach-Object { Get-PhysicalDisk -FriendlyName $_ }
                
                $poolName = Read-Host "=> Nhap ten cho Pool moi (vi du: MyPool)"
                New-StoragePool -FriendlyName $poolName -StorageSubSystemFriendlyName *Windows* -PhysicalDisks $disks
                Write-Host "DA TAO POOL '$poolName'" -ForegroundColor Green
                Read-Host "Nhan Enter de tiep tuc..."
            }
            '2' {
                # Tao Virtual Disk
                Write-Host "--- Tao Virtual Disk Moi ---"
                Get-StoragePool | Format-Table FriendlyName
                $poolName = Read-Host "=> Nhap ten Pool ban muon su dung"
                $vdName = Read-Host "=> Nhap ten cho Virtual Disk moi (vi du: VDisk_Mirror)"
                $vdResiliency = Read-Host "=> Chon loai (Simple, Mirror, hoac Parity)"
                $vdSize = Read-Host "=> Nhap kich thuoc (vi du: 50GB)"
                $vdProv = Read-Host "=> Chon kieu cap phat (Thin hay Fixed)"
                
                New-VirtualDisk -StoragePoolFriendlyName $poolName `
                                -FriendlyName $vdName `
                                -ResiliencySettingName $vdResiliency `
                                -Size $vdSize `
                                -ProvisioningType $vdProv
                Write-Host "DA TAO VIRTUAL DISK '$vdName'" -ForegroundColor Green
                Read-Host "Nhan Enter de tiep tuc..."
            }
            '3' {
                # Tao Volume
                Write-Host "--- Tao Volume cho Virtual Disk ---"
                Get-VirtualDisk | Format-Table FriendlyName
                $vdName = Read-Host "=> Nhap ten Virtual Disk ban muon tao Volume"
                $letter = Read-Host "=> Nhap ky tu o dia (vi du: E)"
                
                Get-VirtualDisk -FriendlyName $vdName | New-Volume -FileSystem NTFS -DriveLetter $letter
                Write-Host "DA TAO VA FORMAT VOLUME $letter" -ForegroundColor Green
                Read-Host "Nhan Enter de tiep tuc..."
            }
        }
    } while ($choice -ne 'q')
}

# ================================================================
# CHUC NANG 4: CAU HINH DYNAMIC DISKS (LOI THOI)
# =Dung DiskPart - Yeu cau tuong tac
# ================================================================
function Manage-DynamicDisks {
    Clear-Host
    Write-Warning "!!! CANH BAO !!!"
    Write-Warning "Day la cong nghe LOI THOI (LEGACY) va khong duoc khuyen nghi."
    Write-Warning "Storage Spaces (Menu so 3) la cong nghe thay the."
    Write-Warning "Ban KHONG THE su dung dong thoi Storage Spaces va Dynamic Disks tren cung mot o dia."
    
    $confirm = Read-Host "=> Ban co muon tiep tuc voi Dynamic Disks? (y/n)"
    if ($confirm -ne 'y') { return }

    Clear-Host
    Write-Host "--- CAU HINH DYNAMIC DISKS (DANG MO DISKPART) ---" -ForegroundColor Red
    Write-Host "Cac dia hien co:"
    Get-Disk | Format-Table Number, FriendlyName, PartitionStyle, Size
    
    Write-Host ""
    Write-Host "Ban se duoc dua vao moi truong DISKPART."
    Write-Host "Su dung cac lenh nhu:"
    Write-Host "  select disk <number>"
    Write-Host "  clean"
    Write-Host "  convert dynamic"
    Write-Host "  create volume mirror disk=<num1>,<num2>"
    Write-Host "  create volume stripe disk=<num1>,<num2>"
    Write-Host "  create volume raid disk=<num1>,<num2>,<num3>"
    Write-Host "  exit (de thoat Diskpart)"
    Write-Host "---------------------------------------------------"
    
    # Mo diskpart de nguoi dung tuong tac
    diskpart
    
    Write-Host "Da thoat khoi Diskpart."
    Read-Host "Nhan Enter de quay lai Menu chinh..."
}

# ================================================================
# CHUC NANG 5: DON SACH (WIPE) DIA
# ================================================================
function Wipe-SelectedDisk {
    Clear-Host
    Get-Disk | Format-Table Number, FriendlyName, PartitionStyle, Size
    Write-Host "--- DON SACH DIA (WIPE DISK) ---" -ForegroundColor Red
    
    try {
        $DiskNumber = Read-Host "=> Nhap 'Number' (so hieu) cua dia ban muon XOA SACH HOAN TOAN"
        $Disk = Get-Disk -Number $DiskNumber

        if ($Disk.IsSystem) {
            Write-Warning "Khong the xoa dia he thong!"
            Read-Host "Nhan Enter de quay lai..."
            return
        }

        Write-Host "!!! CANH BAO CUC KY QUAN TRONG !!!" -ForegroundColor Yellow
        Write-Host "Ban da chon Dia $DiskNumber ($($Disk.FriendlyName))."
        Write-Host "Thao tac nay se XOA SACH TOAN BO DU LIEU, phan vung, va moi thu tren dia nay."
        $ConfirmWipe = Read-Host "=> De xac nhan, vui long go chinh xac 'WIPE' (viet hoa) roi Enter"

        if ($ConfirmWipe -eq 'WIPE') {
            # Thuc hien xoa
            Clear-Disk -Number $DiskNumber -RemoveData -RemoveOEM -Confirm:$false
            Write-Host "DA XOA SACH DIA $DiskNumber. Dia hien o trang thai 'RAW'." -ForegroundColor Green
        } else {
            Write-Host "Da huy bo thao tac."
        }

    } catch {
        Write-Warning "Loi: $_.Exception.Message"
    }
    
    Read-Host "Nhan Enter de quay lai Menu chinh..."
}

# ================================================================
# == MENU CHINH CUA CHUONG TRINH ==
# ================================================================

do {
    Clear-Host
    Write-Host "=============================================" -ForegroundColor Green
    Write-Host "    TOOL CAU HINH O DIA - (PowerShell Script)"
    Write-Host "============================================="
    Write-Host "Ban muon lam gi?"
    Write-Host ""
    Write-Host "1. Hien thi trang thai o dia hien tai"
    Write-Host "2. Khoi tao dia Basic (Chuyen sang GPT / MBR)"
    Write-Host "3. Quan ly Storage Spaces (HIEN DAI / Khuyen dung)"
    Write-Host "4. Cau hinh Dynamic Disks (LOI THOI / Legacy)"
    Write-Host "5. DON SACH (WIPE) mot dia ve trang thai RAW"
    Write-Host ""
    Write-Host "Q. Thoat (Exit)"
    Write-Host "============================================="
    
    $choice = Read-Host "=> Lua chon cua ban [1, 2, 3, 4, 5, Q]"

    switch ($choice) {
        '1' { Show-DiskInfo }
        '2' { Initialize-BasicDisk }
        '3' { Manage-StorageSpaces }
        '4' { Manage-DynamicDisks }
        '5' { Wipe-SelectedDisk }
    }
} while ($choice -ne 'q')

Write-Host "Tam biet!"
# --- KET THUC SCRIPT ---
```