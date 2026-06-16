# WASTE-TRACK

**Sistem Manajemen Setoran Sampah Berbasis Terminal**

WASTE-TRACK adalah aplikasi command-line berbasis Go untuk mencatat, mengelola, dan menganalisis data setoran sampah warga. Program ini dirancang untuk memudahkan pengelolaan bank sampah atau unit pengelolaan sampah komunitas secara terstruktur dan efisien.

---

## Daftar Isi

- [Fitur](#-fitur)
- [Struktur Data](#-struktur-data)
- [Cara Menjalankan](#-cara-menjalankan)
- [Panduan Penggunaan](#-panduan-penggunaan)
  - [1. Manajemen Data Warga](#1-manajemen-data-warga)
  - [2. Catat Setoran Sampah](#2-catat-setoran-sampah)
  - [3. Cari Data Warga](#3-cari-data-warga)
  - [4. Urutkan Data Warga](#4-urutkan-data-warga)
  - [5. Tampilkan Statistik](#5-tampilkan-statistik)
- [Algoritma yang Digunakan](#-algoritma-yang-digunakan)
- [Contoh Alur Penggunaan](#-contoh-alur-penggunaan)
- [Catatan Teknis](#-catatan-teknis)

---

## Fitur

| No | Fitur | Deskripsi |
|----|-------|-----------|
| 1 | **Manajemen Data Warga** | Tambah, lihat, edit, dan hapus data warga |
| 2 | **Catat Setoran Sampah** | Mencatat transaksi setoran sampah per warga dengan tanggal dan berat |
| 3 | **Pencarian Warga** | Cari warga berdasarkan nama (Sequential Search) atau ID (Binary Search) |
| 4 | **Pengurutan Data** | Urutkan data warga berdasarkan total berat sampah (Ascending/Descending) |
| 5 | **Statistik** | Tampilkan rekapitulasi setoran berdasarkan minggu, bulan, atau tahun |

---

## Struktur Data

Program menggunakan dua struct utama:

```go
// Menyimpan satu transaksi setoran sampah
type Transaksi struct {
    date  string   // Format: DD-MM-YYYY
    jenis string   // Jenis sampah (plastik, kertas, dll.)
    berat float64  // Berat dalam kilogram
}

// Menyimpan data satu warga
type warga struct {
    name       string       // Nama warga
    id         int          // ID unik warga
    setoran    []Transaksi  // Riwayat transaksi
    jumlahLog  int          // Total jumlah transaksi
    totalBerat float64      // Akumulasi total berat (kg)
}
```

Selain itu, program memiliki dua variabel global:
- `dataWarga []warga` — slice berisi seluruh data warga yang terdaftar
- `jenisSampah []string` — slice berisi jenis-jenis sampah yang pernah disetorkan

---

## Cara Menjalankan

### Prasyarat

- [Go](https://golang.org/dl/) versi 1.18 atau lebih baru sudah terinstal

### Langkah-langkah

```bash
# 1. Clone atau unduh file program
git clone https://github.com/7bryan/Tugas-Besar-Alpro2.git
cd Tugas-Besar-Alpro2

# 2. Jalankan program
go run main.go

# Atau build terlebih dahulu
go build -o waste-track main.go
./waste-track
```

Setelah dijalankan, menu utama akan tampil di terminal.

---

## Panduan Penggunaan

### Menu Utama

```
=========== WASTE-TRACK ===========
|1. Manajemen Data Warga          |
|2. Catat Setoran Sampah          |
|3. Cari Data Warga               |
|4. Urutkan Data Warga            |
|5. Tampilkan Statistik           |
|9. Exit                          |
===================================
```

---

### 1. Manajemen Data Warga

Submenu untuk operasi CRUD (Create, Read, Update, Delete) pada data warga.

```
=== MANAJEMEN DATA WARGA ===
|1. Tampilkan Data Warga   |
|2. Tambah Warga           |
|3. Edit Data Warga        |
|4. Hapus Data Warga       |
|7. Exit                   |
============================
```

#### Tambah Warga
- Masukkan **ID** berupa angka unik
- Program akan menolak ID yang sudah terdaftar
- Masukkan **Nama** warga

#### Edit Warga
- Masukkan ID warga yang ingin diedit
- Masukkan nama baru untuk warga tersebut
- Hanya nama yang dapat diubah; ID bersifat permanen

#### Hapus Warga
- Masukkan ID warga yang ingin dihapus
- Program akan menampilkan konfirmasi data sebelum menghapus
- Data warga beserta seluruh riwayat setorannya akan dihapus

---

### 2. Catat Setoran Sampah

Digunakan untuk mencatat transaksi setoran sampah dari seorang warga.

**Input yang dibutuhkan:**
1. **ID Warga** — warga harus sudah terdaftar
2. **Jenis Sampah** — pilih dari daftar yang ada, atau ketik nama baru untuk menambahkan jenis sampah baru
3. **Berat Sampah** — dalam satuan kilogram (kg), mendukung desimal
4. **Tanggal** — format input: `DD MM YYYY` (dipisah spasi)

> **Contoh input tanggal:** `15 06 2026`
> Program akan menyimpannya sebagai `15-06-2026`

Setelah dicatat, `jumlahLog` dan `totalBerat` warga akan diperbarui secara otomatis.

---

### 3. Cari Data Warga

Tersedia dua metode pencarian:

#### Cari Berdasarkan Nama — Sequential Search
- Menelusuri seluruh data warga dari awal hingga akhir
- Mendukung **multiple result** (jika ada nama yang sama)
- Input harus **sama persis** dengan nama yang tersimpan (case-sensitive)

#### Cari Berdasarkan ID — Binary Search
- Menyalin data warga ke array sementara, lalu mengurutkannya berdasarkan ID
- Menggunakan algoritma Binary Search untuk pencarian yang lebih efisien
- Data asli **tidak terpengaruh** oleh pengurutan sementara ini

---

### 4. Urutkan Data Warga

Mengurutkan data warga berdasarkan **total berat sampah yang disetorkan**.

| Pilihan | Algoritma | Urutan |
|---------|-----------|--------|
| 1 | Selection Sort | Ascending (terkecil → terbesar) |
| 2 | Insertion Sort | Descending (terbesar → terkecil) |

> Pengurutan ini **mengubah urutan data asli** (`dataWarga`). Setelah diurutkan, data akan ditampilkan langsung.

---

### 5. Tampilkan Statistik

Menampilkan rekapitulasi setoran sampah berdasarkan periode waktu.

#### Filter yang Tersedia:

**Minggu** — Input: nomor minggu (1–4), bulan (1–12), dan tahun
- Minggu 1: tanggal 1–7
- Minggu 2: tanggal 8–14
- Minggu 3: tanggal 15–21
- Minggu 4: tanggal 22–31

**Bulan** — Input: bulan (1–12) dan tahun

**Tahun** — Input: tahun (contoh: 2026)

#### Output yang Dihasilkan:
1. **Detail Log Transaksi** — semua transaksi dalam periode tersebut, dikelompokkan per tanggal
2. **Statistik Akumulasi** — total berat per jenis sampah
3. **Total Keseluruhan** — total berat semua sampah dalam periode

**Contoh output:**
```
====== DETAIL LOG TRANSAKSI (Bulan 06-2026) ======

15-06-2026:
1. Warga: Budi, Jenis: Plastik, Berat: 2.50 kg
2. Warga: Sari, Jenis: Kertas, Berat: 1.80 kg

10-06-2026:
1. Warga: Budi, Jenis: Kertas, Berat: 3.00 kg
===============================================

Statistik Akumulasi Sampah Bulan 06-2026:
- Sampah Plastik: 2.50 kg
- Sampah Kertas: 4.80 kg
TOTAL KESELURUHAN: 7.30 kg
```

---

## Algoritma yang Digunakan

### Sequential Search
```
Fungsi: sequentialSearch(query string)
- Iterasi seluruh elemen dataWarga
- Bandingkan nama warga dengan query
- Tampilkan semua warga dengan nama yang cocok
- Kompleksitas waktu: O(n)
```

### Binary Search
```
Fungsi: binarySearch(queryID int)
- Salin dataWarga ke slice sementara
- Urutkan slice sementara berdasarkan ID (Selection Sort)
- Terapkan Binary Search
- Kompleksitas waktu: O(n log n) untuk sort + O(log n) untuk search
```

### Selection Sort
```
Fungsi: selectionSort()
- Untuk setiap posisi i, cari elemen minimum dari i+1 hingga akhir
- Tukar elemen minimum dengan posisi i
- Mengurutkan berdasarkan totalBerat secara Ascending
- Kompleksitas waktu: O(n²)
```

### Insertion Sort
```
Fungsi: insertionSort()
- Ambil elemen ke-i sebagai key
- Geser elemen-elemen sebelumnya yang lebih kecil dari key ke kanan
- Sisipkan key pada posisi yang tepat
- Mengurutkan berdasarkan totalBerat secara Descending
- Kompleksitas waktu: O(n²)
```

---

## Contoh Alur Penggunaan

Berikut contoh skenario penggunaan lengkap:

```
1. Tambah warga baru:
   → Menu 1 → Pilih 2 → ID: 101 → Nama: Budi

2. Catat setoran sampah:
   → Menu 2 → ID: 101 → Jenis: Plastik → Berat: 2.5 → Tanggal: 15 06 2026

3. Cari data warga:
   → Menu 3 → Pilih 1 (nama) → Ketik: Budi

4. Tampilkan statistik bulan ini:
   → Menu 5 → Pilih 2 (bulan) → Bulan: 6 → Tahun: 2026
```

---

## Catatan Teknis

- **Data tidak persisten** — seluruh data tersimpan di memori program. Setelah program ditutup, data akan hilang. Untuk persistensi data, diperlukan integrasi dengan file (JSON/CSV) atau database.
- **Input nama** — `fmt.Scan` membaca input hingga spasi pertama. Nama dengan spasi (contoh: "Budi Santoso") hanya akan terbaca sebagai "Budi".
- **Jenis sampah bersifat global** — daftar jenis sampah berlaku untuk semua warga dan tidak bisa dihapus setelah ditambahkan.
- **ID bersifat unik dan permanen** — ID tidak bisa diubah setelah warga didaftarkan.

---

## Teknologi

- **Bahasa:** Go (Golang)
- **Library:** Hanya menggunakan package standar `fmt`
- **Platform:** Cross-platform (Windows, Linux, macOS)

---

*Dibuat sebagai Tugas Besar Algoritma dan Pemrograman*
