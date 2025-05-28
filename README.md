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

![image](https://github.com/user-attachments/assets/6f8c6f4b-7750-4caa-b4ff-1717ad684147)

**Penjelasan Output:**
* **`Data berhasil dimuat.`**: Menunjukkan bahwa file data berhasil dibaca.
* **`Data Awal (5 baris pertama):`**: Menampilkan lima baris pertama data mentah, memperlihatkan kolom asli.
* **`Informasi Data Awal:`**: Ringkasan struktur `DataFrame`: ada 1010 baris dan 8 kolom. Sebagian besar kolom bertipe `int64` (numerik), dan 'NAMA RUMAH' bertipe `object` (string). Tidak ada nilai yang hilang.
* **`Bentuk DataFrame Awal: (1010, 8)`**: Mengkonfirmasi dimensi `DataFrame`: 1010 baris dan 8 kolom.

### Statistik Deskriptif

Berikut adalah ringkasan statistik deskriptif untuk fitur-fitur numerik dalam dataset bersih:

| Statistik | harga_rp (Miliar Rp) | luas_tanah_m2 | luas_bangunan_m2 | kamar_tidur | kamar_mandi | garasi_mobil |
| :-------- | :------------------- | :------------ | :--------------- | :---------- | :---------- | :----------- |
| **count** | 1010                 | 1010          | 1010             | 1010        | 1010        | 1010         |
| **mean** | 7.63                 | 276.54        | 237.43           | 4.67        | 3.61        | 1.92         |
| **std** | 7.34                 | 177.86        | 179.96           | 1.57        | 1.42        | 1.51         |
| **min** | 0.43                 | 40.00         | 25.00            | 2.00        | 1.00        | 0.00         |
| **25%** | 3.26                 | 150.00        | 130.00           | 4.00        | 3.00        | 1.00         |
| **50%** | 5.00                 | 216.50        | 165.00           | 4.00        | 3.00        | 2.00         |
| **75%** | 9.00                 | 350.00        | 290.00           | 5.00        | 4.00        | 2.00         |
| **max** | 65.00                | 1126.00       | 1400.00          | 10.00       | 10.00       | 10.00        |

**Penjelasan Output Statistik Deskriptif:**
* Kolom `harga_rp` memiliki rentang nilai yang sangat luas ($4.3 \times 10^8$ hingga $6.5 \times 10^{10}$) dengan standar deviasi yang tinggi ($7.3 \times 10^9$), menunjukkan variasi harga yang signifikan.
* Luas tanah rata-rata sekitar 276 m$^2$ dan luas bangunan rata-rata 237 m$^2$.

**Visualisasi Distribusi Fitur Numerik dan Identifikasi Outlier:**
![image](https://github.com/user-attachments/assets/c7fc4a05-e516-483e-8c4c-05d423e42005)

**Penjelasan Gambar:**
* **Histogram (atas):** Menunjukkan distribusi setiap fitur numerik. Terlihat `harga_rp`, `luas_tanah_m2`, dan `luas_bangunan_m2` cenderung memiliki distribusi yang miring ke kanan (positively skewed), mengindikasikan keberadaan beberapa nilai ekstrem (harga/luas yang sangat tinggi).
* **Box Plot (bawah):** Digunakan untuk mengidentifikasi *outlier*. Titik-titik di luar "jangkauan kumis" menunjukkan *outlier*. Terlihat jelas adanya *outlier* pada `harga_rp` dan `luas_bangunan_m2`, yang mengkonfirmasi adanya properti dengan harga atau luas yang sangat berbeda dari mayoritas data.

### Heatmap Korelasi Antar Fitur

![image](https://github.com/user-attachments/assets/73b87545-11b8-4ee6-b296-12d2eddf65cb)

**Penjelasan Gambar:**
* Heatmap ini menunjukkan koefisien korelasi Pearson antara setiap pasangan fitur. Warna merah/oranye menunjukkan korelasi positif yang kuat, biru menunjukkan korelasi negatif, dan putih/abu-abu menunjukkan korelasi lemah.
* `harga_juta` (variabel target) memiliki korelasi positif yang sangat kuat dengan `luas_bangunan_m2` (0.81) dan `luas_tanah_m2` (0.75), menunjukkan bahwa kedua fitur ini adalah prediktor harga yang paling dominan.
* Ada juga korelasi positif antara fitur-fitur prediktor itu sendiri, seperti `luas_tanah_m2` dan `luas_bangunan_m2` (0.74), yang wajar dalam konteks properti.

## Data Preparation

Tahapan persiapan data dilakukan untuk mengubah data mentah menjadi format yang sesuai untuk pemodelan *machine learning*. Urutan teknik yang digunakan adalah sebagai berikut:

1.  **Membuat Salinan DataFrame:**
    * **Kode:** `df = df_raw.copy()`
    * **Alasan:** Ini dilakukan untuk memastikan bahwa operasi pra-pemrosesan tidak memengaruhi DataFrame asli (`df_raw`), menjaga integritas data mentah.

2.  **Mengubah Nama Kolom (Renaming Columns):**
    * **Kode:**
        ```python
        df.columns = ['no', 'nama_properti','harga_rp', 'luas_tanah_m2', 'luas_bangunan_m2', 'kamar_tidur', 'kamar_mandi', 'garasi_mobil']
        ```
    * **Alasan:** Nama kolom asli (misalnya, 'LB', 'LT', 'GRS') seringkali tidak deskriptif atau konsisten. Mengubah nama kolom menjadi format yang lebih jelas dan konsisten (`luas_bangunan_m2`, `luas_tanah_m2`, `garasi_mobil`) meningkatkan keterbacaan kode dan pemahaman data.

    **Output (dari code Python):**
    ```
    Data setelah rename kolom (5 baris pertama): 
       no                                      nama_properti    harga_rp  luas_tanah_m2  luas_bangunan_m2  kamar_tidur  kamar_mandi  garasi_mobil 
    0   1  Rumah Murah Hook Tebet Timur, Tebet, Jakarta S...  3800000000            220               220            3            3             0 
    1   2  Rumah Modern di Tebet dekat Stasiun, Tebet, Ja...  4600000000  180  137   4   3    2 
    2   3  Rumah Mewah 2 Lantai Hanya 3 Menit Ke Tebet, T...  3000000000  267  250   4   4    4 
    3   4           Rumah Baru Tebet, Tebet, Jakarta Selatan   430000000             40                25            2            2             0 
    4   5  Rumah Bagus Tebet komp Gudang Peluru lt 350m, ...  9000000000  400  355   6   5    3 
    ```

3.  **Menghapus Kolom yang Tidak Relevan:**
    * **Kode:**
        ```python
        df = df.drop('no', axis=1)
        df = df.drop('nama_properti', axis=1)
        ```
    * **Alasan:** Kolom `no` adalah *identifier* unik yang tidak memiliki nilai prediktif. Kolom `nama_properti` berisi teks bebas yang memerlukan *Natural Language Processing* (NLP) yang lebih kompleks untuk diekstrak fiturnya, dan untuk proyek ini, dianggap tidak relevan secara langsung sebagai fitur numerik. Menghapus kolom ini mengurangi dimensi data dan fokus pada fitur-fitur struktural properti.

    **Output (dari code Python):**
    ```
    Data setelah menghapus kolom 'no' dan 'nama_properti' (5 baris pertama): 
         harga_rp  luas_tanah_m2  luas_bangunan_m2  kamar_tidur  kamar_mandi  garasi_mobil 
    0  3800000000            220               220            3            3             0 
    1  4600000000            180               137            4            3             2 
    2  3000000000            267               250            4            4             4 
    3   430000000             40                25            2            2             0 
    4  9000000000            400               355            6            5             3 
    ```

4.  **Penanganan Missing Value (Imputasi Median):**
    * **Kode:**
        ```python
        import numpy as np
        for col in df.select_dtypes(include=np.number).columns: 
            if df[col].isna().any(): 
                df[col] = df[col].fillna(df[col].median())
        ```
    * **Alasan:** Meskipun output menunjukkan tidak ada *missing values* pada dataset awal, langkah ini tetap penting sebagai praktik standar. Jika di masa depan ada data yang hilang, *missing values* pada kolom numerik akan diisi dengan nilai median kolom tersebut. Penggunaan median lebih robust terhadap *outlier* dibandingkan mean.

    **Output (dari code Python):**
    ```
    Jumlah Missing Value per Kolom Sebelum Penanganan: 
    harga_rp            0 
    luas_tanah_m2       0 
    luas_bangunan_m2    0 
    kamar_tidur         0 
    kamar_mandi         0 
    garasi_mobil        0 
    dtype: int64 

    Jumlah Missing Value per Kolom Setelah Penanganan (Median Imputation): 
    harga_rp            0 
    luas_tanah_m2       0 
    luas_bangunan_m2    0 
    kamar_tidur         0 
    kamar_mandi         0 
    garasi_mobil        0 
    dtype: int64 
    ```

5.  **Feature Engineering:**
    * **Kode:**
        ```python
        df['harga_juta'] = df['harga_rp'] / 1_000_000
        df = df.drop('harga_rp', axis=1)

        # Kategorisasi luas_tanah_m2 (contoh bins: 0-100, 101-300, >300)
        import pandas as pd # Tambahkan import pandas jika belum ada
        bins = [0, 100, 300, df['luas_tanah_m2'].max()]
        labels = [0, 1, 2] # Menggunakan label numerik untuk kategori
        df['kategori_luas_tanah'] = pd.cut(df['luas_tanah_m2'], bins=bins, labels=labels, include_lowest=True).astype(int)
        ```
    * **Alasan:**
        * `harga_juta`: Mengubah skala target variabel dari Rupiah penuh ke juta Rupiah (`harga_juta = harga_rp / 1_000_000`). Ini membuat nilai target lebih mudah diinterpretasikan dan mungkin membantu beberapa algoritma optimasi.
        * `kategori_luas_tanah`: Membuat fitur kategorikal baru dari `luas_tanah_m2`. Meskipun `luas_tanah_m2` sudah numerik, kadang-kadang mengkategorikan fitur kontinu dapat membantu model menangkap pola non-linier atau perbedaan signifikan antar kelompok. Misalnya, rumah dengan luas tanah "kecil", "sedang", dan "besar" mungkin memiliki dinamika harga yang berbeda.

    **Output (dari code Python):**
    ```
    Data setelah Feature Engineering (5 baris pertama): 
       luas_tanah_m2  luas_bangunan_m2  kamar_tidur  kamar_mandi  garasi_mobil  harga_juta  kategori_luas_tanah 
    0            220               220            3            3             0      3800.0                    2 
    1            180               137            4            3             2      4600.0                    1 
    2            267               250            4            4             4      3000.0                    2 
    3             40                25            2            2             0       430.0                    0 
    4            400               355            6            5             3      9000.0                    2 
    <class 'pandas.core.frame.DataFrame'> 
    RangeIndex: 1010 entries, 0 to 1009 
    Data columns (total 7 columns): 
     #   Column               Non-Null Count  Dtype  
    ---  ------               --------------  -----  
    0   luas_tanah_m2        1010 non-null   int64  
    1   luas_bangunan_m2     1010 non-null   int64  
    2   kamar_tidur          1010 non-null   int64  
    3   kamar_mandi          1010 non-null   int64  
    4   garasi_mobil         1010 non-null   int64  
    5   harga_juta           1010 non-null   float64 
    6   kategori_luas_tanah  1010 non-null   int64  
    dtypes: float64(1), int64(6) 
    memory usage: 55.4 KB 
    ```

6.  **Pembagian Data (Train-Test Split):**
    * **Kode:**
        ```python
        from sklearn.model_selection import train_test_split # Tambahkan import jika belum ada
        X = df.drop('harga_juta', axis=1)
        y = df['harga_juta']
        x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
        ```
    * **Alasan:** Data dibagi menjadi set pelatihan (80%) dan set pengujian (20%). Set pelatihan digunakan untuk melatih model, sedangkan set pengujian digunakan untuk mengevaluasi kinerja model pada data yang belum pernah dilihat, memberikan estimasi yang lebih realistis tentang bagaimana model akan bekerja di dunia nyata. `random_state` memastikan pembagian yang konsisten.

7.  **Penskalaan Fitur (Feature Scaling):**
    * **Kode:**
        ```python
        from sklearn.preprocessing import StandardScaler # Tambahkan import jika belum ada
        scaler = StandardScaler()
        x_train_scaled = scaler.fit_transform(x_train)
        x_test_scaled = scaler.transform(x_test)
        ```
    * **Alasan:** Fitur-fitur seperti `luas_tanah_m2` dan `luas_bangunan_m2` memiliki rentang nilai yang sangat berbeda dibandingkan `kamar_tidur` atau `garasi_mobil`. Penskalaan fitur menggunakan `StandardScaler` (menormalkan data sehingga memiliki rata-rata 0 dan standar deviasi 1) penting untuk beberapa algoritma *machine learning*. Meskipun XGBoost kurang sensitif terhadap skala fitur dibandingkan model berbasis jarak (seperti SVM atau K-NN), penskalaan dapat tetap memberikan stabilitas dan kadang-kadang sedikit peningkatan kinerja.

    **Output (dari code Python):**
    ```
    Fitur yang Digunakan (X): 
    ['luas_tanah_m2', 'luas_bangunan_m2', 'kamar_tidur', 'kamar_mandi', 'garasi_mobil', 'kategori_luas_tanah'] 

    Target (y): 
    harga_juta 

    Data Fitur (X) setelah scaling (5 baris pertama): 
       luas_tanah_m2  luas_bangunan_m2  kamar_tidur  kamar_mandi  garasi_mobil  kategori_luas_tanah 
    0      -0.318038         -0.096919    -1.061272    -0.428305     -1.271837             0.905083 
    1      -0.543039         -0.558367    -0.425139    -0.428305      0.052447            -0.297725 
    2      -0.053661          0.069870    -0.425139     0.276236      1.376731             0.905083 
    3      -1.330545         -1.181044    -1.697406    -1.132847     -1.271837            -1.500532 
    4       0.694470          0.653629     0.847128     0.980778      0.714589             0.905083 

    Ukuran x_train: (808, 6), x_test: (202, 6) 
    Ukuran y_train: (808,), y_test: (202,) 
    ```

## Modeling

Tahapan pemodelan ini menggunakan algoritma **XGBoost (Extreme Gradient Boosting)** untuk tugas regresi. XGBoost dipilih karena dikenal sebagai algoritma yang kuat dan efisien untuk membangun model prediktif yang akurat, terutama dalam kasus data terstruktur.

**Algoritma yang Digunakan: XGBoost Regressor**

XGBoost adalah implementasi dari algoritma *gradient boosting machine* yang dioptimalkan. Algoritma ini membangun serangkaian pohon keputusan secara sekuensial. Setiap pohon baru mencoba mengoreksi kesalahan yang dibuat oleh pohon sebelumnya.

* **Kelebihan XGBoost:**
    * **Performa Tinggi:** Sering memenangkan kompetisi *machine learning* berkat akurasinya yang tinggi.
    * **Fleksibilitas:** Dapat menangani berbagai jenis data dan masalah (klasifikasi, regresi, peringkat).
    * **Regularisasi:** Memiliki mekanisme regularisasi (`L1` dan `L2`) untuk mencegah *overfitting*.
    * **Penanganan *Missing Values*:** Dapat secara otomatis menangani nilai yang hilang.
    * **Paralelisasi:** Dapat berjalan secara paralel, mempercepat proses pelatihan.

* **Kekurangan XGBoost:**
    * **Interpretasi:** Model akhir, yang merupakan ensemble dari banyak pohon, bisa sulit diinterpretasikan dibandingkan model tunggal.
    * **Memori:** Membutuhkan memori yang cukup besar saat memproses dataset yang sangat besar.
    * **Hyperparameter Tuning:** Memiliki banyak *hyperparameter* yang perlu disetel, yang bisa memakan waktu.

**Proses Improvement Model: Hyperparameter Tuning dengan GridSearchCV**

Untuk mendapatkan model XGBoost terbaik, kami melakukan penyetelan *hyperparameter* (hyperparameter tuning) menggunakan `GridSearchCV`.

* **Tujuan:** Mencari kombinasi optimal dari *hyperparameter* yang memberikan kinerja terbaik pada data validasi.
* **Metode:** `GridSearchCV` melakukan pencarian secara menyeluruh di seluruh *grid* nilai *hyperparameter* yang ditentukan. Untuk setiap kombinasi, model dilatih menggunakan *cross-validation* (3 *folds*) dan diuji pada metrik evaluasi yang ditentukan (dalam hal ini, `neg_mean_squared_error`).

* **Parameter yang Disesuaikan:**
    * `n_estimators`: Jumlah pohon keputusan yang akan dibangun (antara 50 hingga 200).
    * `learning_rate`: Ukuran langkah *boosting* (seberapa besar setiap pohon baru berkontribusi).
    * `max_depth`: Kedalaman maksimum setiap pohon.
    * `subsample`: Fraksi sampel yang digunakan untuk melatih setiap pohon (untuk mengurangi *overfitting*).
    * `colsample_bytree`: Fraksi fitur yang digunakan untuk melatih setiap setiap pohon (untuk mengurangi *overfitting*).

**Output (dari code Python):**

Memulai Penyetelan Hiperparameter XGBoost dengan GridSearchCV...
Fitting 3 folds for each of 243 candidates, totalling 729 fits
[0] validation_0-rmse:7025.23249
... (banyak baris output yang menunjukkan proses training model) ...
[99] validation_0-rmse:3188.37273

Parameter Terbaik Ditemukan: {'colsample_bytree': 0.7, 'learning_rate': 0.05, 'max_depth': 7, 'n_estimators': 100, 'subsample': 1.0}
Skor CV (Negative MSE) Terbaik: -15902225.5805

**Penjelasan Output:**
* **`Memulai Penyetelan Hiperparameter XGBoost dengan GridSearchCV...`**: Menunjukkan dimulainya proses pencarian *hyperparameter* optimal.
* **`Fitting 3 folds for each of 243 candidates, totalling 729 fits`**: GridSearchCV mencoba 243 kombinasi *hyperparameter* dan melakukan 3 *cross-validation* untuk setiap kombinasi, menghasilkan total 729 proses pelatihan model.
* **`[0] validation_0-rmse:...` hingga `[99] validation_0-rmse:...`**: Ini adalah log pelatihan dari XGBoost. `validation_0-rmse` menunjukkan Root Mean Squared Error pada set validasi setelah setiap pohon keputusan ditambahkan. Nilai ini menurun seiring berjalannya pelatihan, menunjukkan bahwa model sedang belajar dan meningkatkan kinerjanya.
* **`Parameter Terbaik Ditemukan: {...}`**: Setelah semua kombinasi diuji, ini adalah set *hyperparameter* yang memberikan kinerja terbaik (MSE terendah) pada validasi silang. Parameter-parameter ini akan digunakan untuk model akhir.
* **`Skor CV (Negative MSE) Terbaik: -15902225.5805`**: Ini adalah skor validasi silang terbaik yang dicapai dengan *hyperparameter* optimal.

## Evaluation

Pada bagian ini, metrik evaluasi yang digunakan untuk mengukur kinerja model XGBoost adalah `R2 Score`, `Mean Absolute Error (MAE)`, `Mean Squared Error (MSE)`, dan `Root Mean Squared Error (RMSE)`. Metrik-metrik ini sangat cocok untuk masalah regresi, di mana tujuannya adalah memprediksi nilai kontinu.

**Metrik Evaluasi yang Digunakan:**

1.  **R2 Score (Coefficient of Determination):**
    * **Penjelasan:** R2 Score mengukur proporsi varians dalam variabel dependen yang dapat diprediksi dari variabel independen. Nilainya berkisar antara 0 hingga 1.
    * **Formula:** $R^2 = 1 - \frac{\sum_{i=1}^{n}(y_i - \hat{y}_i)^2}{\sum_{i=1}^{n}(y_i - \bar{y})^2}$
        * $y_i$: nilai aktual
        * $\hat{y}_i$: nilai prediksi
        * $\bar{y}$: rata-rata nilai aktual
    * **Bagaimana Bekerja:** Semakin dekat nilai R2 ke 1, semakin baik model dalam menjelaskan variabilitas data. R2 = 1 berarti model memprediksi dengan sempurna. R2 = 0 berarti model tidak lebih baik daripada memprediksi rata-rata target.

2.  **Mean Absolute Error (MAE):**
    * **Penjelasan:** MAE mengukur rata-rata dari selisih absolut antara nilai aktual dan nilai prediksi. Metrik ini memberikan gambaran tentang seberapa besar rata-rata "kesalahan" model dalam satuan target (juta Rupiah).
    * **Formula:** $MAE = \frac{1}{n} \sum_{i=1}^{n}|y_i - \hat{y}_i|$
    * **Bagaimana Bekerja:** Nilai MAE yang lebih rendah menunjukkan model yang lebih akurat. Karena menggunakan nilai absolut, MAE tidak memberikan informasi tentang arah kesalahan (apakah prediksi terlalu tinggi atau terlalu rendah).

3.  **Mean Squared Error (MSE):**
    * **Penjelasan:** MSE mengukur rata-rata dari kuadrat selisih antara nilai aktual dan nilai prediksi. Karena mengkuadratkan kesalahan, MSE memberikan bobot yang lebih besar pada kesalahan yang lebih besar (outlier).
    * **Formula:** $MSE = \frac{1}{n} \sum_{i=1}^{n}(y_i - \hat{y}_i)^2$
    * **Bagaimana Bekerja:** Nilai MSE yang lebih rendah menunjukkan model yang lebih akurat. Unit MSE adalah kuadrat dari unit target, sehingga sulit diinterpretasikan secara langsung.

4.  **Root Mean Squared Error (RMSE):**
    * **Penjelasan:** RMSE adalah akar kuadrat dari MSE. Ini adalah metrik yang paling sering digunakan dalam regresi karena memberikan ukuran kesalahan dalam satuan yang sama dengan variabel target (juta Rupiah), sehingga lebih mudah diinterpretasikan daripada MSE.
    * **Formula:** $RMSE = \sqrt{\frac{1}{n} \sum_{i=1}^{n}(y_i - \hat{y}_i)^2}$
    * **Bagaimana Bekerja:** Nilai RMSE yang lebih rendah menunjukkan model yang lebih akurat. Seperti MAE, RMSE juga lebih sensitif terhadap *outlier* dibandingkan MAE karena adanya operasi kuadrat.

**Hasil Proyek Berdasarkan Metrik Evaluasi:**

Berikut adalah perbandingan metrik evaluasi model pada data *training* dan *testing*:

| Metrik      | Training Set      | Testing Set       |
| :---------- | :---------------- | :---------------- |
| **R2 Score** | 0.9492            | 0.7612            |
| **MAE** | 858.7464 (juta Rp) | 1921.5205 (juta Rp) |
| **MSE** | 2825151.1453      | 11148025.6712     |
| **RMSE** | 1680.8186 (juta Rp) | 3338.8659 (juta Rp) |

**Penjelasan Hasil Evaluasi:**
* **`Evaluasi pada Training Set:`**: Model sangat baik dalam menjelaskan variabilitas harga pada data pelatihan (hampir 95%). Rata-rata kesalahan absolut (MAE) sekitar 858 juta Rupiah, dan RMSE sekitar 1.68 miliar Rupiah.
* **`Evaluasi pada Testing Set:`**: Kinerja model pada data pengujian menurun menjadi 76.12% untuk R2 Score, menunjukkan sedikit *overfitting* tetapi masih menunjukkan kemampuan prediksi yang baik pada data baru. MAE meningkat menjadi sekitar 1.92 miliar Rupiah, dan RMSE menjadi sekitar 3.34 miliar Rupiah.

**Visualisasi Evaluasi Model Tambahan:**

![image](https://github.com/user-attachments/assets/e49b0b46-c9fd-4500-86f4-5743dee2886c)
**Penjelasan Gambar:**
* **Plot Scatter Harga Aktual vs. Prediksi (atas):** Menunjukkan bagaimana prediksi model (*y*-axis) dibandingkan dengan harga aktual (*x*-axis) pada data pengujian. Garis putus-putus hitam adalah "garis ideal" ($y=x$). Titik-titik yang dekat dengan garis menunjukkan prediksi yang akurat. Terlihat sebagian besar titik mengumpul di sekitar garis, namun ada penyimpangan lebih besar pada harga yang lebih tinggi.
* **Distribusi Residual (bawah):** Histogram dari residual (harga aktual - prediksi). Idealnya, ini harus terdistribusi normal di sekitar nol. Rata-rata residual adalah -117.15 juta Rp (sangat dekat dengan nol), menunjukkan bias yang sangat kecil. Namun, adanya "ekor" di sisi kanan menunjukkan ada beberapa kasus *underprediction* yang signifikan.

### Pentingnya Fitur (Feature Importance)

Berikut adalah nilai *feature importance* dari model XGBoost terbaik:

| Fitur                 | Skor Kepentingan |
| :-------------------- | :--------------- |
| `luas_bangunan_m2`    | 0.372223         |
| `luas_tanah_m2`       | 0.286692         |
| `kamar_mandi`         | 0.125585         |
| `garasi_mobil`        | 0.082717         |
| `kamar_tidur`         | 0.070624         |
| `kategori_luas_tanah` | 0.062159         |

![image](https://github.com/user-attachments/assets/2ecc428c-f39f-4aa1-9c43-928d49456162)


**Penjelasan Gambar:**
* **Bar Plot Horizontal:** Menampilkan skor kepentingan setiap fitur, diurutkan dari yang paling penting.
* `luas_bangunan_m2` (0.372223) adalah fitur paling penting, menegaskan bahwa luas bangunan adalah faktor utama yang memengaruhi harga rumah.
* `luas_tanah_m2` (0.286692) adalah fitur kedua paling penting.
* Fitur-fitur lain seperti `kamar_mandi`, `garasi_mobil`, `kamar_tidur`, dan `kategori_luas_tanah` juga berkontribusi pada model, meskipun dengan bobot yang lebih kecil.

### Contoh Hasil Prediksi pada Data Test

Berikut adalah contoh beberapa hasil prediksi model pada data pengujian, beserta selisih dan persentase error:

| harga_aktual_juta | harga_prediksi_juta | selisih_juta | persentase_error |
| :---------------- | :------------------ | :----------- | :--------------- |
| 8900.0            | 6574.72             | 2325.28      | 26.13%           |
| 6500.0            | 3540.59             | 2959.41      | 45.53%           |
| 6500.0            | 11873.12            | -5373.12     | 82.66%           |
| 37000.0           | 24670.88            | 12329.12     | 33.32%           |
| 18500.0           | 19731.34            | -1231.34     | 6.66%            |
| 8500.0            | 8324.94             | 175.06       | 2.06%            |
| 2150.0            | 2769.99             | -619.99      | 28.84%           |
| 2250.0            | 3839.22             | -1589.22     | 70.63%           |
| 4000.0            | 3497.74             | 502.26       | 12.56%           |
| 3799.0            | 4121.69             | -322.69      | 8.49%            |

**Statistik Deskriptif Persentase Error:**

| Statistik | persentase_error |
| :-------- | :--------------- |
| **count** | 202.00           |
| **mean** | 26.80%           |
| **std** | 27.03%           |
| **min** | 0.02%            |
| **25%** | 7.98%            |
| **50%** | 18.63%           |
| **75%** | 37.12%           |
| **max** | 180.53%          |

**Penjelasan Output Contoh Prediksi:**
* **`Contoh Hasil Prediksi pada Data Test`**: Menampilkan beberapa baris dari data pengujian dengan harga aktual, prediksi, selisih, dan persentase error. Ini memberikan gambaran langsung tentang seberapa akurat prediksi model untuk sampel individu. Terlihat variasi persentase error yang cukup besar.
* **`Statistik Deskriptif Persentase Error`**:
    * `mean`: Rata-rata persentase error adalah sekitar 26.80%, menunjukkan bahwa rata-rata prediksi model menyimpang sekitar 26.8% dari harga aktual.
    * `std`: Standar deviasi yang tinggi (27.03%) mengindikasikan variasi yang besar dalam akurasi prediksi.
    * `min` dan `max`: Rentang error dari 0.01% hingga 180.52% menunjukkan bahwa ada beberapa prediksi yang sangat akurat dan beberapa yang sangat jauh dari aktual.

**Kesimpulan Evaluasi:**
Model XGBoost yang telah disetel *hyperparameter*-nya menunjukkan kinerja yang solid dalam memprediksi harga properti, dengan `R2 Score` sebesar 0.76 pada data pengujian. Meskipun ada indikasi *overfitting* ringan (penurunan kinerja dari training ke testing set) dan tantangan dalam memprediksi harga ekstrem (yang terlihat dari *outlier* pada distribusi residual dan persentase error maksimum yang tinggi), model ini menyediakan estimasi harga yang cukup akurat dan memberikan wawasan penting tentang fitur-fitur yang paling berpengaruh. MAE sebesar 1.92 miliar Rupiah dan RMSE sebesar 3.34 miliar Rupiah menunjukkan rata-rata kesalahan prediksi dalam skala harga aktual.

## Referensi

* Chen, T., & Guestrin, C. (2016). XGBoost: A Scalable Tree Boosting System. *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*, 785–794. doi:10.1145/2939672.2939785
* Géron, A. (2019). *Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow: Concepts, Tools, and Techniques to Build Intelligent Systems* (2nd ed.). O'Reilly Media.
* Scikit-learn developers. (n.d.). *scikit-learn: Machine Learning in Python*. Retrieved from https://scikit-learn.org/stable/
* Seaborn developers. (n.d.). *seaborn: statistical data visualization*. Retrieved from https://seaborn.pydata.org/
* Python Software Foundation. (n.d.). *Python Language Reference*. Retrieved from https://docs.python.org/3/reference/

