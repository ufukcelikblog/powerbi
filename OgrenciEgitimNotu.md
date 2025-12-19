# 📊 Power BI Ticaret Verileri Analizi Eğitimi
## Öğrenci Rehberi

---

## 👋 Hoş Geldiniz!

Bu rehber, Power BI ile profesyonel bir e-ticaret analiz panosu (dashboard) oluşturmanız için hazırlanmıştır. Adım adım ilerleyerek gerçek iş dünyasında kullanılan becerileri öğreneceksiniz.

### 🎯 Bu Eğitimde Neler Öğreneceksiniz?

- ✅ Power BI'a veri yükleme
- ✅ Veri modelini kurma (tablolar arası ilişkiler)
- ✅ DAX formülleri yazma
- ✅ Etkileşimli pano oluşturma
- ✅ Coğrafi analizler (Türkiye haritası)
- ✅ İleri seviye özellikler (detaya gitme, yer imleri)

### ⏱️ Süre
Yaklaşık 90 dakika

### 📋 Gereksinimler
- Power BI Desktop yüklü bilgisayar
- ETicaret_Veri_Seti.xlsx dosyası
- Temel Excel bilgisi

---

## 📚 İçindekiler

1. [Başlamadan Önce](#baslangic)
2. [Bölüm 1: Veri Yükleme](#bolum-1)
3. [Bölüm 2: Veri Modeli](#bolum-2)
4. [Bölüm 3: DAX Formülleri](#bolum-3)
5. [Bölüm 4: Pano Oluşturma](#bolum-4)
6. [Bölüm 5: Ürün Detay Analizi](#bolum-5)
7. [Bölüm 6: Müşteri Analizi](#bolum-6)
8. [Bölüm 7: İleri Özellikler](#bolum-7)
9. [Ders İçin Ekstra İpucları](#ek-ipucu) 
10. [Ödev](#odev)
11. [Sık Sorulan Sorular](#sss)
12. [Ek Kaynaklar](#kaynaklar)

---

## 🚀 <a id="baslangic"></a>Başlamadan Önce

### Power BI Desktop Nasıl İndirilir?

**Yöntem 1: Microsoft Store (Önerilen)**
1. Windows'ta **Microsoft Store**'u açın
2. Arama çubuğuna "Power BI Desktop" yazın
3. **Ücretsiz** indir butonuna tıklayın
4. Kurulum otomatik olacak

**Yöntem 2: Microsoft Web Sitesi**
1. https://powerbi.microsoft.com/tr-tr/desktop/ adresine gidin
2. "Ücretsiz İndir" butonuna tıklayın
3. İndirilen .exe dosyasını çalıştırın

### İlk Açılış

1. Power BI Desktop'ı açın
2. İlk açılışta Microsoft hesabı istenebilir (isteğe bağlı)
3. Ana ekranda 3 ana bölüm göreceksiniz:
   - Sol: Sayfa görünümleri (Rapor/Report, Veri/Data, Model/Model)
   - Orta: Çalışma alanı (tuval/canvas)
   - Sağ: Görsel Öğeler (Visualizations) ve Alanlar (Fields) panelleri

---

## 📥 <a id="bolum-1"></a>Bölüm 1: Veri Yükleme (10 dakika)

### Adım 1: Excel Dosyasını Yükleme

**1. Veri Al (Get Data) butonuna tıklayın**
```
Giriş (Home) sekmesi → Veri Al (Get Data) → Excel
```
#
**2. Dosyayı seçin**
- `ETicaret_Veri_Seti.xlsx` dosyasını bulun
- **Aç** (Open) tıklayın

**3. Tabloları seçin**
Gezgin (Navigator) ekranında şu 4 tabloyu seçin:
- ☑️ Satislar
- ☑️ Musteriler
- ☑️ Urunler
- ☑️ Magazalar

**4. Yükle (Load) tıklayın**
- Veriler yüklenirken bekleyin (10-20 saniye)

### Adım 2: Verileri Kontrol Etme

**Veri Görünümü'ne (Data View) geçin** (Sol menüden tablo ikonu)

**Satislar tablosunu inceleyin:**
- Yaklaşık 14,000 satır olmalı
- Sütunlar: Siparis_ID, Tarih, Magaza_ID, Urun_ID, vb.
- Enlem ve Boylam sütunları var mı?

**✅ Kontrol Noktası:**
Sağ panelde **Alanlar** (Fields) bölümünde 4 tablo görünüyor mu?
- Satislar
- Musteriler  
- Urunler
- Magazalar

**❓ Sorun mu var?**
- Tablolar yüklenmediyse → Giriş (Home) → Verileri Dönüştür (Transform Data) → Kapat ve Uygula (Close & Apply)
- Tarih formatı yanlışsa → Sonraki bölümde düzelteceğiz

---

## 🔗 <a id="bolum-2"></a>Bölüm 2: Veri Modeli Oluşturma (15 dakika)

### Veri Modeli Nedir?

Veri modeli, tablolarınız arasındaki ilişkileri gösterir. Bu sayede Power BI, farklı tablolardaki verileri birleştirebilir.

**Örnek:** Satışlar tablosunda Musteri_ID var. Bu ID'yi kullanarak Musteriler tablosundan müşterinin yaş grubunu öğrenebiliriz.

---

### Adım 1: Model Görünümü'ne Geçin

**Sol menüden Model (Model) ikonuna tıklayın** (3 bağlantılı kutu ikonu)

Şu tabloları görmelisiniz:
```
Satislar (ortada - en büyük)
Musteriler (sol üst)
Urunler (sağ üst)
Magazalar (sol alt)
```

---

### Adım 2: İlişkileri Kontrol Edin

Power BI muhtemelen bazı ilişkileri otomatik oluşturmuştur. Kontrol edelim:

**İlişki nasıl görünür?**
- İki tablo arasında çizgi
- Çizginin ucunda "1" veya "*" (yıldız)
- **1** = Bir (tek değer)
- ***** = Çok (birden fazla değer)

**Olması gereken ilişkiler:**

**1. Satislar → Musteriler**
```
Satislar[Musteri_ID] --- (*:1) --- Musteriler[Musteri_ID]
```

**2. Satislar → Urunler**
```
Satislar[Urun_ID] --- (*:1) --- Urunler[Urun_ID]
```

**3. Satislar → Magazalar**
```
Satislar[Magaza_ID] --- (*:1) --- Magazalar[Magaza_ID]
```

---

### Adım 3: Eksik İlişki Varsa Ekleme

**Eğer bir ilişki yoksa:**

1. **Satislar tablosundan** `Musteri_ID` sütununu tıklayın ve **tutun**
2. **Musteriler tablosuna** sürükleyin, `Musteri_ID` sütununun üzerine **bırakın**
3. Otomatik ilişki penceresi açılır:
   - Önem düzeyi (Cardinality): Çoktan bire (*:1) olmalı
   - Çapraz filtre yönü (Cross filter direction): Tek (Single)
   - **Tamam** (OK) tıklayın

4. Diğer ilişkiler için tekrarlayın (Urunler ve Magazalar)

## Veri Modeli Şeması (Star Schema):
```DAX
        Musteriler
             |
             |
        Satislar (Olgu Tablosu "Fact Table")
          /  |  \
         /   |   \
    Urunler  |  Magazalar
             |
          Tarih
```
### Burada Tarih tablosunu oluşturmanız gerekiyor!
> "Power BI'da Tarih Tablosu OLMADAN zaman analizleri yapamazsınız!"
---

### Adım 4: Tarih Tablosu Oluşturma

**⚠️ ÖNEMLİ:** Zaman analizleri için özel bir Tarih tablosu gereklidir!

**1. Rapor Görünümü'ne (Report View) dönün** (sol menüden ilk ikon)

**2. Modelleme (Modeling) sekmesine gidin**

**3. Yeni Tablo (New Table) tıklayın**

**4. Şu DAX kodunu kopyalayıp yapıştırın:**

```DAX
Tarih = 
ADDCOLUMNS(
    CALENDAR(DATE(2023, 6, 1), DATE(2024, 12, 31)),
    "Tarih", [Date],
    "Yil", YEAR([Date]),
    "Ay", FORMAT([Date], "MMMM", "tr-TR"),
    "Ay_No", MONTH([Date]),
    "YilAy", FORMAT([Date], "YYYY-MM"),
    "YilAy_No", YEAR([Date]) * 100 + MONTH([Date]),
    "Ceyrek", "Ç" & QUARTER([Date]),
    "Gun_Adi", FORMAT([Date], "dddd", "tr-TR")
)
```

**5. Enter'a basın**

Solda **Tarih** tablosu oluşacak!

---

### Adım 5: Tarih İlişkisini Kurma

**1. Model Görünümü'ne (Model View) geçin**

**2. İlişki oluşturun:**
```
Satislar[Tarih] --- (*:1) --- Tarih[Date]
```

**⚠️ Dikkat:** Tarih sütunu Tarih (Date) formatında olmalı!

Eğer ilişki kurulamazsa:
- Veri Görünümü'ne (Data View) git
- Satislar[Tarih] sütununu seç
- Sütun araçları (Column tools) → Veri türü (Data type) → Tarih (Date)

---

### Adım 6: Ay Sıralamasını Düzeltme

**❗ Kritik Adım - Unutmayın!**

Aylar alfabetik sıralanır (Ağustos, Aralık, Ekim...) Bu yanlış!

**Düzeltme:**
1. **Veri Görünümü**'ne (Data View) geçin
2. **Tarih** tablosunu seçin
3. **Ay** sütununa tıklayın
4. Üst menüden **Sütun Araçları** (Column Tools) sekmesi
5. **Sıralama Ölçütü** (Sort by column) → **Ay_No** seçin
6. ✅ Tik işareti göreceksiniz

Aynı işlemi **YilAy** için de yapın:
- YilAy sütunu → Sıralama Ölçütü (Sort by column) → YilAy_No

---

## 📊 <a id="bolum-3"></a>Bölüm 3: DAX Formülleri (20 dakika)

### DAX Nedir?

DAX (Data Analysis Expressions), Power BI'ın formül dilidir. Excel formüllerine benzer ama daha güçlüdür.

**Excel vs DAX:**
- Excel: `=SUM(A1:A10)` (hücre aralığı)
- DAX: `SUM(Satislar[Tutar])` (tüm sütun)

---

### Adım 1: Ölçü Tablosu Oluşturma

**1. Modelleme (Modeling) → Yeni Tablo (New Table)**

**2. Formül:**
```DAX
_Olculer = { 0 }
```

**3. Enter**

Solda **_Olculer** tablosu oluşacak. Bu tabloya tüm ölçülerimizi koyacağız.

---

### Adım 2: Temel Ölçüler

#### Ölçü 1: Toplam Satış

**1. _Olculer tablosuna sağ tıkla → Yeni Ölçü (New Measure)**

**2. Formül çubuğuna yaz:**
```DAX
Toplam_Satis = SUM(Satislar[Tutar])
```

**3. Enter**

**4. Biçimlendir (Format):**
- Ölçü araçları (Measure tools) sekmesi
- Biçim (Format): Para Birimi (Currency - ₺)
- Ondalık basamaklar (Decimal places): 2

---

#### Ölçü 2: Sipariş Sayısı

**Yeni ölçü ekle:**
```DAX
Siparis_Sayisi = DISTINCTCOUNT(Satislar[Siparis_ID])
```

**Biçim:**
- Tam sayı (Whole number)
- Ondalık: 0

**❓ Neden DISTINCTCOUNT?**
COUNT tüm satırları sayar. Ama bir sipariş birden fazla satırda olabilir (birden fazla ürün). DISTINCTCOUNT benzersiz sipariş numaralarını sayar.

---

#### Ölçü 3: Ortalama Sepet Değeri

```DAX
Ortalama_Sepet = DIVIDE([Toplam_Satis], [Siparis_Sayisi], 0)
```

**Not:** 
- Köşeli parantez = başka ölçü kullanıyoruz
- 3. parametre (0) = bölme hatası varsa 0 döndür

**Biçim:** Para Birimi (Currency - ₺)

---

#### Ölçü 4: Müşteri Sayısı

```DAX
Musteri_Sayisi = DISTINCTCOUNT(Satislar[Musteri_ID])
```

**Biçim:** Tam sayı (Whole number)

---

#### Ölçü 5: Önceki Ay Satış

```DAX
Onceki_Ay_Satis = 
CALCULATE(
    [Toplam_Satis],
    DATEADD(Tarih[Date], -1, MONTH)
)
```

**Açıklama:**
- `CALCULATE` = Filtre ile hesapla
- `DATEADD(..., -1, MONTH)` = Tarihi 1 ay geriye al
- Bu Zaman Zekası (Time Intelligence) fonksiyonu, Tarih tablosu olmadan çalışmaz!

**Biçim:** Para Birimi (Currency - ₺)

---

#### Ölçü 6: Satış Artışı

```DAX
Satis_Artisi = [Toplam_Satis] - [Onceki_Ay_Satis]
```

**Biçim:** Para Birimi (Currency - ₺)

---

#### Ölçü 7: Satış Artış Yüzdesi

```DAX
Satis_Artisi_Yuzde = 
DIVIDE([Satis_Artisi], [Onceki_Ay_Satis], 0)
```

**Biçim:** 
- Yüzde (Percentage - %)
- Ondalık: 1

---

#### Ölçü 8: Toplam Maliyet

```DAX
Toplam_Maliyet = 
SUMX(
    Satislar,
    Satislar[Miktar] * RELATED(Urunler[Maliyet])
)
```

**Açıklama:**
- `SUMX` = Satır satır hesapla, sonra topla
- `RELATED` = İlişkili tablodan veri çek (Urunler tablosundan Maliyet)
- Neden SUMX? Çünkü önce çarpma yapıyoruz (Miktar × Maliyet), sonra topluyoruz

**Biçim:** Para Birimi (Currency - ₺)

---

#### Ölçü 9: Kar

```DAX
Kar = [Toplam_Satis] - [Toplam_Maliyet]
```

**Biçim:** Para Birimi (Currency - ₺)

---

#### Ölçü 10: Kar Marjı

```DAX
Kar_Marji = DIVIDE([Kar], [Toplam_Satis], 0)
```

**Biçim:** Yüzde (Percentage - %), 1 ondalık

---

#### Ölçü 11: Mağaza Sayısı

```DAX
Magaza_Sayisi = DISTINCTCOUNT(Magazalar[Magaza_ID])
```

**Biçim:** Tam sayı (Whole number)

---

#### Ölçü 12: Ortalama Mağaza Satışı

```DAX
Ortalama_Magaza_Satis = AVERAGEX(VALUES(Magazalar[Magaza_ID]), [Toplam_Satis])
```

**Açıklama:**
- Her mağaza için satışı hesapla
- Sonra ortalamasını al

**Biçim:** Para Birimi (Currency - ₺)

---

### ✅ Kontrol Noktası

**_Olculer** tablosunda 12 ölçü olmalı:
1. Toplam_Satis
2. Siparis_Sayisi
3. Ortalama_Sepet
4. Musteri_Sayisi
5. Onceki_Ay_Satis
6. Satis_Artisi
7. Satis_Artisi_Yuzde
8. Toplam_Maliyet
9. Kar
10. Kar_Marji
11. Magaza_Sayisi
12. Ortalama_Magaza_Satis

**Test et:**
Bir Kart (Card) görselinde Toplam_Satis'i kullan, sayı görünüyor mu?

----

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

## 🎨 <a id="bolum-4"></a>Bölüm 4: Pano Oluşturma (25 dakika)

### Sayfa Hazırlığı

**1. Sayfa adını değiştir:**
- Alttaki "Sayfa 1" (Page 1) yazısına sağ tıkla
- Yeniden Adlandır (Rename) → "Ana Özet"

**2. Sayfa arka planını ayarla:**
- Boş alana sağ tıkla → Sayfayı biçimlendir (Format page)
- Tuval arka planı (Canvas background) → Renk (Color): Açık gri (#F5F5F5)

---

### KPI Kartları (4 adet)

#### KPI 1: Toplam Satış

**1. Kart ekle:**
- Sağ panelden **Görsel Öğeler** (Visualizations) → **Kart** (Card) (dikdörtgen içinde tek sayı)

**2. Veri ekle:**
- Alanlar (Fields) panelinden `_Olculer` → `Toplam_Satis` sürükle
- "Alanlar" (Fields) kutusuna bırak

**3. Biçimlendir:**

**Görseli biçimlendir (Format visual) (boya fırçası) sekmesi:**

**Genel (General) → Boyut ve özellikler (Size & properties):**
- X konumu (X position): 20
- Y konumu (Y position): 20
- Genişlik (Width): 280
- Yükseklik (Height): 120

**Görsel (Visual) → Belirtme çizgisi değeri (Callout value):**
- Renk (Color): Mavi (#0078D4)
- Yazı tipi (Font): Segoe UI
- Metin boyutu (Text size): 40
- Görüntüleme birimleri (Display units): Otomatik (Auto)

**Görsel (Visual) → Kategori etiketi (Category label):**
- Etiket (Label): "Toplam Satış"
- Renk (Color): Gri (#666666)
- Metin boyutu (Text size): 16

**Görsel (Visual) → Efektler (Effects) → Arka plan (Background):**
- Renk (Color): Beyaz
- Saydamlık (Transparency): 0%

**Görsel (Visual) → Efektler (Effects) → Görsel kenarlığı (Visual border):**
- Kenarlık (Border): AÇIK (ON)
- Renk (Color): Açık gri (#E0E0E0)
- Yuvarlatılmış köşeler (Rounded corners): 8px

---

#### KPI 2: Sipariş Sayısı

**Kısayol: İlk kartı kopyala!**

1. İlk kartı seç
2. `Ctrl + C` (kopyala)
3. `Ctrl + V` (yapıştır)
4. Yeni kartı yan yana konumlandır:
   - X: 320, Y: 20
   - Genişlik: 280, Yükseklik: 120
5. Alanlar (Fields) bölümünden Toplam_Satis'i sil
6. Siparis_Sayisi ekle
7. Rengi değiştir: Yeşil (#28A745)
8. Etiket: "Toplam Sipariş"

---

#### KPI 3: Ortalama Sepet

**Kopyala-yapıştır yöntemiyle:**
- X: 620, Y: 20
- Ölçü: Ortalama_Sepet
- Renk: Turuncu (#FF8C00)
- Etiket: "Ortalama Sepet"

---

#### KPI 4: Kar Marjı

- X: 920, Y: 20
- Ölçü: Kar_Marji
- Renk: Mor (#6F42C1)
- Etiket: "Kar Marjı"

---

### Aylık Satış Trendi (Çizgi Grafik)

**1. Çizgi grafik ekle:**
- Görsel Öğeler (Visualizations) → Çizgi grafik (Line chart) (çizgi ikonu)

**2. Verileri ekle:**
- **X ekseni** (X-axis): `Tarih[Ay]` sürükle
- **Y ekseni** (Y-axis): `_Olculer[Toplam_Satis]` sürükle

**⚠️ Aylar yanlış sıralandıysa:**
- Grafiğin sağ üst köşesinde "..." (Diğer seçenekler/More options)
- Ekseni sırala (Sort axis) → Ay_No (Artan/Ascending)

**3. Konum:**
- X: 20, Y: 160
- Genişlik: 580, Yükseklik: 300

**4. Biçimlendir:**

**Görsel (Visual) → Başlık (Title):**
- Başlık metni (Title text): "Aylık Satış Trendi"
- Yazı tipi boyutu (Font size): 16
- Yazı tipi (Font): Segoe UI Semibold

**Görsel (Visual) → X ekseni (X-axis):**
- Başlık (Title): KAPALI (OFF)
- Değerler (Values): AÇIK (ON)
- Metin boyutu (Text size): 11

**Görsel (Visual) → Y ekseni (Y-axis):**
- Başlık (Title): KAPALI (OFF)
- Görüntüleme birimleri (Display units): Milyonlar (Millions - M)
- Değerler (Values): AÇIK (ON)

**Görsel (Visual) → Çizgiler (Lines):**
- Kontur genişliği (Stroke width): 3
- İşaretçiler (Markers): AÇIK (ON)
- İşaretçi boyutu (Marker size): 6

**Görsel (Visual) → Veri renkleri (Data colors):**
- Renk (Color): Mavi (#0078D4)

---

### Kategorilere Göre Satış (Çubuk Grafik)

**1. Kümelenmiş çubuk grafik ekle:**
- Görsel Öğeler (Visualizations) → Kümelenmiş çubuk grafik (Clustered bar chart) (yatay çubuk)

**2. Verileri ekle:**
- **Y ekseni** (Y-axis): `Satislar[Kategori]`
- **X ekseni** (X-axis): `_Olculer[Toplam_Satis]`

**3. Sırala:**
- Diğer seçenekler (...) (More options) → Sıralama ölçütü (Sort by): Toplam_Satis (Azalan/Descending)

**4. Konum:**
- X: 620, Y: 160
- Genişlik: 580, Yükseklik: 300

**5. Biçimlendir:**

**Görsel (Visual) → Başlık (Title):**
- "Kategorilere Göre Satış"

**Görsel (Visual) → Veri etiketleri (Data labels):**
- Değerler (Values): AÇIK (ON)
- Konum (Position): Dış uç (Outside end)
- Görüntüleme birimleri (Display units): Binler (Thousands - K)

**Görsel (Visual) → Veri renkleri (Data colors):**
Her kategori için farklı renk:
- Elektronik: Mavi (#0078D4)
- Giyim: Yeşil (#28A745)
- Ev & Yaşam: Sarı (#FFC107)
- Kitap & Kırtasiye: Kırmızı (#E74C3C)
- Spor & Outdoor: Mor (#9B59B6)

---

### Mağaza Haritası (Azure Haritası)

**Önce Azure Haritaları'nı etkinleştirin:**

1. **Dosya** (File) → **Seçenekler ve ayarlar** (Options and settings) → **Seçenekler** (Options)
2. Sol menüden **Güvenlik** (Security)
3. **"Harita ve doldurulmuş harita görsellerini kullan"** (Use Map and Filled Map visuals) kutusunu işaretleyin
4. **Tamam** (OK)
5. **Power BI'ı kapatıp yeniden açın**

---

**Harita ekleme:**

**1. Azure Haritası ekle:**
- Görsel Öğeler (Visualizations) → Azure Haritası (Azure Map) (harita ikonu)
- Eğer yoksa: Daha fazla görsel al (Get more visuals) → "Azure Maps" ara ve ekle

**2. Verileri ekle:**

**Konum (Location):**
- **Enlem** (Latitude): `Satislar[Enlem]`
- **Boylam** (Longitude): `Satislar[Boylam]`

**Boyut (Size):**
- `_Olculer[Toplam_Satis]`

**3. Konum:**
- X: 20, Y: 480
- Genişlik: 580, Yükseklik: 300

**4. Biçimlendir:**

**Görsel (Visual) → Harita ayarları (Map settings):**
- Stil (Style): Yol (Road)
- Yakınlaştırma (Zoom): Otomatik (Auto)
- Otomatik yakınlaştırma (Auto-zoom): AÇIK (ON)

**Görsel (Visual) → Baloncuk (Bubble):**
- Min boyut (Min size): 5
- Maks boyut (Max size): 25
- Saydamlık (Transparency): 30%

**Görsel (Visual) → Başlık (Title):**
- "Mağaza Lokasyonları - Satış Dağılımı"

**🗺️ Haritayı test edin:**
- Türkiye görünüyor mu?
- Mağazalar işaretlendi mi?
- Baloncuk boyutları satışa göre değişiyor mu?

---

### Üyelik Tipine Göre Satış (Halka Grafik)

**1. Halka grafik ekle:**
- Görsel Öğeler (Visualizations) → Halka grafik (Donut chart)

**2. Verileri ekle:**
- **Gösterge** (Legend): `Musteriler[Uyelik_Tipi]`
- **Değerler** (Values): `_Olculer[Toplam_Satis]`

**3. Konum:**
- X: 620, Y: 480
- Genişlik: 280, Yükseklik: 300

**4. Biçimlendir:**

**Görsel (Visual) → Başlık (Title):**
- "Üyelik Tipine Göre Satış"

**Görsel (Visual) → Gösterge (Legend):**
- Konum (Position): Sağ (Right)
- Metin boyutu (Text size): 11

**Görsel (Visual) → Ayrıntı etiketleri (Detail labels):**
- Etiket içerikleri (Label contents): Kategori, Yüzde (Category, Percentage)
- Konum (Position): İçeride (Inside)

**Görsel (Visual) → Veri renkleri (Data colors):**
- Gold: Altın rengi (#FFD700)
- Premium: Gümüş (#C0C0C0)
- Standart: Bronz (#CD7F32)

---

### Dilimleyiciler - Filtreler (Slicers)

#### Dilimleyici 1: Tarih Aralığı

**1. Dilimleyici ekle:**
- Görsel Öğeler (Visualizations) → Dilimleyici (Slicer)

**2. Veri:**
- Alan (Field): `Tarih[Date]`

**3. Konum:**
- X: 920, Y: 480
- Genişlik: 280, Yükseklik: 80

**4. Biçimlendir:**
- Dilimleyici ayarları (Slicer settings) → Stil (Style): Arasında (Between)
- Görsel (Visual) → Dilimleyici üst bilgisi (Slicer header) → Başlık (Title): "Tarih Aralığı"

---

#### Dilimleyici 2: Kategori

**1. Yeni dilimleyici**

**2. Veri:**
- `Satislar[Kategori]`

**3. Konum:**
- X: 920, Y: 580
- Genişlik: 280, Yükseklik: 100

**4. Biçimlendir:**
- Stil (Style): Açılır liste (Dropdown)
- Seçim (Selection) → CTRL ile çoklu seçim (Multi-select with CTRL): AÇIK (ON)
- Dilimleyici üst bilgisi (Slicer header): "Kategori Seçin"

---

#### Dilimleyici 3: Şehir

**1. Yeni dilimleyici**

**2. Veri:**
- `Magazalar[Sehir]`

**3. Konum:**
- X: 920, Y: 700
- Genişlik: 280, Yükseklik: 80

**4. Biçimlendir:**
- Stil (Style): Açılır liste (Dropdown)
- Değerler (Values) → Arama (Search): AÇIK (ON)
- Yer tutucu metni (Placeholder text): "Şehir Seçin..."

---

### Başlık Ekleme

**1. Metin kutusu ekle:**
- Ekle (Insert) sekmesi → Metin kutusu (Text box)

**2. Yazı:**
```
E-TİCARET ANALİZ PANOSU
```

**3. Biçim:**
- Yazı tipi (Font): Segoe UI Bold
- Boyut (Size): 24
- Renk (Color): Koyu mavi (#1F4E78)
- Hizalama (Alignment): Ortala (Center)

**4. Konum:**
- En üst, ortada
- Genişlik: 1200

---

### ✅ Pano Kontrolü

Panonuz şöyle görünmeli:

```
[        E-TİCARET ANALİZ PANOSU        ]

[Toplam Satış] [Sipariş] [Ort.Sepet] [Kar Marjı]

[Aylık Trend Grafiği]    [Kategori Çubuk Grafik]

[Mağaza Haritası]        [Üyelik Halka]  [Filtreler]
```
#
**Test edin:**
1. Tarih filtresinden bir ay seçin → Tüm görseller güncellensin
2. Kategori'den "Elektronik" seçin → Sadece elektronik verisi görünsün
3. Şehir'den "İstanbul" seçin → Haritada sadece İstanbul görünsün
4. Filtreleri temizleyin → Eski haline dönmeli

---
## 🔍 <a id="bolum-5"></a>Bölüm 5: Dashboard Sayfası 3 - Ürün Detay Analizi

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

## 👥 <a id="bolum-6"></a>Bölüm 6: Dashboard Sayfası 4 - Müşteri Analizi

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

## 🚀 <a id="bolum-7"></a>Bölüm 7: İleri Özellikler (15 dakika)

### Yeni Sayfa: Mağaza Analizi

**1. Yeni sayfa ekle:**
- Sol altta `+` butonuna tıkla
- Yeniden Adlandır (Rename) → "Mağaza Analizi"

---
### Görseller:

#### 1. Mağaza KPI Kartları (Üst Satır)

**4 adet Card:**
- En İyi Mağaza (TOPN kullan)
- Ortalama Mağaza Satışı
- Toplam Mağaza Sayısı
- Ortalama Sipariş/Mağaza

**DAX Formülleri:**
```DAX
En_Iyi_Magaza = CALCULATE(SELECTEDVALUE(Magazalar[Magaza_Adi]), TOPN(1, ALL(Magazalar), [Toplam_Satis], DESC))
```
```DAX
Ortalama_Magaza_Satis = AVERAGEX(VALUES(Magazalar[Magaza_ID]), [Toplam_Satis])
```
```DAX
Magaza_Sayisi = DISTINCTCOUNT(Magazalar[Magaza_ID])
```
```DAX
Siparis_Per_Magaza = DIVIDE([Siparis_Sayisi], [Magaza_Sayisi])
```

### Mağaza Performans Tablosu

**1. Tablo ekle:**
- Görsel Öğeler (Visualizations) → Tablo (Table)

**2. Sütunlar ekle (sırayla sürükle):**
- `Magazalar[Magaza_Adi]`
- `Magazalar[Sehir]`
- `Magazalar[Bolge]`
- `_Olculer[Toplam_Satis]`
- `_Olculer[Siparis_Sayisi]`
- `_Olculer[Kar_Marji]`

**3. Biçimlendir:**

**Görsel (Visual) → Stil (Style):**
- Stil ön ayarı (Style preset): Değişen satırlar (Alternating rows)

**Görsel (Visual) → Değerler (Values):**
- Toplam_Satis için:
  - Sağ tıkla → Koşullu biçimlendirme (Conditional formatting) → Veri çubukları (Data bars)
  - Renk (Color): Mavi

**Görsel (Visual) → Kılavuz (Grid):**
- Satır dolgusu (Row padding): 5
- Metin boyutu (Text size): 11

**Görsel (Visual) → Sütun üst bilgileri (Column headers):**
- Yazı tipi rengi (Font color): Beyaz
- Arka plan rengi (Background color): Koyu mavi (#1F4E78)

---
#### Mağaza Performans Tablosu (Matrix)

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

---
#### Azure Map - Mağaza Lokasyonları

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

> **Önemli Not:** Azure Maps kullanmak için Power BI'da etkinleştirmeniz gerekebilir:
> - File → Options → Security → Map and filled map visuals → Enable

---
#### Şehir ve Bölge Bazlı Satış (Treemap)

**Treemap:**
- Category: Magazalar[Sehir], Magazalar[Bolge]
- Values: Toplam_Satis
- Renkler: Kategori bazlı otomatik

#### Mağaza Karşılaştırma (Clustered Bar Chart)

**Clustered Bar Chart:**
- Axis: Magazalar[Magaza_Adi]
- Values: Toplam_Satis, Kar
- Sort: Toplam_Satis (Descending)
- Data labels: Açık
---

#### Mağaza Trend Analizi (Line Chart)

**Line Chart:**
- X-axis: Tarih[Ay]
- Y-axis: Toplam_Satis
- Legend: Magazalar[Magaza_Adi] (Top 5 filtre ekle)
---

### En İyi 10 Mağaza (Çubuk Grafik)

**1. Kümelenmiş çubuk grafik ekle**

**2. Verileri ekle:**
- Y ekseni (Y-axis): `Magazalar[Magaza_Adi]`
- X ekseni (X-axis): `_Olculer[Toplam_Satis]`

**3. İlk 10 filtresi ekle:**
- Filtreler (Filters) paneli → Magaza_Adi üzerine sürükle
- Filtre türü (Filter type): İlk N (Top N)
- Öğeleri göster (Show items): İlk (Top) 10
- Değere göre (By value): Toplam_Satis

**Top 10 Mağaza Filtresi DAX Kodları:**
```DAX
Top_10_Magaza = IF(RANKX(ALL(Magazalar[Magaza_Adi]), [Toplam_Satis], , DESC) <= 10, 1, 0)
```
> Bu ölçüyü Visual level filter olarak kullanın (value = 1)

**4. Biçimlendir:**
- Sırala (Sort): Azalan (Descending)
- Veri etiketleri (Data labels): AÇIK (ON)
- Başlık (Title): "En İyi 10 Mağaza"

---

### Detaya Gitme Özelliği

**Detaya gitme nedir?**
Bir grafiğe sağ tıklayıp detay sayfasına gitmek.

**Yeni sayfa oluştur: "Mağaza Detay"**

**1. Detaya gitme alanını ekle:**
- Yeni sayfada Filtreler (Filters) paneli
- **Detaya gitme** (Drill through) bölümüne `Magazalar[Magaza_Adi]` sürükle

**2. Sayfayı tasarla:**

**Mağaza Adı (Kart):**
- Alan (Field): `Magazalar[Magaza_Adi]`
- Büyük yazı tipi, merkezde

**Günlük Satış Trendi (Çizgi):**
- X ekseni: `Tarih[Tarih]`
- Y ekseni: `Toplam_Satis`

**En Çok Satan 5 Ürün (Tablo):**
- Sütunlar: Urun_Adi, Toplam_Satis, Siparis_Sayisi
- İlk 5 filtresi

**3. Kullanımı test et:**
```
"Ana Özet" sayfasına dön
→ Haritada bir mağaza baloncuğuna sağ tıkla
→ Detaya git (Drill through) → Mağaza Detay
→ Sol üstte otomatik "Geri" butonu var
```
#
---

### Yer İmleri ile Senaryolar

**Yer imi (Bookmark) nedir?**
Pano durumunu kaydetme. Filtrelerin durumu, görsellerin görünürlüğü kaydedilir.

**Yer İmi 1: Tüm Veriler**

1. Ana Özet sayfasında
2. Tüm filtreleri temizle
3. **Görünüm** (View) → **Yer İmleri** (Bookmarks) → **Ekle** (Add)
4. İsim: "Tüm Veriler"

**Yer İmi 2: Sadece İstanbul**

1. Şehir filtresinden sadece "İstanbul" seç
2. Yer İmleri (Bookmarks) → Ekle (Add)
3. İsim: "İstanbul Mağazaları"

**Yer İmi 3: Son 3 Ay**

1. Tarih filtresinden son 3 ayı seç
2. Yer İmleri (Bookmarks) → Ekle (Add)
3. İsim: "Son 3 Ay"

**Düğme ile Yer İmlerini Kullanma:**

1. **Ekle** (Insert) → **Düğme** (Button) → **Boş** (Blank)
2. Düğme seçili → Sağ panel **Biçimlendir** (Format)
3. **Eylem** (Action) bölümünü genişlet
4. Eylem (Action): AÇIK (ON)
5. Tür (Type): Yer imi (Bookmark)
6. Yer imi (Bookmark): "İstanbul Mağazaları" seç
7. Düğme metni (Button text): "İstanbul'a Git"

3 yer imi için 3 düğme oluşturun ve yan yana dizin.

---

### Koşullu Biçimlendirme

**Kar Marjına göre renklendirme:**

**1. Mağaza tablosunda:**
- Kar_Marji sütununa sağ tıkla
- Koşullu biçimlendirme (Conditional formatting)
- Arka plan rengi (Background color)

**2. Kuralları ayarla:**
```
Biçim stili (Format style): Kurallar (Rules)

Kural 1:
- Şu olursa (If): Kar_Marji
- Şundan küçükse (is less than): 0.20 (20%)
- Renk (Color): Kırmızı (#DC3545)

Kural 2:
- Ve şu olursa (And if): Kar_Marji
- Şundan büyük veya eşitse (is greater than or equal to): 0.20
- Ve şundan küçükse (and is less than): 0.30
- Renk (Color): Sarı (#FFC107)

Kural 3:
- Ve şu olursa (And if): Kar_Marji
- Şundan büyük veya eşitse (is greater than or equal to): 0.30
- Renk (Color): Yeşil (#28A745)
```
#
**Uygula** (Apply) → **Tamam** (OK)

Artık kar marjı düşük olan mağazalar kırmızı, yüksek olanlar yeşil görünecek!

---
## 🎓 <a id="ek-ipucu"></a>Ders İçin Ekstra İpuçları

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

## 📝 <a id="odev"></a>Ödev

### Görev: Kendi Mağaza Analiz Sayfanızı Oluşturun

**Yeni bir sayfa oluşturun: "Benim Mağaza Analizim"**

**Minimum gereksinimler:**

1. **4 KPI Kartı:**
   - Toplam mağaza sayısı
   - Ortalama mağaza satışı
   - En yüksek satışlı mağaza adı
   - En düşük satışlı mağaza adı

2. **Şehir Bazlı Analiz:**
   - Şehirlere göre toplam satış (Çubuk grafik)
   - Sırala: En yüksekten en düşüğe

3. **Bölgesel Dağılım:**
   - Ağaç haritası (Treemap) kullan
   - Şehir ve Bölge hiyerarşisi

4. **Trend Analizi:**
   - En iyi 5 mağazanın aylık satış trendi
   - Çizgi grafik, her mağaza farklı renk

5. **Filtreler:**
   - Tarih dilimleyici
   - Şehir dilimleyici

**Bonus özellikler:**

6. **Koşullu Biçimlendirme:**
   - Tabloda kar marjına göre renklendirme

7. **Detaya Gitme:**
   - Şehir detay sayfası oluştur
   - Şehre tıklandığında detaya gitsin

8. **Yer İmleri:**
   - En az 2 yer imi oluştur
   - Düğmeler ile geçiş yap

9. **Yaratıcılık:**
   - Kendi bulduğunuz bir analiz
   - Örnek: Metrekare başına satış, açılış yılına göre performans vb.

---

## ❓ <a id="sss"></a>Sık Sorulan Sorular

### S1: "DAX formülü hata veriyor, ne yapmalıyım?"

**Cevap:**
1. Formülü kontrol et:
   - Tablo ve sütun adları doğru mu?
   - BÜYÜK harfler doğru mu?
   - Parantezler dengeli mi?

2. Hata mesajını oku:
   - "Column not found" → Sütun adı yanlış
   - "Syntax error" → Virgül veya parantez hatası

3. Formülü satır satır yaz, tek seferde değil

**Yaygın Hatalar:**
```
❌ SUM(Tutar)           → Tablo adı yok
✅ SUM(Satislar[Tutar])

❌ [Toplam Satis]       → Boşluk var
✅ [Toplam_Satis]

❌ related(Maliyet)     → Büyük harf önemli
✅ RELATED(Urunler[Maliyet])
```
#
**Kontrol Listesi:**
- [ ] Tüm DAX fonksiyonları BÜYÜK HARF
- [ ] Tablo[Sütun] formatı doğru
- [ ] Parantezler dengeli mi?
- [ ] Virgül/noktalı virgül doğru mu? (Türkçe Windows: noktalı virgül)

---

### S2: "Harita Türkiye'yi göstermiyor?"

**Cevap:**
1. Azure Haritaları etkin mi?
   - Dosya → Seçenekler → Güvenlik → Kontrol et

2. İnternet bağlantınız var mı?

3. Enlem/Boylam doğru formatta mı?
   - Veri görünümünde kontrol et
   - Ondalık sayı (Decimal Number) olmalı

4. Manuel yakınlaştırma dene:
   - Harita ayarları → Otomatik yakınlaştırma: KAPAT
   - Mouse ile Türkiye'ye yakınlaştır

---

### S3: "Aylar alfabetik sıralanıyor (Ağustos, Aralık, Ekim...)"

**Cevap:**
```
Veri Görünümü → Tarih tablosu → Ay sütunu seç
→ Sütun Araçları → Sıralama Ölçütü → Ay_No
```

Bu adımı yaptıktan sonra grafik doğru sıralanacak!

---

### S4: "Görseller yavaş yükleniyor"

**Cevap:**
1. Gereksiz sütunları gizle (görsellerde kullanmadığın)
2. Filtre sayısını azalt
3. Görsel sayısını azalt (her sayfada max 8-10)
4. Toplamları (aggregations) kullan, satır satır veri gösterme

---

### S5: "Dosyamı kaydettim ama görseller gitti?"

**Cevap:**
- .pbix dosyasını kaydettiğinizden emin olun
- Dosya → Farklı kaydet → .pbix formatı seç
- Otomatik kaydetme: Dosya → Seçenekler → Genel → Otomatik kaydet

---

### S6: "Detaya gitme çalışmıyor?"

**Cevap:**
1. Detay sayfasında Detaya gitme (Drill through) alanı eklenmiş mi?
2. Alan doğru sürüklendi mi? (Magaza_Adi gibi)
3. Sağ tıkladığınız görselde o alan var mı?

---

### S7: "Ölçülerim Alanlar panelinde gözükmüyor?"

**Cevap:**
- Rapor Görünümü'nde misiniz? (Sol menü, ilk ikon)
- _Olculer tablosunu genişletin (ok işareti)
- Ölçüler sigma (Σ) simgesi ile gösterilir

---

## 📚 <a id="kaynaklar"></a>Ek Kaynaklar

### Ücretsiz Eğitimler

**1. Microsoft Learn - Power BI Temelleri**
- Link: https://learn.microsoft.com/tr-tr/training/powerplatform/power-bi
- Sertifika: Ücretsiz
- Süre: ~5 saat

**2. YouTube Kanalları**
- **Guy in a Cube** (İngilizce): https://youtube.com/guyinacube
- **Curbal** (İngilizce): https://youtube.com/curbal
- **Power BI Türkiye**: Türkçe içerikler

**3. BTK Akademi**
- Power BI eğitimleri
- Türkçe, ücretsiz
- Link: https://www.btkakademi.gov.tr

---

### DAX Öğrenmek İçin

**Websiteleri:**
- **SQLBI**: https://www.sqlbi.com (En iyi DAX kaynağı)
- **DAX Guide**: https://dax.guide (DAX fonksiyon rehberi)

**Kitap:**
- "The Definitive Guide to DAX" - Marco Russo & Alberto Ferrari
- İleri seviye ama çok kapsamlı

---

### Pratik Yapmak İçin

**Ücretsiz Veri Setleri:**
1. **Kaggle**: https://www.kaggle.com/datasets
2. **data.gov.tr**: Türkiye kamu verileri
3. **Our World in Data**: https://ourworldindata.org

**Challenge Siteleri:**
- **Workout Wednesday**: Haftalık Power BI challenges
- **Data Viz Society**: Topluluk projeleri

---

### İleri Seviye Konular (Sonraki Adımlar)

1. **Power Query (M Dili)**
   - Gelişmiş veri dönüştürme
   - Özel fonksiyonlar

2. **İleri DAX**
   - Variables (VAR)
   - İteratör fonksiyonlar (SUMX, FILTER)
   - Context Transition

3. **Power BI Service**
   - Rapor yayınlama
   - Paylaşım ve işbirliği
   - Automatic refresh

4. **Row Level Security (RLS)**
   - Kullanıcı bazlı veri filtreleme

5. **Python/R Entegrasyonu**
   - Gelişmiş analizler
   - Machine learning

---

## 🎯 Başarı İpuçları

**✅ Yapmanız Gerekenler:**
- Her gün 30 dakika pratik yapın
- Hata yapmaktan korkmayın - en iyi öğrenme yöntemi!
- Gerçek veri setleri ile çalışın
- Topluluk forumlarını takip edin (Power BI Community)
- LinkedIn'de Power BI projelerinizi paylaşın

**❌ Yapmamanız Gerekenler:**
- Son dakikada ödev yapmayın
- Sadece hocayı takip etmeyin, kendiniz de deneyin
- Karmaşık görseller ile başlamayın, basit başlayın
- DAX'ı ezberlemene gerek yok, mantığını anla

---

## 💪 Motivasyon

**Power BI öğrenmek bir yolculuk!**

- İlk haftanız zor geçebilir - normal!
- 2. haftada DAX'a alışacaksınız
- 3. haftada kendi projelerinizi yapabileceksiniz
- 1 ay sonra CV'nize "Power BI" ekleyebileceksiniz

**Unutmayın:** 
> "Her uzman bir zamanlar acemiydi. Fark, vazgeçmemeleriydi."

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

## 🎉 Tebrikler!

Power BI'ın temellerini öğrendiniz! Artık:
- ✅ Veri modelleyebilirsiniz
- ✅ DAX yazabilirsiniz
- ✅ Profesyonel panolar oluşturabilirsiniz
- ✅ Coğrafi analizler yapabilirsiniz

**Sonraki adım:** Kendi projelerinizi yapmaya devam edin!

Başarılar! 🚀

---
