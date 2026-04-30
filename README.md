# LendingGuard: Credit Risk Prediction System

## Repository Outline
```
1. Modeling : `LendingGuard_CreditRisk_Analysis.ipynb` - Alur kerja end-to-end dari Auditing hingga Evaluasi.
2. Model Inference : `LendingGuard_CreditRisk_Inference.ipynb` - Notebook khusus untuk simulasi prediksi data baru.
3. README.md - Dokumentasi teknis dan gambaran umum proyek.
```

## Problem Background
`Industri perbankan dan fintech P2P Lending beroperasi di atas manajemen risiko yang sangat ketat. Salah satu tantangan terbesarnya adalah Gagal Bayar (Default), di mana nasabah tidak mampu memenuhi kewajiban pembayaran mereka. Kerugian finansial akibat satu nasabah yang macet (Bad Loan) jauh lebih besar dibandingkan keuntungan bunga yang didapat dari nasabah lancar (Good Loan).`

`Berdasarkan data historis, angka kredit macet pada dataset ini berada di kisaran 10.9%. Meskipun terlihat kecil, akumulasi kerugian dari angka tersebut dapat mengganggu likuiditas institusi. Proyek ini bertujuan untuk membangun sistem otomatisasi Credit Scoring menggunakan Machine Learning dengan beberapa poin fokus utama:`

* `1. Akurasi Prediksi: Mengidentifikasi calon nasabah berisiko tinggi sejak tahap pendaftaran (Point of Origination) guna menekan angka Non-Performing Loan (NPL).`

* `2. Efisiensi Operasional: Mengurangi ketergantungan pada penilaian manual yang subjektif dan lambat, sehingga proses persetujuan kredit menjadi lebih cepat dan objektif.`

* `3. Integritas Data: Menerapkan framework "Zero Data Leakage" untuk memastikan model tidak menggunakan variabel masa depan yang tidak tersedia saat aplikasi pinjaman diajukan.`

## Dataset

### 1. Informasi Identitas & Peminjam
Kolom ini berisi data pribadi dan demografi si peminjam saat mendaftar.
* **Unnamed: 0**: Nomor urut baris (bawaan dari file Excel/CSV).
* **id**: ID unik untuk transaksi pinjaman.
* **member_id**: ID unik untuk akun nasabah/peminjam.
* **emp_title**: Nama jabatan atau pekerjaan peminjam.
* **emp_length**: Lama bekerja di pekerjaan saat ini (misal: 1 tahun, 10+ tahun).
* **home_ownership**: Status kepemilikan rumah (Sewa, KPR/Mortgage, atau Milik Sendiri).
* [cite_start]**annual_inc**: Total pendapatan atau gaji tahunan peminjam[cite: 3].
* **verification_status**: Status apakah pihak perusahaan sudah memverifikasi sumber pendapatan peminjam.
* **zip_code**: Tiga digit pertama kode pos alamat peminjam.
* [cite_start]**addr_state**: Singkatan nama negara bagian tempat tinggal peminjam[cite: 2].

### 2. Detail Pengajuan Pinjaman
Kolom ini menjelaskan spesifikasi pinjaman yang diajukan.
* **loan_amnt**: Jumlah uang yang ingin dipinjam oleh nasabah.
* **funded_amnt**: Jumlah uang yang akhirnya disetujui untuk dipinjamkan.
* [cite_start]**funded_amnt_inv**: Porsi uang pinjaman yang didanai oleh investor[cite: 1].
* **term**: Jangka waktu cicilan (biasanya 36 bulan atau 60 bulan).
* **int_rate**: Suku bunga pinjaman dalam persentase.
* **installment**: Jumlah uang cicilan yang harus dibayar nasabah setiap bulan.
* **grade**: Peringkat risiko nasabah (A, B, C, dst). A adalah yang paling rendah risiko.
* **sub_grade**: Detail dari peringkat risiko (misal: A1, A2, B1).
* **issue_d**: Bulan dan tahun pinjaman resmi diberikan/dicairkan.
* **url**: *Link* halaman web untuk melihat rincian pinjaman ini.
* **desc**: Alasan atau cerita peminjam saat mengajukan pinjaman.
* **purpose**: Kategori tujuan pinjaman (misal: pernikahan, kartu kredit, renovasi).
* **title**: Judul deskripsi pinjaman yang ditulis oleh nasabah.
* **application_type**: Jenis pengajuan (Apakah diajukan sendiri/Individu atau patungan/Bersama).
* [cite_start]**annual_inc_joint**: Total pendapatan gabungan jika tipe pengajuannya patungan[cite: 4].
* **dti_joint**: Rasio utang terhadap pendapatan gabungan (untuk pinjaman patungan).
* **verification_status_joint**: Status verifikasi pendapatan untuk pinjaman patungan.

### 3. Status & Riwayat Pembayaran (Sangat Penting!)
Kolom ini melacak aliran uang, sisa utang, dan performa pembayaran nasabah.
* **loan_status**: Status pinjaman saat ini (Lunas, Lancar, Menunggak, atau Gagal Bayar). **Ini adalah kolom utama untuk menentukan Target/Label kita.**
* **pymnt_plan**: Indikator apakah nasabah punya rencana pelunasan khusus.
* **initial_list_status**: Status awal saat pinjaman masuk ke daftar (biasanya *Whole* atau *Fractional*).
* **out_prncp**: Sisa pokok hutang yang belum dibayar oleh nasabah.
* **out_prncp_inv**: Sisa pokok hutang yang menjadi hak investor.
* **total_pymnt**: Total seluruh uang (pokok + bunga) yang sudah dibayar nasabah sampai saat ini.
* **total_pymnt_inv**: Total pembayaran yang sudah diterima oleh investor.
* **total_rec_prncp**: Total khusus utang pokok yang sudah dilunasi.
* **total_rec_int**: Total khusus bunga yang sudah dibayar.
* **total_rec_late_fee**: Total denda keterlambatan yang sudah dibayar.
* **recoveries**: Uang yang berhasil ditagih paksa setelah pinjaman dinyatakan macet/gagal bayar.
* **collection_recovery_fee**: Biaya yang dibayarkan ke agen penagih (*debt collector*).
* **last_pymnt_d**: Tanggal nasabah melakukan pembayaran cicilan terakhir kali.
* **last_pymnt_amnt**: Nominal uang yang dibayar pada cicilan terakhir.
* **next_pymnt_d**: Tanggal jatuh tempo untuk cicilan bulan berikutnya.
* **last_credit_pull_d**: Tanggal terakhir perusahaan mengecek ulang skor kredit peminjam.

### 4. Metrik Risiko & Profil Kredit (Dilihat saat mendaftar)
Ini adalah data rapor keuangan masa lalu dari si peminjam.
* **dti** (*Debt-to-Income*): Rasio total cicilan utang bulanan dibagi pendapatan bulanan. Makin tinggi angkanya, makin berisiko.
* **delinq_2yrs**: Berapa kali nasabah telat bayar lebih dari 30 hari dalam 2 tahun terakhir.
* **earliest_cr_line**: Tanggal nasabah pertama kali punya riwayat kredit/pinjaman seumur hidupnya.
* **inq_last_6mths**: Berapa kali pihak bank/kreditur mengecek profil nasabah dalam 6 bulan terakhir.
* **mths_since_last_delinq**: Jumlah bulan sejak terakhir kali nasabah telat bayar.
* **mths_since_last_record**: Jumlah bulan sejak ada catatan hukum/kebangkrutan publik.
* **open_acc**: Jumlah akun fasilitas kredit/kartu kredit yang saat ini masih aktif.
* **pub_rec**: Jumlah catatan hukum buruk (seperti kebangkrutan) yang tercatat di publik.
* **revol_bal**: Total saldo utang kartu kredit yang belum dibayar.
* **revol_util**: Persentase pemakaian limit kartu kredit. (Jika limit 10jt dan dipakai 9jt, berarti utilitasnya 90%).
* **total_acc**: Total semua fasilitas kredit yang pernah dimiliki nasabah, baik yang aktif maupun sudah tutup.
* **collections_12_mths_ex_med**: Jumlah tagihan yang ditangani agen penagih dalam 12 bulan terakhir (tidak termasuk biaya medis).
* **mths_since_last_major_derog**: Jumlah bulan sejak nasabah punya rapor kredit yang sangat buruk (peringkat 9).
* **policy_code**: Kode kebijakan (biasanya isinya angka 1 untuk semua pinjaman).
* **acc_now_delinq**: Jumlah fasilitas kredit nasabah yang *saat ini* sedang nunggak.

### 5. Profil Kredit Lanjutan (Sering kali kosong / Null)
Ini biasanya data tambahan untuk sistem skor kredit versi terbaru.
* **tot_coll_amt**: Total uang nasabah yang sedang ditagih oleh pihak penagihan.
* **tot_cur_bal**: Total semua utang nasabah di semua tempat saat ini.
* **open_acc_6m**: Jumlah rekening kredit baru yang dibuka nasabah 6 bulan terakhir.
* **open_il_6m**: Jumlah pinjaman cicilan (bukan kartu kredit) aktif dalam 6 bulan.
* **open_il_12m**: Jumlah pinjaman cicilan baru dalam 1 tahun.
* **open_il_24m**: Jumlah pinjaman cicilan baru dalam 2 tahun.
* **mths_since_rcnt_il**: Jumlah bulan sejak akun pinjaman cicilan terakhir dibuka.
* **total_bal_il**: Total sisa utang di akun pinjaman cicilan.
* **il_util**: Persentase pemakaian limit untuk pinjaman cicilan.
* **open_rv_12m**: Jumlah kartu kredit baru yang dibuka dalam 1 tahun terakhir.
* **open_rv_24m**: Jumlah kartu kredit baru yang dibuka dalam 2 tahun terakhir.
* **max_bal_bc**: Saldo utang paling besar di salah satu kartu kredit nasabah.
* [cite_start]**all_util**: Persentase total pemakaian seluruh limit kredit yang dimiliki[cite: 2].
* **total_rev_hi_lim**: Total batas maksimal (limit) seluruh kartu kredit nasabah.
* **inq_fi**: Pengecekan riwayat finansial nasabah.
* **total_cu_tl**: Total fasilitas kredit nasabah dari *Credit Union* (koperasi simpan pinjam).
* **inq_last_12m**: Jumlah pengecekan profil kredit dalam 12 bulan terakhir.

## Tech Stack & Libraries
Untuk mencapai standar industri, proyek ini menggunakan *environment* Python dengan pustaka berikut:
- **Data Manipulation:** `pandas`, `numpy`
- **Visualization:** `matplotlib`, `seaborn`
- **Feature Engineering:** `feature-engine` (Khusus untuk teknik *3-Tier Winsorization*)
- **Machine Learning:** `scikit-learn` (Pipeline, ColumnTransformer, LogisticRegression, Random Forest)
- **Model Persistence:** `joblib`

## 🔗 References & Justification
- **Risk Management Strategy:** [The Importance of Credit Risk Management](https://www.investopedia.com/terms/c/creditrisk.asp) (Memahami dampak *default* terhadap kesehatan finansial bank).
- **Explainable AI in Finance:** [Why Interpretability Matters in Financial Models](https://www.forbes.com/sites/forbestechcouncil/2020/03/02/the-importance-of-explainability-in-ai-banking-and-finance/) (Justifikasi pemilihan *Logistic Regression* sebagai model yang mudah diaudit oleh regulator).
- **Standard Guidelines:** [BIS: Principles for the Management of Credit Risk](https://www.bis.org/publ/bcbs75.htm) (Prinsip internasional dalam pengelolaan risiko kredit).

---