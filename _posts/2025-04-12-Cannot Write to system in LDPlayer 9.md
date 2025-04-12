---
author: Ilham
title: "Fix: Cannot write to /system in LDPlayer 9 (Read-only File System)"
categories: ldplayer
tags: [adb, ldplayer, read-only-filesystem, system-partition]
date: 2025-04-12
summary: 'Fix "Read-only file system" error in LDPlayer 9 when writing to /system. Step-by-step solution to remount /system with write access on Android emulator.'
key: 2025-04-12-troubleshoot-1
---

### Permasalahan

Saat mencoba menyalin atau mengedit file di direktori `/system` pada emulator **LDPlayer 9**, saya mengalami error berikut:

```shell
# mv /sdcard/9a5ba575.0 /system/etc/security/cacerts
mv: /system/etc/security/cacerts/9a5ba575.0: Read-only file system
```

Padahal pengaturan **Root Permission** sudah diaktifkan di emulator, namun tetap tidak bisa menyalin file ke partisi `/system`. Hal ini terjadi karena partisi tersebut masih berada dalam mode **read-only** secara default.

### Mengapa ini terjadi?

Secara default, sistem Android (termasuk emulator seperti LDPlayer) melakukan **mount** terhadap direktori `/system` dalam mode `ro` (read-only) untuk menjaga integritas file sistem. Ini adalah langkah keamanan agar pengguna atau aplikasi tidak sembarangan mengubah file penting.

Untuk bisa memodifikasi direktori ini, kita harus **remount** partisi tersebut dalam mode `rw` (read-write), agar sistem mengizinkan perubahan.

### Apa Itu `setprop persist.sys.root_access 1`?

`setprop` adalah perintah di sistem Android yang digunakan untuk mengatur *system properties* secara runtime. Salah satu properti penting terkait akses root adalah:

```shell
setprop persist.sys.root_access 1
```

- `persist.sys.root_access`: properti ini mengontrol **akses root pada sistem Android**.

- Nilai `1` berarti akses root diaktifkan.

- Kata `persist` menunjukkan bahwa properti ini **seharusnya disimpan** secara permanen (persisten), dan tetap aktif meskipun perangkat di-reboot.


Namun, **pada emulator seperti LDPlayer**, properti ini **tidak selalu benar-benar bersifat persisten** setelah reboot. Hal ini kemungkinan disebabkan oleh:

- Emulator melakukan *reset* terhadap sistem properties saat boot.

- File `build.prop` atau `default.prop` tidak dapat dimodifikasi permanen.

- Tidak adanya skrip seperti `init.d` untuk menyetel ulang secara otomatis saat startup.


### Kenapa Perlu Menjalankan `setprop` Ulang Setelah Reboot?

Walaupun sudah menggunakan `setprop persist.sys.root_access 1`, kenyataannya:

- Akses root tidak otomatis aktif sepenuhnya setelah reboot.

- Partisi `/system` akan kembali dimount dalam mode `ro`.

- Anda tetap perlu menjalankan perintah `setprop` **secara manual setiap kali emulator di-reboot**, agar dapat melakukan remount sebagai `rw`.


Jadi, untuk membuat `/system` bisa ditulis (writeable) di LDPlayer 9:

1. Aktifkan kembali akses root dengan `setprop persist.sys.root_access 1`.

2. Remount partisi dengan `mount -o remount,rw /`.


Meskipun menggunakan properti `persist`, emulator kemungkinan **tidak menyimpan** status ini secara permanen. Oleh karena itu, kamu perlu menjalankan perintah ini **setiap kali boot ulang emulator**.

### Apa Itu `ro` dan `rw`?

Dalam konteks sistem Linux (termasuk Android), saat melakukan `mount`, ada dua mode umum:

- `ro` (**Read-Only**): hanya bisa membaca file/folder, tidak bisa mengubah isinya.

- `rw` (**Read-Write**): bisa membaca dan menulis file/folder.


Jika sistem dimount dalam mode `ro`, semua upaya untuk menyalin atau memodifikasi file akan gagal. Oleh karena itu, kita harus melakukan **remount** menggunakan mode `rw`.

### Solusi

1. Aktifkan Akses Root Persisten
  Hubungkan LDPlayer dengan ADB (bisa dari terminal atau command prompt), lalu jalankan:
  ```shell
  $ adb shell
  $ su
  # setprop persist.sys.root_access 1
  ```

2. Remount Sistem dengan Akses Tulis
  Setelah akses root aktif, lakukan remount terhadap partisi root:
  ```shell
  # mount -o remount,rw /
  ```

3. Untuk memastikan partisi sudah berhasil dimount dalam mode writeable:
  ```shell
  # mount | grep system
  ```
  Jika hasil output menunjukkan flag `rw`, berarti partisi `/system` telah berhasil dimount dengan akses tulis.


Untuk studi kasus dan demonstrasi lengkapnya, silakan lihat video di bawah ini:

<div>{%- include extensions/vimeo.html id='1074541239' hash='ab7d4823b2' -%}</div>

### Kesimpulan

Masalah "Read-only file system" di LDPlayer 9 dapat diatasi dengan dua langkah utama:

1. Mengaktifkan akses root menggunakan `setprop persist.sys.root_access 1`.

2. Melakukan remount partisi root ke mode `rw`.


Namun, perlu diingat bahwa LDPlayer tidak selalu mempertahankan properti `persist` setelah reboot. Maka dari itu, perintah `setprop` kemungkinan besar perlu dijalankan ulang setiap kali emulator dinyalakan.

**Catatan**: Mengedit partisi sistem bisa menyebabkan **kerusakan atau crash** jika dilakukan tanpa pengetahuan yang cukup. Selalu backup file penting sebelum memodifikasi sistem.
{:.warning}

Jika kamu mengalami kendala saat mengikuti langkah-langkah di atas, silakan tinggalkan komentar atau kirim pesan langsung. Terima kasih sudah membaca!
