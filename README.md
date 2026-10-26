# Skor Simülatörü (Android)

Günün futbol maçlarını seçip puan durumu istatistikleriyle analiz eden, Monte Carlo simülasyonu ile skor tahmini yapan Android uygulaması.

## APK'yı almak (bilgisayara bir şey kurmadan)

1. **GitHub hesabı aç** (yoksa): github.com
2. **Yeni depo oluştur:** sağ üstte **+ → New repository**, ad olarak `skor-simulatoru` yaz, **Create repository**.
3. **Dosyaları yükle:** depo sayfasında **uploading an existing file** bağlantısına tıkla. Bu klasörün *içindeki* her şeyi (`app`, `gradle`, `.github`, `gradlew` vb.) sürükleyip bırak, **Commit changes**.
   - `.github` klasörü gizli olabilir. Görünmüyorsa: Windows'ta Gezgin → Görünüm → Gizli öğeler, Mac'te Finder'da `Cmd + Shift + .`
4. **Derlemeyi bekle:** depoda **Actions** sekmesinde "APK derle" çalışır (yaklaşık 3–5 dakika). Yeşil tik çıkınca hazırdır.
5. **Telefona indir:** telefondan `github.com/<kullanıcı-adın>/skor-simulatoru/releases/latest` adresini aç, **SkorSimulatoru.apk** dosyasını indir.
6. **Kur:** dosyayı aç. Android "bilinmeyen uygulamalar" uyarısı verirse, tarayıcına bu izni ver ve kurulumu tamamla.

Kodda bir değişiklik yapıp tekrar yüklediğinde yeni APK otomatik derlenir ve aynı sayfaya konur.

## İlk açılış: API anahtarı

Uygulama maç ve puan durumu verilerini **football-data.org** ücretsiz servisinden alır.

1. https://www.football-data.org/client/register adresinden kayıt ol; anahtar e-postana gelir.
2. Uygulamada **Ayarlar** sekmesine anahtarı yapıştır → **Kaydet ve maçları getir**.

Ücretsiz paket: Premier League, La Liga, Serie A, Bundesliga, Ligue 1, Eredivisie, Primeira Liga, Championship, Şampiyonlar Ligi, Brezilya Serie A. Dakikada 10 istek sınırı vardır; uygulama verileri önbelleğe alır ve sınıra takılırsa kendisi bekler.

**Süper Lig** anahtar gerektirmez; veriler TheSportsDB'nin ücretsiz servisinden gelir. Bu servis tam puan durumu vermediği için uygulama puan durumunu haftalık sonuçlardan kendisi hesaplar. İlk açılışta haftalar tek tek indirildiği için yükleme 30–60 saniye sürebilir; bitmiş haftalar telefonda saklanır, sonraki açılışlar hızlıdır. Süper Lig'de her maçın tahmini yalnızca o maçtan önce oynanmış maçlarla yapılır, bu yüzden geçmiş günlerdeki isabet oranı gerçekçidir.

## Kullanım

- **Maçlar:** üstteki oklarla günü değiştir, simüle etmek istediğin maçları işaretle, alttaki düğmeye bas.
- **Tahminler:** özet tablo + her maç için tahmini skor, 1/X/2, 2,5 üst, karşılıklı gol, en sık skorlar ve olasılık ızgarası. Bitmiş maçlarda tahminin tutup tutmadığı gösterilir. Geçmiş günleri seçerek modeli deneyebilirsin; ancak model güncel puan durumunu kullandığı için (o maçın sonucu da içinde) geçmiş günlerde isabet olduğundan biraz yüksek görünür.
- **Ayarlar:** simülasyon sayısı, form etkisi, erken sezon temkini, ev avantajı, tempo, Dixon-Coles ρ.

## Model

1. Puan durumundaki attığı/yediği gollerden hücum ve savunma güçleri (lig ortalamasına göre, az maç oynanmışsa ortalamaya doğru çekilmiş).
2. Beklenen gol: ev = ev hücumu × deplasman savunması × ligin ev gol ortalaması; deplasman için tersi. Son 5 maç formu ile düzeltme.
3. Dixon-Coles düzeltmeli Poisson dağılımından N maç simülasyonu; tahmin = en olası sonuca uyan en sık skor.

İstatistik modelidir; kesin sonuç vermez, bahis tavsiyesi değildir.

## Android Studio ile derlemek (isteğe bağlı)

Klasörü Android Studio'da **Open** ile aç, **Build → Build APK(s)**.

## Yapı

```
app/src/main/assets/index.html     arayüz + model (tamamı burada)
app/src/main/java/.../MainActivity.java   WebView kabuğu + API köprüsü
.github/workflows/build-apk.yml    bulutta APK derleme
```
