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
![resim](https://github.com/user-attachments/assets/930a6717-145c-4537-bc6a-613901861d18)


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

Çıktı:  
- **Model Sonuçları:**

| **Model**             | **MSE**  | **RMSE** | **MAE** | **R2 Score (%)** |
|-----------------------|----------|----------|---------|------------------|
| **Linear Regression** | 269.44   | 16.41    | 10.66   | 34.56           |
| **Decision Tree**     | 178.84   | 13.37    | 4.41    | 56.56           |
| **Random Forest**     | 91.58    | 9.57     | 3.96    | 77.76           |
| **XGBoost**           | 80.86    | 8.99     | 4.12    | 80.36           |
| **SVR**               | 400.14   | 20.00    | 9.48    | 2.81            |

---

### Analiz ve Çıktı Yorumları:

1. **Linear Regression**: 
   - Basit bir model olup doğruluk oranı %34.56 seviyesindedir.
   - Karmaşık ilişkileri modellemede yetersiz kalmıştır.
   - Daha düşük performans sergilemiştir.

2. **Decision Tree**: 
   - Daha düşük MSE ve RMSE değerleriyle Linear Regression'a kıyasla daha iyi performans göstermiştir.
   - Doğruluk oranı %56.56 ile orta seviyede bir başarı yakalamıştır.

3. **Random Forest**: 
   - En düşük MSE (91.58) ve en yüksek R2 skoru (%77.76) ile oldukça başarılı bir modeldir.
   - Overfitting'in önüne geçmesi ve istikrarlı sonuçlar sunmasıyla öne çıkar.

4. **XGBoost**: 
   - En iyi performansı gösteren modeldir.
   - MSE (80.86) ve RMSE (8.99) değerleri oldukça düşük olup, doğruluk oranı %80.36 seviyesindedir.
   - Random Forest'a kıyasla daha iyi sonuçlar vermiştir.

5. **SVR (Support Vector Regressor)**:
   - Bu model, en düşük performansı göstermiştir.
   - MSE oldukça yüksek (400.14) ve doğruluk oranı sadece %2.81 seviyesindedir.
   - Model, bu veri seti için uygun bir seçim değildir.

---

### Özet:
- **En Başarılı Model**: XGBoost (%80.36 doğruluk oranı ile).
- **Alternatif Model**: Random Forest (%77.76 doğruluk oranı ile, XGBoost'a çok yakın performans göstermiştir).
- **Zayıf Performanslı Modeller**: SVR ve Linear Regression, diğer modellere kıyasla düşük başarı göstermiştir.
### Yorumlar:
- **Linear Regression**: Basit bir model olup doğruluk oranı %45.07 seviyesinde kalmıştır. Karmaşık ilişkilerde yetersiz kalabilir.  
- **Decision Tree**: Daha düşük MSE ve yüksek R2 skoru (%69.31) ile Linear Regression'a göre daha iyi bir performans göstermiştir.  
- **Random Forest**: En düşük MSE (293.9502) ve en yüksek R2 skoru (%72.70) ile en başarılı modeldir.  
- **SVR**: Bu model en düşük performansı göstermiştir; MSE oldukça yüksek ve doğruluk oranı %21.05 seviyesindedir. Model, bu veri seti için iyi bir seçim olmayabilir.


## Proje Yapısı

```
.
├── README.md                # Proje dokümantasyonu
├── player_table_multiple_leagues.csv  # Toplanan veri
├── footballPlayerPrediction.ipynb  # Kod
├── player_table_multiple_leagues.csv # Veri seti
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
