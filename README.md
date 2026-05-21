# SolarCast: Prediksi Output Panel Surya Berbasis LSTM

## 1. Deskripsi Proyek
SolarCast adalah proyek implementasi Deep Learning sekuensial yang bertujuan untuk memprediksi output daya listrik (DC Power) dari pembangkit listrik tenaga surya. Proyek ini membandingkan pendekatan pemodelan Linear Regression, Multilayer Perceptron (MLP), dan Long Short-Term Memory (LSTM) untuk membuktikan bahwa retensi memori temporal sangat krusial dalam meramal fluktuasi energi surya secara presisi guna mendukung stabilitas smart grid.

## 2. Informasi Dataset
Proyek ini menggunakan dataset publik "Solar Power Generation Data" (Plant 1) dari Kaggle.
* Resolusi Data: Data berinterval 15 menit yang diubah (resampling) menjadi skala per jam (hourly).
* Fitur Input (X): Ambient Temperature (Suhu udara luar), Module Temperature (Suhu permukaan panel), dan Irradiation (Tingkat radiasi matahari).
* Variabel Target (Y): DC_POWER (Total daya listrik arus searah yang dihasilkan dalam satuan Watt).

## 3. Arsitektur Model
Model LSTM yang diusulkan dirancang agar efisien dalam menangkap pola temporal cuaca tanpa mengalami overfitting pada data skala menengah:
* Input Layer: Menerima urutan data sekuensial dengan lookback window 24 jam ke belakang.
* LSTM Layer: 1 lapisan yang terdiri dari 32 unit untuk mengekstraksi memori siklus waktu harian.
* Regularization: Dropout sebesar 0.2 (20%) untuk menjaga model tetap tangguh saat menghadapi data cuaca baru.
* Output Layer: Dense layer (1 unit neuron) tanpa fungsi aktivasi untuk menghasilkan tebakan regresi kontinu.
* Konfigurasi Pelatihan: Optimizer Adam dan Loss Function Mean Squared Error (MSE).

## 4. Panduan Instalasi dan Penggunaan
Proyek ini dieksekusi di lingkungan Python (direkomendasikan Google Colab) menggunakan library pandas, numpy, tensorflow, dan scikit-learn.

Tahap krusial dalam penyiapan datanya adalah pembentukan sekuens menggunakan sliding window. Penting untuk diperhatikan bahwa parameter pengacakan (shuffle) harus dimatikan (shuffle=False) agar urutan kronologis waktu tetap utuh:

def create_sequences(X, y, time_steps):
    X_seq, y_seq = [], []
    for i in range(len(X) - time_steps):
        X_seq.append(X[i:(i + time_steps)])
        y_seq.append(y[i + time_steps])
    return np.array(X_seq), np.array(y_seq)

## 5. Hasil Evaluasi
Evaluasi model membandingkan tingkat error dan akurasi pada data pengujian. Berikut adalah hasil performa akhir:

| Model | MAE (Watt) | RMSE (Watt) | R2 Score |
| :--- | :--- | :--- | :--- |
| Linear Regression (Baseline) | 17865.02 | 24249.44 | 0.9067 |
| Multilayer Perceptron (MLP) | 17602.51 | 24727.26 | 0.9030 |
| LSTM (Proposed Model) | 15326.93 | 22806.55 | 0.9174 |

Analisis: Model LSTM mencapai tingkat kesalahan terendah dan akurasi tertinggi dengan nilai R2 sebesar 0.9174. Kemampuan gerbang memori (gates) pada LSTM untuk menyimpan riwayat fluktuasi cuaca dari siklus 24 jam sebelumnya memungkinkannya mengungguli metode regresi konvensional yang memproses data per jam secara statis dan independen.

## 6. Rencana Pengembangan
* Integrasi IoT: Menggabungkan model dengan API cuaca atau sensor fisik secara real-time untuk menghasilkan prediksi yang dinamis setiap jamnya.
* Optimasi Hiperparameter: Menguji lookback window yang lebih panjang (misalnya 48 atau 72 jam) dan mengeksplorasi arsitektur jaringan Bidirectional LSTM.
