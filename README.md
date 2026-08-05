# BrainScan AI — Backend (FastAPI Cloud + ONNX Runtime + Turso(database))

## Stack final
- **Compute**: FastAPI Cloud
- **Model AI**: ONNX Runtime (bukan PyTorch lagi) — model di-download dari
  Hugging Face (`delfidev/brain-hybrid-efficientnet-vit`) saat startup
- **Database**: Turso (SQLite-compatible, cloud)
- **Laporan otomatis**: Gemini API

## Environment Variables yang WAJIB di-set
Di tab **Environment Variables** FastAPI Cloud:

| Key | Isi |
|---|---|
| `GEMINI_API_KEY` | API key dari aistudio.google.com |
| `TURSO_DATABASE_URL` | Connection URL Turso, format `libsql://nama-db.turso.io` |
| `TURSO_AUTH_TOKEN` | Token dari `turso db tokens create <nama-db>` |
| `HF_TOKEN` | (opsional) token Hugging Face tipe Read, biar nggak kena rate limit |

## Catatan penting
- Model `.onnx` yang dipakai: **FP32** untuk kedua model (precheck & hybrid).
  INT8/FP16 pernah dicoba tapi TERBUKTI bermasalah (akurasi jatuh drastis
  atau gagal di-load sama sekali) -- JANGAN diganti balik ke INT8/FP16
  tanpa validasi akurasi ulang yang ketat.
- `src/api.py` pakai `_low_memory_session_options()` untuk ONNX Runtime,
  supaya muat di limit RAM kecil (500MB). Kalau masih OOM/"Killed" di log,
  itu tandanya limit RAM plan kamu perlu dinaikkan (bukan lagi masalah kode).
- File `PyTorch` (classifier_model.py, precheck_model.py, fusion_blocks.py)
  sudah DIHAPUS dari project ini -- sudah tidak dipakai lagi setelah
  migrasi penuh ke ONNX Runtime.
