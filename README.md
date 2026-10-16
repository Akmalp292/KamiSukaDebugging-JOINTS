# StuntingCare

Aplikasi mobile-first untuk mendukung pencegahan stunting: pelacak pertumbuhan anak berbasis standar WHO, konsultasi online dengan dokter, dua fitur AI (asisten dan ringkasan konsultasi), dan siaga darurat (SOS).

**Tim KamiSukaDebugging** · Universitas Bina Nusantara · GEMASTIK XIX
Ketua: Akmal Dwi Putra Mahardika · Anggota: Andi Siti Maryam Shaskirana, Rifa Naftali Azka
Dosen Pembimbing: Gabriel Asael Tarigan, S.Kom., M.Kom.

- Aplikasi: https://stuntingcare.my.id/
- Video demo: [isi tautan video demo versi terbaru]

## Fitur

| Fitur | Keterangan |
|---|---|
| Pelacak Pertumbuhan | Input BB dan TB, hitung Z-score WHO (TB/U dan BB/U), status Hijau, Kuning, atau Merah, grafik riwayat, ekspor PDF |
| Konsultasi Online | Chat real-time pasien dan dokter, dokter online dicocokkan otomatis sesuai topik |
| Asisten AI | Chatbot edukasi gizi, MPASI, dan tumbuh kembang, dengan deteksi tanda bahaya yang langsung menampilkan 119 |
| Ringkasan Konsultasi AI | Transkrip chat diringkas menjadi 3 poin: Diagnosa, Resep Obat, Pantangan Makanan, dan bisa diunduh PDF |
| Rekomendasi Menu MPASI | Berbasis aturan (kata kunci diagnosa dan usia anak), disaring dengan pantangan dari dokter |
| Siaga Darurat (SOS) | Pilih kecamatan, lihat faskes terdekat di peta, panduan pertolongan pertama, telepon 119, dan pantau ambulans |
| Akun | Profil anak dan kehamilan (HPHT), riwayat, unduh data, hapus akun |

## Fitur AI

- **Model:** Gemma 4 (26B-A4B, instruction-tuned) lewat Google AI Studio API
- **Integrasi:** Aplikasi (React) → Backend FastAPI (`/chat`, `/summarize`) → Google AI Studio API
- **Asisten AI:** pertanyaan dan 6 pesan terakhir, dengan konteks dari `knowledge_base.json`
- **Ringkasan:** transkrip chat menjadi Diagnosa, Resep, dan Pantangan, dan bagian yang tidak disebut dokter ditulis "Tidak disebutkan dokter."
- **Pengaman:** tidak mendiagnosis dan tidak menyebut dosis, di luar topik ditolak, tanda bahaya langsung ke 119, kunci API hanya di server

Perhitungan Z-score, status warna, rekomendasi menu, dan deteksi tanda bahaya **tidak memakai AI** (berbasis aturan), supaya konsisten dan bisa diaudit.

## Teknologi

React 19, Vite, React Router, Firebase (Auth dan Firestore), Leaflet + OpenStreetMap, jsPDF, FastAPI (Python), Google AI Studio (Gemma)

## Struktur

```
src/
  components/   Tracker, Rekomendasi, AsistenChat, SosButton, AkunPage, Maskot, ...
  lib/          growth (Z-score WHO), recommend, sos, kehamilan, summarizeBackend, ...
  data/         who.json, recipes.json, promos.json
  pages/        AuthPage, MainPage
  legacy/       logika chat dokter
server/ai/      backend FastAPI (chatbot dan ringkasan)
scripts/        build-who.mjs (membuat who.json dari tabel WHO)
```

## Menjalankan

Prasyarat: Node.js 20.19+ dan Python 3.10+.

```bash
# Web
cp .env.example .env        # isi nilai Firebase
npm install
npm run dev                 # http://localhost:5173

# Backend AI (opsional, untuk Asisten dan Ringkasan)
cd server/ai
cp .env.example .env        # isi GOOGLE_API_KEY
pip install -r requirements.txt
python -m uvicorn app:app --reload --port 8000
```

Variabel `.env` (root): `VITE_FIREBASE_*` (6 nilai), `VITE_CHATBOT_ENDPOINT`, `VITE_SUMMARIZE_ENDPOINT`, `VITE_FASKES_DUMMY=true`, `VITE_SOS_DEMO=true`.

Data WHO: unduh tabel Z-score (xlsx) dari WHO ke satu folder, lalu `node scripts/build-who.mjs <folder>`.

## Status dan Batasan (prototype)

- Daftar faskes di SOS adalah **data contoh**, dan pergerakan ambulans adalah **simulasi**. Tombol telepon 119 tetap menelepon nomor sungguhan.
- Ambang status pertumbuhan, panduan pertolongan pertama, dan menu belum divalidasi tenaga medis.
- Hasil pelacak dan jawaban AI adalah skrining awal dan informasi umum, bukan diagnosis.
- Transkrip chat untuk ringkasan dikirim ke Google AI Studio.

## Lisensi

MIT © Tim KamiSukaDebugging, Universitas Bina Nusantara
