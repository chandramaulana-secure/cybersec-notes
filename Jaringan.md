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

---

## 8. Subnetting, CIDR & IP Khusus
**Subnetting** adalah proses memecah satu blok alamat IP menjadi beberapa *subnet* yang lebih kecil agar alokasi IP lebih efisien, batas siaran (*broadcast domain*) lebih sempit, dan pengelolaan/segmentasi jaringan lebih rapi.

* **CIDR (*Classless Inter-Domain Routing*):** Penulisan *prefix* seperti `/24`, `/26`, atau `/30` yang menunjukkan jumlah bit *network* dari total 32-bit IPv4 (sisanya adalah bit *host*).
* **Rumus praktis IPv4 per *prefix* (32-bit):**
  * Jumlah total IP dalam blok = $2^{(32 - \text{prefix})}$.
  * Jumlah *usable host* (umumnya untuk *subnet* standar) = $2^{(32 - \text{prefix})} - 2$ (dikurangi **Network ID** di alamat paling awal dan **Broadcast Address** di alamat paling akhir).
* **Contoh *prefix* yang sering ditemui:**
  * `/24` (`255.255.255.0`): $2^8 = 256$ total IP, $254$ *usable host* (umum di LAN rumahan/kantor).
  * `/26` (`255.255.255.192`): $2^6 = 64$ total IP, $62$ *usable host* (memecah `/24` jadi 4 *subnet*).
  * `/30` (`255.255.255.252`): $2^2 = 4$ total IP, $2$ *usable host* (sering dipakai untuk *point-to-point link* antar-router).
* **Alamat IPv4 khusus yang wajib dikenali:**
  * `127.0.0.0/8` (paling umum `127.0.0.1`): **Loopback** — mengarah ke *host* itu sendiri untuk uji jaringan lokal/layanan internal tanpa keluar ke kabel/NIC fisik.
  * `169.254.0.0/16`: **APIPA (*Automatic Private IP Addressing*)** — alamat otomatis di Windows/OS kalau *host* gagal mendapatkan IP dari DHCP server.
  * `0.0.0.0`: "Unspecified / semua IPv4" — misalnya saat layanan *bind* ke semua antarmuka IP di mesin atau *default route* (`0.0.0.0/0`).
  * `255.255.255.255`: **Limited broadcast** — dikirim ke semua *host* di *broadcast domain* lokal saat ini.

---

## 9. ARP (*Address Resolution Protocol*) & Komunikasi di LAN
Di jaringan lokal (Ethernet/Wi-Fi), pengiriman *frame* di Layer 2 membutuhkan **MAC address** tujuan, padahal aplikasi/OS sering kali hanya tahu **IP address** tujuan.

* **Cara kerja ARP:**
  1. *Host* A mau kirim data ke IP tujuan di jaringan lokal yang sama, tapi belum punya MAC address-nya di **ARP table / ARP cache**.
  2. *Host* A mengirim **ARP Request** (secara *broadcast* ke `FF:FF:FF:FF:FF:FF`): *"Siapa yang punya IP X.X.X.X? Beri tahu MAC gua."*
  3. *Host* pemilik IP tersebut membalas lewat **ARP Reply** (secara *unicast*): *"IP X.X.X.X ada di MAC YY:YY:YY:YY:YY:YY."*
  4. Kedua *host* menyimpan pasangan IP–MAC tersebut di **ARP cache** selama beberapa waktu (*TTL/aging timer*).
* **Relevansi dalam pentest:** ARP tidak memiliki autentikasi bawaan—inilah dasar dari teknik **ARP spoofing / ARP poisoning** di jaringan lokal untuk membelokkan lalu lintas (*Man-in-the-Middle* / *MiTM* di segmen LAN).

---

## 10. DHCP & DNS
Dua layanan dasar yang membuat perangkat bisa langsung terhubung dan menavigasi jaringan dengan mudah:

### DHCP (*Dynamic Host Configuration Protocol*)
* Memberikan konfigurasi IP secara otomatis ke *client* saat terhubung ke jaringan: **IP address**, **Subnet Mask**, **Default Gateway**, dan sering kali **DNS Server** (serta *lease time*).
* **Alur dasar DORA:**
  1. **Discover:** *Client* mencari DHCP server di jaringan lokal (*broadcast*).
  2. **Offer:** DHCP server menawarkan *lease* IP address yang tersedia.
  3. **Request:** *Client* meminta IP yang ditawarkan tersebut.
  4. **Acknowledge:** DHCP server mengonfirmasi dan mengikat *lease* IP ke *client*.

### DNS (*Domain Name System*)
* Menerjemahkan nama domain yang mudah dibaca manusia (seperti `example.com`) menjadi alamat IP (dan sebaliknya), bekerja di atas **UDP port 53** (dan **TCP port 53** untuk *zone transfer* atau respons besar).
* **Tipe *record* penting saat *reconnaissance*:**
  * `A`: Memetakan domain ke alamat **IPv4**.
  * `AAAA`: Memetakan domain ke alamat **IPv6**.
  * `CNAME`: *Canonical name* / alias dari satu domain ke domain lain.
  * `MX`: *Mail exchange* — menentukan *mail server* tujuan email domain tersebut.
  * `TXT`: Teks bebas — sering dipakai untuk verifikasi kepemilikan domain, SPF, DKIM, atau DMARC.
  * `PTR`: *Reverse DNS* — memetakan IP address kembali ke nama *host*/domain.

---

## 11. TCP vs UDP, *3-Way Handshake* & *Port* Umum
Di **Layer 4 (Transport)**, protokol mengatur bagaimana data dikirim antar-proses/aplikasi menggunakan nomor **Port** (`0`–`65535`).

* **TCP (*Transmission Control Protocol*):**
  * *Connection-oriented* (ada pembentukan sesi dulu), **andal (*reliable*)**, menjamin urutan paket, ada *acknowledgement* dan retransmisi jika paket hilang/rusak, serta *flow/congestion control*.
  * Cocok untuk data yang tidak boleh hilang/berantakan: HTTP/HTTPS, SSH, FTP, SMTP.
* **UDP (*User Datagram Protocol*):**
  * *Connectionless* (langsung kirim *datagram* tanpa *handshake*), lebih cepat dan *overhead* kecil, tapi tidak menjamin sampai atau berurutan.
  * Cocok untuk komunikasi *real-time* atau kueri cepat: DNS, DHCP, VoIP, *streaming* game/video.
* **TCP *3-Way Handshake*:**
  1. `SYN`: *Client* mengirim paket sinkronisasi untuk memulai koneksi.
  2. `SYN-ACK`: *Server* merespons dengan *SYN* dan *ACK* (konfirmasi).
  3. `ACK`: *Client* mengirim *ACK* — koneksi terbentuk dan data mulai dikirim.
  *(Penutupan koneksi umumnya memakai `FIN` atau langsung memutus dengan `RST`.)*
* **Pentingnya buat *pentest*:** Memahami *flag* TCP (`SYN`, `ACK`, `FIN`, `RST`, `PSH`, `URG`) membantu membaca hasil *port scanning* (seperti `nmap`) dan mendeteksi perilaku *firewall* atau *filtering*.
* **Daftar *port* umum yang sering muncul:**
  * `21` — **FTP** (*File Transfer Protocol*)
  * `22` — **SSH** (*Secure Shell*)
  * `23` — **Telnet** (tidak terenkripsi)
  * `25` — **SMTP** (*Simple Mail Transfer Protocol*)
  * `53` — **DNS** (*Domain Name System*)
  * `80` — **HTTP** (*Hypertext Transfer Protocol*)
  * `443` — **HTTPS** (HTTP over TLS/SSL)
  * `445` — **SMB** (*Server Message Block* — sering jadi titik perhatian di jaringan internal Windows/Samba)
  * `3389` — **RDP** (*Remote Desktop Protocol*)

---

## 12. NAT (*Network Address Translation*) & *Default Gateway*
* **Default Gateway:** Perangkat (biasanya *router*) di *subnet* lokal yang menjadi pintu keluar utama ketika *host* ingin mengirim paket ke **IP tujuan di luar jaringan/subnet-nya sendiri**.
* **NAT (*Network Address Translation*):** Menerjemahkan alamat IP privat internal (seperti `192.168.1.x` atau `10.x.x.x`) ke IP publik pada *router*/*gateway*, sehingga banyak *host* di jaringan lokal bisa berbagi akses internet.
  * **PAT (*Port Address Translation*) / NAT Overload:** Bentuk NAT paling umum di rumah/kantor — membedakan banyak koneksi internal ke satu IP publik menggunakan nomor *source port* yang berbeda di tabel NAT.

---

## 13. Alur Paket (*Encapsulation & Decapsulation*)
Saat data dikirim dari satu *host* ke *host* lain, data tersebut dibungkus berlapis (*encapsulation*) saat turun dari *layer* atas ke *layer* bawah, lalu dibuka lapis demi lapis (*decapsulation*) saat naik di *host* penerima:

1. **Data (Application/Upper Layers):** Payload/pesan dari aplikasi (misalnya *request* HTTP).
2. **Segment / Datagram (Transport Layer - Layer 4):** Ditambahkan *TCP header* (menjadi *segment*) atau *UDP header* (menjadi *datagram*), berisi *source port* dan *destination port*.
3. **Packet (Network Layer - Layer 3):** Ditambahkan *IP header*, berisi *source IP* dan *destination IP*.
4. **Frame (Data Link Layer - Layer 2):** Ditambahkan *MAC header* (*source MAC* & *destination MAC* di segmen lokal saat ini) serta *trailer* (*FCS* untuk cek integritas *frame*).
5. **Bit (Physical Layer - Layer 1):** Diubah menjadi sinyal listrik, cahaya, atau gelombang radio untuk dikirim melalui media fisik/nirkabel.

* **Catatan penting saat paket melintas *router*:** **IP address** asal dan tujuan *end-to-end* tetap dipertahankan (kecuali diubah oleh NAT), sedangkan **MAC address** di *frame* Layer 2 **berubah tiap lompatan (*hop*)**—dari pengirim ke *router* lokal, lalu dari *router* lokal ke *hop* berikutnya sampai ke jaringan tujuan.
