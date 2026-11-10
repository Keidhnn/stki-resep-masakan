# STKI Pencarian Resep Masakan

Web pencarian resep masakan Indonesia berdasarkan bahan, dibangun dengan konsep Sistem Temu Kembali Informasi (STKI): preprocessing, inverted index, pembobotan TF-IDF, dan perangkingan dengan cosine similarity. Dibuat dengan HTML, CSS, dan JavaScript tanpa library.

## Identitas

- Nama: Peter V
- NIM: A11.2023.15322
- Kelas / Mata kuliah: [4071], Sistem Temu Kembali Informasi

## Laporan Progres 1

Tanggal: 10 November 2026
Tahap yang dikerjakan: perancangan sistem dan koleksi resep.

### 1. Tujuan dan ruang lingkup

- **Tujuan:** membuat web pencarian resep yang mengurutkan hasil berdasarkan kemiripan dengan kata kunci.
- **Masukan:** bahan atau nama masakan, misalnya "ayam bawang putih".
- **Keluaran:** daftar resep terurut dari skor kemiripan tertinggi.
- **Batasan:** koleksi resep statis, bahasa Indonesia, berjalan di browser tanpa server dan tanpa database.

### 2. Kebutuhan sistem

**Fungsional**
- Pengguna dapat mencari resep melalui kolom pencarian.
- Pengguna dapat memfilter hasil per kategori.
- Setiap hasil menampilkan skor kemiripan, bahan, dan langkah memasak.
- Sistem menampilkan proses query dan inverted index sebagai bahan penjelasan.

**Non-fungsional**
- Berjalan langsung di browser tanpa instalasi.
- Tampilan responsif di HP dan komputer.

### 3. Alur sistem

```
TAHAP INDEXING (sekali, saat halaman dibuka)
Koleksi resep -> Preprocessing -> Inverted index -> Bobot TF-IDF -> Vektor tiap resep

TAHAP PENCARIAN (setiap kali pengguna mencari)
Query -> Preprocessing -> Vektor query -> Cosine similarity -> Ranking -> Tampilan hasil
```

Preprocessing: huruf kecil, tokenisasi, dan penghapusan stopword.

### 4. Perancangan data

Struktur satu resep:

```js
{ nama, kategori, bahan: [...], langkah: [...] }
```

Teks yang diindeks adalah gabungan nama, kategori, dan bahan. Langkah memasak hanya untuk ditampilkan.

Struktur inverted index:

```js
{ kata: { nomorResep: frekuensi } }
```

### 5. Perancangan algoritma

- **TF:** jumlah kemunculan kata dalam satu resep.
- **IDF:** log10(1 + N / DF), dengan N jumlah resep dan DF jumlah resep yang memuat kata itu.
- **Bobot:** TF x IDF.
- **Kemiripan:** cosine similarity, yaitu jumlah perkalian bobot query dan resep dibagi hasil kali panjang kedua vektor (nilai 0 sampai 1).

### 6. Perancangan antarmuka

1. Hero berisi judul, kolom pencarian, dan tombol contoh query.
2. Kotak statistik: jumlah resep, jumlah kata unik, dan metode.
3. Tombol filter kategori.
4. Kartu hasil dengan nama, bar kemiripan, chip bahan, dan langkah memasak.
5. Bagian cara kerja serta panel proses query dan inverted index.

### 7. Rencana pengujian

- Uji dengan sekitar 15 query yang bervariasi (satu bahan, kombinasi bahan, nama masakan, bahan umum, dan query tanpa hasil).
- Ukur kualitas dengan Precision@5, Recall@5, dan F1@5.

## Tabel Progres

| Tanggal | Kegiatan | Status |
|---|---|---|
| 10 Nov 2026 | Perancangan sistem | Selesai |
| 10 Nov 2026 | Koleksi 10 resep dan tampilan daftar (commit 1) | Selesai |
| - | Preprocessing dan inverted index | Belum |
| - | Pembobotan TF-IDF | Belum |
| - | Cosine similarity, ranking, dan kolom pencarian | Belum |
| - | Perbaikan tampilan dan tambahan resep | Belum |
| - | Evaluasi (Precision, Recall, F1) | Belum |

## Cara menjalankan

Buka file `index.html` di browser.

## Rencana berikutnya

1. Tambah preprocessing dan inverted index.
2. Tambah pembobotan TF-IDF.
3. Tambah cosine similarity, ranking, dan kolom pencarian.
4. Perbaiki tampilan dan tambah koleksi resep.
5. Lakukan evaluasi dengan query uji.
