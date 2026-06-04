# Kajiri Kamui Kagura — ToolKit

Tools untuk menerjemahkan Kajiri Kamui Kagura ke Bahasa Indonesia menggunakan Malie Script Tool.

---

## Status Terjemahan

| Komponen | Status |
|----------|--------|
| Dialog utama (`exec.msg.txt`) | Dalam Proses |
| Menu & nama (`exec.str.txt`) | Dalam Proses |
| Title Screen / UI Frame | Selesai |
| Patch Dapat Dijalankan |  Ya |

| Kajiri Kamui Kagura Akebono no Hikari (神咒神威神楽 曙之光 ダウンロード版) |
|:---:|
| ![Translation working in-game](https://i.imgur.com/x4kWxLV.jpeg) |

| Horizontal Patch |
|:---:|
| ![Horizontal](https://i.imgur.com/DYo45m9.jpeg) |

| Font Rekomendasi | Keterangan |
|------------------|------------|
| MS ゴシック | Default font |
| Grisaia Custom SP | — |
| Grisaia Custom | — |
| MotoyaLMaruM | — |
| LUNE | — |
| BIZ UD明朝 Medium | — |

---

## Dua Varian Patch

Ada dua folder repo, masing-masing untuk mode teks yang berbeda:

| | Vertikal | Horizontal |
|--|--|--|
| Arah teks | Vertikal (kanan ke kiri) | Horizontal (kiri ke kanan) |
| Folder messageframe | SVG dimodifikasi per tipe | Semua pakai `normal` |
| Edit nama karakter | `exec.str.txt` | `exec.str.txt` |
| Edit dialog | `exec.msg.txt` | `exec.msg.txt` |

Alur kerjanya identik — perbedaan hanya di folder messageframe yang dicopy ke game.

---

## Hal Wajib Sebelum Mulai

**1. Gunakan `malie.exe` dan `malie.ini` dari repo ini**

```
[KT] KKK\
├── malie.exe     ← jalankan game dari sini (tidak perlu AlphaROMdiE)
└── malie.ini     ← copy ke folder instalasi game
```

**2. Buat folder `.data\system` secara manual** *(hanya untuk repo Vertikal — di Horizontal sudah ada)*

```bat
mkdir "dependencies\malie tools\compilar\Malie_Script_Tool-main\bin\Debug\.data\system"
```

---

## Kebutuhan

- **Python** — [python.org](https://python.org), centang **"Add to PATH"** saat install
- **Notepad++** — [notepad-plus-plus.org](https://notepad-plus-plus.org)
- Game KKK sudah terinstall

---

## Format File

### `exec.msg.txt` — Dialog Utama

Buka dengan Notepad++, pastikan encoding **UTF-8** (bukan UTF-8-BOM).

```
◇00000002◇　振り下ろす一閃――[z]
◆00000002◆　Tebasan yang menghujam――[z]
```

| Simbol | Fungsi |
|--------|--------|
| `◇` | Teks Jepang asli — jangan diubah |
| `◆` | Baris terjemahan — ini yang diedit |
| `[z]` | Penanda akhir dialog, wajib ada |
| `[c]` | Jeda, tunggu klik player |
| `[n]` | Baris baru manual |
| `[s]` | Penanda suara/voice |

Aturan: hanya edit baris `◆`, jangan hapus penanda `[z]` `[c]` `[n]` `[s]`, jangan ubah nomor ID.

---

### `exec.str.txt` — Nama Karakter & String UI

File ini berisi nama karakter, pilihan menu, dan string antarmuka lainnya. Edit langsung baris `◇` untuk mengganti teks Jepang dengan terjemahan — perubahan akan langsung terlihat di game.

Contoh:

```
◇00006A3E◇覇吐
```

Ubah menjadi:

```
◇00006A3E◇Habaki
```

> **Catatan:** Tidak seperti `exec.msg.txt`, file ini tidak menggunakan pasangan `◇`/`◆`. Edit langsung nilai setelah `◇XXXXXXXX◇`.

Setelah diedit, proses compile dan pack sama seperti biasa (lihat bagian di bawah).

---

## Alur Kerja

Pastikan CMD berada di folder `dependencies\`:

```bat
cd "C:\Users\user\Downloads\KKK exe manipulator\KKK-main\dependencies"
```

### Opsi A — Edit Langsung (Tanpa Wordwrap)

Edit file ini langsung:

```
dependencies\malie tools\compilar\Malie_Script_Tool-main\bin\Debug\data\system\exec.msg.txt
```

Usahakan ≤25 karakter per baris. Gunakan `[n]` untuk memotong:

```
◆00000003◆　Serangan itu bukan sekadar[n]tebasan biasa――[z]
```

---

### Opsi B — Lewat Folder `script` + Wordwrap Otomatis

Untuk baris panjang yang ingin dipotong otomatis oleh `wordwrap.py`.

Setup sekali saja:

```bat
mkdir "dependencies\script"
```

Alur:

1. Copy `exec.msg.txt` ke `dependencies\script\`, rename jadi `message.txt`
2. Edit `message.txt` dengan Notepad++
3. Jalankan wordwrap:
   ```bat
   python wordwrap.py
   ```
4. Copy hasil ke compile tool:
   ```bat
   copy "script_done\message.txt" "malie tools\compilar\Malie_Script_Tool-main\bin\Debug\data\system\exec.msg.txt"
   ```

---

## Compile & Pack

### Step 1 — Compile skrip

```bat
"malie tools\compilar\Malie_Script_Tool-main\bin\Debug\Malie_Script_Tool.exe"
```

Output: `exec.dat` di `.data\system\exec.dat`

Jika muncul `DirectoryNotFoundException` → buat dulu folder `.data\system` (lihat **Hal Wajib**).

### Step 2 — Pack jadi `data6.dat`

```bat
python dat_pack.py "C:\Users\user\Downloads\KKK exe manipulator\KKK-main\data"
```

Selalu sertakan path lengkap folder `data`. File `data6.dat` muncul di folder `dependencies\`.

### Step 3 — Pasang ke game

```bat
copy data6.dat "C:\[folder instalasi game]\data6.dat"
```

---

## Instalasi Patch (Sekali Saja)

Selain `data6.dat`, dua hal ini perlu dicopy ke folder game satu kali di awal:

**`malie.ini`** — copy dari `[KT] KKK\malie.ini` ke root folder game, timpa yang ada.

**Folder `messageframe`** — copy seluruh isi dari `data\screen\messageframe\` ke `[folder game]\data\screen\messageframe\`. Timpa semua file SVG.

> Untuk **Horizontal Patch**, gunakan folder `messageframe` dari repo Horizontal. Semua SVG di dalamnya sudah dikonfigurasi dengan tipe `normal` (teks horizontal).

---

## Troubleshooting

**`python: can't open file 'dat_pack.py'`**
CMD di folder yang salah. Pindah ke `dependencies\`.

**`PermissionError: [WinError 5]` saat `dat_pack.py`**
Jalankan tanpa path argumen akan membuka dialog dan bisa salah pilih folder. Selalu pakai path eksplisit:
```bat
python dat_pack.py "C:\...\KKK-main\data"
```

**`DirectoryNotFoundException: .data\system\exec.dat`**
Folder `.data\system\` belum ada. Buat dulu:
```bat
mkdir "malie tools\compilar\Malie_Script_Tool-main\bin\Debug\.data\system"
```

**Teks di game masih Jepang setelah patch**
Pastikan `data6.dat` dicopy ke folder instalasi game yang benar dan kamu menggunakan `malie.exe` dari `[KT] KKK\`.

---

## Struktur Folder

```
KKK-main\
│
├── [KT] KKK\
│   ├── malie.exe              ← jalankan game dari sini
│   └── malie.ini              ← dicopy ke folder game sekali saja
│
├── data\
│   └── screen\
│       └── messageframe\      ← SVG kotak dialog (dicopy ke game sekali saja)
│
└── dependencies\
    │
    ├── wordwrap.py            ← potong baris panjang otomatis (opsional)
    ├── dat_pack.py            ← pack jadi data6.dat
    │
    ├── script\                ← buat jika memakai Opsi B
    │   └── message.txt
    ├── script_done\           ← hasil wordwrap (Opsi B)
    │
    └── malie tools\
        └── compilar\
            └── Malie_Script_Tool-main\bin\Debug\
                │
                ├── Malie_Script_Tool.exe
                │
                ├── data\system\
                │   ├── exec.msg.txt    ← dialog utama
                │   ├── exec.str.txt    ← nama karakter & string UI
                │   └── exec.org.dat    ← skrip original, jangan diubah
                │
                └── .data\system\       ← BUAT MANUAL (Vertikal); sudah ada (Horizontal)
                    └── exec.dat        ← hasil compile
```

---

Kredit: Tooling oleh Monaco A. Knox. Referensi: [Dies Irae](https://github.com/Monaco-a-Knox/amantesamentes).
