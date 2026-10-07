# Eyleyici Konum Kontrol Simülatörü

Elektromekanik bir eyleyicinin (DC motor + dişli kutusu) PID ile konum kontrolünü tarayıcıda canlı olarak simüle eden, eğitim amaçlı tek sayfalık bir web uygulaması.

Hedef açıyı değiştir, PID kazançlarıyla oyna, sisteme ani bir dış yük uygula; eyleyicinin nasıl tepki verdiğini grafikte, açı göstergesinde ve performans metriklerinde izle.

## Neden bu proje?

Hareket kontrol sistemleri, bir mekanizmanın istenen konuma hızlı, doğru ve kararlı biçimde gitmesini sağlar. Savunma sanayiinde bu sistemler, platformun etkinliğini doğrudan belirleyen kritik alt sistemlerdir:

- **Kule ve namlu stabilizasyonu:** Araç engebeli arazide hareket ederken kule ve namlunun hedefe kilitli kalması, sürekli çalışan konum ve hız kontrol döngüleriyle sağlanır. Gövdeden gelen sarsıntılar, denetleyicinin bastırması gereken bozuculardır.
- **İHA kontrol yüzeyleri:** Kanatçık, irtifa dümeni ve istikamet dümeni gibi yüzeyler küçük elektromekanik eyleyicilerle hareket ettirilir. Uçuş kontrol sisteminin komutlarını doğru ve gecikmesiz uygulamak, uçuşun kararlılığı için şarttır.
- **Robot kolları:** Bomba imha robotları ve uzaktan kumandalı manipülatörler gibi sistemlerde her eklem ayrı bir konum kontrol döngüsüyle sürülür. Hassas ve aşmasız hareket, hem görev başarısı hem güvenlik açısından önemlidir.

Bu uygulamaların hepsinin temelinde aynı problem yatar: bir motoru, yük ve bozucular altında istenen açıya getirmek. Bu proje, o temel problemi ders kitabı seviyesinde bir modelle ele alır ve PID kazançlarının, doygunluğun ve dış yüklerin davranışı nasıl etkilediğini görünür kılar. Gerçek bir ürünü modellemez.

## Özellikler

- **Canlı simülasyon:** 1 ms adımlı fizik modeli, gerçek zamanla aynı hızda akar.
- **PID denetleyici:** Kp, Ki, Kd ayarları; integral windup koruması, filtreli türev, ±24 V doygunluk.
- **Canlı grafik:** Hedef açı, gerçek açı ve kontrol gerilimi (son 5 saniye).
- **Açı göstergesi:** Gerçek açıyı ve hedefi gösteren dairesel kadran.
- **Performans metrikleri:** Aşma, yükselme süresi, oturma süresi ve kalıcı hata; hedefe göre renkli durum noktaları.
- **Dış yük:** 0.5 saniyelik ani bozucu tork; grafikte ve kadranda işaretlenir.
- **Hazır ayarlar:** Yumuşak, Dengeli, Agresif.
- **Gelişmiş ayarlar:** Yük ataleti, viskoz sürtünme ve dişli oranı.

## Çalıştırma

Kurulum gerekmez. `index.html` dosyasını bir tarayıcıda açman yeterli.

Sayfa, Chart.js ile Google Fonts'u internetten yüklediği için ilk açılışta internet bağlantısı gerekir.

## Nasıl çalışır?

### Model

Eyleyici, dişli kutusu üzerinden bir yük döndüren DC motordur. Durum değişkenleri çıkış mili tarafında tanımlıdır: açı θ, açısal hız ω ve motor akımı i.

```
Elektriksel:  L · di/dt = V − R·i − Ke·N·ω
Mekanik:      J_top · dω/dt = N·Kt·i − b·ω − τ_bozucu
              J_top = J_yük + N²·J_motor
```

Dişli kutusu motor torkunu N kat büyütür; motor rotorunun ataleti çıkışa N² kat yansır. Denklemler 1 ms adımla 4. derece Runge-Kutta (RK4) yöntemiyle çözülür. Motorun elektriksel tepkisi çok hızlı olduğu için (L/R = 1 ms) basit Euler yöntemi bu adım boyunda kararsızlaşabilir.

| Parametre | Değer |
|---|---|
| Sargı direnci R | 4 Ω |
| Sargı endüktansı L | 4 mH |
| Tork sabiti Kt / zıt EMK sabiti Ke | 0.05 N·m/A |
| Motor ataleti J_motor | 1·10⁻⁵ kg·m² |
| Besleme sınırı | ±24 V |

### Denetleyici

PID çıkışı motora uygulanan gerilimdir:

```
V = Kp·e + Ki·∫e dt + Kd·(türev)      e = hedef − gerçek açı (rad)
```

- **Türev ölçümden alınır.** Hata yerine açının türevi (−dθ/dt) kullanılır; böylece hedef aniden değiştiğinde gerilimde ani sıçrama ("türev tekmesi") olmaz.
- **Türev filtrelenir.** Birinci dereceden alçak geçiren filtre (zaman sabiti 10 ms) uygulanır.
- **Doygunluk:** Çıkış ±24 V ile sınırlanır.
- **Windup koruması:** Çıkış doygundayken ve hata onu daha da doyuracak yöndeyse integral biriktirilmez.

### Metrikler

Hedef açı her değiştiğinde yeni bir basamak yanıtı ölçülür.

| Metrik | Tanım | Hedef |
|---|---|---|
| Aşma | Yanıtın hedefi en fazla yüzde kaç geçtiği | < %10 |
| Yükselme süresi | Yanıtın basamağın %10'undan %90'ına çıkma süresi | — |
| Oturma süresi | Açının hedefin ±%2 bandına girip bir daha çıkmadığı an | < 1 s |
| Kalıcı hata | Sistem durduğunda hedef ile gerçek açı arasındaki fark | < 0.5° |

### Hazır ayarlar

Varsayılan yük için (J = 0.20 kg·m², b = 0.05 N·m·s/rad, N = 50), 90°'lik basamakta:

| Ayar | Kp / Ki / Kd | Aşma | Oturma | Karakter |
|---|---|---|---|---|
| Yumuşak | 10 / 0 / 2 | %0 | ~1.5 s | Yavaş, aşmasız |
| Dengeli | 40 / 2 / 3 | ~%2 | ~0.6 s | Üç hedefi de karşılar |
| Agresif | 90 / 30 / 0.3 | ~%20 | ~1.0 s | Hızlı ama salınımlı |

## Sınırlılıklar

Model bilinçli olarak basit tutulmuştur. Gerçek sistemlerde önemli olan şu etkiler modellenmemiştir:

- Coulomb (kuru) sürtünme ve dişli boşluğu (backlash)
- Sensör gürültüsü ve enkoder çözünürlüğü
- Motor sürücüsünün akım sınırı ve PWM etkileri
- Mekanik esneklik ve rezonanslar

## Teknik

- Tek dosya: `index.html` (HTML, CSS ve JavaScript bir arada). Framework ve derleme aracı yok.
- [Chart.js 4.4.1](https://www.chartjs.org/) (cdnjs)
- Yazı tipleri: Inter ve JetBrains Mono (Google Fonts)

## Geliştirici

Alper Durak · [GitHub](https://github.com/alperdurak) · [LinkedIn](https://www.linkedin.com/in/alperdurak/)
