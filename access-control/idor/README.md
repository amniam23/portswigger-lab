# IDOR Write-Up — PortSwigger Access Control (IDOR)

Dokumentasi ini menjelaskan langkah-langkah eksploitasi IDOR (Insecure Direct Object References) pada salah satu lab Access Control di PortSwigger Web Security Academy. Seluruh proses dilakukan menggunakan MITM Proxy untuk menganalisis, memodifikasi, dan mengirim ulang HTTP request.

---

## Pendahuluan

IDOR adalah kelemahan yang muncul ketika aplikasi memberikan akses ke sebuah resource berdasarkan parameter yang dapat dimanipulasi pengguna, tanpa melakukan pengecekan otorisasi. Pada lab ini, targetnya adalah membaca file milik pengguna lain dan menggunakan informasi tersebut untuk mengambil alih akun.

---

## Tampilan Awal Lab

Screenshot berikut menunjukkan kondisi awal sebelum eksploitasi dimulai.

![Home Lab](images/idor_homelab.jpg)

Lab menyediakan deskripsi dan tujuan utama eksploitasi.

![Task](images/idor_task.jpg)

---

## Menyiapkan MITM Proxy

MITM Proxy digunakan untuk memonitor dan mengubah request yang dikirimkan browser. Dengan intercept ini, perubahan pada parameter path atau file dapat dilakukan secara langsung.

![MITM Terminal](images/idor_terminalmitm.jpg)

---

## Mengamati Endpoint yang Relevan

Saat halaman diakses, beberapa fitur seperti live chat dan pengiriman file memunculkan request yang mengarah ke path tertentu. Dari sinilah pola IDOR dapat dideteksi.

![Live Chat](images/idor_livechat.jpg)
![Send Image](images/idor_sendimage.jpg)

Pada tahap tertentu, sistem menampilkan akses terhadap file transkrip.

![View Transcript](images/idor_umazingviewtrascript.jpg)
![Transcript 2.txt](images/idor_2.txt_viewtranscript.jpg)
![URL 2.txt](images/idor_2.txt.url.jpg)

Dari sini terlihat bahwa file menggunakan pola numerik sederhana seperti `1.txt`, `2.txt`, dan seterusnya.

---

## Eksplorasi Path dan Manipulasi ID

Setelah pola file ditemukan, langkah selanjutnya adalah menguji variasi path untuk memastikan apakah server melakukan verifikasi otorisasi atau tidak.

![e-part](images/idor_e-part.jpg)
![4 Path](images/idor_e_part_4path.jpg)

Dengan MITM Proxy, path dapat diubah secara langsung sebelum dikirimkan ke server.

![Path Edit](images/idor_path_2-1.txt.jpg)
![Escape Edit](images/idor_edit1.txt_esc.jpg)

---

## Mengakses File Lain dan Mendapatkan Data Sensitif

Setelah mengubah ID file, permintaan berhasil diproses dan file milik pengguna lain dapat diakses.

![1.txt](images/idor_1.txt.jpg)
![Pra Edit](images/idor_pra1txt.jpg)

Response setelah eksploitasi menampilkan isi file yang sebelumnya tidak boleh diakses.

![Response Body](images/idor_pasca1txt_RESPONSE.jpg)
![Body Extract](images/idor_e_pathresponsebody.jpg)

Pada titik tertentu, file yang mengandung password pengguna lain dapat dibaca.

![Password](images/idor_password.jpg)

---

## Mengambil Alih Akun

Setelah mendapatkan kredensial dari file yang terekspos, login ke akun pengguna lain dapat dilakukan.

![My Account](images/idor_homemyacc.jpg)
![Login](images/idor_login.jpg)

Eksploitasi dinyatakan berhasil ketika sistem memberikan halaman konfirmasi.

![Congrats](images/idor_congrats.jpg)

---

## Analisis Teknis

1. Aplikasi tidak memvalidasi apakah pengguna yang meminta file benar-benar pemilik file tersebut.
2. File dapat diakses hanya dengan memodifikasi parameter numerik pada path.
3. Tidak ada mekanisme session binding atau permission checking pada resource tersebut.
4. Hal ini memungkinkan attacker mem-bypass access control dan membaca file milik pengguna lain.
5. Data sensitif dalam file dapat digunakan untuk mengambil alih akun.

---

## Pelajaran yang Didapat

- Server harus selalu memverifikasi otorisasi, bukan hanya autentikasi.
- Path berbasis angka atau file langsung sangat berisiko jika tidak dilengkapi pengecekan permission.
- MITM Proxy dapat menjadi alat efektif untuk mempelajari struktur request dan menemukan parameter yang rentan.
- Data sensitif tidak seharusnya disimpan dalam file yang dapat diakses langsung lewat URL.

---

## Disclaimer

Write-up ini dibuat semata-mata untuk tujuan pembelajaran dan pengembangan kemampuan keamanan aplikasi web. Eksploitasi terhadap sistem nyata tanpa izin adalah pelanggaran hukum.

---

## Penulis

M. Khusnun Ni'am

