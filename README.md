# 🏔️ Top Climbing

2D fizik tabanlı, **Hill Climb** tarzı mobil tırmanma oyunu. Araç gövdesi + iki tekerlek + süspansiyon (WheelJoint2D) ile tamamen fizik tabanlı sürüş, sonsuz prosedürel arazi, yakıt yönetimi, coin toplama, garaj/upgrade ekonomisi ve satın alınabilir haritalar içerir.

> **Unity sürümü:** 2022.3 LTS (test edilen: `2022.3.62f3`). Unity 6 ile değil, 2022.3 LTS ile kuruludur.

---

## 🚀 Tek Tıkla Kurulum

Bu projede sahneler, prefab'lar ve görseller **repoda hazır gelir**, ama istersen tek tıkla yeniden üretebilirsin:

1. Projeyi Unity ile aç ve scriptlerin derlenmesini bekle (sağ alttaki spinner durana kadar).
2. Üst menüden: **Top Climbing → 1) Tüm Oyunu Kur (Build All)**
3. Çıkan onayda **"Evet, Kur"** de.

Bu işlem otomatik olarak şunları üretir:

- **Görseller** → `Assets/Art/` (araç, tekerlek, coin, yakıt bidonu, butonlar, tepe/bulut arka planları) — hepsi gerçek PNG.
- **Prefab'lar** → `Assets/Prefabs/` (Vehicle, Coin, FuelCan)
- **Sahneler** → `Assets/Scenes/` (`MainMenu.unity`, `Game.unity`)
- **Build Settings** sahne listesi (MainMenu = 0, Game = 1)

Kurulum bitince **MainMenu** sahnesi otomatik açılır. **Play**'e bas, **RACE** ile oyna.

> ⚠️ Prefab / sprite / sahne değişikliklerinden sonra sadece Play yetmez — **Build All**'u tekrar çalıştırman gerekir.
>
> Kayıtları sıfırlamak için: **Top Climbing → Kayıtları Sıfırla**.

---

## 🎮 Kontroller

| Eylem        | Klavye (Editor testi) | Mobil           |
|--------------|------------------------|-----------------|
| Gaz / İleri  | Sağ ok veya **D**      | Sağ alt buton   |
| Fren / Geri  | Sol ok veya **A**      | Sol alt buton   |
| Duraklat     | —                      | Sağ üst **II**  |

Gaz verince aracın burnu havalanır, frenle düzeltilir — tamamen fizik tabanlı.

---

## ✨ Özellikler

- **Fizik tabanlı araç** — gövde + 2 tekerlek + süspansiyon (WheelJoint2D)
- **Sonsuz prosedürel arazi** — chunk üretimi + obje havuzu (pool) ile geri dönüşüm
- **Satın alınabilir haritalar** — her harita kendi arka planını ve biome'unu belirler
- **Biome'a göre değişen arka plan** + paralaks katmanlar
- **Yakıt sistemi** — bidonlarla doldurma, boşta ve gazda farklı tüketim
- **Coin toplama** + obje havuzu
- **Game Over** — yakıt bitti ya da araç uzun süre ters kaldı
- **Tam UI akışı** — Ana Menü / Garaj / Upgrade / Haritalar / Ayarlar / HUD / Pause / Game Over
- **Kalıcı kayıt (PlayerPrefs)** — coin, sahip olunan araçlar, seçili araç, upgrade'ler, ses ayarları, rekor
- **Prosedürel ses** — müzik playlist'i + efektler kod ile üretilir, ses dosyası gerekmez
- **Yumuşak kamera takibi** (SmoothDamp)

---

## 🧩 Mimari

Her sistem ayrı bir script olarak tasarlanmıştır. Tüm kodlar `TopClimbing` namespace'i altındadır (editor araçları `TopClimbing.EditorTools`).

```
Assets/Scripts/
├── Core/      GameManager, SaveSystem, AudioManager, InputManager, ObjectPool, ProceduralAudio
├── Data/      GameConfig, VehicleDefinition (+VehicleCatalog), UpgradeCatalog, MapCatalog
├── Vehicle/   VehicleController (WheelJoint2D fizik), FuelSystem
├── World/     TerrainGenerator + TerrainChunk (sonsuz arazi),
│              BackgroundManager (paralaks + biome), Biome, Pickup, Coin, FuelCan
├── Camera/    CameraFollow (SmoothDamp yumuşak takip)
└── UI/        MainMenuUI, GarageUI, UpgradeUI, MapsUI, SettingsUI, HUDController,
               PauseMenuUI, GameOverUI, MobileButton, ToastMessage, UIHelper

Assets/Editor/
├── GameBuilder    üretici akış (Build All)
├── SpriteFactory  görsel/PNG üretimi
└── UIBuilder      UI kurucu
```

---

## ⚙️ Denge Ayarları

Çoğu ayar `Assets/Scripts/Data/GameConfig.cs` içinde tek yerde toplanmıştır:

- **Yakıt:** başlangıç / maksimum, boşta ve gazda tüketim, bidon miktarı
- **Game Over:** ters dönme süresi ve açısı

Diğer ayarların yeri:

| Ne                | Nerede                                  |
|-------------------|-----------------------------------------|
| Araç gücü/hızı/ağırlığı | `VehicleCatalog`                  |
| Upgrade etkileri  | `UpgradeCatalog`                        |
| Haritalar & biome | `MapCatalog`                            |
| Arazi zorluğu     | `TerrainGenerator.Height()` fonksiyonu  |

> Not: Biome artık **seçili haritaya** göre belirlenir (mesafeye göre değil). `MapCatalog` üzerinden yönetilir.

---

## 📦 Proje Yapısı

```
Assets/
├── Art/        Üretilen sprite'lar (PNG)
├── Editor/     Build All üretici araçları
├── Prefabs/    Vehicle, Coin, FuelCan
├── Scenes/     MainMenu, Game
├── Scripts/    Oyun kodu (yukarıdaki mimari)
└── Settings/   URP / Renderer ayarları
```

---

## 🛠️ Geliştirme Notları

- Proje **URP 2D** (Universal Render Pipeline) kullanır.
- Görseller ve prefab'lar koddan üretildiği için, sıfırdan kurmak için Unity dışında bir asset'e ihtiyaç yoktur.
- Yeni bir sahne/prefab/sprite değişikliği yaptıktan sonra **Build All**'u tekrar çalıştırmayı unutma.
