# Disordo - Model Eğitim ve Analiz Deposu
_Disleksi risk analizi için hibrit yapay zeka modellerinin eğitimi ve optimizasyonu_

Bu depo, **Samsung Innovation Campus** kapsamında geliştirilen **Disordo** mobil uygulaması için eğitilen yapay zeka modellerini, deney süreçlerini ve analiz kodlarını içerir.

Proje, disleksi için erken aşama risk analizini **hibrit bir yapay zeka yaklaşımı** kullanarak gerçekleştirmeyi amaçlamaktadır.

---

## 1. Çözdüğümüz Problem

Disleksi riski, tek bir belirtiye bakılarak belirlenemez. Bu nedenle bir uzmanın analiz sürecini taklit ederek iki ana göstergeye odaklandık:

1. **Harf Ters Çevirme (Reversal):** ‘b’ ile ‘d’, ‘p’ ile ‘q’ gibi harflerin karıştırılması.
2. **Satır Takibi Hatası (Line Error):** Yazı yazarken düz çizgiyi takip edememe veya harflerin satırdan sapması.

---

## 2. Yaklaşımımız: Hibrit Analiz Pipeline

Sistemimiz, bu iki belirtiyi birlikte analiz eden iki aşamalı bir **Hibrit Analiz Pipeline** kullanır:

1. **Aşama – Yapay Zeka (Object Detection):**  
   Eğitilmiş bir **YOLOv8 Nesne Tespiti Modeli**, el yazısı görüntülerinde `Reversal` (Ters Çevirme) ve `Corrected` (Düzeltilmiş) bölgeleri tespit eder ve koordinatlarını çıkarır.

2. **Aşama – Algoritmik Analiz (Line Error):**  
   Tespit edilen harf koordinatları özel bir **Line Error Algoritması** tarafından işlenir. Bu algoritma, harfleri satırlara ayırarak her satırın dikey sapma oranını (`Line Error`) matematiksel olarak hesaplar.

Mobil uygulama, bu iki aşamadan gelen verileri (`Reversal` sayısı + `Line Error` skoru) birleştirerek kullanıcıya bütüncül bir risk değerlendirmesi sunar.

---

## 3. Depo İçeriği ve Model Deneyleri

Doğru modeli belirlemek için çeşitli mimariler üzerinde kapsamlı deneyler yürütülmüştür.

### a) Sınıflandırma Modelleri (Başarısız Denemeler)
- `EfficientNetB0.ipynb`  
- `resnet_18.ipynb`

**Neden Yetersiz?**  
Sınıflandırma modelleri yalnızca “hata var/yok” çıktısı verebilir; hataların **konum bilgisini** (coordinates) sağlayamaz. Bu bilgi, ikinci aşamadaki `Line Error` analizinin çalışması için gereklidir. Bu nedenle **nesne tespiti tabanlı modellere** geçiş yapılmıştır.

---

### b) Nesne Tespiti Modelleri (Başarılı Denemeler)
- `yolov5modelegitimi.ipynb`: İlk başarılı nesne tespiti denemesi. Yaklaşımın doğruluğu bu aşamada doğrulandı.  
- `yolov8_augmented2.ipynb`: **Final Model.** Daha yüksek doğruluk ve hız sunduğu için YOLOv8 mimarisi tercih edilmiştir.  
  Model, farklı el yazılarına karşı dayanıklı hale getirilmesi amacıyla **veri artırma (augmentation)** teknikleri (eğme, kaydırma, perspektif değişimi vb.) ile eğitilmiştir.

---

### c) Hibrit Analiz ve Model Dönüşümü
- `model_line_error.ipynb`: **Projenin merkezi bileşeni.** YOLOv8 modelini kullanarak tespit sonuçlarını `Line Error` algoritmasına besler.  
- `to_tflite.ipynb`: Eğitilen `best.pt` modelini mobil uygulamada kullanılmak üzere **TensorFlow Lite (`.tflite`)** formatına dönüştürür.

---

## 4. Teknoloji Yığını
- Python  
- PyTorch  
- Ultralytics (YOLOv5, YOLOv8)  
- OpenCV  
- Scikit-learn (DBSCAN – Satır kümelemesi için)  
- TensorFlow Lite (Model dönüşümü için)

---

## 5. Genel Akış
```mermaid
flowchart LR
    A[El Yazısı Görseli] --> B[YOLOv8 Modeli<br>Hata Tespiti]
    B --> C[Koordinat Çıktısı]
    C --> D[Line Error Algoritması<br>(Satır Analizi)]
    D --> E[Risk Skoru Hesaplama]
    E --> F[Disordo Mobil Uygulama]





The main project where these developed models are used and reach the end-user is the following mobile application repository: **[Disordo Mobile App](https://github.com/turkmenugur/Disordo)**
