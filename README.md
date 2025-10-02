# clear-memory
# Script: Clear-Memory.ps1
# Membersihkan RAM setiap dijalankan

# Fungsi untuk kosongkan working set
function Clear-Memory {
    Write-Output "Membersihkan RAM..."
    $processes = Get-Process | Where-Object { $_.Name -ne "explorer" -and $_.Name -ne "System" }
    foreach ($p in $processes) {
        try {
            [System.Runtime.InteropServices.Marshal]::ReleaseComObject(
                [System.Diagnostics.Process]::GetProcessById($p.Id).MaxWorkingSet = [System.IntPtr]::Zero
            ) | Out-Null
        } catch {}
    }
    [System.GC]::Collect()
    [System.GC]::WaitForPendingFinalizers()
    Write-Output "Selesai membersihkan."
}

Clear-Memory

Cara Pakai

Buka Notepad, salin kode di atas → simpan dengan nama Clear-Memory.ps1.

Klik kanan file → Run with PowerShell untuk uji coba.

Untuk otomatis tiap 1 jam:

Tekan Win + R, ketik taskschd.msc (Task Scheduler).

Buat Task Baru → di tab Triggers pilih Repeat every 1 hour.

Di tab Actions, pilih Start a Program, lalu isi: 
powershell.exe -ExecutionPolicy Bypass -File "C:\lokasi\Clear-Memory.ps1"
