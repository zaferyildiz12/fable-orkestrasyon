# fable-orkestrasyon

Claude Code için bir skill. Ana oturumun modeli Fable 5.2 ise Fable işi kendisi yapmaz: planlar, işi böler ve alt agent'lara dağıtır, sonra sonucu denetleyip raporlar. Her modele kendi tarzında prompt yazar: Opus'a gevşek, Sonnet'e sıkı, GPT 6.1 Sol'a detaylı, GPT 6 Astra'dan ikinci görüş ister, Haiku ve GPT Luna'ya büyük veri işi verir.

```
                         FABLE 5.2
                      plan + yönetim
                            │
   ┌───────────┬────────────┼────────────┬──────────────┐
  OPUS       SONNET     GPT 6.1 SOL  GPT 6 ASTRA   HAIKU / GPT LUNA
orta-yüksek basit-orta     asker     ikinci görüş   veri temizliği
  gevşek      sıkı        detaylı     kutu dışı      büyük veri
```

## Kim ne yapar

| Model | Rol | Prompt stili |
|---|---|---|
| Opus | Kıdemli uzman | Gevşek |
| Sonnet | Günlük iş gücü | Sıkı |
| GPT 6.1 Sol | Asker | Detaylı |
| GPT 6 Astra | İkinci görüş | Kutu dışı |
| Haiku / GPT Luna | Veri işçisi | Büyük veri |

## Kurulum

```bash
git clone https://github.com/zaferyildiz12/fable-orkestrasyon.git
mkdir -p ~/.claude/skills
cp -R fable-orkestrasyon/skills/fable-orkestrasyon ~/.claude/skills/
```

Yalnız tek projede kullanmak için klasör projenin `.claude/skills/` altına kopyalanır.

## Gereksinimler

- Claude Code (Agent aracı, alt agent `model` parametresi).
- GPT modelleri için OpenAI Codex CLI (`codex exec`) kurulu ve giriş yapılmış olmalı. Codex yoksa skill yalnız Claude modelleriyle çalışır.

## Dosyalar

- `skills/fable-orkestrasyon/SKILL.md`: kurallar.
- `skills/fable-orkestrasyon/references/prompt-sablonlari.md`: model başına brief şablonları.

## Not

Model eşleştirmesi ve prompt stilleri pratik bir çalışma düzenidir, ölçülmüş bir kıyaslama değildir. Model adları değiştikçe tabloyu güncelleyin.

## Lisans

MIT.
