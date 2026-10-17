# stm32f4-secure-telemetry

STM32F407 (Cortex-M4) üzerinde, **HAL kullanmadan** (register seviyesinde) yazılan sürücüler, FreeRTOS, lwIP ve
AEAD şifreleme ile ivmeölçer verisini PC'ye güvenli şekilde ileten gömülü sistem projesi.

> **Durum: geliştirme aşamasında (Aşama 1).** Bu README yalnızca gerçekten yapılanı "yapıldı" olarak listeler;
> geri kalanı planlanan olarak işaretlidir.

## Amaç

Bir gömülü sistem mühendisinin uçtan uca bildiği zinciri tek projede kurmak ve her parçayı anlayarak yazmak:
açılış kodu → saat ağacı → çevre birimi sürücüleri → RTOS → ağ yığını → şifreleme → PC tarafı.

## Donanım

| Parça | Not |
|---|---|
| STM32F407G-DISC1 | Cortex-M4F, 1 MB Flash, 128 KB RAM, 8 MHz kristal (HSE) |
| USB-TTL dönüştürücü (3.3 V) | Aşama 1.3'ten itibaren gerekli (kartın ST-LINK'inde sanal COM portu yok) |

Kartta **Ethernet PHY yok**. Planlanan ağ katmanı, UART üzerinden SLIP ile gerçek bir IP yığını (lwIP) çalıştırır;
bu gerçek bir IP/UDP trafiğidir ama **fiziksel Ethernet değildir**.

## Şu ana kadar yapılanlar

- [x] CubeIDE'de HAL'siz boş F407 projesi (startup dosyası, linker script, derleme ayarları)
- [x] Register seviyesinde GPIO: PD12 LED'ini sürme, PA0 kullanıcı butonunu okuma
- [x] HSE → PLL ile 168 MHz sistem saati yapılandırması (Flash wait state ve APB bölücüleriyle)
- [ ] 168 MHz'in **ölçülerek** doğrulanması (planlı: DWT cycle counter, Aşama 1.2)

## Yol haritası (planlanan)

| Aşama | İçerik |
|---|---|
| 1. Sürücüler | SysTick/DWT, USART2 (polling, kesme + halka tampon, DMA), SPI1 ile ivmeölçer, hard fault yakalama |
| 2. FreeRTOS | Çok görevli yapı, ölçümler, watchdog |
| 3. Ağ | lwIP, SLIP over UART, PC'ye UDP telemetri |
| 4. Güvenlik | AEAD ile doğrulamalı şifreli kanal, replay koruması |
| 5. Cila | Dokümantasyon, test, temizlik |

Şifreleme için kriptografi **sıfırdan yazılmaz**; kanıtlanmış bir kütüphane kullanılır, projenin değeri doğru entegrasyondadır.

## Ölçümler

Henüz yok. Ölçüm sonuçları (ISR gecikmesi, şifreleme süresi, stack kullanımı vb.) gerçekten ölçüldükçe
`docs/olcumler.md` dosyasına yazılacaktır.

## Klasör yapısı

```
firmware/    STM32CubeIDE projesi (telemetri-dugumu)
host/        PC tarafı (Python): köprü ve alıcı  [henüz boş]
docs/        Dokümantasyon ve ölçümler            [henüz boş]
notlar/      Adım adım öğrenme notları
```

## Derleme ve çalıştırma

Gereken: Windows, STM32CubeIDE, STM32F407G-DISC1 kartı, mini-USB kablo (CN1, ST-LINK).

1. STM32CubeIDE'de workspace olarak `firmware/` klasörünü seç.
2. Proje Project Explorer'da görünmüyorsa **File → Import → General → Existing Projects into Workspace** ile `firmware/telemetri-dugumu` klasörünü içe aktar.
3. **Project → Build Project**. (Şu an derleyici bir FPU uyarısı verir; FPU'yu açmak ilerleyen bir adımda yapılacak.)
4. Kartı CN1'den bağla ve **Debug**'a bas, ardından **Resume**.
5. Beklenen: Kullanıcı butonuna (mavi) basılıyken yeşil LED (PD12) yanar.

## Güvenlik notu

Anahtarlar (PSK) repoya **asla** girmez. `secrets.h` dosyası `.gitignore`'dadır; ileride repoda yalnızca
`secrets.h.example` bulunacaktır.

## Referanslar

RM0090 (STM32F407 Reference Manual), STM32F407 Datasheet, UM1472 (Discovery kılavuzu), PM0214 (Cortex-M4 Programming Manual),
RFC 8439 (ChaCha20-Poly1305).
