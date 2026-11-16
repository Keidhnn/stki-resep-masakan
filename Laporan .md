# Laporan Progres

Proyek: Recifind (STKI Pencarian Resep Masakan)
Nama: Peter V
NIM: A11.2023.15322

Dokumen ini mencatat progres pengerjaan dari tahap ke tahap. Penjelasan umum proyek ada di [README](README.md).

## Laporan Progres 1

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

## Laporan Progres 2

Tahap yang dikerjakan: preprocessing dan inverted index (commit 2).

### Yang dikerjakan

1. **Preprocessing:** huruf kecil (case folding), tokenisasi dengan regex huruf dan angka, lalu penghapusan stopword (dan, di, ke, dari, untuk, dengan, yang, atau).
2. **Teks yang diindeks:** gabungan nama, kategori, dan bahan tiap resep. Langkah memasak tidak diindeks.
3. **Inverted index:** struktur `{ kata: { nomorResep: frekuensi } }`, dibangun sekali saat halaman dibuka.
4. **Tampilan:** panel "Uji preprocessing" untuk mencoba kalimat apa saja, dan tabel inverted index berisi kata, DF, dan resep yang memuatnya.

### Hasil

- 10 resep menghasilkan 124 token setelah preprocessing, dengan 51 kata unik di index.
- Contoh isi index: kata `tempe` DF = 1 (hanya R10, muncul 3 kali), `ayam` DF = 3 (R1, R6, R9), `bawang` DF = 10 (ada di semua resep), dan `putih` DF = 9.
- Contoh uji preprocessing: "Ayam goreng dengan bawang putih dan garam" menjadi `ayam`, `goreng`, `bawang`, `putih`, `garam`. Kata "dengan" dan "dan" dibuang sebagai stopword.
- Pengamatan: kata `bawang` ada di semua resep sehingga tidak membedakan satu resep dari yang lain. Masalah ini ditangani pada tahap berikutnya lewat bobot IDF.

### Kendala dan catatan

- Bahan majemuk seperti "bawang putih" terpecah menjadi dua token (`bawang` dan `putih`).
- Belum ada stemming, jadi bentuk kata berimbuhan dianggap kata berbeda.
- Pencarian belum bisa dipakai karena baru tahap indexing.
- Dataset lengkap tersedia di `data/resep.csv` (26 resep, sumber: disusun sendiri). Versi web pada commit ini masih memakai 10 resep, dan sisanya ditambahkan pada commit perbaikan tampilan.

## Laporan Progres 3

Tahap yang dikerjakan: pembobotan TF-IDF (commit 3).

### Yang dikerjakan

1. **TF:** frekuensi kata dalam satu resep, diambil dari inverted index.
2. **IDF:** log10(1 + N / DF), dengan N = 10 resep dan DF jumlah resep yang memuat kata tersebut.
3. **Bobot:** TF x IDF. Setiap resep menjadi satu vektor (kata dan bobotnya).
4. **Tampilan:** panel "Pembobotan TF-IDF" dengan pilihan resep, yang menampilkan TF, DF, IDF, dan bobot tiap kata.

### Hasil

- Kata yang hanya ada di satu resep (misalnya `tempe`, `gula`, `jagung`) mendapat IDF tertinggi, yaitu 1,041.
- IDF kata lain: `ayam` (DF 3) = 0,637; `telur` dan `cabai` (DF 4) = 0,544; `putih` (DF 9) = 0,325; `bawang` (DF 10, ada di semua resep) = 0,301 sebagai yang terendah.
- Contoh R10 Tempe Orek: kata berbobot tertinggi adalah `tempe` = 3,124 (TF 3 x IDF 1,041). Kata `bawang` walau muncul 2 kali hanya berbobot 0,602.
- Contoh R1 Ayam Goreng Bawang Putih: `ayam` = 1,910 (tertinggi), `goreng` = 1,274, dan `bawang` = 0,602.
- Pengamatan: pembobotan bekerja sesuai tujuan. Kata umum seperti `bawang` bobotnya kecil sehingga tidak mendominasi, sedangkan kata khas resep bobotnya besar.

### Kendala dan catatan

- Teks yang diindeks adalah gabungan nama, kategori, dan bahan. Kata yang muncul di ketiganya (misalnya `tempe`) mendapat TF tinggi, sehingga bobotnya ikut besar. Ini dianggap wajar karena kata tersebut memang inti resep, tetapi perlu dicatat.
- Pencarian belum bisa dipakai karena baru tahap pembobotan.

## Laporan Progres 4

Tahap yang dikerjakan: cosine similarity, ranking, dan kolom pencarian (commit 4).

### Yang dikerjakan

1. **Pemrosesan query:** query melewati preprocessing dan pembobotan yang sama dengan resep.
2. **Cosine similarity:** jumlah perkalian bobot query dan resep dibagi hasil kali panjang kedua vektor. Hanya resep dengan skor lebih dari 0 yang ditampilkan.
3. **Ranking:** hasil diurutkan dari skor tertinggi.
4. **Tampilan:** kolom pencarian, skor kemiripan pada tiap kartu resep, dan pesan bila tidak ada hasil. Bila kolom kosong, semua resep ditampilkan.

### Hasil (koleksi 10 resep)

| Query | Jumlah hasil | Teratas (skor) |
|---|---|---|
| ayam bawang putih | 10 | Ayam Goreng Bawang Putih (0,680), Soto Ayam (0,585), Capcay Sayur (0,268) |
| telur cabai | 6 | Telur Balado (0,613), Mie Goreng Telur (0,248), Nasi Goreng Kampung (0,246) |
| tempe | 1 | Tempe Orek (0,829) |
| bawang putih | 10 | Ayam Goreng Bawang Putih (0,290), Nasi Goreng Kampung (0,283), Telur Balado (0,258) |
| pizza | 0 | tidak ada hasil |

- Pengamatan 1: pada query "ayam bawang putih", dua resep berbahan ayam berada di urutan teratas. Kata `ayam` yang lebih langka memiliki bobot lebih besar daripada `bawang` dan `putih`.
- Pengamatan 2: query bahan umum seperti "bawang putih" menghasilkan skor rendah (tertinggi hanya 0,290) dan seluruh 10 resep muncul. Bahan yang ada di hampir semua resep memang tidak membedakan resep.
- Pengamatan 3: query yang kata-katanya tidak ada di index, seperti "pizza", tidak menghasilkan apa pun dan web menampilkan pesan tidak ada hasil.

### Kendala dan catatan

- Pencocokan masih per kata, jadi resep yang hanya cocok pada sebagian kata query tetap muncul dengan skor lebih rendah.
- Belum ada stemming, sehingga "menggoreng" dan "goreng" dianggap kata berbeda.
- Filter kategori dan penyempurnaan tampilan dikerjakan pada commit berikutnya, bersama penambahan koleksi resep.

## Laporan Progres 5

Tahap yang dikerjakan: perbaikan tampilan dan penambahan resep (commit 5).

### Yang dikerjakan

1. **Koleksi diperluas** dari 10 menjadi 26 resep, dan kategori dari 6 menjadi 12 (ditambah Daging, Sup, Seafood, Camilan, Dessert, dan Minuman). Dataset lengkap tersimpan di `data/resep.csv`.
2. **Tampilan baru:** hero dengan kolom pencarian besar, kartu resep dengan ikon kategori, bar persentase kemiripan, chip bahan yang cocok dengan query disorot, serta mode terang dan gelap yang responsif. Palet warna memakai burgundy dengan aksen emas.
3. **Fitur tambahan:** filter kategori berbentuk tombol, tombol contoh query, kotak statistik, bagian "Bagaimana sistem ini bekerja", serta panel proses query dan inverted index.
4. **Nama web:** Recifind.

### Hasil

- Koleksi akhir: 26 resep, 101 kata unik di index, dan 12 kategori.
- Logika pencarian (preprocessing, TF-IDF, cosine similarity) tidak berubah dari commit 4, sehingga perubahan pada tahap ini hanya pada data dan antarmuka.

### Kendala dan catatan

- Bahan majemuk seperti "bawang putih" masih terpecah menjadi dua kata.
- Belum ada stemming.
- Dengan koleksi yang lebih besar, query bahan umum menghasilkan lebih banyak resep dengan skor rendah dan merata.

## Laporan Progres 6

Tahap yang dikerjakan: evaluasi sistem.

### Yang dikerjakan

1. Menyusun 15 query uji dalam lima jenis: satu bahan, kombinasi bahan, nama masakan, bahan umum saja, dan query tanpa hasil.
2. Menetapkan daftar resep relevan untuk setiap query **sebelum** pengujian, berdasarkan isi bahan dan nama resep. Daftar lengkapnya ada di `evaluasi/evaluasi-recifind.xlsx`.
3. Mengukur Precision@5, Recall@5, dan F1@5 pada 5 hasil teratas. Pengujian dijalankan dengan kode pencarian Recifind yang sama dengan web, dan kode yang setara tersedia di `recifind.ipynb`.

### Hasil

| Query | Jumlah relevan | Relevan di 5 teratas | Precision@5 | Recall@5 | F1@5 |
|---|---|---|---|---|---|
| ayam | 6 | 5 | 1,00 | 0,83 | 0,91 |
| telur | 7 | 5 | 1,00 | 0,71 | 0,83 |
| tempe | 1 | 1 | 0,20 | 1,00 | 0,33 |
| santan | 3 | 3 | 0,60 | 1,00 | 0,75 |
| pisang | 2 | 2 | 0,40 | 1,00 | 0,57 |
| ayam bawang putih | 6 | 5 | 1,00 | 0,83 | 0,91 |
| telur cabai | 3 | 3 | 0,60 | 1,00 | 0,75 |
| kentang telur | 2 | 2 | 0,40 | 1,00 | 0,57 |
| wortel kol | 1 | 1 | 0,20 | 1,00 | 0,33 |
| gula merah santan | 2 | 2 | 0,40 | 1,00 | 0,57 |
| soto | 1 | 1 | 0,20 | 1,00 | 0,33 |
| rendang | 1 | 1 | 0,20 | 1,00 | 0,33 |
| bakso | 1 | 1 | 0,20 | 1,00 | 0,33 |
| bawang putih garam | 3 | 3 | 0,60 | 1,00 | 0,75 |
| **Rata-rata (14 query)** | | | **0,50** | **0,96** | **0,59** |

- Query "pizza" tidak menghasilkan resep apa pun, dan web menampilkan pesan tidak ada hasil. Hasil ini sesuai harapan, dan query ini tidak masuk rata-rata.
- Rata-rata per jenis query (Precision@5, Recall@5, F1@5): satu bahan (5 query) 0,64, 0,91, 0,68; kombinasi bahan (5 query) 0,52, 0,97, 0,63; nama masakan (3 query) 0,20, 1,00, 0,33; bahan umum saja (1 query) 0,60, 1,00, 0,75.

### Pembahasan

1. **Semua resep relevan muncul di 5 teratas.** Pada seluruh 14 query, jumlah resep relevan di 5 teratas sama dengan jumlah resep relevan yang mungkin masuk (paling banyak 5). Tidak ada resep relevan yang tertinggal karena kesalahan ranking.
2. **Nilai Precision@5 dibatasi oleh jumlah resep relevan.** Sebanyak 11 dari 14 query hanya memiliki 1 sampai 3 resep relevan, padahal Precision@5 selalu dibagi 5. Akibatnya, batas maksimum Precision@5 rata-rata untuk kumpulan query ini hanya 0,50, dan hasil sistem (0,50) sama dengan batas itu. Jadi sistem sudah mencapai nilai tertinggi yang mungkin, dan nilai 0,50 tidak menunjukkan kualitas ranking yang rendah.
3. **Ambang awal tidak dapat dicapai.** Ambang keberhasilan Precision@5 sebesar 0,70 pada rancangan awal tidak mungkin dipenuhi oleh kumpulan query ini karena batas maksimumnya 0,50. Berdasarkan kriteria awal, statusnya "belum tercapai". Penyebabnya terletak pada rancangan metrik dan ambang, bukan pada sistem, sehingga ambang ini perlu ditinjau ulang pada penelitian lanjutan, misalnya dengan Precision@R atau dengan query yang memiliki lebih banyak resep relevan.
4. **Recall kurang dari 1 hanya pada tiga query** (ayam, telur, dan ayam bawang putih). Ketiganya memiliki lebih dari 5 resep relevan sehingga sebagian berada di luar 5 teratas. Ini akibat batas 5 hasil, bukan kegagalan menemukan resep.
5. **Jenis query.** Query nama masakan (soto, rendang, bakso) bernilai Precision rendah hanya karena masing-masing punya satu resep relevan, tetapi resepnya selalu ditemukan (Recall 1,00).

### Kesimpulan

TF-IDF dengan cosine similarity mampu memeringkat resep yang relevan pada pencarian berdasarkan bahan: seluruh resep relevan yang mungkin masuk berhasil tampil di 5 teratas, dan query tanpa hasil ditangani dengan benar. Nilai Precision@5 yang tampak rendah dijelaskan oleh batas maksimum metrik, bukan oleh kesalahan sistem.

### Keterbatasan

- Daftar resep relevan disusun penulis berdasarkan isi bahan dan nama resep sehingga bersifat subjektif.
- Koleksi hanya 26 resep dan data disusun sendiri.
- Belum ada stemming, dan bahan majemuk terpecah menjadi dua kata.
- Pengujian belum membandingkan dengan metode lain.

Tabel Progres

| Kegiatan | Status |
|---|---|
| Perancangan sistem | 
| Koleksi 10 resep dan tampilan daftar (commit 1) |
| Preprocessing dan inverted index (commit 2) |
| Pembobotan TF-IDF (commit 3) | 
| Cosine similarity, ranking, dan kolom pencarian (commit 4) | 
| Perbaikan tampilan dan tambahan resep (commit 5) | 
| Evaluasi (Precision, Recall, F1) |

