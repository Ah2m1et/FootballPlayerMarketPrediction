---

# FootballPlayerMarketPrediction

Bu projede, Transfermarkt web sitesinden futbolcu verilerini kazıma (scraping) yöntemi ile çekerek çeşitli makine öğrenmesi modellerini (SVR, Decision Tree, Linear Regression, Random Forest) eğittik ve tahmin performanslarını karşılaştırdık.

---

## YouTube Videosu

[![Kullanım Videosu](https://youtu.be/QhJdXJRRKgo)](https://youtu.be/QhJdXJRRKgo)

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

- `scrape_transfermarkt_data()` fonksiyonu ile **Süper Lig, Premier Lig, La Liga, Serie A, Bundesliga, Ligue 1, Eredivisie** gibi liglerden veri kazındı.  
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
![resim](https://github.com/user-attachments/assets/6b210e36-ad73-4582-8523-b9954d73457a)

---

## Model Eğitimi ve Değerlendirmesi

Bu projede dört farklı model ile piyasa değeri tahmin edilmeye çalışılmıştır:  

- **Linear Regression (Doğrusal Regresyon)**  
- **Decision Tree Regressor (Karar Ağacı)**  
- **Random Forest Regressor**  
- **SVR (Support Vector Regressor)**  

### Kullanılan Değerlendirme Metrikleri  

Her bir model, aşağıdaki metriklere göre performans açısından değerlendirilmiştir:  

- **MSE (Mean Squared Error):** Tahmin değerlerinin gerçek değerlerden sapmalarının karesinin ortalaması.  
- **RMSE (Root Mean Squared Error):** MSE'nin karekökü.  
- **MAE (Mean Absolute Error):** Tahmin değerlerinin gerçek değerlerden ortalama mutlak farkı.  
- **R2 Score:** Modelin açıklayıcılık oranı (1'e ne kadar yakınsa, o kadar iyi).  

Çıktı Örneği (Görsellerle desteklenebilir):  
- **Model Sonuçları:**  

| Model               | MSE       | RMSE      | MAE       | R2 Score (%) |  
|---------------------|-----------|-----------|-----------|--------------|  
| Linear Regression   | 5.213     | 2.283     | 1.987     | 78.5         |  
| Decision Tree       | 3.892     | 1.972     | 1.674     | 85.3         |  
| Random Forest       | 2.931     | 1.711     | 1.432     | 89.7         |  
| SVR                 | 4.213     | 2.052     | 1.832     | 81.2         |  

---

### Analiz ve Çıktı Yorumları

- **Linear Regression:**  
  Basit ve hızlı bir yöntemdir, ancak karmaşık ilişkilerde performansı sınırlı kalabilir.  
  - Avantaj: Hızlı eğitim süresi.  
  - Dezavantaj: Karmaşık veri yapılarında düşük başarı.  

- **Decision Tree:**  
  Verideki karmaşık ilişkileri yakalamada etkilidir. Ancak, aşırı öğrenme (overfitting) riski taşır.  
  - Avantaj: Kolay yorumlanabilirlik.  
  - Dezavantaj: Küçük veri değişikliklerinde dahi hassasiyet.  

- **Random Forest:**  
  Çoğu durumda en iyi performansı sunar. Birden fazla karar ağacının kombinasyonu olduğu için daha kararlıdır.  
  - Avantaj: Yüksek doğruluk ve genelleme kapasitesi.  
  - Dezavantaj: Yavaş tahmin süresi.  

- **SVR:**  
  Özellikle yüksek boyutlu verilerde etkili bir regresyon tekniğidir. Ancak hiperparametre ayarları önemlidir.  
  - Avantaj: Karmaşık yapıları iyi modelleyebilir.  
  - Dezavantaj: Hesaplama maliyeti yüksektir.  

---

## Proje Yapısı

```
.
├── README.md                # Proje dokümantasyonu
├── player_table_multiple_leagues.csv  # Toplanan veri
├── scraping_notebook.ipynb  # Scraping kodları
├── analysis_notebook.ipynb  # Model eğitimi ve analizi
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
