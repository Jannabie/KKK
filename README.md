# Kajiri Kamui Kagura — ToolKit

Repositori ini berisi semua tools yang dibutuhkan untuk menerjemahkan Kajiri Kamui Kagura ke Bahasa Indonesia menggunakan Malie Script Tool.

---

## Status Terjemahan

| Komponen | Status | Keterangan |
|----------|--------|------------|
| Skrip Utama (`exec.msg.txt`) | Dalam Proses | Dialog utama game |
| Menu & Pilihan (`exec.str.txt`) | Dalam Proses | Teks antarmuka |
| Title Screen / UI Frame | Selesai | SVG messageframe sudah dimodifikasi |
| Patch Dapat Dijalankan | Ya | — |

| Kajiri Kamui Kagura Akebono no Hikari (神咒神威神楽 曙之光 ダウンロード版) |
|:---:|
| ![Translation working in-game](https://i.imgur.com/x4kWxLV.jpeg) |

---

## Hal Wajib Sebelum Mulai

Baca bagian ini dulu. Banyak error yang terjadi karena melewatkan dua poin ini.

**1. Gunakan `malie.exe` dan `malie.ini` dari repo ini**

Folder `[KT] KKK\` di repo berisi dua file kritis:

```
[KT] KKK\
├── malie.exe     ← pakai ini untuk jalankan game, tidak perlu AlphaROMdiE
└── malie.ini     ← copy ke folder instalasi game, mengatur font dan judul window
```

**2. Buat folder `.data\system` secara manual**

Folder ini tidak dibuat otomatis oleh tool apapun. Tanpanya proses compile akan gagal.

Buka CMD dan jalankan perintah ini sekali saja:

```bat
mkdir "dependencies\malie tools\compilar\Malie_Script_Tool-main\bin\Debug\.data\system"
```

---

## Kebutuhan

- **Python** — [python.org](https://python.org), centang **"Add to PATH"** saat install
- **Notepad++** — [notepad-plus-plus.org](https://notepad-plus-plus.org)
- Game KKK sudah terinstall

---

## Format File Terjemahan

File yang diterjemahkan adalah `exec.msg.txt`. Buka dengan Notepad++ dan pastikan encoding di pojok kanan bawah menunjukkan **UTF-8** (bukan UTF-8-BOM).

Isinya berupa pasangan baris seperti ini:

```
◇00000002◇　振り下ろす一閃――[z]
◆00000002◆　Tebasan yang menghujam――[z]
```

| Simbol | Fungsi |
|--------|--------|
| `◇` | Teks Jepang asli — jangan diubah sama sekali |
| `◆` | Baris terjemahan — ini yang diedit |
| `[z]` | Penanda akhir dialog, wajib ada |
| `[c]` | Jeda, tunggu klik dari player |
| `[n]` | Baris baru manual |
| `[s]` | Penanda suara/voice |

Aturan:
- Hanya edit baris yang diawali `◆`
- Jangan hapus penanda `[z]`, `[c]`, `[n]`, `[s]`
- Jangan ubah nomor ID seperti `◆00000002◆`

---

## Alur Kerja

Ada dua cara. Pilih salah satu.

---

### Cara A — Edit Langsung (Tanpa Wordwrap)

Cara ini lebih simpel. Kamu atur baris baru sendiri pakai `[n]`.

Edit file ini langsung:

```
dependencies\malie tools\compilar\Malie_Script_Tool-main\bin\Debug\data\system\exec.msg.txt
```

Usahakan tidak lebih dari ~25 karakter per baris. Gunakan `[n]` untuk memotong baris:

```
◆00000003◆　Serangan itu bukan sekadar[n]tebasan biasa――[z]
```

Setelah selesai, lanjut ke bagian **Compile & Pack**.

---

### Cara B — Lewat Folder `script` + Wordwrap Otomatis

Cara ini menggunakan `wordwrap.py` untuk memotong baris panjang secara otomatis. `wordwrap.py` bersifat opsional, kamu tidak wajib memakai ini.

**Setup tambahan yang wajib untuk Cara B:**

Buat folder `script` di dalam `dependencies\`:

```bat
mkdir "dependencies\script"
```

Tanpa folder ini, `wordwrap.py` tidak akan bisa jalan.

**Alur:**

1. Copy file ini:
   ```
   dependencies\malie tools\compilar\Malie_Script_Tool-main\bin\Debug\data\system\exec.msg.txt
   ```
   Paste ke `dependencies\script\`, rename jadi `message.txt`.

2. Edit `dependencies\script\message.txt` dengan Notepad++.

3. Jalankan wordwrap dari folder `dependencies\`:
   ```bat
   python wordwrap.py
   ```
   Hasilnya muncul di `dependencies\script_done\message.txt`.

4. Copy hasil ke compile tool:
   ```bat
   copy "script_done\message.txt" "malie tools\compilar\Malie_Script_Tool-main\bin\Debug\data\system\exec.msg.txt"
   ```

Lanjut ke bagian **Compile & Pack**.

---

## Compile & Pack

Pastikan CMD kamu berada di folder `dependencies\`:

```bat
cd "C:\Users\user\Downloads\KKK exe manipulator\KKK-main\dependencies"
```

**Step 1 — Compile skrip**

```bat
"malie tools\compilar\Malie_Script_Tool-main\bin\Debug\Malie_Script_Tool.exe"
```

Output berupa `exec.dat` yang muncul di:
```
...bin\Debug\.data\system\exec.dat
```

Jika muncul error `DirectoryNotFoundException`, kamu belum membuat folder `.data\system`. Lihat bagian **Hal Wajib** di atas.

**Step 2 — Pack jadi `data6.dat`**

```bat
python dat_pack.py "C:\Users\user\Downloads\KKK exe manipulator\KKK-main\data"
```

Wajib sertakan path lengkap folder `data`. Jika dijalankan tanpa argumen, akan muncul dialog dan script bisa gagal dengan `PermissionError`.

File `data6.dat` muncul di folder `dependencies\`.

**Step 3 — Pasang ke game**

```bat
copy data6.dat "C:\[folder instalasi game kamu]\data6.dat"
```

---

## Instalasi Patch (Lakukan Sekali Saja)

Selain `data6.dat`, ada dua hal lagi yang perlu dicopy ke folder game satu kali di awal.

**`malie.ini`**

Copy dari `[KT] KKK\malie.ini` ke root folder instalasi game. Timpa file yang sudah ada.

**Folder `messageframe`**

Copy seluruh isi dari `data\screen\messageframe\` ke folder game di `[folder game]\data\screen\messageframe\`. Jika foldernya belum ada, buat dulu. Timpa semua file SVG yang ada.

---

## Troubleshooting

**`python: can't open file 'dat_pack.py'`**
CMD berada di folder yang salah. Pindah dulu:
```bat
cd "C:\Users\user\Downloads\KKK exe manipulator\KKK-main\dependencies"
```

**`PermissionError: [WinError 5] Access is denied` saat `dat_pack.py`**
Jalankan tanpa argumen path menyebabkan dialog terbuka dan salah pilih folder. Selalu sertakan path secara eksplisit:
```bat
python dat_pack.py "C:\Users\user\Downloads\KKK exe manipulator\KKK-main\data"
```

**`DirectoryNotFoundException: .data\system\exec.dat`**
Folder `.data\system\` belum ada. Jalankan:
```bat
mkdir "malie tools\compilar\Malie_Script_Tool-main\bin\Debug\.data\system"
```
Lalu ulangi Step 1.

**Teks di game masih Jepang setelah patch**
Pastikan `data6.dat` dicopy ke folder instalasi game yang benar, bukan ke folder repo. Pastikan juga kamu menggunakan `malie.exe` dari folder `[KT] KKK\`.

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
│       └── messageframe\      ← SVG kotak dialog, dicopy ke folder game sekali saja
│
└── dependencies\
    │
    ├── script\                ← buat folder ini jika memakai Cara B
    │   └── message.txt        ← file kerja terjemahan (Cara B)
    │
    ├── script_done\           ← hasil wordwrap otomatis (Cara B)
    │
    ├── wordwrap.py            ← potong baris panjang otomatis (opsional)
    ├── dat_pack.py            ← pack jadi data6.dat
    │
    └── malie tools\
        └── compilar\
            └── Malie_Script_Tool-main\bin\Debug\
                │
                ├── Malie_Script_Tool.exe
                │
                ├── data\system\
                │   ├── exec.msg.txt    ← edit langsung di sini (Cara A)
                │   ├── exec.str.txt    ← teks menu dan pilihan
                │   └── exec.org.dat    ← skrip original, jangan diubah
                │
                └── .data\system\       ← BUAT MANUAL (lihat Hal Wajib)
                    └── exec.dat        ← hasil compile
```

---

Kredit: Tooling oleh Monaco A. Knox. Referensi: [Dies Irae](https://github.com/Monaco-a-Knox/amantesamentes).
