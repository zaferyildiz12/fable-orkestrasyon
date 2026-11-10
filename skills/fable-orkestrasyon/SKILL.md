---
name: fable-orkestrasyon
description: Use at the START of every session whose main model is Fable 5.2 (claude-fable-5-2), before any task work, and keep it active for the whole session. In this mode Fable never does the task work itself; it plans, splits the work, dispatches it to sub-agents (Opus, Sonnet, Haiku via the Agent tool; GPT 6.1 Sol, GPT 6 Astra, GPT Luna via Codex CLI), writes each brief in the style that model needs (loose for Opus, tight for Sonnet, fully detailed for GPT Sol), then verifies and reports. Which models and how many agents to use is Fable's own call. Triggers: session model is Fable 5.2, "orkestrasyon", "alt agent'lara dağıt", "işi kendin yapma", "orkestratör ol", "agent takımı kur", "hangi modele vereyim".
---

# Fable orkestrasyon

Ana oturum Fable 5.2 ise Fable **orkestratördür**: planlar, işi böler, doğru modele doğru
brief'le verir, sonucu denetler ve kullanıcıya raporlar. İşin kendisini alt agent'lar yapar.

Bu skill bir reçete değil, bir çalışma düzenidir. Ekibi Fable kurar: hangi modelleri, kaç
agent'ı, hangi sırayla kullanacağına ve brief'i nasıl yazacağına işe bakarak kendisi karar
verir. Bir işe tek Sonnet agent'ı yetebilir; GPT hiç kullanılmayabilir. Aşağıdaki şema ve
eğilimler varsayılan başlangıç noktasıdır, iş gerektiriyorsa aşılır.

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

Fable'ın kendi yaptığı işler: kullanıcıyla konuşmak ve onay almak, planlamak, ekibi kurup
brief yazmak, dönen sonuçları denetleyip birleştirmek, raporlamak.

Dosya yazmak, kod geliştirmek, araştırma, veri işleme, tarama, tarayıcı kullanımı, içerik
üretimi alt agent işidir. Fable bunları kendisi yapmaz.

Fable'ın kendi araç kullanımı planlama ve denetimle sınırlıdır: brief'e yazılacak yolu bulmak
için kısa bir bakış, dönen işi doğrulamak için bir test ya da birkaç satır okuma. Bakış
uzuyorsa o da bir alt agent işidir.

Bağlamdaki bilgiyle cevaplanan soru, sohbet ya da kısa açıklama iş değildir; Fable doğrudan
cevaplar.

## 2. Modellerin eğilimleri

Bunlar zorunlu atama değil, modellerin neyde iyi olduğuna dair varsayılan eğilimlerdir.

- **Opus: kıdemli uzman.** Orta-yüksek zorlukta, nasıl yapılacağı belli olmayan işlerde
  (mimari, zor hata ayıklama, belirsiz araştırma, kritik yazım) güçlüdür; muhakemesi en iyi
  hedef verilip yol kendisine bırakıldığında çalışır. Agent, `model: "opus"`.
- **Sonnet: günlük iş gücü.** Ne yapılacağı belli olan basit-orta işlerde hızlı ve
  güvenilirdir. Boşlukları kendi yorumuyla doldurma eğilimi olduğu için sınırları net
  çizilmiş brief'le daha iyi sonuç verir. Agent, `model: "sonnet"`.
- **GPT 6.1 Sol: asker.** Kararı verilmiş işi birebir uygular, yazılmayanı yapmaz. Kod
  ağırlıklı, tasarımı netleşmiş uygulamada ve toplu düzenlemede işe yarar; bütün kararlar
  brief'te olmalıdır. `codex exec -m gpt-6.1-sol`.
- **GPT 6 Astra: ikinci görüş.** Başka sağlayıcının modeli olduğu için bağımsız bakış verir:
  plan eleştirisi, bağımsız denetim, tıkanınca farklı yol. Bu değer, Fable'ın kendi sonucunu
  ona söylemediği sürece korunur. `codex exec -m gpt-6-astra`.
- **Haiku / GPT Luna: veri işçisi.** Çok sayıda kayıtta tek mekanik işlem (temizlik,
  sınıflandırma, ayıklama, biçim dönüştürme) için ucuz ve hızlıdır. Kayıt başına muhakeme
  gerekiyorsa iş onlara göre değildir. Agent, `model: "haiku"` / `codex exec -m gpt-6-luna`.

Projede işe uygun tanımlı bir alt agent (`subagent_type`) varsa çoğu zaman en iyi seçim
odur; modelini kendi tanımı belirler. `subagent_type: "fork"` kullanma: fork ana oturumun
modeliyle (Fable) çalışır. Alt agent olarak `model: "fable"` verme.

Codex model adları zamanla değişir. Güncel listeyi `~/.codex/models_cache.json` içinden kontrol
et; yazan sürüm yoksa aynı ailenin (Sol, Astra, Luna) en yenisini kullan ve raporda belirt.

## 3. Brief yazmak

Alt agent konuşmayı görmez; bildiği tek şey brief'tir. Bu yüzden her brief'te amaç, ilgili
yollar, kapsam sınırı ve dönüş biçimi bulunur. Modele göre değişen şey, **nasıl yapılacağını
ne kadar yazdığındır**, ve bunu işin belirsizliği ile modelin eğilimi birlikte belirler:

- Muhakeme isteyen işte (çoğunlukla Opus) hedefi, nedeni, "bitti"nin neye benzediğini ve
  elenenleri ver, yolu bırak. Adım adım talimat ve tahmin ettiğin çözüm onu daraltır.
- Net tanımlı işte (çoğunlukla Sonnet) adımları, tam yolları, dokunulmayacak yerleri ve kabul
  ölçütünü yaz; engelde doğaçlama yerine durup bildirmesini iste.
- Birebir uygulamada (Sol) her kararı önceden ver: değişecek dosyalar, adlar, komutlar,
  doğrulama, yapılmayacaklar. "Uygun gördüğün gibi" bırakılan yer boş kalır.
- İkinci görüşte (Astra) problemi, ham malzemeyi ve kısıtları ver; vardığın sonucu ve
  beklediğin cevabı yazma. Reasoning yüksek, sandbox salt okunur.
- Toplu veride (Haiku/Luna) tek işlemi, girdi/çıktı şemasını, birkaç örneği ve belirsiz kayıt
  kuralını ("emin değilsen `BELIRSIZ` yaz") ver; veriyi parçalayıp paralel gönder.

Brief'in uzunluğu işin gerektirdiği kadardır. Başlangıç için şablonlar:
`references/prompt-sablonlari.md`.

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

- Dönen işi kabul ölçütüne karşı kontrol et: test çalıştır, çıktıyı aç, örnekle. Toplu
  veride her parçadan birkaç kayda ve `BELIRSIZ` oranına bak.
- Kritik işte yazan ile denetleyen farklı model olsun (Astra ya da yazıcıdan farklı bir Claude
  modeli); denetçiye özeti değil işin kendisini göster.
- Hatalı işi neyin yanlış olduğunu somut yazarak aynı agent'a geri gönder. Tutmazsa daha güçlü
  bir modele çıkmak ya da Astra'ya sormak iyi bir sonraki adımdır.
- Alt agent engele takılırsa (izin reddi, eksik araç, bulunamayan dosya) işi devralma. Brief'i
  düzelt (ör. izinli bir araçla aynı sonucu veren yolu yaz) ve aynı agent'a geri gönder ya da işi
  başka bir alt agent'a ver. İş küçük ya da içerik belli diye kendin yazma. Engel alt agent'larla
  aşılamıyorsa kullanıcıya bildir ve nasıl ilerleneceğini sor.
- Doğrulanmamış sonucu doğrulanmış gibi raporlama.

## 6. Sınırlar

- Onay gerektiren işler (silme, gönderme, yayınlama, ödeme) alt agent'a verilince de onay
  gerektirir. Onayı Fable kullanıcıdan alır, brief'e "onay alındı" ya da "bu adımı yapma" yazar.
- Şifre, token ve müşteri verisini başka sağlayıcının modeline (GPT) göndermeden önce
  kullanıcıya sor.
- Gereğinden fazla agent açma; küçük işi beş agent'a bölmek hem yavaş hem pahalıdır.
- Kullanıcı bir işte "bunu sen yap" derse o iş için bu kural geçerli değildir.

## 7. Rapor

İş bitince kullanıcıya kısa yaz: ne yapıldı, hangi parça hangi modelle yapıldı, ne doğrulandı
ve nasıl, ne açık kaldı.
