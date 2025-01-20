---

# FootballPlayerMarketPrediction

Bu projede, Transfermarkt web sitesinden futbolcu verilerini kazıma (scraping) yöntemi ile çekerek çeşitli makine öğrenmesi modellerini (SVR, Decision Tree, Linear Regression, Random Forest) eğittik ve tahmin performanslarını karşılaştırdık.

---

## YouTube Videosu

[![Kullanım Videosu](https://youtu.be/4-prbWJUHPc)](https://youtu.be/4-prbWJUHPc)

Bu videoda projenin işleyişine dair detaylı bir rehber bulabilirsiniz. Veri çekimi, temizleme, model eğitimi ve sonuçların görselleştirilmesi gibi adımları öğrenmek için videoyu izleyebilirsiniz.

---

## Projenin Amacı

Bu proje, futbolcuların piyasa değerlerini tahmin edebilmek ve bu süreçte veri bilimi tekniklerini uygulamak amacıyla geliştirilmiştir. Projenin başlıca hedefleri şunlardır:

- **Veri Kazıma (Scraping):** Transfermarkt sitesinden futbolculara ait detaylı bilgilerin toplanması.  
- **Veri Ön İşleme:** Ham verinin temizlenmesi ve model eğitimi için uygun hale getirilmesi.  
- **Model Eğitimi ve Karşılaştırma:** Farklı makine öğrenmesi modellerini eğiterek tahmin performanslarının değerlendirilmesi.  
- **Sonuçların Yorumlanması:** Modellerin güçlü ve zayıf yönlerini analiz ederek en iyi tahmin performansı sunan modeli belirlemek.  

---

## Veri Toplama (Scraping)

Futbolcu verileri, Transfermarkt'tan kazıma yöntemiyle çekilmiştir. Verinin toplanma sürecinde kullanılan adımlar şunlardır:

-  **Süper Lig, Premier Lig, La Liga, Serie A, Bundesliga, Ligue 1, Eredivisie** gibi liglerden veri kazındı.  
- Her lig, sayfa sayfa gezilerek futbolcu bilgileri (oyuncu adı, yaş, pozisyon, kulüp, piyasa değeri, maç sayısı gibi) toplandı ve bir CSV dosyasına kaydedildi.  
- **HTTP isteği sınırlandırmasını aşmak** için her sayfa arasında `time.sleep(3)` ile gecikme eklendi.  

Çıktı: `player_table_multiple_leagues.csv` dosyası.

---

## Veri Ön İşleme ve Temizlik

Toplanan veri, ham haliyle makine öğrenmesi modelleri için uygun değildir. Bu nedenle veri üzerinde bir dizi ön işleme adımı uygulanmıştır:

1. **Eksik veya Tekrarlayan Verilerin Temizlenmesi:** Eksik veya tekrarlayan satırlar kaldırıldı.  
2. **Piyasa Değeri Dönüştürme:**  
   - "200 mil. €" → `200.0`  
   - "900 bin €" → `0.9`  
   (Tüm değerler milyon Euro cinsine dönüştürülmüştür.)
3. **Pozisyonlar Üzerinde Kategorileştirme:**  
   Örneğin; "Merkez Orta Saha", "Sağ Kanat" gibi detaylı pozisyonlar, "Orta Saha", "Forvet" gibi ana kategorilere ayrıldı.  
4. **Label Encoding:** Metinsel veriler (ör. uyruk, pozisyon) sayısal değerlere dönüştürüldü.  
5. **Özelliklerin Ölçeklendirilmesi:** Sayısal sütunlar (ör. yaş, gol sayısı) `StandardScaler` ile ölçeklendirildi.
![resim](https://github.com/user-attachments/assets/59ad4a96-d3e6-453a-b25a-fa68ae013b77)
![resim](https://github.com/user-attachments/assets/930a6717-145c-4537-bc6a-613901861d18)


---

# **Model Eğitimi ve Değerlendirmesi**

Bu projede piyasa değerini tahmin etmek amacıyla dört farklı makine öğrenimi modeli eğitilmiş ve değerlendirilmiştir. Bu modellerin amacı, belirli bir veri kümesi üzerinde tahminlerin doğruluğunu artırmak ve her bir modelin avantajlarını ve dezavantajlarını analiz etmektir.

---

## **Kullanılan Modeller ve Genel Tanımları**

1. **Linear Regression (Doğrusal Regresyon)**  
   - En temel regresyon modellerinden biridir.
   - Veri setindeki bağımsız değişkenler ile bağımlı değişken (Piyasa Değeri) arasında doğrusal bir ilişki varsayar.
   - Basit ve hızlı çalışır, ancak karmaşık ilişkiler veya çok boyutlu problemler için yetersiz kalabilir.

2. **Decision Tree Regressor (Karar Ağacı)**  
   - Karar ağaçları, veriyi dallara ayırarak tahmin yapar.
   - Özellikle verideki karmaşık yapıları öğrenmede ve doğrusal olmayan ilişkilerde daha başarılıdır.
   - Ağaç yapısı nedeniyle veri gürültüsünden etkilenebilir ve overfitting riski taşır.

3. **Random Forest Regressor**  
   - Çok sayıda karar ağacının bir araya getirilmesiyle oluşturulan bir topluluk öğrenme yöntemidir.
   - Rastgele alt kümeler üzerinde çalışarak overfitting'i önler ve genellikle daha istikrarlı sonuçlar verir.
   - Büyük veri setlerinde iyi performans gösterir.

4. **XGBoost (Extreme Gradient Boosting)**  
   - Güçlü bir gradyan artış yöntemi uygulayan topluluk öğrenme modeli.
   - Özellikle doğruluğu artırmak ve hız kazanmak için optimize edilmiştir.
   - Eksik verilerle iyi başa çıkabilir ve karmaşık veri setlerinde yüksek performans gösterir.

5. **SVR (Support Vector Regressor)**  
   - Support Vector Machine'in (SVM) regresyon versiyonudur.
   - Veriler arasındaki sınırları belirlemek için hiper düzlemler kullanır.
   - Küçük veri kümelerinde etkili olabilir, ancak büyük ve karmaşık veri setlerinde yeterli performans göstermeyebilir.

---

## **Kullanılan Değerlendirme Metrikleri**

Her bir modelin performansı şu metriklerle değerlendirilmiştir:

1. **MSE (Mean Squared Error)**  
   - Tahmin değerleri ile gerçek değerler arasındaki farkın karesinin ortalamasıdır.  
   - Düşük bir MSE, modelin hatalarının daha küçük olduğunu gösterir.  

2. **RMSE (Root Mean Squared Error)**  
   - MSE'nin kareköküdür ve verideki hataların gerçek birimlerde ifade edilmesini sağlar.  
   - Daha düşük bir RMSE, modelin tahminlerinin daha doğru olduğunu gösterir.  

3. **MAE (Mean Absolute Error)**  
   - Tahmin değerleri ile gerçek değerler arasındaki mutlak farkların ortalamasıdır.  
   - Hataların büyüklüğü hakkında doğrudan bir fikir verir ve yorumlaması kolaydır.  

4. **R² Score (R Kare)**  
   - Modelin açıklayıcılık oranını ifade eder ve hedef değişkenin varyansının ne kadarının açıklandığını ölçer.  
   - 1’e yakın bir R² skoru, modelin yüksek doğrulukla çalıştığını gösterir.

---

## **Model Performansı**

| **Model**             | **MSE** | **RMSE** | **MAE** | **R² Score (%)** |
|------------------------|---------|----------|---------|------------------|
| **Linear Regression**  | 269.44  | 16.41    | 10.66   | 34.56           |
| **Decision Tree**      | 178.84  | 13.37    | 4.41    | 56.56           |
| **Random Forest**      | 91.58   | 9.57     | 3.96    | 77.76           |
| **XGBoost**            | 80.86   | 8.99     | 4.12    | 80.36           |
| **SVR**                | 400.14  | 20.00    | 9.48    | 2.81            |

---

## **Model Analizi ve Yorumları**

### **1. Linear Regression (Doğrusal Regresyon)**
- **Performans Özeti:**  
  - MSE: 269.44, RMSE: 16.41, MAE: 10.66, R²: %34.56.  
  - Doğruluk oranı diğer modellere kıyasla oldukça düşüktür (%34.56).  
- **Neden Kötü Performans Gösterdi?**  
  - Model, yalnızca doğrusal ilişkileri öğrenebildiği için karmaşık ilişkileri açıklamada yetersiz kalmıştır.  
  - Veri setinde doğrusal olmayan ilişkiler bulunuyorsa, Linear Regression bu ilişkileri öğrenemez.

---

### **2. Decision Tree (Karar Ağacı)**
- **Performans Özeti:**  
  - MSE: 178.84, RMSE: 13.37, MAE: 4.41, R²: %56.56.  
  - Linear Regression’a göre çok daha iyi sonuçlar vermiştir.  
- **Avantajları:**  
  - Karmaşık veri yapıları ve doğrusal olmayan ilişkilerde iyi performans göstermiştir.  
  - Mutlak hataların (MAE: 4.41) düşük olması, modelin tahminlerde daha tutarlı olduğunu gösterir.  
- **Neden Daha Az Başarılı?**  
  - Karar ağaçları, aşırı derinleştiğinde overfitting yapabilir. Bu da modelin test verisi üzerinde daha az genel sonuçlar vermesine neden olur.

---

### **3. Random Forest (Rastgele Orman)**
- **Performans Özeti:**  
  - MSE: 91.58, RMSE: 9.57, MAE: 3.96, R²: %77.76.  
  - Doğruluk oranı oldukça yüksek (%77.76).  
- **Neden Daha İyi Performans Gösterdi?**  
  - Karar ağacındaki overfitting riskini, birden fazla ağacın ortalamasını alarak azaltmıştır.  
  - İstikrarlı ve genelleştirilmiş sonuçlar sunar.  
- **Dezavantajları:**  
  - Eğitim süresi, tek bir karar ağacına kıyasla daha uzun olabilir.  

---

### **4. XGBoost**
- **Performans Özeti:**  
  - MSE: 80.86, RMSE: 8.99, MAE: 4.12, R²: %80.36.  
  - Tüm modeller arasında en iyi doğruluk oranını göstermiştir (%80.36).  
- **Avantajları:**  
  - Verideki karmaşık ilişkileri öğrenebilme kapasitesi oldukça yüksektir.  
  - Boosting algoritması sayesinde hataları aşamalı olarak düzeltir.  
  - Eksik verilerle başa çıkma konusunda güçlüdür.  
- **Neden En Başarılı?**  
  - Optimizasyon teknikleri ve veri setindeki varyansları iyi öğrenmesi nedeniyle en düşük MSE ve en yüksek R² skoru elde edilmiştir.  

---

### **5. SVR (Support Vector Regressor)**
- **Performans Özeti:**  
  - MSE: 400.14, RMSE: 20.00, MAE: 9.48, R²: %2.81.  
  - Model, tahmin edilebilirlik açısından başarısız olmuştur.  
- **Neden Başarısız?**  
  - Model, veri setindeki karmaşık yapıyı yeterince öğrenememiştir.  
  - SVR genellikle küçük ve iyi dengelenmiş veri setlerinde daha etkili çalışır. Ancak bu veri setinde yeterli performansı gösterememiştir.  

---

## **Genel Sonuçlar ve Tavsiyeler**

1. **En Başarılı Model:**  
   - **XGBoost (%80.36 R² ile)**, piyasa değeri tahmininde en iyi performansı göstermiştir.
   - Karmaşık ilişkileri öğrenme kapasitesi ve düşük hata oranlarıyla veri setine en uygun modeldir.

2. **Alternatif Model:**  
   - **Random Forest (%77.76 R² ile)**, XGBoost’a yakın performans göstermiştir. Eğitim süresi daha kısa olan projelerde alternatif olarak tercih edilebilir.

3. **Düşük Performans Gösteren Modeller:**  
   - **Linear Regression** ve **SVR**, veri setindeki karmaşık ilişkileri modelleyemediği için düşük doğruluk oranları göstermiştir.  

4. **Genel Öneri:**  
   - Karmaşık veri setleri üzerinde çalışırken XGBoost veya Random Forest gibi topluluk öğrenme yöntemlerinin tercih edilmesi önerilir.  
   - Daha basit modeller (Linear Regression gibi) yalnızca doğrusal ilişkilere sahip veri setlerinde uygun bir seçenek olabilir.  



## Proje Yapısı

```
.
├── README.md                # Proje dokümantasyonu
├── player_table_multiple_leagues.csv  # Toplanan veri
├── footballPlayerPrediction.ipynb  # Kod
├── requirements.txt         # Python bağımlılıkları
└── ...
```

---

## Nasıl Kullanılır?

### Gereksinimler  

- **Python 3.x**  
- Gerekli kütüphaneleri yüklemek için:  
```
pip install -r requirements.txt
```

### Veri Çekme (Opsiyonel)  

Scraping için:  
```
scrape_transfermarkt_data()
```

Eğer scraping yapmak istemezseniz, `player_table_multiple_leagues.csv` dosyasını kullanabilirsiniz.

---

## Lisans  

Bu proje MIT lisansı altındadır. Daha fazla bilgi için LICENSE dosyasını inceleyebilirsiniz.  

--- 
