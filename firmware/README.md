# firmware/

STM32CubeIDE + STM32CubeMX projesi buraya gelecek: `.project`, `.cproject`,
`.ioc` dosyası, `Core/`, `Drivers/` ve X-CUBE-AI'nin ürettiği
`X-CUBE-AI/` (network*.c/h) kaynakları.

## Kapsam

- CubeMX ile I2S + DMA üzerinden kart üstü PDM mikrofon (MP45DT02 rev B /
  IMP34DT05 rev D) okuması, PDM2PCM ile PCM'e dönüştürme.
- CMSIS-DSP ile kart üzerinde FFT tabanlı bant enerjisi özellik çıkarımı.
- USART2 (PA2/PA3) üzerinden özellik vektörlerinin CSV formatında PC'ye
  gönderilmesi.
- X-CUBE-AI ile `training/` içinde eğitilen modelin gömülmesi ve gerçek
  zamanlı çıkarım.

## Referans Örnek

PDM mikrofon okuma ve I2S konfigürasyonu için başlangıç noktaları,
STM32CubeF4 paketi içinde:

- `Projects\STM32F411E-Discovery\Applications\Audio\Audio_playback_and_record`
  (özellikle `waverecorder.c`)
- `Projects\STM32F411E-Discovery\Examples\BSP` (`AudioRecord_Test`)

## PDM2PCM Notları

- PDM2PCM kütüphanesi **donanım CRC** birimi gerektirir — CubeMX'te CRC
  periferi etkinleştirilmeli.
- I2S periferi/pin ataması, saat konfigürasyonu (PLLN/PLLR) ve DMA
  stream/kanal seçimi kart-özeldir: **F411E-Discovery BSP ve yukarıdaki
  örnekten alınacak**, pinler **UM1842** (STM32F411E-DISCO kullanım
  kılavuzu) pin tablosundan doğrulanacak — burada sabit değer
  varsayılmayacak.
- Half complete / full complete DMA callback'leri ile çift tamponlama
  (double buffering) kullanılacak.

## USART2 Notu

PC iletişimi için kullanılan **USART2 (PA2/PA3)** pin ataması da
**UM1842 pin tablosundan doğrulanmalı** (başka bir periferiyle çakışma
olmadığından emin olmak için).
