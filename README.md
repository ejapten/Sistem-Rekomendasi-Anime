# Sistem-Rekomendasi-Anime

## A. Latar Belakang 
<p align="justify"> 
Laporan Grand View Research, pasar anime secara global mencapai nilai USD 25,46 milia pada 2020 dan diprediksikan tumbuh sebesar 9,5% per tahun hingga 2028 [1]. Di Indonesia, popularitas anime ditunjukan melalui beragam kegiatan seperti Anime Festival Asia Indonesia yang berhasil menarik antusiasme masyarakat dari berbagai usia melalui cosplay, kafe tematik, dan lain-lain [2]. Hal tersebut membuktikan bahwa anime telah mejadi bagian dari gaya hidup dan membentuk komunitas penggemar yang besar.
  <p align="justify"> 
Antusiasme masyarakat terhadap anime tercermin dari media yang mereka gunakan untuk menikmati konten anime. Berdasarkan salah satu penelitian, sebagian besar penonton Indonesia menonton dalam bentuk (64.8%), YouTube (48.8%), internet (24.4%). Hal ini menunjukan bahwa akses terhadap anime sangat luas [3].  Namun, dengan ribuan judul anime yang tersedia, baik dari genre aksi, petualangan, komedi, hingga slice of life, banyak pengguna merasa kesulitan menentukan anime yang sesuai dengan selera atau preferensinya.
    <p align="justify"> 
Berdasarkan analisis klaster dari penelitian sebelumnya, terlihat adanya segmentasi dalam preferensi penonton anime, mulai dari anime yang sangat populer dengan rating tinggi hingga anime berkualitas yang kurang dikenal masyarakat umum [1]. Lalu, dalam penelitian Novianti (2007) menyatakan bahwa peneitian yang dilakukan dalam beberapa universitas menunjukan sebanyak 58%  peminat anime dan drama Jepang yang masuk ke Jepang [4]. Dengan demikian, antusiasme terhadap anime sangatlah besar, melihat fenomena tersebut dibutuhkan sebuah sistem yang mampu memberikan rekomendasi anime secara personal dan relevan bagi setiap pengguna.

## B. Business Understanding
### a. Problem Statement

1. Banyak sekali jumlah anime yang ada membuat user kesulitan dalam menonton sesuai preferensi mereka (cbf)
2. Penonton anime terkadang FOMO terhadap anime yang sedang trend di komunitas mereka (cf)

### b. Goals 
1. Membantu pengguna menemukan anime yang mirip dengan anime yang mereka sukai, berdasarkan kemiripan genre, jenis, sumber cerita, dan popularitas umum
2. Memberikan rekomendasi yang muncul dari pola komunitas

### c. Solution Statement
1. Melakukan pengumpulan data
2. Melakukan pembersihan dan transformasi data
3. Menggunakan 2 model yaitu Content-Based Filtering (CBF) untuk menyelesaikan permasalahan masalah 1 lalu mencapai tujuan 1 dan Collaborative Filtering (CF) untuk menyelesaikan masalah 2 lalu mencapai tujuan 2
4. Evaluasi Model menggunakan precissio@k dan Loss Curve
5. Memberikan Contoh hasil dari sistemk rekomendasi

## C. Load & Data Understanding
|Sumber data |https://www.kaggle.com/datasets/vishalmane109/anime-recommendations-database|
|----|----|

Dataset untuk keperluan proyek dan analisis ini diperoleh dari Kaggle. Dataset tersebut berjumlah 17002 dan 15 Variabel. Berikut Fungsi setiap Variabel:

|Nama Variabel |Deskripsi|
|----|----|
|Anime_id | ID unik untuk setiap anime.|
|Title | Judul anime.|
|Genre | Daftar genre atau kategori anime (misal: Action, Comedy).|
|Synopsis | Ringkasan cerita dari anime.|
|Type | Jenis media anime (TV, Movie, OVA, dll).|
|Producer | Perusahaan yang memproduksi anime.|
|Studio Studio | animasi yang mengerjakan anime.|
|Rating Skor | rata-rata dari pengguna (biasanya dari 1–10).|
|ScoredBy | Jumlah pengguna yang memberi rating.|
|Popularity | Peringkat popularitas berdasarkan jumlah anggota dan aktivitas pengguna.|
|Members | Jumlah pengguna yang menambahkan anime ke daftar mereka (watching, completed, dll).|
|Episodes | Jumlah episode anime.|
|Source | Sumber cerita anime (Original, Manga, Game, dll).|
|Aired | Tanggal tayang atau rilis anime.|
|Link| Tautan ke halaman anime di MyAnimeList.|

<p align="justify"> 
Data anime tersebut mempunyai tipe data berjumlah 6 numerik dan 9 kategorikal. Selanjutnya terdapat missing value pada banyak variabel kecuali anime_id, Title, dan Members. Variabel terbanyak mempunyai missing value adalah Producer dan Studio dimana hampir setengah dari jumlah data. Dilakukan juga pengecekan duplikat data yang terindikasi sebanyak 63 duplikat data. 

## D. Data Preparation 
Data preparation merupakan tahapan untuk membersihkan data agar bisa diproses saat pemodelan. 
### a. Seleksi Feature/variabel/kolom
<p align="justify"> 
Langkah pertama adalah memilih kolom-kolom yang relevan sebagai representasi karakteristik dari setiap anime. Kolom tersebut adalah Title, Genre, Type, Rating, Popularity, Member dan Source. Kolom-kolom ini akan digunakan untuk membandingkan kemiripan antar anime berdasarkan atribut-atribut yang dimiliki. Fitur-fitur tersebut nantinya akan digunakan untuk menghitung kemiripan antar anime menggunakan pendekatan TF-IDF dan Cosine Similarity pada teknik Content-Based Filtering. Swain itu akan dilakukan encoding dan vektorisasi pada teknik Collaborative Filtering.

### b. Menangani Missing Value
<p align="justify"> 
Untuk memastikan kualitas data dan mencegah error saat proses pemodelan, dilakukan penanganan terhadap nilai yang hilang (missing value) pada beberapa kolom penting.
  
 <p align="justify"> 
1. Kolom kategorikal seperti Genre, Type, dan Source yang memiliki nilai kosong diisi dengan label "Unknown". Pendekatan ini dipilih agar informasi tetap dapat digunakan dalam perbandingan antar anime, tanpa menghilangkan baris data yang potensial berguna.

 <p align="justify"> 
2. Kolom numerik seperti Rating dan Popularity diisi dengan nilai median. Median dipilih karena lebih robust terhadap nilai ekstrim (outlier) dibandingkan rata-rata (mean), sehingga distribusi data tetap seimbang dan representatif.

### c. Menangani Duplikat
 <p align="justify"> 
Setelah dilakukan pemeriksaan terhadap data, ditemukan adanya 78 baris duplikat yang memiliki nilai identik pada seluruh kolom terpilih. Untuk itu, dilakukan proses penghapusan duplikat guna memastikan bahwa setiap entri anime bersifat unik. Setelah penghapusan, indeks data juga dilakukan reset agar struktur data tetap teratur dan rapi untuk tahap pemrosesan selanjutnya.

### d. Penyederhanaan pada kolom Source
 <p align="justify"> 
Kolom Source merepresentasikan asal cerita dari sebuah anime, seperti Manga, Light Novel, Game, dan lain sebagainya. Namun, terdapat dua nilai kategori yang bersifat tidak spesifik, yaitu Other dan Unknown. Keduanya tidak memberikan informasi yang jelas mengenai sumber cerita asli dari anime tersebut .Untuk menyederhanakan kategori dan mengurangi ambiguitas saat proses pemodelan, nilai Other digabungkan ke dalam kategori Unknown

### e. Pra-pemrosesan Kolom Genre
 <p align="justify"> 
Kolom Genre awalnya berbentuk string, sehingga dikonversi terlebih dahulu menjadi list agar tiap genre bisa diproses secara terpisah. Jika ada nilai kosong atau tidak terdeteksi, genre diisi dengan 'Unknown' agar tidak menghasilkan list kosong. Selanjutnya, genre dalam list dinormalisasi dengan:

* Mengubah huruf menjadi lowercase

* Mengganti spasi (' ') dan tanda strip ('-') menjadi underscore ('_')

Genre yang sudah dibersihkan kemudian digabung kembali menjadi satu string

### f. Penggabungan Kolom Type dan Source
 <p align="justify"> 
Untuk memberikan konteks yang lebih spesifik mengenai format dan asal cerita dari masing-masing anime, kolom Type dan Source digabung menjadi satu kolom baru bernama Type_Source.

 <p align="justify"> 
Langkah ini bertujuan agar model dapat membedakan kombinasi yang memiliki arti berbeda, misalnya "TV_Original" dan "Movie_Manga", yang secara konteks merepresentasikan pengalaman menonton dan sumber cerita yang berbeda.

 <p align="justify"> 
Setelah penggabungan, kolom Type dan Source yang asli dihapus karena informasinya sudah terwakili dalam kolom baru tersebut.

### g. Data Preparation tambahan untuk metode collaborative filtering (CF)
 <p align="justify"> 
Sebelumnya dilakukan tahap data preparation digunakan untuk content-based filtering (CBF) maupun collaborative filtering (CF), namun akan dilakukan persiapan data tambahan yang khusus untuk metode collaborative filtering (CF). Berbeda dengan CBF yang memanfaatkan fitur konten anime, CF berfokus pada pola interaksi pengguna terhadap anime

#### 1. Penyalinan Data untuk CF
 <p align="justify"> 
Untuk memisahkan proses pengolahan data khusus metode collaborative filtering (CF), dilakukan penyalinan data dari dataset utama yang sudah melalui tahap persiapan umum. Data yang disalin ini diberi nama anime_cf sebagai dasar untuk langkah-langkah pemrosesan selanjutnya yang fokus pada CF.

#### 2. Penambahan Kolom user_id untuk (CF)
 <p align="justify"> 
Metode Collaborative Filtering (CF) membutuhkan data interaksi antara pengguna dan item (anime), misalnya rating yang diberikan oleh setiap pengguna. Namun, dataset yang digunakan hanya berisi informasi anime tanpa data pengguna yang memberikan rating.
   <p align="justify"> 
Untuk mengatasi hal ini, dibuatlah kolom baru bernama user_id yang berisi ID pengguna secara berurutan dari 00001 sampai 16924. Penambahan user_id ini bersifat simulasi atau dummy, agar struktur data memenuhi format yang diperlukan oleh algoritma CF, yaitu berupa relasi antara pengguna dan item.

#### 3. Encoding Data untuk CF
   <p align="justify"> 
Agar algoritma Collaborative Filtering dapat bekerja lebih efisien, user_id dan Title akan dilakukan encoding dengan menyiimpan nilai kedua kolom dalam list, lalu mapping atau membuat pemetaan, selanjutnya Membuat pemetaan sebaliknya dari angka ke nilai asli, supaya hasil prediksi nanti bisa diterjemahkan kembali ke bentuk teks, dan Menambahkan kolom baru ke data (identity_user dan anime_title) yang berisi nilai angka hasil encoding dari user_id dan Title.

#### 4. Prepare Data Numerik (Rating) 
 <p align="justify"> 
Pada tahap ini, kolom Rating diubah menjadi tipe data numerik float agar dapat diproses dengan baik oleh algoritma machine learning.

 <p align="justify"> 
Selanjutnya, dihitung jumlah unik pengguna (num_users) dan anime (num_anime) berdasarkan hasil encoding sebelumnya. Informasi ini penting untuk memahami skala dataset yang akan digunakan dalam model.Terakhir, diperiksa rentang nilai rating dari data, yaitu antara 1.0 hingga 10.0, yang merupakan skor penilaian standar dari pengguna terhadap anime.

#### 5. Pengacakan Data dan Split Data
 <p align="justify"> 
Untuk memastikan model tidak bias terhadap urutan data, dataset diacak secara acak dengan menggunakan random_state agar hasil pengacakan bisa direplikasi.
Selanjutnya, dibuat variabel input x yang berisi pasangan angka dari user dan anime (hasil encoding), serta variabel target y berupa rating yang telah dinormalisasi ke rentang 0 sampai 1. Data dibagi menjadi dua yaitu train (80%) dan validation (20%)

## E. Pemodelan Data menggunaan 2 metode

### a. Menggunakan Content Based Filtering (CBF)
 <p align="justify"> 
CBF adalah rekomendasi sistem dengan mencari kemiripan antar item berdasarkan fitur deskriptif seperti Genre dan Source. Hal tersebut bertujuan untuk merekomendasikan anime yang mirip dengan anime lain. 

#### 1. Salin data untuk CBF 
<p align="justify"> 
dilakukan penyalinan data ke dalam variabel anime_cbf dari data yang telah dipersiapkan sebelumnya. Hal ini bertujuan agar proses Content-Based Filtering dilakukan secara terpisah, tanpa mempengaruhi data yang digunakan untuk metode lainnya seperti Collaborative Filtering.

#### 2. Ekstraksi Fitur dengan TF-IDF
<p align="justify"> 
Kolom Genre dan Type Source dilakukan ekstraksi fitur menggunakan TF-IDF, hal ini akan mengubah representasi numerik yang mencerminkan seberapa penting suatu kata dalam dokumen. Dilakukan dengan beberapa langkah:
* TF-IDF Vectorizer diterapkan secara terpisah pada kolom Genre dan Type_Source.

* Data diubah ke dalam bentuk matriks sparse, lalu dikonversi ke bentuk dense matrix agar dapat diolah lebih lanjut.

* Hasil dari TF-IDF genre ditampilkan dalam bentuk DataFrame untuk melihat bobot masing-masing genre terhadap judul anime.
  
![image](https://github.com/user-attachments/assets/42c24b5c-8f4b-4605-a3ca-0245dd92a470)

<p align="justify"> 
Sebagai contoh hasil ekstraksi fitur dengan TF-IDF, dapat dilihat bahwa anime "One Baby Plus" memiliki nilai 0.676 untuk genre sci-fi, sedangkan "Onegai Teacher" memiliki nilai 0.465 untuk genre yang sama.
Hal ini menunjukkan bahwa genre sci-fi lebih dominan atau lebih merepresentasikan konten anime One Baby Plus dibandingkan Onegai Teacher. Semakin tinggi nilai TF-IDF suatu genre terhadap sebuah judul, maka semakin besar pengaruh genre tersebut dalam menggambarkan karakteristik anime tersebut.

#### 3. Cosine Similarity
<p align="justify"> 
Setelah mendapatkan representasi numerik dari fitur Genre dan Type_Source menggunakan TF-IDF, langkah selanjutnya adalah menghitung kemiripan antar anime menggunakan Cosine Similarity. Metode ini digunakan untuk mengukur seberapa mirip dua anime berdasarkan arah vektor fitur mereka.
  <p align="justify"> 
Karena model menggunakan dua fitur berbeda (Genre dan Type_Source), maka dilakukan perhitungan kemiripan secara terpisah untuk masing-masing fitur, lalu hasilnya dijumlahkan dan dirata-rata untuk mendapatkan skor akhir yang lebih representatif.
* cosine_sim_genre: Kemiripan antar anime berdasarkan genre.
* cosine_sim_type_source: Kemiripan berdasarkan jenis dan sumber cerita.
* cosine_sim_combined: Kombinasi dari kedua skor kemiripan tersebut.
 <p align="justify"> 
Nilai-nilai ini nantinya akan menjadi dasar dalam menghitung kemiripan antar anime untuk menghasilkan rekomendasi yang relevan. Hasil akhirnya disimpan dalam bentuk DataFrame dengan judul anime sebagai indeks dan kolom, sehingga setiap nilai dalam matriks merepresentasikan tingkat kemiripan antara dua anime

![image](https://github.com/user-attachments/assets/6b6db33d-96c3-49be-a5e1-820b2e1c58aa)

 <p align="justify"> 
Sebagai contoh hasil perhitungan kemiripan antar anime menggunakan cosine similarity, diperoleh bahwa anime "Internal Medicine" dan "Aniyome wa Ijippari" memiliki nilai kemiripan sebesar 1.0. Ini menunjukkan bahwa kedua anime tersebut memiliki kemiripan yang sangat tinggi berdasarkan fitur Genre dan Type_Source yang dianalisis—artinya mereka kemungkinan besar berasal dari jenis dan genre yang sangat mirip atau bahkan sama persis.
    <p align="justify"> 
Sebaliknya, anime "Bishoujo Senshi Sailor Moon" dan "Gin no Guardian" memiliki nilai kemiripan hanya 0.23, yang menunjukkan bahwa keduanya cukup berbeda dari segi genre maupun jenis dan sumber ceritanya.
       <p align="justify"> 
Nilai cosine similarity berkisar antara 0 hingga 1, di mana nilai mendekati 1 menunjukkan tingkat kemiripan yang tinggi, dan nilai mendekati 0 menunjukkan bahwa anime-anime tersebut kurang memiliki kesamaan fitur.

#### 4. sistem rekomendasi 
Sistem rekomendasi ini dibuat menggunakan pendekatan Content-Based Filtering (CBF), dengan memanfaatkan hasil cosine similarity antar anime berdasarkan fitur Genre dan Type_Source. Fungsi anime_recommendations() bekerja dengan cara:

* Menerima input judul anime dari pengguna.

* Mencari judul tersebut dalam matriks kemiripan. Jika tidak ditemukan, sistem mencari judul paling mirip menggunakan get_close_matches().

* Mengambil daftar anime dengan tingkat kemiripan tertinggi (dalam hal ini 5 anime teratas) dan mengabaikan judul input itu sendiri.


* Menampilkan daftar rekomendasi beserta informasi genre, type_source, dan skor kemiripan.
  
**Contoh Hasil 1:**

![image](https://github.com/user-attachments/assets/ff76620b-e25c-408b-86c7-f48e4420ceed)

  <p align="justify"> 
Sebagai contoh, ketika pengguna mencari rekomendasi untuk anime "Naruto", sistem menghasilkan lima rekomendasi teratas berdasarkan kemiripan konten. Rekomendasi tersebut mencakup "Boruto: Naruto Next Generations", "Rekka no Honoo", dan dua versi dari "Dragon Ball Kai". Anime-anime ini memiliki genre dan sumber cerita yang serupa, seperti aksi, petualangan, dan seni bela diri, serta berasal dari media yang sama (TV dan Manga). Skor kemiripan yang tinggi menunjukkan bahwa anime tersebut memiliki konten yang sangat mirip dengan Naruto, sehingga relevan untuk direkomendasikan.

**Contoh Hasil 2 :**

![image](https://github.com/user-attachments/assets/6d1e84f7-75b9-47d6-9c99-e0b2c78b52b7)

  <p align="justify"> 
Contoh pencarian rekomendasi untuk "Dragon Ball Kai" menghasilkan anime dengan konten serupa seperti "Dragon Ball Super" dan "Naruto", yang sama-sama bergenre aksi dan petualangan serta berasal dari media TV dan Manga, menunjukkan relevansi tinggi berdasarkan kemiripan fitur.

**Kekurangan dan Kendala:**

Saat ingin mencoba  pakai fungsi rekomendasi dengan judul "Shingeki no Kyojin", muncul error seperti ini:

![image](https://github.com/user-attachments/assets/fa9fe379-883b-40cb-8cb9-6fa8496beffb)

Lalu, dilakukan pengecekan pada judul tersebut “Shingeki no Kyojin” hasil nya ditemukan seperti berikut : 

![image](https://github.com/user-attachments/assets/59f03757-04b5-43e8-a54d-23e33a0ee691)

 <p align="justify">
Hal ini Variabel similarity_scores seharusnya bertipe pandas.Series agar sort_values() bisa dipanggil tanpa argumen tambahan. Namun, saat name_anime tidak persis ada di similarity_data.columns, indexing similarity_data[name_anime] menghasilkan DataFrame, bukan Series, sehingga sort_values() butuh argumen kolom dan akhirnya error.

 <p align="justify">
Meski judul yang mengandung "Shingeki no Kyojin" ada di dataset, fungsi belum berhasil melakukan pencocokan judul secara parsial dengan baik. Jadi, input judul yang tidak tepat menyebabkan pencarian similarity gagal, tipe data yang dihasilkan tidak sesuai, dan memicu error tersebut.

### b. Menggunakan Content Based Filtering (CBF)

#### a. Data preparation untuk CF
<p align="justify">
Sebelumnya, telah dilakukan proses data preparation khusus untuk metode Collaborative Filtering (CF) yang merupakan kelanjutan dari data preparation umum yang telah dilakukan. Pada tahap ini, data anime dilakukan penambahan kolom user_id agar membentuk struktur interaksi pengguna dan item yang dibutuhkan oleh CF. Selain itu, dilakukan encoding pada kolom user_id dan Title untuk memudahkan pemodelan selanjutnya. Lalu, dilakukan Split Data 80:20.

#### b. Training 
<p align="justify">
Pada tahap modeling untuk metode Collaborative Filtering (CF), digunakan model neural network yang dibangun dengan TensorFlow Keras. Model ini diberi nama RecommenderNet yang bertujuan untuk memprediksi rating interaksi antara pengguna (user_id) dan anime (anime_title).

##### Arsitektur Model : RecommenderNet
<p align="justify">
Model RecommenderNet menggunakan embedding untuk mengubah ID pengguna dan anime menjadi representasi vektor berdimensi rendah yang membantu model memahami fitur tersembunyi dari data. Setelah itu, interaksi antara pengguna dan anime dihitung dengan mengalikan vektor embedding mereka dan menambahkan bias khusus untuk pengguna dan anime tersebut. Selanjutnya, hasilnya diproses melalui tiga lapisan dense dengan aktivasi ReLU untuk menangkap pola hubungan yang lebih kompleks. Akhirnya, model menghasilkan prediksi rating yang sudah dinormalisasi antara 0 dan 1 menggunakan lapisan output dengan aktivasi sigmoid.

##### Melatih Model
<p align="justify">
Model RecommenderNet diinisialisasi dengan ukuran embedding sebesar 50, yang menentukan dimensi vektor representasi pengguna dan anime. Model kemudian dikompilasi menggunakan fungsi loss Mean Absolute Error (MAE) dan optimizer Adam dengan learning rate 0.001, serta metrik evaluasi Root Mean Squared Error (RMSE) untuk mengukur performa prediksi. Proses pelatihan dilakukan selama 50 epoch dengan batch size 64, menggunakan data training dan validasi yang telah dipersiapkan sebelumnya. Langkah ini bertujuan untuk mengoptimalkan bobot embedding dan parameter model agar mampu memprediksi rating anime dengan akurat.

#### c. Membuat Sistem Rekomendasi 
Dalam memberikan rekomendasi anime menggunakan CF, perlu dipersiapkan hal berikut:
1. Sampel acak untuk memberikan contoh bagaimana sistem merekomendasikan anime kepada user
2. Identifikasi anime yang sudah ditonton pengguna sebagai dasar preferensi
3. Identifikasi anime yang belum ditonton agar sistem rekomendasi difokuskan pada hal yang belum ditonton
4. Mengubah anime dan pengguna ke bentuk encoding numerik
5. Menyiapkan data input untuk model

Setelah itu, membuat prediksi dengan melakukan:
1. Membuat input untuk model prediks 
2. Melakuan prediksi skor
3. Mengurutkan hasil prediksi
4. Mengambil 10 daftar anime yang direkomendasikan
5. Menyusun data rekomendasi dengan informasi tambahan
6. Menampilkan rekomendasi

**Contoh Hasil**

![image](https://github.com/user-attachments/assets/15d153cd-2f8a-49d6-89f9-d51104a82a84)

<p align="justify">
Untuk user dengan ID 11531, sistem merekomendasikan 10 anime dengan prediksi rating tertinggi yang menunjukkan seberapa besar kemungkinan user menyukai anime tersebut. Setiap rekomendasi dilengkapi dengan rating asli, tingkat popularitas, genre, dan jumlah anggota komunitas, sehingga memberikan gambaran lengkap tentang kualitas dan karakteristik anime yang direkomendasikan.
  
## F. Evaluasi

### a. Evaluasi dengan Precision@k untuk CBF
 <p align="justify">
Precision@k merupakan metrik evaluasi yang mengukur proporsi rekomendasi teratas sebanyak k yang relevan dengan preferensi pengguna. Dalam konteks proyek ini, relevansi diukur berdasarkan kesamaan genre antara anime target dan anime yang direkomendasikan.

![image](https://github.com/user-attachments/assets/ae1d424f-5d6c-4442-aae3-ec72418744a2)

<p align="justify">
Sebagai contoh, nilai Precision@5 untuk anime "Naruto" adalah 0.20. Artinya, dari 5 rekomendasi teratas yang diberikan, hanya sekitar 20% (1 dari 5) yang memiliki genre yang sama dengan "Naruto". Hasil ini menunjukkan bahwa akurasi rekomendasi berdasarkan genre masih dapat ditingkatkan agar lebih relevan dengan preferensi pengguna.

### b. Evaluasi dengan Loss Curve untuk CF
Evaluasi pada model CF dengan Loss Curve. Loss Curve adalah sebuah grafik yang menunjukan perubahan loss atau kesalahan prediksi dalam training model setiap epoch.

![image](https://github.com/user-attachments/assets/92632d70-e064-4624-b4ab-b32a1d13c66d)

Berdasarkan Kurva Loss:
* Train Loss menurun dan stabil di angka rendah seiring bertambahnya epoch.
* Val Loss Setelah beberapa epoch awal, tidak menurun, justru tetap tinggi dan cenderung stagnan bahkan sedikit naik dengan fluktuasi.
* Gap besar antara train loss dan val loss dan Val loss tidak membaik
* Maka, Model mengalami Overfitting

## G. Kesimpulan 
<p align="justify">
Dalam menjawab permasalahan dan mencapai tujuan, perlu dilakukan tahapan seperti mengumpulkan data yang mendukung, membersihkan data, transformasi, pemodelan, bahkan evaluasi. Sistem rekomendasi mengembangkan dua pendekatan utama:

<p align="justify">
  
**1. Content-Based Filtering (CBF)** : merekomendasikan anime yang mirip berdasarkan konten seperti genre, jenis, dan sumber cerita. Dengan menggunakan pendekatan atau metode CBF, permasalahan pertama terselesaikan dan tujuan tercapai dalam merekomendasikan anime berdasarkan genre dan sumber cerita

<p align="justify">
  
**2. Collaborative Filtering (CF):** merekomendasikan anime berdasarkan pola rating atau interaksi pengguna lain dengan preferensi serupa. Dengan menggunakan metode CF, permasalahan dua terselesaikan dan tercapai juga tujuannya. Hal ini memungkinkan sistem menyarankan anime yang sedang trending atau disukai oleh pengguna dengan preferensi serupa, sehingga dapat mengatasi fenomena FOMO

<p align="justify">
Dengan menggunakan  kedua pendekatan ini, sistem rekomendasi mampu memberikan saran anime.
  
**Namun, kendala dan kekurangan tetaplah ada pada proyek ini:**

  <p align="justify">
    
**1. Pada CBF :** terjadi error karena input judul anime tidak tepat persis di data, sehingga fungsi mencari similarity malah menghasilkan tipe data yang salah (DataFrame bukan Series), sehingga sort_values() gagal dipanggil tanpa argumen. Jadi, intinya error muncul karena pencocokan judul di fungsi rekomendasi belum berjalan dengan baik. Saran perbaikan proyek meliputi peningkatan mekanisme pencocokan judul agar dapat menangani judul mirip atau kesalahan penulisan dengan lebih baik. Selain itu, perlu ditambahkan pengecekan tipe data sebelum proses agar fungsi tidak error saat hasil indexing bukan berupa Series. Memberikan pesan yang jelas atau opsi alternatif ketika judul tidak ditemukan juga penting untuk memperbaiki pengalaman pengguna. Terakhir, lakukan pengujian dengan berbagai variasi input untuk memastikan fungsi berjalan stabil dan andal.

<p align="justify">
  
**2. Pada CF :** Train Loss mengalami penurunan yang signifikan dan stabil di nilai yang rendah seiring bertambahnya epoch, Validation Loss (Val Loss) justru tidak menunjukkan perbaikan yang berarti setelah beberapa epoch pertama, bahkan cenderung stagnan atau naik, Terdapat gap yang besar antara train loss dan validation loss, yang menunjukkan bahwa model terlalu cocok pada data pelatihan (overfitting) dan gagal mengeneralisasi dengan baik pada data validasi. Solusi perbaikan : Tambahkan Dropout() setelah hidden layer untuk mengurangi ketergantungan pada neuron tertentu dan memperbaiki generalisasi, Gunakan EarlyStopping,  dan Perkuat Regularisasi


## Referensi
<p align="justify">
[1] 	N. Zalzabila and R. Prathivi, "Analisis Preferensi Penonton Anime berbasiskan Genre Film menggunakan Metode K-Means," Jurnal Penerapan Sistem Informasi (Komputer & Manajemen), vol. 6, no. 1, pp. 235-241, 2025. 
  <p align="justify">
[2] 	P. Safariani, "PENYEBARAN POP CULTURE JEPANG OLEH ANIME FESTIVAL ASIA (AFA) DI INDONESIA TAHUN 2012-2016," eJournal Ilmu Hubungan Internasional, vol. 5, no. 3, pp. 729-744, 2017. 
    <p align="justify">
[3] 	T. Yamane, "Kepopuleran dan Penerimaan Anime Jepang di Indonesia," Jurnal Ayumi, vol. 7, no. 1, pp. 68-82, 2020. 
      <p align="justify">
[4] 	N. Novianti, "DAMPAK DRAMA, ANIME, DAN MUSIK JEPANG TERHADAP MINAT BELAJAR BAHASA JEPANG," Jurnal LINGUA CULTURA, vol. 1, no. 2, pp. 151-156, 2007. 

















