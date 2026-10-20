# UTS Machine Learning — Linear Regression & Logistic Regression

## Identitas Mahasiswa

- **Nama:** Mufti Akbar
- **NIM:** 241011401908
- **Kelas:** Reg B
- **Random state:** `8` (dua digit terakhir NIM adalah `08`; pada Python digunakan sebagai integer `8`)

## Tujuan

Notebook ini menerapkan Linear Regression untuk memperkirakan penjualan berdasarkan biaya iklan dan Logistic Regression untuk mengklasifikasikan email spam. Model utama, pembagian data, dan metrik evaluasi dibuat secara manual menggunakan NumPy. scikit-learn hanya digunakan pada bagian bonus untuk membandingkan hasil.

## Dataset

- **`Ads_Data.csv`** — 40 baris dengan kolom `BiayaIklan` (juta rupiah) dan `Penjualan` (juta rupiah).
- **`Email_Data.csv`** — 60 baris dengan kolom `JumlahLink`, `KataPromo`, dan `Spam` (1 = spam, 0 = bukan spam). Dataset terdiri dari 25 email spam dan 35 email bukan spam.

## Hasil Linear Regression

Data dibagi menjadi 32 baris training dan 8 baris testing. Model Normal Equation menghasilkan persamaan:

\[
\hat{y} = 28.9945 + 9.0688 \times \text{BiayaIklan}
\]

| Data | MAE | MSE | RMSE | R² |
|---|---:|---:|---:|---:|
| Training | 4.2685 | 28.2835 | 5.3182 | 0.9493 |
| Testing | 5.3610 | 68.6741 | 8.2870 | 0.9154 |

Nilai R² testing sebesar 0.9154 menunjukkan bahwa model menjelaskan sekitar 91.54% variasi penjualan pada bagian testing. RMSE testing sekitar Rp8.29 juta menggambarkan besar kesalahan prediksi dalam satuan penjualan. Hasil ini cukup baik pada dataset ini, tetapi belum menjamin performa yang sama untuk data lain.

### Eksperimen Gradient Descent

| Learning rate | Intercept akhir | Koefisien akhir | MSE akhir | Hasil |
|---:|---:|---:|---:|---|
| 0.001 | 20.0333 | 10.5544 | 46.3321 | Stabil, tetapi lambat mencapai solusi |
| 0.02 | 28.9945 | 9.06881 | 28.2835 | Stabil dan mendekati Normal Equation |
| 0.1 | nilai membesar | nilai membesar | non-finite setelah iterasi awal | Divergen |

Learning rate 0.02 memberikan hasil paling dekat dengan Normal Equation dari tiga percobaan. Learning rate 0.1 terlalu besar untuk skala fitur dan menyebabkan divergensi; kondisi tersebut ditampilkan dan dijelaskan di notebook.

Prediksi penjualan untuk biaya iklan Rp4,5 juta, Rp7 juta, dan Rp12 juta berturut-turut sekitar Rp69,8041 juta, Rp92,4761 juta, dan Rp137,8202 juta. Prediksi pada Rp12 juta adalah ekstrapolasi di luar rentang data sehingga lebih tidak pasti.

## Hasil Logistic Regression

Data dibagi menjadi 45 baris training dan 15 baris testing. Model manual menghasilkan persamaan:

\[
P(\text{Spam})=\sigma(-8.4338 + 0.7612\,\text{JumlahLink} + 0.7255\,\text{KataPromo})
\]

Log loss turun dari **0.6807** menjadi **0.3324** selama 10.000 iterasi.

### Evaluasi data testing

| Metrik | Hasil |
|---|---:|
| Accuracy | 1.0000 |
| Precision | 1.0000 |
| Recall | 1.0000 |
| F1-score | 1.0000 |

Confusion matrix (baris = kelas aktual [bukan spam, spam], kolom = prediksi [bukan spam, spam]):

\[
\begin{bmatrix}12 & 0\\0 & 3\end{bmatrix}
\]

Pada 15 sampel testing ini, 12 email bukan spam dan 3 email spam seluruhnya diklasifikasikan dengan benar. Karena ukuran data testing relatif kecil, skor sempurna tersebut tidak boleh dianggap sebagai jaminan bahwa model akan selalu sempurna pada email baru.

### Eksperimen threshold

| Threshold | TN | FP | FN | TP | Precision | Recall | Accuracy |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 0.3 | 11 | 1 | 0 | 3 | 0.75 | 1.00 | 0.9333 |
| 0.5 | 12 | 0 | 0 | 3 | 1.00 | 1.00 | 1.0000 |
| 0.7 | 12 | 0 | 0 | 3 | 1.00 | 1.00 | 1.0000 |

Threshold **0.5** dipilih untuk prediksi email baru karena pada data testing menghasilkan precision dan recall 1.00, sementara threshold 0.3 menambah satu false positive tanpa meningkatkan recall.

## Perbandingan Bonus dengan scikit-learn

- Linear Regression manual dan scikit-learn menghasilkan intercept sekitar **28.9945**, koefisien **9.0688**, dan R² testing **0.9154**.
- Logistic Regression manual dan scikit-learn sama-sama memperoleh accuracy testing **1.0000**. Koefisiennya sedikit berbeda: manual sekitar `[0.7612, 0.7255]`, sedangkan scikit-learn sekitar `[0.7767, 0.7391]`. Perbedaan dapat terjadi karena scikit-learn memakai regularisasi L2 secara default dan proses optimisasi yang berbeda.

## Dependensi yang digunakan

Lingkungan eksekusi notebook ini menggunakan:

- Python 3.13.5
- NumPy 2.3.5
- Pandas 2.2.3
- Matplotlib 3.10.8
- scikit-learn 1.8.0 (hanya untuk bonus)
- ipykernel 7.2.0

## Struktur Repository

```text
uts-machine-learning-241011401908/
├── README.md
├── tugas_UTS.ipynb
├── Ads_Data.csv
└── Email_Data.csv
```

## Kesimpulan

Pada dataset yang disediakan, Linear Regression menunjukkan hubungan positif antara biaya iklan dan penjualan, dengan R² testing 0.9154. Logistic Regression mengklasifikasikan data testing dengan benar pada pembagian ini, tetapi hasil tersebut perlu diuji kembali pada dataset yang lebih besar dan beragam. Eksperimen Gradient Descent juga menunjukkan bahwa pemilihan learning rate berpengaruh besar terhadap kestabilan proses pelatihan.
