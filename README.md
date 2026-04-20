# Road Damage Classification

Bu proje, yol hasarı görüntülerini üç sınıfa ayırmak için geliştirilmiş bir görüntü sınıflandırma çalışmasıdır:

- `Crack`
- `Pothole`
- `Manhole`

Projede transfer learning yaklaşımıyla iki farklı model eğitildi:

- `EfficientNet-B0`
- `ResNet50`

Ana çalışma dosyası:

- `road_damage_classifier.ipynb`

Kaydedilmiş model dosyası:

- `resnet50_road_damage.pt`

## Projenin Amacı

Amaç, kırpılmış yol hasarı görüntülerinin hangi hasar türüne ait olduğunu tahmin etmektir. Bu proje bir `classification` projesidir; yani model doğrudan nesnenin yerini bulmaz, verilen görüntünün hangi sınıfa ait olduğunu tahmin eder.

Bu yüzden proje:

- `object detection` değildir
- `segmentation` değildir
- en iyi sonucu, hasarın görüntü içinde net ve belirgin olduğu crop görüntülerde verir

## Dataset Özeti

Sınıflandırma verisi, etiketli yol hasarı veri setinden crop üretilerek hazırlandı.

Toplam veri:

- kaynak görüntü sayısı: `2009`
- toplam crop sayısı: `4736`

Sınıf dağılımı:

- `Crack`: `2518`
- `Pothole`: `1261`
- `Manhole`: `957`

### Güncel Train/Test Dağılımı

Train:

- `Crack`: `2014`
- `Pothole`: `1009`
- `Manhole`: `766`
- toplam: `3789`

Test:

- `Crack`: `504`
- `Pothole`: `252`
- `Manhole`: `191`
- toplam: `947`

## Veri Tarafında Yaptığımız İyileştirmeler

Bu projede yalnızca model eğitimi değil, veri kalitesi tarafı da iyileştirildi.

### 1. Train/Test Leakage Sorunu Temizlendi

İlk ayrımda aynı ana görüntüden üretilen farklı crop'lar hem `train` hem `test` içine dağılabiliyordu. Bu durum test performansını olduğundan daha iyi gösterebilirdi.

Veri yeniden düzenlenerek split görüntü bazlı temizlendi.

Yani aynı kaynak görüntü ailesi artık hem train hem test içinde yer almıyor.

### 2. Sınıf Yerleşimleri Kontrol Edildi

Crop'ların yanlış klasöre düşüp düşmediği kontrol edildi.

Sonuç:

- `Crack`, `Pothole` ve `Manhole` sınıf atamaları genel olarak doğru
- belirgin sınıf-karışımı tespit edilmedi
- veri yapısı eğitim için kullanılabilir durumda

## Kullanılan Modeller

### EfficientNet-B0

- ImageNet pretrained ağırlıklarla başlatıldı
- feature extractor'ın büyük bölümü donduruldu
- son bloklar fine-tuning için açıldı
- classifier katmanı yeniden tanımlandı

### ResNet50

- ImageNet pretrained ağırlıklarla başlatıldı
- backbone büyük ölçüde donduruldu
- `layer4` fine-tuning için açıldı
- son fully connected katman yeniden tanımlandı

## Veri Ön İşleme ve Augmentation

Notebook içinde kullanılan temel dönüşümler:

Train dönüşümleri:

- `Resize((256, 256))`
- `RandomCrop(224)`
- `RandomHorizontalFlip(p=0.5)`
- `RandomRotation(degrees=15)`
- `ColorJitter(brightness=0.2, contrast=0.2)`
- `ToTensor()`
- `Normalize(ImageNet mean/std)`

Test dönüşümleri:

- `Resize((256, 256))`
- `CenterCrop(224)`
- `ToTensor()`
- `Normalize(ImageNet mean/std)`

Bu yapı, pretrained modellerle uyumlu giriş üretmek ve eğitim verisinde çeşitlilik sağlamak için kullanıldı.

## Eğitim Ayarları

Notebook içindeki güncel eğitim ayarları:

- optimizer: `AdamW`
- learning rate: `1e-4`
- weight decay: `1e-2`
- batch size: `32`
- epoch: `10`

## Güncel Sonuçlar

Notebook içindeki son çalıştırmaya göre en iyi test doğrulukları:

| Model | En İyi Test Doğruluğu |
|---|---:|
| EfficientNet-B0 | `74.57%` |
| ResNet50 | `75.16%` |

### Sonuç Yorumu

- En iyi sonucu `ResNet50` verdi
- `EfficientNet-B0` da yakın performans gösterdi
- mevcut repoda saklanan model: `resnet50_road_damage.pt`

## Performansı Artırmak İçin Neler Yaptık?

Projede performans ve güvenilirliği artırmak için şu adımlar uygulandı:

1. Veri sızıntısı temizlendi
2. Train/test split görüntü bazlı yeniden kuruldu
3. Crop dosyaları ve sınıf klasörleri kontrol edildi
4. Transfer learning kullanıldı
5. Data augmentation uygulandı
6. İki farklı pretrained mimari karşılaştırıldı
7. Eğitilen model notebook içinden `.pt` dosyası olarak kaydedilebilir hale getirildi

## Notebook İçindeki Eklenen Kullanım Akışı

Notebook sonunda eğitilmiş modeli kaydetmek için ek hücre bulunur.

Eklenen fonksiyon:

- `save_model_checkpoint(...)`

Bu hücre sayesinde eğitimden sonra:

- `res_model`
- `class_names`
- model adı

kullanılarak model dosyası kaydedilir:

- `resnet50_road_damage.pt`

## Proje Yapısı

```text
road_damage_classification/
├── road_damage/
│   ├── train/
│   │   ├── Crack/
│   │   ├── Manhole/
│   │   └── Pothole/
│   └── test/
│       ├── Crack/
│       ├── Manhole/
│       └── Pothole/
├── road_damage_classifier.ipynb
├── resnet50_road_damage.pt
```

## Nasıl Çalıştırılır?

1. Notebook'u aç:

- `road_damage_classifier.ipynb`

2. Sırasıyla şu adımları çalıştır:

- veri yükleme hücreleri
- model oluşturma hücreleri
- eğitim hücreleri
- özet sonuç hücresi
- inference hücreleri
- model kaydetme hücresi

3. Eğitim sonunda model dosyası oluşur:

- `resnet50_road_damage.pt`

## Sonuç

Bu proje, yol hasarı sınıflandırması için transfer learning tabanlı, pratik ve geliştirilebilir bir temel sunar.

Öne çıkan kazanımlar:

- veri sızıntısı temizlendi
- split daha güvenilir hale getirildi
- iki farklı güçlü pretrained model karşılaştırıldı
- `ResNet50` ile `75.16%` test doğruluğu elde edildi
- eğitim sonrası model kaydetme akışı notebook içine eklendi

Bir sonraki mantıklı adım, bu classification yapısını `object detection` tabanlı bir sisteme dönüştürmek olacaktır.
