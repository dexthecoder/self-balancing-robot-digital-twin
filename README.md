# Self-Balancing Robot with System Identification and Digital Twin

> **Robot Tasarımı ve Uygulamaları — 2026-2027**
> **Grup ID:** G13 · **Konu No:** 08 · **Proje Alanı:** Dynamic Modeling & Adaptive Control

---

## 1. Proje Adı, Konu Numarası ve Grup Üyeleri

| Alan | Bilgi |
|---|---|
| Proje Adı | Self-Balancing Robot with System Identification and Digital Twin |
| Konu No | 08 |
| Proje Alanı | Dynamic Modeling & Adaptive Control |
| Grup ID | G13 |

| # | Ad Soyad | Öğrenci No | E-posta | CV |
|---|---|---|---|---|
| 1 | Polat Ceylan | 032390025 | 032390025@ogr.uludag.edu.tr | [cv/](cv/) |
| 2 | Ahmet Şahin Yılmaz | 032390070 | 032390070@ogr.uludag.edu.tr | [cv/](cv/) |
| 3 | Yusuf Cihan Yılmaz | 032390079 | 032390079@ogr.uludag.edu.tr | [cv/](cv/) |
| 4 | Yusuf İslam Çelik | 032390060 | 032390060@ogr.uludag.edu.tr | [cv/](cv/) |
| 5 | Abdullah Eren Korkmaz | 032390041 | 032390041@ogr.uludag.edu.tr | [cv/](cv/) |

---

## 2. Amaç, Problem Tanımı ve Proje Kapsamı

### Problem Tanımı
İki teker üzerinde dengede kalan bir robot, farklı yükler taşırken ya da zemin özellikleri değişirken hareket ederek belirlenmiş hedeflere ulaşmalıdır. Başlangıçta kullanılan nominal model gerçekçi sapmalar içerebilir; kütle dağılımı, sürtünme ve algılama gecikmesi değiştiğinde sabit kontrol ayarları her zaman yeterli olmayacaktır.

Sistemin **kararlılığı**, **hareket kabiliyeti** ve **enerji tüketimi** arasında oluşan ödünleşimler projenin temel mühendislik problemidir.

### Amaç
- İki tekerli ters sarkaç (wheeled inverted pendulum) robotun dinamik modelini çıkarmak,
- Çevrim içi durum kestirimi, kontrol ve parametre güncelleme unsurlarını tek bir sistemde birleştirmek,
- Digital Twin yaklaşımının robot davranışını tahmin etmede ve tasarım kararlarını test etmede nasıl kullanılabileceğini araştırmak.

### Kapsam ve İş Paketleri

| İP | İş Paketi | İçerik |
|---|---|---|
| İP1 | Dinamik Modelleme | Lagrange/Newton-Euler ile doğrusal olmayan model, state-space gösterimi, denge noktası etrafında doğrusallaştırma |
| İP2 | Simülasyon Ortamı | Sanal iki tekerli platform, yük/zemin/gecikme parametrelerinin değiştirilebilmesi, bozucu (disturbance) senaryoları |
| İP3 | Durum Kestirimi | IMU + enkoder füzyonu (Complementary / Kalman Filter), algılama gecikmesi ve gürültü modeli |
| İP4 | Kontrol | PID, LQR ve MPC kontrolcülerinin tasarımı ve karşılaştırılması |
| İP5 | Sistem Tanılama ve Adaptif Kontrol | Çevrim içi parametre kestirimi (ör. RLS), model güncelleme, adaptif kontrol |
| İP6 | Digital Twin Doğrulaması | Fiziksel model ile gözlenen davranış arasındaki hatanın ölçülmesi, modelin yetersiz kaldığı koşulların gösterilmesi |
| İP7 | Deneyler ve Rapor | Tekrarlanabilir deney senaryoları, metrikler, karşılaştırmalı teorik rapor |

### Beklenen Çalışan Simülasyon
- Sanal iki tekerli platformun başlangıçta dengelenmesi,
- İleri-geri hareket ve hedef hız/konum değişikliklerinin izlenmesi,
- Beklenmeyen dış etkilerden (itme, eğim, yük değişimi) sonra dengenin korunması,
- Parametreler değiştiğinde kontrol davranışının nasıl farklılaştığının izlenebilmesi,
- Farklı tasarım seçimlerinin güvenli hareket ve denge üzerindeki sonuçlarını karşılaştırmaya uygun tekrarlanabilir deneyler.

---

## 3. Kullanılacak Yöntemler ve Algoritmalar

| Konu | Yöntem / Algoritma |
|---|---|
| Modelleme | Nonlinear Dynamics, State-Space Modeling, Linearization |
| Sistem Tanılama | Least Squares / Recursive Least Squares (RLS), çevrim içi parametre kestirimi |
| Durum Kestirimi | Complementary Filter, Kalman Filter / Extended Kalman Filter |
| Kontrol | PID, Linear Quadratic Regulator (LQR), Model Predictive Control (MPC), Adaptive Control |
| Digital Twin | Model–gözlem hata metrikleri, model güncelleme ve doğrulama |

---

## 4. Yöntem Seçim Gerekçeleri ve Alternatifler

Kontrol yaklaşımları yalnızca hata azaltma açısından değil; **kararlılık**, **disturbance rejection**, **model belirsizliği** ve **hesaplama maliyeti** açısından da değerlendirilecektir.

| Yöntem | Avantaj | Dezavantaj |
|---|---|---|
| PID | Basit, hızlı ayarlanır, düşük hesaplama maliyeti | Çok değişkenli sistemde (açı + konum) koordinasyon zor, model değişimine duyarlı |
| LQR | Çok değişkenli optimal kontrol, sistematik tasarım | Doğrusallaştırılmış modele bağlı, kısıtları doğrudan ele almaz |
| MPC | Kısıtları (motor torku, açı sınırı) doğrudan ele alır | Yüksek hesaplama maliyeti, modele bağımlı |
| Adaptive Control | Kütle/sürtünme değişimine uyum sağlar | Kararlılık analizi daha karmaşık, yakınsama süresi |

> Seçilen modelin varsayımları ve başarısız olduğu koşullar [docs/](docs/) altındaki raporda kanıtlarla tartışılacaktır.

---

## 5. Sistem Mimarisi ve Temel Çalışma Akışı

```
            ┌────────────────────────── Digital Twin ───────────────────────────┐
            │                                                                   │
 Referans → │  Kontrolcü  →  Robot (Simülasyon)  →  Sensörler (IMU, enkoder)    │
 (hız/konum)│ (PID/LQR/MPC)        ↑                     │                      │
            │      ↑               │ bozucu etkiler      ↓                      │
            │      └──────── Durum Kestirimi (KF) ←──────┘                      │
            │                        │                                          │
            │                        ↓                                          │
            │        Sistem Tanılama (RLS) → Model Güncelleme → Kontrolcü        │
            └───────────────────────────────────────────────────────────────────┘
```

Detaylı diyagramlar: [docs/](docs/)

---

## 6. Geliştirme Araçları / Simülasyon Ortamı

| Araç | Kullanım |
|---|---|
| Python 3.10+ | Ana geliştirme dili |
| NumPy, SciPy | Dinamik model, doğrusallaştırma, LQR (Riccati çözümü) |
| Matplotlib | Grafikler ve sonuç görselleştirme |
| PyBullet | Fizik tabanlı 3B simülasyon ortamı |
| CVXPY | MPC optimizasyon problemi |
| Git / GitHub | Sürüm kontrolü ve ilerleme takibi |

> Simülasyon ortamı kesinleştiğinde bu tablo güncellenecektir.

---

## 7. Kurulum ve Projeyi Çalıştırma

```bash
git clone https://github.com/dexthecoder/self-balancing-robot-digital-twin.git
cd self-balancing-robot-digital-twin
python -m venv .venv
# Windows: .venv\Scripts\activate   |   Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
```

Simülasyonu çalıştırma komutları, kod eklendikçe burada belirtilecektir.

---

## 8. Test, Deney, Metrik ve Örnek Çıktılar

### Planlanan Deney Senaryoları
| # | Senaryo | Değişen Parametre |
|---|---|---|
| D1 | Başlangıçta dengelenme | Başlangıç eğim açısı |
| D2 | Hedef hız/konum takibi | Referans profili |
| D3 | Dış itme sonrası toparlanma | Bozucu kuvvet büyüklüğü |
| D4 | Yük değişimi | Gövde kütlesi / ağırlık merkezi yüksekliği |
| D5 | Zemin değişimi | Sürtünme katsayısı, eğim |
| D6 | Algılama gecikmesi | Sensör gecikmesi ve gürültü |

### Metrikler
- Eğim açısı RMS hatası, maksimum sapma
- Yerleşme süresi, aşım (overshoot)
- Konum/hız takip hatası
- Enerji tüketimi (∫ τ·ω dt)
- Kontrolcü hesaplama süresi
- Digital Twin model–gözlem hatası

Sonuçlar ve grafikler: [results/](results/)

---

## 9. Yol Haritası ve Düzenli İlerleme Günlüğü

### Yol Haritası
- [ ] Dinamik model ve doğrusallaştırma (İP1)
- [ ] Simülasyon ortamının kurulması (İP2)
- [ ] Durum kestirimi (İP3)
- [ ] PID ve LQR kontrolcüleri (İP4)
- [ ] MPC kontrolcüsü (İP4)
- [ ] Sistem tanılama ve adaptif kontrol (İP5)
- [ ] Digital Twin doğrulaması (İP6)
- [ ] Deneyler, karşılaştırma ve teorik rapor (İP7)

### İlerleme Günlüğü

| Tarih | Yapılan Çalışma | Mevcut Durum / Sorun | Sonraki Adım |
|---|---|---|---|
| 2026-11-20 | Grup oluşturuldu, Konu 08 seçildi, GitHub deposu ve klasör yapısı hazırlandı | Repo iskeleti hazır | Dinamik modelin çıkarılması ve simülasyon ortamının kurulması |

---

## 10. Araştırma Kaynakları ve Referanslar

Kaynaklar ve makaleler: [references/](references/)

1. *(eklenecek)*

---

## Klasör Yapısı

```
├── README.md          Proje tanıtımı ve takip
├── src/               Kaynak kodlar
├── simulation/        Çalışan simülasyon
├── docs/              Rapor ve diyagramlar
├── results/           Test çıktıları
├── cv/                Tüm grup üyelerinin CV'leri
├── requirements.txt   Yazılım bağımlılıkları
├── assets/            Görseller ve veri kaynakları
└── references/        Araştırma kaynakları ve referanslar
```
