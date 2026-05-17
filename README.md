# 🌍 Küresel Hantavirüs Epidemiyolojik & Çevresel Veri Analizi ve Modelleme Projesi

Bu proje; 1993-2026 yılları arasındaki küresel Hantavirüs vaka trendlerini, 8.000 hastanın sentetik klinik kayıtlarını ve salgınları tetikleyen bölgesel çevresel faktörleri inceleyen uçtan uca bir **Veri Bilimi (Data Science)** çalışmasıdır.

Google Colab ortamında Python kullanılarak; Keşifçi Veri Analizi (EDA), Nedensellik Paradokslarının Test Edilmesi, Makine Öğrenmesi ile Sağkalım Tahmini ve Etkileşimli Risk Haritalandırması gerçekleştirilmiştir.

---

## 📊 Öne Çıkan İstatistiksel & Tıbbi İçgörüler

* **Coğrafi Zıtlık ve Ölüm Oranları (CFR):** Avrupa/Asya'da görülen HFRS sendromu çok yüksek vaka sayısına (653k+) sahip olmasına rağmen ölüm oranı **%2.4**'tür. Amerika kıtasına özgü HPS sendromu ise nadir görülmekle birlikte **%29.6** mortalite oranıyla son derece ölümcüldür.
* **M mortalite ve Yaş Paradoksu (Sitokin Fırtınası):** Geleneksel viral enfeksiyonların aksine, Hantavirüs klinik verilerinde genç nüfustaki (0-18) ölüm oranı yaşlılara (65+) kıyasla daha yüksektir. Güçlü bağışıklık sistemlerinin akciğerlerde yarattığı aşırı reaksiyon modellenmiştir.
* **Tedavi Etkinliği Paradoksu (Causality Paradox):** Yoğun bakım, mekanik ventilasyon ve ECMO tedavisi alan grupların ölüm oranlarının yüksek çıkmasının sebebi tedavilerin başarısızlığı değil; bu ünitelere kabul edilen hastaların başlangıç klinik şiddetinin (Severity) kritik düzeyde olmasıdır.
* **Diyaliz İstikrarı:** Ağır böbrek yetmezliği geliştiren HFRS hastalarında uygulanan diyaliz tedavisi, mortalite oranını hastanede durumu stabil olan hastaların seviyesine (%11.0) çekerek en başarılı standart müdahale olmuştur.

---

## 🤖 Makine Öğrenmesi Modeli (Random Forest)

Hastaların demografik, semptom ve tedavi metrikleri kullanılarak bir **Random Forest Sınıflandırma Modeli** eğitilmiştir.
* **Model Doğruluğu (Accuracy):** %82.3
* **Yapay Zekaya Göre Hayatta Kalmayı Belirleyen En Kritik Faktörler (Feature Importance):**
  1. Yaş (Age) — **%45.0**
  2. Klinik Şiddet Tablosu (Severity) — **%24.9**
  3. Sendrom Türü (HPS/HFRS) — **%13.2**

---

## 🌿 Ekolojik Regresyon & Gecikmeli Etki (Lag Effect)

Kemirgen popülasyonlarındaki patlamaların anlık iklim verileriyle doğrusal bir ilişkisi olmadığı ispatlanmıştır. Gecikmeli Zaman Serisi analiziyle salgın mekanizması modellenmiştir:
* Salgınlar; El Niño ve Mast Yılları (aşırı tohumlanma) gibi ekolojik şokların ardından gelen **3 ay önceki yağış** ve **6 ay önceki bitki örtüsü (NDVI)** birikiminin gecikmeli bir sonucudur.
* Bu doğal nüfus artışını takip eden **Ormansızlaşma (Deforestation)** faaliyetleri, kemirgen yerleşimlerini bozarak virüsün insanlara geçişini tetikleyen ana unsurdur.

---

## 🗺️ İnteraktif Risk Haritalandırması

Çevresel faktörler (Sıcaklık, Bağıl Nem, Ormansızlaşma ve Kemirgen İndeksi) Min-Max normalizasyonu ile harmanlanarak **0-100 arası bir Küresel Salgın Risk Skoru** algoritması geliştirilmiştir. `Plotly Mapbox` altyapısı kullanılarak küresel risk haritası interaktif olarak coğrafi koordinatlara işlenmiştir.
* **En Yüksek Riskli Bölgeler:** Gran Chaco (Paraguay), Güney Brezilya ve Four Corners (ABD).

---

## 🛠️ Kullanılan Teknolojiler & Kütüphaneler

* **Programlama Dili:** Python
* **Veri Analizi & Ön İşleme:** Pandas, NumPy, Scikit-learn (MinMaxScaler)
* **Makine Öğrenmesi:** Scikit-learn (RandomForestClassifier, RandomForestRegressor)
* **Veri Görselleştirme:** Matplotlib, Seaborn, Plotly Express (Scatter Mapbox)

---

## 📂 Veri Seti Hakkında
Bu çalışmada kullanılan veri setleri Kaggle üzerindeki açık kaynaklı Hantavirüs küresel veri havuzlarından (Klinik kayıtlar, ülke yıllık trendleri, aylık mevsimsellik, virüs suşları ve çevresel metrikler) indirilmiştir.
