# firmware/

STM32CubeIDE + STM32CubeMX projesi buraya gelecek: `.project`, `.cproject`,
`.ioc` dosyası, `Core/`, `Drivers/` ve X-CUBE-AI'nin ürettiği
`X-CUBE-AI/` (network*.c/h) kaynakları.

## Kapsam

- CubeMX ile I2S2 (half-duplex master receive) + DMA üzerinden MP45DT02 PDM
  mikrofon okuması, PDM2PCM ile PCM'e dönüştürme.
- CMSIS-DSP ile kart üzerinde FFT tabanlı bant enerjisi özellik çıkarımı.
- USART2 (PA2/PA3) üzerinden özellik vektörlerinin CSV formatında PC'ye
  gönderilmesi.
- X-CUBE-AI ile `training/` içinde eğitilen modelin gömülmesi ve gerçek
  zamanlı çıkarım.

## Referans Örnek

PDM mikrofon okuma ve I2S2 konfigürasyonu için başlangıç noktası:
STM32CubeF4 paketinde `Audio_playback_and_record` adıyla arayın (klasör
yolu paket sürümüne göre değişebilir); mikrofon kayıt kısmı referans
alınacak.

## PDM2PCM Notları

- PDM2PCM kütüphanesi **donanım CRC** birimi gerektirir — CubeMX'te CRC
  periferi etkinleştirilmeli.
- 16 kHz mono çıkış için mikrofon saat frekansı **1.024 MHz**;
  I2S PLL değerleri: **PLLN = 213**, **PLLR = 4**.
- DMA: **SPI2_RX**, **DMA1 Stream3**, peripheral-to-memory yönü; half
  complete / full complete callback'leri ile çift tamponlama (double
  buffering) kullanılacak.
- Mikrofon pinleri: **PB10 (CLK)**, **PC3 (DOUT)**.
