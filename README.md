# 💊 Kimia Farma - Margin Aman, Pertumbuhan Nol

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white)](https://linkedin.com/in/ghifarryalnaffriza)
[![GitHub](https://img.shields.io/badge/GitHub-181717?logo=github&logoColor=white)](https://github.com/ghifarryalnaffriza)
[![Instagram](https://img.shields.io/badge/Instagram-E4405F?logo=instagram&logoColor=white)](https://instagram.com/ghifaralnaffi_)
[![Email](https://img.shields.io/badge/Email-D14836?logo=gmail&logoColor=white)](mailto:ghifaralns@gmail.com)

> Analisis kinerja bisnis Kimia Farma 2020–2023: nett profit Rp 91,2 miliar dengan margin stabil 28,4%, tapi pendapatan **tidak tumbuh empat tahun berturut-turut**. Masalahnya bukan efisiensi — tapi struktur.

## 🎯 Business Problem

Kimia Farma mencatat margin yang rapi dan tidak tergerus diskon, tapi nett sales mandek di kisaran Rp 80 miliar per tahun selama empat tahun (growth YoY: −0,5%, +0,7%, −0,6%). Pertanyaan intinya: **dari mana profit Rp 91,2 miliar berasal, dan di mana ia berhenti bertumbuh?** Dikerjakan sebagai final task Rakamin VIX — Big Data Analytics.

## 📊 Dataset

- **Sumber:** 4 tabel Kimia Farma — `kf_final_transaction`, `kf_inventory`, `kf_kantor_cabang`, `kf_product`
- **Volume:** 672.458 transaksi · 1.035.000 baris inventory · 1.725 cabang · 150 produk
- **Cakupan:** 2020–2023 · 31 provinsi · 3 format cabang (Klinik & Apotek, Apotek, Klinik)
- **Integritas relasi:** 100% bersih — 0 orphan pada join transaction ↔ branch ↔ product

## 🏗️ Arsitektur & Pendekatan

Medallion architecture di Google BigQuery (Standard SQL):

- **Bronze** — raw import 4 CSV apa adanya via BigQuery UI, disalin ke tabel `bronze_*` tanpa transformasi + metadata `_ingested_at`.
- **Silver** — cleansing & standardisasi: TRIM teks, parse tanggal `M/D/YYYY` → `DATE`, validasi rentang (price > 0, discount 0–1, rating 0–5), flag QC `is_valid` **tanpa membuang baris** demi transparansi.
- **Gold** — tabel analisa utama `kf_analysis` (tier `persentase_gross_laba` berbasis harga) + ~14 view agregat siap-dashboard: YoY, tier harga, dampak diskon, gap rating, geo profit provinsi, efisiensi inventory.

Pemisahan layer bikin logika QC terisolasi dari logika analitik — setiap angka di dashboard bisa dilacak balik ke transformasi Silver yang eksplisit.

## 🛠️ Tools & Tech

![BigQuery](https://img.shields.io/badge/Google_BigQuery-4285F4?style=for-the-badge&logo=googlebigquery&logoColor=white)
![Standard SQL](https://img.shields.io/badge/Standard_SQL-4479A1?style=for-the-badge&logo=googlecloud&logoColor=white)
![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Medallion](https://img.shields.io/badge/Medallion_Architecture-4B8BBE?style=for-the-badge&logo=databricks&logoColor=white)

## 🔍 Key Findings

- **Pendapatan stagnan, bukan turun:** selisih antar tahun di bawah 1% (Rp 80,5 → 80,1 → 80,6 → 80,1 M). Mesin pertumbuhan tidak bekerja.
- **Margin bukan masalah:** konstan **28,40%** empat tahun, sebaran antar 31 provinsi cuma 28,35–28,47%. Program efisiensi margin tidak akan menghasilkan apa-apa.
- **Diskon bukan sumber kebocoran:** setiap 5 poin diskon memotong ~Rp 7,2 ribu profit/transaksi secara linear; diskon tertinggi hanya 15% dan **0% transaksi merugi**.
- **Profit terkonsentrasi di satu tier:** transaksi > Rp 500 ribu (51,9% volume) menyumbang **Rp 72,9 M — 79,9% profit**, sementara tier ≤ Rp 100 ribu (10% volume) cuma 0,5%.
- **Tidak ada produk penopang:** produk terbesar hanya **1,4% profit** dari 150 SKU — strategi "fokus top 10 SKU" tidak akan menggerakkan angka.
- **Risiko konsentrasi geografis:** Jawa Barat = 29,6% transaksi nasional (**4,1× provinsi kedua**), Rp 26,94 M profit; DKI Jakarta hanya Rp 1,10 M.
- **Rating cabang menyesatkan:** gap rating cabang (4,45) vs rating transaksi (4,00) = **−0,45**, terlebar −1,09 di cabang berating sempurna 5,00.
- **Modal kerja terparkir:** **7,3%** dari 258.415 pasangan cabang-produk menahan stok yang tak pernah terjual 4 tahun; 1,8% mencatat penjualan tanpa stok opname.

## 📁 Struktur Repo
```bash
kimia-farma-performance-analytics/
├── README.md
├── raw_data/ # 4 CSV sumber (transaction, inventory, kantor_cabang, product)
├── Analyst/ # SQL BigQuery Medallion: Bronze → Silver → Gold
├── Visualisasi/ # dashboard (HTML custom visual / export PDF)
└── Presentation Deck/ # storyline deck 15 slide (PDF)
```

## 🛡️ License

Project ini dilisensikan di bawah [MIT License](LICENSE). Bebas dipakai, dimodifikasi, dan dibagikan dengan atribusi yang sesuai.

## 🌟 About Me

Hai! Aku **Ghifarry Alnaffriza**, mahasiswa Pembangunan Ekonomi Kewilayahan di Sekolah Vokasi UGM yang lagi ngebangun jalan sebagai **data/business analyst**. Aku suka ngulik data buat nemuin cerita di balik angka — kayak project Kimia Farma ini.

Yuk connect! Boleh reach out lewat platform berikut:

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white)](https://linkedin.com/in/ghifarryalnaffriza)
[![GitHub](https://img.shields.io/badge/GitHub-181717?logo=github&logoColor=white)](https://github.com/ghifarryalnaffriza)
[![Instagram](https://img.shields.io/badge/Instagram-E4405F?logo=instagram&logoColor=white)](https://instagram.com/ghifaralnaffi_)
[![Email](https://img.shields.io/badge/Email-D14836?logo=gmail&logoColor=white)](mailto:ghifaralns@gmail.com)
