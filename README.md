### Studi Kasus: Implementasi Bagging Menggunakan Association Rules di R dengan Apriori

#### Latar Belakang
Sebuah supermarket ingin meningkatkan strategi pemasaran mereka dengan menganalisis pola pembelian pelanggan. Untuk meningkatkan akurasi analisis, kita menggunakan metode *bagging* (Bootstrap Aggregating) untuk memperkuat *association rules* yang dihasilkan oleh algoritma Apriori.

**Tujuan**:
1. Menganalisis pola pembelian menggunakan Apriori.
2. Meningkatkan stabilitas dan akurasi model dengan *bagging*.

---

#### Dataset
Kita menggunakan dataset transaksi sederhana yang merepresentasikan pembelian beberapa barang:

```plaintext
Transaksi  Barang
1          [Roti, Susu, Keju]
2          [Roti, Susu]
3          [Keju, Telur]
4          [Roti, Telur, Susu]
5          [Telur, Susu, Keju]
```

---

#### Langkah Implementasi

1. **Persiapan Dataset dan Instalasi Library**

```R
# Instalasi library
install.packages("arules")
install.packages("caret")

# Memuat library
library(arules)
library(caret)
```

2. **Membuat Dataset Transaksi**

```R
# Dataset transaksi
data_list <- list(
  c("Roti", "Susu", "Keju"),
  c("Roti", "Susu"),
  c("Keju", "Telur"),
  c("Roti", "Telur", "Susu"),
  c("Telur", "Susu", "Keju")
)

# Konversi ke format transaksi
transactions <- as(data_list, "transactions")
summary(transactions)
```

3. **Menerapkan Apriori**

```R
# Menjalankan algoritma Apriori
rules <- apriori(
  transactions, 
  parameter = list(supp = 0.4, conf = 0.6, minlen = 2)
)

# Melihat hasil
inspect(rules)
```

4. **Menerapkan Bagging**

Bagging akan dilakukan dengan membangkitkan beberapa subset bootstrap dari dataset asli dan menjalankan algoritma Apriori pada masing-masing subset.

```R
set.seed(123)

# Fungsi untuk menghasilkan aturan dari subset bootstrap
generate_rules <- function(data) {
  sample_data <- sample(data, size = length(data), replace = TRUE)
  sample_transactions <- as(sample_data, "transactions")
  apriori(sample_transactions, parameter = list(supp = 0.3, conf = 0.5, minlen = 2))
}

# Iterasi bagging
n_iter <- 10
bagged_rules <- lapply(1:n_iter, function(x) generate_rules(transactions))

# Gabungkan semua aturan
all_rules <- do.call(c, bagged_rules)

# Menghilangkan aturan duplikat
unique_rules <- unique(all_rules)

# Inspeksi aturan hasil bagging
inspect(unique_rules)
```

5. **Evaluasi**

Evaluasi dilakukan dengan membandingkan hasil aturan dari Apriori tunggal dan *bagged rules*.

```R
# Evaluasi performa
summary(rules)
summary(unique_rules)

# Perbandingan jumlah aturan
cat("Jumlah aturan dari Apriori tunggal: ", length(rules), "\n")
cat("Jumlah aturan setelah Bagging: ", length(unique_rules), "\n")
```

---
