# 📶 STM32 HC-05 Bluetooth ile Kablosuz Kontrol Sistemi

Bu proje, gömülü sistemlerin dış dünya ile kablosuz olarak haberleşmesini sağlamak amacıyla **STM32F407** mikrodenetleyicisi ve **HC-05/HC-06 Bluetooth modülü** kullanılarak geliştirilmiştir. 

Proje kapsamında, bir akıllı telefon (mobil arayüz) üzerinden gönderilen asenkron seri komutlar (String metinler) mikrodenetleyici tarafından ayrıştırılarak (parsing) ilgili donanımların (LED ve Buzzer) kablosuz olarak kontrol edilmesi sağlanmıştır.

![ArmApp-18 Bluetooth Modülü](./image_5b2d35.jpg)
*(Test ve geliştirme süreçleri STM32F407 işlemcisine sahip ArmApp-18 eğitim seti üzerinde gerçekleştirilmiştir.)*

## 🚀 Öne Çıkan Özellikler (Highlights)

* **Kablosuz UART Haberleşmesi:** HC-05 Bluetooth modülü üzerinden 9600 Baud Rate hızında donanımsal UART haberleşmesi kurulmuştur.
* **String Komut Ayrıştırma (Command Parsing):** Telefondan gönderilen `LED_AC`, `LED_KAPAT`, `BUZZER_AC`, `BUZZER_KAPAT` gibi komutlar C dilindeki `strcmp` (String Compare) ve `strstr` algoritmalarıyla analiz edilerek eyleme dönüştürülmüştür.
* **Mobil HMI Entegrasyonu:** Herhangi bir "Serial Bluetooth Terminal" mobil uygulaması HMI (İnsan-Makine Arayüzü) olarak kullanılmış ve çift yönlü haberleşme (STM32'den telefona durum mesajı geri bildirimi) başarıyla uygulanmıştır.
* **Güvenilir Kontrol Döngüsü:** Hatalı komut gönderimlerine karşı sistemi koruyan ve işlemciyi bloklamayan veri alma algoritmaları geliştirilmiştir.

## 🛠️ Donanım ve Pin Yapılandırması

Sistemdeki dış birimlerin STM32F407 üzerindeki pin atamaları aşağıdaki gibidir:

| Bileşen | Pin Kodu | Kullanım Modu | Açıklama |
| :--- | :--- | :--- | :--- |
| **HC-05 TX (Verici)** | `PB11` | USART3_RX | STM32'nin veri alma (RX) pini. |
| **HC-05 RX (Alıcı)** | `PB10` | USART3_TX | STM32'nin veri gönderme (TX) pini. |
| **Kontrol LED'leri** | `PE1-PE7` | GPIO_Output | Kablosuz olarak kontrol edilen 7 adet uyarı LED'i. |
| **Buzzer** | `PA8` | GPIO_Output | Kablosuz olarak kontrol edilen sesli uyarı modülü. |

## 📂 Yazılım Mimarisi (Algoritma)

1.  **Bağlantı Kurulumu:** Sistem başlatıldığında UART3 birimi aktif edilir. Bluetooth modülü üzerinden telefona "STM32 Hazir..." durum mesajı iletilir.
2.  **Veri Dinleme (Reception):** Sistem, `HAL_UART_Receive` fonksiyonu ile belirli bir timeout süresinde veya kesme (Interrupt) yöntemiyle gelen karakterleri bir `Buffer` (tampon bellek) içerisine toplar.
3.  **Aksiyon Kararı:** Buffer içerisindeki kelime analiz edilir. Örneğin; veri `LED_AC` ise `PE1-PE7` pinleri `GPIO_PIN_SET` durumuna getirilir, `BUZZER_AC` ise `PA8` pini aktif edilerek sesli uyarı verilir.

## 💻 Nasıl Çalıştırılır?

1.  Projeyi `STM32CubeIDE` ile açın, derleyin (`Build`) ve karta yükleyin.
2.  Akıllı telefonunuzun Bluetooth ayarlarına giderek `HC-05` (veya modül isminiz) cihazı ile eşleşin (Standart PIN: `1234` veya `0000`).
3.  Cihazınıza Google Play veya App Store'dan **"Serial Bluetooth Terminal"** uygulamasını indirin.
4.  Uygulama üzerinden HC-05'e bağlanın ("Connected" mesajını göreceksiniz).
5.  Terminal ekranından aşağıdaki komutları büyük harflerle göndererek sistemi test edin:
    * `LED_AC`
    * `LED_KAPAT`
    * `BUZZER_AC`
    * `BUZZER_KAPAT`
