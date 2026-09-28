# training/

Python veri toplama ve Keras eğitim scriptleri buraya gelecek.

## Beklenen Dosya Yapısı

- `collect_data.py` — USART2/USB-TTL adaptör üzerinden kart tarafından
  gönderilen CSV özellik vektörlerini okuyup etiketleyerek `data/` altına
  kaydeden script.
- `train.py` — `data/` altındaki etiketli özellik vektörleriyle Keras
  modeli eğiten script.
- `models/` — Eğitim sırasında üretilen ara model dosyaları
  (`.gitignore` ile hariç tutulur; `models/final/` altındaki son model
  dosyaları hariç).

## Not

Eğitimde kullanılan özellik temsili (FFT bant enerjileri), firmware
tarafında CMSIS-DSP ile hesaplanan özellik vektörleriyle **birebir aynı**
olmalıdır (bkz. kök `README.md` → Mimari Notu).
