# Road Damage Classification

Bu proje, yol hasarı görüntülerini `Crack`, `Pothole` ve `Manhole` olarak sınıflandırmak için geliştirilmiş bir bilgisayarlı görü projesidir.

Projede transfer learning ile iki model denenmiştir:

- `EfficientNet-B0`
- `ResNet50`

Ana çalışma dosyası:

- `road_damage_classifier.ipynb`

Kaydedilmiş model:

- `resnet50_road_damage.pt`

## Proje Amacı

Amaç, kırpılmış yol hasarı görüntülerini üç sınıftan birine atamaktır:

- `Crack`
- `Pothole`
- `Manhole`

Bu çalışma bir `image classification` projesidir. Yani model nesnenin yerini bulmaz; verilen görüntünün hangi sınıfa ait olduğunu tahmin eder.

## Dataset Özeti

Toplam classification crop sayısı:

- `4736`

Sınıf dağılımı:

- `Crack`: `2518`
- `Pothole`: `1261`
- `Manhole`: `957`

Train split:

- `Crack`: `2014`
- `Pothole`: `1009`
- `Manhole`: `766`
- toplam: `3789`

Test split:

- `Crack`: `504`
- `Pothole`: `252`
- `Manhole`: `191`
- toplam: `947`

## Veri Tarafında Yaptığımız İyileştirmeler

Projede train/test sızıntısı kontrol edildi ve aynı kaynak görüntüden gelen crop’ların hem train hem test içinde yer almaması sağlandı.

Bu, test sonuçlarının daha güvenilir olmasını sağlar.

## Kullanılan Modeller

### EfficientNet-B0

- pretrained ImageNet ağırlıkları kullanıldı
- son katmanlar fine-tuning için açıldı

### ResNet50

- pretrained ImageNet ağırlıkları kullanıldı
- `layer4` fine-tuning için açıldı

## Eğitim Ayarları

- optimizer: `AdamW`
- learning rate: `1e-4`
- weight decay: `1e-2`
- batch size: `32`
- epoch: `10`

## Sonuçlar

Notebook içindeki güncel sonuçlar:

| Model | En İyi Test Doğruluğu |
|---|---:|
| EfficientNet-B0 | `74.57%` |
| ResNet50 | `75.16%` |

En iyi model:

- `ResNet50`

## Kurulum

Bu projeyi kullanmak için sanal ortam klasörünü repoya yüklemeniz gerekmez. Her kullanıcı kendi makinesinde ayrı bir sanal ortam oluşturmalıdır.

### 1. Repoyu klonla

```bash
git clone <repo-url>
cd road_damage_classification
```

### 2. Sanal ortam oluştur

Windows PowerShell:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

macOS / Linux:

```bash
python -m venv .venv
source .venv/bin/activate
```

### 3. Bağımlılıkları yükle

```bash
pip install -r requirements.txt
```

## Kullanım

Notebook dosyasını aç:

- `road_damage_classifier.ipynb`

Notebook içinde tipik akış:

1. veri yükleme
2. transform tanımlama
3. dataloader oluşturma
4. modeli eğitme
5. test doğruluğunu ölçme
6. yeni görsellerde tahmin alma
7. eğitilen modeli kaydetme

## Model Kaydetme

Notebook sonunda eğitilmiş modeli `.pt` olarak kaydetmek için ek hücre bulunur.

Kaydedilen örnek dosya:

- `resnet50_road_damage.pt`
- 
## Proje Yapısı

```text
road_damage_classification/
├── road_damage/
│   ├── train/
│   └── test/
├── road_damage_classifier.ipynb
├── resnet50_road_damage.pt
├── requirements.txt
├── .gitignore
```
