# Dokumentasi Perancangan Jaringan Smart City Kota Surabaya

## 1. Gambar Topologi Jaringan

![alt text](Assets/Topology.png)

Topologi dirancang untuk menghubungkan jaringan Balai Kota, pusat data, lima router wilayah, jaringan Pelabuhan, dan ISP melalui koneksi WAN.

## 2. Alasan Pemilihan Skema VLSM

Pada perancangan jaringan Smart City Kota Surabaya, skema **Variable Length Subnet Mask (VLSM)** dipilih sebagai metode alokasi subnet berdasarkan kebutuhan host setiap LAN.

Pertimbangan pemilihan VLSM adalah sebagai berikut:

1. **Kebutuhan host berbeda-beda.** Jaringan WiFi publik, CCTV, server, sensor, dan perangkat lainnya membutuhkan jumlah alamat IP yang berbeda.
2. **Penggunaan alamat IP lebih efisien.** Setiap jaringan dapat menggunakan prefix yang sesuai dengan jumlah host yang dibutuhkan, sehingga tidak semua LAN harus menggunakan ukuran subnet yang sama.
3. **Pengalamatan lebih terstruktur.** Setiap LAN dan link antar-router memiliki Network ID, prefix, rentang alamat host, broadcast address, dan gateway yang terdokumentasi.
4. **Sesuai dengan hasil perhitungan UTS.** Alokasi subnet menggunakan hasil perhitungan subnetting yang telah dibuat dan disesuaikan dengan kebutuhan host setiap jaringan.

CIDR juga mendukung prefix dengan panjang yang bervariasi dan agregasi rute. Namun, VLSM dipilih karena pendekatan alokasinya sesuai dengan kebutuhan host yang berbeda pada masing-masing LAN dalam proyek ini.

## 3. Penjelasan Desain Topologi

![alt text](<Assets/Alokasi VLSM.png>)
Alokasi alamat IP dilakukan menggunakan metode VLSM untuk
menyesuaikan kebutuhan host setiap jaringan.

Detail pembagian subnet dapat dilihat pada [Tabel Alokasi VLSM](https://docs.google.com/spreadsheets/d/12Tc-e2IoO7rye4RuHGMcsNEBEp5KzE1eh5NwAQSng9w/edit?usp=sharing).

### 3.1. Router Pusat (R-PUSAT)

R-PUSAT berfungsi sebagai pusat konektivitas jaringan Smart City. Router ini menghubungkan jaringan Balai Kota, R-DC, R-UTARA, R-BARAT, R-TIMUR, dan R-SELATAN melalui link WAN. R-PUSAT juga terhubung dengan R-ISP untuk akses internet serta menjadi endpoint tunnel GRE menuju R-PELABUHAN.

| Koneksi/LAN | Network ID | Prefix | Subnet Mask | IP R-PUSAT / Gateway |
|---|---|---|---|---|
| Pegawai Balai Kota | `10.64.224.0` | `/21` | `255.255.248.0` | `10.64.224.1` |
| Server Balai Kota | `10.64.241.0` | `/27` | `255.255.255.224` | `10.64.241.1` |
| Link ISP | `203.0.113.32` | `/30` | `255.255.255.252` | `203.0.113.33` |

LAN Pegawai Balai Kota menggunakan prefix `/21`, sedangkan LAN Server Balai Kota menggunakan `/27` karena kebutuhan host keduanya berbeda.

### 3.2. Jaringan Pusat Data (R-DC)

R-DC menghubungkan jaringan CCTV Kota dan Server dan Storage DC. Kedua LAN dipisahkan menggunakan VLAN 10 dan VLAN 20 pada switch. Router-on-a-stick digunakan untuk menyediakan gateway melalui subinterface dengan enkapsulasi 802.1Q.

| LAN | Network ID | Prefix | Subnet Mask | Gateway | Contoh IP Perangkat |
|---|---|---|---|---|---|
| CCTV Kota | `10.64.64.0` | `/19` | `255.255.224.0` | `10.64.64.1` | `10.64.64.2` |
| Server dan Storage DC | `10.64.238.0` | `/24` | `255.255.255.0` | `10.64.238.1` | `10.64.238.2` |

Alamat gateway CCTV berada pada subinterface `FastEthernet0/0.10`, sedangkan gateway Server dan Storage DC berada pada `FastEthernet0/0.20`.

### 3.3. Jaringan Wilayah Utara (R-UTARA)

R-UTARA melayani WiFi Publik Utara dan Sensor Lalu Lintas Utara. Kedua LAN menggunakan VLAN berbeda agar jaringan pengguna dan sensor dapat dikelola secara terpisah.

| LAN | Network ID | Prefix | Subnet Mask | Gateway | Contoh IP Perangkat |
|---|---|---|---|---|---|
| WiFi Publik Utara | `10.64.0.0` | `/18` | `255.255.192.0` | `10.64.0.1` | `10.64.0.2` |
| Sensor Lalu Lintas Utara | `10.64.236.0` | `/23` | `255.255.254.0` | `10.64.236.1` | `10.64.236.2` |

Selain melayani kedua LAN tersebut, R-UTARA terhubung ke R-PUSAT melalui L2 dan ke R-PELABUHAN melalui L6.

### 3.4. Jaringan Wilayah Barat (R-BARAT)

R-BARAT menghubungkan jaringan WiFi Publik Barat dengan jaringan Puskesmas dan Sekolah Barat.

| LAN | Network ID | Prefix | Subnet Mask | Gateway | Contoh IP Perangkat |
|---|---|---|---|---|---|
| WiFi Publik Barat | `10.64.96.0` | `/19` | `255.255.224.0` | `10.64.96.1` | `10.64.96.2` |
| Puskesmas dan Sekolah Barat | `10.64.239.0` | `/24` | `255.255.255.0` | `10.64.239.1` | `10.64.239.2` |

Pemisahan kedua LAN memungkinkan jaringan WiFi publik dan jaringan layanan kesehatan serta pendidikan memiliki ruang alamat masing-masing.

### 3.5. Jaringan Wilayah Timur (R-TIMUR)

R-TIMUR melayani WiFi Publik Timur dan Sensor Banjir Timur.

| LAN | Network ID | Prefix | Subnet Mask | Gateway | Contoh IP Perangkat |
|---|---|---|---|---|---|
| WiFi Publik Timur | `10.64.160.0` | `/19` | `255.255.224.0` | `10.64.160.1` | `10.64.160.2` |
| Sensor Banjir Timur | `10.64.232.0` | `/22` | `255.255.252.0` | `10.64.232.1` | `10.64.232.2` |

WiFi publik dan sensor banjir ditempatkan pada subnet yang berbeda untuk memisahkan pengalamatan kedua jenis layanan tersebut.

### 3.6. Jaringan Wilayah Selatan (R-SELATAN)

R-SELATAN menghubungkan jaringan WiFi Publik Selatan dan PJU Pintar Selatan.

| LAN | Network ID | Prefix | Subnet Mask | Gateway | Contoh IP Perangkat |
|---|---|---|---|---|---|
| WiFi Publik Selatan | `10.64.192.0` | `/19` | `255.255.224.0` | `10.64.192.1` | `10.64.192.2` |
| PJU Pintar Selatan | `10.64.240.128` | `/25` | `255.255.255.128` | `10.64.240.129` | `10.64.240.130` |

Kedua LAN menggunakan subnet berbeda sesuai kebutuhan host masing-masing.

### 3.7. Jaringan Pelabuhan (R-PELABUHAN)

R-PELABUHAN melayani jaringan Sensor dan SCADA Pelabuhan serta Kantor Pelabuhan. Router ini terhubung ke R-UTARA melalui L6 dan direncanakan memiliki tunnel GRE dengan R-PUSAT.

| LAN | Network ID | Prefix | Subnet Mask | Gateway | Contoh IP Perangkat |
|---|---|---|---|---|---|
| Sensor dan SCADA Pelabuhan | `10.64.128.0` | `/19` | `255.255.224.0` | `10.64.128.1` | `10.64.128.2` |
| Kantor Pelabuhan | `10.64.240.0` | `/25` | `255.255.255.128` | `10.64.240.1` | `10.64.240.2` |

Kedua jaringan dipisahkan menggunakan VLAN yang berbeda pada switch Pelabuhan. Pemisahan ini membantu pengelolaan jaringan perangkat sensor dan jaringan kantor.

### 3.8. Koneksi WAN Antar-Router

Koneksi WAN menghubungkan router pusat dengan router wilayah serta menghubungkan R-UTARA dengan R-PELABUHAN. Setiap link point-to-point menggunakan subnet `/30`, yang menyediakan dua alamat host yang dapat digunakan.

| Link | Koneksi | Network ID | Prefix | Subnet Mask | IP Endpoint |
|---|---|---|---|---|---|
| L1 | R-PUSAT – R-DC | `10.64.241.32` | `/30` | `255.255.255.252` | PUSAT `.33`, DC `.34` |
| L2 | R-PUSAT – R-UTARA | `10.64.241.36` | `/30` | `255.255.255.252` | PUSAT `.37`, UTARA `.38` |
| L3 | R-PUSAT – R-SELATAN | `10.64.241.40` | `/30` | `255.255.255.252` | PUSAT `.41`, SELATAN `.42` |
| L4 | R-PUSAT – R-TIMUR | `10.64.241.44` | `/30` | `255.255.255.252` | PUSAT `.45`, TIMUR `.46` |
| L5 | R-PUSAT – R-BARAT | `10.64.241.48` | `/30` | `255.255.255.252` | PUSAT `.49`, BARAT `.50` |
| L6 | R-UTARA – R-PELABUHAN | `10.64.241.52` | `/30` | `255.255.255.252` | UTARA `.53`, PELABUHAN `.54` |

Keenam link menggunakan alokasi WAN internal pada blok `10.64.241.32/27`. Blok tersebut mencakup alamat `10.64.241.32–10.64.241.63`; enam subnet `/30` digunakan untuk link L1–L6.

### 3.9. Koneksi ISP dan Server Internet

R-PUSAT terhubung ke R-ISP menggunakan jaringan `203.0.113.32/30`. R-ISP kemudian terhubung ke jaringan server internet `8.8.8.0/24`.

| Segmen | Network ID | Prefix | Subnet Mask | IP Perangkat |
|---|---|---|---|---|
| Link R-PUSAT – R-ISP | `203.0.113.32` | `/30` | `255.255.255.252` | PUSAT `203.0.113.33`, ISP `203.0.113.34` |
| Server Internet | `8.8.8.0` | `/24` | `255.255.255.0` | Server `8.8.8.8`, gateway `8.8.8.1` |

Link ISP menggunakan blok yang terpisah dari jaringan internal `10.64.0.0/16`. Dengan konfigurasi routing dan NAT yang sesuai, jaringan internal dapat mengakses server internet melalui R-ISP.

### 3.10. Tunnel GRE

Tunnel GRE dirancang untuk menghubungkan R-PUSAT dan R-PELABUHAN menggunakan jaringan tunnel `192.168.8.0/30`, sesuai nomor kelompok 8.

| Parameter | R-PUSAT | R-PELABUHAN |
|---|---|---|
| IP tunnel | `192.168.8.1` | `192.168.8.2` |
| Prefix | `/30` | `/30` |
| Subnet Mask | `255.255.255.252` | `255.255.255.252` |

Tunnel GRE membentuk jalur logis antara kedua router melalui jaringan underlay yang sudah tersedia. Routing harus dikonfigurasi agar lalu lintas antara subnet Balai Kota dan Pelabuhan melewati tunnel sesuai ketentuan proyek.

GRE tidak menyediakan enkripsi secara bawaan. Jika diperlukan, enkripsi dapat ditambahkan menggunakan IPsec.

##4.
###4.1

### 4.2 Konfigurasi DHCP pada Jaringan Smart City

DHCP (*Dynamic Host Configuration Protocol*) merupakan protokol yang digunakan untuk memberikan konfigurasi alamat IP secara otomatis kepada perangkat klien. Pada jaringan Smart City Kota Surabaya, DHCP server ditempatkan pada Server dan Storage Data Center (DC), sedangkan router R-DC berfungsi sebagai DHCP relay untuk meneruskan permintaan alamat IP dari jaringan CCTV Kota ke DHCP server.

Konfigurasi ini bertujuan agar perangkat CCTV Kota memperoleh alamat IP, subnet mask, default gateway, dan DNS server secara otomatis tanpa perlu melakukan pengaturan IP secara manual.

#### 4.2.1 Konfigurasi DHCP Server

DHCP server dikonfigurasi pada perangkat Server dan Storage DC dengan alamat IP `10.64.238.2/24`. Server menyediakan pool DHCP bernama `CCTV-KOTA` untuk perangkat klien pada jaringan CCTV Kota dengan network `10.64.64.0/19`.

Langkah-langkah konfigurasi DHCP server adalah sebagai berikut.

1. Buka perangkat **Server dan Storage DC** pada Cisco Packet Tracer.
2. Pilih tab **Services**, kemudian pilih menu **DHCP**.
3. Aktifkan layanan DHCP dengan memilih opsi **On**.
4. Isi parameter pool DHCP dengan konfigurasi berikut.

   | Parameter | Nilai |
   |---|---|
   | Pool Name | `CCTV-KOTA` |
   | Default Gateway | `10.64.64.1` |
   | DNS Server | `8.8.8.8` |
   | Start IP Address | `10.64.64.10` |
   | Subnet Mask | `255.255.224.0` |
   | Maximum Number of Users | `50` |

5. Klik **Add** untuk menambahkan pool atau **Save** apabila sedang mengubah pool yang sudah tersedia.
6. Pastikan pool `CCTV-KOTA` muncul pada daftar konfigurasi DHCP.

Alamat awal `10.64.64.10` digunakan sebagai alamat IP pertama yang dapat diberikan kepada klien CCTV Kota. Dengan subnet mask `255.255.224.0`, jaringan tersebut menggunakan prefix `/19`.

<img width="526" height="497" alt="Konfigurasi DHCP Server" src="https://github.com/user-attachments/assets/063c012d-36a4-4b1c-90fd-f06d63292606" />

*Gambar 4.2.1 Konfigurasi pool DHCP CCTV-KOTA pada Server dan Storage DC.*

#### 4.2.2 Konfigurasi DHCP Relay pada R-DC

Jaringan CCTV Kota dan Server dan Storage DC berada pada subnet yang berbeda. Oleh karena itu, diperlukan DHCP relay agar permintaan alamat IP dari klien CCTV dapat diteruskan ke DHCP server.

DHCP relay dikonfigurasi pada subinterface `FastEthernet0/0.10` di router R-DC. Subinterface tersebut menggunakan alamat `10.64.64.1/19` sebagai gateway jaringan CCTV Kota. Sementara itu, subinterface `FastEthernet0/0.20` menggunakan alamat `10.64.238.1/24` sebagai gateway jaringan Server dan Storage DC.

Langkah-langkah konfigurasi DHCP relay adalah sebagai berikut.

1. Buka perangkat **R-DC**, kemudian pilih tab **CLI**.
2. Tekan Enter apabila diminta untuk memulai CLI.
3. Masuk ke mode privileged dengan perintah:

   ```text
   enable
   ```

4. Tampilkan konfigurasi router menggunakan perintah:

   ```text
   show running-config
   ```

5. Periksa bagian konfigurasi subinterface `FastEthernet0/0.10`. Konfigurasi yang digunakan adalah sebagai berikut.

   ```text
   interface FastEthernet0/0.10
    encapsulation dot1Q 10
    ip address 10.64.64.1 255.255.224.0
    ip helper-address 10.64.238.2
   ```

6. Pastikan baris `ip helper-address 10.64.238.2` terdapat pada subinterface gateway CCTV Kota.
7. Pastikan subinterface `FastEthernet0/0.20` memiliki alamat `10.64.238.1` dengan subnet mask `255.255.255.0`.

Perintah `ip helper-address 10.64.238.2` digunakan untuk meneruskan permintaan DHCP dari jaringan CCTV Kota menuju server DHCP yang berada di jaringan Server dan Storage DC.

<img width="526" height="497" alt="Konfigurasi DHCP Relay R-DC" src="https://github.com/user-attachments/assets/f9829c69-569b-4818-9dce-e9174fe82bf1" />

*Gambar 4.2.2 Konfigurasi subinterface dan DHCP relay pada router R-DC.*

#### 4.2.3 Pengujian DHCP pada PC CCTV-KOTA

Pengujian dilakukan untuk memastikan bahwa perangkat CCTV Kota dapat memperoleh konfigurasi IP secara otomatis dari DHCP server.

Langkah-langkah pengujian adalah sebagai berikut.

1. Buka perangkat **CCTV-KOTA** pada Cisco Packet Tracer.
2. Pilih tab **Desktop**, kemudian buka menu **IP Configuration**.
3. Pilih opsi **DHCP**.
4. Tunggu hingga perangkat menerima konfigurasi jaringan.
5. Periksa nilai IPv4 Address, Subnet Mask, Default Gateway, dan DNS Server.
6. Bandingkan hasil yang diperoleh dengan konfigurasi pool DHCP pada Server dan Storage DC.

Berdasarkan screenshot hasil pengujian, PC CCTV-KOTA memperoleh konfigurasi berikut.

| Parameter | Hasil Pengujian |
|---|---|
| IPv4 Address | `10.64.64.10` |
| Subnet Mask | `255.255.224.0` |
| Default Gateway | `10.64.64.1` |
| DNS Server | `8.8.8.8` |
| Mode konfigurasi | DHCP |

Hasil tersebut menunjukkan bahwa PC CCTV-KOTA telah memperoleh konfigurasi IPv4 secara otomatis sesuai dengan parameter pool DHCP yang dirancang.

<img width="526" height="497" alt="Bukti PC Mendapat IP DHCP" src="https://github.com/user-attachments/assets/e8a38b21-86a0-4a82-8a9e-bb1e4c6554ae" />

*Gambar 4.2.3 Hasil penerimaan alamat IP otomatis pada PC CCTV-KOTA.*

#### 4.2.4 Kesimpulan Pengujian DHCP

Berdasarkan konfigurasi dan hasil pengujian, DHCP server pada Server dan Storage DC menyediakan alamat IP secara otomatis bagi perangkat CCTV Kota. Permintaan DHCP dari klien diteruskan melalui DHCP relay pada router R-DC karena server dan klien berada pada subnet yang berbeda.

PC CCTV-KOTA berhasil memperoleh alamat IP `10.64.64.10`, subnet mask `255.255.224.0`, default gateway `10.64.64.1`, dan DNS server `8.8.8.8`. Dengan demikian, konfigurasi DHCP pada jaringan CCTV Kota telah sesuai dengan rancangan pengalamatan jaringan Smart City.

### 4.3 Konfigurasi NAT/PAT untuk Akses Server Internet

NAT (*Network Address Translation*) merupakan mekanisme penerjemahan alamat IP yang memungkinkan perangkat dengan alamat IP privat mengakses jaringan luar menggunakan alamat IP publik. Pada jaringan Smart City Kota Surabaya, NAT dikonfigurasi pada router R-PUSAT menggunakan metode PAT (*Port Address Translation*) atau *overload*.

Konfigurasi ini bertujuan agar perangkat pada jaringan internal dapat mengakses server internet `8.8.8.8` melalui satu alamat IP publik R-PUSAT, yaitu `203.0.113.33`. Sesuai ketentuan soal, router R-ISP tidak perlu memiliki rute khusus menuju jaringan privat Smart City.

#### 4.3.1 Penentuan Interface NAT

Sebelum melakukan pengujian, interface NAT perlu diperiksa untuk memastikan pemisahan antara jaringan internal dan jaringan luar sudah benar.

Langkah-langkah pemeriksaan adalah sebagai berikut.

1. Buka perangkat **R-PUSAT**, kemudian pilih tab **CLI**.
2. Tekan Enter dan masuk ke mode privileged:

   ```text
   enable
   ```

3. Periksa status dan alamat IP interface menggunakan perintah:

   ```text
   show ip interface brief
   ```

4. Pastikan interface yang terhubung ke R-ISP menggunakan alamat `203.0.113.33` dan berada dalam keadaan aktif.
5. Periksa statistik NAT dengan perintah:

   ```text
   show ip nat statistics
   ```

6. Pastikan `Serial0/1/1` terdaftar sebagai *Outside Interface*, sedangkan interface internal yang digunakan tercantum sebagai *Inside Interfaces*.

Berdasarkan konfigurasi yang ditampilkan, pembagian interface NAT pada R-PUSAT adalah sebagai berikut.

| Interface | Peran NAT |
|---|---|
| `FastEthernet0/0` | Inside |
| `FastEthernet0/1` | Inside |
| `Serial0/1/0` | Inside |
| `Serial0/2/0` | Inside |
| `Serial0/2/1` | Inside |
| `Serial0/3/0` | Inside |
| `Serial0/3/1` | Inside |
| `Serial0/1/1` | Outside |

Pembagian tersebut digunakan untuk membedakan lalu lintas dari jaringan internal dengan lalu lintas yang menuju ISP. Alamat dan peran interface harus sesuai dengan koneksi aktual pada topologi.

<img width="526" height="497" alt="Status Interface NAT R-PUSAT" src="https://github.com/user-attachments/assets/ae710178-2c80-452e-be48-4398c29618ed" />

*Gambar 4.3.1 Hasil pemeriksaan statistik NAT pada router R-PUSAT.*

#### 4.3.2 Konfigurasi ACL dan PAT

ACL (*Access Control List*) digunakan untuk menentukan alamat sumber yang dapat diterjemahkan oleh NAT. Pada rancangan ini, ACL nomor 1 mengizinkan jaringan internal `10.64.0.0/16`.

PAT menggunakan alamat IP pada interface `Serial0/1/1` sebagai alamat publik. Dengan menggunakan kata kunci `overload`, beberapa perangkat internal dapat berbagi satu alamat IP publik untuk mengakses jaringan luar.

Langkah-langkah konfigurasi ACL dan PAT adalah sebagai berikut.

1. Buka CLI pada R-PUSAT.
2. Masuk ke mode privileged menggunakan perintah `enable`.
3. Periksa konfigurasi ACL dan NAT yang sudah tersedia melalui `show running-config`.
4. Pastikan ACL nomor 1 mengizinkan jaringan internal:

   ```text
   access-list 1 permit 10.64.0.0 0.0.255.255
   ```

5. Pastikan konfigurasi PAT menggunakan interface publik dan kata kunci `overload`:

   ```text
   ip nat inside source list 1 interface Serial0/1/1 overload
   ```

6. Pastikan interface internal telah ditandai menggunakan `ip nat inside` dan interface yang terhubung ke ISP menggunakan `ip nat outside`.
7. Pastikan R-PUSAT memiliki default route menuju R-ISP melalui alamat next hop `203.0.113.34`.

Apabila konfigurasi sudah tersedia dan benar, tidak perlu memasukkan ulang perintah yang sama. Pemeriksaan konfigurasi dilakukan untuk memastikan NAT menggunakan interface yang sesuai dengan rancangan.\

<img width="526" height="497" alt="Ping ke Server Publik Berhasil" src="https://github.com/user-attachments/assets/7c49c2cc-757c-4772-a3f7-a167932dad7d" />

*Gambar 4.3.2 Konfigurasi ACL dan PAT pada router R-PUSAT.*

#### 4.3.3 Pengujian Akses ke Server Internet

Pengujian dilakukan untuk mengetahui apakah perangkat dari jaringan internal dapat mencapai server internet `8.8.8.8`.

Langkah-langkah pengujian adalah sebagai berikut.

1. Buka perangkat **CCTV-KOTA**.
2. Pilih tab **Desktop**, kemudian buka **Command Prompt**.
3. Jalankan perintah berikut:

   ```text
   ping 8.8.8.8
   ```

4. Tunggu hingga proses ping selesai.
5. Periksa jumlah paket yang dikirim, diterima, dan hilang.
6. Apabila hasil menunjukkan `Reply from 8.8.8.8`, berarti PC menerima balasan dari server tujuan.

Berdasarkan screenshot pengujian yang tersedia, PC CCTV-KOTA mengirimkan empat paket dan menerima tiga balasan, sedangkan satu paket mengalami *Request timed out*. Hasil ini menunjukkan bahwa konektivitas menuju server internet telah berhasil, tetapi masih terdapat kehilangan satu paket pada pengujian awal.



*Gambar 4.3.3 Hasil pengujian ping dari PC CCTV-KOTA menuju server internet `8.8.8.8`.*

#### 4.3.4 Verifikasi Translasi NAT

Verifikasi translasi dilakukan untuk memastikan alamat IP privat perangkat internal diterjemahkan menjadi alamat IP publik ketika mengakses server internet.

Langkah-langkah verifikasi adalah sebagai berikut.

1. Setelah menjalankan ping dari PC CCTV-KOTA, buka kembali CLI R-PUSAT.
2. Masuk ke mode privileged jika belum berada pada mode tersebut.
3. Jalankan perintah:

   ```text
   show ip nat translations
   ```

4. Periksa kolom *Inside local*, *Inside global*, dan *Outside global*.
5. Cocokkan alamat lokal yang muncul dengan alamat IP PC CCTV-KOTA.
6. Pastikan alamat publik yang digunakan adalah `203.0.113.33` dan alamat tujuan adalah `8.8.8.8`.

Berdasarkan screenshot yang tersedia, tabel translasi NAT menampilkan alamat publik `203.0.113.33` untuk beberapa koneksi menuju `8.8.8.8`. Namun, alamat lokal yang terlihat adalah `10.64.64.5` sampai `10.64.64.8`, sedangkan PC CCTV-KOTA menggunakan `10.64.64.10`.

Oleh karena itu, tabel yang tersedia menunjukkan bahwa translasi NAT telah berlangsung untuk beberapa koneksi internal, tetapi belum membuktikan translasi khusus PC CCTV-KOTA. Pengujian perlu diulangi untuk memperoleh bukti translasi yang sesuai.

<img width="526" height="497" alt="Bukti Translasi NAT" src="https://github.com/user-attachments/assets/794d8da7-b484-47a3-910e-8fb3154c5959" />

*Gambar 4.3.4 Hasil pemeriksaan tabel translasi NAT pada router R-PUSAT.*

#### 4.3.5 Verifikasi Tabel Routing R-ISP

Pemeriksaan tabel routing dilakukan untuk memastikan R-ISP memiliki jalur menuju server internet tanpa perlu menambahkan rute khusus menuju jaringan privat Smart City.

Langkah-langkah pemeriksaan adalah sebagai berikut.

1. Buka perangkat **R-ISP**, kemudian pilih tab **CLI**.
2. Tekan Enter dan masuk ke mode privileged menggunakan perintah:

   ```text
   enable
   ```

3. Tampilkan tabel routing dengan perintah:

   ```text
   show ip route
   ```

4. Periksa jaringan yang terhubung langsung (*connected routes*).
5. Pastikan jaringan `8.8.8.0/24` dan `203.0.113.32/30` muncul pada tabel routing.
6. Periksa apakah terdapat rute khusus menuju jaringan privat `10.64.0.0/16`.

Berdasarkan screenshot yang tersedia, R-ISP menampilkan jaringan `8.8.8.0/24` yang terhubung melalui `FastEthernet0/0` dan jaringan `203.0.113.32/30` yang terhubung melalui `Serial0/3/0`. Pada tabel yang ditampilkan, tidak terlihat rute khusus menuju jaringan privat `10.64.0.0/16`.

Hasil tersebut sesuai dengan tujuan rancangan NAT/PAT, yaitu menerjemahkan alamat privat menjadi alamat publik pada R-PUSAT sehingga jaringan privat tidak perlu dikenali secara khusus oleh R-ISP.

<img width="526" height="497" alt="Bukti R-ISP Tidak Memiliki Rute Privat" src="https://github.com/user-attachments/assets/478f201b-d250-41e8-8a40-803365774b2f" />

*Gambar 4.3.5 Hasil pemeriksaan tabel routing pada router R-ISP.*

#### 4.3.6 Kesimpulan Pengujian NAT/PAT

NAT/PAT dikonfigurasi pada R-PUSAT untuk menerjemahkan alamat IP privat jaringan Smart City menjadi satu alamat IP publik, yaitu `203.0.113.33`. Pengujian ping menunjukkan bahwa PC CCTV-KOTA dapat menerima balasan dari server internet `8.8.8.8`, meskipun satu paket mengalami *Request timed out* pada pengujian awal.

Tabel translasi NAT menunjukkan penggunaan alamat publik `203.0.113.33` untuk sejumlah koneksi internal. Selain itu, tabel routing R-ISP yang diperiksa tidak menampilkan rute khusus menuju jaringan privat `10.64.0.0/16`.

<img width="527" height="504" alt="image" src="https://github.com/user-attachments/assets/494fa364-718a-47ef-9cc4-b95a54b8f89e" />

### 4.4. Soal 6: Tunnel GRE

#### 4.4.1. Tujuan dan Parameter

Tunnel GRE dibangun antara R-PUSAT dan R-PELABUHAN agar lalu lintas antara subnet Balai Kota dan subnet Pelabuhan melewati tunnel.

| Parameter | R-PUSAT | R-PELABUHAN |
|---|---|---|
| Jaringan tunnel | 192.168.8.0/30 | 192.168.8.0/30 |
| IP Tunnel0 | 192.168.8.1 | 192.168.8.2 |
| Tunnel source | Serial0/3/1 (10.64.241.37, link L2) | Serial0/3/0 (10.64.241.54, link L6) |
| Tunnel destination | 10.64.241.54 | 10.64.241.37 |

Jalur fisik (underlay) yang dilalui tunnel adalah R-PUSAT, R-UTARA, lalu R-PELABUHAN, melalui link L2 dan L6.

#### 4.4.2. Pengujian Awal Sebelum Tunnel

Sebelum tunnel dibuat, konektivitas soal 1 sampai 5 diuji terlebih dahulu. PC CCTV Kota dapat melakukan ping ke PC WiFi Barat dan ke server 8.8.8.8, dan PC Kantor Pelabuhan dapat melakukan ping ke PC CCTV Kota (alamat DHCP 10.64.64.10).

**Screenshot:** PC CCTV Kota, Desktop, Command Prompt, `ping 10.64.96.2` dan `ping 8.8.8.8`.

<img width="414" height="177" alt="image" src="https://github.com/user-attachments/assets/958e745a-32a1-4512-8591-c2a58f580032" />
<img width="412" height="186" alt="image" src="https://github.com/user-attachments/assets/64540849-c597-46e9-b13f-bf2600c1ff17" />

**Screenshot:** PC Kantor Pelabuhan, Desktop, Command Prompt, `ping 10.64.64.10`.

<img width="418" height="191" alt="image" src="https://github.com/user-attachments/assets/ece58cec-08d7-426c-bcbb-b16ef3b5d0b0" />


#### 4.4.3. Rute ke Ujung Tunnel (R-PUSAT)

Agar R-PUSAT dapat menjangkau ujung tunnel di R-PELABUHAN (10.64.241.54), ditambahkan rute ke link L6 melalui R-UTARA.

```
enable
configure terminal
ip route 10.64.241.52 255.255.255.252 10.64.241.38
end
ping 10.64.241.54
```

Rute ini harus tetap ada. Jika dihapus, ujung tunnel tidak terjangkau dan tunnel akan down.

**Screenshot:** R-PUSAT, tab CLI, hasil `ping 10.64.241.54`.

<img width="516" height="123" alt="image" src="https://github.com/user-attachments/assets/6c4dfc23-43ec-45bd-ac1b-931360e762b1" />


#### 4.4.4. Konfigurasi Tunnel di R-PUSAT

```
configure terminal
interface Tunnel0
 ip address 192.168.8.1 255.255.255.252
 tunnel source serial0/3/1
 tunnel destination 10.64.241.54
 no shutdown
end
write memory
```

Pada versi Packet Tracer yang digunakan, perintah `tunnel source` tidak menerima alamat IP, sehingga ditulis dengan nama interface (Serial0/3/1) yang memiliki IP 10.64.241.37.

#### 4.4.5. Konfigurasi Tunnel di R-PELABUHAN

Source dan destination dibalik dari R-PUSAT.

```
enable
configure terminal
interface Tunnel0
 ip address 192.168.8.2 255.255.255.252
 tunnel source serial0/3/0
 tunnel destination 10.64.241.37
 no shutdown
end
write memory
```

#### 4.4.6. Verifikasi Tunnel

Perintah di R-PUSAT dan R-PELABUHAN:

```
show ip interface brief
show interfaces tunnel0
```

Tunnel0 berstatus up/up. Pada `show interfaces tunnel0` terlihat IP tunnel, tunnel source, dan tunnel destination sesuai tabel pada bagian 6.1.

**Screenshot:** R-PUSAT, tab CLI, hasil `show ip interface brief` (baris Tunnel0 up/up).

<img width="611" height="185" alt="image" src="https://github.com/user-attachments/assets/1b3c7a1f-0926-47f4-871e-5c61717638ec" />


**Screenshot:** R-PELABUHAN, tab CLI, hasil `show ip interface brief` (baris Tunnel0 up/up).
<img width="656" height="171" alt="image" src="https://github.com/user-attachments/assets/cad5347d-b85f-4949-a089-7fda55977692" />


**Screenshot:** R-PUSAT, tab CLI, hasil `show interfaces tunnel0`.
<img width="523" height="319" alt="image" src="https://github.com/user-attachments/assets/42f45d5f-11e9-49c8-9b84-a4b499edf201" />


Uji koneksi antar ujung tunnel:

```
! di R-PUSAT
ping 192.168.8.2
! di R-PELABUHAN
ping 192.168.8.1
```

**Screenshot:** R-PUSAT, tab CLI, hasil `ping 192.168.8.2`.

<img width="517" height="114" alt="image" src="https://github.com/user-attachments/assets/eb34f061-fdaa-4639-a25a-a8885af3ae87" />


**Screenshot:** R-PELABUHAN, tab CLI, hasil `ping 192.168.8.1`.

<img width="515" height="107" alt="image" src="https://github.com/user-attachments/assets/69b06101-21ce-4c37-be29-bd90235abab3" />


#### 4.4.7. Routing Lalu Lintas Melalui Tunnel

**R-PUSAT:** next-hop lama (R-UTARA, 10.64.241.38) untuk dua LAN Pelabuhan dihapus, lalu diganti ujung tunnel (192.168.8.2).

```
configure terminal
no ip route 10.64.128.0 255.255.224.0 10.64.241.38
no ip route 10.64.240.0 255.255.255.128 10.64.241.38
ip route 10.64.128.0 255.255.224.0 192.168.8.2
ip route 10.64.240.0 255.255.255.128 192.168.8.2
end
write memory
```

**R-PELABUHAN:** ditambahkan rute balik ke dua LAN Balai Kota melalui tunnel.

```
configure terminal
ip route 10.64.224.0 255.255.248.0 192.168.8.1
ip route 10.64.241.0 255.255.255.224 192.168.8.1
end
write memory
```

Default route R-PELABUHAN ke R-UTARA (10.64.241.53) tidak diubah.

| Router | Tujuan | Next-hop | Keterangan |
|---|---|---|---|
| R-PUSAT | 10.64.128.0/19 (Sensor dan SCADA Pelabuhan) | 192.168.8.2 | lewat tunnel |
| R-PUSAT | 10.64.240.0/25 (Kantor Pelabuhan) | 192.168.8.2 | lewat tunnel |
| R-PUSAT | 10.64.241.52/30 (link L6) | 10.64.241.38 | agar ujung tunnel terjangkau |
| R-PELABUHAN | 10.64.224.0/21 (Pegawai Balai Kota) | 192.168.8.1 | lewat tunnel |
| R-PELABUHAN | 10.64.241.0/27 (Server Balai Kota) | 192.168.8.1 | lewat tunnel |
| R-PELABUHAN | 0.0.0.0/0 | 10.64.241.53 | tetap ke R-UTARA |

**Screenshot:** R-PUSAT, tab CLI, hasil `show ip route` (rute S ke 10.64.128.0/19 dan 10.64.240.0/25 via 192.168.8.2).

<img width="585" height="329" alt="image" src="https://github.com/user-attachments/assets/0b7fe7d3-845d-4132-9097-4173672d8273" />

**Screenshot:** R-PELABUHAN, tab CLI, hasil `show ip route` (rute S ke 10.64.224.0/21 dan 10.64.241.0/27 via 192.168.8.1).

<img width="578" height="277" alt="image" src="https://github.com/user-attachments/assets/ebcdb8d2-9fe6-44cf-bae8-35b460d19692" />

#### 4.4.8. Pengujian Lalu Lintas Melalui Tunnel

Dari PC Pegawai Balai Kota (10.64.224.2) menuju PC Kantor Pelabuhan (10.64.240.2):

```
ping 10.64.240.2
tracert 10.64.240.2
```

Hasil tracert:

1. 10.64.224.1 (gateway Balai Kota)
2. 192.168.8.2 (ujung tunnel R-PELABUHAN)
3. 10.64.240.2 (PC Kantor Pelabuhan)

**Screenshot:** PC Pegawai Balai Kota, Desktop, Command Prompt, hasil `tracert 10.64.240.2`.

<img width="455" height="124" alt="image" src="https://github.com/user-attachments/assets/8c462f1b-a89e-40c7-b0a8-e0214bf72b6d" />


Dari PC Kantor Pelabuhan menuju PC Pegawai Balai Kota:

```
tracert 10.64.224.2
```

Hasil tracert:

1. 10.64.240.1 (gateway Pelabuhan)
2. 192.168.8.1 (ujung tunnel R-PUSAT)
3. 10.64.224.2 (PC Pegawai Balai Kota)

**Screenshot:** PC Kantor Pelabuhan, Desktop, Command Prompt, hasil `tracert 10.64.224.2`.

<img width="416" height="143" alt="image" src="https://github.com/user-attachments/assets/c7772961-d2b1-4568-b55d-b5c04fd16a6d" />


Pada kedua arah tidak muncul hop 10.64.241.38 maupun 10.64.241.53 (R-UTARA) sebagai hop terpisah, sehingga terbukti lalu lintas melewati tunnel.

Pengujian akses internet setelah tunnel aktif (dari PC Kantor Pelabuhan):

```
ping 8.8.8.8
```

**Screenshot:** PC Kantor Pelabuhan, Desktop, Command Prompt, hasil `ping 8.8.8.8` 

<img width="451" height="200" alt="image" src="https://github.com/user-attachments/assets/3e78866f-a417-4adb-aac4-7b0f0af0ffb6" />


#### 4.4.9. Penjelasan

**Mengapa router memilih jalur tunnel dibandingkan jalur lain ke Pelabuhan.**
Router memilih rute berdasarkan longest prefix match, lalu administrative distance bila prefix sama. Pada R-PUSAT, rute ke LAN Pelabuhan dikonfigurasi dengan next-hop 192.168.8.2, yaitu ujung tunnel, dan rute lama melalui R-UTARA dihapus. Karena satu-satunya rute ke LAN Pelabuhan mengarah ke Tunnel0, router mengirim paket melalui tunnel. Paket dibungkus dengan header IP baru (source 10.64.241.37, destination 10.64.241.54) dan diteruskan melalui jalur fisik R-UTARA. Karena itu, R-UTARA hanya meneruskan paket GRE antara dua ujung tunnel dan tidak muncul sebagai hop pada tracert. Pada R-PELABUHAN, rute ke LAN Balai Kota juga diarahkan ke 192.168.8.1 sehingga arah baliknya melewati tunnel yang sama.

**Mengapa GRE saja tidak mengenkripsi data.**
GRE (Generic Routing Encapsulation) hanya melakukan encapsulation, yaitu membungkus paket asli dengan header IP dan header GRE baru agar dapat dikirim melalui jaringan lain. Isi paket tetap dikirim sebagai plaintext. GRE tidak menyediakan kerahasiaan (data dapat dibaca oleh siapa pun yang menyadap link), autentikasi (penerima tidak memverifikasi pengirim), maupun integritas (isi paket dapat diubah tanpa terdeteksi). Untuk melindungi data di dalam tunnel diperlukan protokol tambahan seperti IPsec.
