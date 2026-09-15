# 🌍 Küresel Hantavirüs Epidemiyolojik & Çevresel Veri Analizi

1993–2026 arasındaki küresel Hantavirüs vaka trendlerini, klinik kayıtları ve salgınları tetiklediği düşünülen bölgesel çevresel faktörleri inceleyen bir **veri bilimi** çalışması. Google Colab ortamında Python ile yapıldı: Keşifçi Veri Analizi (EDA), korelasyon incelemesi, gecikmeli (lagged) değişkenlerle Random Forest regresyonu ve çevresel değişkenlerden türetilmiş bir risk skoru haritalaması.

> ### ⚠️ Veri hakkında — önce bunu okuyun
> **Kullanılan veri kümesinin tamamı simüledir.** Kaynağın kendi veri kartına göre:
> - Vaka sayıları “ülke başına taban değerlerin zamansal eğilim ve stokastik gürültüyle çarpılmasıyla” üretilmiş.
> - Ölüm oranları (CFR) yayınlanmış literatürdeki ülke bazlı aralıklara **kalibre edilmiş**.
> - Zaman serisindeki eğilim (HFRS’de düşüş, HPS’te artış) veriye **bilerek yazılmış**.
>
> Bu yüzden buradaki hiçbir sonuç gerçek Hantavirüs epidemiyolojisine dair bir bulgu değildir.
> Çalışma, gerçekçi kurgulanmış bir veri kümesi üzerinde yapılmış bir **yöntem alıştırmasıdır**.
> Simüle veride “bulunan” örüntülerin çoğu, veriyi üretenin oraya koyduğu örüntülerdir.
>
> **Veri kaynağı:** Khurram Shahzad, *Hantavirus (Andes Virus) — Global Epidemiology*, Kaggle, CC BY-SA 4.0
> https://www.kaggle.com/datasets/zkskhurram/hantavirus-andes-virus-global-epidemiology
> (CSV dosyaları bu depoda yer almaz.)

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

### Neden sinyal yok

**En olası açıklama teknik değil: aranan mekanizma veriye hiç kodlanmamış olabilir.**
Veri kartı, kemirgen bolluğu indeksinin 3–6 ay önceki yağış veya NDVI’den türetildiğine dair
hiçbir şey söylemiyor. Öyleyse model, var olmayan bir ilişkiyi aramış olur — negatif R² tam
olarak bunu söyler. Modeli suçlamadan önce sorulması gereken soru şu:
*bu ilişkinin bu veride bulunabilir olması için bir sebep var mı?*

Teknik taraftaki eksikler de kayıtta dursun:
- **Bölge kimliği modele hiç girmiyor.** Bölgeler arası taban farkları açıklanamayan varyans olarak kalıyor.
- 10 bölge × çeyrek × yıl kırılımında, gecikme sonrası elde kalan gözlem sayısı küçük.
- Çapraz doğrulama yok; sonuç tek bir rastgele ayrıma bağlı.

**Sonraki adım:** bölge sabit etkileri ve `GroupKFold` ile bölge bazlı doğrulama — ama önce
gerçek saha verisi: kemirgen yakalama serileri, uydu NDVI kayıtları, meteoroloji istasyonu ölçümleri.

---

## 📊 Betimsel özetler

Bunlar model çıktısı değil, doğrudan veriden okunan tanımlayıcı istatistiklerdir — ve veri simüle
olduğu için **hiçbiri gerçek dünya hakkında bir iddia taşımaz.**

* **Coğrafi ayrışma.** PAHO (Amerika) bölgesinde vakaların neredeyse tamamı HPS; Avrupa ve Batı
  Pasifik’te HFRS baskın. En çok vaka bildiren ülkeler Çin, Brezilya ve ABD.
* **Ölüm oranları (CFR).** HPS, HFRS’ye kıyasla belirgin biçimde daha ölümcül görünüyor.
  **Bu bir bulgu değil:** CFR değerleri zaten literatüre kalibre edilerek üretilmiş, yani
  veriye tasarım aşamasında konmuş bir varsayımın geri okunması.
* **Sıcaklık ve vaka sayısı.** Yıllık ortalama sıcaklık ile vaka sayısı arasındaki korelasyon
  **r = −0.26**: zayıf ve negatif.
* **Ormansızlaşma ve kemirgen yoğunluğu.** Korelasyon matrisinde pozitif bir ilişki görünüyor.
  Bu bir korelasyondur, nedensellik iddiası değildir.
* **Yaş / cinsiyet kırılımı.** Üretilmiş klinik kayıtlardan geliyor; çıkan örüntü gerçek hasta
  popülasyonunun değil, veriyi üreten sürecin bir özelliğidir.

Bu bölümü yine de tutuyorum, çünkü asıl öğrettiği şey `groupby` kullanımı değil:
**bir veri kümesinde bulduğun örüntüyü oraya kimin koyduğunu bilmek zorundasın.**

---

## 🗺️ Risk skoru haritası

Çevresel faktörler (sıcaklık, bağıl nem, ormansızlaşma, kemirgen indeksi) Min-Max normalizasyonuyla harmanlanarak 0–100 arası bir bileşik risk skoru üretildi ve `Plotly Mapbox` ile haritalandı. **Bu skor bir tahmin modeli çıktısı değil**, seçilen değişkenlerin ağırlıklandırılmış özetidir — üstelik simüle değerler üzerinden. Gerçek bir risk tahmini olarak kullanılamaz.

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
