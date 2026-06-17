# Pseudocode WASTE-TRACK

## Struktur Data

```
STRUCTURE Transaksi
  date  : STRING
  jenis : STRING
  berat : FLOAT
ENDSTRUCTURE

STRUCTURE warga
  name       : STRING
  id         : INTEGER
  setoran    : ARRAY OF Transaksi
  jumlahLog  : INTEGER
  totalBerat : FLOAT
ENDSTRUCTURE

DECLARE dataWarga   : ARRAY OF warga
DECLARE jenisSampah : ARRAY OF STRING
```

---

## Main Program

```
PROCEDURE main()
  DECLARE run    : BOOLEAN ← TRUE
  DECLARE choice : STRING

  WHILE run = TRUE DO
    CALL printFeature()
    OUTPUT "masukkan pilihan : "
    INPUT choice
    CALL evalChoice(choice, run)
  ENDWHILE

  OUTPUT "Terimakasih"
ENDPROCEDURE
```

---

## Menu Utama

```
PROCEDURE printFeature()
  OUTPUT "=========== WASTE-TRACK ==========="
  OUTPUT "|1. Manajemen Data Warga          |"
  OUTPUT "|2. Catat Setoran Sampah          |"
  OUTPUT "|3. Cari Data Warga               |"
  OUTPUT "|4. Urutkan Data Warga            |"
  OUTPUT "|5. Tampilkan Statistik           |"
  OUTPUT "|9. Exit                          |"
  OUTPUT "==================================="
ENDPROCEDURE
```

```
PROCEDURE evalChoice(choice : STRING, run : BOOLEAN)
  CASE choice OF
    "1" : CALL menuCRUDWarga()
    "2" : CALL catatSetoran()
    "3" : CALL menuCariWarga()
    "4" : CALL menuUrutWarga()
    "5" : CALL tampilkanStatistik()
    "9" : run ← FALSE
  ENDCASE
ENDPROCEDURE
```

---

## 1. Manajemen Data Warga

```
PROCEDURE CRUDWarga()
  OUTPUT "=== MANAJEMEN DATA WARGA ==="
  OUTPUT "|1. Tampilkan Data Warga   |"
  OUTPUT "|2. Tambah Warga           |"
  OUTPUT "|3. Edit Data Warga        |"
  OUTPUT "|4. Hapus Data Warga       |"
  OUTPUT "|7. Exit                   |"
  OUTPUT "============================"
ENDPROCEDURE
```

```
PROCEDURE menuCRUDWarga()
  DECLARE subRun    : BOOLEAN ← TRUE
  DECLARE subChoice : STRING

  WHILE subRun = TRUE DO
    CALL CRUDWarga()
    OUTPUT "Masukkan pilihan menu warga: "
    INPUT subChoice

    CASE subChoice OF
      "1" : CALL showDataWarga()
      "2" : CALL tambahWarga()
      "3" : CALL editWarga()
      "4" : CALL hapusWarga()
      "7" :
          subRun ← FALSE
          OUTPUT ""
    ENDCASE
  ENDWHILE
ENDPROCEDURE
```

```
PROCEDURE showDataWarga()
  IF LENGTH(dataWarga) = 0 THEN
    OUTPUT "Data warga kosong"
    RETURN
  ENDIF

  OUTPUT "=== DATA WARGA ==="
  FOR i ← 0 TO LENGTH(dataWarga) - 1 DO
    OUTPUT "Nama       : " + dataWarga[i].name
    OUTPUT "ID         : " + dataWarga[i].id
    OUTPUT "Jumlah Log : " + dataWarga[i].jumlahLog
    OUTPUT "Total Berat: " + dataWarga[i].totalBerat
  ENDFOR
ENDPROCEDURE
```

```
FUNCTION findIndexByID(id : INTEGER) : INTEGER
  FOR i ← 0 TO LENGTH(dataWarga) - 1 DO
    IF dataWarga[i].id = id THEN
      RETURN i
    ENDIF
  ENDFOR
  RETURN -1
ENDFUNCTION
```

```
PROCEDURE tambahWarga()
  DECLARE newWarga : warga

  OUTPUT "Masukkan ID Warga (Angka): "
  INPUT newWarga.id

  IF findIndexByID(newWarga.id) ≠ -1 THEN
    OUTPUT "ID sudah terdaftar"
    RETURN
  ENDIF

  OUTPUT "Masukkan Nama Warga: "
  INPUT newWarga.name

  newWarga.jumlahLog  ← 0
  newWarga.totalBerat ← 0.0

  APPEND newWarga TO dataWarga
  OUTPUT "Warga berhasil ditambahkan"
ENDPROCEDURE
```

```
PROCEDURE editWarga()
  DECLARE targetID : INTEGER

  OUTPUT "Masukkan ID Warga yang akan diedit: "
  INPUT targetID

  DECLARE idx : INTEGER ← findIndexByID(targetID)

  IF idx = -1 THEN
    OUTPUT "Warga tidak ditemukan"
    RETURN
  ENDIF

  OUTPUT "Masukkan Nama Baru: "
  INPUT dataWarga[idx].name

  OUTPUT "Data warga berhasil diperbarui"
ENDPROCEDURE
```

```
PROCEDURE hapusWarga()
  DECLARE targetID : INTEGER

  OUTPUT "Masukkan ID Warga yang akan dihapus: "
  INPUT targetID

  DECLARE idx : INTEGER ← findIndexByID(targetID)

  IF idx = -1 THEN
    OUTPUT "Warga tidak ditemukan"
    RETURN
  ENDIF

  OUTPUT "Nama : " + dataWarga[idx].name
  OUTPUT "ID   : " + dataWarga[idx].id

  REMOVE dataWarga[idx] FROM dataWarga
  OUTPUT "Data warga berhasil dihapus."
ENDPROCEDURE
```

---

## 2. Catat Setoran Sampah

```
PROCEDURE catatSetoran()
  DECLARE targetID : INTEGER

  OUTPUT "Masukkan ID Warga: "
  INPUT targetID

  DECLARE idx : INTEGER ← findIndexByID(targetID)

  IF idx = -1 THEN
    OUTPUT "Warga tidak ditemukan."
    RETURN
  ENDIF

  OUTPUT "=== Jenis Sampah yang Terdata ==="
  IF LENGTH(jenisSampah) = 0 THEN
    OUTPUT "Belum ada jenis sampah yang terdata"
  ELSE
    FOR i ← 0 TO LENGTH(jenisSampah) - 1 DO
      OUTPUT (i+1) + ". " + jenisSampah[i]
    ENDFOR
  ENDIF

  OUTPUT "Pilih/Ketik Jenis Sampah:"
  DECLARE pilJenis : STRING
  INPUT pilJenis

  DECLARE exist : BOOLEAN ← FALSE
  FOR EACH jenis IN jenisSampah DO
    IF jenis = pilJenis THEN
      exist ← TRUE
      BREAK
    ENDIF
  ENDFOR

  IF exist = FALSE THEN
    APPEND pilJenis TO jenisSampah
    OUTPUT "Jenis sampah baru '" + pilJenis + "' berhasil ditambahkan"
  ENDIF

  DECLARE berat      : FLOAT
  DECLARE tgl, bln, thn : INTEGER

  OUTPUT "Masukkan Berat Sampah (kg): "
  INPUT berat

  OUTPUT "Masukkan Tanggal (dd mm yyyy): "
  INPUT tgl, bln, thn

  DECLARE tanggalFormat : STRING ← FORMAT("%02d-%02d-%04d", tgl, bln, thn)

  DECLARE newTx : Transaksi
  newTx.date  ← tanggalFormat
  newTx.jenis ← pilJenis
  newTx.berat ← berat

  APPEND newTx TO dataWarga[idx].setoran
  dataWarga[idx].jumlahLog  ← dataWarga[idx].jumlahLog + 1
  dataWarga[idx].totalBerat ← dataWarga[idx].totalBerat + berat

  OUTPUT "Setoran Sampah berhasil dicatat pada tanggal " + tanggalFormat
ENDPROCEDURE
```

---

## 3. Pencarian Data Warga

```
PROCEDURE menuCariWarga()
  OUTPUT "=== PENCARIAN DATA WARGA ==="
  OUTPUT "|1. Cari Berdasarkan Nama   |"
  OUTPUT "|2. Cari Berdasarkan ID     |"
  OUTPUT "============================"
  OUTPUT "Pilih pencarian (1-2): "

  DECLARE sub : STRING
  INPUT sub

  IF sub = "1" THEN
    DECLARE query : STRING
    OUTPUT "Masukkan Nama Warga yang dicari: "
    INPUT query
    CALL sequentialSearch(query)

  ELSE IF sub = "2" THEN
    DECLARE queryID : INTEGER
    OUTPUT "Masukkan ID Warga yang dicari: "
    INPUT queryID
    CALL binarySearch(queryID)

  ELSE
    OUTPUT "Pilihan tidak valid."
  ENDIF
ENDPROCEDURE
```

```
PROCEDURE sequentialSearch(query : STRING)
  // Sequential Search — O(n)
  DECLARE found : BOOLEAN ← FALSE

  FOR i ← 0 TO LENGTH(dataWarga) - 1 DO
    IF dataWarga[i].name = query THEN
      OUTPUT "[Ditemukan] ID: " + dataWarga[i].id +
             " | Nama: " + dataWarga[i].name +
             " | Total: " + dataWarga[i].totalBerat + " kg"
      found ← TRUE
    ENDIF
  ENDFOR

  IF found = FALSE THEN
    OUTPUT "Data warga tidak ditemukan."
  ENDIF
ENDPROCEDURE
```

```
PROCEDURE binarySearch(queryID : INTEGER)
  // Binary Search — O(log n), requires sorted data
  DECLARE tempData : ARRAY OF warga
  COPY dataWarga INTO tempData

  // Selection Sort pada tempData berdasarkan ID (ascending)
  FOR i ← 0 TO LENGTH(tempData) - 2 DO
    DECLARE minIdx : INTEGER ← i
    FOR j ← i + 1 TO LENGTH(tempData) - 1 DO
      IF tempData[j].id < tempData[minIdx].id THEN
        minIdx ← j
      ENDIF
    ENDFOR
    SWAP tempData[i] WITH tempData[minIdx]
  ENDFOR

  // Binary Search
  DECLARE low   : INTEGER ← 0
  DECLARE high  : INTEGER ← LENGTH(tempData) - 1
  DECLARE found : BOOLEAN ← FALSE

  WHILE low ≤ high DO
    DECLARE mid : INTEGER ← (low + high) / 2

    IF tempData[mid].id = queryID THEN
      OUTPUT "[Ditemukan via Binary] ID: " + tempData[mid].id +
             " | Nama: " + tempData[mid].name +
             " | Total: " + tempData[mid].totalBerat + " kg"
      found ← TRUE
      BREAK

    ELSE IF tempData[mid].id < queryID THEN
      low ← mid + 1

    ELSE
      high ← mid - 1
    ENDIF
  ENDWHILE

  IF found = FALSE THEN
    OUTPUT "Data warga tidak ditemukan."
  ENDIF
ENDPROCEDURE
```

---

## 4. Pengurutan Data Warga

```
PROCEDURE menuUrutWarga()
  OUTPUT "==== PENGURUTAN DATA WARGA ===="
  OUTPUT "|1. Urutkan dengan Ascending  |"
  OUTPUT "|2. Urutkan dengan Descending |"
  OUTPUT "==============================="
  OUTPUT "Pilih metode (1-2): "

  DECLARE sub : STRING
  INPUT sub

  IF sub = "1" THEN
    IF LENGTH(dataWarga) = 0 THEN
      CALL showDataWarga()
      RETURN
    ENDIF
    CALL selectionSort()
    OUTPUT "Data diurutkan dengan Selection Sort secara Ascending."
    CALL showDataWarga()

  ELSE IF sub = "2" THEN
    IF LENGTH(dataWarga) = 0 THEN
      CALL showDataWarga()
      RETURN
    ENDIF
    CALL insertionSort()
    OUTPUT "Data diurutkan dengan Insertion Sort secara Descending."
    CALL showDataWarga()
  ENDIF
ENDPROCEDURE
```

```
PROCEDURE selectionSort()
  // Selection Sort Ascending berdasarkan totalBerat — O(n²)
  FOR i ← 0 TO LENGTH(dataWarga) - 2 DO
    DECLARE minIdx : INTEGER ← i
    FOR j ← i + 1 TO LENGTH(dataWarga) - 1 DO
      IF dataWarga[j].totalBerat < dataWarga[minIdx].totalBerat THEN
        minIdx ← j
      ENDIF
    ENDFOR
    SWAP dataWarga[i] WITH dataWarga[minIdx]
  ENDFOR
ENDPROCEDURE
```

```
PROCEDURE insertionSort()
  // Insertion Sort Descending berdasarkan totalBerat — O(n²)
  FOR i ← 1 TO LENGTH(dataWarga) - 1 DO
    DECLARE key : warga ← dataWarga[i]
    DECLARE j   : INTEGER ← i - 1

    WHILE j ≥ 0 AND dataWarga[j].totalBerat < key.totalBerat DO
      dataWarga[j + 1] ← dataWarga[j]
      j ← j - 1
    ENDWHILE

    dataWarga[j + 1] ← key
  ENDFOR
ENDPROCEDURE
```

---

## 5. Tampilkan Statistik

```
PROCEDURE tampilkanStatistik()
  OUTPUT "====== WASTE-TRACK STATISTIK ======"
  OUTPUT "|1. Statistik Berdasarkan Minggu  |"
  OUTPUT "|2. Statistik Berdasarkan Bulan   |"
  OUTPUT "|3. Statistik Berdasarkan Tahun   |"
  OUTPUT "==================================="
  OUTPUT "Pilih filter statistik (1-3): "

  DECLARE pilihan      : STRING
  DECLARE targetPeriode : STRING
  DECLARE labelPeriode  : STRING
  DECLARE minggu        : INTEGER
  DECLARE filterMinggu  : BOOLEAN ← FALSE

  INPUT pilihan

  IF pilihan = "1" THEN
    DECLARE bulan, tahun : INTEGER
    OUTPUT "Masukkan Minggu Statistik (1-4): "  → INPUT minggu
    OUTPUT "Masukkan Bulan Statistik (1-12): "  → INPUT bulan
    OUTPUT "Masukkan Tahun Statistik: "          → INPUT tahun
    targetPeriode ← FORMAT("-%02d-%04d", bulan, tahun)
    labelPeriode  ← FORMAT("Minggu %d, Bulan %02d-%d", minggu, bulan, tahun)
    filterMinggu  ← TRUE

  ELSE IF pilihan = "2" THEN
    DECLARE bln, thn : INTEGER
    OUTPUT "Masukkan Bulan Statistik (1-12): " → INPUT bln
    OUTPUT "Masukkan Tahun Statistik: "         → INPUT thn
    targetPeriode ← FORMAT("-%02d-%04d", bln, thn)
    labelPeriode  ← FORMAT("Bulan %02d-%d", bln, thn)

  ELSE IF pilihan = "3" THEN
    DECLARE thn : INTEGER
    OUTPUT "Masukkan Tahun Statistik: " → INPUT thn
    targetPeriode ← FORMAT("-%04d", thn)
    labelPeriode  ← FORMAT("Tahun %d", thn)

  ELSE
    OUTPUT "Pilihan tidak valid."
    RETURN
  ENDIF

  DECLARE totalSemua      : FLOAT ← 0.0
  DECLARE beratJenisSampah : ARRAY OF FLOAT [LENGTH(jenisSampah)]
  DECLARE logPerTanggal   : MAP OF STRING → ARRAY OF STRING
  DECLARE urutanTanggal   : ARRAY OF STRING

  FOR i ← 0 TO LENGTH(dataWarga) - 1 DO
    FOR j ← 0 TO LENGTH(dataWarga[i].setoran) - 1 DO
      DECLARE tx : Transaksi ← dataWarga[i].setoran[j]

      IF SUFFIX(tx.date, LENGTH(targetPeriode)) = targetPeriode THEN

        IF filterMinggu = TRUE THEN
          DECLARE tgl    : INTEGER ← PARSE_INT(tx.date[0:2])
          DECLARE mingguTx : INTEGER

          IF tgl ≥ 1 AND tgl ≤ 7 THEN
            mingguTx ← 1
          ELSE IF tgl ≥ 8 AND tgl ≤ 14 THEN
            mingguTx ← 2
          ELSE IF tgl ≥ 15 AND tgl ≤ 21 THEN
            mingguTx ← 3
          ELSE
            mingguTx ← 4
          ENDIF

          IF mingguTx ≠ minggu THEN
            CONTINUE
          ENDIF
        ENDIF

        totalSemua ← totalSemua + tx.berat

        FOR k ← 0 TO LENGTH(jenisSampah) - 1 DO
          IF tx.jenis = jenisSampah[k] THEN
            beratJenisSampah[k] ← beratJenisSampah[k] + tx.berat
          ENDIF
        ENDFOR

        DECLARE teksLog : STRING ←
          "Warga: " + dataWarga[i].name +
          ", Jenis: " + tx.jenis +
          ", Berat: " + tx.berat + " kg"

        IF logPerTanggal[tx.date] IS EMPTY THEN
          APPEND tx.date TO urutanTanggal
        ENDIF

        APPEND teksLog TO logPerTanggal[tx.date]
      ENDIF
    ENDFOR
  ENDFOR

  OUTPUT "====== DETAIL LOG TRANSAKSI (" + labelPeriode + ") ======"

  IF LENGTH(urutanTanggal) = 0 THEN
    OUTPUT "Tidak ada transaksi pada periode ini."
  ELSE
    FOR EACH tgl IN urutanTanggal DO
      OUTPUT tgl + ":"
      FOR idx ← 0 TO LENGTH(logPerTanggal[tgl]) - 1 DO
        OUTPUT (idx+1) + ". " + logPerTanggal[tgl][idx]
      ENDFOR
    ENDFOR
  ENDIF

  OUTPUT "Statistik Akumulasi Sampah " + labelPeriode + ":"

  IF LENGTH(jenisSampah) = 0 THEN
    OUTPUT "Tidak ada jenis sampah yang terdata."
  ELSE
    FOR i ← 0 TO LENGTH(jenisSampah) - 1 DO
      OUTPUT "- Sampah " + jenisSampah[i] + ": " + beratJenisSampah[i] + " kg"
    ENDFOR
  ENDIF

  OUTPUT "TOTAL KESELURUHAN: " + totalSemua + " kg"
ENDPROCEDURE
```
