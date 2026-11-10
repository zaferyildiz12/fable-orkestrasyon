# Prompt şablonları

Bu şablonlar başlangıç noktasıdır, zorunlu biçim değildir. İşe uymayan satırı çıkar, eksik
olanı ekle, gerekirse iki şablonu karıştır ya da hiç kullanma. Ölçü, alt agent'ın brief'ten
başka bir şey bilmeden işi doğru yapabilmesidir.

## Opus (gevşek)

```
Amaç: [tek cümle]
Neden: [bu iş neye hizmet ediyor, sonucu kim kullanacak]
Bitti sayılır: [gözlemlenebilir sonuç]
Kısıtlar: [dokunulmayacak yerler, uyulacak kurallar]
Bildiklerimiz: [elenen ihtimaller, ilgili dosyalar: yol1, yol2]
Dönüş: [kısa özet + değişen dosyalar + emin olmadığın noktalar]
Yöntemi sen seç.
```

## Sonnet (sıkı)

```
Görev: [tek cümle]

Adımlar:
1. [tam yol] dosyasını oku, [neye bak].
2. [ne yap], [nereye yaz].
3. [doğrulama komutu] çalıştır; çıktı [beklenen] olmalı.

Sınır:
- Yalnız şu dosyalara dokun: [liste]
- [X] yapma, [Y] klasörüne yazma.

Kabul ölçütü: [ölçülebilir koşul]

Engel çıkarsa doğaçlama yapma; dur, neyi denediğini ve nerede kaldığını bildir.

Dönüş biçimi:
- Yapılanlar (madde madde)
- Değişen dosyalar
- Doğrulama çıktısı
- Açık kalanlar
```

## GPT 6.1 Sol (detaylı)

```
# Bağlam
Proje: [ne, hangi dil ve çatı, klasör yapısı]
Bu iş: [büyük işin hangi parçası, neden]
Mevcut durum: [şu an ne çalışıyor, ne eksik]

# Yapılacak değişiklikler
## [dosya yolu 1]
- [fonksiyon adı ve imzası] ekle. Davranış: [girdi → çıktı, hata durumu].
- [mevcut fonksiyon] içinde [şu satır/blok] yerine [şu] olacak.
## [dosya yolu 2]
- ...

# Kurallar
- Adlandırma: [kural]. Hata yönetimi: [kural]. Bağımlılık ekleme.
- Verilen kararları değiştirme; belirsizlik görürsen varsayım yapma, sonuçta bildir.

# Yapma
- Listede olmayan dosyaya dokunma.
- Yeniden düzenleme, biçimlendirme, yorum ekleme yapma.
- Commit atma, paket kurma.

# Doğrulama
1. `[komut]` çalıştır. Beklenen: [çıktı].
2. `[test komutu]` çalıştır. Hepsi geçmeli.

# Çıktı
Son mesajında şunu yaz: değişen dosyalar, her doğrulama komutunun sonucu, yapamadığın
ya da belirsiz kalan her nokta.
```

## GPT 6 Astra (ikinci görüş, kutu dışı)

```
Bir problem üzerinde çalışıyoruz ve bağımsız bir görüş istiyoruz.

Problem: [ne çözülmeye çalışılıyor]
Kısıtlar: [değişmeyecek şeyler]
Malzeme: [dosya yolları / plan metni / hata çıktısı, ham haliyle]

Sorular:
1. Bunu sıfırdan sen çözseydin nasıl yaklaşırdın?
2. Eldeki malzemede hata, eksik ya da gözden kaçmış risk görüyor musun?
3. Daha basit ya da tamamen farklı bir yol var mı?

Kibar olmaya çalışma; katılmadığın yeri gerekçesiyle söyle. Dosyaları değiştirme.
```

Astra'ya kendi çözümünü ya da beklediğin cevabı yazma. Plan eleştirisi istiyorsan planı
"bir öneri" olarak ver, kimin yazdığını ve ne kadar güvendiğini belirtme.

## Haiku / GPT Luna (büyük veri)

```
İşlem: [tek mekanik işlem, ör. "her satırdaki telefon numarasını +90XXXXXXXXXX biçimine çevir"]

Girdi: [dosya yolu], biçim: [CSV, sütunlar: a, b, c], yalnız [N–M] satırları.
Çıktı: [dosya yolu], biçim: [şema]. Girdideki sıra korunur, satır atlanmaz.

Örnekler:
[girdi 1] → [çıktı 1]
[girdi 2] → [çıktı 2]
[sorunlu girdi] → BELIRSIZ

Kural: Emin olmadığın kayıtta tahmin etme, BELIRSIZ yaz.
Dönüş: işlenen kayıt sayısı, BELIRSIZ sayısı, çıktı dosyasının yolu.
```

Parça boyutu: kayıt kısa ise parça başına birkaç yüz kayıt, uzun metinse birkaç on kayıt.
Önce tek parçayla dene, çıktıyı kontrol et, sonra kalanları paralel gönder.
