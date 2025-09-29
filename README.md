
# ☁️ Otomatisasi Deployment Nextcloud dengan Ansible

Proyek ini mengimplementasikan otomatisasi deployment Nextcloud menggunakan Ansible. Tujuan utamanya adalah membangun sebuah **infrastruktur *private cloud storage*** yang terdistribusi, aman, dan efisien dengan fitur-fitur seperti manajemen DNS internal dan sertifikat SSL/TLS.

---

### 📝 Deskripsi Proyek

Implementasi sistem *private cloud storage* berbasis Nextcloud dengan automasi Ansible di lingkungan terdistribusi. Proyek ini dirancang untuk memenuhi kebutuhan penyimpanan data terpusat yang aman, efisien, dan terkontrol.

#### Fitur Utama

-   **🤖 Otomatisasi Penuh**: Seluruh proses deployment diotomatisasi menggunakan Ansible Playbook.
-   **🔒 Keamanan Terjamin**: Menggunakan sertifikat SSL/TLS dari Let's Encrypt untuk komunikasi yang aman.
-   **✨ Infrastruktur Fleksibel**: Dirancang dengan arsitektur terpisah untuk DNS, Database, Web, dan *Reverse Proxy*.
-   **👥 Manajemen Pengguna**: Mendukung multi-level *user* (Admin dan Member) dengan kuota penyimpanan.
-   **📡 DNS Internal**: Menggunakan domain lokal `cloud.reihan.lan.local` untuk akses internal.

---

### 🏛️ Arsitektur Sistem

#### Topologi Jaringan

Meskipun dirancang untuk arsitektur terdistribusi, beberapa layanan (DNS dan SSL) digabungkan pada satu *host* karena keterbatasan sumber daya fisik.

```

\+-------------+         +-------------+         +-----------------+
|   Client    |---------|   Switch    |---------|   DNS & SSL     |
| 192.168.1.1 |         |             |         |  192.168.1.11   |
\+-------------+         +-------------+         +-----------------+
|
\+-------------+-----------------+
|             |                 |
\+-----------------+   +-----------------+   +-----------------+
| Ansible Control |   |   Webserver     |   |   Database      |
|  192.168.1.10   |   |  192.168.1.12   |   |  192.168.1.13   |
\+-----------------+   +-----------------+   +-----------------+

```

#### Spesifikasi Server

| Hostname          | IP Address    | Peran               | Komponen                               |
|-------------------|---------------|---------------------|----------------------------------------|
| `Client`          | `192.168.1.1` | Akses Pengguna      | Web Browser                            |
| `Ansible Control` | `192.168.1.10`| Control Node        | Ansible, SSH                           |
| `DNS & SSL`       | `192.168.1.11`| DNS Server          | BIND9, Certbot                         |
| `Webserver`       | `192.168.1.12`| Aplikasi            | Nextcloud, Nginx, PHP-FPM              |
| `Database`        | `192.168.1.13`| Basis Data          | MariaDB                                |

---

### 💻 Teknologi yang Digunakan

-   **⚙️ Ansible**: Untuk otomatisasi dan *Infrastructure as Code*.
-   **☁️ Nextcloud**: Platform *private cloud storage* utama.
-   **🌐 Nginx**: Berfungsi sebagai *web server* dan *reverse proxy*.
-   **🐘 MariaDB**: Sistem manajemen basis data.
-   **📡 BIND9**: Mengelola DNS internal untuk resolusi nama domain lokal.
-   **🔐 Let's Encrypt & Certbot**: Menyediakan sertifikat SSL/TLS gratis dan mengotomatisasi prosesnya.

---

### 📂 Struktur Proyek

```

projek-nextcloud/
├── hosts                       \# Inventory file untuk Ansible
├── site.yml                    \# Main playbook untuk deployment
└── roles/                      \# Direktori untuk peran Ansible
├── common/
│   └── tasks/main.yml      \# Tugas umum untuk semua host
├── db/
│   ├── tasks/main.yml      \# Menginstal dan mengkonfigurasi MariaDB
│   └── handlers/main.yml
├── dns/
│   ├── tasks/main.yml      \# Menginstal dan mengkonfigurasi BIND9
│   ├── handlers/main.yml
│   └── templates/
│       ├── db.cloud.reihan.lan.local.j2
│       └── ...
├── reverse\_proxy/
│   ├── tasks/main.yml      \# Menginstal Nginx dan mengkonfigurasi reverse proxy
│   ├── handlers/main.yml
│   └── templates/nginx-reverse-proxy.conf.j2
├── user\_management/
│   ├── tasks/main.yml      \# Mengelola user di sistem
│   └── handlers/main.yml
└── webserver/
├── tasks/main.yml      \# Menginstal Nextcloud, Nginx, dan PHP-FPM
├── handlers/main.yml
└── templates/
├── nextcloud.conf.j2
└── www.conf.j2

````

---

### 🚀 Instalasi & Deployment

#### Prasyarat

-   Sistem operasi **Debian 12** pada semua server.
-   Koneksi internet untuk mengunduh paket.
-   SSH *key* sudah diatur antara *control node* (Ansible) dan semua *target hosts*.

#### Langkah Deployment

1.  **Klon Repositori:**
    ```bash
    git clone [https://github.com/reisukacommit/projek-nextcloud.git](https://github.com/reisukacommit/projek-nextcloud.git)
    cd projek-nextcloud
    ```

2.  **Jalankan Playbook:**
    ```bash
    ansible-playbook -i hosts site.yml
    ```
    *Playbook ini akan menjalankan semua peran secara otomatis untuk menginstal dan mengkonfigurasi seluruh sistem.*

3.  **Akses Aplikasi:**
    -   Buka peramban: `https://cloud.reihan.lan.local`
    -   Login dengan kredensial: `admin` / `admin123`

---

### 👥 Manajemen Pengguna

#### Level Akses

-   **Admin**: Akses penuh, dapat mengelola pengguna dan konfigurasi sistem.
-   **Member**: Akses dasar, dapat mengunggah dan mengunduh berkas dengan kuota 5GB.

#### Menambah Pengguna Baru

1.  Login sebagai pengguna admin.
2.  Pilih "User Management".
3.  Pilih "New Account", lalu atur nama pengguna dan kata sandi.
4.  Pastikan pengguna baru ditempatkan di grup "member".

---

### 🔧 Pemecahan Masalah (*Troubleshooting*)

-   **Koneksi SSH Gagal**: Pastikan SSH *key* telah disalin dengan benar ke setiap *host* dengan `ssh-copy-id`.
-   **SSL Certificate Warning**: Domain `.local` tidak diakui oleh peramban. Anda dapat melewati peringatan ini dengan memilih "Advanced" → "Proceed to `cloud.reihan.lan.local`".
-   **Izin Akses Nextcloud**: Jika ada masalah izin, jalankan perintah berikut untuk memperbaiki kepemilikan direktori: `chown -R www-data:www-data /var/www/nextcloud`.

---

### 📈 Monitoring & Pemeliharaan

#### Status Layanan
```bash
# Cek status semua layanan
systemctl status nginx
systemctl status mariadb
systemctl status bind9
systemctl status php8.2-fpm
````

#### File Log

```bash
# Log Nextcloud
tail -f /var/www/nextcloud/data/nextcloud.log

# Log Nginx
tail -f /var/log/nginx/error.log
```

-----

### 📝 Dokumentasi & Lisensi

#### Pengembang

**Reihan Maulana**

  * SMK Negeri 1 Cijulang
  * Jurusan Teknik Jaringan Komputer dan Telekomunikasi
  * Email: `reihan.maulana1241@smk.belajar.id`
  * GitHub: [reisukacommit](https://github.com/reisukacommit)

#### Lisensi

Proyek ini dikembangkan untuk tujuan edukasi.
