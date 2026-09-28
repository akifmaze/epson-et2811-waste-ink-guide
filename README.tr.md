# Epson ET-2810 / ET-2811 atık mürekkep sıfırlama notları

[English](README.md) · [Komut rehberi](docs/commands.md) · [Vaka ve kanıtlar](docs/case-study.md) · [Güvenlik](SAFETY.md) · [Kaynaklar](ATTRIBUTION.md)

Bu repo, bir ET-2811 kullanıcısının mevcut topluluk araçlarıyla tamamladığı başarılı onarımı belgeler. Katkımız; sorunu teşhis etmek, sayaç sıfırlamadan sonra gereken ek adımı açıklamak ve deneyimi başkalarının inceleyebileceği şekilde kaydetmektir. USB iletişimi, EEPROM erişimi ve model tanımları kaynak gösterdiğimiz projelerin emeğidir.

> **Önce atık mürekkep pedlerinin fiziksel bakımını yaptırın.** Sayaç sıfırlamak içerideki mürekkebi temizlemez. Yanlış EEPROM yazımı yazıcının ayarlarını bozabilir. Yazma komutundan önce [güvenlik notlarını](SAFETY.md) okuyun.

## Bu vakada ne oldu?

Onarım konuşmasının özetine göre pedler değiştirilmişti. `ez-reset` denemesinden sonra üç atık mürekkep sayacı sıfır görünmesine rağmen `epson_print_conf`, `Ink overflow error` ve `maintenance_box_1: full (2)` durumunu bildiriyordu.

Konuşmada EEPROM `0x100` adresinin önce `0x01` olduğu, en düşük bit temizlenerek `0x00` yapıldığı anlatılıyor. Kullanıcının paylaştığı gerçek terminal çıktısı son değeri doğruluyor:

```text
EEPROM_ADDR 0x100 = 256: 0x00 = 0
```

Kapatıp açma önerisinin ardından kullanıcı yazıcının çalıştığını bildirdi. Önceki değerlerin ham terminal kaydı, firmware sürümü ve uzun dönem testi elimizde yok. [Vaka kaydı](docs/case-study.md) bu ayrımı açıkça gösterir.

## Kritik ayrıntı

İncelenen ET-2810/2811 model tanımı, normal sayaç yazımlarından sonra şu işlemi tarif ediyor:

```text
yeni_değer = eski_değer & 0xFE
```

Bu işlem yalnızca bit 0'ı temizler. `0x01` için sonuç `0x00` olur; başka bir değere doğrudan sıfır yazmak diğer bitleri de silebilir. İncelenen `ez-reset` sürümünde bu son adımı tanımlayan iç içe XML bölümü işlenmiyor. İncelenen `epson_print_conf` ham reset listesinde de `0x100` yok. Bu gözlemler sürüme bağlıdır; güncel sürümlerde değişmiş olabilir.

## Kapsam

- **ET-2811:** Kullanıcının başarı bildirdiği tek cihaz.
- **ET-2810:** Aynı aileye ait tanım var; ayrı bir fiziksel test yok.
- **Diğer modeller:** Bu repo tarafından doğrulanmadı.
- **Bağlantı:** Windows PowerShell ve USB; kayıttaki komutta `-m ET-2810 --usb` kullanılıyor.

## Kullanım

[Komut rehberi](docs/commands.md), kurulumu, salt okunur sorguları, özel yedeği ve yalnızca bu vakadaki ön koşullara uygun yazma örneğini içerir. Bu repo otomatik reset programı değildir. Rehber hazırlanırken yazıcıya yeni bir yazma işlemi yapılmadı.

Ham logları veya EEPROM dökümlerini yayımlamayın. [Gizlilik rehberine](docs/privacy.md) göre kullanıcı adı, seri numarası, USB aygıt yolu ve ağ bilgilerini çıkarın. Repodaki paylaşılabilir örnek yalnızca doğrulanmış okuma satırını içerir.

## Emek ve lisans

[Ircama/epson_print_conf](https://github.com/Ircama/epson_print_conf) ve [CiRIP/ez-reset](https://github.com/CiRIP/ez-reset) olmadan bu çalışma mümkün olmazdı. Repo, bu araçların veya reset tanımının ilk geliştiricisi olduğumuz iddiasında bulunmaz.

Özgün dokümantasyon ve örnekler için [MIT lisansı](LICENSE) kullanılmıştır. Başka projelerin kodları ve veri tabanları bu repoya kopyalanmamıştır; kendi lisansları geçerlidir. Kaynak sürümleri ve ayrıntılar [ATTRIBUTION.md](ATTRIBUTION.md) dosyasındadır.
