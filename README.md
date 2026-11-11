# Recifind

Recifind adalah web pencarian resep masakan Indonesia berdasarkan bahan. Hasil pencarian diurutkan menurut kemiripan dengan kata kunci memakai konsep Sistem Temu Kembali Informasi (STKI): preprocessing, inverted index, pembobotan TF-IDF, dan cosine similarity.

Dibuat dengan HTML, CSS, dan JavaScript tanpa library. Berjalan langsung di browser, tanpa server dan tanpa database.

> Status: masih dalam pengerjaan. Catatan perkembangan ada di [Laporan.md](Laporan.md).

## Identitas

- Nama: Peter V
- NIM: A11.2023.15322
- Mata kuliah: Sistem Temu Kembali Informasi

## Fitur

- Pencarian resep berdasarkan bahan atau nama masakan
- Hasil terurut dari skor kemiripan tertinggi
- Filter per kategori
- Tampilan bahan dan langkah memasak pada tiap resep
- Panel proses query dan inverted index untuk menjelaskan cara kerja sistem

## Cara kerja singkat

```
Indexing  : Koleksi resep -> Preprocessing -> Inverted index -> Bobot TF-IDF
Pencarian : Query -> Preprocessing -> Vektor query -> Cosine similarity -> Ranking
```

Preprocessing terdiri dari huruf kecil, tokenisasi, dan penghapusan stopword.

## Dataset

Data resep disusun sendiri dan tersimpan di `data/resep.csv`.

| Kolom | Isi |
|---|---|
| id | nomor resep |
| nama | nama masakan |
| kategori | jenis masakan |
| bahan | daftar bahan, dipisah titik koma |
| langkah | langkah memasak bernomor |
| sumber | asal data (disusun sendiri) |

## Struktur repository

```
.
├── index.html            halaman web dan seluruh kode program
├── data/
│   └── resep.csv         dataset resep
├── README.md             penjelasan proyek
└── LAPORAN-PROGRES.md    catatan progres per tahap
```

## Cara menjalankan

Buka file `index.html` di browser.
