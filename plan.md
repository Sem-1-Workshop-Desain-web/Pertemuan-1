# PRODUCT REQUIREMENTS DOCUMENT (PRD)

## Sistem Absensi Muda-Mudi Gemurung 2

**Versi:** 1.0  
**Status:** Final — Ready for Implementation  
**Platform:** Web / PWA  
**Target utama:** Browser HP  
**Timezone resmi:** `Asia/Jakarta`  
**Deployment:** Vercel  
**Backend:** Supabase  
**Database:** PostgreSQL / Supabase  
**Frontend:** React + Vite  
**Styling:** CSS biasa — **JANGAN menggunakan Tailwind CSS**

---

# 1. PRODUCT OVERVIEW

## 1.1 Nama Produk

**Absensi Muda-Mudi Gemurung 2**

Aplikasi web internal untuk mengelola:

- absensi pengajian rutin Muda-Mudi Gemurung 2;
- absensi pengajian khusus;
- materi pengajian;
- anggota;
- jadwal;
- izin;
- laporan kehadiran;
- statistik;
- export laporan;
- data kegiatan khusus.

Aplikasi dibuat khusus untuk kebutuhan Muda-Mudi Gemurung 2 dan **tidak perlu dibuat sebagai sistem HR/attendance generik**.

Jangan menambahkan fitur seperti:

- payroll;
- shift kerja;
- employee management;
- face recognition;
- fingerprint;
- geofencing;
- GPS attendance;
- clock-in/out karyawan;
- dan fitur enterprise attendance lain yang tidak diperlukan.

---

# 2. TUJUAN PRODUK

Tujuan utama:

1. Membuat proses pengisian absensi pengajian sangat cepat melalui HP.
2. Pengelola dapat langsung membuka website dan mengisi absensi tanpa harus masuk dashboard terlebih dahulu.
3. Menyimpan histori absensi secara terstruktur.
4. Mencatat materi yang disampaikan pada setiap kegiatan.
5. Membedakan pengajian rutin dan pengajian khusus.
6. Menyediakan laporan bulanan yang transparan.
7. Menyediakan export Excel yang dapat digunakan untuk dokumentasi.
8. Mendukung penggunaan ketika koneksi internet tidak stabil.
9. Menyediakan guest/demo environment tanpa pernah mencampurkan data guest dengan data akun asli.

---

# 3. PRINCIPLE / PRODUCT PHILOSOPHY

Aplikasi harus:

- sederhana;
- cepat digunakan di HP;
- tidak memiliki navigasi yang berlebihan;
- tidak meminta pengguna mengisi data yang tidak diperlukan;
- menggunakan bahasa Indonesia;
- mengutamakan tabel absensi dan form absensi;
- transparan terhadap status data;
- aman terhadap kesalahan input;
- tidak menghapus histori secara tidak sengaja.

---

# 4. TECH STACK

## 4.1 Frontend

Gunakan:

- React
- Vite
- JavaScript atau TypeScript
- CSS biasa

### Constraint styling

**JANGAN menggunakan:**

- Tailwind CSS
- Bootstrap
- Material UI
- framework CSS lain

Gunakan:

- CSS Modules atau plain CSS;
- CSS variables;
- Flexbox;
- CSS Grid;
- media queries;
- sticky positioning;
- responsive CSS.

---

# 5. BACKEND

Gunakan Supabase:

- Supabase Auth
- PostgreSQL
- Row Level Security (RLS)
- Supabase Storage hanya jika benar-benar diperlukan
- Supabase Cron / pg_cron
- Supabase Edge Functions jika diperlukan untuk pekerjaan server-side

---

# 6. DEPLOYMENT

Frontend:

```text
Vercel
```

Repository:

```text
Satu repository / satu project
```

Frontend dan konfigurasi backend berada dalam satu project/repository.

Supabase menjadi backend service.

Tidak perlu membuat server Express/Laravel terpisah.

---

# 7. PWA

Aplikasi harus dibuat sebagai **Progressive Web App**.

Tujuannya:

- dapat dibuka melalui browser;
- dapat ditambahkan ke Home Screen;
- asset aplikasi dapat dicache;
- aplikasi dapat tetap dibuka ketika offline;
- draft dan perubahan offline dapat disimpan secara lokal;
- perubahan dapat disinkronkan ketika koneksi kembali.

---

# 8. OFFLINE-FIRST REQUIREMENT

Offline support merupakan fitur penting.

Gunakan:

```text
IndexedDB
```

untuk penyimpanan lokal.

Jangan gunakan localStorage sebagai database utama aplikasi.

---

## 8.1 Ketika Online

Flow:

```text
User
 ↓
React
 ↓
Supabase
 ↓
PostgreSQL
```

---

## 8.2 Ketika Offline

Flow:

```text
User
 ↓
React
 ↓
IndexedDB
 ↓
Local Sync Queue
```

User tetap dapat:

- melihat data yang telah tersinkron sebelumnya;
- membuka form;
- mengisi absensi;
- mengisi materi;
- membuat/mengubah data yang diperbolehkan;
- menyimpan draft.

---

## 8.3 Ketika koneksi kembali

Aplikasi harus mencoba:

```text
IndexedDB
 ↓
Sync Queue
 ↓
Supabase
```

Jika browser mendukung Background Sync, gunakan Background Sync.

Namun aplikasi **tidak boleh menganggap Background Sync selalu tersedia**.

Jika tidak tersedia:

```text
Data tetap disimpan lokal.
Ketika user membuka kembali aplikasi saat online,
aplikasi melakukan sinkronisasi.
```

Tampilkan peringatan:

> Ada data yang belum tersinkron. Hubungkan ke internet agar data dapat dikirim.

---

# 9. ONLINE/OFFLINE INDICATOR

Selalu tampilkan status koneksi.

Contoh:

```text
● Online
```

atau:

```text
● Offline
```

Ketika offline:

> Anda sedang offline. Perubahan akan disimpan di perangkat dan dikirim ketika koneksi kembali.

Ketika sedang sinkron:

> Menyinkronkan data...

Setelah berhasil:

> Semua perubahan telah tersinkron.

---

# 10. TIMEZONE

Timezone resmi aplikasi:

```text
Asia/Jakarta
```

Jangan menggunakan timezone server secara implicit.

Semua:

- tanggal;
- waktu jadwal;
- waktu lock;
- laporan;
- recurring schedule;

harus dihitung berdasarkan `Asia/Jakarta`.

Ketika online, server/database menjadi sumber waktu utama.

Ketika offline, gunakan waktu device sebagai fallback sementara.

Saat kembali online, validasi ulang menggunakan waktu server.

---

# 11. ACCOUNT MODEL

Sistem tidak menggunakan role:

```text
admin
user
petugas
anggota
```

Tidak ada role-based application flow.

Pengguna yang login ke aplikasi memiliki akses pengelolaan sistem.

Namun database tetap harus dilindungi dengan authentication + RLS.

---

# 12. ACCOUNT ASLI

Akun asli menggunakan:

```text
Supabase Auth
```

Registrasi publik **tidak tersedia**.

Tidak ada halaman:

```text
Register
```

Akun dibuat/dikelola secara internal.

---

# 13. GUEST ACCOUNT

Aplikasi memiliki opsi:

```text
Login
Masuk sebagai Tamu
```

Guest digunakan untuk demo/testing.

Guest **tidak boleh mendapatkan data asli**.

Guest memiliki:

- account/session sendiri;
- database scope sendiri;
- data seed/dummy sendiri.

---

## 13.1 Guest Isolation

Semua data harus memiliki scope account/tenant.

Contoh:

```text
account_id
```

Guest:

```text
account_id = guest_x
```

Akun asli:

```text
account_id = real_account
```

RLS harus memastikan:

```text
guest_x
```

tidak pernah dapat membaca:

```text
real_account
```

Frontend check saja tidak cukup.

Isolation wajib dilakukan pada database/RLS.

---

# 14. GUEST SEED DATA

Ketika guest dibuat, sistem membuat dataset dummy.

Contoh:

- anggota dummy;
- jadwal dummy;
- absensi dummy;
- materi dummy;
- kegiatan dummy;
- laporan dummy.

Tujuannya agar guest langsung dapat melihat aplikasi dalam keadaan terisi.

Guest tidak boleh menggunakan data asli.

---

# 15. GUEST EXPIRATION

Setiap guest memiliki:

```text
created_at
```

Guest otomatis dihapus setelah:

```text
24 jam sejak account dibuat
```

Bukan 24 jam sejak login terakhir.

Contoh:

```text
Guest dibuat:
3 Oktober 18:00

Expired:
4 Oktober 18:00
```

Walaupun guest aktif menggunakan website selama berjam-jam, expiration tetap berdasarkan `created_at`.

---

# 16. LOGOUT GUEST

Jika guest logout:

```text
langsung hapus guest data
```

Tidak perlu menunggu 24 jam.

Yang dihapus:

- guest auth/account;
- anggota guest;
- jadwal guest;
- attendance guest;
- materi guest;
- kegiatan guest;
- data terkait lainnya.

Tidak boleh menyentuh data account asli.

---

# 17. GUEST CLEANUP CRON

Gunakan scheduled job/cron untuk mencari guest:

```text
created_at < now() - 24 hours
```

kemudian menghapus seluruh data guest.

Cleanup harus aman terhadap foreign key.

---

# 18. NAVIGATION

Aplikasi memiliki tiga halaman utama:

```text
1. Absensi
2. Laporan
3. Admin
```

Navigation utama berada di **bagian bawah layar**, menyerupai aplikasi mobile.

Contoh:

```text
┌──────────────────────────────┐
│                              │
│          CONTENT             │
│                              │
├────────────┬──────────┬──────┤
│  Absensi   │ Laporan  │Admin │
└────────────┴──────────┴──────┘
```

---

# 19. DEFAULT PAGE

Ketika aplikasi dibuka setelah login:

```text
Absensi
```

harus langsung menjadi halaman utama.

Jangan membuka Dashboard.

Tidak ada halaman Dashboard terpisah.

---

# 20. DATA MODEL UTAMA

Database minimal memiliki entitas:

```text
accounts
members
member_statuses
member_status_history (jika diperlukan)
recurring_schedules
schedule_occurrences
special_event_types
special_events
attendance
absence_types
material_types
hadith_materials
free_activity_types
speakers
quran_surahs
materials
holidays
audit_logs
```

Nama tabel dapat disesuaikan oleh agent, tetapi relasi dan behavior harus mengikuti PRD ini.

---

# 21. ACCOUNT

Minimal:

```text
id
auth_user_id
account_type
created_at
```

`account_type`:

```text
REAL
GUEST
```

---

# 22. MEMBERS

Minimal:

```text
id
account_id
full_name
nickname
gender
birth_date
status_id
joined_at
active
created_at
updated_at
```

Gender:

```text
MALE
FEMALE
```

Status anggota adalah master yang dapat dikonfigurasi Admin.

Contoh default:

```text
Sekolah
Kuliah
Bekerja
```

---

# 23. MEMBER ACTIVE RULE

`active = true`:

- muncul pada form absensi baru;
- dihitung dalam laporan.

`active = false`:

- tidak muncul pada form absensi baru;
- tidak dihitung pada laporan baru;
- histori lama tetap dipertahankan.

Ini adalah satu-satunya konsep "nonaktif" utama pada data anggota.

---

# 24. MEMBER HISTORY

Anggota yang baru bergabung tidak boleh otomatis dianggap Alpha pada jadwal sebelum ia bergabung.

Gunakan:

```text
joined_at
```

Saat menentukan apakah anggota termasuk dalam suatu jadwal:

```text
schedule_date >= joined_at
```

Jika anggota menjadi nonaktif, data histori tetap dipertahankan.

Implementasi history status boleh menggunakan pendekatan yang paling aman dan sederhana bagi database.

---

# 25. MEMBER CRUD

Admin dapat:

### Create
Tambah anggota.

### Read
Melihat anggota.

### Update
Mengubah:

- nama lengkap;
- nama panggilan;
- gender;
- tanggal lahir;
- status;
- aktif/tidak aktif.

### Delete
Tidak disarankan menghapus anggota yang memiliki histori, tetapi sistem dapat menyediakan permanent delete dengan warning keras jika diperlukan.

Default action untuk anggota adalah:

```text
active = false
```

bukan menghapus histori.

---

# 26. STATUS ANGGOTA MASTER

Admin dapat mengelola status:

```text
Sekolah
Kuliah
Bekerja
```

Admin dapat:

- tambah;
- edit;
- hapus permanen.

Permanent delete harus menampilkan confirmation.

Jika status sedang digunakan oleh anggota, UI harus memberi warning sebelum menghapus.

---

# 27. RECURRING SCHEDULE

Admin dapat mengatur jadwal rutin mingguan.

Contoh:

```text
☑ Rabu
Jam: 19:30

☑ Jumat
Jam: 19:30
```

Hari dan jam masing-masing dapat berbeda.

Contoh valid:

```text
Rabu 19:30
Jumat 20:00
```

---

# 28. GLOBAL LOCK/EDIT DURATION

Durasi umum ditentukan di Admin.

Default:

```text
24 jam
```

Durasi ini berlaku untuk seluruh jadwal.

Namun **attendance tidak menggunakan konsep lock state lagi**.

Durasi tersebut digunakan sebagai aturan temporal untuk menentukan batas perubahan data berdasarkan waktu jadwal.

---

# 29. SCHEDULE OCCURRENCE

Recurring schedule menghasilkan occurrence aktual.

Contoh:

```text
Recurring:
Rabu 19:30

Occurrence:
2026-10-07 19:30
2026-10-14 19:30
2026-10-21 19:30
```

Setiap occurrence memiliki ID sendiri.

Attendance dan material mengacu pada occurrence, bukan hanya recurring rule.

---

# 30. FORM OPEN TIME

Absensi tidak dapat diisi kapan saja.

Jika jadwal:

```text
19:30
```

maka form mulai dapat digunakan:

```text
19:00
```

atau:

```text
30 menit sebelum jadwal
```

Toleransi 30 menit ini berlaku umum.

---

# 31. BEFORE FORM WINDOW

Jika user membuka form terlalu awal:

```text
Sekarang 18:00
Jadwal 19:30
```

form tidak dapat dikirim.

Tampilkan:

> Absensi belum dapat diisi. Absensi untuk jadwal ini baru dapat diisi mulai pukul 19:00, yaitu 30 menit sebelum acara dimulai.

---

# 32. CURRENT DAY WARNING

Jika user membuka aplikasi pada hari yang bukan hari pengajian:

Contoh:

```text
Hari ini Kamis.
Jadwal berikutnya Jumat.
```

Tampilkan:

> Hari ini masih hari Kamis. Pengajian berikutnya diadakan pada hari Jumat.

Form default diarahkan ke jadwal berikutnya yang tersedia.

---

# 33. PREVIOUS SCHEDULE WARNING

Jika user membuka website pada Sabtu dan jadwal Jumat sebelumnya belum diisi:

Form default dapat diarahkan ke jadwal Jumat yang belum diisi, sesuai aturan navigasi schedule.

Tampilkan:

> Jadwal pengajian Jumat kemarin belum diisi absensinya.

Jangan menganggap jadwal tersebut libur.

---

# 34. FUTURE SCHEDULE

User tidak dapat mengisi jadwal yang belum masuk window 30 menit sebelum jadwal.

Contoh:

```text
Sekarang:
Senin 10:00

Jadwal:
Rabu 19:30
```

Form Rabu tidak dapat dikirim.

---

# 35. ABSENCE / ATTENDANCE MODEL

Attendance memiliki tiga kemungkinan:

```text
HADIR
IZIN
ALPHA
```

UI tidak menggunakan dropdown status utama.

---

# 36. ATTENDANCE UI

Setiap anggota:

```text
Nama
[ Checkbox ] [ Dropdown Izin ]
```

Behavior:

### Hadir

```text
☑ [disabled/no permission]
```

Checkbox tercentang = Hadir.

### Alpha

```text
☐ [Tidak ada izin]
```

Checkbox kosong + tidak ada izin = Alpha.

### Izin

```text
☐ [Kuliah ▼]
```

Dropdown berisi jenis izin = Izin.

---

# 37. MUTUAL EXCLUSION

Jika checkbox dicentang:

```text
☑
```

dropdown izin harus disabled/tidak dapat dipilih.

Jika dropdown izin memiliki nilai:

```text
Kuliah
```

checkbox tidak dapat dicentang.

Jika dropdown izin dikembalikan ke:

```text
Tidak ada izin
```

checkbox kembali dapat digunakan.

---

# 38. ABSENCE TYPES

Master jenis izin.

Default:

```text
Sakit
Sekolah
Kuliah
Kerja
Di luar kota
```

Admin dapat:

- tambah;
- edit;
- hapus permanen.

Delete harus meminta confirmation.

Data historis yang sudah menggunakan jenis izin harus tetap dapat ditampilkan secara aman. Jika diperlukan, attendance menyimpan snapshot nama kategori pada saat transaksi.

---

# 39. FORM ABSENSI UTAMA

Form berada **langsung di halaman Absensi**, bukan modal.

Urutan halaman:

```text
Header
↓
Status online/offline
↓
Form Absensi
↓
Form Materi
↓
Action buttons
↓
Tabel Absensi
```

---

# 40. FORM ABSENSI — HEADER

Tampilkan:

```text
Jadwal:
Jumat, 3 Oktober 2026

Jam:
19:30
```

Jika belum waktunya:

```text
Absensi tersedia mulai 19:00.
```

Jika sudah dapat diisi:

```text
Absensi tersedia.
```

---

# 41. FORM ABSENSI — PENJELASAN STATUS

Di atas daftar anggota, tampilkan legenda:

```text
☑ Centang = Hadir

☐ Kosong + tidak memilih izin = Alpha

Dropdown izin = anggota tidak hadir karena alasan yang dipilih
```

Ini wajib agar pengguna memahami arti checkbox.

---

# 42. MEMBER LIST

Form membagi anggota menjadi:

```text
LAKI-LAKI
PEREMPUAN
```

Layout:

```text
┌─────────────────┬─────────────────┐
│ LAKI-LAKI       │ PEREMPUAN       │
│                 │                 │
│ Rizal  ☑  [—]   │ Aisyah ☑ [—]    │
│ Budi   ☐ [Kuliah]│ Siti   ☐ [Sakit]│
└─────────────────┴─────────────────┘
```

Pada HP, kedua kolom tetap harus nyaman digunakan.

Jika layar terlalu sempit, gunakan responsive layout yang tetap mempertahankan grouping laki-laki/perempuan.

---

# 43. NICKNAME

Form absensi selalu menggunakan:

```text
nickname
```

Tidak ada toggle nama lengkap/nama panggilan di form.

---

# 44. FORM RESET

Tombol:

```text
Reset Form
```

menghapus draft lokal untuk occurrence tersebut.

Sebelum reset:

> Hapus semua jawaban sementara pada form ini?

Buttons:

```text
Batal
Reset
```

---

# 45. DRAFT FORM

Draft form disimpan lokal seperti Google Forms.

Jika user:

```text
mengisi sebagian
↓
menutup browser
↓
membuka kembali
```

jawaban tetap ada.

Draft disimpan berdasarkan:

```text
account_id
schedule_occurrence_id
```

Jangan membuat satu draft global.

---

# 46. SUBMIT ATTENDANCE

Ketika user menekan:

```text
Simpan Absensi
```

lakukan validasi.

Jika valid:

```text
attendance records dibuat
```

Untuk setiap anggota yang termasuk pada jadwal tersebut:

```text
checkbox checked
→ PRESENT

checkbox unchecked + no leave
→ ALPHA

leave selected
→ PERMITTED
```

Setelah berhasil:

- form disembunyikan/reset;
- tabel refresh;
- status jadwal berubah menjadi sudah diisi;
- tampilkan success message;
- tampilkan tombol Edit Absensi.

---

# 47. EDIT ABSENSI TERAKHIR

Setelah submit:

```text
✓ Absensi berhasil disimpan.

[ Edit Absensi Ini ]
```

Ketika ditekan:

- form absensi muncul;
- tanggal/jadwal terpilih;
- data diambil dari database;
- checkbox dan dropdown diisi berdasarkan database.

Jangan hanya menggunakan state lama.

---

# 48. EDIT DATA ABSENSI LAMA

Sediakan:

```text
Edit Kehadiran
```

Form memiliki:

```text
Tanggal/Jadwal
Absensi
Materi
```

Tanggal hanya dapat memilih occurrence yang tersedia di database.

Anggota yang ditampilkan mengikuti kondisi anggota pada periode tersebut.

---

# 49. EDIT ABSENSI FORM

Form edit menggunakan UI yang sama dengan form absensi normal.

Tidak membuat editor terpisah.

Perbedaannya:

```text
Mode:
CREATE
atau
EDIT
```

---

# 50. EDIT MATERI

Form edit juga mencakup materi untuk jadwal yang sama.

Admin/pengguna dapat:

- mengubah Quran;
- mengubah Hadist;
- mengubah Nasehat;
- mengubah kegiatan bebas;
- menambah/menghapus bagian materi sesuai kebutuhan.

---

# 51. MATERIAL TYPES

Pada form materi tersedia empat jenis section:

```text
1. Al-Qur'an
2. Hadist
3. Nasehat
4. Materi/Kegiatan Bebas
```

ON/OFF section dilakukan **ketika mengisi jadwal**.

Ini bukan konfigurasi global Admin.

Contoh Rabu:

```text
☑ Al-Qur'an
☐ Hadist
☑ Nasehat
☐ Kegiatan bebas
```

Contoh Jumat:

```text
☑ Al-Qur'an
☑ Hadist
☑ Nasehat
☑ Kegiatan bebas
```

---

# 52. QURAN MATERIAL

Al-Qur'an merupakan jenis materi default.

Field:

```text
Surat
Ayat
Pemateri
```

User memasukkan nomor surat.

Contoh:

```text
Nomor surat:
36
```

Sistem langsung menampilkan:

```text
Yasin
```

sebelum form dikirim.

Tidak perlu submit untuk mendapatkan nama surat.

Gunakan master Quran:

```text
1 Al-Fatihah
2 Al-Baqarah
...
114 An-Nas
```

Field ayat dapat berupa:

```text
1
1-10
10-15
```

Validasi agar nomor surat valid 1–114.

---

# 53. HADITH MATERIAL

Admin mengelola master Hadist.

Default contoh:

```text
Hadist Adab
Hadist Kitabush Shalah
Hadist Jannah Wannaar
```

User pada form:

```text
Hadist:
[ Hadist Adab ▼ ]

Halaman:
[ 25 ]

Pemateri:
[ Ahmad ▼ ]
```

Satu jadwal hanya memiliki satu materi Hadist.

---

# 54. NASEHAT

Nasehat menggunakan master data pemateri/penyampai.

Form:

```text
Penyampai nasehat:
[ Nama ▼ ]
```

Tidak perlu input text bebas jika nama tersedia di master.

---

# 55. FREE ACTIVITY MATERIAL

Jenis keempat:

```text
Materi/Kegiatan Bebas
```

Master default:

```text
Olahraga (Badminton)
ASAD
Keakraban
Musyawarah Terbuka
Door to Door
```

Admin dapat mengelola master tersebut.

Form:

```text
Materi/Kegiatan:
[ Olahraga (Badminton) ▼ ]

Pemateri/Penanggung jawab:
[ Nama ▼ ]
```

---

# 56. SPEAKER MASTER

Admin dapat mengelola daftar pemateri/penyampai.

Field minimal:

```text
id
account_id
name
created_at
updated_at
```

Digunakan untuk:

- Pemateri Quran;
- Pemateri Hadist;
- Penyampai Nasehat;
- Penanggung jawab kegiatan bebas.

Admin dapat:

- tambah;
- edit;
- hapus permanen.

---

# 57. MATERIAL OPTIONALITY

Jika section tidak dicentang:

```text
Tidak ada record material untuk jenis tersebut.
```

Bukan error.

Contoh:

```text
Al-Qur'an = ON
Hadist = OFF
Nasehat = OFF
Kegiatan = OFF
```

valid.

---

# 58. MATERIAL WARNING

Jika attendance disubmit tanpa materi:

Tampilkan warning sebelum submit:

> Materi pengajian belum diisi. Absensi tetap dapat disimpan tanpa materi.

User tetap dapat melanjutkan.

Setelah attendance tersimpan, jika materi belum ada:

```text
[ Lengkapi Materi ]
```

ditampilkan.

---

# 59. MATERIAL EMPTY IS VALID

Materi bersifat opsional.

Tidak mengisi materi bukan error.

Sistem hanya memberikan warning.

---

# 60. HOLIDAY / LIBUR

Libur harus **secara eksplisit dibuat oleh user**.

Tidak ada kondisi:

```text
attendance kosong
→ otomatis dianggap libur
```

Itu salah.

---

# 61. FORM LIBUR

Form libur dapat diakses dari halaman Absensi utama.

Field:

```text
Tanggal
Hari
Alasan
```

Tanggal dipilih user.

Hari otomatis mengikuti tanggal.

---

# 62. HOLIDAY BEHAVIOR

Setelah disimpan:

- occurrence ditandai libur;
- kolom jadwal pada tabel diberi warna abu-abu;
- jadwal diberi garis vertikal/crossed visual;
- attendance tidak dapat diinput;
- tidak dihitung dalam persentase;
- tetap masuk jumlah jadwal libur.

---

# 63. CANCEL HOLIDAY

Admin dapat membatalkan status libur.

Setelah dibatalkan:

```text
HOLIDAY
↓
ACTIVE SCHEDULE
```

Attendance dapat diisi sesuai aturan waktu.

---

# 64. RECURRING SCHEDULE CHANGE

Perubahan jadwal rutin hanya berlaku ke depan.

Histori occurrence lama tidak boleh berubah hanya karena recurring rule berubah.

---

# 65. SPECIAL EVENTS

Selain pengajian rutin, sistem memiliki:

```text
Pengajian Khusus
```

Pengajian khusus berdiri sendiri dari pengajian umum.

Tidak dimasukkan ke jadwal rutin.

---

# 66. SPECIAL EVENT TYPES

Master default:

```text
Pengajian Muda Mudi Desa Sruni 1
FGD Muda Mudi Desa Sruni 1
Pengajian Muda Mudi Daerah Sidoarjo Tengah
```

Admin dapat:

- tambah;
- edit;
- hapus permanen.

---

# 67. SPECIAL EVENT FORM

Field:

```text
Jenis Pengajian
Tanggal
Hari
Jam
Jadwal Umum yang Diliburkan (optional)
```

Jenis pengajian wajib.

Tanggal wajib.

Jam wajib.

---

# 68. AUTO DAY

Ketika tanggal dipilih:

```text
Tanggal:
10 Oktober 2026
```

sistem otomatis:

```text
Hari:
Sabtu
```

User tidak perlu memilih hari secara manual.

---

# 69. SCHEDULE COLLISION WARNING

Jika tanggal pengajian khusus jatuh pada hari yang merupakan jadwal rutin umum:

contoh:

```text
Jumat
```

dan Jumat adalah jadwal rutin.

Tampilkan:

> Peringatan: tanggal ini bertepatan dengan jadwal pengajian umum Jumat.

Jangan otomatis membatalkan jadwal umum.

User harus secara eksplisit memilih apakah jadwal umum tersebut diliburkan.

---

# 70. SPECIAL EVENT HOLIDAY LINK

Form pengajian khusus memiliki pilihan:

```text
Jadwal umum yang diliburkan
```

Optional.

Jika dipilih:

```text
Special Event
+
Holiday pada occurrence umum
```

Jika tidak dipilih:

```text
Special Event
```

saja.

---

# 71. SPECIAL EVENT ABSENCE

Absensi pengajian khusus disimpan pada entitas kegiatan khusus.

Tidak boleh masuk ke attendance rutin.

Contoh:

```text
General attendance
schedule_id = general_123

Special attendance
special_event_id = special_456
```

---

# 72. SPECIAL EVENT MATERIAL

Pengajian khusus menggunakan struktur materi yang sama:

```text
Al-Qur'an
Hadist
Nasehat
Materi/Kegiatan Bebas
```

Section dapat ON/OFF untuk event tersebut.

---

# 73. SPECIAL EVENT LOCK WINDOW

Pengajian khusus menggunakan:

```text
scheduled_at
```

untuk waktu kegiatan.

Batas perubahan:

```text
scheduled_at + global_lock_duration
```

Namun tidak perlu menyimpan konsep `is_locked` pada attendance.

Aturan waktu diterapkan ketika melakukan operasi edit.

---

# 74. ATTENDANCE TABLE

Setelah form di bagian atas, halaman Absensi menampilkan tabel.

Default periode:

```text
bulan berjalan
```

Bukan seluruh histori.

---

# 75. MONTH NAVIGATION

Tabel memiliki navigasi:

```text
← September 2026
Oktober 2026
November 2026 →
```

User dapat melihat bulan sebelumnya/berikutnya.

---

# 76. TABLE STRUCTURE

Contoh:

```text
┌──────────────┬────────┬────────┬────────┬────────┐
│ Nama         │ Rabu 1 │ Jumat 3│ Rabu 8 │ Jumat10│
├──────────────┼────────┼────────┼────────┼────────┤
│ Rizal        │   ✓    │   ✓    │   I    │   A    │
│ Budi         │   A    │   ✓    │   ✓    │   I    │
│ Aisyah       │   ✓    │   A    │   ✓    │   ✓    │
└──────────────┴────────┴────────┴────────┴────────┘
```

---

# 77. TABLE STATUS VISUAL

Gunakan:

```text
✓ = Hadir
I = Izin
A = Alpha
```

Jika libur:

```text
LIBUR
```

dengan visual abu-abu dan garis vertikal sesuai desain.

---

# 78. FROZEN NAME COLUMN

Nama harus:

```text
position: sticky
```

dan tetap terlihat ketika tabel horizontal scroll.

---

# 79. TABLE DATA EDITING

Tabel utama **tidak menjadi editor attendance**.

User tidak mengubah status dengan klik cell.

Perubahan dilakukan melalui:

```text
Form Absensi
atau
Form Edit Kehadiran
```

Ini mengurangi accidental edit.

---

# 80. REPORT PAGE

Laporan berdasarkan:

```text
bulan kalender
```

Contoh:

```text
Januari 2026
Februari 2026
Maret 2026
...
```

Bukan rolling 30 days.

---

# 81. REPORT FILTER

Minimal:

```text
Bulan
Jenis kegiatan
Anggota
```

Jenis kegiatan:

```text
Semua
Pengajian Umum
Pengajian Khusus
```

Default laporan:

```text
Pengajian Umum
```

---

# 82. GENERAL ATTENDANCE SUMMARY

Untuk pengajian umum:

```text
Total jadwal
Jadwal aktif
Jadwal libur
Jadwal belum diisi
```

Contoh:

```text
Total jadwal: 9
Jadwal aktif: 7
Jadwal libur: 2
Belum diisi: 0
```

---

# 83. ATTENDANCE PERCENTAGE

Untuk jadwal aktif yang sudah diisi:

```text
Persentase Hadir =
Jumlah Hadir / Total Jadwal Aktif × 100
```

```text
Persentase Izin =
Jumlah Izin / Total Jadwal Aktif × 100
```

```text
Persentase Alpha =
Jumlah Alpha / Total Jadwal Aktif × 100
```

Izin **tidak dihitung sebagai hadir**.

Artinya:

```text
Hadir + Izin + Alpha = 100%
```

untuk jadwal yang sudah memiliki data.

Jadwal libur tidak masuk denominator.

---

# 84. REPORT MEMBER TABLE

Contoh:

```text
Nama       Hadir   Izin   Alpha   Kehadiran
Rizal        7      1       0       87.5%
Budi         6      0       2       75%
Andi         5      2       1       62.5%
```

---

# 85. FREQUENT ABSENCE

Laporan menampilkan:

### Sering Alpha

```text
Nama
Jumlah Alpha
```

### Sering Izin

```text
Nama
Jumlah Izin
```

Ini hanya statistik, bukan ranking kualitas anggota.

---

# 86. REPORT CHARTS

Minimal:

### Chart per jadwal

Menampilkan:

```text
Hadir
Izin
Alpha
```

untuk setiap jadwal.

### Chart per anggota

Menampilkan:

```text
Hadir
Izin
Alpha
```

untuk setiap anggota.

---

# 87. SPECIAL EVENT REPORT

Pengajian khusus memiliki statistik sendiri.

Contoh:

```text
Pengajian Muda Mudi Desa Sruni 1

Hadir: 20
Izin: 2
Alpha: 3
Persentase hadir: 80%
```

Tidak dicampur dengan laporan pengajian umum.

---

# 88. EXPORT

Export tidak disimpan ke database.

File dibuat saat user meminta export.

Format utama:

```text
.xlsx
```

CSV dapat disediakan sebagai format tambahan jika diperlukan.

---

# 89. EXPORT FILTER

User memilih:

```text
Bulan
Jenis data
```

Data yang dapat diexport:

```text
Kehadiran
Ketidakhadiran/Izin
Alpha
Semua
Materi
Jadwal Libur
```

Export menggunakan bulan kalender.

---

# 90. EXPORT KEHADIRAN

Sheet:

```text
Rekap Kehadiran
```

Kolom:

```text
Nama
Rabu 1
Jumat 3
Rabu 8
...
Jumlah Hadir
Jumlah Izin
Jumlah Alpha
Persentase Hadir
Persentase Izin
Persentase Alpha
Jumlah Jadwal Libur
```

---

# 91. EXPORT STATISTIK JADWAL

Sheet terpisah:

```text
Statistik Jadwal
```

Kolom:

```text
Jadwal
Hadir
Izin
Alpha
```

---

# 92. EXPORT STATISTIK ANGGOTA

Sheet:

```text
Statistik Anggota
```

Kolom:

```text
Nama
Hadir
Izin
Alpha
Persentase Hadir
Persentase Izin
Persentase Alpha
```

---

# 93. EXPORT CHART

Excel harus berisi **native Excel chart**, bukan hanya screenshot.

Minimal:

### Chart per jadwal

Series:

```text
Hadir
Izin
Alpha
```

### Chart per anggota

Series:

```text
Hadir
Izin
Alpha
```

Chart harus dapat diedit di Excel.

---

# 94. EXPORT KETIDAKHADIRAN

Sheet:

```text
Detail Izin
```

Format:

```text
Nama
Jadwal
Jenis Izin
```

Contoh:

```text
Rizal | Rabu 1 | Kuliah
Andi  | Jumat 3 | Sakit
Budi  | Jumat 3 | Kerja
```

---

# 95. IZIN PER KATEGORI

Sheet:

```text
Statistik Izin
```

Contoh:

```text
Nama | Sakit | Sekolah | Kuliah | Kerja | Luar Kota
```

Kemudian total:

```text
Sakit
Sekolah
Kuliah
Kerja
Luar Kota
```

beserta persentase bulanan.

---

# 96. EXPORT ALPHA

Sheet:

```text
Alpha
```

Minimal:

```text
Nama
Jadwal
```

Kemudian statistik:

```text
Per jadwal
Per anggota
```

beserta chart.

---

# 97. EXPORT SEMUA

Satu file XLSX dapat berisi:

```text
01 - Rekap Kehadiran
02 - Detail Kehadiran
03 - Statistik Jadwal
04 - Statistik Anggota
05 - Detail Izin
06 - Statistik Izin
07 - Alpha
08 - Materi
09 - Jadwal Libur
10 - Pengajian Khusus
```

Pengajian umum dan pengajian khusus harus tetap dapat dibedakan.

---

# 98. ADMIN PAGE

Admin berisi konfigurasi dan CRUD.

Section minimal:

```text
Anggota
Jadwal Rutin
Jenis Izin
Jenis Status Anggota
Materi Hadist
Kegiatan Bebas
Pemateri
Jenis Pengajian Khusus
```

---

# 99. ADMIN — ANGGOTA

CRUD:

```text
Tambah
Lihat
Edit
Aktif/nonaktif
```

Form:

```text
Nama lengkap
Nama panggilan
Jenis kelamin
Tanggal lahir
Status
Aktif
```

---

# 100. ADMIN — JADWAL

Admin dapat:

- menambah/mengubah jadwal rutin;
- memilih hari;
- menentukan jam;
- menentukan durasi umum perubahan/edit.

Contoh:

```text
Rabu
19:30

Jumat
19:30

Durasi perubahan:
24 jam
```

---

# 101. ADMIN — JENIS IZIN

Default:

```text
Sakit
Sekolah
Kuliah
Kerja
Di luar kota
```

CRUD permanent.

Delete membutuhkan confirmation.

---

# 102. ADMIN — JENIS STATUS

Default:

```text
Sekolah
Kuliah
Bekerja
```

CRUD permanent.

---

# 103. ADMIN — HADITH

Default:

```text
Hadist Adab
Hadist Kitabush Shalah
Hadist Jannah Wannaar
```

CRUD permanent.

Form:

```text
Nama Hadist
```

---

# 104. ADMIN — KEGIATAN BEBAS

Default:

```text
Olahraga (Badminton)
ASAD
Keakraban
Musyawarah Terbuka
Door to Door
```

CRUD permanent.

---

# 105. ADMIN — PEMATERI

CRUD:

```text
Nama
```

Digunakan sebagai dropdown pada:

- Quran;
- Hadist;
- Nasehat;
- kegiatan bebas.

---

# 106. ADMIN — JENIS PENGAJIAN KHUSUS

Default:

```text
Pengajian Muda Mudi Desa Sruni 1
FGD Muda Mudi Desa Sruni 1
Pengajian Muda Mudi Daerah Sidoarjo Tengah
```

CRUD permanent.

---

# 107. DELETE MASTER DATA

Master data seperti:

- jenis izin;
- jenis status;
- Hadist;
- kegiatan;
- pemateri;
- jenis pengajian khusus;

boleh dihapus permanen.

Sebelum delete:

```text
Modal confirmation
```

Contoh:

> Hapus "Hadist Adab" secara permanen? Data yang sudah menggunakan item ini mungkin tidak dapat lagi menggunakan referensi tersebut.

Button:

```text
Batal
Hapus Permanen
```

Implementasi harus menjaga histori menggunakan snapshot/reference yang aman.

---

# 108. AUDIT LOG

Simpan perubahan penting.

Minimal:

```text
id
account_id
actor_user_id
action
entity_type
entity_id
old_data
new_data
created_at
```

Contoh:

```text
Attendance
Andi
Alpha → Hadir
```

Audit log digunakan terutama untuk perubahan attendance dan data penting.

---

# 109. ATTENDANCE DELETE

Attendance normal tidak memiliki tombol delete.

Jika terjadi kesalahan:

```text
Edit
```

bukan delete.

Tujuan:

- histori tetap utuh;
- perubahan dapat diaudit;
- tidak ada data tiba-tiba hilang.

---

# 110. DATA SNAPSHOT

Data historis tidak boleh berubah hanya karena master data diedit.

Contoh:

Jika pemateri:

```text
Ahmad
```

kemudian master diubah menjadi:

```text
Ahmad Fauzi
```

materi lama tetap menyimpan snapshot:

```text
Ahmad
```

Hal yang sama berlaku untuk:

- nama anggota;
- jenis izin;
- jenis materi;
- nama pemateri;
- jenis kegiatan.

Namun laporan saat ini tetap dapat menggunakan data master terbaru jika sesuai kebutuhan.

---

# 111. DATA INTEGRITY

Semua foreign key harus memiliki constraint yang tepat.

Jangan mengandalkan validasi frontend saja.

Validasi harus dilakukan:

```text
Frontend
+
Database
```

---

# 112. RLS

Setiap tabel yang menyimpan data user harus dilindungi RLS.

Rule dasar:

```text
auth user
→ hanya dapat mengakses account_id miliknya
```

Guest:

```text
guest account
→ hanya guest account sendiri
```

Real:

```text
real account
→ hanya real account sendiri
```

Tidak boleh:

```text
Guest → Real
Real A → Real B
```

---

# 113. DATABASE AUTHORITY

Server/database adalah sumber kebenaran untuk:

- waktu;
- permission;
- account isolation;
- jadwal;
- attendance;
- laporan;
- guest expiration.

Frontend hanya bertugas memberikan UX.

Jangan membuat security rule hanya di React.

---

# 114. DRAFT DATA

Draft form boleh disimpan di IndexedDB.

Draft bukan attendance record sampai user menekan:

```text
Simpan
```

Draft tidak masuk laporan.

Draft tidak masuk database production kecuali mekanisme sync draft memang diperlukan.

---

# 115. SYNC QUEUE

Setiap perubahan offline masuk queue.

Contoh:

```text
operation:
CREATE_ATTENDANCE

account_id
schedule_id
payload
created_at
retry_count
status
```

Status:

```text
PENDING
SYNCING
SYNCED
FAILED
CONFLICT
```

Jika gagal:

- jangan hilangkan data;
- tampilkan warning;
- retry ketika koneksi tersedia.

---

# 116. CONFLICT HANDLING

Jika data server sudah berubah sejak data offline dibuat:

Jangan silent overwrite.

Tampilkan conflict.

User harus dapat memilih:

```text
Gunakan Data Server
```

atau:

```text
Gunakan Perubahan Lokal
```

Untuk data sensitif/historis, server-side validation tetap wajib.

---

# 117. OFFLINE REPORT

Jika offline, laporan dapat menggunakan data yang sudah tersimpan di IndexedDB.

Tampilkan:

> Data laporan terakhir diperbarui saat [timestamp].

Agar user tahu bahwa data mungkin belum terbaru.

---

# 118. OFFLINE EXPORT

Export boleh dilakukan offline menggunakan data lokal yang tersedia.

Jika data belum tersinkron:

Tampilkan warning:

> Export dibuat dari data lokal yang belum sepenuhnya tersinkron.

---

# 119. ERROR HANDLING

Error harus manusiawi.

Jangan menampilkan:

```text
PostgrestError 23505
```

kepada user.

Gunakan:

> Data gagal disimpan. Periksa koneksi internet dan coba lagi.

Untuk offline:

> Anda sedang offline. Data telah disimpan di perangkat dan akan dikirim saat koneksi kembali.

---

# 120. SUCCESS FEEDBACK

Setelah submit:

> Absensi Jumat, 3 Oktober berhasil disimpan.

Setelah edit:

> Perubahan absensi berhasil disimpan.

Setelah materi:

> Materi berhasil disimpan.

Setelah sync:

> Semua perubahan berhasil disinkronkan.

---

# 121. RESPONSIVE DESIGN

Prioritas:

```text
Mobile
↓
Tablet
↓
Desktop
```

Aplikasi harus nyaman pada:

```text
360px+
```

Tabel boleh horizontal scroll.

Form harus mudah disentuh.

Target touch area minimal sekitar:

```text
44px
```

untuk tombol/checkbox utama.

---

# 122. TABLE MOBILE UX

Tabel absensi merupakan bagian yang paling membutuhkan horizontal scrolling.

Requirement:

- nama sticky;
- header jadwal tetap mudah dibaca;
- cell tidak terlalu kecil;
- scroll horizontal halus;
- scroll vertical tetap memungkinkan;
- bulan hanya menampilkan jadwal bulan tersebut.

---

# 123. FORM MOBILE UX

Form harus menjadi bagian paling mudah digunakan.

Prioritas:

```text
Pilih jadwal
↓
lihat warning/status
↓
isi anggota
↓
isi materi
↓
simpan
```

Jangan membuat user membuka banyak modal.

---

# 124. MODAL USAGE

Jangan menggunakan modal sebagai primary attendance input.

Form absensi dan materi berada langsung di halaman.

Modal hanya digunakan untuk:

- confirmation;
- delete confirmation;
- warning;
- conflict;
- informasi penting.

---

# 125. DATE HANDLING

Semua tanggal harus menggunakan format internal yang konsisten.

Display Indonesia:

```text
Jumat, 3 Oktober 2026
```

Database gunakan format date/timestamp standar PostgreSQL.

Timezone:

```text
Asia/Jakarta
```

---

# 126. SCHEDULE GENERATION

Recurring schedule harus menghasilkan occurrence yang diperlukan.

Jangan membuat ribuan jadwal jauh ke masa depan tanpa alasan.

Sistem dapat generate occurrence berdasarkan periode yang diperlukan.

---

# 127. CRON

Cron dapat digunakan untuk:

1. generate recurring schedule;
2. cleanup guest >24 jam;
3. maintenance;
4. pekerjaan sinkronisasi server-side jika diperlukan.

Jangan menggunakan cron hanya untuk melakukan ping database secara agresif dengan tujuan menghindari pause.

---

# 128. SCHEDULE GENERATION IDENTITY

Occurrence harus memiliki identity unik.

Contoh:

```text
recurring_schedule_id
occurrence_date
```

harus tidak menghasilkan duplicate occurrence.

Gunakan unique constraint.

---

# 129. HOLIDAY CONSTRAINT

Satu occurrence umum tidak boleh memiliki lebih dari satu holiday record aktif.

---

# 130. SPECIAL EVENT COLLISION

Special event boleh jatuh pada hari yang sama dengan recurring schedule.

Sistem hanya memberi warning.

Tidak otomatis melakukan perubahan.

Jika user memilih:

```text
jadwal umum yang diliburkan
```

baru occurrence umum diberi holiday.

---

# 131. REPORTING RULE — HOLIDAY

Holiday:

- tidak dihitung sebagai hadir;
- tidak dihitung sebagai izin;
- tidak dihitung sebagai alpha;
- tidak masuk denominator;
- tetap dihitung sebagai jumlah libur.

---

# 132. REPORTING RULE — NO ATTENDANCE

Jika occurrence aktif belum memiliki attendance submission:

```text
belum diisi
```

Jangan:

- dianggap alpha;
- dianggap libur;
- masuk denominator persentase.

---

# 133. REPORTING RULE — SUBMITTED ATTENDANCE

Setelah form attendance disimpan:

Semua anggota yang eligible mendapatkan:

```text
PRESENT
PERMITTED
ALPHA
```

Maka occurrence dianggap:

```text
SUBMITTED
```

dan masuk perhitungan.

---

# 134. REPORTING RULE — MEMBER ELIGIBILITY

Anggota dihitung jika:

```text
active
AND
joined_at <= occurrence_date
```

Jika anggota sudah tidak aktif sebelum occurrence:

```text
tidak dihitung.
```

---

# 135. SPECIAL EVENT REPORTING

Special event memiliki:

- jumlah hadir;
- jumlah izin;
- jumlah alpha;
- persentase;
- chart;
- tabel anggota.

Namun statistiknya terpisah dari pengajian umum.

---

# 136. ADMIN DATA VALIDATION

Contoh:

### Member

Nama lengkap wajib.

Nickname wajib.

Gender wajib.

Tanggal lahir valid.

Status wajib.

### Schedule

Hari wajib.

Jam wajib.

### Hadith

Nama wajib.

### Speaker

Nama wajib.

### Special event

Jenis wajib.

Tanggal wajib.

Jam wajib.

---

# 137. FORM MATERIAL VALIDATION

### Quran

Nomor surat:

```text
1–114
```

Ayat wajib jika Quran diaktifkan.

### Hadith

Hadith wajib.

Halaman wajib.

### Nasehat

Penyampai wajib.

### Free activity

Kegiatan wajib.

Penanggung jawab/pemateri wajib jika field tersebut digunakan.

---

# 138. SECURITY

Jangan menyimpan:

- service role key;
- secret key;
- Supabase service key;

di frontend.

Frontend hanya menggunakan public/anon key yang sesuai.

Operasi privileged menggunakan:

- RLS;
- Edge Function/server-side logic;
- cron;
- server environment.

---

# 139. NO PUBLIC REGISTRATION

Tidak boleh ada:

```text
/registration
```

atau signup publik.

Login hanya untuk akun yang telah tersedia.

Guest creation merupakan flow khusus terpisah.

---

# 140. ACCOUNT DELETION

Guest dapat dihapus otomatis.

Akun asli tidak boleh terhapus melalui flow guest.

Permanent deletion account asli bukan bagian dari MVP.

---

# 141. PERFORMANCE

Karena aplikasi digunakan di HP:

- lazy load halaman laporan;
- query hanya bulan yang sedang dilihat;
- jangan mengambil seluruh histori attendance sekaligus;
- gunakan pagination/query range jika diperlukan;
- index kolom yang sering difilter;
- gunakan aggregate query untuk laporan;
- jangan mengirim seluruh database ke frontend.

---

# 142. DATABASE INDEX

Minimal pertimbangkan index:

```text
accounts.auth_user_id
members.account_id
members.active
members.joined_at
recurring_schedules.account_id
schedule_occurrences.account_id
schedule_occurrences.occurrence_date
attendance.account_id
attendance.schedule_occurrence_id
attendance.member_id
special_events.account_id
special_events.event_date
materials.account_id
audit_logs.account_id
```

Unique constraints harus digunakan untuk mencegah duplicate attendance.

---

# 143. ATTENDANCE UNIQUE CONSTRAINT

Satu anggota hanya boleh memiliki satu attendance record untuk satu occurrence:

```text
(account_id, occurrence_id, member_id)
```

unique.

---

# 144. MATERIAL RELATION

Material harus terkait dengan kegiatan:

```text
schedule_occurrence_id
```

atau:

```text
special_event_id
```

Jangan hanya menyimpan tanggal.

---

# 145. AUDIT LOG DATA

Old/new values dapat disimpan dalam JSONB.

Contoh:

```json
{
  "status": "ALPHA"
}
```

menjadi:

```json
{
  "status": "PRESENT"
}
```

---

# 146. DESIGN DIRECTION

PRD ini mendefinisikan functionality.

Design system harus dibuat terpisah, tetapi implementasi wajib mengikuti prinsip:

- mobile-first;
- clean;
- sederhana;
- cepat;
- tidak terlalu banyak card;
- informasi penting terlihat jelas;
- bottom navigation;
- tabel menjadi pusat halaman Absensi;
- warna status harus konsisten.

Design detail seperti:

- warna;
- font;
- border radius;
- shadow;
- icon;
- spacing;
- visual style;

akan diberikan sebagai design specification terpisah.

---

# 147. ACCESSIBILITY

Pastikan:

- checkbox memiliki label;
- dropdown memiliki label;
- button dapat digunakan keyboard;
- contrast memadai;
- focus state jelas;
- error tidak hanya dibedakan dengan warna;
- touch target cukup besar.

---

# 148. ACCEPTANCE CRITERIA — ABSENSI

Feature dianggap selesai jika:

- [ ] user dapat membuka halaman Absensi;
- [ ] form langsung terlihat;
- [ ] jadwal otomatis dipilih;
- [ ] warning hari berbeda muncul;
- [ ] warning jadwal sebelumnya belum diisi muncul;
- [ ] form tidak dapat dikirim sebelum window 30 menit;
- [ ] checkbox checked = hadir;
- [ ] checkbox unchecked + no permission = alpha;
- [ ] permission selected = izin;
- [ ] checkbox dan permission mutual exclusive;
- [ ] anggota terbagi laki-laki/perempuan;
- [ ] nickname digunakan;
- [ ] reset tersedia;
- [ ] draft bertahan setelah browser ditutup;
- [ ] submit membuat attendance;
- [ ] setelah submit form menghilang;
- [ ] tabel otomatis refresh;
- [ ] edit attendance tersedia.

---

# 149. ACCEPTANCE CRITERIA — MATERIAL

- [ ] Quran dapat diaktifkan/nonaktifkan per jadwal;
- [ ] Hadist dapat diaktifkan/nonaktifkan per jadwal;
- [ ] Nasehat dapat diaktifkan/nonaktifkan per jadwal;
- [ ] kegiatan bebas dapat diaktifkan/nonaktifkan per jadwal;
- [ ] Quran meminta nomor surat;
- [ ] nama surat muncul otomatis;
- [ ] Quran meminta ayat;
- [ ] Hadist menggunakan master database;
- [ ] Hadist meminta halaman;
- [ ] Quran/Hadist memiliki pemateri;
- [ ] Nasehat menggunakan master penyampai;
- [ ] kegiatan bebas menggunakan master;
- [ ] materi kosong hanya menghasilkan warning;
- [ ] materi dapat diedit.

---

# 150. ACCEPTANCE CRITERIA — HOLIDAY

- [ ] holiday harus diinput eksplisit;
- [ ] tidak ada attendance otomatis jika holiday;
- [ ] holiday tidak masuk denominator;
- [ ] holiday tetap dihitung;
- [ ] tabel menampilkan holiday secara visual;
- [ ] holiday dapat dibatalkan;
- [ ] alasan holiday tersimpan.

---

# 151. ACCEPTANCE CRITERIA — SPECIAL EVENT

- [ ] jenis event menggunakan master;
- [ ] tanggal menentukan hari otomatis;
- [ ] jam wajib;
- [ ] collision dengan jadwal umum menghasilkan warning;
- [ ] user dapat memilih jadwal umum yang diliburkan;
- [ ] pilihan tersebut optional;
- [ ] special event tidak masuk attendance umum;
- [ ] special event memiliki attendance sendiri;
- [ ] special event memiliki report sendiri;
- [ ] special event memiliki material;
- [ ] special event mengikuti duration configuration umum.

---

# 152. ACCEPTANCE CRITERIA — GUEST

- [ ] guest dapat dibuat;
- [ ] guest mendapatkan dataset dummy;
- [ ] guest tidak dapat melihat data real;
- [ ] guest dapat mengubah data miliknya;
- [ ] guest dapat menambah data;
- [ ] guest logout menghapus data guest;
- [ ] guest >24 jam dihapus cron;
- [ ] expiration berdasarkan created_at;
- [ ] guest tidak mempengaruhi database real.

---

# 153. ACCEPTANCE CRITERIA — OFFLINE

- [ ] aplikasi dapat dibuka setelah asset dicache;
- [ ] status offline terlihat;
- [ ] draft tetap tersedia;
- [ ] data dapat disimpan lokal;
- [ ] sync queue tersedia;
- [ ] ketika online, data disinkronkan;
- [ ] Background Sync digunakan jika tersedia;
- [ ] jika tidak tersedia, sync dilakukan ketika aplikasi dibuka;
- [ ] failed sync tidak menghapus data;
- [ ] conflict dapat dideteksi;
- [ ] user mendapat feedback sync.

---

# 154. ACCEPTANCE CRITERIA — REPORT

- [ ] laporan berdasarkan bulan kalender;
- [ ] jadwal libur tidak dihitung;
- [ ] jadwal belum diisi tidak dihitung;
- [ ] attendance submitted dihitung;
- [ ] hadir/izin/alpha memiliki persentase;
- [ ] total jadwal terlihat;
- [ ] jadwal aktif terlihat;
- [ ] jumlah libur terlihat;
- [ ] jadwal belum diisi terlihat;
- [ ] tabel anggota tersedia;
- [ ] chart per jadwal tersedia;
- [ ] chart per anggota tersedia;
- [ ] frequent alpha tersedia;
- [ ] frequent izin tersedia;
- [ ] special event terpisah.

---

# 155. ACCEPTANCE CRITERIA — EXPORT

- [ ] user dapat memilih bulan;
- [ ] user dapat memilih jenis data;
- [ ] XLSX dibuat tanpa menyimpan file ke database;
- [ ] rekap attendance tersedia;
- [ ] detail attendance tersedia;
- [ ] statistik jadwal tersedia;
- [ ] statistik anggota tersedia;
- [ ] detail izin tersedia;
- [ ] statistik izin tersedia;
- [ ] alpha tersedia;
- [ ] materi tersedia;
- [ ] holiday tersedia;
- [ ] special event tersedia;
- [ ] chart native Excel tersedia;
- [ ] pengajian umum dan khusus terpisah.

---

# 156. DEVELOPMENT ORDER

AI coding agent harus mengimplementasikan secara bertahap.

## Phase 1 — Foundation

- React/Vite
- CSS architecture
- Supabase
- Auth
- RLS
- account isolation
- basic routing
- PWA foundation

## Phase 2 — Database

Implement:

- accounts
- members
- statuses
- schedules
- occurrences
- attendance
- absence types
- materials
- speakers
- special events
- master tables
- audit log

## Phase 3 — Admin

Implement:

- member CRUD
- recurring schedule CRUD
- status CRUD
- permission CRUD
- Hadith CRUD
- free activity CRUD
- speaker CRUD
- special event type CRUD

## Phase 4 — Attendance

Implement:

- schedule selection
- timing rule
- warning
- attendance form
- material form
- draft
- submit
- edit

## Phase 5 — Holiday

Implement:

- holiday form
- cancel holiday
- holiday display

## Phase 6 — Special Event

Implement:

- special event form
- collision detection
- optional linked holiday
- special attendance
- special materials

## Phase 7 — Report

Implement:

- monthly report
- statistics
- charts
- frequent absence

## Phase 8 — Export

Implement:

- XLSX
- multiple sheets
- native Excel charts

## Phase 9 — Offline

Implement:

- IndexedDB
- service worker
- sync queue
- retry
- conflict detection
- background sync

## Phase 10 — Guest

Implement:

- guest creation
- seed
- isolation
- expiration
- logout cleanup

## Phase 11 — QA

Test:

- mobile;
- desktop;
- offline;
- online;
- guest;
- real account;
- schedule edge cases;
- holiday;
- special events;
- report calculations;
- export.

---

# 157. IMPORTANT IMPLEMENTATION RULES FOR AI AGENT

AI coding agent **MUST NOT**:

1. menggunakan Tailwind;
2. membuat halaman Dashboard;
3. membuat role Admin/User;
4. membuat GPS attendance;
5. membuat face recognition;
6. membuat employee/payroll features;
7. menganggap attendance kosong sebagai alpha;
8. menganggap attendance kosong sebagai libur;
9. membuat libur otomatis;
10. membuat special event masuk ke attendance umum;
11. membuat tabel utama menjadi editor attendance;
12. menggunakan localStorage sebagai database offline utama;
13. mengekspos Supabase service role key;
14. mengandalkan frontend untuk security;
15. menghapus data real account ketika membersihkan guest;
16. mencampurkan guest data dengan real data;
17. menyimpan file export ke database/storage secara permanen;
18. mengubah histori hanya karena master data berubah;
19. menghapus attendance sebagai cara normal memperbaiki kesalahan;
20. menganggap izin sebagai hadir;
21. menghitung jadwal libur sebagai denominator;
22. menghitung jadwal yang belum diisi sebagai denominator;
23. menambahkan fitur di luar scope tanpa alasan teknis yang kuat.

---

# 158. DEFINITION OF DONE

Produk dianggap selesai jika seluruh flow berikut bekerja:

```text
Login
 ↓
Absensi
 ↓
Sistem memilih jadwal yang benar
 ↓
User melihat warning/status
 ↓
User mengisi attendance
 ↓
User mengisi material jika ada
 ↓
User submit
 ↓
Database tersimpan
 ↓
Tabel refresh
 ↓
Laporan berubah
 ↓
Export dapat dibuat
```

Untuk offline:

```text
Offline
 ↓
Isi form
 ↓
IndexedDB
 ↓
Sync Queue
 ↓
Online kembali
 ↓
Sync
 ↓
Supabase
 ↓
Database updated
```

Untuk guest:

```text
Masuk sebagai Tamu
 ↓
Guest account dibuat
 ↓
Seed dummy
 ↓
User bebas mencoba
 ↓
Logout
 ↓
Guest data dihapus
```

Atau:

```text
Guest dibuat
 ↓
24 jam
 ↓
Cron
 ↓
Guest data dihapus
```

Untuk pengajian khusus:

```text
Pilih jenis
 ↓
Pilih tanggal
 ↓
Hari otomatis
 ↓
Collision warning jika bertepatan
 ↓
Optional: pilih jadwal umum yang diliburkan
 ↓
Set jam
 ↓
Absensi khusus
 ↓
Laporan khusus
```

---

# 159. FINAL PRODUCT STRUCTURE

Secara konseptual aplikasi terdiri dari:

```text
ABSENSI GEMURUNG 2
│
├── Authentication
│   ├── Login
│   └── Guest
│
├── Absensi
│   ├── Form Absensi
│   ├── Form Materi
│   ├── Form Libur
│   ├── Form Pengajian Khusus
│   ├── Edit Absensi
│   └── Tabel Absensi
│
├── Laporan
│   ├── Pengajian Umum
│   ├── Pengajian Khusus
│   ├── Statistik
│   ├── Chart
│   └── Export
│
└── Admin
    ├── Anggota
    ├── Jadwal Rutin
    ├── Jenis Izin
    ├── Status Anggota
    ├── Materi Hadist
    ├── Kegiatan Bebas
    ├── Pemateri
    └── Jenis Pengajian Khusus
```

---

# 160. PRIMARY USER EXPERIENCE

Prinsip utama aplikasi:

> **Buka → lihat form → isi → simpan.**

User tidak boleh dipaksa:

```text
Login
→ Dashboard
→ Pilih menu
→ Pilih jadwal
→ Buka halaman
→ Buka modal
→ Isi
```

Sebaliknya:

```text
Login
↓
Absensi
↓
Form sudah tersedia
↓
Isi
↓
Simpan
```

Tabel berada di bawah form sebagai histori dan monitoring.

---

# 161. FINAL BUSINESS RULE SUMMARY

1. Pengajian umum memiliki recurring weekly schedule.
2. Hari dan jam tiap recurring schedule dapat berbeda.
3. Duration perubahan/edit adalah konfigurasi global.
4. Timezone resmi adalah `Asia/Jakarta`.
5. Absensi umum tersedia mulai 30 menit sebelum jadwal.
6. Sebelum window tersebut, form tidak dapat disubmit.
7. Hari yang bukan hari pengajian menampilkan informasi jadwal berikutnya.
8. Jadwal sebelumnya yang belum diisi menampilkan warning.
9. Attendance kosong bukan Alpha.
10. Attendance kosong bukan Libur.
11. Attendance baru dianggap ada setelah form disubmit.
12. Checkbox checked = Hadir.
13. Checkbox unchecked tanpa izin = Alpha.
14. Dropdown izin terisi = Izin.
15. Checkbox dan dropdown izin mutually exclusive.
16. Anggota aktif dan sudah bergabung pada tanggal tersebut masuk perhitungan.
17. Anggota nonaktif tidak masuk attendance baru.
18. Histori anggota tetap dipertahankan.
19. Holiday harus dibuat eksplisit.
20. Holiday tidak masuk denominator.
21. Holiday tetap masuk jumlah libur.
22. Materi bersifat optional.
23. Quran/Hadist/Nasehat/Kegiatan dapat ON/OFF per kegiatan.
24. Master material dikelola Admin.
25. Special event berdiri sendiri dari general attendance.
26. Special event dapat secara optional meliburkan general schedule.
27. Special event memiliki attendance dan report sendiri.
28. Guest memiliki database scope sendiri.
29. Guest data dihapus saat logout.
30. Guest data dihapus otomatis setelah 24 jam.
31. Guest tidak boleh mengakses real account.
32. Offline data disimpan di IndexedDB.
33. Offline changes masuk sync queue.
34. Online kembali melakukan synchronization.
35. Server menjadi authority ketika terjadi conflict.
36. Report menggunakan bulan kalender.
37. Export tidak disimpan ke database.
38. Attendance normal diperbaiki melalui Edit, bukan delete.
39. Master data dapat dihapus permanen dengan confirmation.
40. UI utama mobile-first.
41. Styling menggunakan CSS biasa.
42. Deployment menggunakan Vercel.
43. Backend menggunakan Supabase.
44. PWA wajib digunakan untuk offline capability.
45. Tidak ada Dashboard page.
46. Navigation utama: Absensi, Laporan, Admin.
47. Halaman Absensi adalah halaman default.
48. Tabel nama menggunakan sticky/frozen first column.
49. Form menggunakan nickname.
50. Tabel dapat menampilkan full name atau nickname sesuai toggle yang tersedia pada tabel.
```

