# Tugas Akhir - Clustering K-Means pada Data Pelanggan Grosir

Repositori ini berisi analisis dan implementasi model clustering menggunakan algoritma **K-Means** untuk mengelompokkan pelanggan grosir berdasarkan pola pembelian mereka, menggunakan dataset "Wholesale customers data".

## 🔍 Tujuan
Membantu perusahaan mengidentifikasi segmen pelanggan untuk menyusun strategi pemasaran yang lebih tepat sasaran.

## 📁 Struktur Folder
- `data/` – Dataset asli.
- `src/` – Skrip preprocessing dan clustering (opsional).
- `visualizations/` – Gambar visualisasi (boxplot sebelum dan sesudah winsorization).
- `Wholesale_KMeans_Clustering.ipynb` – Notebook utama.
- `README.md` – Penjelasan tugas akhir.
- `requirements.txt` – Daftar library yang digunakan.
- `LICENSE` – Lisensi penggunaan.

## 💻 Teknologi
- Python
- Pandas
- Matplotlib
- Scikit-learn

## 📜 Lisensi
MIT License



# Penjelasan Kode Program - Clustering K-Means pada Dataset Wholesale Customers

Disini kita akan membangun model clustering menggunakan metode **K-Means** pada dataset *Wholesale customers data*. Masalah yang ditemui adalah bagaimana cara mengelompokkan pelanggan grosir berdasarkan pola pembelian mereka agar perusahaan dapat menyusun strategi pemasaran yang lebih tepat sasaran? Berikut merupakan langkah-langkahnya:

## Load Data
Pertama, kita memuat dataset dari file CSV. Proses ini dilakukan menggunakan modul `csv` di Python. Kami hanya mengambil kolom-kolom numerik, yaitu dari kolom ke-3 sampai akhir, karena kolom `Channel` dan `Region` tidak akan digunakan dalam proses clustering.

```python
def load_data(filename):
    ...
```
Setelah data dimuat, akan ditampilkan jumlah baris dan jumlah fitur per baris. Diketahui terdapat **440 baris** data dengan **6 fitur** numerik yaitu: Fresh, Milk, Grocery, Frozen, Detergents_Paper, dan Delicassen.  


## Statistik Deskriptif
Langkah selanjutnya adalah melakukan **statistika deskriptif** terhadap setiap fitur. Kami menghitung nilai minimum, maksimum, rata-rata (mean), dan standar deviasi dari masing-masing fitur untuk memahami penyebaran nilai-nilai dalam data.

```python
def describe_statistics(data, labels):
    ...
```
Ini penting untuk mengetahui seberapa besar variasi antar fitur dan melihat adanya kemungkinan outlier. Misalnya, fitur `Fresh` memiliki nilai maksimum lebih dari 100 ribu, yang jauh lebih tinggi dari rata-ratanya.  


## Cek Missing Values
Kemudian dilakukan pengecekan terhadap **missing value**. Kami menggunakan `pandas` untuk memuat data dan menjalankan fungsi `.isnull().sum()` guna memastikan bahwa tidak ada nilai kosong dalam dataset.

```python
df = pd.read_csv(...)
df.isnull().sum()
```
Hasilnya menunjukkan **tidak ada missing value** dalam dataset ini, sehingga tidak perlu dilakukan imputasi atau penghapusan data.  


## Deteksi Outlier dengan Z-Score
Selanjutnya, kami melakukan **deteksi outlier** menggunakan metode **Z-Score**. Nilai dianggap outlier jika berada lebih dari 3 standar deviasi dari rata-rata.

```python
def detect_outliers_zscore(data, threshold=3):
    ...
```
Dari hasil deteksi, terdapat **43 outlier** yang tersebar di berbagai fitur. Hal ini menandakan perlunya penanganan lebih lanjut agar model clustering tidak terdistraksi oleh data ekstrem.  


## Visualisasi Outlier - Boxplot
Untuk **visualisasi outlier**, digunakan plot **boxplot** untuk masing-masing fitur. Ini membantu mengidentifikasi nilai-nilai yang menyimpang secara visual.

```python
plt.boxplot(...)
```
Terlihat dengan jelas bahwa beberapa fitur memiliki titik-titik di luar whisker boxplot yang mengindikasikan outlier.  


## Winsorization
Karena jumlah data relatif kecil (440 baris), maka kami **tidak membuang outlier**, melainkan menggunakan metode **winsorization**. Winsorization mengubah nilai ekstrem menjadi nilai percentile ke-5 dan ke-95.

```python
def winsorize(data, lower_percent=5, upper_percent=95):
    ...
```
Dengan cara ini, data tetap dipertahankan dan efek dari outlier dapat diminimalkan.  


## Visualisasi Setelah Winsorization
Boxplot ditampilkan kembali setelah proses winsorization untuk melihat bagaimana distribusi data berubah.

```python
plt.boxplot(..., data=winsorized_data)
```
Distribusi menjadi lebih stabil dan outlier tidak lagi mendominasi secara visual.  


## Normalisasi Min-Max
Langkah selanjutnya adalah **normalisasi skala data** menggunakan metode **min-max scaler**. Ini dilakukan agar semua fitur memiliki rentang nilai yang sama (0-1) sehingga tidak ada fitur yang mendominasi proses clustering.

```python
def min_max_scale(data):
    ...
```
Langkah ini sangat penting dalam K-Means karena algoritma ini sensitif terhadap skala data.  


## Implementasi K-Means Clustering
Setelah preprocessing selesai, kita melanjutkan ke tahap utama yaitu penerapan algoritma **K-Means**. Di sini kita menggunakan pustaka `sklearn.cluster.KMeans`.

```python
from sklearn.cluster import KMeans

kmeans = KMeans(n_clusters=3, random_state=42)
kmeans.fit(normalized_data)
```

Kita memilih `n_clusters=3` sebagai jumlah klaster awal. Proses fitting dilakukan terhadap data yang telah dinormalisasi.  


---

## Menampilkan Label dan Pusat Klaster
Setelah model dilatih, kita dapat menampilkan **label** hasil clustering dan **pusat klaster**.

```python
print(kmeans.labels_)
print(kmeans.cluster_centers_)
```

Label digunakan untuk mengelompokkan masing-masing data ke dalam klaster tertentu, sedangkan pusat klaster menunjukkan koordinat dari masing-masing klaster di ruang fitur.  


---

## Visualisasi Hasil Clustering
Untuk memvisualisasikan hasil clustering, digunakan PCA (Principal Component Analysis) untuk mereduksi dimensi dari 6D ke 2D.

```python
from sklearn.decomposition import PCA
...
```

Visualisasi ini memudahkan dalam melihat pola klaster meskipun ada informasi yang hilang karena reduksi dimensi. Warna berbeda menunjukkan anggota dari klaster yang berbeda.  


---

## Menentukan Jumlah Klaster Optimal (Metode Elbow)
Untuk menentukan jumlah klaster yang paling optimal, digunakan **metode Elbow** yang memplot jumlah klaster terhadap inertia (total jarak dari data ke pusat klaster masing-masing).

```python
inertia = []
for k in range(1, 11):
    km = KMeans(n_clusters=k, random_state=42)
    km.fit(normalized_data)
    inertia.append(km.inertia_)
```

Grafik Elbow akan menunjukkan titik tekukan yang menandakan jumlah klaster optimal (biasanya nilai `k` sebelum perubahan inertia menjadi datar).  


---

## Interpretasi Klaster
Setelah jumlah klaster dipilih, dilakukan **analisis rata-rata** tiap fitur per klaster untuk interpretasi.

```python
import numpy as np
clusters = kmeans.labels_
data_array = np.array(data)
for i in range(3):
    print(f"Klaster {i}:")
    print(np.mean(data_array[clusters == i], axis=0))
```

Hasil ini membantu memahami karakteristik tiap klaster, misalnya klaster 0 mungkin adalah pelanggan dengan pembelian tinggi pada Grocery dan Detergents_Paper.  


# Tambahkan bagian penjelasan DBSCAN ke README

dbscan_section = """
---

## Clustering Alternatif: DBSCAN
Selain K-Means, kita juga mencoba algoritma **DBSCAN (Density-Based Spatial Clustering of Applications with Noise)** dari pustaka `sklearn.cluster`. DBSCAN tidak memerlukan penentuan jumlah klaster sejak awal, dan cocok untuk data dengan bentuk klaster yang tidak reguler serta dapat mengenali noise (outlier).

```python
from sklearn.cluster import DBSCAN

dbscan = DBSCAN(eps=0.5, min_samples=5)
dbscan.fit(normalized_data)
```
