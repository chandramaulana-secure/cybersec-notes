# Fundamental Jaringan Komputer (Catatan Pentest)

## 1. Pengenalan Dasar Jaringan Komputer, End Devices & Ekosistemnya
Jaringan komputer adalah kumpulan perangkat yang saling terhubung untuk bertukar data dan berbagi sumber daya (seperti koneksi internet, file, atau layanan).

* **Tujuan utama dalam pentest:** Memahami alur pergerakan data dari satu host ke host lain, lokasi kontrol/pengamanan jaringan, serta bagaimana paket atau sesi komunikasi dapat dianalisis atau diuji keamanannya.
* **End Devices (*Host*) & Perangkat Terkait:** Titik akhir di jaringan yang mengirim, menerima, atau menyediakan data/layanan:
  * **Client / Workstation:** Perangkat pengguna akhir seperti laptop, PC desktop, dan *smartphone* yang mengakses aplikasi, *browser*, atau layanan jaringan.
  * **Server:** Host khusus yang menyediakan layanan atau menyimpan data untuk perangkat lain—misalnya *web server*, *database server*, *file server*, *DNS server*, atau *mail server*.
  * **IoT & Embedded Devices:** Perangkat pintar/terhubung seperti kamera IP, *smart TV*, printer jaringan, atau perangkat industri yang sering kali punya keterbatasan sumber daya dan patch keamanan.
  * **Virtual Machine (VM) & Container:** *Host* virtual yang berjalan di atas *hypervisor* atau *container engine* (sering dipakai untuk lab pentest maupun infrastruktur server modern).
* **Komponen Pendukung di Sekitar End Devices:**
  * **Intermediary / Network Devices:** Perangkat perantara yang mengarahkan, meneruskan, atau menyaring lalu lintas antar-*end device* (seperti *switch*, *router*, *access point*, dan *firewall*).
  * **Transmission Media (Media Transmisi):** Jalur fisik atau nirkabel yang menghubungkan perangkat—seperti kabel *twisted pair* (UTP/STP), *fiber optic*, dan gelombang radio (Wi-Fi).
* **Jenis jaringan berdasarkan cakupan:**
  * **LAN (Local Area Network):** Jaringan area lokal (rumah, kantor, lab) dengan latensi rendah dan pengelolaan lokal.
  * **MAN (Metropolitan Area Network):** Menghubungkan beberapa LAN dalam satu area kota atau kawasan.
  * **WAN (Wide Area Network):** Menghubungkan jaringan antar-kota atau antar-negara (contoh terbesar: Internet).

---

## 2. Perangkat Jaringan & Cara Kerjanya
Setiap perangkat jaringan bekerja pada *layer* tertentu dan memproses data sesuai fungsinya:

* **Hub / Repeater (Layer 1 - Physical):** Meneruskan sinyal listrik/optik ke semua *port* tanpa menyaring tujuan—kurang efisien dan rawan *collision*.
* **Switch (Layer 2 - Data Link):** Meneruskan *frame* data hanya ke *port* tujuan berdasarkan tabel **MAC address** (`MAC table`), sehingga komunikasi di jaringan lokal lebih terarah.
* **Router (Layer 3 - Network):** Menghubungkan jaringan yang berbeda (misalnya LAN ke internet/WAN) dan menentukan jalur pengiriman paket berdasarkan **IP address** dan *routing table*.
* **Access Point (AP) / Wireless Router:** Menjembatani perangkat nirkabel (Wi-Fi) ke jaringan kabel/LAN.
* **Firewall:** Menyaring lalu lintas jaringan berdasarkan aturan (*rule/policy*) seperti alamat IP, *port*, protokol, atau aplikasi.

---

## 3. IPv4 & IPv6
IP (*Internet Protocol*) address adalah alamat logis pada jaringan agar paket data dapat diarahkan dan dikirim sampai ke tujuan yang tepat.

### IPv4 (*Internet Protocol version 4*)
* **Panjang alamat:** **32-bit** (kapasitas total $2^{32} = 4.294.967.296$ alamat).
* **Format penulisan:** Empat oktet desimal yang dipisahkan titik (contoh: `192.168.1.10`), tiap oktet berukuran 8-bit (`0`–`255` dalam desimal).
* **Private vs Public IP:**
  * *Private IP* (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) digunakan di jaringan internal/lokal dan tidak dirutekan langsung di internet publik tanpa NAT.
  * *Public IP* digunakan di jaringan publik/internet.
* **Subnetting & CIDR:** Membagi blok IP menjadi jaringan yang lebih kecil menggunakan *subnet mask* atau notasi CIDR (misalnya `/24`) untuk memisahkan porsi *network* dan *host*.

### IPv6 (*Internet Protocol version 6*)
* **Panjang alamat:** **128-bit** (kapasitas jauh lebih besar dibanding IPv4).
* **Format penulisan:** Delapan kelompok heksadesimal (16-bit per blok) yang dipisahkan titik dua (`:`), contoh: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`.
* **Aturan penyederhanaan:** Menghilangkan angka nol di depan pada tiap blok, serta mengganti satu rangkaian blok nol beruntun menjadi `::` (hanya boleh dilakukan satu kali per alamat).
* **Karakteristik utama:** Tidak mengandalkan NAT seperti IPv4 pada umumnya, mendukung konfigurasi otomatis (seperti SLAAC), dan memiliki jenis alamat seperti *Global Unicast*, *Link-Local* (`fe80::/10`), dan *Multicast*.

---

## 4. Class IPv4 (Classful Addressing)
Sebelum CIDR/subnetting modern digunakan secara luas, ruang alamat IPv4 dibagi ke dalam beberapa kelas berdasarkan bit awal oktet pertama:

| Kelas | Rentang Oktet Pertama | Default Subnet Mask | Penggunaan Utama |
| :--- | :--- | :--- | :--- |
| **Class A** | `1`–`126` *(catatan: `127.x.x.x` dipakai untuk loopback)* | `255.0.0.0` (`/8`) | Jaringan berskala sangat besar (banyak host) |
| **Class B** | `128`–`191` | `255.255.0.0` (`/16`) | Jaringan skala menengah hingga besar |
| **Class C** | `192`–`223` | `255.255.255.0` (`/24`) | Jaringan skala kecil / LAN umum |
| **Class D** | `224`–`239` | Tidak ada (bukan unicast) | *Multicast* |
| **Class E** | `240`–`255` | Tidak ada (bukan unicast) | Eksperimental / riset / cadangan |

* **Catatan penting:** Dalam praktik jaringan modern, pembagian blok IP menggunakan **CIDR (*Classless Inter-Domain Routing*)** jauh lebih fleksibel dibanding pembagian kelas kaku.

---

## 5. MAC Address
**MAC (*Media Access Control*) address** adalah alamat fisik/perangkat keras (48-bit) yang melekat pada antarmuka jaringan (biasanya ditulis dalam 6 pasang karakter heksadesimal, contoh: `00:1A:2B:3C:4D:5E`).

* **Fungsi utama:** Mengidentifikasi perangkat secara unik di dalam **segmen jaringan lokal (*LAN / broadcast domain*)** pada Layer 2.
* **Struktur umum (48-bit):**
  * **24-bit pertama (OUI - *Organizationally Unique Identifier*):** Menunjukkan vendor/pabrikan pembuat antarmuka jaringan.
  * **24-bit berikutnya:** Identitas unik yang ditetapkan oleh vendor.
* **Perbedaan dengan IP Address:** MAC address bekerja di jaringan lokal (Layer 2) dan tidak melewati *router* ke jaringan lain, sedangkan IP address bekerja untuk *routing* antar-jaringan (Layer 3). *(Catatan: MAC address juga bisa diubah/dispoofing secara perangkat lunak pada OS/driver tertentu).*

---

## 6. NIC & vNIC
Antarmuka jaringan berfungsi sebagai penghubung antara sistem operasi/perangkat dengan media jaringan:

* **NIC (*Network Interface Card*):** Komponen perangkat keras fisik (seperti *Ethernet port* atau *Wi-Fi adapter* di laptop/PC) yang menangani sinyal fisik, *framing*, serta memiliki MAC address fisik.
* **vNIC (*Virtual Network Interface Card*):** Antarmuka jaringan berbasis perangkat lunak yang dibuat oleh *hypervisor* atau sistem virtualisasi (seperti VirtualBox, VMware, KVM, atau Docker).
  * Berfungsi seperti NIC fisik bagi *virtual machine* (VM) atau *container*.
  * Diberi MAC address virtual (sering kali dengan awalan OUI khas *hypervisor*) dan dihubungkan ke *virtual switch* atau mode jaringan tertentu (*NAT*, *Bridged*, *Host-only*, atau *Internal*).

---

## 7. OSI Layer & TCP/IP Layer
Model *layer* membantu memetakan proses komunikasi jaringan dari lapisan fisik hingga aplikasi, sekaligus memudahkan analisis *troubleshooting* dan keamanan:

### OSI Model (7 Layer)
1. **Layer 7 - Application:** Antarmuka langsung ke aplikasi pengguna/protokol aplikasi (HTTP, HTTPS, DNS, FTP, SSH).
2. **Layer 6 - Presentation:** Format data, *encoding*, kompresi, dan enkripsi/dekripsi (TLS/SSL, JSON, JPEG).
3. **Layer 5 - Session:** Pembentukan, pengelolaan, dan pengakhiran sesi komunikasi antar-aplikasi.
4. **Layer 4 - Transport:** Pengiriman data end-to-end, segmentasi, kontrol aliran/keandalan (**TCP** yang *connection-oriented*, **UDP** yang *connectionless*), dan identifikasi **port**.
5. **Layer 3 - Network:** Pengalamatan logis (**IP address**) dan *routing* paket antar-jaringan (Router, IPv4/IPv6, ICMP).
6. **Layer 2 - Data Link:** Pengalamatan fisik lokal (**MAC address**), *framing*, dan akses media lokal (Switch, Ethernet, Wi-Fi).
7. **Layer 1 - Physical:** Transmisi bit mentah berupa sinyal listrik, gelombang radio, atau cahaya melalui kabel/media fisik (Kabel UTP, Fiber Optic, Hub).

### TCP/IP Model (4 Layer)
| TCP/IP Layer | Padanan di OSI Layer | Peran & Contoh Protokol |
| :--- | :--- | :--- |
| **Application** | Layer 7, 6, 5 | Layanan & protokol aplikasi: HTTP/HTTPS, DNS, SMTP, FTP, SSH |
| **Transport** | Layer 4 | Komunikasi *end-to-end* & *port*: TCP, UDP |
| **Internet** | Layer 3 | Pengalamatan logis & *routing* paket: IPv4, IPv6, ICMP, ARP *(sering ditempatkan di batas Link/Internet)* |
| **Network Access / Link** | Layer 2, 1 | Pengiriman *frame* & sinyal di jaringan lokal: Ethernet, Wi-Fi, MAC, *driver* NIC |
