# reMona Desktop

**reMona**, Windows için bir **Agent IDE**: sağlayıcı ekleyip sohbet edersiniz, aynı pencerede
araç kullanan otonom bir **coder** ajanı çalıştırırsınız, ajan kendi gömülü tarayıcısında ve
terminalinde iş yapar, işi bitince Git panelinden değişiklikleri inceleyip commit edersiniz.

Bu depo **yalnızca yayınlanan kurulum dosyalarını** (Releases) barındırır; **kaynak kod
paylaşılmaz**.

---

## İndirme

Kurulum dosyaları yalnızca **Releases** bölümündedir: **[Son sürüm →](../../releases/latest)**

| Dosya | Boyut | Açıklama |
| --- | --- | --- |
| `reMona-Setup-<sürüm>.exe` | ~133 MB | Windows x64 kurulum dosyası (NSIS) |
| `reMona-Setup-<sürüm>.exe.blockmap` | ~0,14 MB | Otomatik güncelleme için delta haritası |

Her sürümün SHA-256 özeti, o sürümün release notlarında yazar. İndirdiğiniz dosyayı
doğrulamak için:

```cmd
certutil -hashfile "reMona-Setup-0.9.13.exe" SHA256
```

**0.9.13** için beklenen özet:
`3f39f8a87fbee192254ff523d9eb90a48b8b83210b025d6b7c14a545acaa7dbd`

---

## Sistem gereksinimleri

| | |
| --- | --- |
| İşletim sistemi | Windows 10 veya Windows 11 |
| Mimari | x64 (64-bit). ARM64 kurulum dosyası yayınlanmıyor. |
| Disk | ~133 MB kurulum dosyası, kurulduktan sonra **~500 MB** |
| İnternet | Model yanıtları için gerekli (yerel Ollama hariç) |

macOS için durum ayrı bölümde: [macOS](#macos).

---

## Kurulum

1. Releases sayfasından `reMona-Setup-<sürüm>.exe` dosyasını indirin.
2. Çalıştırın. Kurulum **imzasız** olduğu için Windows SmartScreen
   **"Bilinmeyen yayıncı"** uyarısı gösterir: **Daha fazla bilgi** → **Yine de çalıştır**.
   (Dosya özetini yukarıdaki `certutil` komutuyla karşılaştırabilirsiniz.)
3. Kurulum **tek kullanıcı içindir**, yönetici izni istemez ve hedef klasörü siz seçersiniz.
   Varsayılan konum kullanıcı klasörüdür.
4. Kurulum biter ve uygulama açılır.

**Sessiz kurulum** (betik/otomasyon için):

```cmd
reMona-Setup-0.9.13.exe /S
```

**Kaldırma:** Windows **Ayarlar → Uygulamalar** listesinden *reMona* → *Kaldır*.
Kaldırma **verilerinizi silmez**; sohbetler ve anahtarlar aşağıdaki veri klasöründe kalır.
Tamamen temizlemek için o klasörü de silin.

> Kurulum sırasında Windows Güvenlik Duvarı sorarsa, uygulama yalnız **yerel ağ** üzerinden
> mobil denetim sunucusu açtığı için izin istenir. İzin vermezseniz uygulama normal çalışır,
> yalnız telefondan denetim kullanılamaz.

---

## İlk açılış

Uygulama ilk açılışta **Ayarlar → Model ayarları** kataloğunu açar. Bir model bağlantısı
tanımlamadan sohbet başlamaz.

1. **Bir sağlayıcı seçin.** Hazır ön ayarlar gelir; adres ön ayardan gelir, sizden yalnız
   **API anahtarı** istenir.
2. **Anahtarı yapıştırın ve kaydedin.** Model listesi otomatik çekilir (`/v1/models`).
3. **Modeli seçin** (üst çubuktaki model menüsü) ve sohbeti başlatın.
4. **Coder için bir klasör seçin.** Coder görevi, seçtiğiniz klasöre *sandbox*'lanır:
   okuma/yazma o klasörle sınırlıdır, dışına çıkan yollar reddedilir.

İki özel durum:

- **OpenCode Zen (ücretsiz hat)** — anahtar istemez; kimliği maskenizdeki opencode
  kurulumundan **otomatik** okur. Hazır gelir, seçip kullanmaya başlarsınız.
- **reMona Platform (mağaza)** — ana sağlayıcı olarak hazır gelir. Kimlik bilgisi
  reMona hesabıyla **OAuth** girişi ya da konsoldan alınan `sk-rmn-…` anahtarıdır;
  fiyat/kota bilgisi ve kullanım pencereleri bu sağlayıcıdan okunur.

---

## Neler yapabilir

### Üç arayüz modu

Hesap menüsünden seçilir, tercih hatırlanır:

| Mod | Ne verir |
| --- | --- |
| **Kodlama** | Tam arayüz: Sohbet ve Coder ekranları, gelişmiş kontroller |
| **Ofis** | Sadeleştirilmiş, **sohbet odaklı** görünüm (coder ekranı ve gelişmiş kontroller gizlenir) |
| **Video** | Video çalışma alanı: storyboard, Player önizlemesi, render kuyruğu |

### Sohbet

- **SSE ile akan yanıt**, katlanabilir **"Düşünce süreci"** bloğu (reasoning modelleri).
- **Markdown + kod blokları** (kopyala düğmeli) ve **GFM tabloları** (hizalama, hücre içi
  biçimlendirme). Modelin ürettiği ham HTML güvenlik için her zaman kaçırılır.
- **Görsel ekleme**: dosyayı sürükleyip yapıştırın; ekler diskte tutulur, istek anında
  içerik parçası olarak gönderilir.
- **Web araması / derin araştırma** ve **yerleşik (native) web araması** — arama istemcide
  koşar, her sağlayıcıda çalışır.
- **Bağlam pencereleri** ve model başına **düşünme eforu** (uç `/v1/models`'te hangi
  seviyeleri desteklediğini bildiriyorsa menü ona göre daralır).

### Coder — araç kullanan otonom ajan

Coder döngüsü **istemcide** koşar (ana süreç), uç kendi `/v1/agent/run` ucunu sunuyorsa istek
oraya yönlenir. Ajanın araç seti:

`bash` · `read_file` · `write_file` · `edit_file` · `glob` · `grep` · `list_dir` · `todo` ·
`skill` · `memory` · `finish` · `web_search` · `web_fetch` · `subagents` (açık istekte) ·
`browser` · `computer` (kapılar açıksa)

Araç çağrıları ve çıktıları **zaman çizelgesinde canlı** görünür; yanıtın altında
adım/araç/süre özeti çıkar.

- **Görev listesi (todo)** — model `todo` aracını kullandığında başlığın altında canlı bir
  panel açılır. Panel etkileşimlidir: bir maddeyi **siz de "bitti" işaretleyebilirsiniz** ve
  bu işaret sonraki isteğin gövdesine girer.
- **Plan modu → onay** — plan hazır olunca turun altında **onay kartı** çıkar:
  *Otomatik düzenle* ya da *Full access* derseniz **aynı tur** o modda sürer.
- **Durdur / devam** — koşu sırasında gönder düğmesi **duraklat** olur; iptal upstream
  bağlantısını gerçekten kapatır. Durdurulan tur **"Devam et"** ile kaldığı yerden sürer.
- **Arka plan komutları** — `bash background:true`: uzun süren derleme/sunucu arka planda
  başlar, çıktı `.remona/background/<id>.log` dosyasına yazılır, ajan elindeki işe devam eder;
  komut bitince ajan bilgilendirilir.
- **Alt ajanlar (subagents)** — yalnız siz açıkça isterseniz ("alt ajanlarla/paralel yap"):
  görev bölünür, alt ajanlar **paralel** koşar, sonuçları ana ajana rapor edilir ve nihai
  yanıtı ana ajan yazar.
- **Proje kuralları ve bellek** — çalışma alanındaki `AGENTS.md` ve `.remona/memory.md`
  ajan koşuları arasında taşınan kalıcı bağlamdır.

**İzin modları** (besteci solundaki rozet; görev başına, istemci döngüsünde uygulanır):

| Mod | Ne yapar |
| --- | --- |
| **Plan** | Yalnız okuma: inceler, plan çıkarır. Hiçbir dosya değiştirilmez, komut çalışmaz. Hazır olunca onay kartı sunar. |
| **Değişiklikten önce sor** | Okuma serbest; her yazma/komut **çalışmadan önce** onay kartına düşer (*İzin ver · Bu koşuda hep izin ver · Reddet*). Onay kanalı yoksa çağrı reddedilir. |
| **Otomatik düzenle** | Dosya okur ve yazar, **bash yok** — komut gerektiren doğrulamaları yapamaz ve bunu açıkça söyler. |
| **Full access** | Tüm araçlar (varsayılan). Yıkıcı komutlar yine de deny-list'e takılır. |

**Hassas dosya koruması her modda geçerlidir** — `.env` ailesi, `*.pem` / `*.key` / `id_rsa`,
`.ssh/`, `.aws/`, `.npmrc`, `.netrc`, `.git-credentials`, `credentials.json` gibi hedefler
Full access dahil **ayrı ayrı sorulur** ve toplu "hep izin ver" sunulmaz. `.env.example`
gibi şablonlar serbesttir (sır taşımazlar).

### Sağlayıcılar

Hazır ön ayarlar (yalnız API anahtarı gerekir):

`OpenAI` · `Anthropic (Claude)` · `Google Gemini` · `NVIDIA NIM` · `OpenRouter` · `Groq` ·
`DeepSeek` · `Moonshot (Kimi API)` · `xAI (Grok)` · `Mistral` · `Together AI` · `Cerebras` ·
`Fireworks AI` · `Ollama (yerel)` · `OpenCode Zen (ücretsiz)` · `Command Code` ·
`reMona Platform (mağaza)`

Ayrıca **Özel uç nokta** ile OpenAI uyumlu herhangi bir sunucu bağlanabilir.

- **Anthropic kendi protokolüyle** konuşulur (`/v1/messages` ↔ `/v1/chat/completions`
  adaptörü; system ayırma, görsel blokları, `thinking` akışı).
- **Anahtarlar arayüze hiç gelmez**: istekler ana süreçteki **yerel ağ geçidinden** (loopback,
  her oturumda yenilenen jeton) geçer. Anahtar renderer'a sızmaz, CORS engeli de bu kapıda aşılır.
- **Sağlayıcı paketi** ile tanımlarınızı dışa/içe aktarabilirsiniz (yedek ve makine taşıma).
- Birden çok sağlayıcı **tek model listesinde** görünür (`<sağlayıcı>::<model>` kimliğiyle).

### Gömülü tarayıcı

Sağ panelin **Tarayıcı** sekmesi (Ctrl+Shift+B) gerçek, sekmeli bir tarayıcıdır — ve
**her sohbetin kendi oturumu** vardır: ayrı sekme listesi, ayrı **kalıcı profil/çerezler**,
ayrı cihaz ve yakınlaştırma.

- Sekme çubuğu, adres çubuğu (URL ya da arama), geri/ileri, konsol, ekran görüntüsü,
  **öğe seçici** ve "sayfayı sohbete gönder".
- **Görünüm ölçüsü**: panelin tamamı, masaüstü yatay (1280/1440/1920), masaüstü dikey,
  iPad, iPhone 12/SE, Pixel 5, Galaxy S20 ve serbest ölçü. Telefon/tablet ön ayarları
  mobil UA + dokunma açar; sayfa istenen CSS genişliğinde dizilir (panele kırpılmaz).
- **Ajan da bu tarayıcıyı kullanır** (`browser` aracı): gezme, tıklama, form doldurma,
  ekran görüntüsü, öğe seçme; hangi sohbette koşuyorsa komutlar oraya gider ve arka
  plandaki sohbetin odağı çalınmaz. Ekran görüntüleri koşu tavanıyla (40 kare) sınırlıdır.
- Panel kapansa da sekmeler yaşar; sohbet silinince o sohbetin kalıcı profili de silinir.

### Tümleşik terminal

Ctrl+J ile sohbetin altında bir kabuk açılır. Kabuk **proje klasöründe** doğar,
**sohbete özeldir** ve ana süreçte yaşar: panel kapansa, sohbet değişse ya da arayüz
yenilense de `cd` ve ortam değişkenleri korunur. Kabuk seçilebilir
(**cmd · PowerShell · bash**).

### Sağ yan panel (tek panel, sekmeli)

Tarayıcı, dosyalar, ajanlar ve Git aynı panelin sekmeleridir (`Ctrl+Alt+B` paneli gizler/gösterir):

| Sekme | İçerik |
| --- | --- |
| **Tarayıcı** | Yukarıdaki gömülü tarayıcı |
| **Dosyalar** | Ajanın okuduğu dosyalar sekmeli açılır; kopyala, sistem uygulamasında aç |
| **Ajanlar** | Çalışma alanındaki ortak ajan defteri (`collab`) |
| **Git** | Çalışma alanının deposu |

**Git sekmesi**: dal (+ uzak iz ↑/↓), **Sahnelenen · Değişen · İzlenmeyen** bölümleri,
satır başına `+N/−M` istatistiği; sahnele/çıkar, commit (Ctrl+Enter), **amend**, dal
değiştir/oluştur/sil, uzak ekle, fetch, pull (`--ff-only`), push. Dosyaya tıklayınca
**farkı**, commit'e tıklayınca **commit detayı** açılır. `--force` hiçbir yolda üretilmez;
yıkıcı eylemler iki adımlıdır; **hassas dosyaların farkı arayüze taşınmaz** (satır
"hassas" işaretlenir).

### Mobil uzaktan denetim

**Ayarlar → Mobil uzaktan → Başlat** → çıkan **QR**'ı telefonla okutun (ya da adresi elle açın).
Telefon dört görünümlü bir uygulamadır: **Sohbetler · Aktif · Dosyalar · Terminal**.

- **Aktif** görünümde canlı transkript, araç satırları, **onay kartları** ve **plan kartı**
  görünür: koşuyu izler, mesaj gönderir, durdurur ve "Devam et" ile sürdürürsünüz.
- **Dosyalar** çalışma alanında gezinir ve dosya okur (512 KB tavanı, ikili reddi);
  telefondan dosyaya **yazılmaz** — yazmayı sohbetin araçları yapar.
- **Terminal** masaüstünde açık kabuğun aynasıdır ve o kabuğa komut yazabilir.

Sınırlar açıktır: sunucu **yalnız yerel ağda** çalışır (tünelse/internete açılmaz; eş adresi
kapısı jeton kapısından önce çalışır) ve her çağrı **jeton** taşır (sabit zamanlı
karşılaştırma, hatalı denemede kısa süreli kilit).

### MCP, beceriler, eklentiler

- **MCP sunucuları** (`Ayarlar → MCP sunucuları`): yapılandırma **Claude Code uyumludur**
  (`{"mcpServers": {…}}`; stdio için `command`/`args`/`env`/`cwd`, uzak için
  `type:"http"|"sse"` + `url` + `headers`). MCP, ajanın araç setini çekirdek koda
  dokunmadan **genişletir**; araç onayları izin modlarına ve hassas dosya kapısına uyar.
  Depoyla gelen `<çalışma alanı>/.remona/mcp.json` **onay ister**.
- **Beceri kitaplığı** (`Ayarlar → Beceri kitaplığı`): ajanın `skill` aracıyla yüklediği
  talimat paketleri.
- **Eklentiler** (`Ayarlar → Eklentiler`): paket listesi, arama ve katalog.
- **Kurulumla gelen paketler**: kurulum dosyası **50 hazır paket** (145 beceri, 32 pakette MCP sunucu
  tanımı) ile gelir — ilk açılışta ağdan bir şey indirmeniz gerekmez.

### Zamanlanmış görevler (Görevler)

`Ayarlar → Görevler`: zamanı gelince masaüstünde **yeni bir sohbet/coder kaydı açıp istemi
kendiliğinden gönderen** tanımlar (tekrarlayan rutinler için).

### Video modu

Üçüncü tam ekran mod: **storyboard** (başlık, maddeler, görsel, alıntı, grafik sahneleri),
**Player** önizlemesi ve **render kuyruğu** (ilerleme, iptal, kare önizleme). Her düzenleme
otomatik kaydedilir; çıktı dosyasının yerini uygulama belirler.

### Kullanım istatistikleri

`Ayarlar → Kullanım istatistikleri`: sağlayıcının bildirdiği `usage` alanından toplam/girdi/çıktı
token, sohbet ve gün kırılımı tabloları ve **CSV olarak indirme** (Excel uyumlu).
reMona Platform kullanıyorsanız üstteki **Coder paketi** kartı üç kullanım penceresini
(5 saatlik / haftalık / aylık) yüzde ve yenilenme geri sayımıyla gösterir.

---

## Klavye kısayolları

| Kısayol | İşlev |
| --- | --- |
| `Ctrl+N` | Yeni sohbet / coder görevi |
| `Ctrl+K` | Arama (`proje:ad` ile proje filtresi) |
| `Ctrl+P` | Komut paleti |
| `Ctrl+,` | Ayarlar |
| `Ctrl+Alt+B` | Yan paneli gizle/göster |
| `Ctrl+Shift+B` | Tarayıcı sekmesi |
| `Ctrl+J` | Tümleşik terminal |
| `Esc` | Açık modalı kapat |

Terminal odaklıyken: `Ctrl+J` satır sonu, `Ctrl+L` temizle, `Ctrl+C` satırı iptal / seçimi kopyala.

---

## Verileriniz nerede

Her şey **yerelde** durur; buluta kopyalanmaz. Windows'ta kök klasör:

```
%APPDATA%\reMona\remona-desktop\
```

| Yol | İçerik |
| --- | --- |
| `store.json` | Sohbetler, projeler, ayarlar |
| `store.json.bak-<zaman>.gz` | Otomatik yedekler (son 10 adet) |
| `providers.json` | Sağlayıcı tanımları + **API anahtarları** (yalnız sizin hesabınız) |
| `mcp.json` | Kullanıcı katmanı MCP sunucuları |
| `attachments\` | Sohbete eklenen dosyalar/görseller |
| `screenshots\` | Ajanın ve tarayıcının ekran görüntüleri |
| `downloads\` | Gömülü tarayıcının indirmeleri |
| `logs\` | Günlükler (`Ayarlar → Günlük`) |

**Yedekleme:** uygulamayı kapatın, `store.json` + `providers.json` dosyalarını kopyalayın.
**Geri yükleme:** kopyaladığınız dosyaları aynı yere koyup uygulamayı açın.
Yanlışlıkla silinen sohbet için: `store.json.bak-<zaman>.gz` dosyasını açıp (gzip) çıkan
`store.json`'ı yukarıdaki klasöre kopyalayın ve uygulamayı yeniden başlatın.

---

## Gizlilik ve güvenlik

- **Model istekleri doğrudan sizin sağlayıcınıza gider.** reMona araya girmez; uç noktayı ve
  anahtarı siz belirlersiniz (reMona Platform'u seçerseniz elbette o sağlayıcı kullanılır).
- **Anahtarlar renderer'a gelmez.** Tüm istekler yerel ağ geçidinden (loopback, oturumluk
  jeton) geçer; anahtar dosyası yalnız sizin kullanıcı hesabınızca okunur.
- **Telemetri yok.** reMona kullanım verisi toplamaz. Uygulamanın kendiliğinden yaptığı tek
  ağ isteği **güncelleme denetimidir**; kapatmak için ortam değişkeni:
  `REMONA_NO_UPDATE_CHECK=1`.
- **Çalışma alanı sandbox'ı**: coder araçları seçtiğiniz klasörün dışına çıkamaz.
- **Hassas dosya kapısı**: sır taşıyan dosyalar her izin modunda ayrıca sorulur; onay kanalı
  yoksa erişim **reddedilir** (fail closed).
- **Mobil sunucu** yalnız yerel ağda dinler, jeton ister ve genel bir arayüze düşen trafik
  jeton denemesinden önce reddedilir.
- **Web araçları**: `web_fetch` yerel/özel adresleri reddeder (SSRF koruması).

---

## Otomatik güncelleme

Uygulama yeni sürümü **remona.net** üzerinden denetler (açılıştan sonra bir kez ve 6 saatte
bir):

1. **Denetle** — `GET https://remona.net/api/desktop/latest`; yayın yoksa sessizce geçer.
2. **İndir** — yalnız **sizin onayınızla**; yarıda kalan indirme sonraki açılışta temizlenir.
3. **Doğrula** — **SHA-256 zorunludur**; boyut ya da özet uyuşmazsa dosya silinir ve kurulum
   başlatılmaz. İndirme adresi `https` olmalı ve alan adı remona.net ya da manifestoyu sunan
   alan adı olmalı.
4. **Kur** — NSIS sessiz kurulumu; kurulum dizini kayıt defterinden okunur, yani özel bir
   klasöre kurmuş olsanız bile **yerinde** güncellenir. Sohbetler, anahtarlar ve ayarlar korunur.

İndirme ve kurulum asla sessizce başlamaz. "Bu sürümü atla" seçeneği bir sonraki sürümde
yeniden sorar.

> **GitHub Releases bir indirme/arşiv kanalıdır**; otomatik güncelleme akışı remona.net
> üzerinden yürür. Buradan elle indirdiğiniz kurulum dosyasını doğrudan çalıştırabilirsiniz —
> mevcut kurulumun üzerine kurulur, verileriniz korunur.

---

## macOS

Uygulama macOS'ta da çalışır (Apple Silicon ve Intel): pencere kromu ve uygulama menüsü
mac'e uyarlıdır (⌘Q/⌘C/⌘V), terminal `zsh`'i seçer, dock tıklaması pencereyi geri getirir.
**Bilgisayar kullanımı** (fare/klavye denetimi) hâlâ yalnız Windows'tadır ve otomatik
güncelleme mac'te kapalıdır (NSIS akışı Windows'a özgüdür; mac'te yeni `.dmg` kurulur).
Bu depoda şu an yalnız Windows kurulum dosyası yayınlanıyor; dmg üretimi macOS gerektirir.

---

## Sorun giderme

| Belirti | Çözüm |
| --- | --- |
| "Windows kişisel bilgisayarınızı korudu" | **Daha fazla bilgi** → **Yine de çalıştır**. Kurulum imzasız; dosya özetini `certutil` ile doğrulayın. |
| Antivirüs dosyayı karantinaya aldı | Klasörü istisnaya ekleyin ya da kurulum dosyasını yeniden indirip özeti karşılaştırın. |
| Model listesi boş | **Ayarlar → Model ayarları**: anahtarın kaydedildiğini ve uç noktanın erişilebilir olduğunu kontrol edin; "anahtar bekliyor" işareti varsa anahtarı girin. |
| İstekler hata dönüyor | **Ayarlar → Günlük**'e bakın; ağ geçidi ve sağlayıcı yanıtı orada satır satır yazar. |
| Telefon bağlanmıyor | Telefon ve bilgisayar **aynı ağda** olmalı; güvenlik duvarında uygulamaya izin verin; ağ profili "Özel" olsun. |
| Güncelleme inmiyor | Releases'ten elle indirip kurun; mevcut kurulumun üzerine yazılır. |
| Kurulum "dosya kullanımda" diyor | Uygulamayı (ve varsa arka planda kalan reMona süreçlerini) kapatıp tekrar deneyin. |
| Sohbet yanlışlıkla silindi | `store.json.bak-<zaman>.gz` yedeğini geri yükleyin (yukarıdaki "Verileriniz nerede"). |

---

## Bu depo hakkında

- Burada **yalnızca derlenmiş kurulum dosyaları** yayınlanır; **kaynak kod paylaşılmaz**.
- Sürüm notlarını ve SHA-256 özetlerini **Releases** sayfasında bulabilirsiniz.
- Hata bildirimi ve öneriler için **Issues** bölümünü kullanabilirsiniz.
- Eski sürümler arşiv olarak eklenmez; her zaman en son sürüm önerilir.
