# 🎓 Power BI E-Ticaret Analizi Dashboard Rehberi

## 📥 Adım 1: Veriyi Power BI'a Aktarma

1. Power BI Desktop'ı açın
2. **Home** → **Get Data** → **Excel**
3. `ETicaret_Veri_Seti.xlsx` dosyasını seçin
4. Dört tabloyu da seçin (Satislar, Musteriler, Urunler, Magazalar) ve **Load**

**Önemli:** Power Query Editor'de veri tiplerini kontrol edin:
- Tarih sütunu → Date formatında olmalı
- Enlem/Boylam → Decimal Number formatında olmalı

## 🔗 Adım 2: Veri Modeli Oluşturma (Data Model)

### İlişkileri Kurma:
**Model** görünümüne geçin (sol menüden Model ikonu)

**İlişki 1:** Satislar → Musteriler
- `Satislar[Musteri_ID]` → `Musteriler[Musteri_ID]`
- İlişki Tipi: Many to One (*:1)
```DAX
Satislar[Musteri_ID] sürükle
    ↓
Musteriler[Musteri_ID] bırak
```

**İlişki 2:** Satislar → Urunler  
- `Satislar[Urun_ID]` → `Urunler[Urun_ID]`
- İlişki Tipi: Many to One (*:1)
```DAX
Satislar[Urun_ID]
    ↓
Urunler[Urun_ID]
```

**İlişki 3:** Satislar → Magazalar
- `Satislar[Magaza_ID]` → `Magazalar[Magaza_ID]`
- İlişki Tipi: Many to One (*:1)
```DAX
Satislar[Magaza_ID]
    ↓
Magazalar[Magaza_ID]
```

**İpucu:** Power BI genellikle bu ilişkileri otomatik algılar, ama kontrol edin!

## Veri Modeli Şeması (Star Schema):
```DAX
        Musteriler
             |
             |
        Satislar (Fact Table)
          /  |  \
         /   |   \
    Urunler  |  Magazalar
             |
          Tarih
```
### Burada Tarih tablosunu oluşturmanız gerekiyor!
> "Power BI'da Tarih Tablosu OLMADAN zaman analizleri yapamazsınız!"
---

## 📅 Adım 3: Tarih Tablosu Oluşturma

**Home** → **Enter Data** ile yeni tablo, YA DA DAX ile:
Modeling → New Table → Tarih tablosunu oluştur

**DAX Kodu**
```DAX
Tarih = 
ADDCOLUMNS(
    CALENDAR(DATE(2023, 6, 1), DATE(2024, 12, 31)),
    "Yil", YEAR([Date]),
    "Ay", FORMAT([Date], "MMMM"),
    "Ay_No", MONTH([Date]),
    "Ceyrek", "Q" & QUARTER([Date]),
    "Gun_Adi", FORMAT([Date], "dddd")
)
```
**Kod açıklaması:**
- `CALENDAR()` → Her gün için satır oluştur
- `YEAR()` → Yıl numarası
- `FORMAT(..., "MMMM")` → Ay ismi (Haziran, Temmuz...)
- `MONTH()` → Ay numarası (sıralama için!)

**ÖNEMLİ: Sıralama Ayarı**
```
Tarih[Ay] seç
→ Column Tools
→ Sort by column: Ay_No
```

> "Bu adımı unutursanız, aylar alfabetik sıralanır: Ağustos, Aralık, Ekim... Yanlış!"

Sonra `Satislar[Tarih]` ile `Tarih[Date]` arasında ilişki kurun.
```DAX
Satislar[Tarih] 
    ↓
Tarih[Date]
```
---

## 📊 Adım 4: DAX Ölçüleri (Measures) Oluşturma

**Yeni tablo oluşturun:** `_Olculer` (Measures'ı organize etmek için)
```
Modeling → New Table
Formül: _Olculer = { 0 }
Enter
```

> "Alt çizgi (Underscore) ile başladık çünkü alfabetik sıralamada en üstte olsun. İçindekiler aslında önemli değil, sadece ölçüleri buraya koyacağız."

---

### Temel Ölçüler:

```DAX
Toplam_Satis = SUM(Satislar[Tutar])
Siparis_Sayisi = DISTINCTCOUNT(Satislar[Siparis_ID])
Ortalama_Sepet = DIVIDE([Toplam_Satis], [Siparis_Sayisi], 0)
Toplam_Miktar = SUM(Satislar[Miktar])
Musteri_Sayisi = DISTINCTCOUNT(Satislar[Musteri_ID])
```
---
**Ölçü: Toplam Satış**
```DAX
Toplam_Satis = SUM(Satislar[Tutar])
```
**Açıklama:** 
>"En basit ölçü. Tutar sütunundaki tüm değerleri topla."

---

**Ölçü: Sipariş Sayısı**
```DAX
Siparis_Sayisi = DISTINCTCOUNT(Satislar[Siparis_ID])
```
> "COUNT değil, DISTINCTCOUNT! Çünkü aynı sipariş birden fazla satırda olabilir."

---

**Ölçü: Ortalama Sepet**
```DAX
Ortalama_Sepet = DIVIDE([Toplam_Satis], [Siparis_Sayisi], 0)
```
> "Köşeli parantez = başka ölçü kullanıyoruz"

> "DIVIDE'da 3. parametre = bölme hatası varsa ne dönsün (0)"

---
### Zaman Bazlı Ölçüler:
```DAX
Onceki_Ay_Satis = CALCULATE([Toplam_Satis], DATEADD(Tarih[Date], -1, MONTH))
Satis_Artisi = [Toplam_Satis] - [Onceki_Ay_Satis]
Satis_Artisi_Yuzde = DIVIDE([Satis_Artisi], [Onceki_Ay_Satis], 0)
YTD_Satis = TOTALYTD([Toplam_Satis], Tarih[Date])
```
---

**Ölçü: Önceki Ay Satış**
```DAX
Onceki_Ay_Satis = CALCULATE([Toplam_Satis], DATEADD(Tarih[Date], -1, MONTH))
```

**Açıklama (Önemli!):**
> `CALCULATE` = Filtre ile hesapla

> `DATEADD(..., -1, MONTH)` = 1 ay geriye git

> Bu Time Intelligence için Tarih tablosu şart!

```
CALCULATE yapısı:
CALCULATE(
    Ne hesaplayacak?,
    Hangi filtre ile?
)
```

---

**Ölçü: Satış Artışı %**
```DAX
Satis_Artisi_Yuzde = 
DIVIDE(
    [Toplam_Satis] - [Onceki_Ay_Satis], 
    [Onceki_Ay_Satis], 
    0
)
```

**Format ayarı:**
```
Ölçüyü seç → Measure tools
→ Format: Percentage
→ Decimal places: 1
```

---
### Karlılık Analizi:
```DAX
Toplam_Maliyet = SUMX(Satislar, Satislar[Miktar] * RELATED(Urunler[Maliyet]))
Kar = [Toplam_Satis] - [Toplam_Maliyet]
Kar_Marji = DIVIDE([Kar], [Toplam_Satis], 0)
```

**Açıklama (Detaylı):**

> `SUMX` = İteratif toplama (satır satır)

> `RELATED` = İlişkili tablodan veri çek

> Neden SUMX? Çünkü hesaplama var (Miktar × Maliyet)

**SUM vs SUMX farkı:**
```
SUM: Hazır sütunu topla
SUMX: Önce hesapla, sonra topla
```

---

**Ölçü: Kar**
```DAX
Kar = [Toplam_Satis] - [Toplam_Maliyet]
```

---

**Ölçü: Kar Marjı**
```DAX
Kar_Marji = DIVIDE([Kar], [Toplam_Satis], 0)
```

---

### DAX Pratik İpuçları

**✅ İyi Pratikler:**
- Ölçü isimleri Türkçe olabilir ama boşluk yerine _ kullan
- Formatlama her zaman yap (₺, %, sayı)
- Karmaşık formülleri satır satır yaz (okunabilir)

**❌ Kötü Pratikler:**
- Calculated Column ile Measure karıştırma
- RELATED yerine LOOKUPVALUE kullanma (yavaş)
- Hata kontrolü yapmamak (DIVIDE yerine / kullanmak)

---

## 🎨 Adım 5: Dashboard Sayfası 1 - Ana Özet

### Yerleşim:

**Üst Kısım - KPI Kartları (4 adet):**
- Card 1: Toplam Satış (Toplam_Satis) (Mavi)
- Card 2: Sipariş Sayısı (Siparis_Sayisi) (Yeşil)
- Card 3: Ortalama Sepet (Ortalama_Sepet) (Turuncu)
- Card 4: Kar Marjı % (Kar_Marji) (Mor)

**İlk kart adım adım:**
```
1. Visualizations → Card
2. Fields: Toplam_Satis sürükle
3. Format visual:
   - Callout value: 40pt, mavi
   - Category label: "Toplam Satış"
   - Background: beyaz
   - Border: 8px radius
```

**Pozisyon:**
```
Size & Properties:
X: 20, Y: 20
Width: 280, Height: 120
```

**Sonra diğer kartlar:**
> "İlk kartı kopyala-yapıştır yaparak diğer 3'ünü oluşturun. Sadece ölçüyü değiştirin."

---

**Sol Üst:**
- **Line Chart:** Aylık Satış Trendi
  - X-Axis: Tarih[Ay] (sıralı)
  - Y-Axis: Toplam_Satis
  - Legend: (boş)

**Demo:**
```
Visualizations → Line chart
X-axis: Tarih[Ay]
Y-axis: Toplam_Satis
```

**ÖNEMLİ: Sıralama sorunu varsa**
```
Grafik seçili → More options (...) 
→ Sort axis → Ay_No (Ascending)
```

**Format:**
- Başlık: "Aylık Satış Trendi"
- Çizgi kalınlığı: 3
- Data labels: Açık

---

**Sağ Üst:**
- **Stacked Bar Chart:** Kategorilere Göre Satış
  - Y-Axis: Satislar[Kategori]
  - X-Axis: Toplam_Satis
  - Veri etiketlerini göster

```
Clustered bar chart
Y-axis: Satislar[Kategori]
X-axis: Toplam_Satis
```
#
**Sıralama:**
```
More options → Sort by: Toplam_Satis (Descending)
```
#
**Renkler:**
- Elektronik: Mavi
- Giyim: Yeşil
- Ev & Yaşam: Sarı
- Kitap: Kırmızı
- Spor: Mor

---
#
**Sol Alt:**
- **Map:** Mağaza Lokasyonlarına Göre Satış (YENİ!)
  - **Önemli:** Azure Maps veya Filled Map kullanın
  - **Azure Map Kullanımı:**
    - Location: Magazalar[Sehir] VEYA Enlem/Boylam
    - Latitude: Satislar[Enlem]
    - Longitude: Satislar[Boylam]
    - Size: Toplam_Satis
    - Legend: Magazalar[Magaza_Adi]
  - **Alternatif - Filled Map:**
    - Location: Magazalar[Sehir]
    - Legend: Magazalar[Magaza_Adi]
    - Size: Toplam_Satis
  - Türkiye haritasına zoom yapın
  - Bubble'ların boyutu satış tutarını göstersin

**Harita eklemeden önce:**
```
File → Options → Security
→ Use Map and Filled Map visuals: ✓
Power BI'ı yeniden başlat
```
#
**Harita ekleme:**
```
Visualizations → Azure Map
Location:
  - Latitude: Satislar[Enlem]
  - Longitude: Satislar[Boylam]
Size: Toplam_Satis
```

---
#
**Sağ Alt:**
- **Donut Chart:** Üyelik Tipine Göre Satış
  - Legend: Musteriler[Uyelik_Tipi]
  - Values: Toplam_Satis

---
#
**Slicer'lar (Filtreler):**
- Tarih aralığı (Date Slicer)
- Kategori (Dropdown)
- Şehir (Dropdown)
- Mağaza Adı (Dropdown - YENİ!)
- Bölge (Dropdown - YENİ!)

**Slicer ekle:**

**1. Tarih:**
```DAX
Slicer → Tarih[Date]
Style: Between
```

**2. Kategori:**
```DAX
Slicer → Satislar[Kategori]
Style: Dropdown, Multi-select
```

**3. Şehir:**
```DAX
Slicer → Magazalar[Sehir]
Style: Dropdown
```

**Canlı test et:**
> "Şimdi sadece İstanbul'u seçelim... Tüm grafikler güncellenecek!"

**İpucu:** Slicer'ları sayfanın sol tarafında dikey olarak düzenleyin.

---

## 🔍 Adım 6: Dashboard Sayfası 2 - Mağaza Performans Analizi (YENİ!)

### Sayfa Başlığı: "Mağaza Analizi"

### Görseller:

#### 1. Mağaza KPI Kartları (Üst Satır)

**4 adet Card:**
- En İyi Mağaza (TOPN kullan)
- Ortalama Mağaza Satışı
- Toplam Mağaza Sayısı
- Ortalama Sipariş/Mağaza

**DAX Formülleri:**
```DAX
En_Iyi_Magaza = 
CALCULATE(
    SELECTEDVALUE(Magazalar[Magaza_Adi]),
    TOPN(1, ALL(Magazalar), [Toplam_Satis], DESC)
)

Ortalama_Magaza_Satis = 
AVERAGEX(
    VALUES(Magazalar[Magaza_ID]),
    [Toplam_Satis]
)

Magaza_Sayisi = DISTINCTCOUNT(Magazalar[Magaza_ID])

Siparis_Per_Magaza = DIVIDE([Siparis_Sayisi], [Magaza_Sayisi])
```

#### 2. Mağaza Performans Tablosu (Matrix)

**Matrix:**
- Rows: Magazalar[Magaza_Adi], Magazalar[Sehir], Magazalar[Bolge]
- Values: 
  - Toplam_Satis
  - Siparis_Sayisi
  - Ortalama_Sepet
  - Kar_Marji
- Conditional Formatting:
  - Data bars: Toplam_Satis
  - Color scale: Kar_Marji (Kırmızı → Yeşil)

#### 3. Azure Map - Mağaza Lokasyonları

**Azure Map (Harita):**
- **Latitude:** Satislar[Enlem] veya Magazalar[Enlem]
- **Longitude:** Satislar[Boylam] veya Magazalar[Boylam]
- **Size:** Toplam_Satis
- **Legend:** Magazalar[Magaza_Adi]
- **Tooltips:**
  - Mağaza Adı
  - Şehir + Bölge
  - Toplam Satış
  - Sipariş Sayısı

**Ayarlar:**
- Bubble size: 5 - 50
- Transparency: 70%
- Heat map option: Açık (isteğe bağlı)

**Önemli Not:** Azure Maps kullanmak için Power BI'da etkinleştirmeniz gerekebilir:
- File → Options → Security → Map and filled map visuals → Enable

#### 4. Şehir ve Bölge Bazlı Satış (Treemap)

**Treemap:**
- Category: Magazalar[Sehir], Magazalar[Bolge]
- Values: Toplam_Satis
- Renkler: Kategori bazlı otomatik

#### 5. Mağaza Karşılaştırma (Clustered Bar Chart)

**Clustered Bar Chart:**
- Axis: Magazalar[Magaza_Adi]
- Values: Toplam_Satis, Kar
- Sort: Toplam_Satis (Descending)
- Data labels: Açık

#### 6. Mağaza Trend Analizi (Line Chart)

**Line Chart:**
- X-axis: Tarih[Ay]
- Y-axis: Toplam_Satis
- Legend: Magazalar[Magaza_Adi] (Top 5 filtre ekle)

**Top 5 Mağaza Filtresi:**
```DAX
Top_5_Magaza = 
IF(RANKX(ALL(Magazalar[Magaza_Adi]), [Toplam_Satis], , DESC) <= 5, 1, 0)
```
>Bu ölçüyü Visual level filter olarak kullanın (value = 1)

---

## 🔍 Adım 7: Dashboard Sayfası 3 - Ürün Detay Analizi

### Görseller:

1. **Matrix (Tablo):**
   - Rows: Urunler[Kategori], Urunler[Urun_Adi]
   - Values: Toplam_Satis, Siparis_Sayisi, Kar_Marji
   - Conditional Formatting ekleyin (Data bars)

2. **Scatter Chart:** Fiyat vs Satış Miktarı
   - X-Axis: Urunler[Fiyat]
   - Y-Axis: Toplam_Miktar
   - Size: Kar
   - Legend: Kategori

3. **Top 10 Ürünler (Bar Chart):**
   ```DAX
   Top_10_Urun = 
   IF(
       RANKX(ALL(Urunler[Urun_Adi]), 
       [Toplam_Satis]) <= 10,
       [Toplam_Satis],
       BLANK()
   )
   ```

---

## 👥 Adım 8: Dashboard Sayfası 4 - Müşteri Analizi

1. **Clustered Column Chart:** Yaş Grubu ve Üyelik Tipi
   - X-Axis: Musteriler[Yas_Grubu]
   - Y-Axis: Toplam_Satis
   - Legend: Musteriler[Uyelik_Tipi]

2. **Table:** En Değerli Müşteriler (Top 20)
   - Müşteri Adı
   - Sipariş Sayısı
   - Toplam Harcama
   - Ortalama Sepet Değeri

3. **KPI Card:** Müşteri Başına Ortalama Gelir
   ```DAX
   Musteri_Basina_Gelir = DIVIDE([Toplam_Satis], Musteri_Sayisi])
   ```

---

## 🎯 İleri Seviye Özellikler

### 1. Drill-Through Sayfası - Mağaza Detay:
- Yeni sayfa oluştur: "Mağaza Detay Sayfası"
- Drill-through filter: Magaza_Adi
- Bu sayfada seçilen mağazanın:
  - Günlük satış trendi
  - En çok satan ürünler
  - Müşteri profili
  - Karşılaştırmalı performans

**Nasıl Yapılır:**
1. Yeni sayfa oluştur
2. Sağ panel → Drill through → Add drill-through fields here
3. Magazalar[Magaza_Adi] sürükle
4. Sayfaya detaylı görseller ekle
5. Ana sayfada mağaza adına sağ tık → "Drill through" → "Mağaza Detay"

### 2. Drill-Through Sayfası - Ürün Detay:
- Yeni sayfa oluştur: "Ürün Detay"
- Drill-through filter: Urun_Adi
- Bu sayfada seçilen ürünün detaylı analizi

### 2. Tooltips - Mağaza Bilgi Kartı:
- Yeni sayfa oluştur (küçük boyut: 320x240)
- Page Information → Allow use as tooltip: ON
- İçerik:
  - Mağaza adı ve logosu
  - Adres bilgisi
  - Açılış yılı
  - Bu ay satış
  - Geçen ay karşılaştırma
- Ana sayfada harita görseline sağ tık → Tooltip → Oluşturduğunuz sayfa

### 3. Tooltips - Ürün Detay:
- Ürün resmi (varsa)
- Stok durumu
- Ortalama puan
- Üzerine gelindiğinde detay göster

### 3. Bookmarks - Farklı Görünümler:
- **"Tüm Mağazalar"** bookmark
- **"Sadece İstanbul"** bookmark (İstanbul mağazaları filtrelenmiş)
- **"Top 5 Mağazalar"** bookmark
- **"Elektronik Kategorisi"** bookmark
- Butonlarla geçiş yapabilirsiniz

**Nasıl Oluşturulur:**
1. İstediğiniz filtreleri uygulayın
2. View → Bookmarks → Add
3. Bookmark'a isim verin
4. Insert → Button → Blank
5. Button'a tıkla → Action → Bookmark → Seç

### 4. Conditional Formatting:
- Matrix'te kar marjına göre renk kodlama
- Pozitif: Yeşil, Negatif: Kırmızı

---

## 📈 Önerilen Analizler (Ödev)

1. **Hangi mağaza en karlı?** (Kar marjı bazında)
2. **Hangi bölgede yeni mağaza açılmalı?** (Satış yoğunluğu analizi)
3. **Mağazalar arası performans farkı neden var?** (Lokasyon, kategori tercihi)
4. **Hangi kategoriler hangi şehirlerde daha çok satılıyor?**
5. **Premium üyelerin mağaza tercihi nasıl?**
6. **Hangi mağazanın stoğu artırılmalı?** (Satış hızı)
7. **Coğrafi genişleme stratejisi:** Harita üzerinde boşluk analizi
8. **Mağaza verimliliği:** Metrekare başına satış (eğer eklerseniz)

---

## 💡 Sunum İpuçları

- Dashboard'u hikaye gibi anlat: "Önce genel durum, sonra detaya iniyoruz"
- Filter'ları canlı kullan: "Şimdi sadece İstanbul'a bakalım"
- Sayıları yorumla: "Kar marjı %35, bu sektör ortalamasının üstünde"
- Sorun-Çözüm yaklaşımı: "Kasım ayında satışlar düşmüş, neden?"

---

## 🎨 Tasarım Tavsiyeleri

- **Renk paleti:** Tek bir ana renk + nötr renkler
- **Font:** Segoe UI (Power BI default) tutarlı
- **Beyaz alan:** Görseller arasında nefes alan bırak
- **Başlıklar:** Her görselin ne gösterdiği açık olsun
- **Logo:** Şirket logosu sol üst köşede

---

## ✅ Kontrol Listesi

- [ ] Veri modeli doğru kuruldu (4 tablo ilişkilendirildi)
- [ ] Enlem/Boylam sütunları Decimal Number formatında
- [ ] Tarih tablosu oluşturuldu
- [ ] Tüm DAX ölçüleri çalışıyor
- [ ] Azure Maps etkinleştirildi
- [ ] Harita görseli Türkiye'yi gösteriyor
- [ ] Mağaza lokasyonları doğru görünüyor
- [ ] Filter'lar tüm görsellere uygulanıyor
- [ ] Drill-through sayfaları çalışıyor
- [ ] Görseller performanslı (3 saniyeden hızlı)
- [ ] Mobile layout düzenlendi
- [ ] Dashboard yayınlandı (Power BI Service)

---

## 🎓 Ders İçin Ekstra İpuçları

### Mağaza Analiziyle İlgili Sorular:

1. "Hangi mağazanın metrekare başına satışı en yüksek?" (Alan bilgisi eklerseniz)
2. "İki mağaza arasındaki mesafe kaç km?" (Koordinatlardan hesaplanabilir)
3. "Hangi şehirde 3. mağaza açmalıyız?"
4. "Mağaza lokasyonu ile müşteri profili arasında ilişki var mı?"

### Gelişmiş DAX - Mesafe Hesaplama:

İki nokta arası mesafe (Haversine formülü):

```DAX
Mesafe_KM = 
VAR Lat1 = RADIANS([Enlem1])
VAR Lon1 = RADIANS([Boylam1])
VAR Lat2 = RADIANS([Enlem2])
VAR Lon2 = RADIANS([Boylam2])
VAR dLat = Lat2 - Lat1
VAR dLon = Lon2 - Lon1
VAR a = 
    SIN(dLat/2) * SIN(dLat/2) +
    COS(Lat1) * COS(Lat2) *
    SIN(dLon/2) * SIN(dLon/2)
VAR c = 2 * ATAN2(SQRT(a), SQRT(1-a))
VAR R = 6371 // Dünya yarıçapı (km)
RETURN R * c
```

### Coğrafi Segmentasyon:

Mağazaları bölgelere ayırma:

```DAX
Bolge_Kategori = 
SWITCH(
    TRUE(),
    Magazalar[Sehir] IN {"İstanbul", "İzmir", "Bursa"}, "Batı",
    Magazalar[Sehir] IN {"Ankara", "Konya"}, "İç Anadolu",
    Magazalar[Sehir] IN {"Antalya"}, "Akdeniz",
    Magazalar[Sehir] IN {"Adana", "Gaziantep"}, "Güneydoğu",
    "Diğer"
)
```

---

## 🆘 Sık Karşılaşılan Sorunlar {#sorunlar}

### Sorun 1: "Excel dosyası açılmıyor"

**Belirtiler:**
- "File not found" hatası
- "Access denied" hatası

**Çözümler:**
1. Dosya yolu kontrol et (Türkçe karakter olmasın)
2. Excel dosyası başka program tarafından açık olmasın
3. Power BI'ı "Yönetici olarak çalıştır"
4. Dosyayı C:\ kök dizinine kopyala

---

### Sorun 2: "İlişkiler kurulmuyor"

**Belirtiler:**
- Görsel boş geliyor
- "Couldn't find relationship" uyarısı

**Çözümler:**
1. Model view'de ilişkileri gör
2. Sütun adları birebir aynı mı kontrol et
3. Veri tipleri aynı mı? (Text-Text, Number-Number)
4. Data view'de örnek değerlere bak

---

### Sorun 3: "DAX formülü hata veriyor"

**Yaygın Hatalar:**
```
❌ SUM(Tutar)           → Tablo adı yok
✅ SUM(Satislar[Tutar])

❌ [Toplam Satis]       → Boşluk var
✅ [Toplam_Satis]

❌ related(Maliyet)     → Büyük harf önemli
✅ RELATED(Urunler[Maliyet])
```

**Kontrol Listesi:**
- [ ] Tüm DAX fonksiyonları BÜYÜK HARF
- [ ] Tablo[Sütun] formatı doğru
- [ ] Parantezler dengeli mi?
- [ ] Virgül/noktalı virgül doğru mu? (Türkçe Windows: noktalı virgül)

---

### Sorun 4: "Harita Türkiye'yi göstermiyor"

**Çözümler:**
1. Azure Maps etkin mi? (Options → Security)
2. İnternet bağlantısı var mı?
3. Enlem/Boylam decimal number mı?
4. Map Settings → Auto-zoom: OFF, manuel Türkiye'ye zoom

---

### Sorun 5: "Aylar yanlış sıralanıyor"

**Çözüm:**
```
Tarih[Ay] seç
Column Tools → Sort by column → Ay_No
```

**Test:**
Grafik şöyle mi görünüyor?
✅ Haziran → Temmuz → Ağustos
❌ Ağustos → Aralık → Ekim

---

## 📚 Ek Kaynaklar {#kaynaklar}

### Önerilen Kaynaklar

**Ücretsiz Eğitimler:**
1. **Microsoft Learn** - Power BI Fundamentals
   - https://learn.microsoft.com/tr-tr/training/powerplatform/power-bi
   
2. **Guy in a Cube** (YouTube)
   - En iyi Power BI kanalı
   - https://youtube.com/guyinacube

3. **SQLBI** - DAX öğrenmek için
   - https://www.sqlbi.com

**Türkçe Kaynaklar:**
1. BTK Akademi - Power BI Eğitimi
2. Ankara Üniversitesi Açık Ders Malzemeleri

**Kitaplar:**
- "The Definitive Guide to DAX" - Marco Russo, Alberto Ferrari
- "Power BI Dashboard in a Day" - Microsoft (ücretsiz PDF)

---

### Sonraki Ders İçin Öneriler

**İleri Seviye Konular:**
1. Power Query (M Language)
2. Advanced DAX (Variables, Iterator functions)
3. Row Level Security (RLS)
4. Power BI Service (Publish & Share)
5. Dataflows & Pipelines
6. Python/R entegrasyonu

**Proje Fikirleri:**
1. Üniversite öğrenci analizi dashboard'u
2. COVID-19 vaka takip sistemi
3. Hisse senedi portföy analizi
4. Sosyal medya analitik
5. E-spor turnuva istatistikleri

---
---

**Başarılar! 🚀**