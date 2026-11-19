# Kalp Hastalığı Tahmini

Hastaların yaş, kan basıncı, EKG ölçümleri gibi 11 özelliğinden yola çıkarak kalp hastalığı olup olmadığını tahmin eden bir ikili sınıflandırma çalışması. Amaç sadece yüksek doğruluk almak değil; veriyi anlamak, hataların türünü incelemek ve modelin neye bakarak karar verdiğini yorumlamak.

📓 **Kaggle notebook:** [buraya notebook linkini ekle]

## Veri Seti

[Heart Failure Prediction Dataset](https://www.kaggle.com/datasets/fedesoriano/heart-failure-prediction) (918 satır, 11 özellik, hedef: `HeartDisease`).

## Yaklaşım

1. **Veri kalitesi:** `Cholesterol` sütunundaki 172 ve `RestingBP` sütunundaki 1 adet sahte `0` değeri tespit edildi. `RestingBP = 0` olan satır silindi, kolesterol sıfırları eksik değer (`NaN`) sayıldı.
2. **Eksik veri analizi:** Kolesterolü eksik olan hastaların %88'i hasta (genel oran yaklaşık %55). Eksiklik rastgele değil, bu yüzden satırlar silinmedi.
3. **Ön işleme (Pipeline):** Medyan ile doldurma, standartlaştırma ve one-hot encoding. Veri sızıntısını önlemek için hepsi `Pipeline` içinde ve sadece eğitim katlarına uygulanıyor.
4. **Model karşılaştırması:** 5 katlı çapraz doğrulama ile Logistic Regression, Random Forest ve KNN.
5. **Hata analizi:** Karışıklık matrisi ve karar eşiği analizi.
6. **Yorumlama:** Logistic Regression katsayıları ve EDA bulgularıyla karşılaştırma.

## Sonuçlar

| Model | Ortalama Doğruluk | Std |
|---|---|---|
| Logistic Regression | 0.867 | 0.019 |
| Random Forest | 0.864 | 0.023 |
| KNN | 0.852 | 0.018 |

Doğruluklar birbirine çok yakın olduğu için daha basit ve yorumlanabilir olan **Logistic Regression** seçildi.

Karar eşiği analizi (çapraz doğrulama tahminleriyle):

| Eşik | Recall | Precision | Kaçırılan hasta | Yanlış alarm |
|---|---|---|---|---|
| 0.3 | 0.94 | 0.82 | 30 | 108 |
| 0.4 | 0.92 | 0.84 | 43 | 87 |
| 0.5 | 0.90 | 0.87 | 52 | 70 |
| 0.6 | 0.85 | 0.88 | 76 | 58 |

En belirleyici özellikler: göğüs ağrısı tipi (`ChestPainType`), ST eğimi (`ST_Slope`), egzersiz anjini (`ExerciseAngina`) ve cinsiyet (`Sex`).

## Nasıl Çalıştırılır?

```bash
git clone https://github.com/<kullanici-adi>/<repo-adi>.git
cd <repo-adi>
pip install -r requirements.txt
jupyter notebook
```

Veri setini Kaggle'dan indirip `heart.csv` adıyla proje klasörüne koyman gerekiyor. Notebook içindeki dosya yolunu (`/kaggle/input/...`) kendi dosya yoluna göre değiştir.

`requirements.txt` için örnek:

```
pandas
numpy
scikit-learn
matplotlib
seaborn
```

## Sınırlılıklar

- Veri küçük (917 satır), skorlar yaklaşık ±2 puan belirsizlik taşıyor.
- Örneklem erkek ağırlıklı (724 erkek, 193 kadın).
- Kolesterolün yaklaşık %19'u eksik ve eksiklik rastgele değil.
- Karar eşiği, sonuçlara bakılarak aynı veri üzerinde seçildi. Gerçek bir uygulamada ayrı bir doğrulama verisi gerekir.
- Model ilişkiyi gösterir, nedenselliği değil. Tıbbi karar aracı değildir.
