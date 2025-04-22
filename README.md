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

Pertama, dilakukan pemeriksaan missing values pada dataset movies dan ratings, dan hasilnya menunjukkan tidak ada data yang hilang, sehingga tidak diperlukan proses imputasi.

```sh
# Cek Missing Value
print(movies.isnull().sum())
print(ratings.isnull().sum())
```

Selanjutnya, data ratings dibagi menjadi data latih (80%) dan data uji (20%) menggunakan train_test_split. Ini penting untuk mengevaluasi performa model secara objektif.

```sh
# Split Data
train_data, test_data = train_test_split(ratings, test_size=0.2, random_state=42)

print('Train:', train_data.shape)
print('Test:', test_data.shape)
```

Setelah itu, kolom userId dan movieId diubah menjadi indeks numerik (user_idx dan movie_idx) menggunakan teknik encoding, agar data dapat diolah dalam bentuk matriks.
```sh
# Encode User ID dan Movie ID
user_ids = ratings['userId'].unique()
movie_ids = ratings['movieId'].unique()

user_to_index = {uid: idx for idx, uid in enumerate(user_ids)}
movie_to_index = {mid: idx for idx, mid in enumerate(movie_ids)}

ratings['user_idx'] = ratings['userId'].map(user_to_index)
ratings['movie_idx'] = ratings['movieId'].map(movie_to_index)

train_data['user_idx'] = train_data['userId'].map(user_to_index)
train_data['movie_idx'] = train_data['movieId'].map(movie_to_index)

test_data['user_idx'] = test_data['userId'].map(user_to_index)
test_data['movie_idx'] = test_data['movieId'].map(movie_to_index)
```

Langkah terakhir adalah membentuk user-item matrix dari data latih, menggunakan pivot_table, lalu mengisi nilai kosong dengan 0 menggunakan fillna(0). Matriks ini digunakan sebagai input pada model collaborative filtering.

```sh
# Buat User-Item Matrix
user_item_matrix = train_data.pivot_table(index='user_idx', columns='movie_idx', values='rating')
user_item_filled = user_item_matrix.fillna(0)
```

## Modeling
Pada tahap ini, dikembangkan tiga pendekatan sistem rekomendasi: Content-Based Filtering, Collaborative Filtering, dan Top-N Recommendation berdasarkan rating. Setiap pendekatan digunakan untuk menghasilkan rekomendasi film dengan mekanisme dan logika yang berbeda.

1. **Content-Based Filtering (CBF)**

Pendekatan ini menggunakan informasi konten film, khususnya fitur genres, untuk mengukur kemiripan antar film menggunakan TF-IDF vectorization dan cosine similarity. Sistem kemudian merekomendasikan film yang memiliki genre serupa dengan film yang disukai pengguna.

2. **Collaborative Filtering (CB)**

Pendekatan ini membangun sistem rekomendasi berdasarkan interaksi historis pengguna, menggunakan teknik matrix factorization. Dengan mempelajari pola rating dalam user-item matrix, model dapat memprediksi preferensi pengguna terhadap film yang belum ditonton dan memberikan rekomendasi yang bersifat personal.

3. **Top-N Recommendation**

Selain dua pendekatan utama, sistem juga menyajikan rekomendasi film berdasarkan statistik agregat rating. Rata-rata rating dan jumlah rating dihitung menggunakan groupby, kemudian difilter berdasarkan batas minimal jumlah reviewer untuk menjaga kualitas rekomendasi.

Fungsi top_n_movies() dikembangkan untuk mengambil N film terbaik dengan rating tertinggi dan jumlah reviewer tertentu. Pendekatan ini sangat berguna untuk menyarankan film-film populer yang terbukti disukai banyak orang.

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
