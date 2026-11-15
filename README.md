# 🧠 BrainScan – Beyin MR Görüntülerinde Tümör Sınıflandırması

Beyin MR görüntülerini dört sınıfa ayıran uçtan uca bir derin öğrenme projesi: `glioma`, `meningioma`, `pituitary`, `notumor`.

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange)
![License](https://img.shields.io/badge/License-MIT-green)

> ⚠️ **Uyarı:** Bu proje yalnızca **eğitim ve araştırma amaçlıdır.** Modeller klinik olarak doğrulanmamıştır; tıbbi teşhis, tedavi kararı veya hasta değerlendirmesi için **kullanılamaz.**

## Neler var?

Tüm çalışma tek bir notebook'ta: [`notebooks/BrainScan_Projesi.ipynb`](notebooks/BrainScan_Projesi.ipynb)

| Aşama | İçerik |
|---|---|
| Veri analizi | Sınıf dağılımı, örnek görüntüler, çözünürlük/renk modu, **tekrar eden görüntü ve veri sızıntısı kontrolü** (MD5 + görsel hash) |
| Modeller | Baseline CNN (4 bloklu) ve ImageNet ağırlıklı **EfficientNetB0** (transfer learning + fine-tuning) |
| Eğitim | Data augmentation, EarlyStopping, ReduceLROnPlateau, ModelCheckpoint, eğitim süresi ölçümü |
| Karşılaştırma | Accuracy, Precision, Recall, F1, ROC-AUC, eğitim süresi (aynı test setinde) |
| Analiz | Confusion matrix, classification report, çok sınıflı ROC, yanlış tahmin analizi |
| Yorumlanabilirlik | Grad-CAM (orijinal görüntü ile yan yana) |
| Kullanım | Bilgisayarından yeni bir MR görüntüsü seçip tahmin + olasılık grafiği + Grad-CAM |

### Metodolojik notlar
- Test seti eğitimde veya epoch/model seçiminde **kullanılmaz**; seçim doğrulama kaybına göre yapılır.
- Test ile aynı görünen ve eğitim içinde tekrar eden görüntüler eğitimden çıkarılır (`TEKRARLARI_CIKAR`).
- Fine-tuning sırasında BatchNormalization katmanları dondurulur, küçük öğrenme oranı (1e-5) kullanılır.
- Sınıf sırası tüm modellerde aynıdır ve `model_bilgisi.json` ile modellerin yanına kaydedilir.

## Veri seti

[Brain Tumor MRI Dataset (Kaggle)](https://www.kaggle.com/datasets/masoudnickparvar/brain-tumor-mri-dataset) verisini indirip şu yapıda bir klasöre çıkar. Veri bu repoda **yer almaz.**

```
data/
├── Training/{glioma, meningioma, notumor, pituitary}/
└── Testing/{glioma, meningioma, notumor, pituitary}/
```

## Kurulum ve çalıştırma

```bash
git clone https://github.com/<kullanici-adin>/brainscan.git
cd brainscan
python -m venv .venv
.venv\Scripts\activate          # Windows (Linux/Mac: source .venv/bin/activate)
pip install -r requirements.txt
jupyter notebook notebooks/BrainScan_Projesi.ipynb
```

1. Notebook'un **3. bölümündeki** `DATA_DIR` yolunu kendi veri klasörüne göre düzenle.
2. Önce `HIZLI_TEST = True` ile kodu hızlıca dene (sonuçlar anlamsızdır), sonra `False` yapıp gerçek eğitimi çalıştır.
3. GPU yoksa EfficientNet eğitimi yavaş olabilir; Google Colab kullanılabilir.

Çıktılar `outputs/` altına yazılır: `.keras` modeller, grafikler, classification report'lar ve `model_karsilastirma.csv`.

## Sonuçlar

_Notebook'u çalıştırdıktan sonra `outputs/reports/model_karsilastirma.csv` içindeki değerleri buraya yaz ve `outputs/figures/` altındaki görselleri `assets/` klasörüne kopyalayıp aşağıdaki satırların yorumunu kaldır._

| Model | Accuracy | Macro F1 | ROC-AUC (macro) | Eğitim süresi |
|---|---|---|---|---|
| Baseline CNN | – | – | – | – |
| EfficientNetB0 (Transfer) | – | – | – | – |
| EfficientNetB0 (Fine-Tuned) | – | – | – | – |

<!--
![Model karşılaştırma](assets/model_karsilastirma.png)
![Confusion Matrix](assets/confusion_matrix_efficientnet_b0_finetuned.png)
![Grad-CAM](assets/gradcam_ornekleri.png)
-->

## Proje yapısı

```
brainscan/
├── notebooks/
│   └── BrainScan_Projesi.ipynb
├── assets/                 # README görselleri
├── requirements.txt
├── .gitignore
├── LICENSE
└── README.md
```

## Sınırlamalar

- Tek bir açık veri seti kullanıldı; dış doğrulama yapılmadı.
- Veri setinde hasta kimliği yok; aynı hastanın farklı kesitleri hem eğitimde hem testte olabilir. Kopya kontrolü yalnızca aynı/görsel olarak aynı görüntüleri bulur, bu yüzden raporlanan başarı gerçek dünya performansından iyimser olabilir.
- Grad-CAM modelin neye baktığına dair yaklaşık bir fikir verir; klinik açıklama değildir.
- Softmax olasılıkları kalibre değildir.

## Lisans

MIT. Ayrıntılar için [`LICENSE`](LICENSE) dosyasına bak.
