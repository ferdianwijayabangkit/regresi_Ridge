# ⚙️ Analisis Ridge Regression untuk Mengatasi Multikolinearitas

![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)
![R](https://img.shields.io/badge/R-4.0+-purple.svg)
![License](https://img.shields.io/badge/License-MIT-red.svg)
![Affiliation](https://img.shields.io/badge/Affiliation-UNTIRTA-orange.svg)

Repositori ini menyajikan alur kerja komprehensif untuk analisis regresi linier menggunakan metode **Ridge Regression**. Ridge adalah pendekatan regularisasi L2 yang sangat kuat untuk mengatasi masalah **multikolinearitas**, di mana variabel-variabel prediktor saling berkorelasi tinggi. Kondisi ini dapat menyebabkan estimasi koefisien pada model Ordinary Least Squares (OLS) menjadi tidak stabil dan sulit diinterpretasikan. Proyek ini mendemonstrasikan implementasi paralel di **Python** dan **R** untuk diagnosis masalah dan membangun model Ridge yang lebih robust.

## ✨ Fitur Utama

- **🎯 Diagnosis Komprehensif**: Memulai dengan model OLS untuk mendiagnosis multikolinearitas menggunakan *Variance Inflation Factor* (VIF) dan analisis matriks korelasi.
- **⚙️ Pemodelan Baseline OLS**: Menjalankan OLS standar dan memberikan analisis terstruktur mengenai dampaknya, seperti tanda koefisien yang berlawanan dengan logika dan signifikansi statistik yang rendah.
- **🔄 Standardisasi Data**: Menunjukkan langkah pra-pemrosesan krusial untuk menstandardisasi variabel prediktor, sebuah syarat wajib sebelum menerapkan penalti Ridge, lengkap dengan konfirmasi statistik (mean ≈ 0, std ≈ 1).
- **🛠️ Implementasi Ridge Regression Lanjutan**:
    - **Python**: Menerapkan proses pencarian alpha (lambda) multi-tahap yang sangat robust:
        1.  Pencarian rentang super luas dengan `RidgeCV` untuk mendapatkan estimasi awal.
        2.  Pencarian bertarget (*targeted search*) untuk menemukan alpha terkecil yang menghasilkan semua koefisien positif, sesuai dengan logika bisnis.
        3.  *Fine-tuning* di sekitar alpha terbaik untuk presisi optimal.
    - **R**: Menggunakan fungsi `cv.glmnet()` dari paket `glmnet` untuk secara efisien menemukan alpha optimal melalui *cross-validation*.
- **📊 Laporan Reproducibel**: Menyediakan file R Markdown (`.Rmd`) yang menghasilkan laporan analisis lengkap, dari pemuatan data hingga perbandingan model, yang siap untuk dikonsumsi di GitHub (`.md`).
- **💡 Analisis Komparatif Mendalam**: Menyajikan tabel perbandingan yang jelas antara koefisien OLS dan Ridge, lengkap dengan kalkulasi **persentase penyusutan (Shrinkage %)** untuk setiap variabel.

## 🔧 Tumpukan Teknologi (Tech Stack)

- **Bahasa**: `Python 3.7+`, `R 4.0+`
- **Pustaka Python**: `pandas`, `numpy`, `statsmodels`, `scikit-learn`, `tabulate`, `openpyxl`
- **Pustaka R**: `glmnet`, `car`, `readxl`, `dplyr`, `knitr`, `rmarkdown`
- **Lingkungan**: `Jupyter Notebook / Lab`, `RStudio`

## 🚀 Cara Memulai

### Prasyarat
- **Perangkat Lunak**: Python 3.7+, R 4.0+, Jupyter Notebook/Lab, dan RStudio.
- **File Data**: Pastikan file `data_simulasi_ridge.xlsx` berada di dalam direktori proyek Anda.

### Instalasi & Penggunaan

1.  **Unduh Repositori**: *Clone* atau unduh proyek ini ke komputer Anda.

2.  **Untuk Pengguna Python**:
    - Buka terminal atau command prompt, lalu instal paket yang diperlukan:
      ```bash
      pip install pandas openpyxl numpy statsmodels scikit-learn tabulate jupyterlab
      ```
    - Jalankan Jupyter Lab/Notebook dan buka file Python (`.ipynb`).
    - **Penting**: Ubah `file_path` di dalam skrip agar sesuai dengan lokasi file `data_simulasi_ridge.xlsx` di komputer Anda.

3.  **Untuk Pengguna R**:
    - Buka RStudio, lalu instal paket yang diperlukan dengan menjalankan perintah berikut di *console*:
      ```r
      install.packages(c("glmnet", "car", "readxl", "dplyr", "knitr", "rmarkdown"))
      ```
    - Buka file `.Rmd` dan tekan "Knit" untuk menghasilkan laporan Markdown (`.md`).
    - **Penting**: Ubah `file_path` di dalam skrip agar sesuai dengan lokasi file `data_simulasi_ridge.xlsx` di komputer Anda.

4.  **Jalankan Analisis**: Eksekusi semua sel (Python) atau *chunks* (R) untuk mereplikasi seluruh alur kerja.

## 📊 Metrik Hasil & Ringkasan Output Ridge Regression

Fokus evaluasi adalah pada stabilitas koefisien dan bagaimana Ridge mengatasi masalah VIF yang tinggi.

| Metrik                       | Deskripsi                                                                                                           | Contoh Nilai        |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------- | ------------------- |
| `VIF (sebelum Ridge)`        | Nilai VIF pada model OLS awal. Nilai yang sangat tinggi (>10) mengkonfirmasi adanya multikolinearitas parah.         | `> 400`             |
| `Koefisien OLS (Asli)`       | Estimasi koefisien yang tidak stabil dari model OLS, seringkali dengan tanda yang salah atau nilai yang meledak.       | `-0.021` (salah)    |
| `Lambda (α) Optimal`         | Parameter penalti yang dipilih melalui proses *cross-validation* dan pencarian bertarget untuk hasil terbaik.         | `~ 2.0 - 5.0`       |
| `Koefisien Ridge`            | Estimasi koefisien akhir dari model Ridge. Nilainya "menyusut" (shrinkage) ke arah nol, lebih stabil dan masuk akal. | `> 0` (diperbaiki)  |
| `Shrinkage (%)`              | Persentase penyusutan koefisien Ridge dibandingkan OLS (data standar). Menunjukkan kekuatan efek regularisasi.       | `> 50%`             |


## ⚖️ Lisensi & Ketentuan Penggunaan

- **Hak Cipta**: © 2025 Ferdian Bangkit Wijaya - Seluruh Hak Dilindungi.
- **Penggunaan Akademik**: Diizinkan secara bebas untuk keperluan penelitian, tesis, dan pendidikan non-komersial dengan atribusi.
- **Penggunaan Komersial**: Memerlukan izin tertulis dari pengembang.

## 👨‍💻 Author

- **Ferdian Bangkit Wijaya**
- Program Studi Statistika, Fakultas Teknik
- **Universitas Sultan Ageng Tirtayasa (UNTIRTA)**
- Banten, Indonesia 🇮🇩

---

> Proyek ini dirancang sebagai panduan mendalam untuk menangani multikolinearitas, menunjukkan bagaimana pendekatan iteratif dan perbandingan model dapat menghasilkan estimasi parameter yang lebih andal dan stabil.
