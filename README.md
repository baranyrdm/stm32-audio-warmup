# STM32 Audio Warmup

STM32F407G-DISC1 kartı üzerindeki kart üstü MP45DT02 dijital PDM mikrofonuyla
gerçek zamanlı ses sınıflandırması yapan 2 haftalık bir ısınma (warmup)
projesi. Hedef üç sınıf: **sessizlik**, **ıslık** ve **alkış**.

Amaç; STM32CubeMX ile donanım konfigürasyonunu kurmak, PC tarafında Python ile
veri toplayıp Keras ile küçük bir sınıflandırıcı eğitmek ve X-CUBE-AI ile bu
modeli STM32F407 üzerinde gerçek zamanlı çalıştırmaktır.

## Donanım

- **Kart:** STM32F407G-DISC1 (STM32F407VG, Cortex-M4F, FPU)
- **Mikrofon:** Kart üstü MP45DT02 dijital PDM MEMS mikrofon
  - STM32F407'de SAI veya DFSDM periferi **yoktur**. PDM verisi
    **I2S2 (half-duplex master receive) + DMA** ile okunur ve yazılım
    tarafında **PDM2PCM** kütüphanesiyle PCM'e dönüştürülür.
  - Başlangıç noktası: STM32CubeF4 paketinde `Audio_playback_and_record`
    adıyla arayın (klasör yolu paket sürümüne göre değişebilir); mikrofon
    kayıt kısmı referans alınacak.
- **PC iletişimi:** Kartta ST-LINK üzerinden sanal COM portu **yoktur**.
  PC ile haberleşme **USART2 (PA2/PA3)** üzerinden, harici bir **3.3V
  USB-TTL (USB-seri) adaptör** ile yapılır.
- **Programlama/Debug:** Kart üstü entegre ST-LINK/V2

## Araçlar

- STM32CubeIDE
- STM32CubeMX
- X-CUBE-AI (Cube.AI eklentisi)
- Python 3 + Keras/TensorFlow
- CMSIS-DSP (MCU üzerinde FFT/özellik çıkarımı için)
- pyserial, numpy, tensorflow/keras (UART CSV okuma ve eğitim)

## Mimari Notu: Özellik Çıkarımı Nerede Yapılıyor?

Eğitim ve gerçek zamanlı çıkarım arasında tutarlılığı garanti altına almak
için özellik çıkarımı (FFT tabanlı bant enerjileri) **kartın üzerinde
CMSIS-DSP ile** yapılır. PC'ye sadece ham ses değil, **hesaplanmış özellik
vektörleri UART üzerinden CSV formatında** gönderilir. Böylece Python
tarafında eğitilen model, MCU'daki çıkarım sırasında görülen özellik
vektörleriyle birebir aynı temsille çalışır.

## 14 Günlük Plan (Taslak)

> Not: Bu plan bir **taslaktır**; ilerleme ve karşılaşılan sorunlara göre
> gün sırası ve kapsam değişebilir.

- **Gün 1-2:** Ortam kurulumu (STM32CubeIDE, STM32CubeMX, X-CUBE-AI paketi),
  kartı tanıma, LED blink ile toolchain doğrulama.
- **Gün 3-4:** CubeMX ile I2S2 (half-duplex master receive) + DMA
  konfigürasyonu, PDM2PCM entegrasyonu; STM32CubeF4 paketindeki
  `Audio_playback_and_record` örneği (bkz. Donanım bölümü) referans
  alınarak PDM mikrofon okumasının çalıştığının doğrulanması.
- **Gün 5-6:** USART2 (PA2/PA3) + USB-TTL adaptör üzerinden PC iletişiminin
  kurulması; CMSIS-DSP ile kart üzerinde FFT tabanlı bant enerjisi özellik
  çıkarımının yazılması ve özellik vektörlerinin CSV olarak UART'tan
  gönderilmesi.
- **Gün 7-8:** Python tarafında UART'tan gelen özellik vektörlerini
  okuyan veri toplama scripti; 3 sınıf (sessizlik/ıslık/alkış) için
  etiketli veri toplama ve Keras ile bu özellik vektörleri üzerinde basit
  bir sınıflandırıcı eğitimi (eğitim ve çıkarımda aynı özellik temsili
  kullanılır).
- **Gün 9-10:** Modelin X-CUBE-AI ile STM32 için dönüştürülmesi
  (quantization/optimizasyon), CubeMX projesine entegre edilmesi.
- **Gün 11-12:** Firmware'de gerçek zamanlı çıkarım döngüsü (mikrofon →
  PDM2PCM → CMSIS-DSP özellik çıkarımı → model → sonuç), sonucun LED/UART
  ile gösterilmesi.
- **Gün 13:** Uçtan uca test, doğruluk/latency ölçümü, hata ayıklama.
- **Gün 14:** Dokümantasyon, demo kaydı/log, sonraki adımlar notu.

## Klasör Yapısı

- [`firmware/`](firmware/README.md) — STM32CubeIDE/CubeMX projesi ve
  X-CUBE-AI entegrasyonu
- [`training/`](training/README.md) — Python veri toplama ve Keras eğitim
  scriptleri
- [`data/`](data/README.md) — Ham ve işlenmiş ses/özellik verisi
- [`docs/`](docs/README.md) — Donanım şeması, ölçüm sonuçları, ilerleme
  notları
