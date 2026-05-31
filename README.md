# Ryota Encryption - Advanced Python Obfuscator

Tool enkripsi dan proteksi bytecode Python tingkat lanjut yang dilengkapi dengan multi-layered compression, AES-256-CBC, polymorphic chunking, dan proteksi aktif terhadap runtime injection (anti-hook).

**Author:** RyotaXD

**Facebook:** https://www.facebook.com/ryotaaaxd0

---

## Deskripsi
script ini dirancang khusus untuk melindungi source code Python dari dekompilasi statis (seperti uncompyle6/decompyle3) dan analisis memori secara runtime (runtime interceptor/decoder script). 
Berbeda dengan obfuscator biasa yang cuma mengubah nama variabel atau meng-encrypt string, tool ini merombak struktur skrip menjadi objek bytecode terkompilasi, mengacak urutan datanya di memori, dan memasang jebakan aktif untuk mendeteksi apakah fungsi eksekusi Python asli sedang dimanipulasi atau di-hook oleh pihak ketiga.

---

## Fitur Utama

* **TUI Interactive Menu**: Menggunakan `inquirer` untuk mempermudah pemilihan target file, jumlah layer kompresi, dan input pesan jebakan langsung lewat navigasi panah terminal.

* **Multi-Layered Polymorphic Compression**: Memanfaatkan kombinasi modul `zlib` dan `lzma` yang diputar secara acak bersama encoding biner (`base64`, `base32`, `base16`) hingga puluhan layer sesuai konfigurasi.

* **AES-256-CBC Encryption**: Mengamankan serialisasi bytecode (`marshal.dumps`) menggunakan enkripsi simetris AES 256-bit. Key dan Initialization Vector (IV) di-generate secara acak setiap kali enkripsi dilakukan.

* **Payload Chunk Shuffling**: Payload biner dipecah menjadi potongan-potongan kecil dengan panjang variabel, kemudian indeksnya diacak ke dalam dictionary mapping. Teknik ini mematahkan analisis statis seperti pencarian teks mentah via `grep` atau utility `strings`.

* **Active Anti-Decode Hard Trap**: Sistem proteksi runtime yang memeriksa integritas objek `builtins.exec`. Jika skrip mendeteksi adanya interceptor atau fungsi `exec` yang telah dimodifikasi oleh tool decoder:
  * Skrip otomatis mem-bypass standard output dan langsung menulis pesan peringatan kustom ke terminal asli lewat `sys.__stdout__.write`.
  * Proses langsung dimatikan paksa di tingkat kernel menggunakan `os._exit(0)`, sehingga tidak bisa digagalkan atau di-bypass menggunakan error handling seperti `try-except` atau `SystemExit`.

---

## Alur Kerja Proteksi

1. **Bytecode Compilation**: Source code asli dikompilasi menjadi objek kode internal Python lalu diserialisasi menggunakan modul `marshal`.
2. **Layered Obfuscation**: Hasil serialisasi dikompresi berulang kali lewat tumpukan algoritma yang dipilih acak berdasarkan jumlah layer.
3. **Kriptografi**: Lapisan terdalam di-encrypt total menggunakan algoritma AES-256-CBC.
4. **Stub Generation**: Kode akhir (stub) disusun menggunakan teknik polimorfisme karakter serupa (`_l1Ii...`) untuk menghancurkan keterbacaan kode secara manual. Semua fungsi import sensitif disamarkan ke dalam bentuk biner `chr()` dinamis.

---

## Instalasi & Penggunaan

### Dependensi
Pastikan modul yang dibutuhkan sudah terpasang di environment Python / Termux Anda:

```bash
pip install pycryptodome inquirer
git clone https://github.com/RyotaXD/encryption-python3
python encryption.py
