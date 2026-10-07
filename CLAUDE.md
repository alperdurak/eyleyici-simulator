# Proje: Eyleyici Konum Kontrol Simülatörü

## Amaç
Elektromekanik bir eyleyicinin (DC motor + dişli kutusu) PID ile konum
kontrolünü tarayıcıda simüle eden, eğitim amaçlı tek sayfalık web uygulaması.
Staj başvurusu için portföy projesi. Gerçek bir ürünü modellemez;
ders kitabı seviyesinde genel bir modeldir.

## Teknik kurallar
- Tek dosya: index.html (CSS ve JS içinde). Framework ve build aracı yok.
- Harici bağımlılık sadece: Chart.js (cdnjs, sabit sürüm) ve Google Fonts
  "Inter" + "JetBrains Mono".
- Arayüz metinleri ve kod yorumları Türkçe, değişken isimleri İngilizce.
- Ben yeni başlayanım: her önemli bölümün başına kısa Türkçe açıklama yorumu yaz.
- Her aşamadan sonra dur ve onayımı bekle.

## Fizik ve kontrol
- Model: DC motor (R, L, Kt, Ke) + dişli oranı N + yük ataleti J + viskoz sürtünme b.
- Sayısal integrasyon dt = 1 ms; ekran ~60 fps güncellenir (kare başına alt adımlar).
- PID: Kp, Ki, Kd; integral windup koruması; türev için basit alçak geçiren filtre.
- Kontrol çıkışı ±24 V ile sınırlı (doygunluk).
- Hedef açı aralığı: -180° ile 180°.
- "Dış yük uygula" butonu: 0.5 saniye süren ani bozucu tork.
- Metrikler: aşma (%), yükselme süresi (%10-%90), oturma süresi (±%2), kalıcı hata (°).
- Hazır ayarlar: Yumuşak, Dengeli, Agresif.

## Tasarım sistemi
- His: koyu tema, mühendislik kontrol istasyonu. Sade ve profesyonel.
  Gradyan yok, emoji yok, abartılı gölge yok.
- Renkler: arka plan #0B1220, kart #111A2E, kenarlık #1F2A44, ana metin #E5E7EB,
  ikincil metin #94A3B8. Gerçek açı ve ana vurgu camgöbeği #38BDF8,
  kontrol sinyali amber #F59E0B, hedef açı gri #64748B, iyi #10B981, kötü #EF4444.
- Arka plan: çok silik mühendislik kağıdı ızgarası (CSS ile, 24px aralık).
- Yazı: metinler Inter, sayılar (slider değerleri, metrikler, grafik eksenleri,
  açı göstergesi) JetBrains Mono. Başlıklar 600 ağırlık.
  Tüm sayılarda font-variant-numeric: tabular-nums.
- Boşluklar 8px ızgarası (8/16/24/32). Kart köşeleri 12px, 1px ince kenarlık.
- Masaüstü düzeni: üstte ince başlık şeridi (proje adı, tek satır alt başlık,
  sağda durum göstergesi ve GitHub linki). Altında solda 320px kontrol paneli,
  sağda grafik ve yanında açı göstergesi. Grafiğin altında yan yana 4 metrik kartı.
  Sol panelle sağ sütunun alt kenarları hizalı. En altta "Nasıl çalışır?" bölümü.
- Mobil (<768px): tek sütun, grafik üstte, kontrol paneli altta.
- Durum göstergesi: "● Hazır" (gri); simülasyon çalışırken "● Çalışıyor"
  (yeşil, hafifçe yanıp sönen).
- Grafik: hedef açı gri kesikli çizgi, gerçek açı camgöbeği, kontrol sinyali amber
  (sağ eksen, Volt). Izgara çizgileri çok silik. Chart.js animasyonu kapalı.
- Açı göstergesi (SVG): dairesel kadran, derece işaretleri, hedef açı için ince
  gri işaret, gerçek açı için camgöbeği kol, ortada anlık açı büyük puntoyla.
- Slider'ların yanında anlık değer; üzerine gelince tek cümlelik açıklama.
- Metrik kartları: değer büyük ve belirgin, birim küçük. Hedefe göre küçük renkli nokta:
  aşma < %10, oturma < 1 s, kalıcı hata < 0.5° ise yeşil, değilse kırmızı.
- Başlıktaki GitHub linki proje deposuna gider:
  https://github.com/alperdurak/eyleyici-simulator
- Footer: "Eğitim amaçlı genel model · Alper Durak · github.com/alperdurak · LinkedIn"
  (LinkedIn: https://www.linkedin.com/in/alperdurak/)