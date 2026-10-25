# Ternary PTQ Fine-Grained — Interactive Learning Hub

Website edukasi interaktif satu halaman (single-file) yang menjelaskan **Ternary Post-Training Quantization (PTQ)** dengan pendekatan **Fine-Grained** untuk kompresi model Deep Learning, dari presisi `FP32` (32-bit) menjadi bobot terner `{-1, 0, +1}` (~1.58-bit) tanpa melatih ulang model.

Seluruh aplikasi berada dalam satu file: [`interactive_ternary_ptq_learning_hub.html`](./interactive_ternary_ptq_learning_hub.html).

---

## ⚠️ Tujuan Proyek (Penting)

> **File ini dibuat hanya untuk mengetes pembuatan website menggunakan AI Argon milik Gemini.**
>
> Konten, materi, dan data yang dipakai sebagai **bahan tes** diambil dari **PPT seminar proposal milik user**. Jadi ini adalah eksperimen uji coba kemampuan AI dalam membangun website, **bukan** produk final, publikasi resmi, atau luaran penelitian yang sudah tervalidasi.
>
> Angka, metrik, dan hasil yang ditampilkan bersifat ilustratif untuk kebutuhan demonstrasi antarmuka. Untuk keperluan akademik, selalu rujuk ke dokumen proposal asli dan sumber literatur primer.

---

## ✨ Fitur Interaktif

| No | Bagian | Fitur |
|----|--------|-------|
| 1 | **Hero** | Toggle visualisasi jaringan saraf antara mode `FP32` dan `Ternary` (bobot pada koneksi berubah menjadi `+1 / -1 / 0`). |
| 2 | **Konsep & Bit** | *Bit-width Precision Explorer* — membandingkan `FP32`, `INT8`, `INT4`, dan `Ternary` (jumlah bit terpakai, rasio kompresi, kecepatan, karakteristik akurasi). |
| 3 | **Model & Dataset** | Matriks **2 arsitektur CNN × 2 dataset** (`LeNet-5` / `ResNet-18` × `MNIST` / `Fashion-MNIST`) dengan simulasi *memory footprint* FP32 vs Ternary. |
| 4 | **Simulator Terner** | Input bobot FP32 sendiri, kalkulasi **Threshold Δ = 0.7 × E(|W|)** secara live, pemetaan otomatis ke `{+1, 0, -1}`, perhitungan faktor skala `α`, serta preset/acak bobot. |
| 5 | **Coarse vs Fine-Grained** | Visualisasi matriks 16 bobot dengan pilihan granularitas **Coarse (global)**, **Fine grup 8**, dan **Fine grup 4**, lengkap dengan jumlah skala `α` dan galat rekonstruksi (MAE). |
| 6 | **Prosedur & Uji** | Pipeline **7 tahapan** implementasi (klik per tahap) dan penjelasan uji signifikansi statistik: *Cochran's Q*, *McNemar's*, dan *Wilcoxon Signed-Rank Test*. |
| 7 | **State of the Art** | Kartu literatur terkait dengan filter kategori (`Semua`, `Fokus CNN`, `Inspirasi Fine-Grained LLM`) beserta posisi kebaruan riset. |
| 8 | **Kuis** | Modal kuis 3 pertanyaan dengan skor dan pembahasan jawaban. |

---

## 🧠 Inti Materi

### Kuantisasi Terner

Bobot desimal dipetakan menjadi tiga nilai diskrit menggunakan threshold `Δ`:

$$
w_q =
\begin{cases}
+1, & \text{jika } w > +\Delta \\
0, & \text{jika } |w| \le \Delta \\
-1, & \text{jika } w < -\Delta
\end{cases}
$$

dengan

$$
\Delta = 0.7 \times E(|W|), \qquad
\alpha = \frac{1}{|W_{\ne 0}|}\sum_{w \ne 0} |w|
$$

Hasil rekonstruksi bobot: `w ≈ α · w_q`. Karena `α` konstan per grup, operasi perkalian floating-point dapat diganti penjumlahan/pengurangan sederhana.

### Coarse-Grained vs Fine-Grained

- **Coarse-Grained** — satu skala `α` untuk seluruh layer. Detail bobot lokal yang bernilai kecil mudah "tergilas" oleh skala global.
- **Fine-Grained (usulan)** — matriks bobot dipecah menjadi grup kecil (ukuran **4, 8, 16**), masing-masing memiliki skala `α₁, α₂, α₃, …` sehingga detail lokal lebih terjaga tanpa retraining.

### Rencana Eksperimen

- **Arsitektur:** `LeNet-5` (~60 ribu parameter), `ResNet-18` (~11 juta parameter)
- **Dataset:** `MNIST`, `Fashion-MNIST` (70.000 citra, 10.000 data uji)
- **Metrik:** akurasi klasifikasi, rasio kompresi memori, kecepatan inferensi (C++/CPU)
- **Validasi:** Cochran's Q & McNemar's Test (akurasi), Wilcoxon Signed-Rank Test (latensi)

---

## 🛠️ Teknologi

- **HTML5** + **JavaScript vanilla** (tanpa build step, tanpa framework)
- [Tailwind CSS](https://tailwindcss.com/) via CDN (`cdn.tailwindcss.com`)
- [Phosphor Icons](https://phosphoricons.com/) via CDN
- Google Fonts: **Inter** (teks) & **JetBrains Mono** (kode/angka)
- Visualisasi: **SVG** inline + manipulasi **DOM** langsung

Tidak ada backend, database, atau proses build — semua logika berada di dalam blok `<script>` pada file HTML.

---

## 🚀 Cara Menjalankan

### Opsi 1 — Buka langsung

Klik dua kali `interactive_ternary_ptq_learning_hub.html`, atau jalankan:

```powershell
Start-Process ".\interactive_ternary_ptq_learning_hub.html"
```

### Opsi 2 — Server lokal (disarankan)

```powershell
python -m http.server 8000
```

Lalu buka `http://localhost:8000/interactive_ternary_ptq_learning_hub.html`.

> **Catatan:** koneksi internet diperlukan saat pertama kali dibuka karena Tailwind, ikon, dan font dimuat dari CDN. Tanpa internet, tampilan akan kehilangan styling.

---

## 📁 Struktur Proyek

```text
PTQ Learning made with Argon/
├── README.md                                    # Dokumen ini
└── interactive_ternary_ptq_learning_hub.html    # Seluruh aplikasi (HTML + CSS + JS)
```

Peta bagian di dalam file HTML:

| ID Section | Isi |
|------------|-----|
| *(hero)* | Judul, ringkasan, visualisasi jaringan interaktif |
| `#masalah` | Konsep dasar kuantisasi & eksplorer presisi bit |
| `#arsitektur` | Pemilihan arsitektur CNN & dataset + telemetri kompresi |
| `#simulator-terner` | Simulator pemetaan bobot terner & threshold Δ |
| `#fine-grained` | Perbandingan Coarse vs Fine-Grained |
| `#prosedur` | 7 tahapan pipeline & uji statistik |
| `#sota` | Matriks literatur State-of-the-Art |
| `#quizModal` | Modal kuis interaktif |

---

## 🎨 Kustomisasi Cepat

| Yang ingin diubah | Lokasi |
|-------------------|--------|
| Warna tema & font | `tailwind.config` di `<head>` + variabel CSS di `<style>` |
| Data presisi bit | Objek `precisionData` |
| Preset bobot simulator | `resetDefaultExample()`, `loadPresetOutlier()`, `randomizeWeights()` |
| Tahapan pipeline | Array `pipelineSteps` |
| Literatur SOTA | Array `sotaList` |
| Pertanyaan kuis | Array `quizQuestions` |

---

## 📌 Batasan

- Angka performa (akurasi, MAE, rasio kompresi) adalah **ilustrasi** untuk keperluan demonstrasi UI, bukan hasil pengukuran riil.
- Belum ada implementasi model atau inferensi sesungguhnya — semua simulasi berjalan di sisi klien.
- Bergantung pada CDN pihak ketiga; tidak dirancang untuk penggunaan offline penuh.
- Dioptimalkan untuk browser modern (Chrome/Edge/Firefox/Safari versi terbaru).

---

## 📄 Lisensi & Kredit

- **Pembuatan website:** eksperimen AI **Argon (Gemini)**.
- **Konten & bahan tes:** PPT seminar proposal milik user.
- Materi literatur (LeNet-5, ResNet, MNIST, Fashion-MNIST, Ternary/Binary Quantization) merupakan karya akademik pihak lain dan tetap milik penulis aslinya.

Repository: [Wildan-bin/Ternary-PTQ-Learning-Argon-AI](https://github.com/Wildan-bin/Ternary-PTQ-Learning-Argon-AI)
