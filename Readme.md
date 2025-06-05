
# Natural Scene Image Classification 🌍🖼️


## 📌 Project Overview (Ulasan Proyek)

Dalam era digital saat ini, kebutuhan untuk mengidentifikasi dan mengklasifikasikan gambar secara otomatis menjadi semakin penting, terutama dalam bidang seperti keamanan, pertanian, kesehatan, dan e-commerce. Proyek ini bertujuan untuk mengembangkan model klasifikasi gambar berbasis deep learning yang mampu mengenali beberapa kelas objek dengan akurasi tinggi.

Dengan memanfaatkan pendekatan transfer learning menggunakan arsitektur MobileNetV2, model dapat belajar dari fitur-fitur umum dalam gambar tanpa harus dilatih dari nol, yang sangat efisien dalam hal waktu dan sumber daya.

Referensi:

* Howard et al. (2017), *MobileNetV2: Inverted Residuals and Linear Bottlenecks*, [arXiv:1801.04381](https://arxiv.org/abs/1801.04381)
* TensorFlow Image Classification Guide: [https://www.tensorflow.org/tutorials/images/classification](https://www.tensorflow.org/tutorials/images/classification)

---

## 🧠 Business Understanding

### Problem Statement

Bagaimana cara mengklasifikasikan gambar ke dalam beberapa kelas yang telah ditentukan secara otomatis dan efisien?

### Goals

* Membangun model klasifikasi gambar multi-kelas berbasis deep learning dengan akurasi tinggi.
* Meningkatkan akurasi model dengan teknik augmentasi data dan transfer learning.
* Mengonversi model ke format TFLite dan TensorFlow\.js untuk keperluan deployment.

### Solution Approach

Untuk menyelesaikan permasalahan klasifikasi, dua pendekatan utama yang dapat digunakan:

1. **Content-Based Filtering (CBF)**
   Menggunakan fitur visual (misalnya piksel atau fitur CNN) dari gambar untuk mengklasifikasikan ke dalam kelas tertentu.
2. **Collaborative Filtering (CF)**
   Biasanya digunakan untuk sistem rekomendasi. Tidak relevan langsung untuk klasifikasi citra, tapi bisa digunakan jika ada interaksi user-image (misal preferensi user terhadap gambar). **(Opsional, tidak digunakan dalam model ini)**

Pada proyek ini, pendekatan utama adalah content-based menggunakan deep learning (MobileNetV2 + CNN).

---

## 📊 Data Understanding

### 📝 Dataset Description

Dataset berisi **gambar pemandangan alam dari seluruh dunia**, berukuran **150x150 piksel**, dengan total sekitar **25.000 gambar** yang terbagi dalam 6 kategori:

| Label ID | Class Name | Description |
|----------|------------|-------------|
| 0 | **Buildings** | Bangunan |
| 1 | **Forest** | Hutan |
| 2 | **Glacier** | Gletser |
| 3 | **Mountain** | Gunung |
| 4 | **Sea** | Laut |
| 5 | **Street** | Jalan |

Dataset dibagi menjadi:

- **Train (seg_train)** : ~14.000 gambar (training) -> Digunakan untuk melatih model.
- **Validation (seg_val)** : ~3.000 gambar (testing/validation) -> Digunakan untuk memantau performa selama pelatihan.
- **Prediction (seg_test)** : ~3.000 gambar (inference/prediction) -> Digunakan untuk mengevaluasi model akhir.

Dataset terdiri dari tiga subset:

### Informasi Dataset

* Format data: Gambar RGB dengan berbagai ukuran.
* Jumlah kelas: Terdeteksi secara otomatis dari folder (`os.listdir`).
* Ukuran gambar distandarkan ke 256x256 piksel.

**Contoh visualisasi gambar:**

* Ditampilkan 1 gambar per kelas di awal proses untuk memverifikasi format dan representasi gambar.

### Link Dataset


📊 **Sumber dataset**: [Intel Image Classification Challenge — Analytics Vidhya](https://datahack.analyticsvidhya.com)  
📸 **Kredit foto**: Jan Böttinger di Unsplash

---

## 🧹 Data Preparation

### Langkah-langkah yang dilakukan:

1. **Rescaling**: `layers.Rescaling(1./255)` untuk mengubah nilai piksel ke skala \[0, 1].
2. **Augmentasi Data**: `layers.RandomFlip`, `RandomRotation`, dan `RandomZoom` diterapkan hanya pada dataset pelatihan.
3. **Resize gambar**: Semua gambar diubah ukurannya menjadi (256, 256) agar konsisten dengan input model.
4. **Label Encoding**: Label dikonversi ke one-hot encoding (`label_mode='categorical'`).

### Alasan:

* Augmentasi membantu meningkatkan generalisasi model.
* Rescaling dan resize diperlukan agar model pretrained (MobileNetV2) dapat memproses input dengan benar.
* One-hot encoding diperlukan untuk fungsi loss `categorical_crossentropy`.

---

## 🏗️ Modeling and Result

### Arsitektur Model

1. **Base Model**: MobileNetV2 (`include_top=False`, pretrained on ImageNet)
2. **Head Model**:

   * Conv2D → BatchNorm → ReLU → MaxPooling
   * Flatten → Dense → Dropout → Softmax

### Alur Training:

* Optimizer: Adam dengan `learning_rate=0.0001`
* Loss Function: `categorical_crossentropy`
* Callbacks:

  * EarlyStopping (patience=10)
  * ReduceLROnPlateau
  * ModelCheckpoint (save\_best\_only)

### Visualisasi Training:

* Grafik akurasi dan loss untuk training dan validation ditampilkan per epoch.

### Hasil:

* Akurasi pada data uji: **`~X.XXXX`** (ganti sesuai hasil `model.evaluate(test_ds)`)
* Model disimpan dalam format `.keras`, `.tflite`, dan `TensorFlow.js`.

---

## ✅ Evaluation

### Metrik yang Digunakan:

* **Accuracy**: Digunakan karena klasifikasi multi-kelas dengan label seimbang.
* **Visualisasi Prediksi**: 10 gambar pertama ditampilkan dengan label asli dan prediksi (warna hijau = benar, merah = salah).

### Rumus Accuracy:

$$
Accuracy = \frac{Jumlah\ prediksi\ benar}{Jumlah\ total\ data}
$$

### Hasil Evaluasi:

* Akurasi pada test set menunjukkan performa model cukup baik.
* Beberapa prediksi salah terjadi pada kelas yang memiliki kemiripan visual.

---

## 📦 Bonus: Deployment Format

Model dikonversi ke dua format:

* `.tflite` untuk deployment mobile
* `.tfjs` untuk deployment web

Label disimpan dalam file `label.txt` untuk referensi klasifikasi di aplikasi client.



## 📂 Project Structure

```
├── data
│   ├── data       # Sumber data awal (~20k images)
│   ├── train      # Data latih (~14k images)
│   ├── val        # Data validasi (~3k images)
│   └── test       # Data prediksi (~3k images)
├── tfjs_model         # Model untuk deployment di browser (TensorFlow.js)
│   ├── group1-shard1of1.bin
│   └── model.json
├── tflite             # Model untuk deployment di perangkat mobile (TensorFlow Lite)
│   ├── model.tflite
│   └── label.txt
├── saved_model        # Model dalam format SavedModel TensorFlow
│   ├── saved_model.pb
│   └── variables/
├── main.ipynb     # Notebook pelatihan dan evaluasi model
├── README.md          # Deskripsi project (file ini)
└── requirements.txt   # Daftar dependensi Python
```
