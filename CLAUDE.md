You are senior software engineer working on:

# Rangkuman Proyek MVP: Sistem Notifikasi Preventif & Reschedule RS AR Fachrudin

## 1. Latar Belakang & Masalah

- Jadwal dokter spesialis sering berubah mendadak akibat operasi darurat atau praktik di fasilitas kesehatan lain.
- Pemberitahuan perubahan jadwal kepada pasien masih dilakukan secara manual satu per satu oleh staf via WhatsApp.
- Proses manual ini memicu keterlambatan informasi, penumpukan pasien di ruang tunggu, komplain terbuka, dan ulasan negatif bagi rumah sakit.

## 2. Solusi yang Ditawarkan

Pengembangan _middleware_ (sistem penengah) berbasis API untuk mengotomatisasi notifikasi perubahan jadwal dan memfasilitasi _reschedule_ mandiri oleh pasien. Sistem ini dirancang _system-agnostic_, artinya menjadi jembatan komunikasi operasional tanpa menggantikan SIMRS yang sudah ada.

### Pembagian Tanggung Jawab Sistem

| Sistem                         | Peran & Tanggung Jawab Utama                                                                                                                                           |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SIMRS (Eksisting)**          | Menyimpan _master data_ rekam medis pasien, _billing_, dan penjadwalan awal/induk.                                                                                     |
| **Aplikasi Baru (Middleware)** | Dashboard operasional untuk mengubah status dokter, _engine_ pengirim notifikasi massal (_blast_), _interface_ konfirmasi/reschedule pasien, dan penampung _feedback_. |

## 3. Alur Kerja (End-to-End Flow)

1. **Update Status:** Staf RS (Perawat/Admisi) mengubah status kehadiran dokter (misal: "Terlambat 2 Jam") melalui **Dashboard Internal aplikasi baru**.
2. **Sinkronisasi Data:** Aplikasi secara instan menarik (_fetch_) daftar antrean pasien yang terdampak pada hari tersebut dari SIMRS.
3. **Notifikasi Otomatis:** Aplikasi memicu pengiriman pesan WhatsApp berantai kepada pasien berisi tautan akses satu kali pakai (_One-Time Link_).
4. **Self-Service Pasien:** Pasien mengklik tautan melalui _smartphone_ dan memilih opsi: "Tetap Hadir (Jam Menyesuaikan)" atau "Ubah Jadwal ke Hari Lain".
5. **Sinkronisasi Balik:** Pilihan pasien direkapitulasi di Dashboard Internal, dan sistem secara otomatis menembak _request_ API (POST/PUT) ke SIMRS untuk memperbarui jam antrean/jadwal master.

## 4. Strategi Eksekusi Prototype (Fase Proposal)

Fokus pada fase awal ini adalah mendemonstrasikan UI dan interaksi (_flow_) yang meyakinkan tanpa membangun _backend_ kompleks.

- **Teknologi:** Menggunakan HTML tunggal dipadukan dengan **Tailwind CSS** (untuk _styling_ UI cepat bergaya modern) dan **Vue.js via CDN** (untuk interaktivitas DOM instan seperti render _list_ data dan memunculkan _pop-up_ tanpa _build tools_).
- **Layar Utama (MVP):**

1. _Dashboard Staf:_ Tabel jadwal dokter dengan tombol instan "Ubah Status".
2. _Mockup Notifikasi:_ Simulasi tampilan layar HP yang menerima pesan WhatsApp otomatis.
3. _Halaman Pasien (Mobile):_ Antarmuka web sederhana untuk konfirmasi kedatangan atau pilih tanggal baru.

## 5. Nilai Jual & Fitur Keamanan (Untuk Proposal)

- **Data Masking (Penyamaran Data):** Nomor HP pasien yang ditampilkan di Dashboard Staf disamarkan (contoh: `0812xxxxxx56`). Ini mencegah penyalahgunaan kontak oleh staf (_insider threat_) sekaligus tetap memberikan rasa aman bahwa sistem sedang memproses data yang benar.
- **Pemisahan Akses (RBAC):** Sistem hanya mengolah dan menampilkan data administratif (Nama dan Kontak), tanpa menyentuh kerahasiaan diagnosis atau rekam medis.
- **Arsitektur Standar Terbuka:** Sistem dikonsepkan menggunakan standar API yang siap dihubungkan dengan ekosistem eksternal seperti FHIR SATUSEHAT Kemenkes.

## 6. Daftar Pertanyaan Validasi Kritis untuk Mitra RS

1. Apakah SIMRS yang digunakan saat ini sudah memiliki API (RESTful/JSON) yang siap diakses untuk menarik jadwal (GET) dan memperbarui antrean (POST/PUT)?
2. Apakah _database_ pendaftaran pasien di SIMRS sudah mewajibkan dan memvalidasi nomor WhatsApp yang aktif?
3. Siapa staf yang secara SOP paling berwenang untuk mengeksekusi perubahan status dokter mendadak (Perawat Poli, Pendaftaran, atau Humas)?
4. Jika pasien memilih _reschedule_ mandiri (terutama pasien BPJS), apakah ada aturan batas kuota harian atau prosedur khusus agar rujukan tidak hangus?
