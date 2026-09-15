# 🌍 Küresel Hantavirüs Epidemiyolojik & Çevresel Veri Analizi

1993–2026 arasındaki küresel Hantavirüs vaka trendlerini, klinik kayıtları ve salgınları tetiklediği düşünülen bölgesel çevresel faktörleri inceleyen bir **veri bilimi** çalışması. Google Colab ortamında Python ile yapıldı: Keşifçi Veri Analizi (EDA), korelasyon incelemesi, gecikmeli (lagged) değişkenlerle Random Forest regresyonu ve çevresel değişkenlerden türetilmiş bir risk skoru haritalaması.

> ### ⚠️ Veri ve kapsam hakkında
> - **Klinik veri kümesi sentetiktir.** 8.000 hastalık kayıtlar gerçek hasta verisi değildir. Bu dosyadan çıkan yaş, cinsiyet ve tedavi kırılımları veriyi üreten sürecin özellikleridir; **epidemiyolojik bulgu olarak okunmamalıdır.**
> - Ülke-yıl vaka/ölüm kayıtları ve çevresel metrikler sentetik değildir.
> - Tüm veri Kaggle'daki açık kaynaklı Hantavirüs veri havuzlarından indirilmiştir; bu depoda CSV dosyaları yer almaz.
> - Bu bir öğrenme projesidir, akademik bir yayın değildir.

---

## 🤖 Model sonucu: sinyal bulunamadı

Gecikmeli çevresel değişkenlerle (3 ve 6 ay önceki yağış ve NDVI, ormansızlaşma, sıcaklık) kemirgen bolluğunu tahmin etmek için bir **Random Forest Regressor** kuruldu. %80/%20 eğitim-test ayrımı kullanıldı, çapraz doğrulama yapılmadı.

| Metrik | Değer |
|---|---|
| **R² (test seti)** | **−0.0357** |
| MSE (test seti) | 0.0706 |

**Negatif R², modelin test verisinde ortalamayı tahmin etmekten daha kötü olduğu anlamına gelir.** Yani bu değişken setiyle kemirgen bolluğunda anlamlı bir sinyal bulunamadı. MSE'nin küçük görünmesinin tek sebebi hedef değişkenin (`rodent_abundance_index`) zaten 0–1 aralığında bir indeks olması; ölçeği verilmeden MSE tek başına bir şey söylemez.

### Özellik ağırlıkları da aynı sonuca işaret ediyor

| Değişken | Ağırlık |
|---|---|
| `rainfall_lag1` | %19.7 |
| `ndvi_lag2` | %17.9 |
| `deforestation_rate_pct` | %16.4 |
| `ndvi_lag1` | %16.2 |
| `rainfall_lag2` | %15.0 |
| `mean_temperature_c` | %14.8 |

Altı değişken için eşit dağılım **1/6 = %16.7** olurdu. Gözlenen tablo bundan kayda değer biçimde ayrışmıyor — bu, bir faktörün baskın olduğunun değil, modelin değişkenler arasında ayrım yapamadığının göstergesidir. Sinyal bulamamış bir modelin özellik ağırlıkları doğayı değil gürültüyü tarif eder, bu yüzden buradaki sıralamadan mekanizma çıkarımı yapılmamıştır.

### Neden başarısız olmuş olabilir

- **Bölge kimliği modele hiç girmiyor.** Bölgeler arası taban farkları açıklanamayan varyans olarak kalıyor.
- Çeyreklik çözünürlükte gözlem sayısı sınırlı.
- Çapraz doğrulama yok; sonuç tek bir rastgele ayrıma bağlı.

**Sonraki adım:** bölge sabit etkilerini eklemek ve `GroupKFold` ile bölge bazlı doğrulama kurmak.

---

## 📊 Betimsel bulgular

Bunlar model çıktısı değil, doğrudan veriden okunan tanımlayıcı istatistiklerdir.

* **Coğrafi ayrışma.** PAHO (Amerika) bölgesinde vakaların neredeyse tamamı HPS; Avrupa ve Batı Pasifik bölgelerinde HFRS baskın. En çok vaka bildiren ülkeler Çin, Brezilya ve ABD.
* **Ölüm oranları (CFR).** Ülke-yıl vaka ve ölüm toplamlarından hesaplandığında HPS, HFRS'ye kıyasla belirgin biçimde daha ölümcül. Kesin oranlar notebook çıktılarındadır.
* **Sıcaklık ve vaka sayısı.** Yıllık ortalama sıcaklık ile onaylanmış vaka sayısı arasındaki korelasyon **r = −0.26**: zayıf ve negatif. Yıllık sıcaklık tek başına vaka hacmini açıklamıyor. Buradan çıkarılabilecek tek şey budur; neyin açıkladığı sorusu açık kalıyor.
* **Ormansızlaşma ve kemirgen yoğunluğu.** Korelasyon matrisinde pozitif bir ilişki görülüyor. Bu bir korelasyondur, nedensellik iddiası değildir.
* **İnsandan insana bulaş.** Veri kümesindeki suşlar arasında yalnızca birkaçı (ör. Andes virüsü) H2H bulaş özelliği taşıyor.

### Sentetik klinik veri üzerinde yapılan alıştırma

Yaş grubu / cinsiyet kırılımlı ölüm oranları ve sağkalım-yaş dağılımı bu depodaki **sentetik** klinik dosyadan üretilmiştir. Gruplama, `pd.cut` ile yaş kategorileme ve karşılaştırmalı görselleştirme pratiği olarak tutulmuştur; gerçek Hantavirüs hastalarına dair bir sonuç içermez.

---

## 🗺️ Risk skoru haritası

Çevresel faktörler (sıcaklık, bağıl nem, ormansızlaşma, kemirgen indeksi) Min-Max normalizasyonuyla harmanlanarak 0–100 arası bir bileşik risk skoru üretildi ve `Plotly Mapbox` ile haritalandı. **Bu skor bir tahmin modeli çıktısı değil**, seçilen değişkenlerin ağırlıklandırılmış özetidir; doğrulanmış bir risk tahmini olarak kullanılmamalıdır.

---

## 🛠️ Kullanılan teknolojiler

* **Dil:** Python (Google Colab)
* **Veri analizi:** Pandas, NumPy
* **Ön işleme:** Scikit-learn (MinMaxScaler)
* **Model:** Scikit-learn (RandomForestRegressor)
* **Görselleştirme:** Matplotlib, Seaborn, Plotly Express (Scatter Mapbox)

## 🔗 Bağlantılar

* Yazının tamamı: https://orcuncakar.com/post/hantavirus-analysis
* Notebook: `Hantavirus_Data_Science_Analysis.ipynb`
