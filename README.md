# PPDB MI ISLAMADINA 2026–2027

Aplikasi Pendaftaran Peserta Didik Baru (PPDB) berbasis web modern, responsif, dan tanpa server backend (Serverless).

## 🚀 Cara Install & Deploy

### 1. Cloudflare Pages
Aplikasi ini dirancang untuk berjalan di **Cloudflare Pages**.
1. Masuk ke dashboard Cloudflare Pages.
2. Buat Project baru.
3. Upload folder project ini (atau connect ke Git repository).
4. Deploy! (Tidak perlu build command, ini hanya HTML static).

### 2. Integrasi Google Sheets (Wajib)
Agar data tersimpan, Anda harus menyiapkan backend Google Apps Script.

**Langkah-langkah:**

1. Buka [Google Sheets](https://sheets.google.com) baru.
2. Beri nama sheet, misal "Data PPDB 2026".
3. Di baris pertama (Header), buat kolom berikut secara berurutan (pastikan tulisannya sama persis atau mendekati):
   - `timestamp`
   - `nama_lengkap`
   - `nik_siswa`
   - `no_kk`
   - `tempat_lahir`
   - `tgl_lahir`
   - `jenis_kelamin`
   - `asal_sekolah`
   - `asal_sekolah_lainnya`
   - `anak_ke`
   - `jml_sdr_kandung`
   - `jml_sdr_tiri`
   - `jml_sdr_angkat`
   - `bahasa`
   - `berat_badan`
   - `tinggi_badan`
   - `hobi`
   - `cita_cita`
   - `pilihan_ortu_wali`
   - `nama_ayah`
   - `nik_ayah`
   - `status_ayah`
   - `tempat_lahir_ayah`
   - `tgl_lahir_ayah`
   - `pendidikan_ayah`
   - `pekerjaan_ayah`
   - `penghasilan_ayah`
   - `nohp_ayah`
   - `nama_ibu`
   - `nik_ibu`
   - `status_ibu`
   - `tempat_lahir_ibu`
   - `tgl_lahir_ibu`
   - `pendidikan_ibu`
   - `pekerjaan_ibu`
   - `penghasilan_ibu`
   - `nohp_ibu`
   - `nama_wali`
   - `hubungan_wali`
   - `pekerjaan_wali`
   - `nohp_wali`
   - `alamat_lengkap`
   - `status_tempat_tinggal`
   - `transportasi`
   - `jarak`
   - `waktu_tempuh`

4. Klik **Extensions** > **Apps Script**.
5. Hapus kode yang ada, copy & paste kode di bawah ini:

```javascript
var sheetName = 'Sheet1';
var scriptProp = PropertiesService.getScriptProperties();

function intialSetup() {
  var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet();
  scriptProp.setProperty('key', activeSpreadsheet.getId());
}

function doPost(e) {
  var lock = LockService.getScriptLock();
  lock.tryLock(10000);

  try {
    var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'));
    var sheet = doc.getSheetByName(sheetName);

    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
    var nextRow = sheet.getLastRow() + 1;

    var newRow = headers.map(function(header) {
      if (header === 'timestamp') {
        return new Date();
      }
      return e.parameter[header];
    });

    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow]);

    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'success', 'row': nextRow }))
      .setMimeType(ContentService.MimeType.JSON);
  }

  catch (e) {
    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'error', 'error': e }))
      .setMimeType(ContentService.MimeType.JSON);
  }

  finally {
    lock.releaseLock();
  }
}
```

6. Simpan project (E.g "PPDB Backend").
7. **PENTING**: Jalankan fungsi `intialSetup` sekali. (Pilih `intialSetup` di menu dropdown toolbar, klik Run/Jalankan). Berikan izin akses yg diminta.
8. Klik tombol **Deploy** > **New Deployment**.
9. Pilih type **Web App**.
   - Description: "Versi 1"
   - Execute as: **Me**
   - Who has access: **Anyone** (Ini penting agar form HTML bisa mengirim data).
10. Klik **Deploy**, copy **Web App URL**.

### 3. Update File `index.html`
Buka file `index.html` di project ini.
Cari baris:
`const SCRIPT_URL = 'YOUR_GOOGLE_SCRIPT_URL_HERE';`
Ganti dengan URL yang Anda copy dari langkah deploy tadi.

---
Dikembangkan untuk MI Islamadina.
Teknologi: HTML5, CSS3, Vanilla JS.
