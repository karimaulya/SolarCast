## Penjelasan Model LSTM (Long Short-Term Memory) dalam SolarCast

Proyek SolarCast menggunakan algoritma Long Short-Term Memory (LSTM), sebuah varian tingkat lanjut dari Recurrent Neural Network (RNN) yang dirancang khusus untuk memodelkan data berbasis deret waktu (time-series).

### Mengapa Menggunakan LSTM?
Output daya dari panel surya sangat bergantung pada pola cuaca yang bersifat sekuensial dan memiliki siklus harian (misalnya, pergerakan suhu dan radiasi dari pagi hingga malam). Model konvensional seperti Linear Regression atau Multilayer Perceptron (MLP) memproses data secara statis; mereka hanya melihat kondisi cuaca pada satu jam tertentu tanpa memahami urutan kejadian sebelumnya.

LSTM memecahkan masalah tersebut melalui mekanisme *memory cell* dan *gates* (gerbang informasi). Algoritma ini mampu "mengingat" tren cuaca historis. Dalam proyek ini, LSTM dikonfigurasi untuk melihat rekam jejak data selama 24 jam ke belakang (*lookback window*) guna memprediksi output daya pada satu jam ke depan dengan tingkat presisi yang tinggi.

### Arsitektur Model
Model LSTM dalam SolarCast dirancang agar ringan, efisien, dan kebal terhadap *overfitting* saat memproses data cuaca lokal:

1. **Input Layer (Sliding Window):** Menerima struktur data 3 dimensi dengan format `(jumlah_sampel, time_steps, fitur)`. Model ini menggunakan `time_steps = 24` dan `fitur = 3` (Suhu Udara, Suhu Modul, dan Iradiasi).
   
2. **LSTM Layer (32 Unit):** Lapisan inti yang bertugas mengekstraksi pola temporal. Penggunaan 32 unit terbukti optimal dalam eksperimen ini untuk menyeimbangkan kecepatan komputasi dan kemampuan menangkap fluktuasi cuaca yang kompleks.

3. **Dropout Layer (20%):** Berfungsi sebagai teknik regularisasi. Lapisan ini menonaktifkan 20% neuron secara acak pada setiap iterasi pelatihan untuk mencegah model menghafal data latih secara buta, sehingga model lebih tangguh saat dihadapkan pada data cuaca baru yang belum pernah dilihat sebelumnya.

4. **Dense Layer (Output):** Lapisan akhir yang terdiri dari 1 unit neuron tanpa fungsi aktivasi pembatas. Lapisan ini mengagregasi seluruh informasi temporal dari lapisan sebelumnya menjadi satu nilai prediksi numerik kontinu, yaitu prediksi total daya listrik (DC Power) dalam satuan Watt.
