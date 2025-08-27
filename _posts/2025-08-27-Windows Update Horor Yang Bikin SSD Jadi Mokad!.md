---
title: "KB5063878: Windows Update Horor yang Bikin SSD Mokad!"
categories: Windows-Update
date: 2025-08-27
author: Ilham
tags:
  [
    KB5063878,
    Windows Update,
    Windows 11,
    Windows 11 Update,
    AutoCad,
    OBS NDI,
    Data Loss,
    Data Corruption,
  ]
summary: "Windows Update KB5063878 ini ternyata punya efek samping yang bikin ngeri. membuat SSD jadi rewel, bahkan ada yang sampai meninggal dunia."
image: /assets/images/KB5063878.png
key: 2025-08-27-Windows-Update-1
---

![KB5063878](/assets/images/KB5063878.png "KB5063878")

Ada kabar buruk yang sedang ramai di jagat teknologi. Banyak pengguna melaporkan kalau Windows Update **KB5063878** ini ternyata punya efek samping yang bikin ngeri; membuat SSD jadi rewel, bahkan ada yang sampai **meninggal dunia**.

### **Mengenal Lebih Dekat Windows Update KB5063878**

Penting untuk dipahami bahwa pembaruan **KB5063878** bukanlah pembaruan biasa. Ini adalah pembaruan keamanan bulanan (_Monthly Security Update_) yang bersifat kumulatif. Artinya, pembaruan ini tidak hanya menambal satu lubang keamanan, tetapi juga mencakup berbagai perbaikan dan peningkatan kinerja dari pembaruan sebelumnya.

Pembaruan ini secara spesifik mencakup:

- **Peningkatan Keamanan:** Menutup celah-celah keamanan yang ditemukan di sistem operasi.

- **Perbaikan Bug:** Menyelesaikan masalah yang ada di Windows, seperti bug pada AutoCAD dan lag NDI yang juga dilaporkan.

- **Peningkatan Kinerja:** Optimasi performa sistem.

Jadi, meskipun pembaruan ini membawa masalah, tujuannya sebenarnya baik. Itulah mengapa Microsoft tidak langsung menariknya.

### **Apa yang Sebenarnya Terjadi?**

Singkatnya, Windows Update ini seharusnya bikin sistem lebih stabil dan aman, tapi entah kenapa malah jadi **jebakan batman** buat SSD. Masalahnya muncul saat SSD dipaksa kerja keras, seperti saat kamu lagi:

- **Instal game yang ukurannya bikin nangis** (misalnya **Genshin Impact** yang setiap update-nya makan puluhan GB, atau **Grand Theft Auto V**.)

- **Transfer file besar** (video, folder proyek, dll.).

- **Rendering video** atau kompilasi kode yang butuh penulisan data intensif.

Setelah itu, SSD yang tadinya sehat walafiat mendadak **melambat drastis**, **sistem nge-freeze**, atau yang paling bikin panik, **SSD nggak terdeteksi lagi!** Tentu saja, ini bikin banyak pengguna, terutama para gamer dan _content creator_, kelabakan.

### **Siapa Saja yang Kena Azab?**

Berdasarkan laporan dari berbagai forum, masalah ini nggak menyerang semua SSD. Pelakunya diduga adalah **kontroller Phison**, terutama seri **PS5012-E12** dan beberapa varian lain. Jadi, kalau kamu pakai SSD dengan kontroler ini, sebaiknya hati-hati.

Beberapa merek dan model yang dilaporkan terdampak, antara lain:

- **Corsair Force MP600**

- **Kioxia Exceria Plus G4**

- **SanDisk Extreme Pro M.2 NVMe**

- **Fikwot FN955**

Penting untuk diingat, ini hanya laporan dari pengguna. Microsoft dan Phison sedang melakukan penyelidikan, jadi jangan buru-buru vonis mati SSD-mu ya.

### **Sudah Terlanjur Update? Jangan Panik, Ini Cara Mengatasinya!**

Kalau kamu sudah terlanjur menginstal update **KB5063878** dan merasa ada masalah, kamu bisa mencopotnya (uninstall). Begini caranya:

1. Buka **Settings** (**Pengaturan**) di Windows.

2. Masuk ke **Windows Update**.

3. Klik **Update history** (**Riwayat pembaruan**).

4. Pilih **Uninstall updates** (**Hapus instalan pembaruan**).

5. Cari pembaruan dengan nama **"Security Update for Microsoft Windows (KB5063878)"**.

6. Klik kanan pada pembaruan tersebut dan pilih **Uninstall**.

7. Ikuti petunjuk di layar. Setelah proses selesai, komputer akan restart.

Setelah restart, pembaruan akan dicopot, dan sistemmu akan kembali ke versi sebelumnya. Ini adalah cara paling efektif untuk membatalkan efek negatif dari pembaruan tersebut.

### **Tips Tambahan: Mematikan Auto Update Secara Permanen (Pilihan Ekstrem)**

Jika kamu paranoid dengan auto update Windows dan ingin mengambil kendali penuh, ada cara untuk mematikan fitur ini secara permanen. Namun, perlu diingat, ini adalah **opsi ekstrem** dan ada plus-minus yang harus kamu pertimbangkan.

**Cara Melakukannya:**

1. Buka **Command Prompt (CMD)** sebagai **Administrator**.

2. Masukkan kode berikut ini satu per satu, lalu tekan **Enter** setelah setiap baris:

   ```powershell
   Reg.exe add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoUpdate" /t REG_DWORD /d "0" /f
   Reg.exe add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "AUOptions" /t REG_DWORD /d "2" /f
   Reg.exe add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallDay" /t REG_DWORD /d "0" /f
   Reg.exe add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "ScheduledInstallTime" /t REG_DWORD /d "3" /f
   ```

3. Setelah selesai, ketik `shutdown /r /f /t 0` di CMD untuk me-restart Windows.

**Plus-Minus dari Tips Ini:**

**Plus:**

- **Kendali Penuh:** Kamu bisa memilih kapan dan update apa yang akan diinstal.

- **Aman dari Bug Update:** Kamu terhindar dari pembaruan yang berpotensi merusak hardware atau menimbulkan bug.

- **Fleksibilitas:** Kamu bisa menggunakan software tambahan seperti **WuMgr** (cari di Google ya) untuk mengelola dan menyembunyikan update yang bermasalah.

**Minus:**

- **Risiko Keamanan:** Ini adalah risiko terbesar! Kamu tidak akan menerima pembaruan keamanan otomatis. Jika kamu lalai, sistemmu rentan terhadap serangan **zero-day exploit** dan malware terbaru.

- **Windows Defender:** Beberapa bagian dari Windows Defender (khususnya _malware definition_) mungkin tidak terupdate secara otomatis, sehingga kamu harus melakukannya secara manual.

**Peringatan:** Tips ini tidak disarankan untuk pengguna awam. Ingat, **lebih baik menunda update daripada tidak menginstal update sama sekali**. Lakukan ini hanya jika kamu paham betul risiko yang ada.

### **Kesimpulan**

Meskipun Windows Update **KB5063878** telah menimbulkan keresahan di kalangan pengguna, penting untuk melihat masalah ini secara proporsional. Masalah yang terjadi, meski serius, tidak memengaruhi semua sistem. Fokus utamanya adalah pada SSD dengan **kontroler Phison** yang sedang melakukan operasi penulisan data yang berat.

Dari semua solusi yang ada, cara terbaik untuk menghindari risiko adalah dengan **menunda pembaruan** dan **selalu mencadangkan data penting Anda**. Mencabut pembaruan secara permanen memang memberikan kontrol penuh, tetapi juga membuka pintu terhadap risiko keamanan yang jauh lebih besar. Ingat, pembaruan keamanan bulanan seperti ini sangat krusial untuk melindungi sistem Anda dari serangan siber.

Jadi, daripada panik, ambil langkah-langkah pencegahan yang bijak: **tunda pembaruan selama beberapa minggu**, **hindari tugas berat pada SSD**, dan pastikan Anda memiliki **cadangan data**. Dengan begitu, Anda bisa tenang menunggu perbaikan resmi dari Microsoft, dan SSD Anda pun tetap aman sentosa.

### **Referensi**

[Serious issues regarding KB5063878 and the need for official quick resolution](https://learn.microsoft.com/en-us/answers/questions/5529461/serious-issues-regarding-kb5063878-and-the-need-fo)

[Update issue KB5063878 (OS Build 26100.4946) - Microsoft Q&amp;A](<https://learn.microsoft.com/en-us/answers/questions/5534483/update-issue-kb5063878-(os-build-26100-4946)>)

[Windows 11 Update KB5063878 corrupted my 8TB Seagate drive (RAW, data loss) - Microsoft Q&amp;A](https://learn.microsoft.com/en-us/answers/questions/5534282/windows-11-update-kb5063878-corrupted-my-8tb-seaga)

[Problems with KB5063878 Windows Update](https://learn.microsoft.com/en-us/answers/questions/5527173/problems-with-kb5063878-windows-update)

[Microsoft August 2025 Patch Breaks Reset & Recovery; Emergency Fix Incoming](https://windowsforum.com/threads/microsoft-august-2025-patch-breaks-reset-recovery-emergency-fix-incoming.378139/)

[Microsoft is investigating Windows 11 KB5063878 SSD data corruption/failure issue](https://www.windowslatest.com/2025/08/20/microsoft-is-investigating-windows-11-kb5063878-ssd-data-corruption-failure-issue/)

[Microsoft confirms Windows 11 KB5063878 AutoCAD admin request issue, OBS NDI audio video lag](https://www.windowslatest.com/2025/08/23/microsoft-admits-windows-11-kb5063878-autocad-admin-request-issue-obs-ndi-audio-video-lag/)
