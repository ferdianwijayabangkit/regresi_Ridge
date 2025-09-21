Analisis Ridge Regression untuk Mengatasi Multikolinearitas
================
Ferdian Bangkit Wijaya
21 September 2025

- [Latar Belakang](#latar-belakang)
  - [1. Memuat Data](#1-memuat-data)
  - [2. Diagnosis Multikolinearitas](#2-diagnosis-multikolinearitas)
  - [3. Analisis OLS (Model Baseline)](#3-analisis-ols-model-baseline)
  - [4. Analisis Ridge Regression](#4-analisis-ridge-regression)
    - [4.1. Persiapan Data](#41-persiapan-data)
    - [4.2. Mencari Lambda Optimal](#42-mencari-lambda-optimal)
    - [4.3. Melatih Model Ridge](#43-melatih-model-ridge)
  - [5. Perbandingan Akhir](#5-perbandingan-akhir)
    - [Analisis Perbandingan](#analisis-perbandingan)

# Latar Belakang

Laporan ini mendemonstrasikan alur kerja untuk mengatasi masalah
**multikolinearitas** dalam model regresi linier menggunakan **Ridge
Regression**. Multikolinearitas terjadi ketika variabel prediktor dalam
model sangat berkorelasi satu sama lain, yang dapat menyebabkan estimasi
koefisien menjadi tidak stabil dan sulit diinterpretasikan.

Studi kasus ini akan melalui langkah-langkah berikut: 1. Memuat data
simulasi mahasiswa. 2. Mendiagnosis adanya multikolinearitas parah
menggunakan *Variance Inflation Factor* (VIF). 3. Menjalankan model OLS
(Ordinary Least Squares) sebagai *baseline* untuk melihat dampak negatif
multikolinearitas. 4. Menerapkan Ridge Regression sebagai solusi. 5.
Membandingkan hasil koefisien dari model OLS dan Ridge.

## 1. Memuat Data

Data yang digunakan adalah data simulasi yang berisi Indeks Prestasi
Kumulatif (IPK) mahasiswa beserta beberapa variabel prediktor. Data ini
dimuat dari file Excel.

``` r
# Path ke file Excel yang sudah disimpan
file_path <- "C:/Users/user/OneDrive - untirta.ac.id/UNTIRTA/Bahan Ajar/Supervised Learning/Github Supervised Learning/data_simulasi_ridge.xlsx"

# Membaca data dari file Excel ke dalam dataframe
tryCatch({
  data_mhs <- read_excel(file_path)
  print("--- Data Berhasil Dimuat dari Excel ---")
  kable(head(data_mhs), caption = "Pratinjau 6 Baris Pertama Data Mahasiswa")
}, error = function(e) {
  stop("GAGAL MEMBACA FILE. Pastikan path dan nama file sudah benar. Error: ", e$message)
})
```

    ## [1] "--- Data Berhasil Dimuat dari Excel ---"

|      IPK | jam_belajar | kehadiran_persen | skor_tryout |
|---------:|------------:|-----------------:|------------:|
| 3.205960 |    9.313663 |         98.89073 |    42.68112 |
| 3.621702 |   16.824577 |         97.06897 |    72.00324 |
| 3.182792 |   11.134654 |         90.72116 |    50.43374 |
| 3.643342 |   18.245261 |         93.86402 |    78.85918 |
| 3.466804 |   19.107009 |         70.73841 |    82.24962 |
| 2.799969 |    5.683347 |         84.33388 |    28.42203 |

Pratinjau 6 Baris Pertama Data Mahasiswa

## 2. Diagnosis Multikolinearitas

Sebelum membangun model, kita perlu membuktikan adanya
multikolinearitas. Kita akan menggunakan VIF sebagai alat diagnosis.
Nilai VIF di atas 10 umumnya dianggap sebagai indikasi multikolinearitas
yang serius.

``` r
# Membuat model OLS pada data asli untuk menghitung VIF
model_ols_raw <- lm(IPK ~ jam_belajar + kehadiran_persen + skor_tryout, data = data_mhs)
vif_values <- vif(model_ols_raw)

print("--- Hasil Uji VIF untuk Multikolinearitas ---")
```

    ## [1] "--- Hasil Uji VIF untuk Multikolinearitas ---"

``` r
print(vif_values)
```

    ##      jam_belajar kehadiran_persen      skor_tryout 
    ##       439.585150         1.025953       439.276530

``` r
if (any(vif_values > 10)) {
  print("KESIMPULAN: Terbukti ada multikolinearitas parah (VIF > 10). Hasil OLS kemungkinan tidak stabil.")
}
```

    ## [1] "KESIMPULAN: Terbukti ada multikolinearitas parah (VIF > 10). Hasil OLS kemungkinan tidak stabil."

## 3. Analisis OLS (Model Baseline)

Selanjutnya, kita akan memeriksa hasil model OLS standar pada data asli.
Ini akan menjadi model dasar (baseline) kita untuk perbandingan dan
untuk melihat secara langsung efek dari multikolinearitas.

``` r
print("--- Hasil Model OLS pada Data Asli ---")
```

    ## [1] "--- Hasil Model OLS pada Data Asli ---"

``` r
print(summary(model_ols_raw))
```

    ## 
    ## Call:
    ## lm(formula = IPK ~ jam_belajar + kehadiran_persen + skor_tryout, 
    ##     data = data_mhs)
    ## 
    ## Residuals:
    ##       Min        1Q    Median        3Q       Max 
    ## -0.245627 -0.037711 -0.002934  0.058949  0.176960 
    ## 
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)       1.779642   0.198864   8.949 2.03e-09 ***
    ## jam_belajar       0.154053   0.077982   1.976 0.058915 .  
    ## kehadiran_persen  0.008387   0.001893   4.431 0.000151 ***
    ## skor_tryout      -0.021153   0.019532  -1.083 0.288751    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.08754 on 26 degrees of freedom
    ## Multiple R-squared:  0.9302, Adjusted R-squared:  0.9222 
    ## F-statistic: 115.5 on 3 and 26 DF,  p-value: 3.765e-15

``` r
print("ANALISIS OLS: Perhatikan koefisien 'skor_tryout' yang negatif dan tidak signifikan (p-value tinggi). Ini adalah gejala klasik multikolinearitas yang membuat model tidak dapat diinterpretasikan dengan benar.")
```

    ## [1] "ANALISIS OLS: Perhatikan koefisien 'skor_tryout' yang negatif dan tidak signifikan (p-value tinggi). Ini adalah gejala klasik multikolinearitas yang membuat model tidak dapat diinterpretasikan dengan benar."

## 4. Analisis Ridge Regression

Untuk mengatasi masalah multikolinearitas, kita akan menerapkan Ridge
Regression. Proses ini melibatkan standardisasi data, menemukan
parameter penalti (lambda) yang optimal, dan melatih model akhir.

### 4.1. Persiapan Data

Pertama, kita pisahkan variabel prediktor (X) dan respons (y), lalu
lakukan standardisasi pada prediktor. Standardisasi adalah langkah wajib
dalam Ridge Regression agar penalti diterapkan secara adil ke semua
koefisien.

``` r
# Langkah 5a: Pisahkan matriks prediktor (X) dan vektor respons (y)
x_matrix_raw <- model.matrix(IPK ~ ., data = data_mhs)[, -1]
y_vector <- data_mhs$IPK

# Langkah 5b: Lakukan Standarisasi pada Prediktor (Langkah Wajib!)
x_matrix_scaled <- scale(x_matrix_raw)
kable(head(x_matrix_scaled), caption = "Pratinjau Data Prediktor Setelah Distandarisasi")
```

| jam_belajar | kehadiran_persen | skor_tryout |
|------------:|-----------------:|------------:|
|  -0.9775547 |        1.8084530 |  -0.9655148 |
|   0.7410187 |        1.5990286 |   0.7155345 |
|  -0.5608934 |        0.8693003 |  -0.5210536 |
|   1.0660856 |        1.2305959 |   1.1085884 |
|   1.2632624 |       -1.4278651 |   1.3029637 |
|  -1.8082076 |        0.1350351 |  -1.7829940 |

Pratinjau Data Prediktor Setelah Distandarisasi

### 4.2. Mencari Lambda Optimal

Kita menggunakan *cross-validation* untuk menemukan nilai lambda
terbaik, yaitu nilai yang meminimalkan error prediksi.

``` r
# Langkah 5c: Temukan lambda terbaik menggunakan cross-validation
set.seed(123)
cv_fit <- cv.glmnet(x_matrix_scaled, y_vector, alpha = 0) # alpha = 0 untuk Ridge
best_lambda <- cv_fit$lambda.min
print(paste("Lambda terbaik yang ditemukan:", best_lambda))
```

    ## [1] "Lambda terbaik yang ditemukan: 0.0288637489074024"

### 4.3. Melatih Model Ridge

Dengan lambda terbaik, kita sekarang melatih model Ridge Regression
final.

``` r
# Langkah 5d: Latih model Ridge final dengan lambda terbaik
model_ridge <- glmnet(x_matrix_scaled, y_vector, alpha = 0, lambda = best_lambda)
```

## 5. Perbandingan Akhir

Langkah terakhir adalah membandingkan koefisien dari model OLS (baik
pada data asli maupun yang distandarisasi) dengan koefisien dari model
Ridge. Ini akan menunjukkan bagaimana Ridge “menyusutkan” (shrink)
koefisien untuk membuatnya lebih stabil.

``` r
# Langkah 6a: Latih OLS pada data yang SUDAH DISTANDARISASI untuk perbandingan yang adil
model_ols_scaled <- lm(y_vector ~ x_matrix_scaled)

# Langkah 6b: Ekstrak semua koefisien
ols_raw_coeffs <- coef(model_ols_raw)
ols_scaled_coeffs <- coef(model_ols_scaled)
ridge_scaled_coeffs <- as.vector(coef(model_ridge))

# Langkah 6c: Buat tabel perbandingan yang komprehensif
perbandingan_final <- data.frame(
  Variabel = names(ols_raw_coeffs),
  Koefisien_OLS_Asli = ols_raw_coeffs,
  Koefisien_OLS_Distandarisasi = ols_scaled_coeffs,
  Koefisien_Ridge_Distandarisasi = ridge_scaled_coeffs
)
rownames(perbandingan_final) <- NULL # Merapikan nomor baris

kable(perbandingan_final, caption = "Perbandingan Koefisien Model OLS vs. Ridge")
```

| Variabel | Koefisien_OLS_Asli | Koefisien_OLS_Distandarisasi | Koefisien_Ridge_Distandarisasi |
|:---|---:|---:|---:|
| (Intercept) | 1.7796421 | 3.3109462 | 3.3109462 |
| jam_belajar | 0.1540533 | 0.6732801 | 0.1511917 |
| kehadiran_persen | 0.0083867 | 0.0729547 | 0.0635667 |
| skor_tryout | -0.0211533 | -0.3689719 | 0.1381778 |

Perbandingan Koefisien Model OLS vs. Ridge

### Analisis Perbandingan

Dari tabel di atas, kita bisa melihat bagaimana koefisien OLS pada data
asli memiliki nilai yang tidak masuk akal (misalnya, `skor_tryout` yang
negatif). Setelah distandarisasi, koefisien OLS tetap besar. Namun,
koefisien Ridge Regression jauh lebih kecil dan lebih stabil,
menunjukkan efek dari penalti regularisasi dalam mengatasi
multikolinearitas.
