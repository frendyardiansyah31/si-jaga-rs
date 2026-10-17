# DokterSiaga

*RS AR Fachrudin UMS · prototype hackathon · data dummy*

## Pasien tahu jadwal dokter berubah sebelum berangkat ke rumah sakit

Yang diminta RS AR Fachrudin UMS: petugas mengubah jadwal sekali, semua pasien terdampak diberi tahu otomatis, pasien bisa memilih jadwal pengganti, dan petugas melihat hasilnya di satu layar. Bukan sekadar aplikasi jadwal dokter.

## Sekarang vs. Dengan DokterSiaga

### Sekarang

- Dokter mendadak berhalangan, petugas mengirim WhatsApp satu per satu.
- Informasi telat, pasien sudah datang dan menunggu lama.
- Kepuasan turun, muncul ulasan buruk di ruang publik.

### Dengan DokterSiaga

- Petugas ubah status jadwal, sistem mencari pasien terdampak sendiri.
- Notifikasi dan tautan respons terkirim otomatis, target masuk antrean di bawah 1 menit.
- Pasien pilih jadwal pengganti atau minta bantuan tanpa menelepon.

## Alur dari awal sampai selesai

1. **Petugas poli — Ubah jadwal.** Pilih dokter, status baru, alasan yang disetujui RS, jadwal pengganti.
2. **Sistem — Catat dan cari pasien.** Simpan perubahan dulu, lalu ambil pasien dengan pendaftaran aktif.
3. **Sistem — Kirim notifikasi.** Pesan berisi perubahan dan tautan unik. Gagal kirim dicatat.
4. **Pasien — Buka tautan.** Halaman ponsel: paham, pilih jadwal pengganti, atau minta bantuan.
5. **Sistem — Validasi slot.** Slot penuh tidak bisa dipilih. Pilihan sah dicatat.
6. **Petugas admisi — Tindak lanjut.** Dashboard: siapa sudah, belum, gagal, atau butuh bantuan.

## Yang dibuat, disimulasikan, ditunda

### Wajib di MVP

- Dashboard jadwal dokter
- Ubah status jadwal
- Cari pasien terdampak
- Notifikasi otomatis
- Halaman respons pasien
- Reschedule sederhana
- Daftar tindak lanjut

### Cukup simulasi atau desain

- Pengiriman WhatsApp (pakai simulasi kalau kredensial belum ada)
- Konektor SIMRS
- Rancangan adapter SATUSEHAT dan BPJS
- Data dummy dan mock API

### Tunda setelah hackathon

- Integrasi produksi SATUSEHAT / BPJS
- Sinkronisasi langsung SIMRS
- Aplikasi mobile native
- Chatbot AI
- Promosi personal
- Sistem komplain lengkap

## Kebutuhan fitur

| Kode | Fitur | Intinya | Pengguna |
| --- | --- | --- | --- |
| F01 | Manajemen jadwal | Lihat jadwal, ubah status: Terjadwal, Berubah, Dibatalkan, Selesai | Petugas poli |
| F02 | Pasien terdampak | Hanya pendaftaran aktif di jadwal itu; yang batal tidak diberi pesan | Sistem |
| F03 | Notifikasi otomatis | Status: menunggu, terkirim, gagal, tidak diketahui. Terkirim belum berarti dibaca | Sistem |
| F04 | Konfirmasi dan reschedule | Paham, pilih jadwal pengganti, atau minta bantuan; slot penuh ditolak | Pasien |
| F05 | Dashboard koordinasi | Jadwal berubah, jumlah terdampak, status kirim, respons, yang perlu ditindaklanjuti | Admisi, humas |
| F06 | Riwayat dan audit | Semua perubahan, pengiriman, respons, dan tindakan petugas dengan waktu dan pelaku | Admin IT |

## Aturan bisnis

1. Perubahan tercatat sebelum notifikasi diproses.
2. Hanya pasien dengan pendaftaran aktif di jadwal itu yang menerima pesan.
3. Perubahan berulang tidak menggandakan notifikasi.
4. Pesan gagal terlihat dan bisa dicoba ulang.
5. Tautan memakai token acak, ada masa berlaku, tanpa data pribadi.
6. Pilihan jadwal cek slot dan cegah pesan ganda.
7. Pasien yang belum merespons masuk daftar tindak lanjut.
8. Alasan di pesan harus disetujui RS, tanpa info medis atau kondisi dokter.
9. Akses dibatasi per peran, tindakan penting masuk audit log.

## Ukuran keberhasilan

| Ukuran | Target |
| --- | --- |
| Informasi cepat | Masuk antrean pengiriman paling lama 1 menit setelah jadwal disimpan |
| Kerja manual turun | Semua pasien terdampak dihubungi tanpa satu per satu |
| Pasien mudah | Respons lewat tautan, tanpa menelepon RS |
| Koordinasi | Satu dashboard untuk perubahan dan respons |
| Reschedule terpantau | Pilihan jadwal tercatat dan bisa ditindaklanjuti |

> Waktu masuk antrean diukur terpisah dari waktu pesan diterima pasien, karena pengiriman lewat penyedia kanal tidak sepenuhnya dikendalikan aplikasi.

## Arsitektur MVP

- **Web app** — Dashboard petugas + halaman respons pasien
- **Backend API** — Validasi jadwal, akses, respons
- **Database** — Jadwal, pendaftaran, notifikasi, audit
- **Worker antrean** — Kirim, catat hasil, coba ulang
- **Kanal notifikasi** — WhatsApp Business API atau simulasi
- **Adapter (tahap lanjut)** — SIMRS, SATUSEHAT (HL7 FHIR), BPJS

> Satu backend modular sudah cukup, tanpa microservices. SATUSEHAT memakai HL7 FHIR dengan aturan resource tersendiri, jadi di MVP cukup rancangan adapter dan pemetaan data, tanpa klaim sudah terintegrasi.

## Checklist kelulusan MVP

- [ ] **Petugas dapat mengubah jadwal dokter** — Status, alasan, dan jadwal pengganti tersimpan.
- [ ] **Pasien terdampak teridentifikasi dengan benar** — Hanya pendaftaran aktif di jadwal itu; jadwal lain dan pendaftaran batal tidak ikut.
- [ ] **Notifikasi otomatis diproses** — Pesan dibuat tanpa kirim manual dan waktu antrean tercatat.
- [ ] **Pasien dapat merespons** — Tautan membuka informasi dan pasien memilih tindakan.
- [ ] **Reschedule memvalidasi slot** — Slot penuh ditolak, pilihan sah tercatat.
- [ ] **Dashboard menunjukkan status terkini** — Respons, kegagalan, dan tindak lanjut terlihat.
- [ ] **Riwayat tindakan dapat diaudit** — Perubahan dan respons punya catatan waktu.
- [ ] **Demo menjaga privasi** — Data dummy, nomor tersamarkan, tautan tanpa identitas.

## Urutan pengerjaan

1. Data dummy: dokter, poli, jadwal, pendaftaran.
2. Dashboard petugas dan form ubah jadwal.
3. Mesin notifikasi: cari pasien, buat pesan, antrekan, catat status.
4. Halaman pasien: paham, pilih slot, minta bantuan.
5. Dashboard tindak lanjut: respons, gagal, belum merespons.
6. Uji satu kasus dari awal sampai akhir, termasuk pesan gagal dan slot penuh.

> Selesaikan alur inti dulu sebelum menambah promosi, AI, atau integrasi yang hanya berupa tampilan. Seluruh data pasien di demo ini dummy.
