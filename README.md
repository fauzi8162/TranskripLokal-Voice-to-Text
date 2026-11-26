# 🎙️ TranskripLokal
Demo : https://fauzi8162.github.io/TranskripLokal-Voice-to-Text/
**Ubah file audio menjadi transkrip teks ber-timestamp 100% langsung di browser — tanpa API key, tanpa server backend, dan tanpa upload file.**

![HTML5](https://img.shields.io/badge/HTML5-Single%20File-E34F26?style=flat-square&logo=html5&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-CDN-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![Transformers.js](https://img.shields.io/badge/Transformers.js-v3-FFD21E?style=flat-square&logo=huggingface&logoColor=black)
![Whisper ONNX](https://img.shields.io/badge/AI-Whisper%20ONNX-4F46E5?style=flat-square)
![Privacy](https://img.shields.io/badge/Privacy-100%25%20Lokal-10B981?style=flat-square)

---

## ✨ Tentang Proyek

**TranskripLokal** adalah aplikasi web *single-file* berbasis client-side untuk mengubah rekaman suara atau file audio menjadi teks transkrip dengan format timestamp yang bersih. Seluruh proses kecerdasan buatan (AI) dijalankan secara lokal pada perangkat pengguna menggunakan library `@huggingface/transformers` (model OpenAI Whisper berformat ONNX) melalui **WebGPU** atau **WebAssembly (WASM)**.

### 📄 Contoh Format Hasil Transkrip

```text
[0:00] Halo teman-teman, selamat datang kembali di podcast Teknologi Santai.
[0:06] Hari ini kita akan membahas bagaimana kecerdasan buatan membantu pekerjaan sehari-hari.
[0:14] Banyak orang mengira teknologi ini rumit, padahal penggunaannya sangat sederhana.
[0:21] Mulai dari merangkum catatan rapat hingga merapikan transkrip video panjang.
[0:28] Mari kita simak tips praktis yang bisa langsung Anda terapkan hari ini.
[0:35] Jangan lupa siapkan catatan dan kopi hangat sebelum kita mulai!
```

---

## 🚀 Fitur Utama

- **🔒 100% Lokal & Privat**: File audio diproses sepenuhnya di perangkat Anda dan tidak pernah dikirim ke server pihak ketiga mana pun.
- **🚫 Tanpa API Key & Tanpa Backend**: Cukup satu file HTML statis (`transkrip-lokal.html`), siap di-host di GitHub Pages, Netlify, atau dijalankan langsung di komputer lokal.
- **⚡ Akselerasi WebGPU & Auto-Fallback WASM**: Otomatis memanfaatkan GPU browser untuk pemrosesan lebih cepat, dan secara otomatis beralih ke CPU (WASM) jika WebGPU tidak tersedia.
- **🧵 Web Worker Anti-Lag**: Proses inferensi AI berjalan di *background thread* terpisah agar halaman tetap responsif saat memutar audio atau mengedit teks.
- **💾 Cache Model Otomatis**: Model Whisper ONNX diunduh sekali ke *Browser Cache Storage*. Pemakaian berikutnya langsung instan dan dapat berjalan secara *offline*.
- **🎧 Upload File & Rekam Mikrofon**: Mendukung *drag & drop* (`.mp3`, `.wav`, `.m4a`, `.ogg`, `.webm`, `.flac`, `.mp4`) serta perekam mikrofon langsung di dalam browser.
- **📝 Editor Interaktif & Teks Mentah**:
  - Klik timestamp `[0:00]` untuk langsung melompat ke detik audio tersebut.
  - Edit teks secara langsung (*inline edit*) dengan sinkronisasi dua arah ke tab **Teks Mentah**.
  - *Auto-highlight* baris transkrip mengikuti waktu pemutaran audio.
- **📥 Ekspor Instan**: Salin teks ke clipboard dalam satu klik, atau unduh hasil sebagai file **`.TXT`** maupun subtitle **`.SRT`**.
- **🌗 Mode Gelap & Terang**: Antarmuka responsif dengan dukungan *Dark Mode* otomatis maupun manual.

---

## 🧠 Pilihan Model Whisper

Anda dapat memilih model langsung dari antarmuka aplikasi sesuai kebutuhan kecepatan dan akurasi:

| Model | ID Hugging Face | Estimasi Ukuran | Karakteristik |
| :--- | :--- | :---: | :--- |
| **Whisper Tiny** | `onnx-community/whisper-tiny` | ~40 MB | Paling ringan & cepat, cocok untuk perangkat spesifikasi rendah. |
| **Whisper Base** *(Default)* | `onnx-community/whisper-base` | ~77 MB | Seimbang antara kecepatan dan akurasi (direkomendasikan). |
| **Whisper Small** | `onnx-community/whisper-small` | ~250 MB | Akurasi lebih tinggi untuk Bahasa Indonesia & audio yang kompleks. |

> **Bahasa yang Didukung:** Bahasa Indonesia *(default)*, Deteksi Otomatis, English, Bahasa Melayu, Basa Jawa, dan Basa Sunda.

---

## ⚙️ Arsitektur & Cara Kerja

1. **Decoding Audio (Main Thread)**: File audio atau rekaman mikrofon didekode menggunakan `Web Audio API` (`AudioContext` + `OfflineAudioContext`) dan di-*resample* menjadi `Float32Array` **16kHz Mono** sesuai standar input Whisper.
2. **Transfer Data Zero-Copy**: Buffer audio dikirim ke *inline Web Worker* menggunakan *Transferable Object*.
3. **Inferensi Lokal (Web Worker)**: `@huggingface/transformers` v3 memuat model ONNX dari Hugging Face Hub (pada pemakaian pertama) lalu menyimpannya di Cache Browser, kemudian mengeksekusi transkripsi dengan *chunking* 30 detik (*stride* 5 detik) beserta *timestamp*.
4. **Pemformatan Output**: Segmen hasil transkripsi dinormalisasi ke format `[M:SS]` (atau `[H:MM:SS]` untuk audio $\ge 1$ jam) dan ditampilkan pada panel interaktif maupun teks mentah.

---

## 🌐 Kompatibilitas Browser

| Fitur | Chrome / Edge | Firefox | Safari |
| :--- | :---: | :---: | :---: |
| Transkripsi CPU (WASM) | ✅ | ✅ | ✅ |
| Akselerasi WebGPU | ✅ | ⚠️ Eksperimental | ⚠️ Terbatas |
| Rekam Mikrofon | ✅ *(HTTPS / localhost)* | ✅ *(HTTPS / localhost)* | ✅ *(HTTPS / localhost)* |

---

## 📦 Teknologi yang Digunakan

- **HTML5 & Vanilla JavaScript** (Arsitektur Single-File, tanpa *build step*)
- **[Tailwind CSS](https://tailwindcss.com/)** via CDN untuk styling responsif & *Dark Mode*
- **[@huggingface/transformers v3](https://huggingface.co/docs/transformers.js)** untuk menjalankan model AI langsung di browser
- **[OpenAI Whisper ONNX](https://huggingface.co/onnx-community)** (model *Automatic Speech Recognition*)
- **Web Audio API**, **MediaRecorder API**, & **Web Workers API**

---

## 📝 Lisensi

Proyek ini dirilis di bawah lisensi **MIT**. Silakan gunakan, modifikasi, dan distribusikan ulang secara bebas untuk keperluan pribadi maupun komersial.
