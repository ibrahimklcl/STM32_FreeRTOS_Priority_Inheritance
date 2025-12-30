# STM32 FreeRTOS Priority Inheritance Demo

Bu proje, Gömülü Sistemler ve RTOS mimarilerinde sıkça karşılaşılan **"Priority Inversion" (Öncelik Tersinmesi)** probleminin simülasyonunu ve **"Priority Inheritance" (Öncelik Mirası)** yöntemiyle çözümünü gösterir.

## Projenin Amacı
Sistemde düşük, orta ve yüksek öncelikli görevler varken; ortak kaynak (Shared Resource) kullanımında oluşabilecek kilitlenmeleri ve gecikmeleri (Latency) engellemek.

## Senaryo ve Çalışma Mantığı
Sistemde 3 farklı görev (Task) ve 1 adet Mutex bulunmaktadır:

1.  **Low Priority Task:** Mutex'i (Ortak Kaynağı) alır (Acquire). İşini yaparken yavaştır.
2.  **High Priority Task:** Mutex'i almak ister ancak Low Task elinde tuttuğu için beklemeye (Block) geçer.
3.  **Mid Priority Task:** Normalde Low Task'tan daha öncelikli olduğu için araya girip CPU'yu işgal etmeye çalışır.

### Çözüm: Priority Inheritance
`osMutexPrioInherit` özniteliği kullanılarak; **Low Task**, Mutex'i elinde tuttuğu sürece geçici olarak **High Task**'in öncelik seviyesine yükseltilir.
* Böylece **Mid Task** araya girip Low Task'ın işini bitirmesini engelleyemez.
* Low Task işini hızla bitirip Mutex'i bırakır.
* High Task beklemeden yoluna devam eder.

## Kullanılan Yapılar
* **STM32 & FreeRTOS (CMSIS-V2)**
* **Mutex:** `osMutexPrioInherit` özelliği aktif edilmiş Binary Mutex.
* **Tasks:**
    * `highPrioTask`: Kritik ve acil işlem.
    * `midPrioTask`: İşlemciyi meşgul eden gürültü görevi.
    * `lowPrioTask`: Kaynağı uzun süre tutan görev.

## Pin Gözlem (Logic Analyzer / Osiloskop)
Bu projenin doğruluğunu test etmek için aşağıdaki pinler lojik analizör ile izlenmelidir:
* **SHARED_PIN:** Mutex'in kilitli olduğu süreyi gösterir.
* **HIGH_TASK_PIN:** Yüksek öncelikli görevin aktivitesini gösterir.
* **LOW_TASK_PIN:** Düşük öncelikli görevin aktivitesini gösterir.
* **MID_TASK_PIN:** Orta öncelikli görevin (parazit) çalışmasını gösterir.
