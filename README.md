# 💊 Kimia Farma Sales & Profit Analysis 2020-2023

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white)](https://linkedin.com/in/ghifarryalnaffriza)
[![GitHub](https://img.shields.io/badge/GitHub-181717?logo=github&logoColor=white)](https://github.com/ghifarryalnaffriza)
[![Instagram](https://img.shields.io/badge/Instagram-E4405F?logo=instagram&logoColor=white)](https://instagram.com/ghifaralnaffi_)
[![Email](https://img.shields.io/badge/Email-D14836?logo=gmail&logoColor=white)](mailto:ghifaralns@gmail.com)

> Analisis kinerja bisnis Kimia Farma 2020-2023: nett profit **Rp91,2 miliar** dengan margin 28,4%, tapi penjualan jalan di tempat selama empat tahun. Kebocoran paling jelas ada di diskon: **Rp7,4 miliar profit dilepas tanpa menambah pembeli.**

![Dashboard Gambaran Umum](Screenshots%20Dasboard/Dasboard_Kimia%20Farma_Gambaran%20Umum.png)

## 🎯 Business Problem

Nett sales Kimia Farma tertahan di sekitar Rp80 miliar per tahun (growth YoY: −0,5%, +0,7%, −0,6%). Kalau penjualan tidak tumbuh, kenaikan profit harus datang dari tempat lain. Project ini mencari tahu:

- Kenapa sebagian cabang untung lebih besar dari cabang lain?
- Apakah rating cabang mencerminkan kepuasan pembeli?
- Dari mana profit datang, dan berapa yang dilepas untuk diskon?

Dikerjakan sebagai final task Rakamin VIX Big Data Analytics, lalu saya kembangkan dengan pipeline SQL Server dan dashboard Power BI tiga halaman.

## 📊 Dataset

- **Sumber:** 4 tabel Kimia Farma dari Rakamin VIX: `kf_final_transaction`, `kf_inventory`, `kf_kantor_cabang`, `kf_product`
- **Volume:** 672.458 transaksi · 1.035.000 baris inventory · 1.725 cabang · 150 produk
- **Cakupan:** 2020-2023 · 31 provinsi
- **File:** ada di folder `Raw Data/`. Dua file terbesar (`kf_final_transaction` dan `kf_inventory`) disimpan dalam format `.zip`.
- **Integritas relasi:** semua `branch_id` dan `product_id` di tabel transaksi dan inventory punya pasangan di tabel master

Rumus yang dipakai:

| Metrik | Rumus |
|---|---|
| Nett sales | harga x (1 - diskon) |
| Nett profit | nett sales x persentase laba kotor |
| Persentase laba kotor | ≤ Rp50 rb 10% · Rp50-100 rb 15% · Rp100-300 rb 20% · Rp300-500 rb 25% · > Rp500 rb 30% |
| Profit yang dilepas untuk diskon | harga x diskon x persentase laba kotor |

## 🏗️ Arsitektur & Pendekatan

Medallion architecture di SQL Server (T-SQL):

- **Bronze**: load 4 CSV apa adanya dengan `BULK INSERT`.
- **Silver**: `TRIM` semua teks, ubah tanggal `M/D/YYYY` ke `DATE` (style 101, supaya 9/7/2023 terbaca 7 September), dan hapus duplikat dengan `ROW_NUMBER()`.
- **Gold**: tabel `kf_analysis` berisi nett sales, nett profit, dan profit yang dilepas per transaksi, ditambah satu tabel untuk setiap pertanyaan dashboard (Q1-Q5), termasuk simulasi batas diskon.

Struktur Gold mengikuti halaman dashboard, jadi setiap angka di Power BI bisa dicocokkan dengan query sumbernya. Bagian bawah file Gold berisi query cek angka.

## 🛠️ Tools & Tech

![SQL Server](https://img.shields.io/badge/SQL_Server-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white)
![T-SQL](https://img.shields.io/badge/T--SQL-4479A1?style=for-the-badge&logo=databricks&logoColor=white)
![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0B2E59?style=for-the-badge&logo=powerbi&logoColor=white)
![Medallion](https://img.shields.io/badge/Medallion_Architecture-4B8BBE?style=for-the-badge&logo=databricks&logoColor=white)

## 🔍 Key Findings

- **Penjualan stagnan, bukan turun:** Rp80,4 → 80,0 → 80,6 → 80,1 miliar. Rata-rata Rp6,69 miliar per bulan, dan Februari selalu paling rendah karena bulannya lebih pendek. Dihitung per hari, Februari sama dengan bulan lain.
- **Diskon adalah kebocoran terbesar:** di semua kelompok diskon, harga barang (±Rp516 rb) dan jumlah transaksi (±44,8 rb per level) sama. Yang berubah cuma untung per transaksi: **Rp147 rb** kalau tanpa diskon, **Rp127 rb** di diskon 12-15%. Totalnya **Rp7,4 miliar** dilepas, setara 7,5% potensi profit.
- **Cabang terbaik menang karena ramai:** 10 cabang teratas rata-rata untung Rp62,1 jt, 40% di atas 10 terbawah (Rp44,4 jt). Transaksinya 435 vs 344, rating pembelinya sama (4,01 vs 3,99).
- **Jawa Barat unggul karena jumlah cabang:** 510 cabang, Rp26,9 miliar profit. Dihitung per cabang, semua provinsi setara di Rp52-54 jt.
- **Rating cabang menyesatkan:** rating cabang naik dari 4,08 ke 4,83, rating pembeli tetap **4,00**. Lima cabang berating sempurna 5,0 cuma dapat rating pembeli 3,91 sampai 3,96.
- **Profit bertumpu pada produk mahal:** transaksi di atas Rp500 rb (51,9% volume) menyumbang **79,9% profit**. Di sisi lain, 10 produk teratas cuma 12,9% profit, jadi tidak ada produk penopang tunggal.
- **Stok terparkir:** **7,3%** dari 258.415 pasangan cabang-produk punya stok yang tidak pernah terjual selama empat tahun, dan 1,7% mencatat penjualan tanpa stok opname.

## 🧹 Persiapan Data

Sebelum menghitung apa pun, saya mengecek kualitas data:

- Semua key (`branch_id`, `product_id`, `transaction_id`) unik dan tidak kosong.
- Semua `branch_id` dan `product_id` di tabel transaksi dan inventory punya pasangan di tabel master.
- Tanggal di CSV memakai format M/D/YYYY. Kalau dibaca dengan format lain, 9/7/2023 bisa terbaca 9 Juli, bukan 7 September.
- Nama produk dan nama cabang tidak unik. Semua analisis produk memakai `product_id`, dan cabang saya identifikasi dengan kota dan `branch_id`.

## 🔬 Analisis

### 1. Penjualan per tahun dan per bulan

![Nett sales per tahun](Images/Nett%20sales%20per%20tahun.png)

Nett sales berada di sekitar Rp80 miliar setiap tahun dengan perubahan di bawah 1%. Penjualan tidak turun, tapi juga tidak tumbuh.

![Nett sales per bulan](Images/Nett%20sales%20per%20bulan%20%28Rp%20miliar%29.png)

Rata-rata penjualan Rp6,69 miliar per bulan, dengan 26 bulan di atas rata-rata dan 22 bulan di bawahnya. Titik terendah selalu jatuh di bulan Februari.

Setelah saya hitung per hari, penjualan Februari ternyata sama dengan bulan lain (sekitar Rp221 jt per hari). Turunnya hanya karena jumlah harinya lebih sedikit, bukan karena pembeli berkurang.

### 2. Kenapa provinsi tidak bisa langsung dibandingkan

![Top 10 provinsi](Images/Top%2010%20provinsi%20%28total%20nett%20profit%29.png)

Ranking awal menempatkan Jawa Barat jauh di atas provinsi lain. Ternyata Jawa Barat punya 510 cabang, hampir 30% dari seluruh cabang. Setelah saya bagi per cabang, semua provinsi menghasilkan Rp52 sampai 54 jt. Karena itu dashboard menampilkan total profit bersama jumlah cabangnya, supaya pembaca tidak salah mengira Jawa Barat lebih efisien.

### 3. Cabang terbaik vs terburuk

![Top 10 cabang](Images/Top%2010%20cabang%20%28nett%20profit%29.png)

10 cabang teratas rata-rata untung Rp62,1 jt, 40% di atas 10 terbawah (Rp44,4 jt). Bedanya ada di jumlah transaksi (435 vs 344), bukan rating pembeli (4,01 vs 3,99). Cabang terbaik lebih ramai, bukan lebih disukai.

### 4. Rating cabang vs rating pembeli

![Rating cabang vs rating pembeli](Images/Rating%20cabang%20vs%20rating%20pembeli.png)

Saya membagi cabang ke tiga kelompok berdasarkan rating cabangnya:

| Kelompok rating cabang | Jumlah cabang | Rating cabang | Rating pembeli |
|---|---|---|---|
| 3,9 sampai 4,2 | 576 | 4,08 | 4,00 |
| 4,3 sampai 4,6 | 600 | 4,46 | 4,00 |
| 4,7 sampai 5,0 | 549 | 4,83 | 4,00 |

Rating cabang naik, tapi rating dari pembeli tetap 4,00. Lima cabang berating 5,0 bahkan hanya mendapat rating pembeli 3,91 sampai 3,96.

![5 contoh cabang berating 5,0](Images/5%20contoh%20cabang%20berating%205%2C0.png)

### 5. Sumber profit

![Kontribusi profit per tier harga](Images/Kontribusi%20profit%20per%20tier%20harga.png)

Produk di atas Rp500 rb hanya 52% dari transaksi, tapi menyumbang 80% profit. Sebagian karena margin di tier ini paling tinggi (30%). Di sisi lain, 10 produk teratas cuma 12,9% dari total profit, jadi profit tidak bertumpu pada segelintir produk.

![Top 10 produk](Images/Top%2010%20produk%20%28nett%20profit%29.png)

### 6. Dampak diskon

![Profit per transaksi tanpa diskon vs setelah diskon](Images/Profit%20per%20transaksi%20tanpa%20diskon%20vs%20setelah%20diskon.png)

Ini temuan paling kuat.

"Profit yang dilepas untuk diskon" adalah selisih profit kalau barang yang sama dijual tanpa diskon. Contohnya, obat seharga Rp600 rb dengan margin 30% menghasilkan untung Rp180 rb. Dengan diskon 10%, harganya jadi Rp540 rb dan untungnya Rp162 rb. Artinya Rp18 rb profit dilepas. Diskon baru sepadan kalau pembelinya bertambah cukup banyak untuk menutup potongan untung itu.

Saya membagi 16 level diskon ke empat kelompok:

| | Diskon 0-3% | Diskon 4-7% | Diskon 8-11% | Diskon 12-15% |
|---|---|---|---|---|
| Rata-rata harga barang | Rp516 rb | Rp516 rb | Rp516 rb | Rp517 rb |
| Transaksi per level diskon | 44,8 rb | 44,9 rb | 44,8 rb | 44,7 rb |
| Untung per transaksi tanpa diskon | Rp146 rb | Rp147 rb | Rp147 rb | Rp147 rb |
| Untung per transaksi setelah diskon | Rp144 rb | Rp139 rb | Rp133 rb | Rp127 rb |
| Profit yang dilepas | Rp0,4 M | Rp1,5 M | Rp2,5 M | Rp3,1 M |

Harga barang dan jumlah pembeli sama di semua kelompok. Satu-satunya yang berbeda adalah besar diskonnya, jadi turunnya untung per transaksi dari Rp147 rb ke Rp127 rb murni berasal dari diskon. Totalnya Rp7,4 miliar profit dilepas selama empat tahun, setara 7,5% dari potensi profit.

Baris transaksi tidak menghitung level 0% dan 15%, karena dua level itu hanya punya separuh transaksi akibat pembulatan data.

### 7. Simulasi batas diskon

Dengan asumsi jumlah transaksi tetap (dan data menunjukkan diskon memang tidak mengubah jumlah transaksi):

| Skenario | Tambahan profit 2020-2023 | Per tahun |
|---|---|---|
| Diskon maksimal 10% | Rp0,8 M | Rp0,2 M |
| Diskon maksimal 5% | Rp3,3 M | Rp0,8 M |
| Tanpa diskon | Rp7,4 M | Rp1,8 M |

## 🧪 Yang Juga Saya Cek

Tidak semua dugaan terbukti. Beberapa sudut ini saya cek dan hasilnya rata, jadi tidak saya jadikan rekomendasi:

| Yang dicek | Hasil | Artinya |
|---|---|---|
| Kategori cabang | Profit per cabang: Apotek Rp53,1 jt · Klinik & Apotek Rp52,9 jt · Klinik-Apotek-Lab Rp52,7 jt | Jenis cabang tidak memengaruhi profit |
| Margin per provinsi | 28,35% sampai 28,47% di 31 provinsi | Margin seragam, jadi kebocoran bukan di margin |
| Penjualan per produk | Setiap produk terjual 4.330 sampai 4.608 transaksi | Tidak ada produk andalan |
| Hari dalam seminggu | Sekitar 460 transaksi per hari, Senin sampai Minggu | Tidak ada pola hari ramai |
| Harga vs rating | Rating 4,00 di semua rentang harga | Harga tidak memengaruhi kepuasan pembeli |

## 💡 Rekomendasi

| Area | Langkah | Dampak |
|---|---|---|
| Diskon | Batasi diskon maksimal 5%, uji dulu di sebagian cabang | ±Rp0,8 miliar profit per tahun |
| Rating | Pakai rating transaksi sebagai KPI kepuasan, tinjau cara rating cabang dihitung | Ukuran kepuasan yang sesuai penilaian pembeli |
| Cabang | Lengkapi data lokasi, jam buka, dan jumlah staf sebelum memutuskan per cabang | Keputusan cabang berbasis penyebab, bukan ranking |
| Produk & stok | Jaga stok produk di atas Rp500 rb, pindahkan stok mati ke cabang yang menjualnya | Modal kerja tidak terparkir |

## 🖥️ Dashboard

| Gambaran Umum | Cabang & Pelanggan | Profit & Kebocoran |
|---|---|---|
| ![](Screenshots%20Dasboard/Dasboard_Kimia%20Farma_Gambaran%20Umum.png) | ![](Screenshots%20Dasboard/Dasboard_Kimia%20Farma_Cabang%20%26%20Pelanggan.png) | ![](Screenshots%20Dasboard/Dasboard_Kimia%20Farma_Profit%20%26%20Kebocoran.png) |

Tiga halaman dengan slicer Tahun, Provinsi, dan Kategori Cabang, kotak Key Insight dan Rekomendasi di setiap halaman, serta navigasi lewat sidebar.

## ⚠️ Keterbatasan & Kualitas Data

| Masalah | Bukti | Dampak ke analisis |
|---|---|---|
| Kode kategori produk tidak cocok dengan nama obat | Hanya 20 dari 150 produk (13%) yang kodenya sesuai, misalnya obat anxiolytic diberi kode M01AE (antiinflamasi) | Analisis per kategori obat tidak saya pakai |
| Pelanggan hanya dicatat dengan nama | 264.601 nama unik, satu nama muncul di 316 transaksi | Pelanggan setia tidak bisa dibedakan dari orang lain bernama sama, jadi analisis retensi tidak bisa dibuat |
| Data kemungkinan sintetis | Rating pembeli tersebar rata dari 3,0 sampai 5,0, transaksi per cabang, harga barang, dan diskon juga terlalu seragam | Temuan perlu diuji ulang dengan data nyata |
| Inventory tanpa tanggal | Tabel stok hanya berisi cabang, produk, dan jumlah | Angka stok tidak ikut slicer Tahun |
| Pembulatan diskon | Diskon 0% dan 15% hanya punya separuh transaksi level lain | Perbandingan transaksi per level tidak memakai 0% dan 15% |
| Asumsi simulasi | Simulasi diskon mengasumsikan jumlah transaksi tetap | Data mendukung asumsi ini, tapi tetap perlu diuji di lapangan |

## 📁 Struktur Repo

```bash
SQL_Kimia-Farma-Analyst/
├── README.md
├── LICENSE
├── Raw Data/                  # 4 tabel sumber (2 file besar dalam .zip)
├── Analyst/                   # SQL Server Medallion: Bronze → Silver → Gold
├── Images/                    # chart dari dashboard Power BI
├── Screenshots Dasboard/      # tampilan 3 halaman dashboard
├── Visualisasi/               # dashboard versi PDF
└── Presentation deck/         # deck presentasi (PDF)
```

- 📄 [Dashboard versi PDF](Visualisasi/Kimia_Farma_Dashboard.pdf)
- 📑 [Deck presentasi](Presentation%20deck/Presentasi%20desain%20deck%20Kimia%20Farma.pdf)

## ▶️ Cara Menjalankan

```
git clone https://github.com/ghifarryalnaffriza/SQL_Kimia-Farma-Analyst.git
```

1. Ekstrak file `.zip` di folder `Raw Data/`.
2. Jalankan file SQL di folder `Analyst/` secara berurutan di SQL Server Management Studio: Bronze (sesuaikan path CSV), Silver, lalu Gold.
3. Cocokkan hasil query cek angka di bagian bawah file Gold dengan angka di dashboard.

## 🛡️ License

Project ini dilisensikan di bawah [MIT License](LICENSE). Bebas dipakai, dimodifikasi, dan dibagikan dengan atribusi yang sesuai.

## 🌟 About Me

Hai! Aku **Ghifarry Alnaffriza**, mahasiswa Pembangunan Ekonomi Kewilayahan di Sekolah Vokasi UGM yang lagi ngebangun jalan sebagai **data/business analyst**. Aku suka ngulik data buat nemuin cerita di balik angka, kayak project Kimia Farma ini.

Yuk connect! Boleh reach out lewat platform berikut:

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white)](https://linkedin.com/in/ghifarryalnaffriza)
[![GitHub](https://img.shields.io/badge/GitHub-181717?logo=github&logoColor=white)](https://github.com/ghifarryalnaffriza)
[![Instagram](https://img.shields.io/badge/Instagram-E4405F?logo=instagram&logoColor=white)](https://instagram.com/ghifaralnaffi_)
[![Email](https://img.shields.io/badge/Email-D14836?logo=gmail&logoColor=white)](mailto:ghifaralns@gmail.com)
