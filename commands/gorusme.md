---
description: Satış görüşmesi ses kaydını transkripte eder, satış uzmanı gözüyle analiz eder, itiraz yanıtları + müşteri profili + içerik brief'i üretir
argument-hint: "<ses dosyası veya klasör yolu — boş bırakırsan sorarım>"
---

# /gorusme — Satış Görüşmesi Analizi

Satış ekibinin müşteri telefon görüşmelerini **ses → transkript → satış analizi → brief** hattından geçirir.

Girdi: **$ARGUMENTS**

## Ürün bağlamı (ÖNCE BUNU DOLDUR)

> Bu bölümü kendi ürününe göre düzenle. Analizin isabetli olması buna bağlı.
> Dosya: `~/.claude/commands/gorusme.md` — aşağıdaki üç satırı değiştir.

**Ne satıyoruz:** <ürün/hizmetini yaz>
**Alıcı tipleri:** <ör. bireysel müşteri · kurumsal satın alma · İK sorumlusu>
**Tipik itiraz eksenleri:** <ör. fiyat/taksit · süre · kalite · rakip karşılaştırması · kurumsal süreç>

Doldurulmamışsa: transkriptten ürünü ve alıcı tipini kendin çıkar, analizin başında
"Bağlam dosyadan okunamadı, görüşmeden çıkardım" diye not düş.
---

## ADIM 0 — Girdiyi netleştir

`$ARGUMENTS` boşsa sor: "Hangi ses kaydını analiz edelim? (dosya yolunu ver ya da klasörü söyle)"

Girdi tipine göre:
- **Ses/video dosyası** (.m4a .mp3 .wav .mp4 .mov) → Adım 1'den başla
- **Klasör** → içindeki tüm ses dosyalarını sırayla işle (toplu analiz)
- **Hazır .txt transkript** → Adım 1'i atla, Adım 2'ye geç

> ⚠️ **KVKK hatırlatması:** İlk çalıştırmada bir kez hatırlat — görüşme kaydı için karşı tarafın bilgilendirilmiş olması gerekir. Satışçı görüşme başında "kayıt alıyorum" demeli. Kayıtlar yerelde işlenir, dışarı gönderilmez.

---

## ADIM 1 — Transkripsiyon (yerel, Türkçe)

Tamamen yerel çalışır — ses dosyası internete çıkmaz.

```bash
mkdir -p transkriptler
mlx_whisper "<ses-dosyasi>" \
  --model mlx-community/whisper-large-v3-turbo \
  --language tr \
  --output-format txt \
  --output-dir transkriptler
```

Notlar:
- `large-v3-turbo` zaten indirilmiş, hızlıdır. Kayıt çok uzunsa (>1 saat) kullanıcıyı bilgilendir, beklemesini söyle.
- Ses dosyası bozuk/desteklenmiyorsa önce `ffmpeg -i girdi -ar 16000 -ac 1 cikti.wav` ile çevir.
- Konuşmacı ayrımı (kim konuştu) Whisper'da yok. Transkriptte satışçı ile müşteriyi **bağlamdan** ayır; emin olamadığın yeri "(?)" ile işaretle, uydurma.

---

## ADIM 2 — Analiz (satış uzmanı gözüyle)

Transkripti oku, sırayla şu skilleri devreye al ve **tek bir analiz dosyası** üret.

| Sıra | Skill | Çıkaracağın |
|---|---|---|
| 1 | `conversation-summarization` | 5-8 maddelik görüşme özeti |
| 2 | `intent-detection` | Müşterinin gerçek niyeti: bilgi topluyor / karşılaştırıyor / almaya yakın / nazikçe reddediyor |
| 3 | `objection-recognition` | **Sorduğu sorular** ve **itirazlar** — birebir alıntıyla |
| 4 | `sentiment-analysis` | Görüşme boyunca duygu seyri; nerede soğudu, nerede ısındı |
| 5 | `persona-classification` | Alıcı tipi ve iletişim stili |
| 6 | `entity-extraction` + `budget-extraction-qualification` | Bütçe sinyali, zaman, şehir, mevcut durum, hedef |
| 7 | `decision-maker-identification` | Kendi mi karar veriyor, eşi/patronu/İK var mı |
| 8 | `qualifying-leads` | Ne kadar nitelikli: sıcak / ılık / soğuk — gerekçesiyle |
| 9 | `conversation-quality-scoring` | Satışçının görüşme performansı: ne iyi gitti, ne kaçtı |
| 10 | `objection-handling` + `sales-psychology` + `storytelling` | **Her itiraza hazır yanıt** |
| 11 | `closing` + `follow-up-discipline` | Bir sonraki adım ve takip zamanlaması |

### Analiz dosyası formatı
`analizler/<tarih>-<müşteri>.md` olarak yaz:

```markdown
# Görüşme Analizi — <müşteri> · <tarih>

## Özet
<5-8 madde>

## Müşteri profili
| | |
|---|---|
| Niyet | ... |
| Alıcı tipi | ... |
| Karar verici | ... |
| Bütçe sinyali | ... |
| Nitelik | 🔥 sıcak / 🟡 ılık / 🔵 soğuk — gerekçe |

## Sorduğu sorular
1. "<birebir alıntı>" → ne öğrenmek istiyor

## İtirazlar ve hazır yanıtlar
### İtiraz: "<birebir alıntı>"
**Altında yatan kaygı:** ...
**Yanıt:** <Satışçının telefonda söyleyebileceği, ağızdan çıkacak gibi yazılmış cümleler>
**Kanıt/hikâye:** <varsa vaka, sayı, referans>

## Satışçının performansı
✅ İyi gidenler · ⚠️ Kaçan fırsatlar · 🎯 Bir sonraki görüşmede şunu dene

## Sonraki adım
<somut aksiyon + ne zaman>
```

---

## ADIM 3 — Birikimli dosyaları güncelle (EN ÖNEMLİ ADIM)

> Tek görüşme bir şey söylemez; **20 görüşme desen söyler.** Bu adım atlanırsa araç değerinin yarısını kaybeder.

Her çalıştırmada şu üç dosyayı **oku, güncelle, say**:

**1. `ACI-HARITASI.md`** — en sık gelen sorular/itirazlar
```markdown
| Sıra | İtiraz / Soru | Kaç görüşmede | Son görülme | Not |
|---|---|---|---|---|
| 1 | Sertifika geçerli mi | 7 | 06.10 | En sık — web sayfasında cevaplanmalı |
```
Yeni itiraz geldiyse ekle, varsa sayacı artır, sıralamayı güncelle.

**2. `ITIRAZ-PLAYBOOK.md`** — itiraz → en iyi yanıt
Her itiraz için **tek bir** olgunlaşmış yanıt tut. Yeni görüşmede daha iyi bir yanıt çıkarsa eskisini güncelle, altına "(güncellendi: tarih)" yaz. Satışçının telefonda açıp bakacağı dosya budur.

**3. `ICERIK-FIKIRLERI.md`** — içerik havuzu
```markdown
| Fikir | Kaynak itiraz | Format | Öncelik |
|---|---|---|---|
| "Sertifikamız nerede geçerli?" | 7 görüşmede soruldu | Blog + Reels + SSS | 🔴 yüksek |
```

---

## ADIM 4 — İçerik brief'i üret

`content-strategy` + `social-content` + `copywriting` + `marketing-psychology` ile:

- **Müşterinin kendi kelimeleri = en iyi başlık.** Transkriptteki ifadeleri birebir kullan, pazarlama diline çevirme.
- Her yüksek frekanslı itiraz için **bir içerik** öner: format (blog / reels / carousel / SSS / e-posta), başlık, açılış cümlesi (hook).
- 3-5 fikirden fazlasını bir seferde verme — uygulanabilir kalsın.

---

## ADIM 5 — Kullanıcıya sun

Sohbette **kısa** özet ver (dosyaları zaten yazdın):
1. Müşteri profili tek satır + nitelik (🔥/🟡/🔵)
2. Çıkan yeni itiraz var mı
3. **Sonraki adım** (somut)
4. Acı haritasında değişen sıralama varsa söyle
5. 1-2 içerik fikri

Sonra menü sun: *"Toplu analiz ister misin? · Playbook'u yazdırayım mı? · İçerik brief'ini açayım mı?"*

---

## Davranış kuralları
1. **Uydurma.** Transkriptte olmayan itirazı/bilgiyi yazma. Emin değilsen "(belirsiz)" yaz.
2. **Birebir alıntı kullan.** Özetleyip pazarlama diline çevirme — müşterinin kelimesi değerli.
3. **Yanıtlar konuşma dilinde olsun.** Satışçı bunları telefonda okuyacak; yazı dili değil, ağızdan çıkacak cümle yaz.
4. **Adım 3'ü atlama.** Birikimli dosyalar bu aracın asıl değeri.
5. Toplu işlemde her dosya için ayrı analiz yaz, birikimli dosyaları **en sonda bir kez** güncelle.
6. Müşteri adını dosya adında kullanıyorsan sadece **ad + ilk harf** yaz (Ayşe K.) — tam isim/telefon yazma.
