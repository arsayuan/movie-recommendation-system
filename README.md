# Laporan Proyek Machine Learning - Nama Anda

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

Dataset ini terdiri dari dua file utama yang digunakan dalam proyek, yaitu movies.csv dan ratings.csv. File movies.csv berisi metadata film sebanyak 9.742 entri, yang mencakup informasi seperti ID film, judul film, dan genre. Sementara itu, ratings.csv mencakup 100.836 baris data rating, yang menunjukkan interaksi pengguna dengan berbagai film.

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



[Kaggle]: <https://www.kaggle.com/datasets/ayushimishra2809/movielens-dataset>
