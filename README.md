# Satış Görüşmesi Analizi

**Müşteriyle yaptığın telefon görüşmesinin ses kaydını ver — sana satış analizi, itiraz yanıtları ve içerik fikirleri çıkarsın.**

Claude Code için Türkçe bir `/gorusme` komutu. Ses kaydı tamamen **kendi bilgisayarında** yazıya çevrilir, dışarı gönderilmez.

---

## Ne yapıyor?

Bir ses kaydı veriyorsun. Karşılığında şunlar oluyor:

1. **Yazıya çeviriyor** (Türkçe, bilgisayarında, internetsiz)
2. **Satış uzmanı gibi analiz ediyor** — müşteri ne sordu, nerede itiraz etti, ne kadar sıcak, kim karar veriyor
3. **Her itiraza hazır yanıt yazıyor** — telefonda okuyabileceğin, konuşma dilinde cümleler
4. **Biriktiriyor** — 20 görüşme sonra "en çok sorulan 10 soru" listesi kendiliğinden oluşuyor
5. **İçerik fikri veriyor** — müşterilerin sorduğu sorular = en iyi blog/reels konusu

### En değerli kısmı: birikim

Tek görüşme sana bir şey söylemez. Ama 20 görüşme **desen** söyler:

> "Sertifika geçerli mi?" → **7 görüşmede** soruldu
> Demek ki web sitende bu soru yeterince net cevaplanmıyor.

Araç bunu otomatik sayıyor ve üç dosyada biriktiriyor.

---

## Claude Code'u hiç kullanmadıysan

**Claude Code nedir?** Bilgisayarında çalışan bir yapay zekâ asistanı. ChatGPT gibi yazışıyorsun ama farkı: **bilgisayarındaki dosyaları görebiliyor** ve senin için işlem yapabiliyor. Ses dosyanı okuyup analiz edebilmesinin sebebi bu.

**Komut ne demek?** `/gorusme` gibi eğik çizgiyle başlayan şeyler kısayol. Yazdığında Claude uzun bir talimat listesini açıp adım adım uyguluyor. Sen her seferinde "şunu yap, sonra bunu yap" diye anlatmıyorsun.

**Terminal gerekli mi?** Hayır. Her şeyi Claude Code uygulamasının içinden, **Claude'a yazarak** yapabilirsin.

---

## Kurulum

Bir kere yapılır, sonra hep hazır. Tahmini süre: **15 dakika**.

### 1. Claude Code'u kur ve giriş yap

[claude.com/claude-code](https://claude.com/claude-code) adresinden indir, kur, aç. İlk açılışta hesabınla giriş yaparsın.

### 2. Bir klasör aç

Claude her zaman **bir klasörün içinde** çalışır. Masaüstünde `gorusmeler` diye bir klasör aç, Claude Code'da üstteki klasör seçiciden onu seç.

### 3. Ses yazıya çevirme araçlarını kur

Claude'a sohbet kutusuna şunu yaz:

```
Bilgisayarıma ffmpeg ve whisper kur.
Mac'teysem mlx-whisper da kur (Apple Silicon'da çok daha hızlı).
```

Claude kurulumu senin yerine yapar, izin isterse "izin ver" de.

<details>
<summary>Elle kurmak istersen (tıkla)</summary>

```bash
# Mac
brew install ffmpeg
pip install mlx-whisper        # Apple Silicon (M1/M2/M3/M4) — hızlı
pip install openai-whisper     # alternatif / Intel Mac / Windows / Linux

# Windows / Linux
# ffmpeg: https://ffmpeg.org/download.html
pip install openai-whisper
```
</details>

### 4. Analiz skillerini kur

Claude'a şunu yaz:

```
louisblythe/salesskills reposundan şu skilleri GLOBAL olarak kur:
objection-recognition, objection-pattern-learning, objection-handling,
conversation-summarization, conversation-quality-scoring, sentiment-analysis,
persona-classification, entity-extraction, budget-extraction-qualification,
decision-maker-identification, win-loss-reason-extraction, intent-detection,
discovery, closing, pricing-negotiation, building-rapport, sales-psychology,
storytelling, qualifying-leads, follow-up-discipline
```

İçerik tarafı için de (isteğe bağlı ama tavsiye edilir):

```
coreyhaines31/marketingskills reposundan şunları GLOBAL kur:
content-strategy, social-content, copywriting, marketing-psychology,
customer-research, product-marketing-context
```

> **"Global" ne demek?** Bilgisayarındaki **her klasörde** çalışır demek. Bir kez kurarsın, hangi müşteri klasöründe olursan ol hazırdır. Global kurmazsan sadece o tek klasörde çalışır.

### 5. `/gorusme` komutunu kur

Bu repodaki `commands/gorusme.md` dosyasını bilgisayarında şu klasöre kopyala:

```
~/.claude/commands/gorusme.md
```

Ya da Claude'a yaz:

```
https://github.com/enisakarsu/satis-gorusme-analizi reposundaki
commands/gorusme.md dosyasını ~/.claude/commands/ içine kopyala
```

### 6. Kendi ürününe uyarla ⚠️ ÖNEMLİ

`~/.claude/commands/gorusme.md` dosyasını aç, baştaki **"Ürün bağlamı"** bölümünü doldur:

```
Ne satıyoruz: <ürünün>
Alıcı tipleri: <kimler satın alıyor>
Tipik itiraz eksenleri: <müşteriler genelde neye takılıyor>
```

Bu üç satır analizin isabetini belirliyor. Boş bırakırsan araç çalışır ama genel kalır.

### 7. Claude'u kapat aç

Komutlar açılışta yüklenir. Kapat, yeniden aç. Sohbete `/` yazdığında listede `gorusme` görüyorsan hazırsın.

---

## Nasıl kullanılır

Ses kaydını bir klasöre at, Claude Code'da o klasörü seç ve yaz:

```
/gorusme kayitlar/ayse-k-06ekim.m4a
```

Birden fazla kaydı tek seferde işlemek için klasör ver:

```
/gorusme kayitlar/
```

Elinde zaten yazıya çevrilmiş metin varsa onu da verebilirsin:

```
/gorusme transkriptler/gorusme-12.txt
```

### Desteklenen dosyalar
`.m4a` `.mp3` `.wav` `.mp4` `.mov` — telefon kayıt uygulamalarının çoğu bunlardan birini üretir.

---

## Ne çıkıyor?

Çalıştırdıktan sonra klasöründe şunlar oluşur:

```
gorusmeler/
├── transkriptler/           ← ses kayıtlarının yazıya dökülmüş hâli
├── analizler/               ← her görüşme için ayrı analiz
├── ACI-HARITASI.md          ← ⭐ en çok sorulan sorular (sayaçlı)
├── ITIRAZ-PLAYBOOK.md       ← ⭐ itiraz → hazır yanıt
└── ICERIK-FIKIRLERI.md      ← ⭐ içerik havuzu
```

### Üç birikimli dosya

| Dosya | Ne işe yarar | Kim kullanır |
|---|---|---|
| **ACI-HARITASI.md** | Hangi soru kaç görüşmede geldi, sıralı liste | Pazarlama — neyi düzeltmeli |
| **ITIRAZ-PLAYBOOK.md** | Her itiraza olgunlaşmış tek yanıt | **Satışçı — telefonda açıp bakar** |
| **ICERIK-FIKIRLERI.md** | Frekansa göre sıralı içerik konuları | İçerik ekibi |

Bunlar her görüşmede **güncellenir**, sıfırdan yazılmaz. Zamanla olgunlaşır.

### Tek görüşme analizinde neler var

- Özet (5-8 madde)
- Müşteri profili: niyet, alıcı tipi, karar verici mi, bütçe sinyali
- Niteliklendirme: 🔥 sıcak / 🟡 ılık / 🔵 soğuk — gerekçesiyle
- Sorduğu sorular (birebir alıntı)
- İtirazlar + **her birine hazır yanıt**
- Satışçının performansı: ne iyi gitti, hangi fırsat kaçtı
- Sonraki adım (somut)

---

## Gizlilik ve yasal

- **Ses kayıtları bilgisayarından çıkmaz.** Yazıya çevirme (Whisper) tamamen yerel çalışır, internet gerekmez.
- **Kayıt almadan önce karşı tarafı bilgilendir.** Görüşme başında "kayıt alıyorum" demek yasal gereklilik (KVKK / GDPR).
- Araç dosya adlarında tam isim ve telefon yazmaz (`Ayşe K.` formatı kullanır).
- Analiz metni Claude'a gider — müşteri verisi barındıran işlerde kurumsal gizlilik politikanı kontrol et.

---

## Sık karşılaşılan sorunlar

| Sorun | Çözüm |
|---|---|
| `/gorusme` listede yok | Claude'u kapat aç. Dosya `~/.claude/commands/gorusme.md` yolunda mı kontrol et. |
| "whisper bulunamadı" | Kurulum adım 3'ü tekrarla. Claude'a "whisper kurulu mu kontrol et" diyebilirsin. |
| Transkript çok yavaş | Mac'te `mlx-whisper` kur — 5-10 kat hızlı. Uzun kayıtlarda (1 saat+) ilk çalıştırma model indirir, sabırlı ol. |
| Transkript bozuk/anlamsız | Ses kalitesi düşük olabilir. Claude'a "bu dosyayı ffmpeg ile 16kHz mono wav'a çevir, sonra dene" de. |
| Analiz genel/yüzeysel kaldı | Kurulum adım 6'yı yap — ürün bağlamını doldur. |
| Kim konuşuyor karışmış | Whisper konuşmacı ayrımı yapmaz. Araç bağlamdan ayırmaya çalışır, emin olmadığı yeri `(?)` ile işaretler. |

---

## Teşekkür

Bu komut şu açık kaynak skill paketlerini kullanır:

- **[louisblythe/salesskills](https://github.com/louisblythe/salesskills)** — satış analizi skilleri
- **[coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills)** — içerik ve pazarlama skilleri
- **[openai/whisper](https://github.com/openai/whisper)** · **[ml-explore/mlx-examples](https://github.com/ml-explore/mlx-examples)** — konuşma tanıma

## Lisans

MIT — bkz. [LICENSE](LICENSE). Kullanılan skill paketleri kendi lisanslarına tabidir.
