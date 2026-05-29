# 🗺️ Analisis Spasial Lokasi Prioritas SPPG Baru Kota Surabaya

**Model pendukung keputusan spasial untuk menentukan kawasan prioritas pembangunan Satuan Pelayanan Pemenuhan Gizi (SPPG) baru di Kota Surabaya menggunakan pendekatan *Multi-Criteria Decision Analysis* berbasis *Analytic Hierarchy Process* dan unit analisis grid 500 meter.**

---

## 📌 Ringkasan Proyek

Repositori ini berisi alur analisis spasial untuk mengidentifikasi **kawasan prioritas pembangunan SPPG baru** di Kota Surabaya. Analisis dilakukan dengan mengintegrasikan data sekolah, jumlah siswa, titik SPPG eksisting, jaringan jalan, serta batas administrasi wilayah ke dalam model **MCDA-AHP berbasis grid 500 m × 500 m**.

Pendekatan ini dikembangkan untuk menghasilkan rekomendasi lokasi yang lebih presisi dibandingkan analisis berbasis batas administrasi kecamatan. Dengan unit grid, variasi spasial kebutuhan layanan dapat dibaca secara lebih detail, terutama pada kawasan dengan konsentrasi sekolah tinggi, demand siswa besar, dan jangkauan SPPG eksisting yang belum optimal.

---

## 🎯 Tujuan Analisis

Analisis ini bertujuan untuk:

1. Menginventarisasi dan memvalidasi data spasial sekolah, SPPG eksisting, jaringan jalan, dan batas administrasi Kota Surabaya.
2. Menghitung **demand gap siswa** berbasis radius pelayanan 3 km pada unit grid 500 m.
3. Menyusun model prioritas lokasi SPPG baru menggunakan pendekatan **MCDA-AHP**.
4. Mengidentifikasi kawasan rekomendasi pembangunan SPPG baru yang dekat dengan sekolah dan tidak berada di badan jalan.
5. Mengestimasi kebutuhan tambahan SPPG baru pada kecamatan prioritas.

---

## 🧭 Wilayah Studi

Wilayah studi dalam analisis ini adalah **Kota Surabaya, Provinsi Jawa Timur**.

Kota Surabaya dipilih karena memiliki kepadatan institusi pendidikan yang tinggi, jaringan jalan yang kompleks, serta kebutuhan layanan gizi peserta didik yang besar. Seluruh data spasial diproyeksikan ke sistem koordinat **EPSG:32749 / UTM Zone 49S** agar perhitungan jarak, luas, buffer, dan grid dilakukan dalam satuan meter.

---

## 📊 Data Utama

Data yang digunakan dalam analisis meliputi:

| Data                    | Fungsi dalam Analisis                                            |
| ----------------------- | ---------------------------------------------------------------- |
| Data SD, SMP, dan SMA   | Menentukan titik sekolah dan jumlah siswa sebagai demand layanan |
| Data SPPG eksisting     | Menentukan jangkauan layanan dan kapasitas eksisting             |
| Data jaringan jalan     | Mengukur aksesibilitas dan menghapus area badan jalan            |
| Data batas administrasi | Membatasi wilayah studi dan agregasi hasil per kecamatan         |
| Data jumlah siswa       | Menghitung demand gap dan estimasi kebutuhan SPPG baru           |

Ringkasan data utama:

| Komponen           |            Nilai |
| ------------------ | ---------------: |
| Total sekolah      |    1.313 sekolah |
| SD                 |      822 sekolah |
| SMP                |      385 sekolah |
| SMA                |      106 sekolah |
| Total siswa        |    409.998 siswa |
| SPPG eksisting     |        140 titik |
| Kapasitas asumsi   | 2.000 siswa/SPPG |
| Grid analisis      |        1.477 sel |
| Resolusi grid      |    500 m × 500 m |
| Luas Kota Surabaya |       335,96 km² |

---

## 🧠 Pendekatan Metode

Model analisis menggunakan pendekatan **MCDA-AHP** dengan lima parameter utama:

| Kode | Parameter                      | Bobot | Makna Utama                                                                      |
| ---- | ------------------------------ | ----: | -------------------------------------------------------------------------------- |
| C1   | Jangkauan SPPG eksisting       |  0,35 | Area yang semakin jauh dari SPPG eksisting memiliki prioritas lebih tinggi       |
| C2   | Demand gap siswa radius 3 km   |  0,30 | Area dengan kekurangan layanan siswa lebih besar memiliki prioritas lebih tinggi |
| C3   | Kepadatan sekolah berbasis KDE |  0,20 | Area hotspot sekolah memiliki prioritas lebih tinggi                             |
| C4   | Rasio layanan kecamatan        |  0,10 | Kecamatan dengan rasio layanan rendah memiliki prioritas lebih tinggi            |
| C5   | Aksesibilitas jalan            |  0,05 | Area dengan akses jalan lebih baik lebih layak direkomendasikan                  |

Rumus skor akhir:

```text
Skor AHP = 0,35(C1) + 0,30(C2) + 0,20(C3) + 0,10(C4) + 0,05(C5)
```

---

## 🧩 Constraint Spasial

Setelah skor AHP dihitung, diterapkan constraint spasial agar kawasan rekomendasi lebih realistis secara operasional.

| Constraint               |                         Nilai | Tujuan                                                    |
| ------------------------ | ----------------------------: | --------------------------------------------------------- |
| Kedekatan dengan sekolah |            Maksimal 750 meter | Memastikan lokasi dekat dengan penerima manfaat           |
| Badan jalan              | Buffer jalan 15 meter dihapus | Menghindari rekomendasi jatuh di atas badan jalan         |
| Minimum luas geometri    |                       >500 m² | Menghapus fragmen geometri kecil yang tidak representatif |

---

## ⚙️ Teknologi dan Library

Analisis dilakukan menggunakan Python dengan beberapa library geospasial dan numerik:

| Library                    | Fungsi                                                        |
| -------------------------- | ------------------------------------------------------------- |
| `pandas`                   | Pengolahan data tabular                                       |
| `geopandas`                | Pengolahan data spasial vektor                                |
| `shapely`                  | Operasi geometri seperti buffer, intersection, dan difference |
| `numpy`                    | Komputasi numerik                                             |
| `scipy.spatial.cKDTree`    | Perhitungan radius siswa dan SPPG secara efisien              |
| `scipy.stats.gaussian_kde` | Estimasi kepadatan sekolah menggunakan KDE                    |
| `matplotlib`               | Visualisasi peta dan grafik                                   |
| `contextily`               | Basemap pendukung visualisasi                                 |
| `pyproj`                   | Transformasi sistem koordinat                                 |

---

## 🔄 Alur Analisis

Secara umum, tahapan analisis dalam proyek ini adalah:

1. Pengumpulan data sekolah, siswa, SPPG eksisting, jalan, dan administrasi.
2. Pembersihan data tabular dan validasi koordinat.
3. Konversi data sekolah dan SPPG menjadi GeoDataFrame.
4. Reproyeksi seluruh data ke **EPSG:32749**.
5. Pembuatan grid 500 m × 500 m sebagai unit analisis.
6. Perhitungan skor C1 melalui multi-ring buffer SPPG eksisting.
7. Perhitungan skor C2 melalui demand gap radius 3 km menggunakan `cKDTree`.
8. Perhitungan skor C3 melalui *Kernel Density Estimation* sekolah.
9. Perhitungan skor C4 melalui rasio layanan kecamatan.
10. Perhitungan skor C5 melalui aksesibilitas jaringan jalan.
11. Penghitungan skor akhir AHP berbobot.
12. Penerapan constraint kedekatan sekolah 750 m.
13. Penghapusan area badan jalan.
14. Ekstraksi kawasan prioritas tinggi sebagai rekomendasi akhir.
15. Estimasi kebutuhan SPPG baru per kecamatan.

---

## 🗺️ Output Analisis

Output utama dari analisis ini meliputi:

* Peta sebaran awal sekolah, jalan, dan SPPG eksisting.
* Peta multi-ring buffer SPPG eksisting 0–3 km.
* Peta skor C1 jangkauan SPPG eksisting.
* Peta skor C2 demand gap siswa radius 3 km.
* Peta skor C3 kepadatan sekolah berbasis KDE.
* Peta prioritas AHP berbasis grid 500 m.
* Peta kawasan rekomendasi pembangunan SPPG baru.
* Grafik estimasi kebutuhan SPPG baru per kecamatan.
* Tabel kebutuhan SPPG baru pada kecamatan prioritas.

---

## 📈 Hasil Utama

Hasil akhir menunjukkan bahwa:

| Komponen                             |        Nilai |
| ------------------------------------ | -----------: |
| Grid sebelum constraint              |    1.477 sel |
| Grid setelah constraint              |    1.197 sel |
| Persentase grid lolos constraint     |        81,0% |
| Luas dalam 750 m dari sekolah        |   260,71 km² |
| Persentase terhadap luas Surabaya    |        77,6% |
| Kawasan Prioritas Tinggi             |       65 sel |
| Kawasan Sangat Prioritas             |        0 sel |
| Total luas rekomendasi akhir         |    11,58 km² |
| Kecamatan dengan kawasan rekomendasi | 14 kecamatan |
| Estimasi kebutuhan SPPG baru         |      56 unit |

Kecamatan dengan kebutuhan tambahan SPPG terbesar adalah:

| Kecamatan   | Estimasi SPPG Baru |   Demand Gap |
| ----------- | -----------------: | -----------: |
| Genteng     |            +8 SPPG | 15.841 siswa |
| Semampir    |            +8 SPPG | 14.938 siswa |
| Sambikerep  |            +6 SPPG | 10.813 siswa |
| Wonokromo   |            +5 SPPG |  8.184 siswa |
| Lakarsantri |            +4 SPPG |  7.932 siswa |

---

## 🧪 Keunggulan Analisis

Beberapa keunggulan pendekatan dalam proyek ini:

* Menggunakan **grid 500 m** sehingga hasil lebih detail dibanding analisis berbasis kecamatan.
* Menghitung demand gap berbasis radius pelayanan 3 km, bukan hanya agregasi administratif.
* Mengintegrasikan **MCDA-AHP** dengan *Kernel Density Estimation* untuk membaca hotspot sekolah.
* Menggunakan constraint spasial agar rekomendasi tidak hanya tinggi secara skor, tetapi juga lebih realistis secara lokasi.
* Menghasilkan rekomendasi dalam bentuk peta, tabel, grafik, dan estimasi kebutuhan unit SPPG baru.

---

## ⚠️ Keterbatasan

Analisis ini masih memiliki beberapa keterbatasan:

1. Kapasitas SPPG diasumsikan seragam sebesar 2.000 siswa per SPPG.
2. Data TK/PAUD belum dimasukkan dalam model final.
3. Aksesibilitas jalan belum dihitung menggunakan waktu tempuh aktual.
4. Constraint lahan masih berbasis penghapusan badan jalan, belum menggunakan data persil atau RDTR.
5. Hasil KDE dapat dipengaruhi oleh pemilihan bandwidth.

---

## 🚀 Rekomendasi Pengembangan

Pengembangan analisis selanjutnya dapat dilakukan dengan:

* Menambahkan data TK/PAUD dan kelompok penerima manfaat lain.
* Menggunakan kapasitas aktual tiap SPPG.
* Mengembangkan analisis aksesibilitas berbasis *network analysis* dan waktu tempuh.
* Mengintegrasikan data RDTR, aset pemerintah, atau persil lahan kosong.
* Melakukan validasi lapangan pada kawasan prioritas tinggi.
* Mengembangkan dashboard WebGIS interaktif untuk eksplorasi hasil.

---

## 📁 Struktur Repositori

```text
.
├── data/
│   ├── raw/                # Data mentah
│   ├── processed/          # Data hasil pra-pemrosesan
│   └── output/             # Data hasil analisis
│
├── maps/                   # Output peta
├── figures/                # Grafik dan visualisasi
├── notebook/
│   └── AnalisisSPPG.ipynb  # Notebook utama analisis
│
├── README.md
└── requirements.txt
```

---

## ▶️ Cara Menjalankan

1. Clone repositori:

```bash
git clone https://github.com/username/nama-repo.git
```

2. Masuk ke folder proyek:

```bash
cd nama-repo
```

3. Buat virtual environment:

```bash
python -m venv .venv
```

4. Aktifkan virtual environment:

```bash
# Windows
.venv\Scripts\activate

# macOS/Linux
source .venv/bin/activate
```

5. Install dependencies:

```bash
pip install -r requirements.txt
```

6. Jalankan notebook:

```bash
jupyter notebook
```

Buka file:

```text
notebook/AnalisisSPPG.ipynb
```

---

## 👤 Author

**Muhammad Raihan Jofaldi**
D4 Sistem Informasi Geografis
Departemen Teknologi Kebumian, Sekolah Vokasi
Universitas Gadjah Mada

---

## 📌 Catatan

Repositori ini disusun sebagai bagian dari analisis spasial berbasis SIG untuk mendukung perencanaan lokasi fasilitas layanan publik. Hasil analisis bersifat akademik dan perlu divalidasi lebih lanjut dengan data kapasitas aktual, data lahan, regulasi tata ruang, serta survei lapangan sebelum digunakan sebagai dasar keputusan implementatif.
