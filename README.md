# Analisis Kebocoran Margin Promo JSM Alfamart

Capstone Project Modul 3 · Purwadhika Business & Data Analyst Bootcamp
**Raihan Hasbi**

## Ringkasan
Promo JSM (Jumat–Sabtu–Minggu) berhasil menaikkan rata-rata transaksi harian **±18%**, tetapi **±Rp3,97 M dari Rp7,98 M (49,8%)** diskonnya jatuh ke pembelian di atas batas wajar rumah tangga. Seluruh kebocoran itu terjadi di **79 gerai Dekat Pasar/Grosir** (7,9% dari 1.003 gerai).

Rekomendasinya bukan menghapus promo, tapi menerapkan **batas maksimal 12 pcs per SKU promo per transaksi** saat JSM, dimulai dari gerai dekat pasar. Potensi hemat hingga **±Rp1,21 M per tahun**, dan hanya **3,4%** transaksi pelanggan rumah tangga yang ikut terkena.

## Problem Statement
Manajemen menduga ada kebocoran margin dari Promo JSM: barang promo dibeli dalam jumlah besar dengan pola non-rumah-tangga (reseller atau pengecer kecil), sehingga subsidi diskon tidak sampai ke pelanggan target.

**Tujuan:**
1. Mengukur besaran kebocoran dalam rupiah.
2. Merancang batas qty pembelian untuk SKU promo.

**Pertanyaan bisnis:**
1. Apakah Promo JSM mengubah pola belanja?
2. Apakah diskon JSM terbagi merata atau terkonsentrasi?
3. Berapa rupiah diskon yang jatuh ke pembelian di atas batas wajar rumah tangga?
4. Di mana dan oleh siapa pola borongan terjadi?
5. Batas qty berapa yang paling seimbang antara penghematan dan dampak ke pelanggan biasa?

## Data
Database `alfamart` di server MySQL Purwadhika, 5 tabel, periode **Jan 2021 – Agu 2025**.

| Tabel | Jumlah baris |
|---|---|
| `alfa_pos_transactions` | 455.148 |
| `alfa_stores` | 1.003 gerai (761 perumahan, 163 perkantoran, 79 dekat pasar) |
| `alfa_products` | 507 SKU, 4 kategori |
| `alfa_members` | 48.214 |
| `alfa_complaint_tickets` | 10.759 |

Kredensial server disimpan di **Colab Secrets** dan tidak ada di notebook maupun repo ini. Karena server tidak selalu bisa diakses, notebook membaca ulang data dari CSV hasil ekstraksi di Google Drive (data mentah tidak disertakan di repo).

## Data Cleaning
455.148 baris → **430.705 baris bersih (94,6%)**

| Masalah | Tindakan | Baris |
|---|---|---|
| Transaksi VOID | Dipisahkan | 14.024 |
| Qty ≤ 0 atau > 500 | Dibuang (human error) | 1.762 |
| Transaksi sebelum gerai buka | Dibuang | 8.657 |
| `payment_method` tidak seragam | Diseragamkan 10 → 6 | – |
| `product_name` tidak seragam | Diseragamkan 20 → 12 | – |
| `member_id` kosong (±34%) | Dibiarkan, bukan error | – |

## Temuan Utama
- **Q1:** Qty SKU promo saat JSM naik signifikan (median 2 → 5 pcs, Mann-Whitney p < 0,001). Transaksi harian naik ±18%.
- **Q2:** 20% transaksi dengan qty terbesar menyerap **63,5%** diskon JSM.
- **Q3:** **Rp3,97 M (49,8%)** diskon jatuh ke unit di atas 24 pcs (p90 qty hari biasa).
- **Q4:** 100% kebocoran ada di **79 gerai Dekat Pasar/Grosir**. Median qty di sana naik dari 26 ke 48 pcs saat JSM, sementara gerai lain tetap 2 pcs. Penyumbangnya bukan segelintir orang (11.395 member).

## Simulasi Batas Qty

| Batas | Diskon terselamatkan | Per tahun | Pelanggan biasa terdampak | Penilaian |
|---|---|---|---|---|
| 2 pcs | Rp7,34 M (91,9%) | Rp1,57 M | 40,9% | Terlalu ketat |
| **12 pcs** | **Rp5,65 M (70,7%)** | **Rp1,21 M** | **3,4%** | **Rekomendasi** |
| 24 pcs | Rp3,97 M (49,8%) | Rp0,85 M | 0% | Terlalu longgar |

## Rekomendasi
1. Batas maks **12 pcs per SKU promo per transaksi** saat JSM, mulai dari 79 gerai dekat pasar.
2. Batas akumulasi per `member_id` per periode JSM untuk mencegah pemecahan struk.
3. Skema harga grosir terpisah untuk pedagang di gerai dekat pasar.
4. Monitoring 1 kuartal dan validasi input di POS.

## Keterbatasan
- Tidak ada data HPP, jadi kebocoran diukur dari nilai diskon (proxy), bukan margin bersih.
- Harga satuan untuk produk yang sama tidak konsisten (Rp3.200–Rp44.800).
- Angka hemat adalah batas atas: belum memperhitungkan pembeli yang memecah struk atau pindah toko.
- Pola data sangat rapi, indikasi data simulasi. Temuan perlu divalidasi dengan data riil.

## Isi Repo
| File | Keterangan |
|---|---|
| `Capstone3_Alfamart_JSM.ipynb` | Notebook lengkap: extract, cleaning, analisis, uji statistik, simulasi, visualisasi |
| `README.md` | Ringkasan proyek |

## Link
- Slide presentasi & PDF dashboard (Google Drive): https://drive.google.com/drive/folders/1uTedAGL9KHe5j7TVnKfYGnNfw3NQyrkJ
- Dashboard Looker Studio: https://datastudio.google.com/reporting/5bb45aeb-ec21-42f6-ba2f-ac096034fa31

## Tools
Python (pandas, numpy, scipy, matplotlib), Google Colab, MySQL (pymysql), Looker Studio, Canva
