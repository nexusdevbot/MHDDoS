# MHDDoS — Stress-Test Tool (57 Methods) + Panduan Lengkap

> Mirror + dokumentasi bahasa Indonesia dari [MatrixTM/MHDDoS](https://github.com/MatrixTM/MHDDoS) (OWAS'22 #3).
> **CATATAN:** Gunakan HANYA pada infrastruktur milik sendiri / yang Anda miliki izinnya. Penyalahgunaan terhadap pihak ketiga adalah tindakan ilegal.

---

## 📋 Status Pengujian (terverifikasi Ubuntu 22.04, Python 3.10)

| Mode | Metode | Status |
|---|---|---|
| Layer7 | GET, POST, HEAD, BYPASS, OVH, PPS, COOKIE, RHEX | ✅ 8/8 jalan |
| Layer4 | TCP (10 dtk, 2 thread) | ✅ jalan, target tetap sehat |
| Tools | PING | ✅ 5/5 paket |

---

## 🔧 Cara Instalasi

### 1. Clone & dependensi
```bash
git clone https://github.com/MatrixTM/MHDDoS.git
cd MHDDoS

# Python 3.10+ wajib
python3 -m pip install --upgrade pip
python3 -m pip install -r requirements.txt

# Jika ada modul yang kurang (umum ditemui), pasang manual:
python3 -m pip install paramiko pysocks psutil requests cfscrape icmplib
```

### 2. Cek instalasi
```bash
python3 start.py
```
Akan muncul daftar method: **Layer4 (21)** · **Layer7 (26)** · **Tools (7)**.

### 3. Trik: matikan auto-download proxy
Saat dijalankan tanpa file proxy, script akan **mengunduh & mengecek proxy dari internet** (lambat). Supaya jalan langsung tanpa proxy, buat file kosong:
```bash
mkdir -p files/proxies
touch files/proxies/none
# lalu pakai proxy_file = none saat memanggil
```
Inilah penyebab umum "tools macet setelah banner" — file proxy tidak ada sehingga ia men-verify ribuan proxy dulu.

---

## 🚀 Cara Menggunakan

### Layer 7 (HTTP/HTTPS)
```bash
python3 start.py <METHOD> <URL> <SOCKS_TYPE> <THREADS> <PROXY_FILE> <RPC> <DURASI>
```
| Arg | Arti | Contoh |
|---|---|---|
| `METHOD` | lihat daftar L7: GET, POST, HEAD, BYPASS, OVH, PPS, COOKIE, RHEX, ... | `GET` |
| `URL` | URL target lengkap | `http://127.0.0.1:8899/` |
| `SOCKS_TYPE` | 6=RANDOM, 5=SOCKS5, 4=SOCKS4, 1=HTTP, 0=ALL | `6` |
| `THREADS` | jumlah thread (≤1000) | `2` |
| `PROXY_FILE` | nama file di `files/proxies/` | `none` (file kosong = tanpa proxy) |
| `RPC` | request per koneksi (≤100) | `1` |
| `DURASI` | detik | `15` |

Contoh stress-test server sendiri, 2 thread, 15 detik:
```bash
python3 start.py GET http://127.0.0.1:8899/ 6 2 none 1 15
```

### Layer 4 (TCP/UDP)
```bash
python3 start.py <METHOD> <ip:port> <THREADS> <DURASI>
```
Contoh TCP stress-test server sendiri, 2 thread, 10 detik:
```bash
python3 start.py TCP 127.0.0.1:8899 2 10
```
> Method `SYN` dan `ICMP` butuh raw socket + akses publik (`Cannot Create Raw Socket` = environment tidak mengizinkan).

### Tools (PING / CHECK / CFIP / dll)
`tools` adalah console interaktif — untuk otomasi, pipe input-nya:
```bash
# ping
printf "PING\n1.2.3.4\n" | python3 start.py tools

# cek port
printf "CHECK\n1.2.3.4\n" | python3 start.py tools
```
> Tanpa input, `tools` akan menunggu stdin (`EOFError` saat dijalankan non-interaktif itu normal).

---

## 🩹 Troubleshooting Cepat
- **Diam setelah banner** → file proxy tidak ada, tools sedang download proxy. Pakai trik `files/proxies/none`.
- **`Cannot Create Raw Socket`** → method L4 itu tidak didukung environment (container/VM tanpa NET_RAW).
- **`Method Not Found`** → cek nama method: `python3 start.py` (huruf besar semua).
- **`DeprecationWarning ssl...`** → hanya warning, abaikan.

## ⚖️ Legalitas
Repositori ini hanya untuk **pengujian beban dan pengembangan proteksi pada infrastruktur milik sendiri**. Penyalahgunaan melanggar hukum di banyak negara (termasuk UU ITE di Indonesia).

## 📜 Lisensi
Mengikuti lisensi proyek asli: **MIT** — © [MatrixTM](https://github.com/MatrixTM/MHDDoS)
