# FLATHEIM — Üretim Rehberi (Tüm AI Agent'lar İçin)

> **Sürüm:** 2 · **Kapsam:** Sezon 1 ve sonrası tüm bölümler
> **Referans uygulama:** `Flatheim_Son_Dilek.html` (Sezon 1 · Bölüm 1). Bu rehberdeki kod blokları bu dosyadan **birebir** alıntıdır.
> **Hedef okuyucu:** Flatheim bölümü üreten, düzenleyen, denetleyen her AI agent.

---

## 0. BU DOSYA NASIL KULLANILIR

**Bu dosya bağlayıcıdır.** Flatheim bölümü üretecek her agent işe başlamadan önce bu rehberi **baştan sona** okur. Rehber üç şeyi tanımlar:

1. **NASIL ÇİZİLİR / ÇALIŞIR** (Bölüm 3–11): HTML iskeleti, CSS, zaman motoru, SVG çizim dili, karakter fabrikaları, ses, anlatı, overlay. **Değiştirilemez.**
2. **NE ANLATILIR** (Bölüm 12–15): dünya, karakterler, kurallar, bölüm listesi.
3. **NASIL ÇALIŞILIR** (Bölüm 2, 16–18): iş akışı, kontrol listesi, hata listesi.

**Öncelik sırası (çelişki olursa):**
1. Kullanıcının o bölüm için verdiği **açık talimat** (içerik/olay konusunda),
2. Bu rehber (**format, stil, teknik yapı konusunda daima bu rehber**; kullanıcı açıkça "formatı değiştir" demedikçe),
3. Bölüm 1 HTML'i (rehberde belirsiz kalan bir ayrıntı varsa referans).

**Altın kural:** Kullanıcı bu formatın **hiçbir zaman değiştirilmemesini** istedi. "Daha iyi olur" diye mimari, stil, font, renk dili, süre, ses yaklaşımı, kontrol çubuğu, altyazı sistemi **değiştirilmez**. Yenilik yalnızca **yeni sahne içeriği** olarak eklenir.

---

## 1. ALTIN KURALLAR (özet)

1. Her bölüm **tek bir `.html` dosyası**dır. Dış görsel/ses/video yok. Tek dış kaynak: Google Fonts.
2. Sahne: **9:16**, `viewBox="0 0 360 640"`, SVG ile çizilir.
3. Süre: **sabit 90 sn** (`const D=90`).
4. **Her şey `t` (saniye) fonksiyonudur.** `render(t)` saf çizim yapar; durum biriktirmez. Sarma (seek) her zaman çalışır.
5. Müzik/efekt **Web Audio ile üretilir**; anlatıcı **tarayıcı `speechSynthesis` Türkçe sesidir**. Altyazı kelime kelime vurgulanır.
6. Karakterler **sade SVG şekillerdir** (daire, dikdörtgen, çizgi, yarım daire) ve **siluet + kenar ışığı (rim light)** ile sinematik hale gelir. Detaylı/gerçekçi figür çizilmez.
7. Işık: **LUX altın `#f0c470`** · gölge: **NOX mor `#7a6ae0`** · yaşam monitörü yeşili `#5dffb0`. Zemin: koyu, soğuk.
8. Rastgelelik yalnız **tohumlu `rng(seed)`** ile yapılır. `Math.random()` yalnızca ses gürültü tamponunda kullanılır.
9. Ana karakter **aşırı güçlü gösterilmez, adı yoktur**, Işık/Gölge'nin anlamı açıklanmaz.
10. Bölüm 1 dosyası **asla değiştirilmez**; her bölüm **yeni dosyadır**.

---

## 2. AGENT İŞ AKIŞI (adım adım)

### 2.1 Girdi toplama
- Kullanıcıdan **bölüm içeriği** (olaylar, mekân, duygu, diyalog/anlatım) alınır. Yoksa Bölüm 15'teki taslak kullanılır.
- Eksik/çelişen **kritik** bilgi varsa **tek** kısa soru sorulur. Aksi halde varsayım tek cümlede belirtilip devam edilir.

### 2.2 Bölüm planı (dahili)
Yazmadan önce şunlar netleştirilir:
- **Sahne tablosu:** `id`, `a` (başlangıç sn), `b` (bitiş sn), `f` (giriş geçiş süresi). Toplam 4–6 sahne.
- **Cue listesi:** `[başlangıç, bitiş, "anlatıcı metni"]`, 14–20 cümle.
- **Overlay zamanları:** kanca, başlık kartı, köşe başlığı, özel yazılar, bitiş kartı.
- **Ses planı:** sürekli katmanlar + tek seferlik olaylar.
- **Poster karesi** (`POSTER`): durağan durumda gösterilen, en çekici an (Bölüm 1'de 5.6 sn: başlık kartı).

### 2.3 Yazım
1. Bölüm 1'den **motoru kopyala** (Bölüm 4–11'deki bloklar): CSS, stage DOM, yardımcı fonksiyonlar, render, ses, oynatma.
2. `defs`'e yalnızca **gerçekten kullanılan** gradyan/filtre/clipPath'leri koy.
3. Her sahne için: **statik SVG grubu** + (gerekirse) JS ile dinamik öğe kurulumu + `animSN(t)`.
4. `scenes`, `CUES`, `BEATS`/`EV`, `overlays()`, `updateCont()` bölümünü yeni bölüme göre uyarla.
5. **Bölüm 1'den Bölüm N'ye taşırken değiştirilecek yerler** (Bölüm 17.1'de liste).

### 2.4 Doğrulama
- Bölüm 17'deki **kalite kontrol listesi**.
- JS sözdizimi kontrolü (en azından `node --check` ile `<script>` içeriği).
- Mümkünse headless tarayıcıyla `t=0, 10, 30, 50, 70, 89` karelerinde ekran görüntüsü alıp bakmak.

### 2.5 Teslim
- Dosya adı: `Flatheim_Bolum_<N>_<Ad>.html` (ASCII, örn. `Flatheim_Bolum_2_Ahir.html`).
- Dosya çıktı klasörüne konur ve kullanıcıya sunulur. Kısa özet: ne yapıldı, hangi varsayımlar, hangi yeni elemanlar (varsa).

---

## 3. DOSYA MİMARİSİ (üst düzey)

```
<!doctype html><html><head> (reset + viewport-fit=cover) </head><body>
  <title>Flatheim <Ad></title>
  <link> Google Fonts (Cinzel, Cormorant Garamond, Inter)
  <style> … tüm CSS … </style>
  <main class="col">
    .head            → FLATHEIM + "Sezon 1 · Bölüm N · <Ad> · 1:30"
    .stage#stage     → 9:16 sahne
        svg#art      → <defs> + <g id="s1"…"sN" class="scene">…
        .grain .vig .shade .bartrack .bar .mark
        .ov#hook .ov#titleCard .ov#cap .ov#wishes(opsiyonel) .ov#flash .ov#endcard
        #subs
        button#startBtn
    .controls        → Oynat, Baştan, zaman, Anlatıcı, Müzik, #seek, .note
  </main>
  <script> (function(){ 'use strict'; … tek IIFE … })(); </script>
```

**Tek IIFE, `'use strict'`.** Global değişken sızdırılmaz. Dış kütüphane yok.

### 3.1 Sahne üstü katman sırası (aşağıdan yukarı)
| z | Katman | Not |
|---|---|---|
| 0 | `svg#art` içindeki sahneler | `.scene` grupları, `will-change:opacity` |
| – | `.grain` | `opacity:.10`, `mix-blend-mode:overlay`, SVG `feTurbulence` data-URI |
| – | `.vig` | `radial-gradient(120% 90% at 50% 45%, transparent 55%, rgba(0,0,0,.55) 100%)` |
| – | `.shade` | alt %34 siyah geçiş (altyazı okunurluğu) |
| 3 | `.wishes`, `.endcard` | |
| 4 | `.flash`, `.mark` | |
| 5 | `#subs`, `.bartrack` | |
| 6 | `.bar` | altın ilerleme çubuğu |
| 8 | `.start` butonu | |

### 3.2 Güvenli alan
- **Altyazı:** `bottom:11%`, yan boşluk `7%`, `5.6cqw` Inter 700.
- **Alt %34** `.shade` ile kararır. Önemli görsel unsur (yüz, göz, olay) **üst %65'e** konur; alt kısım zemin/siluet/boşluk olur.
- **Sağ üst** `.mark` (`S1 · B<N>`) ve **üst 3px** ilerleme çubuğu için boşluk bırak.
- Başlık/kanca yazıları üstte (`top:20%`–`34%`), köşe başlığı sol üstte (`top:7%`).

---

## 4. CSS (referans, birebir)

Aşağıdaki blok **olduğu gibi** kullanılır. Yeni bölümde yalnızca **bölüme özgü** yeni overlay sınıfları (örn. yeni bir söz kartı) **eklenir**, mevcutlar değiştirilmez.


```css
/* Layout: tek sütun, ortada 9:16 dikey sahne (Reels/Shorts oranı), altında oynatma çubuğu.
   Renk: Flatheim'in iki gücü, ışık (LUX, altın) ve gölge (NOX, mor), soğuk hastane tonları üzerinde. */
:root{
  --bg:#0a0d11;
  --panel:#121820;
  --panel-2:#1a222c;
  --line:#2a3541;
  --fg:#ece6da;
  --muted:#8d97a2;
  --lux:#f0c470;
  --nox:#7a6ae0;
  --display:'Cinzel','Trajan Pro',Georgia,serif;
  --serif:'Cormorant Garamond',Georgia,'Times New Roman',serif;
  --sans:'Inter',system-ui,-apple-system,'Segoe UI',Roboto,sans-serif;
  color-scheme:dark;
}
html{background:var(--bg)}
body{
  margin:0;background:var(--bg);color:var(--fg);
  font:14px/1.5 var(--sans);
  padding-block:10px 18px;
  padding-left:max(16px,env(safe-area-inset-left,0px));
  padding-right:max(16px,env(safe-area-inset-right,0px));
}
.col{
  width:min(100%,max(300px,calc((100dvh - 176px) * 0.5625)));
  margin-inline:auto;display:flex;flex-direction:column;gap:10px;
}
.head{display:flex;align-items:baseline;justify-content:space-between;gap:12px;min-width:0}
.head h1{margin:0;font:700 15px/1.2 var(--display);letter-spacing:.2em;color:var(--lux)}
.head p{margin:0;color:var(--muted);font-size:12px;text-align:right}

.stage{
  position:relative;width:100%;aspect-ratio:9/16;overflow:hidden;border-radius:14px;
  background:#000;container-type:inline-size;isolation:isolate;
  box-shadow:0 0 0 1px var(--line),0 18px 50px rgba(0,0,0,.5);
}
.stage svg#art{position:absolute;inset:0;width:100%;height:100%;display:block}
.stage .scene{will-change:opacity}
.grain{position:absolute;inset:0;pointer-events:none;opacity:.10;mix-blend-mode:overlay;
  background-image:url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='160' height='160'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='.9' numOctaves='2' stitchTiles='stitch'/></filter><rect width='160' height='160' filter='url(%23n)'/></svg>")}
.vig{position:absolute;inset:0;pointer-events:none;background:radial-gradient(120% 90% at 50% 45%,transparent 55%,rgba(0,0,0,.55) 100%)}
.shade{position:absolute;left:0;right:0;bottom:0;height:34%;pointer-events:none;background:linear-gradient(to bottom,transparent,rgba(0,0,0,.72))}
.ov{position:absolute;pointer-events:none;opacity:0}
.bar{position:absolute;left:0;top:0;height:3px;width:0;background:var(--lux);z-index:6;box-shadow:0 0 8px var(--lux)}
.bartrack{position:absolute;left:0;right:0;top:0;height:3px;background:rgba(255,255,255,.12);z-index:5}
.mark{position:absolute;right:5%;top:2.6%;z-index:4;font:600 2.5cqw/1 var(--sans);letter-spacing:.22em;color:rgba(236,230,218,.55)}

.hook{left:0;right:0;top:20%;text-align:center}
.hook .y{font:700 17cqw/1 var(--display);letter-spacing:.06em;color:#dff7ea;text-shadow:0 0 18px rgba(93,255,176,.45)}
.hook .s{margin-top:2.4cqw;font:italic 600 6cqw/1.1 var(--serif);color:#b9d9c9;letter-spacing:.04em}
.title{left:0;right:0;top:34%;text-align:center}
.title .brand{font:700 12.5cqw/1 var(--display);letter-spacing:.22em;padding-left:.22em;
  background:linear-gradient(180deg,#fff2c9,#f0c470 55%,#b98a3a);-webkit-background-clip:text;background-clip:text;color:transparent;
  filter:drop-shadow(0 0 14px rgba(240,196,112,.35))}
.title .rule{width:46%;height:1px;margin:4.5cqw auto;background:linear-gradient(90deg,transparent,var(--lux),transparent)}
.title .ch{font:600 3.3cqw/1 var(--sans);letter-spacing:.42em;padding-left:.42em;color:rgba(240,196,112,.85)}
.title .nm{margin-top:3cqw;font:italic 600 9cqw/1.05 var(--serif);color:#f3ecdd}
.cap{left:7%;top:7%}
.cap .a{font:600 2.9cqw/1.2 var(--sans);letter-spacing:.26em;color:#cfe3ee}
.cap .b{margin-top:1.2cqw;font:700 6.4cqw/1 var(--display);letter-spacing:.1em;color:#fff}
.cap .l{width:9cqw;height:1px;background:var(--lux);margin-top:2cqw}
.wishes{left:0;right:0;top:12%;padding:0 9%;display:flex;flex-direction:column;gap:3.2cqw;text-align:center;opacity:1;z-index:3}
.wish{font:italic 600 6.5cqw/1.18 var(--serif);color:#f6efe0;text-shadow:0 2px 14px rgba(0,0,0,.85);opacity:0}
.wish b{color:var(--lux);font-weight:600}
.endcard{left:0;right:0;top:20%;text-align:center;z-index:3}
.endcard .k{font:600 3cqw/1 var(--sans);letter-spacing:.42em;padding-left:.42em;color:rgba(240,196,112,.9)}
.endcard .n{margin-top:3.4cqw;font:700 7cqw/1 var(--display);letter-spacing:.16em;padding-left:.16em;color:#f6efe0;text-shadow:0 0 18px rgba(0,0,0,.8)}
.endcard .t{margin-top:2.2cqw;font:italic 600 11cqw/1 var(--serif);color:#fff;text-shadow:0 0 20px rgba(0,0,0,.85)}
.flash{inset:0;background:#fff;z-index:4}
#subs{position:absolute;left:0;right:0;bottom:11%;z-index:5;padding:0 7%;display:flex;flex-wrap:wrap;justify-content:center;gap:0 1.6cqw;
  font:700 5.6cqw/1.3 var(--sans);text-align:center;pointer-events:none;text-shadow:0 2px 10px #000,0 0 3px #000}
#subs .w{opacity:.45;color:#fff}
#subs .w.on{opacity:1}
#subs .w.cur{opacity:1;color:var(--lux)}

.start{position:absolute;left:50%;top:64%;z-index:8;transform:translate(-50%,-50%);display:flex;flex-direction:column;align-items:center;gap:10px;
  background:none;border:0;color:#fff;cursor:pointer;padding:8px;font:600 13px var(--sans);letter-spacing:.06em}
.start .disc{width:76px;height:76px;border-radius:50%;display:grid;place-items:center;background:rgba(10,13,17,.55);
  border:1.5px solid rgba(240,196,112,.85);box-shadow:0 0 28px rgba(240,196,112,.28);backdrop-filter:blur(3px)}
.start:hover .disc{background:rgba(240,196,112,.18)}
.start[hidden]{display:none}

.controls{display:flex;flex-direction:column;gap:8px}
.row{display:flex;align-items:center;gap:8px;flex-wrap:wrap}
.btn{min-height:40px;min-width:40px;padding:0 12px;border-radius:10px;background:var(--panel-2);color:var(--fg);border:1px solid var(--line);
  font:600 13px/1 var(--sans);cursor:pointer;display:inline-flex;align-items:center;justify-content:center;gap:6px}
.btn:hover{border-color:#41505f}
.btn:focus-visible,.start:focus-visible,#seek:focus-visible{outline:2px solid var(--lux);outline-offset:2px}
.btn.primary{background:var(--lux);color:#1a1306;border-color:var(--lux)}
.btn[aria-pressed="true"]{border-color:rgba(240,196,112,.7);background:rgba(240,196,112,.12);color:#ffe3a6}
.btn[aria-pressed="false"]{color:var(--muted)}
.time{font:600 13px var(--sans);font-variant-numeric:tabular-nums;color:var(--muted);margin-right:auto;padding-left:4px}
#seek{width:100%;margin:0;accent-color:var(--lux)}
.note{margin:0;color:var(--muted);font-size:12px;min-height:1.5em}
.note[hidden]{display:none}
```


**Tipografi kuralları:**
- `--display` (Cinzel): `FLATHEIM`, `BÖLÜM N`, büyük yıl/rakam, uyarı başlıkları.
- `--serif` (Cormorant Garamond **italik**): bölüm adı, dilek/söz cümleleri, alt başlıklar.
- `--sans` (Inter): altyazı, küçük etiketler, arayüz.
- Boyutlar `cqw` birimiyle (sahne genişliğinin %'si). Piksel kullanma.
- Büyük harf + geniş `letter-spacing` + `padding-left` aynı değer (optik ortalama).

---

## 5. ZAMAN MOTORU

### 5.1 Yardımcı fonksiyonlar (aynen kopyala)

```js
const D=90, POSTER=5.6;
const $=(s,r)=>(r||document).querySelector(s);
const NS='http://www.w3.org/2000/svg';
function el(tag,attrs,parent){const e=document.createElementNS(NS,tag);if(attrs)for(const k in attrs)e.setAttribute(k,attrs[k]);if(parent)parent.appendChild(e);return e;}
const clamp=(x,a=0,b=1)=>Math.min(b,Math.max(a,x));
const lerp=(a,b,t)=>a+(b-a)*t;
const sm=x=>{x=clamp(x);return x*x*(3-2*x);};
const ramp=(t,a,b)=>sm((t-a)/(b-a));
function rng(seed){let s=seed>>>0;return()=>{s=(s*1664525+1013904223)>>>0;return s/4294967296;};}
const f1=n=>n.toFixed(1);
```


| Fonksiyon | Ne yapar |
|---|---|
| `$` | `querySelector` kısayolu |
| `el(tag,attrs,parent)` | SVG öğesi oluştur + özelliklerini ata + ebeveyne ekle |
| `clamp(x,a,b)` | sınırla (varsayılan 0–1) |
| `lerp(a,b,t)` | doğrusal ara değer |
| `sm(x)` | smoothstep (0→1 yumuşak) |
| `ramp(t,a,b)` | `t` a→b aralığında 0'dan 1'e yumuşak geçiş. **Animasyonların temel taşı.** |
| `rng(seed)` | **tohumlu** sözde rastgele üreteç (her açılışta aynı sonuç) |
| `f1(n)` | 1 ondalıklı string (SVG attribute'u küçük tutar) |
| `wrap(x,lo,len)` | döngüsel konum (ekrandan çıkan figür karşıdan girer) |
| `lb(arr,x)` | sıralı dizide ikili arama (alt sınır) |

**Animasyon yazma reçetesi:** Her hareket `lt` (sahne yerel zamanı) ve `ramp/lerp/sin` ile ifade edilir. Örnekler:
- Yumuşak giriş: `opacity = ramp(t, a, a+0.9)`
- Giriş + çıkış: `ramp(t,a,b) * (1 - ramp(t,c,d))`
- Salınım (nefes): `Math.sin(t*1.25)*0.9`
- Yavaş kamera: `scale = 1 + 0.2*clamp(lt/15)`
- Döngüsel hareket: `wrap(x0 + v*lt, -60, 490)`

### 5.2 Sahne tablosu

```js
const scenes=[
 {id:'s1',a:0,b:9,f:0.01},{id:'s2',a:9,b:24,f:0.9},{id:'s3',a:24,b:40,f:1.0},
 {id:'s4',a:40,b:58,f:1.2},{id:'s5',a:58,b:68,f:1.0},{id:'s6',a:68,b:90,f:0.8}
];
scenes.forEach(s=>{s.g=$('#'+s.id);});
```


- `a`/`b`: sahnenin görünür başlangıç/bitiş saniyesi.
- `f`: **bu sahnenin giriş fade süresi** (`opacity = ramp(t, a, a+f)`). `f≈0.01` = anında (ilk sahne). Önceki sahne, bir sonrakinin girişi bitene kadar görünür kalır → çapraz geçiş.
- Sahneler **ardışık, boşluksuz** olmalı (`b` = sonrakinin `a`).
- Her `scenes[i].g` = `<g id="sN" class="scene">`.

### 5.3 Render döngüsü

```js
/* ===================== Çizim ===================== */
const timeEl=$('#time'),seek=$('#seek');
const fmt=x=>Math.floor(x/60)+':'+String(Math.floor(x%60)).padStart(2,'0');
const ANIMS=[animS1,animS2,animS3,animS4,animS5,animS6];
let seeking=false;
function render(t){
  scenes.forEach((s,i)=>{
    const nx=scenes[i+1];const vis=t>=s.a&&t<=s.b+(nx?nx.f:0)+0.001;
    s.g.style.display=vis?'':'none';
    if(vis)s.g.style.opacity=(s.f>0.05?ramp(t,s.a,s.a+s.f):1).toFixed(3);
  });
  scenes.forEach((s,i)=>{if(s.g.style.display!=='none')ANIMS[i](t);});
  overlays(t);renderSubs(t);
  timeEl.textContent=fmt(t)+' / 1:30';
  if(!seeking)seek.value=Math.round(t*10);
}
```


- Görünür olmayan sahne `display:none` olur (performans + sızıntı engeli).
- Sadece görünür sahnelerin `animSN` fonksiyonu çağrılır.
- `ANIMS` dizisi, `scenes` sırasıyla eşleşir.

### 5.4 Oynatma yaşam döngüsü

```js
/* ===================== Oynatma ===================== */
let tNow=POSTER,playing=false,started=false,ended=false,last=0,dirty=true;
const btnPlay=$('#btnPlay'),btnRestart=$('#btnRestart'),btnNarr=$('#btnNarr'),btnSnd=$('#btnSnd'),startBtn=$('#startBtn'),startLbl=$('#startLbl'),note=$('#note'),icoPlay=$('#icoPlay');
const ICO_PLAY='<path d="M8 5.2v13.6L19.2 12z" fill="currentColor"/>',ICO_PAUSE='<path d="M7 5h4v14H7zM13 5h4v14h-4z" fill="currentColor"/>';
function setIcon(){icoPlay.innerHTML=playing?ICO_PAUSE:ICO_PLAY;btnPlay.setAttribute('aria-label',playing?'Duraklat':'Oynat');}
function showNote(){
  if(!started){note.hidden=true;return;}
  if(narrOn&&!voice){note.hidden=false;note.textContent='Bu tarayıcıda Türkçe anlatıcı sesi bulunamadı. Altyazı, müzik ve efektler çalışmaya devam eder.';}
  else note.hidden=true;
}
function play(){
  if(!started&&Math.abs(tNow-POSTER)<0.001)tNow=0;
  if(ended||tNow>=D-0.01){tNow=0;ended=false;}
  started=true;
  initAudio();if(ctx&&ctx.state==='suspended')ctx.resume();
  try{speechSynthesis.speak(new SpeechSynthesisUtterance(''));}catch(e){}
  pickVoice();resync(tNow);
  playing=true;last=performance.now();startBtn.hidden=true;setIcon();showNote();dirty=true;
}
function pause(){playing=false;silence();setIcon();}
function finish(){playing=false;ended=true;silence();setIcon();startLbl.textContent='Tekrar izle';startBtn.hidden=false;}
btnPlay.addEventListener('click',()=>{playing?pause():play();});
startBtn.addEventListener('click',play);
btnRestart.addEventListener('click',()=>{tNow=0;ended=false;started=true;resync(0);dirty=true;if(!playing)play();});
seek.addEventListener('input',()=>{seeking=true;startBtn.hidden=true;tNow=seek.value/10;ended=false;resync(tNow);cancelSpeech();dirty=true;});
seek.addEventListener('change',()=>{seeking=false;});
btnNarr.addEventListener('click',()=>{narrOn=!narrOn;btnNarr.setAttribute('aria-pressed',String(narrOn));if(!narrOn)cancelSpeech();showNote();});
btnSnd.addEventListener('click',()=>{sndOn=!sndOn;btnSnd.setAttribute('aria-pressed',String(sndOn));if(!sndOn)silence();});
document.addEventListener('visibilitychange',()=>{if(document.hidden&&playing)pause();});
function frame(now){
  if(playing){
    const dt=Math.min(0.12,(now-last)/1000);last=now;tNow+=dt;
    if(tNow>=D){tNow=D;tick(tNow);finish();}else tick(tNow);
    dirty=true;
  }
  if(dirty){render(tNow);dirty=false;}
  requestAnimationFrame(frame);
}
setIcon();render(POSTER);requestAnimationFrame(frame);
})();
```


Önemli noktalar:
- `POSTER`: dosya açıldığında gösterilen kare. İlk "Başlat"ta `tNow=0`'a döner.
- Autoplay **yok** (ses kısıtı). `play()` içinde `AudioContext` oluşturulur/`resume` edilir.
- `speechSynthesis.speak(new SpeechSynthesisUtterance(''))` ile ses motoru "ısıtılır".
- Sekme gizlenince `pause()`.
- `dt` 0.12 sn ile sınırlı (sekme geri gelince atlama olmasın).

---

## 6. SAHNE YAZMA KILAVUZU

### 6.1 Sahne üç parçadan oluşur
1. **Statik SVG** (`<g id="sN" class="scene">…</g>`): sabit şekiller, gradyanlar, id verilmiş dinamik öğeler.
2. **Kurulum (IIFE/üst düzey)**: tohumlu `rng` ile parçacık, yıldız, pencere, ot gibi çok sayıdaki öğeyi `el()` ile üretir.
3. **`animSN(t)`**: her karede çağrılır, **sadece attribute günceller** (`transform`, `opacity`, `d`, `cx/cy`, `r`).

### 6.2 Kamera
Kamera, sahne içeriğini saran grubun `transform`'udur (`<g id="sNcam">`). Sabit desen:
```js
cam.setAttribute('transform','translate(CX CY) scale('+sc.toFixed(4)+') translate(-CX -CY)');
```
- `CX,CY` = yakınlaşmanın odak noktası (ilgi çeken yer).
- `sc = 1 + k*clamp(lt/süre)`; Bölüm 1 değerleri: hastane 1→1.2 (15 sn), şehir 1→1.07 (16 sn), monitör 1→1.05 (10 sn), ahır `1.05 ± 0.012·sin`.
- İsteğe bağlı hafif döndürme: `rotate(-3 + 1.4·sin(lt·0.5))` (ahır, "başım dönüyor" hissi).
- Sabit **çerçeve** (örn. pencere kasası `s3frame`) kamera grubunun **dışında** durur → içerik hareket eder, çerçeve sabit kalır.
- Bulanıklık: `filter` + `feGaussianBlur stdDeviation` animasyonu (`4 → 0`), netleşme hissi.

### 6.3 Sahne geçiş yöntemleri
| Yöntem | Bölüm 1'de nerede |
|---|---|
| **Çapraz solma** (`f` ile) | S2→S3→S4→S5 |
| **Anında kesme** (`f≈0.01`) | S1 açılış |
| **Beyaz flash** (`#flash`) | ölümden sonra 65.8–69.0 |
| **Göz kapağı açılması + dünya fade-in + blur** | S6 (göz → ahır) |
| **Karartma** (`#s6dark` / `s3dim`) | zaman geçişi, uyku/ölüm |

### 6.4 Parçacık/doku reçeteleri (hepsi tohumlu)
- **Toz/ışık zerreleri:** 22–26 küçük daire; `cx = bx + 12·sin(t·0.3+ph)`, `cy = by + 9·cos(t·0.25+ph·1.3)`, `opacity = 0.15 + 0.5·sin²(t·0.7+ph)`.
- **Yıldız:** 80 daire; `opacity = 0.35 + 0.65·|sin(t·sp+ph)|·ramp(lt,0,3)`.
- **Aurora:** iki yol, `y = base + amp·sin(x·0.018 + t·sp + ph)`, 40px adımlı çokgen; gradyan dolgu.
- **Işık zerreleri (motes):** `wrap(y0 − v·lt)` ile yukarı süzülür, glow filtreli, `#ffd98a`/`#cbbcff`.
- **Pencere ışıkları:** her pencere için rastgele eşik `th`; `ramp(lt, th−1, th+1)` ile sırayla yanar; bir kısmı (`base`) hep yanık.
- **Hay/ot:** 110 kavisli çizgi (`Q` eğri), 4 renk tonu, farklı kalınlık/opaklık.
- **Tahta/ahşap:** dikey çizgiler (`stroke`), aralara açık mavi ışık şeritleri (`opacity .42`).

### 6.5 Işık huzmeleri
Yarı saydam poligon + gradyan (`gBeam`/`gBeam2`); `opacity = 0.85 + 0.15·sin(t·0.5)` ile hafif titrer. Işık kaynağı penceredir; huzme kaynaktan zemine genişleyerek iner.

---

## 7. SVG ÇİZİM DİLİ (referans)

### 7.1 `<defs>` (Bölüm 1 gradyan/filtre/clipPath kütüphanesi)
Yeni bölümde yalnızca gerekenler alınır; mevcut `id`'ler **aynı adlarla** korunur (tutarlılık + kopyala-yapıştır kolaylığı).

```xml
        <linearGradient id="gWall" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#1c2b35"/><stop offset="1" stop-color="#0c1319"/></linearGradient>
        <linearGradient id="gBeam" x1="1" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#dff1ff" stop-opacity=".34"/><stop offset="1" stop-color="#dff1ff" stop-opacity="0"/></linearGradient>
        <linearGradient id="gWin" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#d7ecf8"/><stop offset="1" stop-color="#7399ae"/></linearGradient>
        <linearGradient id="gGreen" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#2dd48a" stop-opacity="0"/><stop offset=".5" stop-color="#2dd48a" stop-opacity=".16"/><stop offset="1" stop-color="#2dd48a" stop-opacity="0"/></linearGradient>
        <linearGradient id="gSunset" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#4b3f7a"/><stop offset=".38" stop-color="#c8607a"/><stop offset=".68" stop-color="#f2a171"/><stop offset="1" stop-color="#ffd59a"/></linearGradient>
        <radialGradient id="gSun"><stop offset="0" stop-color="#fff3c8"/><stop offset=".35" stop-color="#ffd08a" stop-opacity=".9"/><stop offset="1" stop-color="#ff9d6c" stop-opacity="0"/></radialGradient>
        <linearGradient id="gNight" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#070a1d"/><stop offset=".55" stop-color="#1a1840"/><stop offset="1" stop-color="#3b2a5e"/></linearGradient>
        <radialGradient id="gMoon"><stop offset="0" stop-color="#fffbe9"/><stop offset=".7" stop-color="#f3e6bd"/><stop offset="1" stop-color="#d9c690"/></radialGradient>
        <radialGradient id="gMoonGlow"><stop offset="0" stop-color="#fff1c2" stop-opacity=".5"/><stop offset="1" stop-color="#fff1c2" stop-opacity="0"/></radialGradient>
        <linearGradient id="gAur1" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#5ef2c6" stop-opacity=".0"/><stop offset=".6" stop-color="#5ef2c6" stop-opacity=".22"/><stop offset="1" stop-color="#7a6ae0" stop-opacity="0"/></linearGradient>
        <linearGradient id="gAur2" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#a98bff" stop-opacity="0"/><stop offset=".6" stop-color="#a98bff" stop-opacity=".2"/><stop offset="1" stop-color="#5ef2c6" stop-opacity="0"/></linearGradient>
        <radialGradient id="gMon" cx=".5" cy=".5" r=".5"><stop offset="0" stop-color="#2dd48a" stop-opacity=".28"/><stop offset="1" stop-color="#2dd48a" stop-opacity="0"/></radialGradient>
        <radialGradient id="gSkin" cx=".42" cy=".5" r=".6"><stop offset="0" stop-color="#6a4a3c"/><stop offset=".6" stop-color="#3a281f"/><stop offset="1" stop-color="#150d0a"/></radialGradient>
        <radialGradient id="gSclera" cx=".5" cy=".5" r=".6"><stop offset="0" stop-color="#efe6da"/><stop offset=".8" stop-color="#cdbfb0"/><stop offset="1" stop-color="#8e7d70"/></radialGradient>
        <radialGradient id="gIris" cx=".5" cy=".5" r=".5"><stop offset="0" stop-color="#b3783f"/><stop offset=".55" stop-color="#6d4220"/><stop offset="1" stop-color="#2b160a"/></radialGradient>
        <linearGradient id="gBarn" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#0d0907"/><stop offset=".45" stop-color="#2c2015"/><stop offset="1" stop-color="#110b07"/></linearGradient>
        <linearGradient id="gBeam2" x1="0" y1="0" x2="1" y2="1"><stop offset="0" stop-color="#a9cdee" stop-opacity=".28"/><stop offset="1" stop-color="#a9cdee" stop-opacity="0"/></linearGradient>
        <filter id="glow" x="-20%" y="-20%" width="140%" height="140%"><feGaussianBlur stdDeviation="2.2" result="b"/><feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge></filter>
        <filter id="glowSm" x="-60%" y="-60%" width="220%" height="220%"><feGaussianBlur stdDeviation="1.6" result="b"/><feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge></filter>
        <filter id="fBarnBlur" x="-5%" y="-5%" width="110%" height="110%"><feGaussianBlur id="barnBlurStd" stdDeviation="0"/></filter>
        <clipPath id="eyeClip"><path d="M60,300 C110,235 250,235 300,300 C250,365 110,365 60,300 Z"/></clipPath>
        <clipPath id="s2scr"><rect x="98" y="218" width="96" height="54"/></clipPath>
        <clipPath id="s5scr"><rect x="38" y="164" width="284" height="200" rx="6"/></clipPath>
```


**Gradyan sözlüğü**
| id | Kullanım |
|---|---|
| `gWall` | hastane duvarı (`#1c2b35 → #0c1319`) |
| `gBeam`, `gBeam2` | pencere ışık huzmeleri (açık mavi, şeffaflaşır) |
| `gWin` | pencere camı |
| `gGreen` | monitör yeşil parıltı bandı |
| `gSunset`, `gSun` | gün batımı gökyüzü, güneş hâlesi |
| `gNight`, `gMoon`, `gMoonGlow` | gece, ay, ay parıltısı |
| `gAur1`, `gAur2` | aurora şeritleri |
| `gMon` | monitör etrafı yeşil hâle |
| `gSkin`, `gSclera`, `gIris` | göz yakın planı |
| `gBarn` | ahır karanlık iç mekân |

**Filtreler:** `glow` (iri, stdDev 2.2), `glowSm` (küçük, 1.6 — gözler, ECG, küçük ışıklar), `fBarnBlur` (animasyonlu bulanıklık).

### 7.2 Koordinat ve ölçek
- Tüm çizim **360×640** kullanıcı uzayında. Orta çizgi `x=180`.
- Zemin/ufuk çizgileri genelde `y≈462–478` (şehir), `y≈468–478` (hastane yer), ahır tabanı `y≈570–590`.
- **İnsan figürü** yüksekliği ~60–75 birim (ölçek `sc` ile 0.8–1.15).
- Büyük zemin dikdörtgenleri ekran dışına taşar (`x=-90, width=540`), kamera zoom'unda boşluk görünmesin.

### 7.3 Çizim üslubu
- **Düz dolgu + gradyan + opaklık** kullan. Kontur (`stroke`) yalnızca ince detay/çerçeve için.
- Gölgeler ayrı yarı saydam şekillerle (`opacity .3–.6`) verilir.
- Silüetler **`#05080b`** (hemen hemen siyah). Saf `#000` yalnız tam karartma için.
- Yuvarlatma: `rx` ile köşeler yumuşatılır (`rx 4–8`).
- **Parlama:** göz, monitör çizgisi, kristal, sihirli ışık = `filter="url(#glowSm)"`.

---

## 8. KARAKTER FABRİKALARI (birebir kod)

### 8.1 Ana karakter, arkadan siluet + kenar ışığı

```js
function mkBoyBack(parent,rim){
  const g0=el('g',{},parent);
  el('path',{d:'M6,640 C16,590 58,556 98,556 C138,556 178,590 190,640 Z',fill:'#05080b'},g0);
  el('rect',{x:80,y:530,width:34,height:34,rx:10,fill:'#05080b'},g0);
  el('ellipse',{cx:96,cy:504,rx:40,ry:48,fill:'#05080b'},g0);
  el('path',{d:'M56,492 C56,458 78,450 98,450 C120,450 136,466 136,492 C120,484 76,484 56,492 Z',fill:'#05080b'},g0);
  el('path',{d:'M135,478 C142,500 138,530 124,548',fill:'none',stroke:rim,'stroke-width':2.2,'stroke-linecap':'round',opacity:.75},g0);
  el('path',{d:'M150,584 C168,598 180,616 186,636',fill:'none',stroke:rim,'stroke-width':2,'stroke-linecap':'round',opacity:.5},g0);
  return g0;
}
const s3boy=mkBoyBack($('#s3boy'),'#ffb078');
const s4boy=mkBoyBack($('#s4boy'),'#a8bcff');
```


- `rim` parametresi ortam ışığının rengidir: gün batımı `#ffb078`, ay ışığı `#a8bcff`. **Yeni sahnelerde sahnenin ana ışık rengi verilir** (ahır: soğuk mavi `#9cc3e8`, gün ışığı: `#ffe3a6`, büyü: `#cbbcff`, vb.).
- Yerleşim: figür sol altta (`x≈6–190`, `y≈450–640`), kamera boşluğu sağa/üste bırakır → olay/manzara görüntünün geri kalanında görünür.
- Nefes: `translate(0, sin(t·1.1)·1.2)`.

### 8.2 Ana karakter, hastane (yan profil, yatakta)
Bölüm 1'de `s2body` grubu (çizim): gövde `#9fb7c6` hastane gömleği, boyun/yüz `#e6cdbb`, kulak `#d9bfae`, bere `#4d6372` + şerit `#3b4e5b`, göz `ellipse` (`#1b1411`), burun/ağız ince `stroke`.

```xml
          <g id="s2body">
            <path d="M96,432 C92,398 98,372 118,366 L150,366 C166,376 170,402 168,432 Z" fill="#9fb7c6"/>
            <path d="M118,366 L134,382 L150,366" fill="none" stroke="#7e98a9" stroke-width="2"/>
            <rect x="120" y="352" width="18" height="20" rx="6" fill="#d9bfae"/>
            <g id="s2head">
              <ellipse cx="131" cy="336" rx="23" ry="26" fill="#e6cdbb"/>
              <ellipse cx="111" cy="342" rx="4" ry="6" fill="#d9bfae"/>
              <path d="M107,332 C106,306 120,300 133,300 C148,300 156,312 155,328 C146,322 120,322 107,332 Z" fill="#4d6372"/>
              <path d="M106,330 C120,322 146,320 155,327 L155,335 C146,329 120,330 106,337 Z" fill="#3b4e5b"/>
              <path d="M134,336 q6,-3 11,0" stroke="#7c5c4b" fill="none" stroke-width="1.4" stroke-linecap="round"/>
              <g id="s2eye" transform="translate(139 341)"><ellipse rx="3.4" ry="2.3" fill="#1b1411"/></g>
              <path d="M153,341 q4,4 -1,7" stroke="#c4a591" fill="none" stroke-width="1.4" stroke-linecap="round"/>
              <path d="M141,353 q5,2 9,0" stroke="#a97f72" fill="none" stroke-width="1.5" stroke-linecap="round"/>
            </g>
            <path d="M150,382 C160,406 166,422 178,434" stroke="#a9c0cf" stroke-width="12" stroke-linecap="round" fill="none"/>
            <ellipse cx="184" cy="438" rx="9" ry="6" fill="#e6cdbb"/>
          </g>
```


Animasyon (`animS2`): hafif nefes `translate`, baş `rotate` (5° / 6–12 sn), göz kırpma (`scaleY`, her ~4.1 sn, 0.2 sn süreli).

### 8.3 Figüran insanlar (yürüyen/duran halk)

```js
function mkPerson(parent,o){
  const g0=el('g',{},parent);
  const L1=el('g',{},g0),L2=el('g',{},g0);
  el('line',{x1:0,y1:-14,x2:0,y2:0,stroke:'#1d1730','stroke-width':3.6,'stroke-linecap':'round'},L1);
  el('line',{x1:0,y1:-14,x2:0,y2:0,stroke:'#2a2242','stroke-width':3.6,'stroke-linecap':'round'},L2);
  el('rect',{x:-6,y:-31,width:12,height:18,rx:4,fill:o.c},g0);
  const A1=el('g',{},g0),A2=el('g',{},g0);
  el('line',{x1:0,y1:-29,x2:0,y2:-14,stroke:o.c,'stroke-width':2.8,'stroke-linecap':'round',opacity:.9},A1);
  el('line',{x1:0,y1:-29,x2:0,y2:-14,stroke:o.c,'stroke-width':2.8,'stroke-linecap':'round'},A2);
  el('circle',{cx:0,cy:-38,r:6,fill:o.s},g0);
  el('path',{d:'M-6.2,-38 A6.2,6.2 0 0 1 6.2,-38 Z',fill:o.h},g0);
  if(o.bag)el('rect',{x:3,y:-20,width:8,height:10,rx:2,fill:'#8a4b2a'},g0);
  if(o.kid){
    el('path',{d:'M5,-27 C7,-44 9,-54 10,-62',fill:'none',stroke:'#e9dfcf','stroke-width':.8},g0);
    el('ellipse',{cx:10,cy:-72,rx:9,ry:11,fill:'#e34b4b'},g0);el('ellipse',{cx:7,cy:-75,rx:2.4,ry:3.6,fill:'#fff',opacity:.5},g0);
  }
  return {g:g0,L1:L1,L2:L2,A1:A1,A2:A2,o:o};
}
const PEOPLE=[
 {x:30,v:14,c:'#c9503e',s:'#e6c3a1',h:'#2a1b14',sc:1.15,ph:0},
 {x:210,v:-18,c:'#3f7a9c',s:'#d9a98a',h:'#111',sc:1.1,ph:1.3},
 {x:300,v:11,c:'#e0b04c',s:'#f0d2b4',h:'#6a3a1a',sc:1.0,ph:2.1,bag:1},
 {x:104,v:0,c:'#6e5bb0',s:'#e6c3a1',h:'#1a1a1a',sc:1.15,ph:0,stand:1,face:1},
 {x:134,v:0,c:'#58a37a',s:'#c99373',h:'#3b2a1d',sc:1.1,ph:.5,stand:1,face:-1},
 {x:-40,v:62,kid:1,c:'#f0c14b',s:'#f0d2b4',h:'#3a2a1a',sc:0.8,ph:.7}
].map(o=>mkPerson($('#s3people'),o));
```


- **Bacaklar** `L1/L2` ve **kollar** `A1/A2` ayrı grup; `rotate(sw)` ile ters fazda sallanır.
- Yürüyüş: `sw = sin(lt·rate + ph)·28`, `rate = 3.2 + |v|·0.11`, zıplama `bob = |sin|·−1.4`.
- Yön: `scale(sc·dir, sc)` ile aynalanır (`dir = v≥0 ? 1 : −1`).
- Duran grup (konuşanlar): `stand:1`, `face:±1`, kol hareketi `sin(lt·3+ph)·26`.
- Çocuk: `kid:1` → balon (`#e34b4b` elips + ip + parlak nokta).
- Paletler: gömlek `#c9503e, #3f7a9c, #e0b04c, #6e5bb0, #58a37a, #f0c14b`; ten `#e6c3a1, #d9a98a, #f0d2b4, #c99373`; saç `#2a1b14, #111, #6a3a1a, #1a1a1a, #3b2a1d`.

### 8.4 Araçlar

```js
function mkCar(parent,o){
  const g0=el('g',{},parent);
  el('polygon',{points:'26,-10 78,-20 78,0',fill:'#ffe9a0',opacity:.1},g0);
  el('rect',{x:-26,y:-17,width:52,height:12,rx:4,fill:o.c},g0);
  el('path',{d:'M-14,-17 L-9,-26 H11 L17,-17 Z',fill:o.c},g0);
  el('path',{d:'M-10,-18 L-7,-24 H-1 V-18 Z M1,-18 V-24 H9 L13,-18 Z',fill:'#d6e6f2',opacity:.6},g0);
  el('circle',{cx:-15,cy:-4,r:5,fill:'#14121c'},g0);el('circle',{cx:15,cy:-4,r:5,fill:'#14121c'},g0);
  el('circle',{cx:25,cy:-11,r:2.4,fill:'#ffe9a0'},g0);
  return {g:g0,o:o};
}
const CARS=[{x:50,v:58,y:520,c:'#c94a4a',sc:1},{x:270,v:-72,y:506,c:'#4a7fc9',sc:.9},{x:380,v:48,y:520,c:'#d9b84a',sc:1}].map(o=>mkCar($('#s3cars'),o));
```


### 8.5 Yaratıklar: karanlıkta parlayan gözler

```js
const creatures=[];
function mkCreature(x,y,body,eyes,color,ph){
  const g0=el('g',{transform:'translate('+x+' '+y+')'},$('#s6creatures'));
  el('path',{d:body,fill:'#050403'},g0);
  const eg=el('g',{filter:'url(#glowSm)'},g0);
  const es=eyes.map(p=>({e:el('ellipse',{rx:p[2],ry:p[3],fill:color},eg),p:p}));
  creatures.push({g:g0,es:es,x:x,y:y,ph:ph});
}
mkCreature(95,330,'M-34,70 C-38,20 -30,-20 -14,-48 L-18,-72 L-6,-52 L0,-84 L6,-52 C10,-54 14,-52 16,-50 L22,-74 L24,-46 C40,-20 44,30 38,70 Z',[[-12,-26,5,3],[16,-26,5,3],[2,-40,3,2]],'#ffb347',0);
mkCreature(250,312,'M-30,110 C-34,40 -26,-10 -10,-40 L-14,-66 L0,-46 C12,-60 30,-52 40,-30 L66,-22 L40,-8 C44,30 40,70 34,110 Z',[[-4,-24,4,2.6],[14,-24,4,2.6],[-2,-10,3,2],[16,-8,3,2]],'#c8ff6b',1.7);
mkCreature(322,238,'M-14,44 C-16,10 -10,-10 0,-20 C10,-10 16,10 14,44 Z',[[-4,-6,2.4,1.6],[5,-6,2.4,1.6]],'#ff6b5a',3.1);
mkCreature(192,300,'M-12,40 C-14,14 -8,-6 0,-12 C8,-6 14,14 12,40 Z',[[-5,0,2.6,1.6],[6,0,2.6,1.6]],'#b68cff',4.4);
```


- Gövde: **`#050403`** (neredeyse siyah) siluet yolu; **kimliği gözler verir**.
- Göz renkleri: turuncu `#ffb347`, limon `#c8ff6b`, kırmızı `#ff6b5a`, mor `#b68cff`. Her yaratığa **farklı renk**.
- Göz sayısı/şekli yaratığa göre değişir (2, 3, 4 göz), böylece "alışılmadık" hissi.
- Yaratıklar `vis = ramp(lt, 12.4+ci·0.6, 14.2+ci·0.6)` ile **sırayla** belirir, yüzme `sin(lt·0.9+ph)·2.2`, göz kırpma `scaleY` 4.6 sn döngü.
- **Yeni yaratık eklemek:** `mkCreature(x, y, 'bodyPath', [[gx,gy,rx,ry],…], '#renk', faz)`. Gövde yolu orijin (0,0) etrafında çizilir.

### 8.6 Ejderha (siluet)

```js
function mkDragon(parent){
  const g0=el('g',{},parent);
  const wb=el('g',{},g0);el('path',{d:'M-6,-6 C-30,-60 -10,-96 40,-118 C32,-96 40,-86 28,-74 C36,-64 24,-56 16,-48 C14,-30 4,-14 -6,-6 Z',fill:'#06070e',opacity:.9},wb);
  el('path',{d:'M-118,22 C-96,14 -70,6 -48,2 C-30,-6 0,-12 24,-8 C40,-4 52,-2 62,-6 L74,-14 L70,-4 L88,-2 L72,4 L66,10 C52,12 34,10 16,12 C-6,16 -30,12 -54,16 C-80,22 -100,34 -118,22 Z',fill:'#05060c'},g0);
  el('path',{d:'M60,-6 L54,-22 L68,-10 Z',fill:'#05060c'},g0);
  el('path',{d:'M-100,18 L-92,6 L-86,18 M-70,10 L-62,-2 L-56,10',fill:'#05060c'},g0);
  el('circle',{cx:70,cy:-3,r:1.8,fill:'#ffd27a',filter:'url(#glowSm)'},g0);
  const wf=el('g',{},g0);el('path',{d:'M4,-8 C-24,-70 6,-110 70,-132 C58,-108 66,-96 52,-84 C62,-72 46,-62 38,-52 C34,-30 18,-14 4,-8 Z',fill:'#07080f'},wf);
  return {g:g0,wf:wf,wb:wb};
}
const dr1=mkDragon($('#s4dragon1')),dr2=mkDragon($('#s4dragon2'));
function flap(d,ph){const a=10*Math.sin(ph),s=0.62+0.38*Math.cos(ph);d.wf.setAttribute('transform','translate(4 -8) rotate('+f1(a)+') scale(1 '+s.toFixed(3)+') translate(-4 8)');d.wb.setAttribute('transform','translate(-6 -6) rotate('+f1(a*0.8-6)+') scale(1 '+(s*0.9).toFixed(3)+') translate(6 6)');}
```


- Ejderha **tamamen siyah siluet**; kimliği gökyüzü/ay ışığına karşı konturla verilir.
- Büyük ejderha: `scale(−0.95 0.95)` (sola uçar), yatay hız `lerp(470,−170,…)` (~14 sn), dikey salınım `16·sin(lt·0.9)`.
- Uzak küçük ejderha: `scale(0.42)`, `opacity .85`, farklı hız/yön.
- **Ejderha her zaman "büyük, nadir, görkemli"**: ekranda uzun süre yavaş süzülür; aceleci/komik hareket yok.

### 8.7 Göz yakın planı (uyanış tekniği)

```xml
        <g id="s6eye">
          <rect width="360" height="640" fill="#0a0605"/>
          <ellipse cx="180" cy="300" rx="215" ry="175" fill="url(#gSkin)"/>
          <path d="M26,214 C100,166 250,158 340,200" fill="none" stroke="#140c09" stroke-width="18" stroke-linecap="round" opacity=".9"/>
          <g clip-path="url(#eyeClip)">
            <ellipse cx="180" cy="300" rx="130" ry="72" fill="url(#gSclera)"/>
            <g id="s6iris" transform="translate(180 300)">
              <circle r="54" fill="url(#gIris)"/><circle r="53" fill="none" stroke="#120a05" stroke-width="5"/>
              <circle id="s6pupil" r="14" fill="#030201"/>
              <circle cx="-15" cy="-17" r="8" fill="#fff" opacity=".85"/><circle cx="16" cy="14" r="4" fill="#fff" opacity=".4"/>
            </g>
            <path id="s6lid" fill="#3c2a23"/>
          </g>
          <path id="s6lidline" fill="none" stroke="#0e0706" stroke-width="4" stroke-linecap="round"/>
          <path d="M60,300 C110,365 250,365 300,300" fill="none" stroke="#120a07" stroke-width="2.6" opacity=".85"/>
          <path id="s6crease" fill="none" stroke="#180e0a" stroke-width="3" stroke-linecap="round" opacity="0"/>
          <rect id="s6dark" width="360" height="640" fill="#000" opacity="1"/>
        </g>
```

```js
const s6eye=$('#s6eye'),s6barn=$('#s6barn'),s6lid=$('#s6lid'),s6lidline=$('#s6lidline'),s6crease=$('#s6crease'),s6iris=$('#s6iris'),s6pupil=$('#s6pupil'),s6dark=$('#s6dark'),s6cam=$('#s6barnCam'),blurStd=$('#barnBlurStd');
const eyeKeys=[[0,0],[1.6,0],[2.4,0.15],[3.4,0.05],[4.4,0.3],[5.2,0.12],[6.2,0.6],[7.2,0.93],[9.5,0.9]];
function openAt(lt){if(lt<=eyeKeys[0][0])return eyeKeys[0][1];for(let i=1;i<eyeKeys.length;i++){if(lt<=eyeKeys[i][0]){const a=eyeKeys[i-1],b=eyeKeys[i];return lerp(a[1],b[1],sm((lt-a[0])/(b[0]-a[0])));}}return eyeKeys[eyeKeys.length-1][1];}
```


- Göz şekli `clipPath#eyeClip` ile kesilir; sklera + iris + gözbebeği + parlama noktaları.
- **Üst göz kapağı** `s6lid` yolu her karede `y1` ile yeniden yazılır (kapalı `365` → açık `235`).
- `eyeKeys` = `[yerel_zaman, açıklık]` anahtar kareleri; `openAt(lt)` aralarda `sm` ile geçer. **Doğal açılış için duraksamalı/titrek dizilim** (0→0.15→0.05→0.3→0.12→0.6→0.93).
- `s6dark` karartma, `s6pupil` yarıçapı (11→22) ve iris bakışı (`look`) ile uyanış hissi.
- Göz açıldıktan sonra `kb = ramp(lt,7.6,9.6)` ile **göz grubu sönerken yeni dünya (ahır) fade-in** olur.

---

## 9. BÖLÜM 1 SAHNE DÖKÜMÜ (referans akış)

| # | Zaman | Sahne | İçerik | Kamera/Işık | Ses |
|---|---|---|---|---|---|
| S1 | 0–9 | Monitör, başlık | siyah zemin, yeşil bant, ızgara, **tam genişlik ECG çizgisi** + parlayan nokta. Kanca ("2026 / son gün") 0.2–3.6, **başlık kartı** 3.3–9 | ECG 3.3–4.6'da alta iner ve sönümlenir | bip (0.10), drone |
| S2 | 9–24 | Hastane odası | pencere + şehir silueti, duvar saati (ibreler hızlı döner), monitör (ECG), yatak, hasta çocuk, serum damlası, toz. Köşe başlığı "HASTANE ODASI / 2026" | yavaş zoom 1→1.2, huzme titrer | arpej başlar (t=9), bip (0.05) |
| S3 | 24–40 | Pencereden şehir | gün batımı, güneş iner (342→380), yürüyen insanlar, araçlar, bina pencereleri sırayla yanar, sokak lambası, **pencere çerçevesi sabit**, çocuk arkadan siluet | zoom 1→1.07, `s3dim` 0→0.42 | arpej, bip (0.035) |
| S4 | 40–58 | Dilek | gece, 80 yıldız, aurora, dolunay, **yüzen ada + kristal**, iki ejderha, dağ katmanları, ışık zerreleri, çocuk siluet (mavi rim), **dilek cümleleri** (44.8/48/51.4/54.2) | çerçeve 1→0.12 solar (8–14 sn) | `pad` katmanı, arpej (+0.02) |
| S5 | 58–68 | Son nefes | monitör yakın plan: ECG, **BPM yavaşlar**, SpO2 düşer, **t>64.6 düz çizgi**, kırmızıya döner, el + kâğıt | zoom 1→1.05 | 4 ek bip (58.7–63.6, aralık açılır), `flat` 1000 Hz (64.55–67.5) |
| – | 65.8–69.0 | **Beyaz flash** | ölümden yeni dünyaya geçiş | | |
| S6 | 68–90 | Yeni dünya | göz kapağı açılır (68→77), ahır (75.6+), huzme, toz, hay, **4 yaratık gözleri sırayla** (80.4+), **bitiş kartı** 86.4–88 | blur 4→0, `rotate` sallanma | rüzgâr (68.4–73), **thump** kalp atışı 69.6→90 (hızlanır/yükselir), `endp` ped |

**Dramaturji örüntüsü:** *Kurma (kapalı/küçük) → dünya genişler (pencere) → duygusal doruk (dilek) → sessizlik/ölüm → yeni dünyaya geçiş.* Gelecek bölümler bu eğriyi **taklit etmek zorunda değil**, ama **kurma → gelişme → doruk → sakinleşme/geçiş** ritmi korunur.

---

## 10. ANLATIM (CUES) + EKRAN YAZILARI

### 10.1 Cue formatı ve Bölüm 1 örneği

```js
const CUES=[
 [0.5,3.6,"Ölmeden önce, tek bir dileği vardı."],
 [4.4,8.4,"Hiçbir şeyi gerçekten yaşayamamış bir çocuk."],
 [9.6,14.0,"Kanser, onun zamanını, özgürlüğünü ve geleceğini çoktan elinden almıştı."],
 [14.6,19.0,"Doktorlar artık yapılabilecek şeylerin sınırlı olduğunu söylüyordu."],
 [19.6,23.6,"Çocuk yatağında, sessizce bekliyordu."],
 [24.5,28.8,"Pencerenin ardında hayat akıp gidiyordu."],
 [29.4,34.6,"İnsanlar alışveriş yapıyor, çocuklar koşuyor, arkadaşlar gülüyordu."],
 [35.2,39.6,"Başkalarının sıradan saydığı her şey, onun için bir hayaldi."],
 [40.5,44.4,"Öfkelenmedi. Sadece hiç yaşayamadığına üzüldü."],
 [44.8,47.8,"Ve içinden bir dilek geçirdi."],
 [48.0,53.6,"Ejderhaların, büyünün ve bilinmeyen dünyaların olduğu bir yerde yaşamak istedi."],
 [54.0,57.8,"Keşke geleceği olsaydı."],
 [58.6,62.6,"Gözlerini kapattı. Monitörün sesi yavaşladı."],
 [63.6,67.4,"Sonra tamamen kesildi. Oda sessizliğe gömüldü."],
 [70.2,73.8,"Kısa süre sonra, gözleri yeniden açıldı."],
 [76.4,81.0,"Bu kez hastane odasında değildi. Karanlık, kötü kokan bir yerdeydi."],
 [81.6,85.2,"Etrafında, hiç görmediği yaratıkların gözleri parlıyordu."],
 [85.6,89.6,"Bu kez, hayatını kendisi yaşayacaktı."]
];
```


**Yazım kuralları**
- **Üçüncü şahıs, geçmiş zaman**, sade, duygusal. ("…vardı", "…yaşamak istedi", "…yeniden açıldı.")
- Cue başına **3–5 sn**, genelde **8–14 kelime**. Cue'lar arası **0.4–1 sn** boşluk; **bilinçli büyük sessizlikler** (Bölüm 1'de 67.4–70.2) dramatiktir.
- İlk cue **0.5–1 sn** civarında başlar; son cue **≤ 89.6**'da biter.
- Bir cue = bir fikir. Zincirleme uzun cümleler yok.
- Konuşma hızı: 2.4 kelime/sn (kod bunu `rate` ile hesaplar). Cue süresi **kelime sayısı / 2.4**'ten çok kısa olmasın.
- Türkçe özel karakterler doğru yazılır (ı, İ, ş, ğ, ü, ö, ç). Tırnak/uzun tire kullanma, anlatıcı sesi bozulur.
- Dünya bilgisi **anlatıcıyla** değil mümkünse **görüntüyle** verilir; anlatıcı duyguyu ve geçişi taşır.

### 10.2 Overlay zamanlama ve kod

```js
const hook=$('#hook'),titleCard=$('#titleCard'),cap=$('#cap'),wishes=$('#wishes'),wishEls=Array.prototype.slice.call(wishes.children),flash=$('#flash'),endcard=$('#endcard'),bar=$('#bar'),subs=$('#subs');
const WT=[44.8,48.0,51.4,54.2];
function overlays(t){
  hook.style.opacity=(ramp(t,0.2,0.9)*(1-ramp(t,3.0,3.6))).toFixed(3);
  titleCard.style.opacity=(ramp(t,3.3,4.5)*(1-ramp(t,8.0,9.0))).toFixed(3);
  cap.style.opacity=(ramp(t,9.6,10.4)*(1-ramp(t,12.6,13.4))).toFixed(3);
  const wf=1-ramp(t,57,58.2);
  wishEls.forEach((w,i)=>{const p=ramp(t,WT[i],WT[i]+0.9);w.style.opacity=(p*wf).toFixed(3);w.style.transform='translateY('+((1-p)*8).toFixed(1)+'px)';});
  flash.style.opacity=(ramp(t,65.8,67.4)*(1-ramp(t,67.8,69.0))).toFixed(3);
  endcard.style.opacity=ramp(t,86.4,88).toFixed(3);
  bar.style.width=(t/D*100).toFixed(2)+'%';
}
let curCue=-2,curWords=[],lastWi=-2;
function renderSubs(t){
  let idx=-1;for(let i=0;i<CUES.length;i++){if(t>=CUES[i][0]&&t<CUES[i][1]){idx=i;break;}}
  if(idx!==curCue){
    curCue=idx;subs.textContent='';curWords=[];lastWi=-2;
    if(idx>=0)CUES[idx][2].split(/\s+/).forEach(w=>{const s=document.createElement('span');s.className='w';s.textContent=w;subs.appendChild(s);curWords.push(s);});
  }
  if(idx>=0){
    const a=CUES[idx][0],b=CUES[idx][1],n=curWords.length;
    const wi=Math.min(n-1,Math.max(0,Math.floor((t-a)/(b-a-0.3)*n)));
    if(wi!==lastWi){lastWi=wi;curWords.forEach((s,i)=>{s.className='w'+(i<wi?' on':(i===wi?' cur':''));});}
  }
}
```


**Overlay HTML (stage içinde, `svg`'den sonra):**

```html
    <div class="grain"></div>
    <div class="vig"></div>
    <div class="shade"></div>
    <div class="bartrack"></div><div class="bar" id="bar"></div>
    <div class="mark">S1 · B1</div>

    <div class="ov hook" id="hook"><div class="y">2026</div><div class="s">son gün</div></div>
    <div class="ov title" id="titleCard"><div class="brand">FLATHEIM</div><div class="rule"></div><div class="ch">BÖLÜM 1</div><div class="nm">Son Dilek</div></div>
    <div class="ov cap" id="cap"><div class="a">HASTANE ODASI</div><div class="b">2026</div><div class="l"></div></div>
    <div class="ov wishes" id="wishes">
      <div class="wish"><b>Keşke</b> başka bir dünyada doğabilseydim.</div>
      <div class="wish"><b>Keşke</b> hayatımı yeniden yaşayabilseydim.</div>
      <div class="wish"><b>Keşke</b> korkmadan yürüyebilseydim.</div>
      <div class="wish"><b>Keşke</b> geleceğim olsaydı.</div>
    </div>
    <div class="ov flash" id="flash"></div>
    <div class="ov endcard" id="endcard"><div class="k">DEVAM EDECEK</div><div class="n">BÖLÜM 2</div><div class="t">Ahır</div></div>
    <div id="subs" aria-live="off"></div>

    <button class="start" id="startBtn" type="button" aria-label="Videoyu başlat">
      <span class="disc"><svg width="30" height="30" viewBox="0 0 24 24" aria-hidden="true"><path d="M8 5.2v13.6L19.2 12z" fill="#f0c470"/></svg></span>
      <span id="startLbl">Başlat</span>
    </button>
  </div>
```


- **Kanca (`#hook`):** 0.2–3.6 sn. Büyük Cinzel (`17cqw`), kısa ("2026"), altına italik serif alt yazı ("son gün"). Renk/parıltı bölüm tonuna göre (Bölüm 1'de monitör yeşili).
- **Başlık kartı (`#titleCard`):** ~3.3–9 sn. `FLATHEIM` altın gradyan, ince çizgi, `BÖLÜM N`, italik bölüm adı.
- **Köşe başlığı (`#cap`):** mekân + yıl/zaman, 9.6–13.4 sn gibi.
- **Dilek/söz cümleleri (`#wishes`):** isteğe bağlı; her cümle `WT[i]`'de **8 px yukarı kayarak** belirir; ilk kelime `<b>` ile altın.
- **Flash (`#flash`):** büyük geçişte; kısa **in** (1.6 sn) + **out** (1.2 sn).
- **Bitiş kartı (`#endcard`):** `DEVAM EDECEK` / `BÖLÜM N+1` / sonraki bölüm adı, 86.4–88 sn'de belirir ve sona kadar kalır.
- Yeni bölümlerde `.mark`, `.head p`, `aria-label`, `<title>` **bölüme göre güncellenir.**

### 10.3 Altyazı vurgusu
`renderSubs(t)` (yukarıda): her cue kelimelere bölünür. Okunan kelimeler beyaz (`.on`), **şu anki kelime altın (`.cur`)**, okunmayanlar soluk (`.w`). Vurgu hızı `(t−a)/(b−a−0.3)·n` ile cue süresine yayılır.

---

## 11. SES (dosyasız, Web Audio)

### 11.1 Olay planı

```js
const EV=[];
BEATS.forEach(b=>EV.push({t:b,k:'beep',v:(b<9||b>=58)?0.10:(b<24?0.05:0.035)}));
const CH=[[110,[220,261.63,329.63,440]],[87.31,[174.61,220,261.63,349.23]],[130.81,[196,261.63,329.63,392]],[98,[196,246.94,293.66,392]]];
for(let bar0=0;bar0<9;bar0++){
  const t0=9+bar0*6;if(t0>=58)break;const ch=CH[bar0%4];
  EV.push({t:t0,k:'note',f:ch[0],d:5.5,v:0.10});
  const pat=[0,1,2,3,2,1,2,3];
  for(let s=0;s<8;s++){const tt=t0+s*0.75;if(tt>=58.2)break;EV.push({t:tt,k:'note',f:ch[1][pat[s]],d:2.2,v:0.07+(tt>40?0.02:0)});}
}
for(let tt=69.6;tt<90;tt+=1.1){const vv=0.28+0.18*clamp((tt-69.6)/20);EV.push({t:tt,k:'thump',v:vv});EV.push({t:tt+0.28,k:'thump',v:vv*0.55});}
EV.sort((a,b)=>a.t-b.t);
const EVT=EV.map(e=>e.t);
```


### 11.2 Ses motoru

```js
let ctx=null,nodes={},sndOn=true,narrOn=true,voice=null,evI=0,cueI=0;
function initAudio(){
  if(ctx)return true;
  const AC=window.AudioContext||window.webkitAudioContext;if(!AC)return false;
  try{
    ctx=new AC();
    const master=ctx.createGain();master.gain.value=0.9;master.connect(ctx.destination);
    nodes.sfx=ctx.createGain();nodes.sfx.connect(master);
    nodes.mus=ctx.createGain();nodes.mus.gain.value=0.8;nodes.mus.connect(master);
    const layer=(dest,filt)=>{const gn=ctx.createGain();gn.gain.value=0;if(filt){const f=ctx.createBiquadFilter();f.type='lowpass';f.frequency.value=filt;gn.connect(f);f.connect(dest);}else gn.connect(dest);return gn;};
    const osc=(type,f,dest,det)=>{const o=ctx.createOscillator();o.type=type;o.frequency.value=f;if(det)o.detune.value=det;o.connect(dest);o.start();return o;};
    nodes.drone=layer(nodes.mus);osc('sine',55,nodes.drone);osc('sine',82.4,nodes.drone);
    nodes.pad=layer(nodes.mus,700);osc('sawtooth',110,nodes.pad);osc('sawtooth',164.81,nodes.pad,6);osc('sawtooth',220,nodes.pad,-6);
    nodes.endp=layer(nodes.mus,520);osc('sawtooth',73.42,nodes.endp);osc('sawtooth',110,nodes.endp,5);osc('sawtooth',146.83,nodes.endp,-5);
    nodes.flat=layer(nodes.sfx);osc('sine',1000,nodes.flat);
    const len=ctx.sampleRate*2,buf=ctx.createBuffer(1,len,ctx.sampleRate),chd=buf.getChannelData(0);
    for(let i=0;i<len;i++)chd[i]=Math.random()*2-1;
    const src=ctx.createBufferSource();src.buffer=buf;src.loop=true;
    nodes.bp=ctx.createBiquadFilter();nodes.bp.type='bandpass';nodes.bp.frequency.value=480;nodes.bp.Q.value=0.7;
    nodes.wind=ctx.createGain();nodes.wind.gain.value=0;src.connect(nodes.bp);nodes.bp.connect(nodes.wind);nodes.wind.connect(nodes.mus);src.start();
    return true;
  }catch(e){ctx=null;return false;}
}
function setG(gn,v){if(gn)gn.gain.setTargetAtTime(v,ctx.currentTime,0.15);}
function updateCont(t,on){
  if(!ctx)return;
  const k=on?1:0;
  setG(nodes.drone,k*0.12*(1-ramp(t,6.5,9.5)));
  setG(nodes.pad,k*0.07*ramp(t,41,48)*(1-ramp(t,57,59.5)));
  setG(nodes.flat,k*0.06*ramp(t,64.55,64.7)*(1-ramp(t,66.6,67.5)));
  setG(nodes.wind,k*0.5*ramp(t,68.4,73));
  setG(nodes.endp,k*0.07*ramp(t,83.5,88.5));
  if(nodes.bp)nodes.bp.frequency.setTargetAtTime(380+150*Math.sin(t*0.4),ctx.currentTime,0.2);
}
function fire(e){
  const now=ctx.currentTime;
  if(e.k==='beep'){const o=ctx.createOscillator(),gn=ctx.createGain();o.type='sine';o.frequency.value=880;gn.gain.setValueAtTime(0,now);gn.gain.linearRampToValueAtTime(e.v,now+0.006);gn.gain.exponentialRampToValueAtTime(0.0001,now+0.14);o.connect(gn);gn.connect(nodes.sfx);o.start(now);o.stop(now+0.16);}
  else if(e.k==='note'){[['triangle',e.f,1],['sine',e.f*2,0.25]].forEach(q=>{const o=ctx.createOscillator(),gn=ctx.createGain();o.type=q[0];o.frequency.value=q[1];gn.gain.setValueAtTime(0,now);gn.gain.linearRampToValueAtTime(e.v*q[2],now+0.012);gn.gain.exponentialRampToValueAtTime(0.0001,now+e.d);o.connect(gn);gn.connect(nodes.mus);o.start(now);o.stop(now+e.d+0.05);});}
  else if(e.k==='thump'){const o=ctx.createOscillator(),gn=ctx.createGain();o.type='sine';o.frequency.setValueAtTime(95,now);o.frequency.exponentialRampToValueAtTime(42,now+0.18);gn.gain.setValueAtTime(0,now);gn.gain.linearRampToValueAtTime(e.v,now+0.01);gn.gain.exponentialRampToValueAtTime(0.0001,now+0.32);o.connect(gn);gn.connect(nodes.sfx);o.start(now);o.stop(now+0.35);}
}
function pickVoice(){try{const vs=speechSynthesis.getVoices();voice=vs.find(v=>/^tr/i.test(v.lang))||null;}catch(e){voice=null;}}
try{speechSynthesis.onvoiceschanged=pickVoice;pickVoice();}catch(e){}
function cancelSpeech(){try{if(speechSynthesis.speaking||speechSynthesis.pending)speechSynthesis.cancel();}catch(e){}}
function speak(text,dur){
  if(!narrOn||!voice)return;
  try{
    cancelSpeech();
    const u=new SpeechSynthesisUtterance(text);u.voice=voice;u.lang=voice.lang||'tr-TR';
    u.rate=clamp(text.split(/\s+/).length/2.4/dur,0.7,1.08);u.pitch=0.9;
    speechSynthesis.speak(u);
  }catch(e){}
}
function tick(t){
  while(evI<EV.length&&EV[evI].t<=t){const e=EV[evI++];if(sndOn&&ctx&&t-e.t<0.3)fire(e);}
  updateCont(t,sndOn);
  while(cueI<CUES.length&&CUES[cueI][0]<=t){const c=CUES[cueI++];if(t-c[0]<0.5)speak(c[2],c[1]-c[0]);}
}
function resync(t){evI=lb(EVT,t);let i=0;while(i<CUES.length&&CUES[i][0]<t)i++;cueI=i;cancelSpeech();}
function silence(){cancelSpeech();if(ctx){const o=ctx.currentTime;['drone','pad','endp','flat','wind'].forEach(n=>{if(nodes[n])nodes[n].gain.setTargetAtTime(0,o,0.25);});}}
```


**Ses tasarımı ilkeleri**
- **Monitör bip'i (880 Hz sine, 0.14 sn):** yaşam göstergesi. Sahneye göre seviye `0.10 / 0.05 / 0.035`. Ölüm anında düz çizgi tonu (`1000 Hz`).
- **Müzik (arpej):** 6 sn'lik ölçülerde 4 akor (`CH`), bas + 8'li arpej (`pat`). Dramatik anlarda bir kademe yükselir.
- **Drone (55/82.4 Hz):** başlangıç/sessizlik zemini; **Pad:** dilek/duygusal doruk; **Rüzgâr:** yeni dünya; **Thump:** yeni dünyanın "nabzı" (hızlanıp güçlenir); **`endp`:** bitiş kartına yaklaşırken.
- Bölüme göre **uyarlanır, ama tüm sesler üretilebilir (synth) kalır.** Örnek ses dosyası gömülmez.
- Her sürekli katman `updateCont` içinde `ramp` ile `t`'ye bağlanır (sarma güvenli).
- `tick` yalnızca **geçmişi çalmaz** (`t−e.t<0.3` koşulu), sarma sonrası ses yağmuru olmaz.

### 11.3 Yeni bölümde yeni ses eklemek
Yeni efekt gerekiyorsa `fire(e)`'ye yeni `k` türü eklenir (osilatör + gain zarfı), sürekli ses için `initAudio`'da yeni `layer()` + `updateCont`'ta `setG(...)` eklenir. **Mevcut sesler silinmez.**

---

## 12. SAHNE SAHNE KOD ÖRNEKLERİ (animS fonksiyonları)

Aşağıdakiler yeni sahne yazarken **örnek alınacak** gerçek animasyon fonksiyonlarıdır.

### 12.1 ECG çizgisi (kalp monitörü)

```js
/* ---------------- Kalp atışları (monitör + bip) ---------------- */
const BEATS=[];for(let b=0.6;b<57.9;b+=0.92)BEATS.push(b);BEATS.push(58.7,60.0,61.6,63.6);
function lb(a,x){let lo=0,hi=a.length;while(lo<hi){const m=(lo+hi)>>1;if(a[m]<x)lo=m+1;else hi=m;}return lo;}
const g=(x,c,w)=>Math.exp(-((x-c)/w)*((x-c)/w));
function pulse(d){return 0.14*g(d,-0.20,0.045)-0.13*g(d,-0.035,0.011)+1.0*g(d,0,0.013)-0.22*g(d,0.032,0.014)+0.26*g(d,0.27,0.06);}
function sig(tau){let v=0;for(let i=lb(BEATS,tau-0.5);i<BEATS.length&&BEATS[i]<tau+0.3;i++)v+=pulse(tau-BEATS[i]);return v;}
function ecgD(t,x0,x1,win,y0,amp,n){let d='';for(let k=0;k<=n;k++){const x=x0+(x1-x0)*k/n;const tau=t-win*(1-k/n);d+=(k?'L':'M')+f1(x)+' '+f1(y0-amp*sig(tau));}return d;}

/* ---------------- Sahne düzeni ---------------- */
const scenes=[
 {id:'s1',a:0,b:9,f:0.01},{id:'s2',a:9,b:24,f:0.9},{id:'s3',a:24,b:40,f:1.0},
 {id:'s4',a:40,b:58,f:1.2},{id:'s5',a:58,b:68,f:1.0},{id:'s6',a:68,b:90,f:0.8}
];
scenes.forEach(s=>{s.g=$('#'+s.id);});

/* ===================== SAHNE 1 ===================== */
(function(){const gr=$('#s1grid');for(let x=0;x<=360;x+=24)el('line',{x1:x,y1:200,x2:x,y2:460},gr);for(let y=200;y<=460;y+=26)el('line',{x1:0,y1:y,x2:360,y2:y},gr);})();
const s1ecg=$('#s1ecg'),s1dot=$('#s1dot');
function animS1(t){
  const amp=100*ramp(t,0.1,0.9)*(1-0.55*ramp(t,3.3,4.6));
  const y0=lerp(330,478,ramp(t,3.3,4.5));
  s1ecg.setAttribute('d',ecgD(t,0,346,4,y0,amp*0.8,520));
  s1dot.setAttribute('cx',346);s1dot.setAttribute('cy',f1(y0-amp*0.8*sig(t)));
}
```


- `ecgD(t,x0,x1,win,y0,amp,n)`: zaman penceresi `win` sn'lik kayan çizgi; sağ uç "şimdi".
- Kalp atışı zamanları `BEATS`. Düzensiz/yavaşlayan atış için dizi elle uzatılır (58.7, 60.0, 61.6, 63.6).
- `pulse(d)`: P dalgası + Q + R + S + T biçimli sentetik EKG atımı.

### 12.2 Hastane odası (S2)

```js
const s2cam=$('#s2cam'),s2body=$('#s2body'),s2head=$('#s2head'),s2eye=$('#s2eye'),s2ecg=$('#s2ecg'),s2mh=$('#s2mh'),s2hh=$('#s2hh'),s2drop=$('#s2drop'),s2beam=$('#s2beam'),s2dust=$('#s2dust');
const dustR=rng(7),dust=[];
for(let i=0;i<22;i++){dust.push({x:el('circle',{r:(0.7+dustR()*1.4).toFixed(2),fill:'#e9f5ff'},s2dust),bx:90+dustR()*210,by:300+dustR()*260,ph:dustR()*6.28});}
function animS2(t){
  const lt=t-9,k=clamp(lt/15),sc=1+0.2*k;
  s2cam.setAttribute('transform','translate(150 370) scale('+sc.toFixed(4)+') translate(-150 -370)');
  s2body.setAttribute('transform','translate(0 '+f1(Math.sin(t*1.25)*0.9)+')');
  s2head.setAttribute('transform','rotate('+f1(5*ramp(lt,6,12))+' 131 360)');
  const bp=t%4.1;let es=1;if(bp<0.2)es=1-Math.sin(bp/0.2*Math.PI)*0.92;
  s2eye.setAttribute('transform','translate(139 341) scale(1 '+es.toFixed(3)+')');
  s2ecg.setAttribute('d',ecgD(t,98,194,2.6,246,18,150));
  s2mh.setAttribute('transform','rotate('+f1(lt*42)+' 70 140)');
  s2hh.setAttribute('transform','rotate('+f1(60+lt*3.5)+' 70 140)');
  s2drop.setAttribute('cy',f1(285+((t*0.85)%1)*13));s2drop.setAttribute('opacity',(1-((t*0.85)%1)*0.7).toFixed(2));
  s2beam.setAttribute('opacity',(0.85+0.15*Math.sin(t*0.5)).toFixed(3));
  dust.forEach(d=>{d.x.setAttribute('cx',f1(d.bx+12*Math.sin(t*0.3+d.ph)));d.x.setAttribute('cy',f1(d.by+9*Math.cos(t*0.25+d.ph*1.3)));d.x.setAttribute('opacity',(0.15+0.5*Math.pow(Math.sin(t*0.7+d.ph),2)).toFixed(2));});
}
```


### 12.3 Şehir, pencereden (S3)

```js
function animS3(t){
  const lt=t-24,k=clamp(lt/16),sc=1+0.07*k;
  s3cam.setAttribute('transform','translate(190 300) scale('+sc.toFixed(4)+') translate(-190 -300)');
  const sy=342+lt*2.4;s3sun.setAttribute('cy',f1(sy));s3sunglow.setAttribute('cy',f1(sy));
  s3dim.setAttribute('opacity',(0.42*ramp(lt,8,16)).toFixed(3));
  s3lamp.setAttribute('opacity',(0.5*ramp(lt,6,12)).toFixed(3));
  winEls.forEach(w=>{w.e.setAttribute('opacity',(w.base?0.9:0.12+0.88*ramp(lt,w.th-1,w.th+1)).toFixed(2));});
  PEOPLE.forEach(p=>{
    const o=p.o;let x,dir,sw=0,bob=0;
    if(o.stand){x=o.x;dir=o.face;bob=Math.sin(lt*7+o.ph)*0.9*(Math.sin(lt*0.9+o.ph)>0?1:0.2);}
    else{x=wrap(o.x+o.v*lt,-60,490);dir=o.v>=0?1:-1;const rate=3.2+Math.abs(o.v)*0.11;sw=Math.sin(lt*rate+o.ph)*28;bob=Math.abs(Math.sin(lt*rate+o.ph))*-1.4;}
    p.g.setAttribute('transform','translate('+f1(x)+' '+f1(481+bob)+') scale('+(o.sc*dir).toFixed(2)+' '+o.sc+')');
    p.L1.setAttribute('transform','rotate('+f1(sw)+' 0 -14)');p.L2.setAttribute('transform','rotate('+f1(-sw)+' 0 -14)');
    const as=o.stand?Math.sin(lt*3+o.ph)*(o.face>0?26:8):-sw*0.8;
    p.A1.setAttribute('transform','rotate('+f1(as)+' 0 -29)');p.A2.setAttribute('transform','rotate('+f1(-as)+' 0 -29)');
  });
  CARS.forEach(c=>{const o=c.o;const x=wrap(o.x+o.v*lt,-90,540);c.g.setAttribute('transform','translate('+f1(x)+' '+o.y+') scale('+(o.sc*(o.v>=0?1:-1)).toFixed(2)+' '+o.sc+')');});
  s3boy.setAttribute('transform','translate(0 '+f1(Math.sin(t*1.1)*1.2)+')');
}
```


### 12.4 Gece + ejderha (S4)

```js
function animS4(t){
  const lt=t-40;
  stars.forEach(s=>s.e.setAttribute('opacity',(0.35+0.65*Math.abs(Math.sin(t*s.sp+s.ph))*ramp(lt,0,3)).toFixed(2)));
  aur(s4aur1,t,150,26,0,0.5);aur(s4aur2,t,200,22,2,0.38);
  s4island.setAttribute('transform','translate(84 '+f1(262+Math.sin(t*0.8)*5)+')');
  const x1=lerp(470,-170,clamp((lt-2)/14)),y1=196+16*Math.sin(lt*0.9);
  s4d1.setAttribute('transform','translate('+f1(x1)+' '+f1(y1)+') scale(-0.95 0.95)');flap(dr1,lt*3.3);
  const x2=lerp(-80,440,clamp((lt-6)/12)),y2=112+8*Math.sin(lt*1.2);
  s4d2.setAttribute('transform','translate('+f1(x2)+' '+f1(y2)+') scale(0.42)');flap(dr2,lt*4.2+1);
  motes.forEach(m=>{const y=wrap(m.y0-m.v*lt,-40,720);m.e.setAttribute('cx',f1(m.x0+14*Math.sin(lt*0.7+m.ph)));m.e.setAttribute('cy',f1(y));m.e.setAttribute('opacity',(ramp(lt,2,9)*(0.25+0.6*Math.pow(Math.sin(lt*1.6+m.ph),2))).toFixed(2));});
  s4frame.setAttribute('opacity',(1-0.88*ramp(lt,8,14)).toFixed(3));
  s4boy.setAttribute('transform','translate(0 '+f1(Math.sin(t*1.1)*1.2)+')');
}
```


### 12.5 Monitör yakın planı (S5)

```js
function animS5(t){
  const lt=t-58;
  s5ecg.setAttribute('d',ecgD(t,38,322,3.4,276,64,520));
  let i=lb(BEATS,t+1e-6);if(i>=BEATS.length||BEATS[i]>t)i--;
  let bpm=65;if(i>=1)bpm=Math.round(60/(BEATS[i]-BEATS[i-1]));
  const flat=t>64.6;if(flat)bpm=0;
  s5bpm.textContent=bpm?String(bpm):'--';
  const col=flat?'#ff6b6b':'#5dffb0';s5bpm.setAttribute('fill',col);s5led.setAttribute('fill',col);
  s5spo.textContent=flat?'--':String(Math.round(97-12*ramp(t,58,64.5)));
  s5glow.setAttribute('opacity',(flat?0.35:0.7+0.3*Math.sin(t*3)).toFixed(2));
  s5cam.setAttribute('transform','translate(180 300) scale('+(1+0.05*clamp(lt/10)).toFixed(4)+') translate(-180 -300)');
  s5hand.setAttribute('transform','translate(0 -52) rotate('+f1(-2.5*ramp(lt,5,7.5))+' 160 530)');
}
```


### 12.6 Yeni dünyada uyanış (S6)

```js
function animS6(t){
  const lt=t-68;
  const a=openAt(lt),y1=lerp(365,235,a),yc=y1-24;
  s6lid.setAttribute('d','M20,120 L340,120 L340,300 L300,300 C250,'+f1(y1)+' 110,'+f1(y1)+' 60,300 L20,300 Z');
  s6lidline.setAttribute('d','M60,300 C110,'+f1(y1)+' 250,'+f1(y1)+' 300,300');
  s6crease.setAttribute('d','M72,296 C120,'+f1(yc)+' 240,'+f1(yc)+' 288,296');
  s6crease.setAttribute('opacity',(0.7*clamp((a-0.25)/0.5)).toFixed(2));
  const look=7*Math.sin(lt*0.7)*ramp(lt,6,9);
  s6iris.setAttribute('transform','translate('+f1(180+look)+' '+f1(300+2*Math.sin(lt*0.9))+')');
  s6pupil.setAttribute('r',f1(11+11*ramp(lt,4.5,9)));
  s6dark.setAttribute('opacity',(1-0.8*ramp(lt,1.6,7.5)).toFixed(3));
  s6eye.setAttribute('transform','translate(180 300) scale('+(1+0.3*ramp(lt,7,10)).toFixed(3)+') translate(-180 -300)');
  const kb=ramp(lt,7.6,9.6);
  s6eye.setAttribute('opacity',(1-kb).toFixed(3));s6eye.style.display=kb>=1?'none':'';
  s6barn.setAttribute('opacity',kb.toFixed(3));s6barn.style.display=kb<=0?'none':'';
  if(kb>0){
    const sd=4*(1-ramp(lt,8.2,14));
    if(sd>0.05){s6cam.setAttribute('filter','url(#fBarnBlur)');blurStd.setAttribute('stdDeviation',sd.toFixed(2));}else s6cam.removeAttribute('filter');
    s6cam.setAttribute('transform','translate(180 330) rotate('+f1(-3+1.4*Math.sin(lt*0.5))+') scale('+(1.05+0.012*Math.sin(lt*1.1)).toFixed(4)+') translate(-180 -330)');
    $('#s6beam').setAttribute('opacity',(0.8+0.2*Math.sin(lt*0.6)).toFixed(3));
    dust6.forEach(d=>{d.e.setAttribute('cx',f1(d.bx+12*Math.sin(lt*0.3+d.ph)));d.e.setAttribute('cy',f1(d.by+9*Math.cos(lt*0.25+d.ph*1.3)));d.e.setAttribute('opacity',(0.15+0.5*Math.pow(Math.sin(lt*0.7+d.ph),2)).toFixed(2));});
    creatures.forEach((c,ci)=>{
      c.g.setAttribute('transform','translate('+c.x+' '+f1(c.y+Math.sin(lt*0.9+c.ph)*2.2)+')');
      const vis=ramp(lt,12.4+ci*0.6,14.2+ci*0.6);
      const bp=(lt+c.ph)%4.6;const bl=bp<0.18?1-Math.sin(bp/0.18*Math.PI)*0.95:1;
      c.es.forEach(q=>{q.e.setAttribute('opacity',vis.toFixed(2));q.e.setAttribute('transform','translate('+q.p[0]+' '+q.p[1]+') scale(1 '+bl.toFixed(2)+')');});
    });
  }
}
```


---

## 13. RENK PALETİ KÜTÜPHANESİ

| Rol | Hex |
|---|---|
| **LUX / altın** | `#f0c470` (açık `#fff2c9`, koyu `#b98a3a`) |
| **NOX / mor** | `#7a6ae0` (açık `#a98bff`, gece `#1a1840`, derin `#3b2a5e`) |
| Zemin (UI) | `#0a0d11`, panel `#121820`, panel-2 `#1a222c`, çizgi `#2a3541` |
| Metin | `#ece6da`, sönük `#8d97a2` |
| Monitör yeşili | `#5dffb0` (ızgara `#0f2c20`, arka `#03100a`), uyarı kırmızı `#ff6b6b`, SpO2 mavi `#7fd9ff` |
| Hastane | duvar `#1c2b35→#0c1319`, yatak `#22313b`, battaniye `#5d7d91`, gömlek `#9fb7c6`, ten `#e6cdbb` |
| Gün batımı | `#4b3f7a #c8607a #f2a171 #ffd59a`, güneş `#fff0c4`, bina `#8a6a86 #5b4a70 #3a3050 #43385a` |
| Gece | `#070a1d #1a1840 #3b2a5e`, ay `#fffbe9 #f3e6bd`, aurora `#5ef2c6` |
| Ahır | `#0d0907 #2c2015 #110b07`, hay `#c4a35a #a98a45 #8f7338 #d6b96c`, ışık sızıntısı `#9cc3e8` |
| Siluet | `#05080b` (insan), `#06070e`–`#07080f` (ejderha), `#050403` (yaratık) |
| Yaratık gözleri | `#ffb347 #c8ff6b #ff6b5a #b68cff` |
| Kenar ışığı (rim) | `#ffb078` (sıcak), `#a8bcff` (soğuk), `#9cc3e8` (ahır) |

**Işık hiyerarşisi:** her sahnede **bir** baskın ışık kaynağı (pencere, güneş, ay, monitör, huzme), karakter ve nesneler o kaynağa göre **rim ve gölge** alır. Çoklu eşit ışık yok.

**Duygu → renk:** umut/yaşam/dilek = altın · gizem/güç/gölge = mor · hastalık/ölçüm = soğuk mavi-yeşil · ölüm = kırmızı uyarı + siyah · tehlike = sıcak göz renkleri + koyu zemin.

---

## 14. DÜNYA, KARAKTERLER, KURALLAR

### 14.1 Seri
**FLATHEIM**: 7 sezonluk dark fantasy isekai. Sezon başına 20, toplam 140 bölüm. Her bölüm 90 sn (Reels/Shorts). Şu an yalnızca **Sezon 1** planlıdır; diğer sezonların detayları **uydurulmaz**.

### 14.2 Premise
2026'da kanserle mücadele eden genç bir çocuk hastane yatağında ölürken "gerçekten yaşayabileceği bir dünya" diler. Dileği gerçekleşir, ama özel/zengin/güçlü doğmaz: **ahırımsı bir yerde, yırtık kıyafetle, aç, parasız, dilini bilmeden** uyanır. **Sezon 1 teması: HAYATTA KALMAK.**

### 14.3 Dünya
- İnsan krallıkları, elfler, beastfolk, yarı insanlar, bağımsız şehirler, karanlık bölgeler; **ejderhalar**, büyüyle değişmiş canlılar, gölgeler, kadim varlıklar.
- Hiçbir yer tam güvenli değil. Bölgeler arası tehlike/güç farkı büyük.
- **Sezon 1 yalnız küçük köy Eldervale ve çevresi.** Dünyanın büyüklüğü haritadan, konuşmadan, görevden anlaşılır. Dünya bilgisi **olay içinde** verilir, ders gibi anlatılmaz.

### 14.4 Toplumsal sınıf (üstten alta)
1. **Kraliyet/İmparatorluk**: yönetim; en güçlü olması gerekmez.
2. **Alfa**: 4 elementin tamamı + olağanüstü kapasite; bazıları zihne müdahale eder (hipnoz, manipülasyon). Toplum düzenini değiştirebilecek kadar tehlikeli. *S1'de ekranda yok, söylentide/korkuda var.*
3. **Üst kademe**: soylular, güçlü büyücüler, önemli tüccarlar, komutanlar.
4. **Orta kademe**: zanaatkâr, tüccar, düşük/orta büyücü, asker, maceracı.
5. **Alt kademe "Çöp"**: büyüsüz/düşük kapasite, yoksul, dışlanmış; ağır iş, borç, küçümsenme.

### 14.5 Büyü
- Ateş (saldırı/yıkım) · Su (akış/kontrol) · Toprak (dayanıklılık/savunma) · Hava (hız/basınç/uçuş). Çoğu insanda 1, azında 2 element. Doğuştan belirlenir. **Test küreleri** ölçer.
- **Ana karakter: IŞIK (LUX) + GÖLGE (NOX)** , bilinen sistemin dışında, kadim, nadir. **S1'de anlamı açıklanmaz.** Güç yalnız korku/hayatta kalma/yoğun duyguda **istemeden** çıkar.

### 14.6 Ekonomi
100 bronz = 1 gümüş · 100 gümüş = 1 altın. Bir altın, sıradan işçinin uzun süre çalışarak kazandığı para. **Para her bölümde sorun olarak hissedilir** (yemek, konaklama, kıyafet, silah, tedavi, ulaşım). En ucuz kılıç = 1 altın. Depo işi = saatte 1 bronz. At arabası köy yolculuğu = 1 altın verilir.

### 14.7 Lonca
Canavar avı, keşif, eskort, kayıp eşya, bölge temizliği. Katılım için büyü gerekir. Ana karakter test küresini **kırar**, elementi okunamaz → **geçici kayıt: `ELEMENT UNKNOWN, RANK UNCLASSIFIED`**. Diğer maceracılar ona güvenmez.

### 14.8 Karakterler
- **Ana karakter:** **adı yok** (ilk 20 bölümde isim verilmez). 2026'dan gelen çocuk; eski dünya anıları tam; başta korkak, zeki, gözlemci, temkinli ama güvenmek ister; yanlış kararlar alır; ölümün duygusal izlerini taşır; özgürlüğü kolay kabullenemez; **gücünden habersiz.** S1 sonunda "güçlü değil, potansiyeli olan".
- **Lys:** genç yarı insan kız, Toprak elementi, düşük kapasite yüzünden dışlanmış. İlk gerçek arkadaş; dil öğretir, basit kılıç hareketleri gösterir, bazen dalga geçer ama yardım eder. **İlişki yavaş gelişir.**
- **Koyu pelerinli adam (B5):** yüzü görünmez, kurdu saniyeler içinde öldürür, yabancı dilde konuşur, **10 altın** bırakıp gider. Açıklama yok.
- **At arabası sürücüsü (B6):** altını görünce tavrı değişir.
- **Eldervale halkı (B7–B15):** pansiyoncu, kıyafetçi, silahçı, depo patronu, lonca kayıt görevlisi + baş görevli, alt kademe insanlar (kovulan çocuk, işten atılan işçi, eşyasını satan kadın, yaşlı adam).
- **Gölge yaratık (B19):** insan biçimli, tamamen karanlık, saldırıdan etkilenmez.
- **Saray büyücüleri (B20):** çatlayan büyü taşı, `LUX`/`NOX` sembolleri.

### 14.9 Yazım kuralları (30 maddenin üretim karşılığı)
**Güç & tempo**
1. Ana karakter erken bölümlerde güçlü olmaz; güçlerini hemen açıklama, bilinçli kullandırma.
2. Kurtuluşlar tesadüf zinciri olmaz. Canavarlar **gerçekten tehlikeli**; karakter her karşılaşmadan sağ çıkmak zorunda değil.
3. Tempo aceleye getirilmez. Güç, para, bağ, dil **zaman alır**. Dil aşamalı: bazı kelimeleri anlar, bazılarını anlamaz.

**Dünya bilgisi**
4. Dünya karakterle birlikte keşfedilir. Bilgi **olay içinde**:
   - sınıf sistemi → bir insanın aşağılanması,
   - ekonomi → para kazanmakta zorlanma,
   - element sistemi → büyü testi,
   - lonca → hayatta kalma ihtiyacı.
5. Uzun açıklamalarla sistem bir anda anlatılmaz.
6. Alfa'lar S1'de görünmese de konuşmada/korkuda hissedilir.
7. Ejderhalar sıradan yaratık gibi gösterilmez: büyük, korkutucu, nadir.

**Karakterler**
8. Herkes ana karakteri sevmez; gücünü bilmedikleri için küçümseyebilirler.
9. Her karakterin amacı, kişiliği, geçmişi var. İyi/kötü/gri dengesi. İlişkiler hızlı gelişmez.
10. Diyaloglar doğal; karakterler yalnızca bilgi vermek için konuşmaz.

**Duygu & ton**
11. Gerçek ihtiyaçlar önemli: açlık, susuzluk, yorgunluk, yaralanma, para, güvenlik. Savaşın sonuçları (yaralanma, korku, ölüm, travma) kalıcı.
12. Karanlık ton var ama sürekli kasvet yok: dünyanın güzelliği, keşif, mizah, küçük mutluluk, **ilk kez özgür hissettiği anlar** da var.
13. Eski dünya ve ölüm zaman zaman hatırlanır, yaşam değerine bakışı etkiler; yeni dünyadaki olayları kesecek sıklıkta değil.
14. Her bölüm yapay cliffhanger ile bitmek zorunda değil; sakin/duygusal bitiş serbest.
15. Önceki bölüm uzun tekrar edilmez.
16. Görsel güçlü sahneler: ortam, hava, ışık, mimari, yaratık, yüz ifadesi **görüntüyle** anlatılır.

---

## 15. SEZON 1 BÖLÜM LİSTESİ

| # | Başlık | Ana olay |
|---|---|---|
| 1 | **Son Dilek** | Hastane, kanser, dilek, monitör susar, gözler yeni dünyada açılır. **(YAPILDI)** |
| 2 | **Ahır** | Karanlık, kötü kokan ahırda uyanış; tuhaf yaratıklar (at benzeri, garip çıkıntılı/gözlü). Kapıdan çıkış: dağlar, vadi, uçan devasa yaratıklar, **ejderha** geçer. "Rüya mı?" → yere dokunur, taş, rüzgâr: gerçek. Uçurumun kenarına yürür. |
| 3 | **Flatheim** | Uçurumdan gözlem: dağlar, ormanlar, duman, dev yaratıkların dövüşü (ağaç devirir). Cebinde eski harita, `FLATHEIM`. En yakın köy ~30 km. Yiyecek/su/silah/para yok. Haritayı katlar, yola çıkar. |
| 4 | **Otuz Kilometre** | Orman yolu. Güzellik → açlık/susuzluk/ağrı. Yaratıklar izliyor. Hırıltı. |
| 5 | **İlk Canavar** | Dev kurt benzeri yaratık, kolundan yaralanır, taşla savunma boşa. Pelerinli adam yaratığı öldürür, yabancı dil, 10 altın bırakır. |
| 6 | **On Altın** | Altını alır, at arabasını durdurur, 1 altın verir, adamın tavrı değişir, ekonomiyi bilmediğini fark eder. |
| 7 | **Eldervale** | Köy, farklı ırklar, pazar. Pansiyon, ilk güvenli uyku. |
| 8 | **Yeni Kıyafetler** | Deri ceket, pantolon, bot, pelerin; aynada ilk düzgün görünüm. |
| 9 | **Silah Dükkânı** | En ucuz kılıç 1 altın. Almadan çıkar; para kazanması gerektiğini anlar. |
| 10 | **Bir Bronz Sikke** | Depo işi, saatte 1 bronz. Para birimi hesabı. Hayatta kalmak = çalışmak. |
| 11 | **Maceracı Loncası** | Lonca, görev panosu, kayıt için büyü ölçümü. |
| 12 | **Kırılan Küre** | Küre tepkisiz, sonra çatlar ve parçalanır. |
| 13 | **Elementsiz** | İkinci küre siyah ışıkla parçalanır. Geçici kayıt, güvensizlik. |
| 14 | **İlk Görev** | Bitki toplama, küçük canavar, taşla ilk öldürme, rahatsızlık, ilk emek parası. |
| 15 | **Çöp** | Alt kademe yaşamı; yaşlı adamın sözleri. |
| 16 | **Üç Kişilik Görev** | Hep reddedilir; Lys yanına gelir. |
| 17 | **Lys** | Tanışma, dil dersleri, kılıç hareketleri, ilk gerçek bağ. |
| 18 | **Eski Taş Yol** | Görev yolu, terk edilmiş karakol, semboller, içeriden tuhaf ses. |
| 19 | **Gölge** | İnsan biçimli karanlık yaratık. Lys'in büyüsü işe yaramaz; ölüm anında ışık kaybolur, gölgeler hareket eder. Görev başarısız. |
| 20 | **Tanrıların Unuttuğu** | Mum söner, gölge parmaklarında. Uzakta saray taşı çatlar: `LUX`/`NOX`. Son söz: *"Ben… ne oldum?"* |

> Taslak, yazım sırasında doğal gelişen **küçük** karakter/olay/diyalog eklemelerine açıktır; **ana hikâyeyle çelişmemesi şartıyla.**

### 15.1 Bölüm 2 (Ahır) hazır notları
*(Kullanıcı içerik verirse o geçerlidir.)*

**Bağ:** Bölüm 1'in son karesi: göz kapağı açılır, karanlık ahır, parlayan yaratık gözleri. Bölüm 2 **doğrudan bu kareden** devam eder (aynı ahır dokusu: tahta panolar, aralıklardan sızan soğuk ışık, hay, toz).

**Sahne önerisi (toplam 5):**
1. **Uyanış (0–24 sn):** yerde, kirli/yırtık kıyafet, güçsüz. Yaratıklar bakar (tuhaf çıkıntı/göz). Kötü koku **görüntüyle** (bulanıklık, burnunu kapatma). Ayağa kalkmaya çalışır.
2. **Kapı (24–38):** kapıdan ışığa çıkış, gözler kamaşır (beyaz parlama → netleşme). Poster karesi/başlık bu bölümde veya açılışta.
3. **Vadi (38–56):** geniş dağ/vadi manzarası, uzakta devasa uçan yaratıklar; **ejderha** süzülür. Karakter donar. Arkadan siluet + rim (gün ışığı rengi).
4. **Gerçek mi? (56–72):** yere dokunma, taş, rüzgâr, el yakın planı, rüzgârla savrulan toz. "Rüya değil."
5. **Uçurum (72–90):** uçurum kenarına yürüyüş, dünya geniş planda. Bitiş kartı: `DEVAM EDECEK / BÖLÜM 3 / Flatheim`.

**Ton:** şaşkınlık → korku → inkâr → kabul. Abartılı monolog yok.

---

## 16. TASARIM İLKELERİ (görsel kalite standardı)

1. **Bir sahne = bir duygu = bir baskın ışık.**
2. **Önce siluet, sonra detay.** Karakter ve yaratık net bir silüetle tanınır; iç detay minimaldir.
3. **Derinlik katmanları:** arka plan (gradyan) → orta (şehir/dağ/ahır) → ön (karakter/çerçeve) → atmosfer (toz/ışık/yıldız) → film dokusu (grain/vinyet). En az **4 katman** her sahnede.
4. **Her sahnede sürekli küçük hareket olmalı** (nefes, toz, ışık titremesi, bulut, yıldız). Tamamen donuk kare yok.
5. **Büyük hareket yavaş olmalı** (kamera zoom'u 10–16 sn). Ani/hızlı kesme yalnızca şok anlarında.
6. **Karşıtlık:** sıcak ışık vs soğuk gölge; küçük insan vs devasa ejderha/manzara. Ölçek farkı hissi önemli.
7. **Negatif alan:** ekranın alt kısmı altyazı için boş/karanlık kalır; önemli olay üstte.
8. **Duygusal doruklarda sade ol:** az öğe, tek odak (Bölüm 1'de ölüm anı = yalnız monitör).
9. **Düz renk blokları bırakma**; her büyük alan gradyan, doku veya ışık alır.
10. **Her bölümde en az 1 "ikonik kare"** (poster/sosyal medya küçük resmi olabilecek): net siluet + güçlü ışık.

---

## 17. KALİTE KONTROL

### 17.1 Bölüm 1'den Bölüm N'ye taşırken **mutlaka güncellenecek yerler**
| Konum | Ne yapılır |
|---|---|
| `<title>` | `Flatheim <Ad>` |
| `.head p` | `Sezon 1 · Bölüm N · <Ad> · 1:30` |
| `.mark` | `S1 · B<N>` |
| `svg aria-label` | Bölüm N + ad + "90 saniyelik animasyon" |
| `#hook` | yeni kanca (ya da kaldır; kanca her bölümde olmalı) |
| `#titleCard .ch/.nm` | `BÖLÜM N` / bölüm adı |
| `#cap` | yeni mekân/zaman |
| `#endcard` | `BÖLÜM N+1` / sonraki bölüm adı (sezon sonunda farklı) |
| `POSTER` | yeni ikonik kare saniyesi |
| `scenes` | yeni sahne tablosu |
| `CUES` | yeni anlatı |
| `BEATS`, `EV`, `CH`, `updateCont` | yeni ses planı (kalp bip'i **yalnızca hastane/monitör bölümlerinde**, her bölümde değil) |
| `overlays()` | yeni overlay zamanları |
| `ANIMS` | yeni `animSN` listesi |
| `#seek max` | `900` (90 sn × 10) sabit |

### 17.2 Kontrol listesi
- [ ] Süre tam 90 sn, `D=90`, etiket `0:00 / 1:30`, `seek max=900`.
- [ ] Sahneler boşluksuz ardışık, sınır noktalarında boş/siyah kare yok (kasıtlı karartma hariç).
- [ ] Zaman çizgisini her noktaya sürükleyince doğru sahne/overlay/altyazı çiziliyor; başka sahne sızmıyor.
- [ ] Son cue ≤ 89.6; bitiş kartı 86–90 arası görünür ve son karede de durur.
- [ ] Cue'lar çakışmıyor, 3–5 sn, altyazı ≤ 2 satır, taşma yok.
- [ ] İleri/geri sarmada `resync` çalışıyor; ses yağmuru/çift anlatım yok.
- [ ] Müzik ve Anlatıcı düğmeleri çalışıyor; Türkçe ses yoksa not çıkıyor, hata yok.
- [ ] Konsolda JS hatası yok; tek IIFE; `'use strict'`.
- [ ] Parçacıklar tohumlu `rng` (her açılışta aynı).
- [ ] 9:16 oran bozulmuyor; mobil güvenli alanlar (`env(safe-area-*)`) korunuyor.
- [ ] Altyazı alanında (alt %34) önemli görsel yok.
- [ ] Ana karakter güç gösterisi yapmıyor; isim yok; Işık/Gölge açıklanmıyor.
- [ ] Başlık, `S1 · B<N>`, sonraki bölüm adı doğru.
- [ ] Dış dosya/kütüphane yok (yalnız Google Fonts).

### 17.3 Sık yapılan hatalar
| Hata | Doğrusu |
|---|---|
| `Math.random()` ile parçacık | tohumlu `rng(seed)` |
| `setInterval/setTimeout` ile animasyon | yalnız `t`'ye bağlı `animSN(t)` |
| Animasyon durumunu global değişkende biriktirme | her karede `t`'den hesapla |
| `animSN` içinde yeni DOM öğesi üretme | kurulumda bir kez üret, karede yalnız attribute güncelle |
| Gradyan/filtre `id` çakışması | her `id` benzersiz; klonlanan öğelerden `id` sil (`s4frame` örneği) |
| Sahne `display:none` yönetimini unutma | `render()` içindeki mantık aynen kalsın |
| Ekran dışı zemin bırakmak | büyük dikdörtgenler `-90…450` taşsın, zoom'da boşluk görünmesin |
| Altyazıyı üstte/ortada göstermek | altyazı yeri sabit (`bottom:11%`) |
| Uzun, açıklayıcı anlatı | görüntüyle anlat, cue'yu kısa tut |
| Ses dosyası gömmek | tüm sesi Web Audio ile sentezle |
| Formatta "iyileştirme" | formatı değiştirme; yalnız içerik ekle |
| Kullanıcıya sormadan hikâye olayı değiştirmek | taslak/talimata sadık kal |

---

## 18. YAPILMAYACAKLAR (kesin)

- Çizim stilini, fontları, renk dilini, süreyi (90 sn), 9:16 oranını, kontrol çubuğunu, altyazı mantığını, ses yaklaşımını **değiştirme**.
- **Bölüm 1 dosyasını değiştirme.**
- Dış görsel, ses, video, kütüphane (framework, GSAP, Three.js vb.) **ekleme.**
- Ana karaktere **isim verme**; gücünü **açıklama**; Işık/Gölge'nin **anlamını söyleme.**
- Sezon 2–7 olaylarını **uydurma / ifşa etme.**
- Önceki bölümü uzun uzun **özetleme.**
- Kullanıcı istemeden bölüm olaylarını **değiştirme.**
- Gereksiz yere **yapay cliffhanger** ekleme.
- Görüntüsü hazır olmayan şeyi **sadece anlatıcıyla geçiştirme** (önce görsel çözüm).

---

## 19. DİĞER AGENT'LARA GÖREV VERİRKEN KISA ŞABLON

> "Flatheim Bölüm N (**<Ad>**) videosunu yap. **FLATHEIM_Seri_Rehberi.md** dosyasını baştan sona oku ve aynen uygula; `Flatheim_Son_Dilek.html` referans uygulamadır. İçerik: <olaylar>. Format, stil, süre (90 sn), renk dili, ses ve altyazı sistemi değişmeyecek. Teslim: tek HTML dosyası `Flatheim_Bolum_N_<Ad>.html`. Teslimden önce Bölüm 17 kontrol listesini uygula."
