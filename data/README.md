# data/

Ham ve işlenmiş ses/özellik verisinin tutulduğu klasör.

## Alt Klasörler

- `raw/` — UART'tan gelen ham CSV özellik vektörü kayıtları (ve varsa ham
  ses kayıtları). Büyük/binary olduğu için `.gitignore` ile hariç
  tutulur; sadece `.gitkeep` ile klasör yapısı repoda tutulur.
- `processed/` — İşlenmiş/etiketlenmiş, eğitime hazır veri (opsiyonel).

Sınıflar: `sessizlik`, `islik`, `alkis`.
