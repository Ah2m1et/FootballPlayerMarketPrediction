# FootballPlayerMarketPrediction

Bu projede, Transfermarkt web sitesinden futbolcu verilerini kazıma (scraping) yöntemi ile çekerek çeşitli makine öğrenmesi modellerini (SVR, Decision Tree, Linear Regression, Random Forest) eğittik ve tahmin performanslarını karşılaştırdık.

## YouTube Videosu
[![Kullanım Videosu](https://youtu.be/QhJdXJRRKgo)](https://youtu.be/QhJdXJRRKgo)

## Projenin Amacı

Bu proje, Transfermarkt sitesinden alınan futbolcu istatistikleri ve piyasa değerleri temelinde:

    Veri çekme ve temizleme tekniklerini uygulamak,
    Farklı makine öğrenmesi algoritmaları ile futbolcu piyasa değeri tahmin etmek,
    Farklı modellerin (SVR, Decision Tree, Linear Regression, Random Forest) performanslarını karşılaştırmaktır.

## Veri Toplama (Scraping)

    scrape_transfermarkt_data() fonksiyonu kullanılarak birden fazla lig (Süper Lig, Premier Lig, La Liga, Serie A, Bundesliga, Ligue 1, Eredivisie vb.) için Transfermarkt'tan veri çekildi.
    Her lig sayfa sayfa gezilerek futbolcu verileri (oyuncu adı, yaş, pozisyon, kulüp, piyasa değeri, istatistikler vb.) CSV dosyasına kaydedildi.
    HTTP isteği sınırlandırmasını aşabilmek için time.sleep(3) ile her sayfa arasında 3 saniye gecikme kullanıldı.
    Scraping işlemi sonunda “player_table_multiple_leagues.csv” adında bir dosya oluşturuldu.

## Veri Ön İşleme ve Temizlik

    Toplanan verideki tekrarlayan veya eksik değerler temizlendi.
    Piyasa değeri sütunu (Piyasa Değeri) farklı formatlarda (ör. "200 mil. €", "900 bin €") geldiğinden metinsel ifadeler sayısal değerlere dönüştürüldü:
        Örneğin 200 mil. -> 200.0
        900 bin -> 0.9 (milyon euro cinsinden)
    Pozisyon bilgileri "Forvet", "Orta Saha", "Stoper", "Kaleci" gibi ana kategorilere ayrılarak feature engineering yapıldı.
    Gerekirse "Oyuncu", "Kulüp" gibi model için önemsiz (veya metinsel) alanlar atıldı.
    LabelEncoder ile Uyruk gibi kategorik sütunlar sayısal değerlere dönüştürüldü.
    StandardScaler ile sayısal kolonlar (örneğin yaş, maç sayısı, gol sayısı vb.) ölçeklendirildi.

## Model Eğitimi ve Değerlendirmesi
### Kullanılan Modeller

    Linear Regression (Doğrusal Regresyon)
    Decision Tree Regressor (Karar Ağacı)
    Random Forest Regressor
    SVR (Support Vector Regressor)

### Değerlendirme Metrikleri

Her model eğitildikten sonra aşağıdaki metrikler hesaplandı:

    MSE (Mean Squared Error)
    RMSE (Root Mean Squared Error)
    MAE (Mean Absolute Error)
    R2 Score
    Accuracy (%): R2 skorunun 100 ile çarpımı

Bu metrikler sayesinde modellerin tahmin başarısı kıyaslandı.
### Analiz ve Çıktı Yorumları

    Linear Regression:
        Genellikle en basit model olarak kabul edilir.
        Eğer veri lineer ilişkiler gösteriyorsa iyi sonuç verebilir. Fakat karmaşık ilişkilerde hatalar artabilir.

    Decision Tree:
        Verideki karmaşık etkileşimleri daha iyi yakalayabilir.
        Aşırı öğrenme (overfitting) riskine karşı random_state gibi parametre ayarlamaları yapılmalıdır.

    Random Forest:
        Birçok karar ağacının birleşiminden oluştuğu için tek bir ağaca göre daha kararlı sonuçlar üretir.
        Genellikle iyi bir genelleme (generalization) performansı sunar.

    SVR:
        Destek vektör makinelerinin regresyon versiyonu.
        Özellikle yüksek boyutlu verilerde etkili olabilir ancak bazı durumlarda öznitelik seçimi veya hiperparametre ayarları gerekli olabilir.

Aldığımız sonuçlara bakıldığında (örnek değerler):

    Random Forest genelde diğer modellere göre daha yüksek R2 skoru verebilir, dolayısıyla piyasadaki değer tahminlerinde daha başarılı olabilir.
    Linear Regression basit ve hızlıdır ama MSE ve RMSE değerleri bazen diğerlerine kıyasla daha yüksek kalabilir.
    Decision Tree, bazı verisetlerinde iyi sonuç verebilse de, çok sayıda özelliğin olduğu durumlarda overfitting riskiyle R2 skorunu düşürebilir.
    SVR, uygun parametrelerle iyi sonuç verebilir fakat default parametrelerle bazen istenen doğruluğa ulaşmak zor olabilir.

Gerçek verilerde, Random Forest veya XGBoost gibi topluluk (ensemble) yöntemleri, tahmin başarısını artırma eğilimindedir.
Proje Yapısı
    ```
.
├── README.md                # Bu doküman
├── player_table_multiple_leagues.csv  # Scraping sonucunda oluşturulan CSV
├── scraping_notebook.ipynb  # Scraping ve veri çekme kodları (opsiyonel)
├── analysis_notebook.ipynb  # Veri ön işleme, model eğitim ve değerlendirme kodu
├── requirements.txt         # Gerekli Python paketlerinin listesi 
└── ...
    ´´´


## Nasıl Kullanılır?
Gereksinimler
Python 3.x
Gerekli kütüphaneler (pandas, numpy, scikit-learn, beautifulsoup4, requests, seaborn, matplotlib vb.)

Tüm bağımlılıkları kurmak için:

´´´
pip install -r requirements.txt
´´´

## Veri Çekme (Opsiyonel)
scrape_transfermarkt_data() fonksiyonunu çalıştırarak veriyi doğrudan Transfermarkt’tan çekebilirsiniz.
Eğer fonksiyonu çalıştırmak istemiyorsanız, repoda yer alan player_table_multiple_leagues.csv dosyasını kullanabilirsiniz.

## Veri Ön İşleme ve Model Eğitimi
analysis_notebook.ipynb dosyasını Jupyter Notebook ya da Google Colab üzerinde açın.
Dilediğiniz gibi veri ön işleme adımlarını (drop, label encoding, scaling vb.) değiştirebilirsiniz.
Notebook’u adım adım çalıştırarak sonuçları inceleyebilirsiniz.

## Sonuçların Yorumlanması
Metrikleri (MSE, RMSE, R2 vb.) Output hücrelerinde göreceksiniz.
En yüksek R2 skoruna sahip model, elinizdeki veri için en başarılı tahmin performansını göstermiş olacaktır.

## Lisans

Bu proje MIT lisansı altındadır. Daha fazla bilgi için LICENSE dosyasına bakabilirsiniz.
