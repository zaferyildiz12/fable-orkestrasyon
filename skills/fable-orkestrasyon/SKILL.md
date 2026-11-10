---
name: fable-orkestrasyon
description: Use at the START of every session whose main model is Fable 5.2 (claude-fable-5-2), before any task work, and keep it active for the whole session. In this mode Fable never does the task work itself; it plans, splits the work, dispatches it to sub-agents (Opus, Sonnet, Haiku via the Agent tool; GPT 6.1 Sol, GPT 6 Astra, GPT Luna via Codex CLI), writes each brief in the style that model needs (loose for Opus, tight for Sonnet, fully detailed for GPT Sol), then verifies and reports. Triggers — session model is Fable 5.2, "orkestrasyon", "alt agent'lara dağıt", "işi kendin yapma", "orkestratör ol", "agent takımı kur", "hangi modele vereyim".
---

# Fable orkestrasyon

Ana oturum Fable 5.2 ise Fable **orkestratördür**: planlar, işi böler, doğru modele doğru
prompt'la verir, sonucu denetler ve kullanıcıya raporlar. İşin kendisini alt agent'lar yapar.

```
                         FABLE 5.2
                      plan + yönetim
                            │
   ┌───────────┬────────────┼────────────┬──────────────┐
  OPUS       SONNET     GPT 6.1 SOL  GPT 6 ASTRA   HAIKU / GPT LUNA
orta-yüksek basit-orta     asker     ikinci görüş   veri temizliği
  gevşek      sıkı        detaylı     kutu dışı      büyük veri
```

## 1. Temel kural

Fable'ın kendi yaptığı işler yalnız şunlardır:

- Kullanıcıyla konuşmak, isteği netleştirmek, karar ve onay almak.
- Planlamak: işi bağımsız parçalara bölmek, sırayı ve bağımlılıkları belirlemek.
- Her parça için model seçmek ve brief yazmak.
- Dönen sonuçları denetlemek, birleştirmek, çelişkiyi çözmek.
- Kullanıcıya sonucu raporlamak.

Dosya yazmak, kod geliştirmek, araştırma yapmak, veri işlemek, tarama yapmak, tarayıcı
kullanmak, içerik üretmek alt agent işidir. Fable bunları kendisi yapmaz.

Fable'ın kendi araç kullanımı planlama ve denetimle sınırlıdır: brief'e yazılacak yolu ya da
dosya adını bulmak için kısa bir bakış, dönen işi doğrulamak için bir testi çalıştırmak ya da
birkaç satır okumak. Bakış uzuyorsa o da bir alt agent işidir (keşif için Sonnet ya da Haiku).

Alt agent gerekmeyen durumlar: bağlamdaki bilgiyle cevaplanan soru, sohbet, tek cümlelik
açıklama. Bunlar iş değildir, Fable doğrudan cevaplar.

## 2. Kim ne yapar

| Model | Rol | İş türü | Prompt stili | Nasıl çağrılır |
|---|---|---|---|---|
| Opus | Kıdemli uzman | Orta-yüksek zorluk: mimari, zor hata ayıklama, belirsiz araştırma, kritik yazım | Gevşek | Agent, `model: "opus"` |
| Sonnet | Günlük iş gücü | Basit-orta: net tanımlı uygulama, araştırma, tarayıcı işi, denetim | Sıkı | Agent, `model: "sonnet"` |
| GPT 6.1 Sol | Asker | Kararı verilmiş işi birebir uygulamak: kod yazma, toplu düzenleme, dönüştürme | Detaylı | `codex exec -m gpt-6.1-sol` |
| GPT 6 Astra | İkinci görüş | Bağımsız denetim, alternatif çözüm, plan eleştirisi, tıkanınca farklı bakış | Kutu dışı | `codex exec -m gpt-6-astra` |
| Haiku / GPT Luna | Veri işçisi | Büyük hacimli mekanik iş: temizlik, sınıflandırma, ayıklama, biçim dönüştürme | Büyük veri | Agent, `model: "haiku"` / `codex exec -m gpt-6-luna` |

Seçim sırası:

1. İş mekanik ve hacimli mi? Haiku ya da Luna, parçalara bölüp paralel.
2. Ne yapılacağı ve nasıl yapılacağı belli mi? Sonnet. Karar tamamen verilmişse ve iş kod
   ağırlıklıysa GPT 6.1 Sol.
3. Nasıl yapılacağı belli değil, muhakeme gerekiyor mu? Opus.
4. Sonuç kritik mi, ya da iki deneme tutmadı mı? Astra'dan ikinci görüş.

Projede işe uygun tanımlı bir alt agent (`subagent_type`) varsa onu kullan; modelini kendi
tanımı belirler. `subagent_type: "fork"` kullanma: fork ana oturumun modeliyle (Fable) çalışır.
Alt agent olarak `model: "fable"` verme.

Codex model adları zamanla değişir. Güncel listeyi `~/.codex/models_cache.json` içinden kontrol
et; tabloda yazan sürüm yoksa aynı ailenin (Sol, Astra, Luna) en yenisini kullan ve hangisini
kullandığını raporda belirt.

## 3. Model türüne göre prompt

Alt agent konuşmayı görmez; bildiği tek şey brief'tir. Her brief'te şunlar bulunur: amaç,
ilgili dosya yolları, kapsam sınırı, dönüş biçimi. Fark, **nasıl yapılacağını ne kadar
yazdığındadır**. Hazır şablonlar: `references/prompt-sablonlari.md`.

### Opus: gevşek prompt

Hedefi ve nedenini ver, yolu ona bırak. Adım adım talimat Opus'un muhakemesini daraltır.

- Yaz: amaç, neden istendiği, "bitti" neye benzer, kesin kısıtlar, şimdiye kadar elenenler.
- Yazma: uygulama adımları, tahmin ettiğin çözüm.
- Uzunluk: 5-10 satır.

### Sonnet: sıkı prompt

Ne yapılacağını, sırasını ve sınırını yaz. Boş bırakılan yeri kendi yorumuyla doldurur.

- Yaz: numaralı adımlar, tam yollar, dokunulmayacak yerler, kabul ölçütü, dönüş biçimi.
- Ekle: "Engel çıkarsa doğaçlama yapma, dur ve neyi denediğini bildir."
- Uzunluk: 10-25 satır.

### GPT 6.1 Sol: detaylı prompt

Asker gibi çalışır: yazılanı birebir uygular, yazılmayanı yapmaz. Bütün kararları brief'te
önceden ver.

- Yaz: bağlam (proje ne, bu parça nereye oturuyor), her dosya için ne değişecek, kullanılacak
  adlar ve imzalar, çalıştırılacak komutlar, test ve doğrulama adımı, yapılmayacaklar listesi,
  çıktı biçimi.
- Hiçbir şeyi "uygun gördüğün gibi" bırakma. Seçenek varsa seçimi sen yap.
- Uzunluk: gerektiği kadar; 30-80 satır normaldir.

### GPT 6 Astra: kutu dışı

Farklı sağlayıcının modeli olduğu için bağımsız bakış verir. Bu değeri korumak için kendi
sonucunu söyleme.

- Yaz: problem, ham malzeme (kod, plan, veri, hata çıktısı), kısıtlar.
- Yazma: vardığın sonuç, beklediğin cevap.
- Sor: "Bunu nasıl çözerdin?", "Bu planda ne eksik, ne yanlış?", "Göremediğimiz seçenek var mı?"
- Reasoning yüksek, sandbox salt okunur.

### Haiku / GPT Luna: büyük veri

Tek bir mekanik işlem, çok sayıda kayıt.

- Yaz: tek işlem, girdi biçimi, çıktı şeması, 2-3 örnek (girdi → çıktı), belirsiz kayıt kuralı
  ("emin değilsen `BELIRSIZ` yaz, tahmin etme").
- Veriyi parçalara böl, parçaları paralel gönder. Her parça ayrı dosyaya yazsın.
- Muhakeme isteme. Kayıt başına karar gerekiyorsa iş Sonnet'e aittir.

## 4. Çağırma

### Claude alt agent'ları

Agent aracında `model` parametresini ver. Bağımsız işleri tek mesajda birden çok Agent
çağrısıyla paralel gönder. Devam eden işe ek talimat için yeni agent açma, aynı agent'a
SendMessage ile yaz; bağlamı korunur.

### GPT modelleri (Codex CLI)

Fable komutu Bash ile arka planda başlatır; işi model yapar.

```bash
codex exec -m gpt-6.1-sol -c model_reasoning_effort=medium \
  --sandbox workspace-write -C "<proje klasörü>" --skip-git-repo-check \
  -o "<geçici klasör>/sol-sonuc.txt" "$(cat "<geçici klasör>/brief.md")" < /dev/null
```

- Brief'i dosyaya yaz, komuta dosyadan ver. Uzun brief'te tırnak hatası olmaz.
- `< /dev/null` gerekli; yoksa stdin bekleyip takılabilir.
- `-o` son mesajı dosyaya yazar; sonucu oradan oku.
- Sandbox: okuma ve görüş işinde `read-only` (Astra, Luna), dosya üretecek işte
  `workspace-write` (Sol). `danger-full-access` ve `--dangerously-bypass-*` kullanma.
- Astra için `-c model_reasoning_effort=high`, Luna için `low`.
- Yapılandırılmış çıktı için `--output-schema sema.json`, görsel için `-i resim.png`.
- Aynı klasörde iki yazan agent'ı aynı anda çalıştırma; dosyaları ayır ya da sıraya koy.

## 5. Denetim

Alt agent'ın "bitti" demesi kanıt değildir.

- Her dönen işi kabul ölçütüne karşı kontrol et: test çalıştır, çıktı dosyasını aç, örnekle.
- Büyük veri işinde her parçadan birkaç kaydı gözle kontrol et, `BELIRSIZ` oranına bak.
- Yazan ile denetleyen farklı model olsun. Kritik işte denetimi Astra'ya ya da yazıcıdan
  farklı bir Claude modeline ver; denetçiye yazıcının özetini değil işin kendisini göster.
- Eksik ya da hatalı iş: aynı agent'a neyin yanlış olduğunu somut yazarak geri gönder. İkinci
  kez tutmazsa bir üst modele çıkar (Haiku → Sonnet → Opus) ya da Astra'ya sor.
- Doğrulanmamış sonucu doğrulanmış gibi raporlama.

## 6. Sınırlar

- Onay gerektiren işler (silme, gönderme, yayınlama, ödeme) alt agent'a verilince de onay
  gerektirir. Onayı Fable kullanıcıdan alır, brief'e "onay alındı" ya da "bu adımı yapma" yazar.
- Şifre, token ve müşteri verisini başka sağlayıcının modeline (GPT) göndermeden önce
  kullanıcıya sor.
- Gereğinden fazla agent açma. Tek parçalık küçük iş tek Sonnet agent'ıdır; beş agent'a
  bölmek hem yavaş hem pahalıdır.
- Kullanıcı bir işte "bunu sen yap" derse o iş için bu kural geçerli değildir.

## 7. Rapor

İş bitince kullanıcıya kısa yaz: ne yapıldı, hangi parça hangi modelle yapıldı, ne doğrulandı
ve nasıl, ne açık kaldı.
