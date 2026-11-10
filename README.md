# fable-orkestrasyon

Claude Code için bir skill. Ana oturumun modeli Fable 5.2 ise Fable bir orkestra şefi gibi çalışır: planlar, işi böler, alt agent'lara dağıtır, sonucu denetleyip raporlar.

Tek katı kural, Fable'ın işin kendisini yapmamasıdır. Hangi modeli, kaç agent'ı, hangi sırayı, brief'in biçimini ve denetimin yolunu şef seçer; bir işte tek model yetebilir, GPT hiç kullanılmayabilir. Aşağıdaki eşleştirmeler (Opus'a gevşek, Sonnet'e sıkı, GPT 6.1 Sol'a detaylı prompt, GPT 6 Astra'dan ikinci görüş, Haiku ve GPT Luna'ya büyük veri) atama değil, varsayılan eğilimlerdir.

```
                         FABLE 5.2
                      plan + yönetim
                            │
   ┌───────────┬────────────┼────────────┬──────────────┐
  OPUS       SONNET     GPT 6.1 SOL  GPT 6 ASTRA   HAIKU / GPT LUNA
orta-yüksek basit-orta     asker     ikinci görüş   veri temizliği
  gevşek      sıkı        detaylı     kutu dışı      büyük veri
```

## Varsayılan eğilimler

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

- `skills/fable-orkestrasyon/SKILL.md`: çalışma düzeni, model eğilimleri ve sınırlar.
- `skills/fable-orkestrasyon/references/prompt-sablonlari.md`: model başına brief şablonları (başlangıç noktası, zorunlu biçim değil).

## Not

Model eşleştirmesi ve prompt stilleri pratik bir çalışma düzenidir, ölçülmüş bir kıyaslama değildir. Model adları değiştikçe tabloyu güncelleyin.

## Lisans

MIT.
