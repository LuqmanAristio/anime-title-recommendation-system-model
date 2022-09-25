# Laporan Proyek Machine Learning - Muhammad Luqman Aristio

## Project Overview

Diera digital saat ini, segala aspek kehidupan sudah berkembang dengan sangat pesat. Salah satu aspek yang paling berkembang dan sering diminati masyarakat yakni hiburan. Tidak hanya anak-anak, hampir seluruh kalangan usia tidak akan lepas dari yang namanya hiburan. Salah satu jenis hiburan yang paling sering masyarakat lakukan khususnya anak remaja yakni menonton anime. Hal ini dibuktikan dengan adanya data statistik yang dirilis oleh The Association of Japanese Animations (AJA) mengenai jumlah kontrak industri animasi Jepang dengan berbagai negara sepanjang tahun 2015. Data tersebut menunjukkan bahwa dari survei yang dilakukan kepada 31 negara terdapat 5 negara Asia Tenggara dengan Indonesia yang berada pada posisi kedua setelah thailand sebagai kontraktor penayang anime tertinggi.

![us-anime-market](https://user-images.githubusercontent.com/96041357/192114685-c2475d0d-00d2-4af5-a9d6-306a52cbe470.png)

**Gambar 1. Perkembangan Budaya Jepang di US**

Industri animasi dimulai kurang dari 50 tahun yang lalu, dan seiring berjalannya waktu, itu telah berkembang menjadi fenomena yang sangat populer. Ada dua gaya animasi yang umum, dan ini adalah animasi 2D dan 3D [1]. Selain itu juga berdasarkan jurnal Toi Yamane tahun 2020, Indonesia menyumbang 2 kota besar sebagai penggemar anime versi google. Dua kota tersebut adalah Surabaya dan Jakarta.[2]

Akibat pesatnya perkembangan anime inilah, hampir seluruh perusahaan dijepang merupakan perusahaan yang bergerak dibidang hiburan. Oleh karena itu banyak sekali anime setiap tahunnya rilis dari berbagai perusahaan dijepang. Bayangkan saja sudah berapa jumlah anime yang rilis dari awal terciptanya hingga saat ini, tentu tidaklah sedikit. Sebagai seorang yang penggemar anime, akan sangat sulit mencari jenis anime yang sesuai dengan selera jika tidak memiliki informasi atau referensi apapun. Oleh karena itu disinilah peluang kita untuk dapat berperan dalam pengembangan suatu sistem rekomendasi yang dapat membantu mereka agar dapat dengan mudah mencari jenis anime berdasarkan selera dan referensi yang mereka miliki.

## Business Understanding

### Problem Statements
- Dengan memanfaatkan data anime beserta fitur-fiturnya, bagaimana cara membuat sistem rekomendasi anime secara otomatis dan terstruktur?
- Selain itu juga, dengan memanfaatkan rating anime para penggemar, bagaimana membuat sistem rekomendasi yang disukai oleh pengguna dan juga baru untuk mereka?
- Sebaik apa hasil sistem rekomendasi yang dihasilkan dengan memanfaatkan kedua informasi sebelumnya?

### Goals
- Dapat membangun dan menghasilkan sistem rekomendasi judul anime bserta genrenya yang dipersonalisasi pengguna
- Menghasilkan sistem rekomendasi yang sesuai dengan referensi penonton yang yang memberi rating sehingga penonton lainnya yang belum tau dapat menontonnya
- Mengukur peforma sistem rekomendasi dengan memanfaatkan metrik evaluasi yang sesuai

### Solution statements
- Menggunakan content based filtering untuk membuat sistem rekomendasi judul anime berdasarkan selera pengguna yang dipersonalisasi
- Menggunakan teknik collaborative filtering untuk membuat sistem rekomendasi anime berdasarkan rating pengguna yang bersesuaian
- Menggunakan metrik evaluasi seperti presisi dengan visualisasi atau tanpa visualisasi

## Data Understanding
Dataset yang digunakan merupakan dataset yang diunduh dari website kaggle dengan judul "Anime Recommendations Database" pada link berikut ini : https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database. Dataset tersebut berisi 2 file CSV yakni anime dan rating. 

Pada file anime CSV berisikan informasi sebagai berikut :
- anime_id : id anime berupa angka numerik
- name : judul anime dalam berbagai bahasa
- genre : genre anime dalam bahasa inggris seperti action
- type : saluran tayang anime seperti TV, bioskop dll
- episodes : jumlah episode anime tersebut
- rating : rata-rata rating yang diberikan seluruh penonton
- members : jumlah penonton yang memberi rating.

Pada file rating CSV berisikan informasi sebagai berikut :
- user_id : id user yang memberikan rating berupa angka numerik
- anime_id : id anime seperti pada file CSV anime
- rating : skor yang diberikan pada anime yang ditonton. Terdapat angka -1 yang menandakan bahwa user sudah menonton namun belum memberikan rating sedangkan 1-10 jika user sudah memberikan skor.

Dari kesemua fitur tersebut tidak terdapat data yang bernilai kosong atau NULL. Sehingga tidak dilakukan penanganan missing value diawal

### **EDA : Univariate Analysis**

Pada file anime csv jumlah sampel yang ada yakni 12.294. Kemudian dari hasil describe tidak ditemukan anomali pada data sehingga tidak dilakukan tindakan lebih lanjut. Berikutnya yakni mengecek genre unik yang ada dikarenakan genre akan berperan penting nantinya pada sistem rekomendasi. Dari hasil yang ada diperoleh lebih dari 3 ribu genre unik. Ternyata genre yang dimiliki sebuah judul anime berjumlah lebih dari 1 oleh karena itu genre unik menjadi lebih banyak karena hal tersebut. Kemudian dari fitur type, dapat dilihat bahwa sampel terbesar dipegang oleh TV kemudian diikuti oleh OVA dan Movie.

![s1](https://user-images.githubusercontent.com/96041357/192116202-f5e94bdc-7f27-41ab-942b-1e4d329d1c12.PNG)

**Gambar 2. Bar Chart Fitur type**

Berikutnya berpindah ke file berikutnya yakni rating. Tidak disangka jumlah rating yang tersedia yakni lebih dari 7 juta sampel. Hal tersebut dikarenakan nilai -1 yang menandakan bahwa anime tersebut belum dirating oleh pengguna namun tercantum dalam file. Setelah dilakukan eksplorasi, ditemukan bahwa hanya terdapat 73 ribu data user dan juga 6 juta data review valid yang bukan -1.

## Data Preparation

### Data Cleaning
Pada visualisasi sebelumnya, pada fitur **name** dataset anime.csv terdapat banyak sekali simbol atau angka yang tidak relevan. Kemudian juga pada fitur **genre** terdapat tanda koma pemisah yang harus kita hilangkan karena dapat berakibat nantinya nilai tersebut dianggap sebagai nilai float. Sehingga perlu dilakukan data cleaning pada kedua fitur tersebut. Untuk melakukannya dibuatlah 2 fungsi, fungsi pertama untuk fitur name dan fungsi kedua untuk fitur genre. Didalam fungsi tersebut terdapat metode re.sub() yang merupakan metode untuk melakukan replacement pada target. Nilai yang direplace adalah simbol-simbol seperti kutip, koma, kutip satu dan-lain-lain.

Sesudahnya, bisa dilihat pada fitur name sudah tidak terdapat simbol atau angka yang mengganggu kemudian pada fitur genre sudah tidak terdapat koma sebagai pemisah antar genre.Kemudian berikutnya yakni menggabungkan kedua file csv tersebut menjadi 1. Namun sebelum itu nama pada fitur rating file csv harus diubah karena akan bertabrakan dengan fitur rating pada file anime. Setelah berhasil digabungkan, dilakukan proses pengurutan data berdasarkan user id agar tampak lebih rapi. Kemudian sampel dengan nilai -1 pada fitur rating kita hilangkan karena tidak kita butuhkan. Terakhir dilakukan proses penghapusan beberapa nilai NULL pada fitur yang ada.

### Data Preprocessing
Pada tahap ini kita mengurutkan kembali data berdasarkan fitur anime_id karena akan kita gunakan kedepannya. Setelah itu kita hapus data duplikat pada kolom anime_id dikarenakan sebelumnya, kita baru menggabung dataset anime.csv dan rating.csv. Sehingga banyak sekali data duplikat akibat penggabungan tersebut, oleh karena itu akan kita hapus. Untuk menghapusnya cukup digunakan fungsi drop _duplicates dengan parameternya yakni anime_id sehingga hasilnya tidak ada data yang sama lagi pada fitur anime_id 

Berikutnya kita akan membuat dictionary agar bisa dilakukan tahapan TF-IDF. Namun sebelum itu kita perlu mengubah seluruh fitur kita dalam bentuk list terlebih dahulu. Pastikan juga ketiga ukuran fitur sama karena mereka berasal dari file yang sama juga. Kemudian tinggal membuat dictionary dengan pair dan keynya.

**Tabel 1. Dataset Siap Guna**
|   | id    | name                   | genre                                           |   |
|---|-------|------------------------|-------------------------------------------------|---|
| 0 | 15451 |    High School DxD New | Action Comedy Demons Ecchi Harem Romance School |   |
| 1 | 11617 |        High School DxD |        Comedy Demons Ecchi Harem Romance School |   |
| 2 |  8074 | Highschool of the Dead |                Action Ecchi Horror Supernatural |   |
| 3 | 11757 |       Sword Art Online |           Action Adventure Fantasy Game Romance |   |
| 4 | 11771 |       Kuroko no Basket |                    Comedy School Shounen Sports |   |

Bisa dilihat pada hasilnya bahwa jumlah sampel yang akan digunakan hanya 9000 saja setelah melewati berbagai tahapan sebelumnya. Selain itu juga pada tabel 1. fitur yang digunakan dalam content based filtering ini juga hanya 3 yakni id, name dan genre saja.

## Modeling
Seperti yang sudah dijelaskan dalam solution sebelumnya, kita akan menggunakan 2 model yakni content based dan collaborative filtering. 

### Content Based Filtering
Pada teknik ini, sistem rekomendasi dibuat berdasarkan perilaku pengguna itu sendiri. Content-based filtering mempelajari profil minat pengguna baru berdasarkan data dari objek yang telah dinilai pengguna. Algoritma ini bekerja dengan menyarankan item serupa yang pernah disukai di masa lalu atau sedang dilihat di masa kini kepada pengguna. Semakin banyak informasi yang diberikan pengguna, semakin baik akurasi sistem rekomendasi. Kelebihan dari algoritma ini sendiri tergolong lebih mudah dibandingkan collaborative dikarenakan informasi yang diperlukan tidak terlalu banyak. Namun terdapat kelemahan jika sistem tidak memiliki informasi yang cukup terkait pengguna tersebut yang pada akhirnya sistem hanya akan menduga-duga saja secara acak. Selain itu juga content based tidak dapat memberikan keterbaharuan bagi pengguna. 
Berikut tahapannya dalam kasus ini :

**TF-IDF Vectorizer**
Pada tahapan ini akan dilakukan vektorisasi dengan teknik TF-IDF. Tahapan ini sangat penting dalam content based filtering. Hal tersebut dikarenakan pada tahap inilah kita bisa melihat representasi fitur penting dari genre anime yang akan kita olah. Fitur akan direpresentasikan secara numerik dalam matriks. Pada tahap ini fitur yang digunakan adalah genre karena merupakan fitur terpenting dalam teknik ini. Hasilnya berupa mapping array berisi seluruh genre pada dataset yang berjumlah 48. Sangat berbanding jauh dengan genre unik diawal yakni 3 ribu.

**Tabel 2. Matriks hasil TF-IDF**

| name                                            | thriller | adventure |   shoujo |  fantasy | cars |
|-------------------------------------------------|---------:|----------:|---------:|---------:|-----:|
|                    Inaka Isha                   |      0.0 |  0.000000 | 0.000000 | 0.000000 |  0.0 |
|                12-sai. 2nd Season               |      0.0 |  0.000000 | 0.657658 | 0.000000 |  0.0 |
|            Kara no Kyoukai: Shuushou            |      0.0 |  0.000000 | 0.000000 | 0.000000 |  0.0 |
|             Kaiketsu Jouki Tanteidan            |      0.0 |  0.000000 | 0.000000 | 0.000000 |  0.0 |
| Kino no Tabi: The Beautiful World - Tou no Kuni |      0.0 |  0.428854 | 0.000000 | 0.438085 |  0.0 |

Tabel 2 diatas merupakan sampel dari bentuk matriks dari hasil TF-IDF sebelumnya. Bisa dilihat terdapat berbagai fitur baru yang merupakan setiap genre yang dipetakan dalam rentang 0 sampai 1 sesuai dengan dataset sebelumnya.

**Cosine Similarity**
Pada tahapan berikutnya akan dicari cosine similarity antara anime dengan genrenya. Tujuannya agar kita dapat menemukan kesamaan diantara berbagai anime berdasarkan genre mereka. Kesamaan tersebut diubah dalam bentuk numerik dan disusun menjadi matriks. Nilai numerik ini merupakan nilai kesamaan antar anime yang mulai dari rentang 0 sampai 1. Semakin tinggi nilainya maka semakin kuat kesamaan antar anime tersebut. 1 anime dapat memiliki kesamaan dengan banyak anime. Setelah semua tahapan ini selesai terakhir kita tinggal melakukan prediksi dengan membuat fungsi terlebih dahulu.

Fungsi ini dibangun berisikan 4 parameter penting yakni nama anime, similarity data berdasarkan cosine similarity sebelumnya, nama dan genre dari dataset dan juga jumlah N rekomendasi yang diinginkan. Cara kerja fungsi ini dengan mengambil N nilai tertinggi dari hasil cosine similarity. Berdasarkan nilai cosine similarity inilah kita dapat memberikan rekomendasi anime berdasarkan kemiripannya. Bisa dilihat sebelumnya diatur nilai N atau K nya adalah 10. Kemudian kita inputkan judul anime yang sudah kita tonton dan kita cari judul anime yang serupa dengannya.

**Tabel 3. Input Data**
|   |    id |             name |                                 genre |
|---|------:|-----------------:|--------------------------------------:|
| 3 | 11757 | Sword Art Online | Action Adventure Fantasy Game Romance |

**Tabel 4. Hasil Rekomendasi**
|   |                                        name |                                       genre |
|---|--------------------------------------------:|--------------------------------------------:|
| 0 |             Sword Art Online: Extra Edition |       Action Adventure Fantasy Game Romance |
| 1 |                         Sword Art Online II |       Action Adventure Fantasy Game Romance |
| 2 |             Sword Art Online II: Debriefing |               Action Adventure Fantasy Game |
| 3 |                       Ys IV: The Dawn of Ys |               Action Adventure Fantasy Game |
| 4 |               Slime Boukenki: Umi da, Yeah! |        Action Adventure Comedy Fantasy Game |
| 5 | Bakugan Battle Brawlers: Gundalian Invaders |       Action Adventure Fantasy Game Shounen |
| 6 |   Bakugan Battle Brawlers: Mechtanium Surge |       Action Adventure Fantasy Game Shounen |
| 7 |            Monster Strike: Mermaid Rhapsody |                         Action Fantasy Game |
| 8 |                              Monster Strike |                         Action Fantasy Game |
| 9 |                      Log Horizon 2nd Season | Action Adventure Fantasy Game Magic Shounen |

Bisa dilihat dari kedua tabel diatas, pada tabel 3 kita menginputkan anime dengan 5 genre kemudian hasilnya bisa dilihat pada tabel 4 terdapat 10 rekomendasi. Kemudian lihat bagian genre terlihat bahwa kesepuluh rekomendasi tersebut sangat terkait dengan input user sehingga dapat dikatakn sistem rekomendasi dengan content based ini berhasil. Di bagian akhir nanti akan dibahas metrik evaluasinya. 

### Collaborative Filtering
Pada teknik collaborative ini, sangat bergantung dengan rating atau pendapat dari orang lain (komunitas). Sehingga teknik ini sangat bagus jika digunakan pada orang atau pengguna yang masih baru atau hanya ada sedikit informasi tentangnya. Karena sistem ini akan secara otomatis mempelajari pengguna lainnya dan memberikan informasi yang relevan. Namun kekurangan teknik ini yakni lebih sulit dibandingkan content based, kemudian dataset yang digunakan juga lebih besar karena mencakup banyak pengguna sekaligus. Sebelumnya itu kita perlu memangkas dataset rating yang awalnya 6 juta menjadi 50k saja. Hal itu dikarenakan demi menghindari lamanya proses trainin nanti. Kemudian tahapan berikutnya yakni :

**Data Preprocessing Kembali**
Pada tahap ini kita akan kembali mengolah data dengan menyandikan fitur user id dan anime_id. Namun sebelum itu ubah dulu menjadi list, kemudian berikutnya akan dilakukan mapping hasil encode ke dataframe. Kemudian kita perlu menghitung jumlah user, anime, min rating dan max rating untuk tahapan berikutnya nanti. Kemudian kita perlu mengacak dataset demi menghindari bias. Setelah diacak, kita bagi dataset menjadi 80% data training dan 20% data testing. Dan berikutnya kita masuk ke tahapan training, pada tahapan ini kita akan menggunakan teknik embedding terhadap data user dan anime. Selanjutnya, lakukan operasi perkalian dot product antara embedding user dan resto. Selain itu, kita juga dapat menambahkan bias untuk setiap user dan resto.

**Traning** 
Digunakan class recommenderNet disini yang berasal dari library Keras dengan fungsi aktivasi sigmoid. Model kali ini menggunakan Binary Crossentropy untuk menghitung loss function, Adam (Adaptive Moment Estimation) sebagai optimizer, dan root mean squared error (RMSE) sebagai metrics evaluation. Dan berikutnya tinggal melakukan proses training dengan 50 epoch.

**Prediksi**
Pada prediksi collaborative disini, kita akan menggunakan sampel acak dari dataset dan definisikan variabel anime_not_visited yang merupakan daftar anime yang belum pernah ditonton oleh pengguna. Karena sejak awal kelebihan dari collborative adalah cocok untuk pengguna baru oleh karena itu diatur not_visited. Selanjutnya, untuk memperoleh rekomendasi restoran, gunakan fungsi model.predict().

**Tabel 5. Hasil Collaborative**
|                                                                       Showing recommendations for users: 511 |
|-------------------------------------------------------------------------------------------------------------:|
|                                                                                  =========================== |
|                                                                            anime with high ratings from user |
|                                                                             -------------------------------- |
|                          Zero no Tsukaima : Action Adventure Comedy Ecchi Fantasy Harem Magic Romance School |
|                               Rosario to Vampire : Comedy Ecchi Fantasy Harem Romance School Shounen Vampire |
|                            Code Geass: Hangyaku no Lelouch : Action Mecha Military School Sci-Fi Super Power |
|                          Code Geass: Hangyaku no Lelouch R2 : Action Drama Mecha Military Sci-Fi Super Power |
|                                            Tengen Toppa Gurren Lagann : Action Adventure Comedy Mecha Sci-Fi |
|                                                                             -------------------------------- |
|                                                                                  Top 10 anime recommendation |
| --------------------------------                                                                             |
| Gintama Movie: Kanketsu-hen - Yorozuya yo Eien Nare : Action Comedy Historical Parody Samurai Sci-Fi Shounen |
| Gintama: Enchousen : Action Comedy Historical Parody Samurai Sci-Fi Shounen                                  |
| Uchuu Kyoudai : Comedy Sci-Fi Seinen Slice of Life Space                                                     |
| Kuroshitsuji: Book of Murder : Comedy Demons Fantasy Historical Mystery Shounen Supernatural                 |
| Haikyuu!!: Karasuno Koukou VS Shiratorizawa Gakuen Koukou : Comedy Drama School Shounen Sports               |
| Gintamaand : Action Comedy Historical Parody Samurai Sci-Fi Shounen                                          |
| Kimi no Na wa. : Drama Romance School Supernatural                                                           |
| Poyopoyo Kansatsu Nikki : Comedy Seinen Slice of Life                                                        |
| Ginga Eiyuu Densetsu : Drama Military Sci-Fi Space                                                           |
| Mobile Police Patlabor: On Television : Comedy Mecha Police Sci-Fi                                           |

Bisa dilihat dari hasil rekomendasi tersebut, genre genre yang diberikan oleh sistem collaborative filtering sangat serupa dengan yang disukai oleh user. Oleh karena itu dapat dianggap bahwa sistem ini berjalan sebagai mana mestinya.

## Evaluation

### Content Based Filtering
Pada teknik ini, untuk metrik evaluasi yang digunakan cukup sederhana yakni presisi. Namun karena kita bukan melakukan supervised learning jadi tidak bisa secara otomatis dilakukan. Oleh sebab itu saya membuat teknik saya sendiri dengan membandingkan hasil rekomendasi yang relevan. Pertama dilihat dari genre anime yang diinputkan kemudian kita bandingkan genre yang diinputkan dengan genre dari hasil rekomendasi. Jumlah genre benar pada setiap hasil rekomendasi akan dibagi dengan keseluruhan genrenya. Contoh semisal terdapat 3 genre pada anime input. Kemudian kita bandingkan dengan salah satu genre hasil rekomendasi. Ternyata terdapat 6 genre pada hasil rekomendasi, kemudian 3 diantaranya sama dengan genre input maka hasilnya adalah 3 / 6 yakni 0.5. Nilai 0.5 ini adalah nilai presisi pada satu hasil rekomendasi. Untuk mendapatkan keseluruhan nilai presisi kita perlu menambahkannya dan membaginya dengan jumlah N rekomendasi. Sehingga didapatkan rata-rata presisinya. Berikut adalah hasil presisi pada anime berjudul "Sword Art Online" :

![s6](https://user-images.githubusercontent.com/96041357/192118635-e82ddbcd-613e-4e54-a146-2fd86757a8b4.PNG)

**Gambar 3. Hasil Presisi**

### Collaborative Filtering
Pada teknik ini, metrik evaluasi yang digunakan adalah nilai RMSE saat melakukan training data. Pada training tersebut terdapat 50 epoch dengan hasil sebagai berikut :

![s7](https://user-images.githubusercontent.com/96041357/192118692-f5842883-e841-4403-a6a6-7ac3123a5872.PNG)

**Gambar 4. Hasil Epoch Terakhir**

![s8](https://user-images.githubusercontent.com/96041357/192118707-11d031f0-6b98-4d1f-8bcf-a74cd28cd787.PNG)

**Gambar 5. Matriks Model**

Bisa dilihat dari hasil matriks diatas bisa dilihat pada data training nilai RMSE terus menurun hingga menyentuh dibawah 0.13. Kemudian pada data testing nilai RMSE stabil antara 0.15 dan 0.14. Hal ini merupakan hasil yang baik mengingat sistem rekomendasi yang akurat sangat sulit dibuat.

# Kesimpulan
Dari segala percobaan tersebut, dapat disimpulkan seluruh goal kita tercapai. Pertama yakni membangun model dengan teknik content based mampu memberikan hingga 10 rekomendasi dengan presisi mencapai 73%. Kemudian yakni dapat membuat sistem rekomendasi dengan tekni collaborative berdasarkan rating pengguna mampu tercapai dengan nilai RMSE dibawah 0.15. Terakhir metrik evaluasi mampu menunjukan hasil yang baik dan memuaskan sesuai dengan target.

# Referensi
[1]   Fuentes, M.A.R. Atendido, M.L.L. Gecale, J.N, "_STUDENTS’ PERCEPTION ON THE EFFECT OF WATCHING ANIME_", 2018
[2]   Yamane, T.,"_Kepopuleran dan Penerimaan Anime Jepang di Indonesia_", vol. 7, no. 1, pp. 68-82, Maret 2020
