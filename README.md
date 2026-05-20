# Pintos İşletim Sistemi — Proje 1: Threads
Bu proje, Stanford Üniversitesi'nin Pintos öğretim amaçlı işletim sistemi çekirdeği (kernel) üzerinde, thread yönetimi ve zamanlama algoritmalarının geliştirilmesi amacıyla gerçekleştirilmiştir. Proje kapsamında işletim sisteminin temel senkronizasyon ve zamanlama problemleri çözülerek tüm testler başarıyla geçilmiştir.

## 👥 Grup Üyeleri
* Elif Naz Taşkın** 
* Bahşende Erken** 



---

## 🚀 Proje Kapsamında Yapılan Geliştirmeler

Proje üç temel aşamadan oluşmaktadır ve `threads/thread.c`, `threads/thread.h`, `threads/synch.c` ile `devices/timer.c` dosyalarında değişiklikler yapılmıştır.

### 1. Alarm Clock (Busy Waiting Olmadan Zamanlama)
* **Problem:** Pintos'un varsayılan yapısında `timer_sleep()` fonksiyonu işlemciyi gereksiz yere meşgul eden bir *busy waiting* yöntemi kullanıyordu.
* **Çözüm:** Uyuyan thread'leri uyanma zamanlarına göre artan sırada tutan global bir `sleep_list` yapısı geliştirildi. Thread'ler CPU'yu meşgul etmek yerine bloklanarak (`thread_block()`) uykuya alınır ve sadece uyanma zamanları geldiğinde timer interrupt tarafından uyandırılır. Böylece işlemci kaynakları verimli hale getirilmiştir.

### 2. Priority Scheduling & Priority Donation (Öncelik Tabanlı Planlama)
* **Öncelikli Zamanlama:** Hazır listesi (`ready_list`) ve senkronizasyon araçlarının bekleme kuyrukları (`waiters`), thread'ler önceliklerine göre büyükten küçüğe sıralanacak şekilde düzenlendi[cite: 282]. [cite_start]Daha yüksek öncelikli bir thread geldiğinde *preemption* (işlemcinin hemen bırakılması) mekanizması kuruldu.
  **Priority Donation & Inversion Çözümü:** Düşük öncelikli bir thread'in kilit (lock) tutması nedeniyle yüksek öncelikli thread'in beklemek zorunda kalması durumu (*priority inversion*) engellendi. 
* **Çoklu ve İç İçe Bağış:** Birden fazla thread'in aynı thread'e bağış yaptığı *multiple donation* ve zincirleme kilitlenmeleri çözen *nested donation* (maksimum 8 derinlik) senaryoları eksiksiz şekilde implement edilmiştir.

### 3. Advanced Scheduler — MLFQS (Gelişmiş Zamanlayıcı)
* **4.4BSD Zamanlayıcısı:** Thread'lerin önceliklerinin dinamik olarak sistem tarafından hesaplandığı Çok Seviyeli Geri Besleme Kuyruğu (Multilevel Feedback Queue Scheduler) uygulandı.
* **Sabit Nokta Aritmetiği:** Pintos çekirdeğinde floating-point (kesirli sayı) desteği bulunmadığı için 17.14 sabit nokta formatını destekleyen `fixed-point.h` makro kütüphanesi sıfırdan yazılarak entegre edildi.
* **Dinamik Hesaplamalar:** Her saniye sistem genelindeki yük ortalaması (`load_avg`) ve thread'lerin son CPU kullanım miktarları (`recent_cpu`) hesaplanarak, CPU'yu yoğun kullanan işlerin önceliği düşürülürken, I/O bekleyen interaktif işlerin önceliği yüksek tutulmuştur.

---

## 📊 Test Sonuçları

[cite_start]Projenin doğruluğunu doğrulamak adına `make check` komutuyla yürütülen tüm test senaryoları başarıyla tamamlanmıştır[cite: 358]:

```text
PASS: 18/18 tests# Pintos-Thread-Project
