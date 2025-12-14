# FP Jarkom Kelompok 5
## Anggota Kelompok

| No | Nama                   | NRP         |
|----|------------------------|-------------|
| 1  | Revalina Erica Permatasari | 5027241007  |
| 2  | Evan Christian Nainggolan     | 5027241026  |
| 3  | Salsa Bil Ulla     | 5027241052  |
| 4  | Mohammad Abyan Ranuaji     | 5027241106  |

---

## Deskripsi Proyek

Yayasan ARA merupakan organisasi yang membawahi berbagai unit kerja pendidikan dan teknologi. Proyek ini mencakup pembangunan infrastruktur jaringan untuk tiga lokasi utama:
- **Gedung Utama Yayasan ARA** - Kantor pusat dengan 6 unit kerja
- **Gedung ARA Tech** - Kantor pusat layanan digital dengan 5 lantai
- **Kantor Cabang** - Regional office

---

## 1. Topologi Jaringan

https://github.com/RutherfordiumEnjoyer/FP-Jarkom-ARA-Network-Design-Kel5/blob/main/Topologi-K5.png

### 1.1 Struktur Jaringan

Jaringan terdiri dari 3 gedung yang saling terhubung:
- **Gedung Utama** ↔ **ARA Tech** (via Serial Link)
- **ARA Tech** ↔ **Kantor Cabang** (via Serial Link)
- **Gedung Utama** ↔ **Kantor Cabang** (via GRE Tunnel)

### 1.2 Koneksi Internet

Gedung Utama terhubung ke ISP melalui interface Serial0/3/1 dengan implementasi NAT Overload (PAT) untuk akses internet dari semua jaringan.

---

## 2. Subnetting VLSM - Gedung Utama

### 2.1 Tabel Subnetting

| Network ID | Subnet Mask | Prefix | Broadcast Address | Range IP | Jumlah Host | Unit | Cadangan |
|------------|-------------|--------|-------------------|----------|-------------|------|----------|
| 172.16.0.0 | 255.255.254.0 | /23 | 172.16.1.255 | 172.16.0.1 - 172.16.1.254 | 512 | LAYANAN_OPS (380→456 host) | ~20% |
| 172.16.2.0 | 255.255.254.0 | /23 | 172.16.3.255 | 172.16.2.1 - 172.16.3.254 | 512 | KURIKULUM (220→264 host) | ~20% |
| 172.16.4.0 | 255.255.255.128 | /25 | 172.16.4.127 | 172.16.4.1 - 172.16.4.126 | 128 | SDM (95→114 host) | ~20% |
| 172.16.4.128 | 255.255.255.192 | /26 | 172.16.4.191 | 172.16.4.129 - 172.16.4.190 | 64 | SARPRAS (45→54 host) | ~20% |
| 172.16.4.192 | 255.255.255.224 | /27 | 172.16.4.223 | 172.16.4.193 - 172.16.4.222 | 32 | PEMBINAAN (18→22 host) | ~22% |
| 172.16.4.224 | 255.255.255.240 | /28 | 172.16.4.239 | 172.16.4.225 - 172.16.4.238 | 16 | IT_PEND (6→8 host) | ~33% |

### 2.2 VLAN Assignment

- **VLAN 10** - SDM
- **VLAN 20** - KURIKULUM
- **VLAN 30** - SARPRAS
- **VLAN 40** - PEMBINAAN
- **VLAN 50** - IT_PEND
- **VLAN 60** - LAYANAN_OPS

---

## 3. Subnetting CIDR - Gedung ARA Tech

### 3.1 Tabel Subnetting per Departemen

| Kode | Network ID | Subnet Mask | Prefix | Broadcast Address | Range IP | Block Size | Host Asli→+20% | Departemen |
|------|------------|-------------|--------|-------------------|----------|------------|----------------|------------|
| A1 | 10.0.0.0 | 255.255.255.128 | /25 | 10.0.0.127 | 10.0.0.1 - 10.0.0.126 | 128 | 79→95 | LANTAI_1 (IT Supp, Cyber, Svr Room) |
| A2 | 10.0.0.128 | 255.255.255.128 | /25 | 10.0.0.255 | 10.0.0.129 - 10.0.0.254 | 128 | 85→102 | LANTAI_2 (Mkt, Sales, HR) |
| A3 | 10.0.1.0 | 255.255.255.128 | /25 | 10.0.1.127 | 10.0.1.1 - 10.0.1.126 | 128 | 78→94 | LANTAI_3 (R&D, Ppl Dev) |
| A4 | 10.0.1.128 | 255.255.255.128 | /25 | 10.0.1.255 | 10.0.1.129 - 10.0.1.254 | 128 | 86→104 | LANTAI_4 (Keu, Legal, CS) |
| A5 | 10.0.2.0 | 255.255.255.192 | /26 | 10.0.2.63 | 10.0.2.1 - 10.0.2.62 | 64 | 37→45 | LANTAI_5 (Audit, Lounge, Exec) |

### 3.2 Detail per Lantai

**Lantai 1 (A1):**
- IT Support: 45 host
- Server & Data Center: 12 host (12 server)
- Cybersecurity: 22 host (2 server)
- **Total: 79 host → 95 host (+20%)**

**Lantai 2 (A2):**
- Marketing: 35 host
- Sales: 25 host
- Human Resources: 25 host
- **Total: 85 host → 102 host (+20%)**

**Lantai 3 (A3):**
- R&D: 55 host (5 server)
- People Development: 18 host
- **Total: 73 host → 94 host (+20%)** *(5 server sudah termasuk dalam 55 host R&D)*

**Lantai 4 (A4):**
- Keuangan: 28 host
- Legal: 18 host
- Customer Service: 40 host
- **Total: 86 host → 104 host (+20%)**

**Lantai 5 (A5):**
- Executive Office: 12 host
- Guest Lounge: 10 host
- Auditorium: 15 host
- **Total: 37 host → 45 host (+20%)**

### 3.3 Supernetting (Agregasi CIDR)

| Kode | CIDR Block | Prefix | Range IP Total | Penjelasan Singkat |
|------|------------|--------|----------------|-------------------|
| B2 | 10.0.0.0/22 | /22 | 10.0.0.1 - 10.0.3.254 | Agregasi Gedung ARA Tech (Total 5 lantai) |

**Penjelasan Singkat Terkait Penggabungan:**

Subnet ini menggabungkan seluruh unit kerja di Lantai 1 s.d. Lantai 5 (A1-A5). Alasan pemilihan prefix: Total kebutuhan IP host dari seluruh lantai melebihi 512 alamat (batas maksimal /23), sehingga network diperluas menjadi /22 (kapasitas 1024 host) untuk skalabilitas. Terkait koneksi Antar-Switch: Jalur koneksi dari Core Switch ke Switch Lantai tidak dibuatkan subnet terpisah (A6, dst) dan langsung melebur ke dalam topologi ini. Hal ini karena koneksi tersebut berfungsi sebagai Trunk (Layer 2) yang hanya bertugas meneruskan data, sehingga tidak membutuhkan Network ID atau alokasi IP sendiri.

### 3.4 VLAN Assignment

- **VLAN 100** - LANTAI_1
- **VLAN 200** - LANTAI_2
- **VLAN 300** - LANTAI_3
- **VLAN 400** - LANTAI_4
- **VLAN 500** - LANTAI_5

---

## 4. Konfigurasi IP Statis dan DHCP

### 4.1 Router Gedung Utama

#### Interface Configuration (Router-on-a-Stick)

```cisco
enable
conf t

interface g0/0
 no ip address
 no shutdown

interface g0/0.10
 encapsulation dot1Q 10
 ip address 172.16.4.1 255.255.255.128
 description SDM

interface g0/0.20
 encapsulation dot1Q 20
 ip address 172.16.2.1 255.255.254.0
 description KURIKULUM

interface g0/0.30
 encapsulation dot1Q 30
 ip address 172.16.4.129 255.255.255.192
 description SARPRAS

interface g0/0.40
 encapsulation dot1Q 40
 ip address 172.16.4.193 255.255.255.224
 description PEMBINAAN

interface g0/0.50
 encapsulation dot1Q 50
 ip address 172.16.4.225 255.255.255.240
 description IT_PEND

interface g0/0.60
 encapsulation dot1Q 60
 ip address 172.16.0.1 255.255.254.0
 description LAYANAN_OPS

interface s0/3/0
 ip address 192.168.0.2 255.255.255.252
 no shutdown
```

#### DHCP Server Configuration

```cisco
ip dhcp excluded-address 172.16.4.1 172.16.4.10
ip dhcp excluded-address 172.16.2.1 172.16.2.10
ip dhcp excluded-address 172.16.4.129 172.16.4.138
ip dhcp excluded-address 172.16.4.193 172.16.4.200
ip dhcp excluded-address 172.16.4.225 172.16.4.228
ip dhcp excluded-address 172.16.0.1 172.16.0.10

ip dhcp pool SDM
 network 172.16.4.0 255.255.255.128
 default-router 172.16.4.1

ip dhcp pool KURIKULUM
 network 172.16.2.0 255.255.254.0
 default-router 172.16.2.1

ip dhcp pool SARPRAS
 network 172.16.4.128 255.255.255.192
 default-router 172.16.4.129

ip dhcp pool PEMBINAAN
 network 172.16.4.192 255.255.255.224
 default-router 172.16.4.193

ip dhcp pool IT_PEND
 network 172.16.4.224 255.255.255.240
 default-router 172.16.4.225

ip dhcp pool LAYANAN_OPS
 network 172.16.0.0 255.255.254.0
 default-router 172.16.0.1
```

### 4.2 Router ARA Tech

#### Interface Configuration

```cisco
enable
conf t

interface g0/0
 no ip address
 no shutdown

interface g0/0.100
 encapsulation dot1Q 100
 ip address 10.0.0.1 255.255.255.128
 description LANTAI_1

interface g0/0.200
 encapsulation dot1Q 200
 ip address 10.0.0.129 255.255.255.128
 description LANTAI_2

interface g0/0.300
 encapsulation dot1Q 300
 ip address 10.0.1.1 255.255.255.128
 description LANTAI_3

interface g0/0.400
 encapsulation dot1Q 400
 ip address 10.0.1.129 255.255.255.128
 description LANTAI_4

interface g0/0.500
 encapsulation dot1Q 500
 ip address 10.0.2.1 255.255.255.192
 description LANTAI_5

interface s0/3/0
 ip address 192.168.0.1 255.255.255.252
 no shutdown

interface s0/3/1
 ip address 192.168.1.1 255.255.255.252
 no shutdown
```

#### DHCP Server Configuration

```cisco
ip dhcp excluded-address 10.0.0.1 10.0.0.10
ip dhcp excluded-address 10.0.0.129 10.0.0.138
ip dhcp excluded-address 10.0.1.1 10.0.1.10
ip dhcp excluded-address 10.0.1.129 10.0.1.138
ip dhcp excluded-address 10.0.2.1 10.0.2.10

ip dhcp pool LANTAI1
 network 10.0.0.0 255.255.255.128
 default-router 10.0.0.1

ip dhcp pool LANTAI2
 network 10.0.0.128 255.255.255.128
 default-router 10.0.0.129

ip dhcp pool LANTAI3
 network 10.0.1.0 255.255.255.128
 default-router 10.0.1.1

ip dhcp pool LANTAI4
 network 10.0.1.128 255.255.255.128
 default-router 10.0.1.129

ip dhcp pool LANTAI5
 network 10.0.2.0 255.255.255.192
 default-router 10.0.2.1
```

### 4.3 Router Kantor Cabang

#### Interface Configuration

```cisco
enable
conf t

interface g0/0
 no ip address
 no shutdown

interface g0/0.70
 encapsulation dot1Q 70
 ip address 192.168.10.1 255.255.255.192

interface s0/3/0
 ip address 192.168.1.2 255.255.255.252
 no shutdown
```

#### DHCP Server Configuration

```cisco
ip dhcp excluded-address 192.168.10.1 192.168.10.10

ip dhcp pool REGIONAL
 network 192.168.10.0 255.255.255.192
 default-router 192.168.10.1
```

### 4.4 Server Configuration (Static IP)

| Server | IP Address | Subnet Mask | Default Gateway | Lokasi |
|--------|------------|-------------|-----------------|--------|
| Server IT_PEND | 172.16.4.227 | 255.255.255.240 | 172.16.4.225 | Gedung Utama |
| Server DATCEN | 10.0.0.10 | 255.255.255.128 | 10.0.0.1 | ARA Tech - Lantai 1 |
| Server CYBER | 10.0.0.11 | 255.255.255.128 | 10.0.0.1 | ARA Tech - Lantai 1 |

---

## 5. Konfigurasi Switch

### 5.1 Switch Distribusi Utama (Gedung Utama)

```cisco
enable
conf t

vlan 10
 name SDM
vlan 20
 name KURIKULUM
vlan 30
 name SARPRAS
vlan 40
 name PEMBINAAN
vlan 50
 name IT_PEND
vlan 60
 name LAYANAN_OPS

interface fa0/7
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40,50,60
 no shutdown

end
wr
```

### 5.2 Switch Access Layer (per VLAN)

#### Switch SDM
```cisco
conf t
vlan 10
 name SDM

interface gi0/1
 switchport mode trunk
 no shutdown

interface range fa0/1-24
 switchport mode access
 switchport access vlan 10
 no shutdown

end
wr
```

#### Switch Kurikulum
```cisco
conf t
vlan 20
 name KURIKULUM

interface gi0/1
 switchport mode trunk

interface range fa0/1-24
 switchport mode access
 switchport access vlan 20
 no shutdown

end
wr
```

#### Switch Sarpras
```cisco
conf t
vlan 30
 name SARPRAS

interface gi0/1
 switchport mode trunk

interface range fa0/1-24
 switchport mode access
 switchport access vlan 30
 no shutdown

end
wr
```

#### Switch Pembinaan
```cisco
conf t
vlan 40
 name PEMBINAAN

interface gi0/1
 switchport mode trunk

interface range fa0/1-24
 switchport mode access
 switchport access vlan 40
 no shutdown

end
wr
```

#### Switch IT_PEND
```cisco
conf t
vlan 50
 name IT_PEND

interface gi0/1
 switchport mode trunk

interface range fa0/1-24
 switchport mode access
 switchport access vlan 50
 no shutdown

end
wr
```

#### Switch Layanan OPS
```cisco
conf t
vlan 60
 name LAYANAN_OPS

interface gi0/1
 switchport mode trunk

interface range fa0/1-24
 switchport mode access
 switchport access vlan 60
 no shutdown

end
wr
```

### 5.3 Switch Core ARA Tech

```cisco
enable
conf t

vlan 100
 name LANTAI_1
vlan 200
 name LANTAI_2
vlan 300
 name LANTAI_3
vlan 400
 name LANTAI_4
vlan 500
 name LANTAI_5

interface fa0/6
 switchport mode trunk
 switchport trunk allowed vlan 100,200,300,400,500
 no shutdown

end
wr
```

### 5.4 Switch Access per Lantai (ARA Tech)

#### Switch Lantai 1
```cisco
conf t
vlan 100
 name LANTAI_1

interface fa0/4
 switchport mode trunk
 switchport trunk allowed vlan 100
 no shutdown

interface range fa0/1-3
 switchport mode access
 switchport access vlan 100
 no shutdown

interface range fa0/5-24
 switchport mode access
 switchport access vlan 100
 no shutdown

end
wr
```

#### Switch Lantai 2
```cisco
conf t
vlan 200
 name LANTAI_2

interface fa0/4
 switchport mode trunk
 switchport trunk allowed vlan 200
 no shutdown

interface range fa0/1-3
 switchport mode access
 switchport access vlan 200
 no shutdown

interface range fa0/5-24
 switchport mode access
 switchport access vlan 200
 no shutdown

end
wr
```

#### Switch Lantai 3
```cisco
conf t
vlan 300
 name LANTAI_3

interface fa0/3
 switchport mode trunk
 switchport trunk allowed vlan 300
 no shutdown

interface range fa0/1-2
 switchport mode access
 switchport access vlan 300
 no shutdown

interface range fa0/4-24
 switchport mode access
 switchport access vlan 300
 no shutdown

end
wr
```

#### Switch Lantai 4
```cisco
conf t
vlan 400
 name LANTAI_4

interface fa0/4
 switchport mode trunk
 switchport trunk allowed vlan 400
 no shutdown

interface range fa0/1-3
 switchport mode access
 switchport access vlan 400
 no shutdown

interface range fa0/5-24
 switchport mode access
 switchport access vlan 400
 no shutdown

end
wr
```

#### Switch Lantai 5
```cisco
conf t
vlan 500
 name LANTAI_5

interface fa0/4
 switchport mode trunk
 switchport trunk allowed vlan 500
 no shutdown

interface range fa0/1-3
 switchport mode access
 switchport access vlan 500
 no shutdown

interface range fa0/5-24
 switchport mode access
 switchport access vlan 500
 no shutdown

end
wr
```

### 5.5 Switch Kantor Cabang

```cisco
enable
conf t

vlan 70
 name REGIONAL

interface fa0/2
 switchport mode trunk
 switchport trunk allowed vlan 70
 no shutdown

interface fa0/1
 switchport mode access
 switchport access vlan 70
 no shutdown

end
wr
```

---

## 6. Routing Configuration

### 6.1 Static Routing (ARA Tech Internal)

```cisco
# Router ARA Tech
ip route 0.0.0.0 0.0.0.0 192.168.0.2
```

### 6.2 OSPF Dynamic Routing

#### Router Gedung Utama
```cisco
router ospf 1
 network 172.16.0.0 0.0.7.255 area 0
 network 192.168.0.0 0.0.0.3 area 0
```

#### Router ARA Tech
```cisco
router ospf 1
 network 10.0.0.0 0.0.3.255 area 0
 network 192.168.0.0 0.0.0.3 area 0
 network 192.168.1.0 0.0.0.3 area 0
```

#### Router Kantor Cabang
```cisco
router ospf 1
 network 192.168.10.0 0.0.0.63 area 0
 network 192.168.1.0 0.0.0.3 area 0
```

---

## 7. NAT Overload (PAT) & Internet Access

### 7.1 Konfigurasi ISP

```cisco
# Router ISP
conf t

interface s0/3/0
 ip address 203.0.113.2 255.255.255.252
 no shutdown

interface loopback0
 ip address 8.8.8.8 255.255.255.255

ip route 172.16.0.0 255.255.248.0 203.0.113.1
ip route 10.0.0.0 255.255.252.0 203.0.113.1
ip route 192.168.10.0 255.255.255.192 203.0.113.1
```

### 7.2 Konfigurasi NAT pada Router Gedung Utama

```cisco
conf t

# Konfigurasi Interface Internet
interface s0/3/1
 ip address 203.0.113.1 255.255.255.252
 no shutdown

# NAT Inside Interfaces
interface g0/0
 ip nat inside

interface g0/0.10
 ip nat inside

interface g0/0.20
 ip nat inside

interface g0/0.30
 ip nat inside

interface g0/0.40
 ip nat inside

interface g0/0.50
 ip nat inside

interface g0/0.60
 ip nat inside

# NAT Outside Interface
interface s0/3/1
 ip nat outside

# Access List untuk NAT
access-list 1 permit 172.16.0.0 0.0.7.255
access-list 1 permit 10.0.0.0 0.0.3.255
access-list 1 permit 192.168.10.0 0.0.0.63

# NAT Overload Configuration
ip nat inside source list 1 interface s0/3/1 overload

# Default Route ke ISP
ip route 0.0.0.0 0.0.0.0 203.0.113.2
```

---

## 8. GRE Tunnel Configuration

### 8.1 Router Gedung Utama

```cisco
conf t

interface Tunnel0
 ip address 10.10.10.1 255.255.255.252
 tunnel source Serial0/3/0
 tunnel destination 192.168.1.2
 tunnel mode gre ip
 no shutdown

# Route melalui Tunnel
ip route 192.168.10.0 255.255.255.192 10.10.10.2
```

### 8.2 Router Kantor Cabang

```cisco
conf t

interface Tunnel0
 ip address 10.10.10.2 255.255.255.252
 tunnel source Serial0/3/0
 tunnel destination 192.168.0.2
 tunnel mode gre ip
 no shutdown

# Route melalui Tunnel
ip route 172.16.0.0 255.255.248.0 10.10.10.1
ip route 10.0.0.0 255.255.252.0 10.10.10.1
```

---

## 9. Verifikasi dan Testing

### 9.1 Testing DHCP
- PC dikonfigurasi untuk mendapatkan IP via DHCP
- Desktop > IP Configuration > DHCP

### 9.2 Testing Konektivitas Lokal (Gedung Utama)
- PC0 SDM → Router (Default Gateway)
- PC0 SDM → PC5 LAYANAN_OPS

### 9.3 Testing Konektivitas Lokal (ARA Tech)
- PC Server Room → Router (Default Gateway)
- PC Legal → PC Marketing

### 9.4 Testing Konektivitas Lokal (Kantor Cabang)
- PC Regional → Router (Default Gateway)

### 9.5 Testing OSPF Routing
- PC Pembinaan (Gedung Utama) → PC Keuangan (ARA)
- PC Pendidikan (Gedung Utama) → PC Regional Office (Cabang)
- PC Auditorium (ARA) → PC Sarpras (Gedung Utama)

### 9.6 Testing Router-to-Router
- PC Kurikulum (Gedung Utama) → Router ARA Tech
- PC Rnd (ARA) → Router Gedung Utama
- PC Sales (ARA) → Router Gedung Cabang

### 9.7 Testing Internet Access (NAT)
- Router → Internet (ping 8.8.8.8)
- PC SDM (Gedung Utama) → Internet
- PC CustService (ARA) → Internet

### 9.8 Testing GRE Tunnel
- Status Tunnel0 (up/up) di kedua router
- Ping antar router melalui tunnel (10.10.10.1 ↔ 10.10.10.2)
- PC SDM (Gedung Utama) → PC Regional (Cabang)
- PC Regional (Cabang) → PC SDM (Gedung Utama)

### 9.9 Verifikasi Routing Table
```cisco
# Cek routing table di setiap router
show ip route

# Output akan menampilkan:
# - O (OSPF routes) - route yang dipelajari dari router lain
# - S (Static routes) - route manual
# - C (Connected) - network yang langsung terhubung
```

---

## 10. Ringkasan Konfigurasi

### 10.1 Summary Addressing

| Lokasi | Network Summary | Wildcard Mask | Jumlah Subnet | Total Host |
|--------|-----------------|---------------|---------------|------------|
| Gedung Utama | 172.16.0.0/21 | 0.0.7.255 | 6 VLAN | 764 host |
| ARA Tech | 10.0.0.0/22 | 0.0.3.255 | 5 VLAN | 360 host |
| Kantor Cabang | 192.168.10.0/26 | 0.0.0.63 | 1 VLAN | 40 host |

### 10.2 Point-to-Point Links

| Link | Network | Router A | Router B |
|------|---------|----------|----------|
| Gedung Utama ↔ ARA Tech | 192.168.0.0/30 | 192.168.0.2 | 192.168.0.1 |
| ARA Tech ↔ Kantor Cabang | 192.168.1.0/30 | 192.168.1.1 | 192.168.1.2 |
| Gedung Utama ↔ ISP | 203.0.113.0/30 | 203.0.113.1 | 203.0.113.2 |
| GRE Tunnel | 10.10.10.0/30 | 10.10.10.1 | 10.10.10.2 |

### 10.3 Teknologi yang Diimplementasikan

1. **VLSM** - Variable Length Subnet Masking (Gedung Utama)
2. **CIDR** - Classless Inter-Domain Routing (ARA Tech)
3. **VLAN** - Virtual Local Area Network
4. **Router-on-a-Stick** - Inter-VLAN routing
5. **DHCP** - Dynamic Host Configuration Protocol
6. **Static Routing** - Manual route configuration
7. **OSPF** - Open Shortest Path First (Dynamic routing)
8. **NAT Overload (PAT)** - Port Address Translation
9. **GRE Tunnel** - Generic Routing Encapsulation

---

## 11. Catatan Penting

1. **Subnetting**: Semua subnet telah dikonfigurasi dengan cadangan ~20% untuk skalabilitas
2. **DHCP Excluded**: 10 IP pertama di setiap subnet direserve untuk perangkat statis
3. **Security**: GRE Tunnel digunakan untuk koneksi aman antar gedung
4. **Redundancy**: OSPF memungkinkan jalur alternatif jika ada link failure
5. **Scalability**: Design dapat dikembangkan untuk menambah lantai/departemen baru
