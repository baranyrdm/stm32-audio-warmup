# STM32 Audio Warmup

STM32F411E-DISCO kartı üzerindeki kart üstü MP45DT02/IMP34DT05 dijital PDM
mikrofonuyla gerçek zamanlı ses sınıflandırması yapan 2 haftalık bir ısınma
(warmup) projesi. Hedef üç sınıf: **sessizlik**, **ıslık** ve **alkış**.

Amaç; STM32CubeMX ile donanım konfigürasyonunu kurmak, PC tarafında Python ile
veri toplayıp Keras ile küçük bir sınıflandırıcı eğitmek ve X-CUBE-AI ile bu
modeli STM32F411 üzerinde gerçek zamanlı çalıştırmaktır.

## Donanım

- **Kart:** STM32F411E-DISCO (STM32F411VET6, 512 KB Flash, 128 KB RAM,
  100 MHz, Cortex-M4F)
- **Mikrofon:** Kart üstü PDM MEMS mikrofon — kart revizyonuna göre
  **MP45DT02 (rev B)** veya **IMP34DT05 (rev D)**; her ikisi de PDM arayüzlü.
  - STM32F411'de SAI veya DFSDM periferi **yoktur**. PDM verisi
    **I2S + DMA** ile okunur ve yazılım tarafında **PDM2PCM**
    kütüphanesiyle PCM'e dönüştürülür (kesin I2S periferi/pin ataması için
    bkz. `firmware/README.md`).
  - Başlangıç noktası: STM32CubeF4 paketindeki F411E-Discovery BSP ve
    `Audio_playback_and_record` örneği (bkz. `firmware/README.md`).
- **PC iletişimi:** Kartta ST-LINK üzerinden sanal COM portu **yoktur**.
  PC ile haberleşme **USART2 (PA2/PA3)** üzerinden, harici bir **3.3V
  USB-TTL (USB-seri) adaptör** ile yapılır (pinleri UM1842'den doğrulayın).
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
- **Gün 3-4:** CubeMX ile I2S + DMA konfigürasyonu, PDM2PCM entegrasyonu;
  F411E-Discovery BSP ve `Audio_playback_and_record` örneği (bkz.
  `firmware/README.md`) referans alınarak PDM mikrofon okumasının
  çalıştığının doğrulanması.
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
