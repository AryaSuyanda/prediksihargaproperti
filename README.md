# Laporan Proyek Machine Learning - Prediksi Harga Properti

## Domain Proyek

Proyek ini berada dalam domain **real estate**, khususnya pada sub-domain **prediksi harga properti**. Pasar properti di perkotaan besar, seperti Jakarta Selatan (berdasarkan data yang terlihat pada output 'NAMA RUMAH'), dikenal sangat dinamis dan kompleks. Harga properti dipengaruhi oleh berbagai faktor, mulai dari karakteristik fisik properti (luas tanah, luas bangunan, jumlah kamar), fasilitas (garasi, kamar mandi), hingga lokasi dan kondisi pasar secara makro.

Memahami dan memprediksi harga properti secara akurat menjadi tantangan sekaligus peluang penting bagi berbagai pihak. Bagi calon pembeli atau investor, informasi harga yang akurat dapat membantu dalam pengambilan keputusan investasi yang cerdas. Bagi pengembang properti, prediksi harga membantu dalam perencanaan pembangunan dan strategi penetapan harga yang kompetitif. Sementara itu, bagi agen properti, model prediksi dapat meningkatkan efisiensi dalam valuasi properti dan negosiasi.

Secara tradisional, valuasi properti seringkali mengandalkan penilaian manual oleh ahli yang mungkin subjektif dan memakan waktu. Dengan kemajuan *machine learning*, dimungkinkan untuk membangun model prediktif yang lebih objektif, konsisten, dan efisien berdasarkan data historis properti. Model ini dapat mengidentifikasi pola dan hubungan kompleks antara fitur-fitur properti dan harganya yang mungkin sulit dilihat oleh manusia. Oleh karena itu, proyek ini berupaya memanfaatkan *machine learning* untuk mengatasi tantangan tersebut dan menyediakan alat prediksi harga properti yang lebih andal.

## Business Understanding

Proses klarifikasi masalah dalam proyek ini berfokus pada bagaimana teknologi *machine learning* dapat memberikan nilai tambah dalam pasar properti.

### Problem Statements

1.  **Kurangnya transparansi dan objektivitas dalam valuasi harga properti:** Valuasi properti seringkali didasarkan pada pengalaman atau perkiraan agen/penilai, yang dapat menghasilkan harga yang tidak konsisten atau bias. Ini menyulitkan pembeli dan penjual untuk menentukan harga yang adil.
2.  **Efisiensi waktu dan sumber daya dalam proses penilaian properti:** Penilaian properti secara manual membutuhkan waktu dan keahlian khusus, yang dapat menghambat kecepatan transaksi di pasar properti yang bergerak cepat.
3.  **Kesulitan mengidentifikasi faktor-faktor kunci yang paling mempengaruhi harga properti:** Dengan banyaknya variabel yang terlibat, sulit bagi individu untuk secara intuitif menentukan fitur properti mana yang memiliki dampak paling signifikan terhadap harganya.

### Goals

1.  **Membangun model prediksi harga properti yang akurat dan objektif:** Tujuan utama adalah mengembangkan model regresi yang mampu memprediksi harga properti berdasarkan karakteristiknya dengan tingkat akurasi yang dapat diterima.
2.  **Mengotomatiskan proses estimasi harga properti:** Dengan adanya model prediktif, proses valuasi dapat dilakukan secara lebih cepat dan dengan intervensi manusia yang minimal, sehingga meningkatkan efisiensi.
3.  **Mengidentifikasi fitur-fitur properti yang paling berpengaruh terhadap harga:** Model juga akan memberikan wawasan tentang fitur-fitur seperti luas bangunan, luas tanah, atau jumlah kamar yang paling berkorelasi dengan harga properti, memberikan pemahaman yang lebih baik tentang nilai properti.

### Solution Statements

Untuk mencapai tujuan-tujuan di atas, kami mengajukan solusi berikut:

1.  **Pengembangan Model Regresi Berbasis XGBoost dengan Hyperparameter Tuning:**
    * Kami akan menggunakan algoritma XGBoost (`XGBRegressor`) karena dikenal memiliki kinerja tinggi dan robust dalam berbagai tugas regresi, termasuk prediksi harga. XGBoost mampu menangani non-linearitas dan interaksi antar fitur dengan baik.
    * Untuk mengoptimalkan kinerja model dan mengurangi risiko *overfitting*, kami akan melakukan **penyetelan *hyperparameter*** menggunakan `GridSearchCV`. Proses ini akan secara sistematis mencari kombinasi *hyperparameter* terbaik (seperti `n_estimators`, `learning_rate`, `max_depth`, `subsample`, `colsample_bytree`) yang meminimalkan *Mean Squared Error* (MSE) pada validasi silang.
    * **Metrik Terukur:** Kinerja model akan diukur menggunakan `R2 Score`, `Mean Absolute Error (MAE)`, `Mean Squared Error (MSE)`, dan `Root Mean Squared Error (RMSE)` pada *testing set*. Tujuan adalah mencapai `R2 Score` yang tinggi (mendekati 1) dan `MAE`/`RMSE` yang rendah, menunjukkan prediksi yang akurat.

2.  **Analisis Pentingnya Fitur (Feature Importance):**
    * Setelah model terbaik ditemukan, kami akan menganalisis *feature importance* yang disediakan oleh model XGBoost. Ini akan memberikan peringkat fitur berdasarkan kontribusinya dalam membuat prediksi.
    * **Metrik Terukur:** Output berupa skor kepentingan relatif untuk setiap fitur. Fitur dengan skor tertinggi akan diidentifikasi sebagai faktor paling berpengaruh terhadap harga properti. Ini akan menjawab *problem statement* ketiga dan memberikan wawasan bisnis yang berharga.

## Data Understanding

Data yang digunakan dalam proyek ini adalah kumpulan data properti yang berisi informasi tentang karakteristik rumah dan harganya. Data ini dimuat dari file Excel bernama `DATA RUMAH.xlsx`. Asumsi, data ini adalah data historis properti dari area tertentu (misalnya, Jakarta Selatan, berdasarkan contoh nama rumah).

### Variabel-variabel pada Dataset

Berikut adalah daftar variabel (kolom) yang ada pada dataset awal:

| Kolom Asli | Kolom Setelah Rename | Deskripsi                                        | Tipe Data Awal | Catatan Pra-pemrosesan                          |
| :--------- | :------------------- | :----------------------------------------------- | :------------- | :---------------------------------------------- |
| `NO`       | `no`                 | Nomor urut/identifikasi unik properti            | `int64`        | Dihapus karena tidak relevan sebagai fitur      |
| `NAMA RUMAH` | `nama_properti`      | Deskripsi tekstual properti (lokasi, fitur singkat) | `object`       | Dihapus karena tidak relevan sebagai fitur numerik |
| `HARGA`    | `harga_rp`           | Harga jual properti dalam Rupiah                 | `int64`        | Target variabel; dikonversi ke `harga_juta`     |
| `LB`       | `luas_bangunan_m2`   | Luas bangunan properti (m$^2$)                   | `int64`        | Fitur prediktor                                 |
| `LT`       | `luas_tanah_m2`      | Luas tanah properti (m$^2$)                      | `int64`        | Fitur prediktor                                 |
| `KT`       | `kamar_tidur`        | Jumlah kamar tidur                               | `int64`        | Fitur prediktor                                 |
| `KM`       | `kamar_mandi`        | Jumlah kamar mandi                               | `int64`        | Fitur prediktor                                 |
| `GRS`      | `garasi_mobil`       | Kapasitas garasi (jumlah mobil)                  | `int64`        | Fitur prediktor                                 |

**Output dan Penjelasan (dari code Python):**
