<h1 align="center"><img src="./logo.svg" alt="The Keila logo is a stylized elephant">Keila - An Open Source Newsletter Tool</h1>

<details>
  <summary>Anggota Kelompok</summary>
  <ul>
    <li>G6401221021 - Maulana Ahmad Baihaqi</li>
    <li>G6401221023 - Ahmad Subhan Daryhadi</li>
    <li>G6401221027 - Dicky Anugrah</li>
    <li>G6401221029 - Sri Arini Ismayasari</li>
    <li>G6401221054 - Raihana Luthfia</li>
  </ul>
</details>

<details>
  <summary>Daftar Isi</summary>
  <ol>
    <li><a href="#sekilas-tentang">Sekilas Tentang</a></li>
    <li><a href="#instalasi">Instalasi</a>
      <ul>
        <li><a href="#siapkan-akun-untuk-smtp">Siapkan Akun untuk SMTP</a></li>
        <li><a href="siapkan-akun-untuk-captcha-opsional">Siapkan Akun untuk Captcha</a></li>
        <li><a href="#proses-instalasi">Proses Instalasi</a></li>
      </ul>
    </li>
  </ol>
</details>

## Sekilas Tentang
Keila adalah alternatif Open Source untuk alat newsletter seperti Mailchimp atau Sendinblue.

Dengan Keila, kita dapat dengan mudah mengirim kampanye newsletter dan membuat formulir pendaftaran.

Untuk newsletter yang lebih kecil, kita dapat menggunakan kotak masuk email kita sendiri untuk mengirim kampanye. Untuk proyek newsletter yang lebih besar, Keila mendukung AWS SES, Sendgrid, Mailgun, dan Postmark selain dari SMTP.

![Screenshot of the Keila campaign editor showing the WYSIWYG editor and the default template](https://www.keila.io/_astro/keila-2024-05-01.BUp8L2VZ.png)

## Instalasi

### Siapkan Akun untuk SMTP
1.  Gunakan email IPB
2.  Masuk ke pengaturan google account
3.  Pada tab security, pilih 2-Step Verification
4.  Klik Turn on 2-Step Verification
5.  Jika sudah berhasil mengaktifkan 2-Step Verification, akan muncul App passwords pada bagian bawah
6.  Jika App passwords tidak muncul, bisa dicari menggunakan pencarian menu yang disediakan
7.  Jika masih tidak muncul, pastikan kembali agar menggunakan email IPB dan sudah mengaktifkan 2-Step Verification
8.  Buka menu App passwords dan ketikkan App name
9.  Tekan Create dan salin kode yang muncul

    Kode yang muncul akan diletakkan di `MAILER_SMTP_HOST` pada file konfigurasi `docker-compose.yml`. Aplikasi ini memerlukan email untuk mengirim kode verifikasi ke user.

### Siapkan Akun untuk Captcha (Opsional)
1.  Kunjungi halaman [hCaptcha](https://www.hcaptcha.com/)
2.  Jika belum punya akun, pilih menu Sign Up terlebih dahulu
3.  Setelahnya akan diarahkan menuju menu billing, pilih Basic (Free) untuk menggunakan versi gratisnya
4.  Kemudian selesaikan menjawab pertanyaan yang muncul sampai masuk pada bagian proses pembuatan akun
5.  Setelah proses pembuatan akun selesai akan diarahkan pada halaman welcome
6.  <span id="sitekey">Salin kode Sitekey yang muncul</span>
7.  <span id="secret">Pada point Secret, generata kode dan salin kode yang muncul</span>

    Kode yang muncul akan diletakkan di `CAPTCHA_SITE_KEY` dan `CAPTCHA_SECRET_KEY` pada file konfigurasi `docker-compose.yml`. Captcha akan digunakan dalam proses registrasi user.

### Proses Instalasi

1.  Login kedalam server menggunakan SSH
    ```
    $ ssh root@202.73.25.70 -p 10207
    ```

2.  Pastikan seluruh paket sistem up-to-date, dan install seluruh kebutuhan sistem seperti Docker dan Docker Compose
    ```
    $ sudo apt update
    $ sudo apt install docker.io
    $ sudo apt install docker-compose
    ```

3.  Cek apakah sudah terinstall dengan benar
    ```
    $ docker --version
    $ docker-compose --version
    ```

4.  Buat dan masuk ke dalam directory `keila`
    ```
    $ mkdir keila
    $ cd keila
    ```

5.  <span id="langkah5">Generate random secret key base, salin string yang muncul</span>
    ```
    $ head -c 48 /dev/urandom | base64
    ```

6. Konfigurasi File `docker-compose.yml`
    ```
    $ nano docker-compose.yml


    version: '3'

    services:
      keila:
        image: pentacent/keila:latest
        container_name: keila
        restart: unless-stopped
        ports:
          - "80:80"
        environment:
          DB_URL: postgres://keila_user:keila_password@db/keila_db
          SECRET_KEY_BASE: eQMBx0d4WLjmzkL5v6yYrjW87iKSh2HnngzyxsInl3bRmSbTGSLHHKwpEx4R/SJh
          URL_HOST: www.keila-komdat-p2-kelompok7.me
          URL_SCHEMA: http
          PORT: 80
          MAILER_TYPE: smtp
          MAILER_SMTP_HOST: smtp.gmail.com
          MAILER_SMTP_PORT: 587
          MAILER_SMTP_USER: ahmadsubhan@apps.ipb.ac.id
          MAILER_SMTP_PASSWORD: "umrx nofd iyzc oiar"
          MAILER_SMTP_FROM_EMAIL: ahmadsubhan@apps.ipb.ac.id
          MAILER_ENABLE_SSL: "false"
          MAILER_ENABLE_STARTTLS: "true"
          MAILER_ENABLE_STARTTLS: "true"

        depends_on:
          - db

      db:
        image: postgres:13
        container_name: keila_db
        restart: unless-stopped
        environment:
          POSTGRES_USER: keila_user
          POSTGRES_PASSWORD: keila_password
          POSTGRES_DB: keila_db
        volumes:
          - postgres_data:/var/lib/postgresql/data

    volumes:
      postgres_data:
    ```
    - Pada bagian `SECRET_KEY_BASE` isi dengan string yang muncul pada [langkah 5](#langkah5)
    - Pada bagian `URL_HOST` isi dengan nama domain atau alamat IP
    - Pada bagian `MAILER_SMTP_HOST` isi dengan `smtp.gmail.com`
    - Pada bagian `MAILER_SMTP_USER` isi dengan email yang digunakan
    - Pada bagian `MAILER_SMTP_PASSWORD` isi dengan kode yang muncul dari [App passwords](#siapkan-akun-untuk-SMTP)
    - Pada bagian `MAILER_SMTP_FROM_EMAIL` isi dengan email yang digunakan
    
    <span>Tambahan pengaturan (opsional), pengaturan ini bisa diletakkan dibawah `MAILER_ENABLE_STARTTLS: "true"`</span>
    <ul>
      <li>Pada bagian <code>CAPTCHA_PROVIDER</code> isi dengan <code>hcaptcha</code></li>
      <li>Pada bagian <code>CAPTCHA_SITE_KEY</code> 
      isi dengan kode sitekey yang muncul di akun <a href="#sitekey">hcaptcha</a></li>
      <li>Pada bagian <code>CAPTCHA_SECRET_KEY</code> 
      isi dengan kode secret yang muncul di akun <a href="#secret">hcaptcha</a></li>
      <li>Pada bagian <code>CAPTCHA_URL</code> isi dengan <code>https://hcaptcha.com/siteverify</code></li>
    </ul>

7.  Jalankan Keila
    ```
    $ docker-compose up -d
    ```

8.  Jika terdapat masalah, cek log untuk mengetahui letak error
    ```
    $ docker-compose logs -f
    ```

9.  Kunjungi alamat IP web server untuk meneruskan instalasi

### Demo Pemakaian

1. Login atau Sign up ke Website Keila dengan menggunakan email
![komdat1](https://github.com/user-attachments/assets/1f5059c9-4199-4b5d-8fef-b56acd577b7a)

2. Pilih button Create Project
![komdat 2](https://github.com/user-attachments/assets/58b0e91f-8485-46ac-b70f-a66baabc95f8)
