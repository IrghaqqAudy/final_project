# Sistem Pengaduan Warga Otomatis (Perumahan Permata Mediterania)

Proyek ini adalah workflow n8n yang dirancang untuk mengotomatisasi proses penerimaan, klasifikasi berbasis AI, pencatatan, dan pengiriman notifikasi atas keluhan warga di Perumahan Permata Mediterania.

---

## 📌 Latar Belakang & Masalah

* **Masalah:** Banyaknya keluhan warga yang masuk setiap hari namun penanganannya lambat atau tidak tepat karena keterbatasan personel serta kendala penentuan prioritas. Hal ini sering kali memicu penumpukan masalah dan diskusi berulang di WhatsApp Group (WAG) warga.
* **Solusi Otomasi:** Menggunakan kecerdasan buatan (Google Gemini) untuk menganalisis dan mengelompokkan laporan secara otomatis berdasarkan **Kategori** dan **Tingkat Urgensi**, lalu mengarahkannya ke media komunikasi yang tepat (Email/Telegram) serta mencatatnya di Google Sheets.

---

## 🚀 Alur Kerja (Workflow Steps)

```
[ Form Submisi Warga ]
          │
          ▼
[ AI Classification (Google Gemini) ] ────► Analisis Kategori & Urgensi
          │
          ▼
[ Data Parsing & Formatting ] ──────────► Konversi Format Waktu & JSON
          │
          ├──────────────────────────────────────────┐
          ▼                                          ▼
[ Switch Kategori ]                     [ Filter Urgensi Sedang/Rendah ]
  ├── Infrastruktur                                  │
  ├── Keamanan                                       ▼
  └── Kebersihan                         [ Notifikasi Telegram ]
          │
          ▼
[ Append to Google Sheets ]
          │
          ▼
[ Filter Urgensi Tinggi ]
          │
          ▼
[ Kirim Email Gmail (Ketua RT/Pengurus) ]
```

### Detail Langkah Notifikasi & Penyimpanan:
1. **Form Trigger:** Warga mengisi formulir publik (Nama, Alamat, Keluhan, No. HP).
2. **AI Processing:** Google Gemini (`models/gemini-3-flash-preview`) mengklasifikasikan isi keluhan ke dalam:
   - **Kategori:** *Infrastruktur*, *Kebersihan*, atau *Keamanan*.
   - **Urgensi:** *Rendah*, *Sedang*, atau *Tinggi*.
3. **Penyimpanan Data (Google Sheets):** Data disimpan ke dalam spreadsheet `monitoring_laporan_warga` sesuai tab kategorinya masing-masing.
4. **Notifikasi Terarah:**
   - **Urgensi Tinggi:** Langsung mengirimkan email alert melalui **Gmail** kepada pengurus/Ketua RT (`irghaqq@gmail.com`) untuk tindakan darurat.
   - **Urgensi Sedang / Rendah:** Mengirimkan pesan koordinasi berkala ke grup/chat **Telegram**.

---

## 🛠️ Integrasi & Layanan yang Digunakan

| Komponen | Node n8n | Fungsi |
| :--- | :--- | :--- |
| **Trigger** | `n8n-nodes-base.formTrigger` | Menampilkan formulir input pengaduan warga. |
| **AI Model** | `@n8n/n8n-nodes-langchain.googleGemini` | Analisis konteks dan penentuan prioritas. |
| **Database** | `n8n-nodes-base.googleSheets` | Menyiapkan rekapitulasi data keluhan warga. |
| **Email Alert**| `n8n-nodes-base.gmail` | Mengirim notifikasi email untuk laporan darurat (Tinggi). |
| **Messaging**  | `n8n-nodes-base.telegram` | Mengirim notifikasi pesan ringkas untuk keluhan umum/biasa. |

---

## ⚙️ Persyaratan & Integrasi Kredensial (Credentials)

Untuk menjalankan workflow ini di instance n8n Anda, Anda perlu menyiapkan credentials berikut:

1. **Google Gemini (PaLM) API:** Untuk modul analisis teks berbasis AI.
2. **Google Sheets OAuth2:** Akses ke spreadsheet target `monitoring_laporan_warga`.
3. **Gmail OAuth2:** Untuk mengirimkan surel pemberitahuan ke pengurus.
4. **Telegram API Bot:** Bot token & Chat ID target untuk notifikasi Telegram.

---

## 📥 Cara Mengimpor Workflow ke n8n

1. Buka dashboard **n8n** Anda.
2. Buat workflow baru (**Create Workflow**).
3. Klik menu titik tiga `...` di pojok kanan atas, lalu pilih **Import from File**.
4. Pilih file `final_project_irghaqq (1).json`.
5. Sesuaikan kredensial di setiap node dengan akun Anda.
6. Aktifkan workflow dengan mengklik sakelar **Active** di bagian atas.