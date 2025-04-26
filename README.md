# Laporan Proyek Machine Learning - Muhammad Arsayuan Wijaya

## Project Overview

Industri hiburan saat ini berkembang pesat, dengan ribuan film tersedia di berbagai platform streaming. Hal ini menyulitkan pengguna untuk menemukan film yang sesuai dengan selera mereka. Oleh karena itu, dibutuhkan sistem rekomendasi yang mampu menyajikan daftar film yang relevan secara personal.

Sistem rekomendasi memainkan peran penting dalam meningkatkan pengalaman pengguna, retensi pelanggan, dan nilai bisnis. Proyek ini bertujuan membangun sistem rekomendasi film dengan menggunakan pendekatan Content-Based Filtering dan Collaborative Filtering, dan mengevaluasi performanya melalui metrik yang sesuai.

## Business Understanding

### Problem Statememts
1. Bagaimana cara membantu pengguna menemukan film yang sesuai dengan preferensi mereka di tengah banyaknya pilihan yang tersedia?
2. Bagaimana membangun sistem rekomendasi film yang dapat memberikan saran yang relevan berdasarkan data interaksi pengguna atau konten film?

### Goals
1. Membangun sistem rekomendasi yang dapat memberikan daftar film yang sesuai dengan preferensi pengguna.
2. Mengimplementasikan dua pendekatan sistem rekomendasi, yakni content-based dan collaborative filtering untuk membandingkan performa dan hasilnya.

### Solution Statements
Untuk mencapai tujuan tersebut, dua pendekatan berikut digunakan:
1. Content-Based Filtering: Menggunakan informasi konten film seperti genre untuk menghitung kemiripan antar film dan merekomendasikan film yang mirip dengan yang disukai pengguna.
2. Collaborative Filtering: Menggunakan pola interaksi antar pengguna (rating) untuk menemukan film yang disukai oleh pengguna dengan profil serupa.

## Data Understanding
Dataset yang digunakan dalam proyek sistem rekomendasi film ini adalah MovieLens Dataset, yang tersedia secara publik dan dapat diakses melalui platform [Kaggle]. Dataset ini berasal dari GroupLens Research, sebuah laboratorium riset di University of Minnesota yang memang secara khusus menyediakan kumpulan data untuk pengembangan dan evaluasi sistem rekomendasi.

Dataset ini terdiri dari dua file utama yang digunakan dalam proyek, yaitu movies.csv dan ratings.csv. File movies.csv berisi metadata film sebanyak 10.329 rows x 3 columns, yang mencakup informasi seperti ID film, judul film, dan genre. Sementara itu, ratings.csv mencakup 105.339 rows × 4 columns data rating, yang menunjukkan interaksi pengguna dengan berbagai film.

Secara umum, kondisi data cukup baik, bersih, dan tidak terdapat missing value pada kedua file utama ini, sehingga tidak memerlukan proses pembersihan data yang kompleks di tahap awal.

### Deskripsi Variabel
Variabel-variabel pada dataset movies.csv dan ratings.csv adalah sebagai berikut:

1. **movies.csv**

| Variabel | Deskripsi |
| ------ | ------ |
| movieId | ID unik untuk setiap film |
| title | Judul film |
| genres | Genre dari film, bisa terdiri dari kombinasi beberapa genre yang dipisahkan dengan tanda "|" |

2. **ratings.csv**

| Variabel | Deskripsi |
| ------ | ------ |
| userId | ID unik pengguna |
| movieId | ID film yang dirating oleh pengguna |
| rating | Rating yang diberikan pengguna terhadap film, dalam skala 0.5 sampai 5.0 |
| timestemp | Waktu rating diberikan dalam format UNIX timestamp |

### Exploratory Data Analysis (EDA)
**Distribusi Rating**
Sebagian besar rating yang diberikan pengguna berada pada nilai 3.0 hingga 4.0, dengan rating 4.0 menjadi yang paling sering muncul.

![image](https://github.com/user-attachments/assets/ab8966f8-a133-46b1-b069-e45c540d55d8)

## Data Preparation
Tahap ini bertujuan untuk menyiapkan data sebelum digunakan dalam pemodelan sistem rekomendasi.

### 1. Cek Missing Value
Sebelum melakukan modeling, dilakukan pemeriksaan terhadap keberadaan missing value pada dataset `movies` dan `ratings`.  
Hasil pemeriksaan menunjukkan bahwa tidak terdapat missing value pada kedua dataset, sehingga tidak diperlukan teknik imputasi atau pembersihan lanjutan.

### 2. Split Data

Data `ratings` dibagi menjadi 80% data latih dan 20% data uji menggunakan teknik train-test split.

Alasan pemilihan rasio 80:20 adalah:
- 80% data latih cukup untuk membangun model yang kuat.
- 20% data uji cukup untuk mengevaluasi performa model secara objektif.
- Rasio ini merupakan praktik umum dalam machine learning untuk menjaga keseimbangan antara proses training dan validasi.

### 3. Encoding User dan Movie ID

Untuk membentuk User-Item Matrix, ID pengguna (`userId`) dan ID film (`movieId`) di-encode menjadi indeks numerik menggunakan teknik mapping sederhana.

Tujuannya adalah:
- Menyesuaikan struktur data ke dalam format matrix berbasis indeks.
- Mempermudah pemodelan Collaborative Filtering berbasis Matrix Factorization.

### 4. Membentuk User-Item Matrix

Dari data latih (`train_data`), dibentuk User-Item Matrix dengan pengguna sebagai baris dan film sebagai kolom.

## Modeling
Pada tahap ini, dikembangkan dua pendekatan utama untuk membangun sistem rekomendasi film, yaitu **Content-Based Filtering** dan **Collaborative Filtering**. Selain itu, sistem juga menyajikan **Top-N Recommendation** sebagai bentuk tampilan hasil rekomendasi terbaik untuk pengguna.

### 1. **Content-Based Filtering (CBF)**

Content-Based Filtering membangun sistem rekomendasi berdasarkan informasi konten film, seperti genre, untuk menentukan kemiripan antar film.

- **Teknik yang digunakan**:  
  - TF-IDF Vectorization: Mengubah teks genre film menjadi representasi numerik berdasarkan frekuensi term terhadap dokumen.
  - Cosine Similarity: Menghitung tingkat kemiripan antar film berdasarkan vektor TF-IDF.

- **Proses yang dilakukan**:
  1. Menerapkan TF-IDF pada kolom `genres`.
  2. Menghitung cosine similarity antar seluruh pasangan film.
  3. Memberikan rekomendasi film yang memiliki skor similarity tertinggi terhadap film favorit pengguna.

- **Top-N Recommendation CBF**:
  
  Untuk Content-Based Filtering, sistem menampilkan film-film dengan kemiripan genre tertinggi terhadap film yang dipilih pengguna.
  
  **Contoh:**
  
  Input film: `'Green Street Hooligans (a.k.a. Hooligans) (2005)'`
  
  Output rekomendasi berdasarkan genre serupa:

| Title | Genres |
|:------|:-------|
| Capote (2005) | Crime\|Drama |
| City by the Sea (2002) | Crime\|Drama |
| Mean Streets (1973) | Crime\|Drama |
| Rush (1991) | Crime\|Drama |
| No Country for Old Men (2007) | Crime\|Drama |


### 2. **Collaborative Filtering (CB)**

Collaborative Filtering membangun sistem rekomendasi berdasarkan pola interaksi pengguna terhadap item (film) tanpa melihat konten film.

- **Teknik yang digunakan**:
  - Matrix Factorization menggunakan Singular Value Decomposition (SVD). SVD memfaktorkan user-item matrix menjadi tiga matriks terpisah. Ini memungkinkan model memahami hubungan laten antara pengguna dan film. Model mempelajari representasi numerik dari user dan item dalam ruang dimensi lebih rendah. Kemudian memprediksi rating untuk kombinasi user-film yang belum ada.
  - Implementasi menggunakan library `Surprise`.

- **Proses yang dilakukan**:
  1. Melatih model SVD dengan data latih (train_data).
  2. Memvalidasi model menggunakan data uji (test_data).
  3. Menghasilkan prediksi rating untuk kombinasi user-film baru.

- **Top-N Recommendation CBF**:
  
  Untuk Collaborative Filtering, sistem:
  1. Memilih user secara acak.
  2. Mencari film yang belum ditonton oleh user tersebut.
  3. Menghitung prediksi rating untuk film-film yang belum ditonton.
  4. Menampilkan 10 film dengan prediksi rating tertinggi.
  
  **Contoh:**

  User terpilih: `User ID 60`

  Output rekomendasi berdasarkan prediksi model:

| Title | Genres |
|:------|:-------|
| Citizen Kane (1941) | Drama\|Mystery |
| Cinema Paradiso (Nuovo cinema Paradiso) (1989) | Drama |
| Paths of Glory (1957) | Drama\|War |
| Touch of Evil (1958) | Crime\|Film-Noir\|Thriller |
| Raise the Red Lantern (1991) | Drama |
| Princess Mononoke (1997) | Action\|Adventure\|Animation\|Drama\|Fantasy |
| Dr. Horrible's Sing-Along Blog (2008) | Comedy\|Drama\|Musical\|Sci-Fi |
| Cosmos (1980) | Documentary |
| Louis C.K.: Live at the Beacon Theater (2011) | Comedy |
| Black Mirror (2011) | Drama\|Sci-Fi |


### Kelebihan & Kekurangan

| Model | Kelebihan | Kekurangan |
| ------ | ------ | ------ |
| CBF | Tidak bergantung pada interaksi pengguna lain. | Terbatas pada informasi konten, sehingga kurang variatif. |
| CB | Mampu menangkap preferensi pengguna secara lebih kompleks. | Membutuhkan cukup banyak data interaksi dan tidak bekerja optimal untuk pengguna atau film baru. |
| Top-N | Sederhana dan efektif untuk menampilkan film-film terbaik secara umum. | Tidak bersifat personal; semua pengguna akan menerima daftar yang sama. |

## Evaluation
Untuk mengevaluasi performa sistem rekomendasi yang dibangun, digunakan metrik Root Mean Square Error (RMSE). Metrik ini sesuai digunakan untuk pendekatan Collaborative Filtering, karena model memprediksi rating numerik berdasarkan interaksi pengguna dan film.

RMSE mengukur seberapa jauh prediksi rating yang dihasilkan model dari rating sebenarnya. Nilai RMSE yang lebih kecil menunjukkan performa model yang lebih baik.

### Formula RMSE

$$
RMSE = \sqrt{\frac{1}{n} \sum_{i=1}^{n} (\hat{y}_i - y_i)^2}
$$

- $\hat{y}_i$ : rating yang diprediksi
- $y_i$ : rating aktual
- $n$ : jumlah data

### Hasil Evaluation
Model Collaborative Filtering yang dikembangkan berhasil mencapai RMSE sebesar 0.2331 pada data validasi di epoch ke-15. Ini menunjukkan bahwa model cukup baik dalam mempelajari preferensi pengguna dari data pelatihan, dengan tingkat kesalahan prediksi yang tergolong rendah.

![image](https://github.com/user-attachments/assets/77fbff98-d071-4ad3-bbf7-63249caa7c33)

Sementara itu, pendekatan Content-Based Filtering dan Top-N Recommendation berbasis agregasi rating dievaluasi secara kualitatif, dengan meninjau apakah hasil rekomendasi relevan dan mencerminkan film-film berkualitas berdasarkan popularitas dan rating. Evaluasi lebih lanjut terhadap kepuasan pengguna bisa dilakukan menggunakan feedback pengguna langsung atau A/B testing di tahap implementasi nyata.


[Kaggle]: <https://www.kaggle.com/datasets/ayushimishra2809/movielens-dataset>
