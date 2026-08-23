# PLAN.md — Nový web SK Planá (fáze 1: audit a plán)

> **Stav dokumentu:** návrh k projednání. Žádný kód zatím nevznikl (záměrně — fáze 1 = pouze dokumenty).
>
> **Metodická poznámka (důležité):** Prostředí, ve kterém tento audit vznikal, má síťovou
> politiku, která **blokuje přímý přístup na skplana.cz i fotbal.cz** (egress proxy vrací
> `EGRESS_BLOCKED`). Nepodařilo se proto přečíst ani `robots.txt` — a podle zadání („pokud
> nelze číst robots.txt / crawling není povolen, necrawlovat") **neproběhl žádný crawling
> živého webu**. Všechna tvrzení o současném webu jsou doložena buď (a) výsledky vyhledávače
> (titulky, URL, úryvky), (b) zadáním od klienta, nebo jsou označena **[nezjištěno]**.
> Seznam stránek k ručnímu uložení je v CONTENT-INVENTORY.md.

---

## 1. Potvrzení stacku

Zadaný stack **potvrzuji — žádný z bodů není blokátor**:

| Rozhodnutí | Stav | Poznámka |
|---|---|---|
| Next.js 15 (App Router) + TypeScript | ✅ OK | — |
| Tailwind CSS | ✅ OK | — |
| Payload CMS 3, admin na `/admin` | ✅ OK | Payload 3 běží nativně uvnitř Next.js App Routeru, jeden repozitář, jeden deploy. |
| SQLite lokálně / Postgres v produkci | ✅ OK s výhradou | Payload má oficiální adaptéry `@payloadcms/db-sqlite` i `@payloadcms/db-postgres`. **Výhrada (ne blokátor):** dva různé adaptéry = dvě sady migrací a drobné rozdíly v typech sloupců. Doporučený režim: vývoj schématu na SQLite v „dev push" režimu, ale migrace generovat a testovat proti Postgres (lokálně stačí Postgres v Dockeru před každým nasazením schématu). |
| Vercel free (Hobby) tier | ✅ OK s výhradou | Dvě věci Vercel free tier neřeší sám o sobě: (1) **Postgres** — nutná externí služba (typicky Neon free tier; Vercel Postgres je také Neon). (2) **Úložiště médií** — souborový systém serverless funkcí je efemérní, uploady v Payloadu musí jít do blob storage (Vercel Blob free tier, případně Cloudflare R2/S3). Obojí vyžaduje založení účtu/služby → **neprovedeno, čeká na odsouhlasení** (viz otevřené otázky). |
| Pouze čeština, `lang="cs"`, česká URL, diakritika | ✅ OK | Slugy bez diakritiky (standard), texty s plnou diakritikou. Formát dat `d. M. yyyy` (`Intl.DateTimeFormat('cs-CZ')`). |
| Mobile-first + korektní zobrazení na projektoru 1920×1080 | ✅ OK | Řešeno v designové sekci (kontrastní režim „projekce" = velké titulky, max-width kontejner 1200–1280 px, žádný text pod 16 px). |

Web se staví jako **samostatný projekt**, který se živého webu nijak nedotýká. Přepnutí domény
je až poslední krok po schválení výborem (a je kdykoli vratné zpět na starý web).

---

## 2. Informační architektura (strom stránek, české slugy)

Zásady: max. 2 úrovně v hlavní navigaci, týmy a hráči jsou datově řízené (obsah z CMS, žádné
ručně zakládané stránky), zpravodajství na jednom místě (dnes jsou články rozházené mezi
WP posty v rootu a starými `/clanky/*.html`).

```
/                          Úvod (hero, nejbližší zápas + poslední výsledek, aktuality, partneři)
/aktuality/                Aktuality — výpis (filtr podle týmu)
/aktuality/[slug]/         Detail článku
/tymy/                     Přehled všech týmů (karty po kategoriích)
/tymy/[slug]/              Detail týmu: soupiska, realizační tým, tréninky, zápasy, tabulka
                           (slugy dle skutečných kategorií, např. /tymy/muzi-a/, /tymy/muzi-b/,
                           /tymy/mladsi-zaci/, /tymy/starsi-pripravka/, /tymy/mladsi-pripravka/ …
                           — úplný seznam kategorií [nezjištěno], doplní se při sběru obsahu)
/hrac/[slug]/              Profil hráče (slug `/hrac/…` záměrně zachován ze starého webu,
                           aby indexované profily nevedly na 404)
/zapasy/                   Rozpis a výsledky napříč týmy (výchozí: Muži A)
/klub/                     O klubu (poslání, výbor, kontaktní osoby)
/klub/historie/            Historie (od r. 1938)
/klub/areal/               Areál (hřiště, UMT s osvětlením, zázemí, jak se k nám dostat)
/nabor/                    Nábor mládeže (info + náborový formulář)
/partneri/                 Partneři a sponzoři
/kontakt/                  Kontakt (adresa, mapa, fakturační údaje, kontaktní formulář)
```

Mimo hlavní strom: `/gdpr/` (zásady zpracování osobních údajů — nutné kvůli formulářům a
fotkám mládeže), `/sitemap.xml`, `/robots.txt`, error stránky česky (404 „Stránka nenalezena").

**Přesměrování ze starého webu (301):** `/o-klubu/ → /klub/`, `/historie/ → /klub/historie/`,
`/areal/ → /klub/areal/`, `/hrac/* → beze změny`, WP posty v rootu → `/aktuality/[slug]/`,
staré `/clanky/*.html` → `/aktuality/` (nebo konkrétní článek, pokud se obsah migruje).
Přesná mapa vznikne až z ručně uloženého seznamu URL (fáze 0).

---

## 3. Payload CMS — kolekce a pole

Názvy kolekcí v kódu anglicky (konvence Payloadu), **labely v adminu česky**, aby admin
mohl obsluhovat kdokoli z klubu. Navíc ke kolekcím níže: vestavěné `Users` (role `admin`,
`editor`) a `Media` (povinný `alt`), globály `SiteSettings` (název, znak, adresa, IČO,
kontakty, sociální sítě) a `Navigation` (hlavní menu + patička).

### News (Aktuality)
| Pole | Typ | Poznámka |
|---|---|---|
| `title` | text, required | Titulek |
| `slug` | text, unique, auto z titulku | bez diakritiky |
| `publishedAt` | date | řazení výpisu |
| `excerpt` | textarea | perex pro výpis a OG description |
| `coverImage` | upload → Media | úvodní fotka |
| `content` | richText (Lexical) | text článku, vložené obrázky/galerie |
| `team` | relationship → Teams, optional | filtr „aktuality týmu" |
| `_status` | drafts/versions zapnuto | koncept vs. publikováno |

### Teams (Týmy)
| Pole | Typ | Poznámka |
|---|---|---|
| `name` | text, required | např. „Muži A" |
| `slug` | text, unique | `/tymy/[slug]/` |
| `category` | select | muži / dorost / žáci / přípravka / školička — přesné hodnoty dle skutečného stavu klubu |
| `competition` | group: `name` text, `facrUrl` text | např. „I.A třída Jihočeský kraj" + odkaz na fotbal.cz |
| `description` | richText | představení týmu (nahradí demo texty) |
| `teamPhoto` | upload → Media | společné foto |
| `staff` | array: `role` text, `person` text | trenér, asistent, vedoucí |
| `trainings` | array: `day` select, `time` text, `place` text | tréninkové časy |
| `standings` | group (volitelné): `rows` array (pozice, tým, Z, V, R, P, skóre, body), `updatedAt` date | ruční snímek tabulky; pokud by údržba nezvládala, pole se nevyplňuje a web ukáže jen odkaz na fotbal.cz |
| `order` | number | pořadí na přehledu týmů |

### Players (Hráči)
| Pole | Typ | Poznámka |
|---|---|---|
| `firstName`, `lastName` | text, required | |
| `slug` | text, unique | `/hrac/[slug]/` |
| `photo` | upload → Media | |
| `team` | relationship → Teams | |
| `position` | select | brankář / obránce / záložník / útočník |
| `number` | number, optional | dres |
| `birthDate` | date, optional | **GDPR:** u mládeže zveřejňovat max. ročník, a to jen se souhlasem zák. zástupců |
| `formerClubs` | array: text | doloženo, že stávající profily toto obsahují (např. Jan Pintér) |
| `bio` | richText, optional | |
| `active` | checkbox | skrytí bez mazání (odchod hráče) |

### Matches (Zápasy)
| Pole | Typ | Poznámka |
|---|---|---|
| `team` | relationship → Teams, required | čí zápas |
| `season` | select | „2025/2026" … |
| `competition` | text | předvyplněno z týmu, přepsatelné (pohár, turnaj) |
| `round` | number, optional | kolo |
| `kickoff` | date+time, required | výkop |
| `opponent` | text, required | soupeř (volný text — soupisky soupeřů nespravujeme) |
| `isHome` | checkbox | domácí/venku |
| `venue` | text, optional | jen když se hraje jinde než standardně |
| `scoreFor`, `scoreAgainst` | number, optional | prázdné = neodehráno |
| `status` | select | plánováno / odehráno / odloženo / zrušeno |
| `report` | relationship → News, optional | odkaz na reportáž |
| `facrUrl` | text, optional | detail utkání na fotbal.cz |

### Sponsors (Partneři)
| Pole | Typ | Poznámka |
|---|---|---|
| `name` | text, required | |
| `logo` | upload → Media, required | ideálně SVG/PNG s průhledností |
| `url` | text, optional | proklik |
| `tier` | select | generální / hlavní / partner |
| `order` | number | pořadí v rámci úrovně |
| `active` | checkbox | archivace bez mazání |

### Pages (Stránky)
Statické stránky (`/klub/`, `/klub/historie/`, `/klub/areal/`, `/nabor/`, `/gdpr/` …).
| Pole | Typ | Poznámka |
|---|---|---|
| `title` | text, required | |
| `slug` | text, unique | vč. vnořených cest (`klub/historie`) |
| `hero` | group: nadpis, podnadpis, obrázek | |
| `layout` | blocks | povolené bloky: `richText`, `gallery`, `ctaBanner` (např. „Přijď na nábor"), `contactBlock` |
| `_status` | drafts/versions | |

### FormSubmissions (Odeslané formuláře)
| Pole | Typ | Poznámka |
|---|---|---|
| `formType` | select | kontakt / nábor |
| `name`, `email`, `phone` | text | telefon volitelný |
| `message` | textarea | |
| `consent` | checkbox, required | souhlas se zpracováním (GDPR) |
| `createdAt` | auto | |

Přístup: zápis pouze přes server action s honeypotem a rate-limitem; čtení jen admin/editor.
E-mailová notifikace klubu o novém záznamu (Resend/SMTP — služba k odsouhlasení, viz otázky).

---

## 4. Designový směr (jeden, ne varianty)

**Koncept: „Modrý balet z Plané" — klubová modrá jako hlavní vypravěč.** Čistý, sebevědomý
vzhled okresního klubu, který to myslí vážně: tmavě modré plochy s bílou typografií pro
hero/patičku/výsledkové prvky, světlý obsah s vysokým kontrastem. Žádné dekorativní efekty
ThemeREX šablony — obsah (zápasy, lidé, fotky z areálu) je design.

### Barvy
> **Kalibrace nutná:** znak klubu se nepodařilo z tohoto prostředí stáhnout — přesné odstíny
> **[nezjištěno]**. Níže je pracovní paleta postavená na klubové modré; před implementací se
> doladí kapátkem podle dodaného znaku (fáze 0). Struktura palety se tím nemění.

| Token | Hex (pracovní) | Použití |
|---|---|---|
| `plana-navy` | `#0D2250` | hero, patička, výsledkové pruhy, tmavé plochy |
| `plana-royal` | `#1B4AAE` | primární barva: tlačítka, aktivní stavy, akcenty |
| `plana-sky` | `#3B82F6` | odkazy, hover, sekundární akcenty na tmavé ploše |
| `plana-ice` | `#EEF3FC` | pozadí střídaných sekcí |
| `white` | `#FFFFFF` | základní pozadí obsahu |
| `ink` | `#101828` | text |
| `ink-soft` | `#475467` | sekundární text, popisky |
| funkční | zelená `#12B76A`, žlutá `#EAB308`, červená `#E5484D` | výhra/remíza/prohra, stavy formulářů — nejsou brandové |

Kontrast všech kombinací text/pozadí min. WCAG AA (4.5:1); `plana-royal` na bílé i bílá na
`plana-navy` vyhovují.

### Typografie (obojí Google Fonts, latin-ext = plná česká diakritika, self-host přes `next/font`)
- **Titulky: Barlow Condensed** (SemiBold/Bold, jen pro H1–H3 a čísla skóre) — kondenzovaný
  sportovní charakter, skvěle nese VELKÉ TITULKY i výsledky.
- **Text a UI: Inter** (Regular/Medium/SemiBold) — výborná čitelnost na mobilu i projektoru.

### Typografická škála (mobile-first; na ≥1024 px se display stupně zvětšují přes `clamp()`)
| Stupeň | Mobil | Desktop/projekce | Použití |
|---|---|---|---|
| display | 40 px | až 64 px | H1 hero, skóre |
| h1 | 32 px | 44 px | nadpis stránky |
| h2 | 24 px | 32 px | sekce |
| h3 | 20 px | 24 px | karty |
| body | 16 px | 18 px | text (nikdy méně než 16 px) |
| small | 14 px | 14 px | metadata, popisky |

Řádkování: 1.15 u display/H1, 1.5 u textu. Šířka textového sloupce max. ~70 znaků.

### Spacing a layout
- Tailwind škála (základ 4 px); vertikální rytmus sekcí: **40/64 px mobil, 64/96 px desktop**.
- Kontejner `max-w-[1240px]`, boční odsazení 16 px mobil / 32 px desktop — na projekci
  1920×1080 obsah nedegraduje do „nudle" přes celou šířku a titulky zůstávají čitelné z dálky.
- Karty: rádius 12 px, jemný stín, žádné glow/parallax efekty.
- Komponenta „zápasový pruh" (soupeř, datum, skóre, V/R/P badge) je základní stavební kámen
  úvodu, detailu týmu i výpisu zápasů — navrhne se jednou, použije všude.

---

## 5. Fáze výstavby a odhad pracnosti

Odhady v člověkodnech (MD) pro jednoho vývojáře; „fáze 0" je práce klienta, ne kód.

| Fáze | Obsah | Odhad |
|---|---|---|
| **0. Sběr podkladů** (klient) | Ruční uložení stránek dle seznamu v CONTENT-INVENTORY.md, znak klubu ve zdrojové kvalitě, seznam týmů/partnerů/kontaktů od výboru, potvrzení otevřených otázek | 0,5–1 MD |
| **1. Základ projektu** | Next.js 15 + TS + Tailwind + Payload 3, SQLite lokálně, kolekce a globály dle kap. 3, role, seed skript s ukázkovými daty, CI (lint + typecheck) | 2–3 MD |
| **2. Kostra frontend** | Layout, navigace, patička, design tokeny, Úvod, Aktuality (výpis + detail), Pages renderer (`/klub/*`, `/nabor/`), 404 | 3–4 MD |
| **3. Sportovní obsah** | Týmy (přehled + detail), Hráči, Zápasy (rozpis/výsledky, zápasový pruh, volitelná tabulka) | 3–4 MD |
| **4. Partneři a formuláře** | Partneři, Kontakt + kontaktní/náborový formulář (FormSubmissions, honeypot, e-mail notifikace), GDPR stránka | 1,5–2 MD |
| **5. Migrace obsahu a QA** | Naplnění CMS reálným obsahem z fáze 0, mapa přesměrování, SEO (metadata, OG, sitemap), Lighthouse, test na mobilech a projekci 1920×1080, korektura češtiny | 2–3 MD |
| **6. Produkce** | Postgres (migrace ze SQLite schématu), blob storage médií, deploy na Vercel, doména `skplana.cz` — plán přepnutí DNS + rollback | 1 MD |
| | **Celkem (bez fáze 0)** | **≈ 12–17 MD** |

Milník pro výbor: po fázi 3 existuje klikatelná verze s reálným vzhledem a ukázkovými daty —
vhodný okamžik pro prezentaci, ještě před migrací plného obsahu.

---

## 6. Rizika a otevřené otázky

1. **Podklady ze živého webu** — bez ručně uložených stránek (fáze 0) nelze dokončit
   inventuru obsahu ani mapu přesměrování; audit v tomto dokumentu je limitován blokovaným
   síťovým přístupem (viz metodická poznámka).
2. **Produkční služby** — Postgres (Neon free) a úložiště médií (Vercel Blob) + e-maily
   z formulářů (Resend/SMTP): vše vyžaduje založení účtů. Podle zadání **nezakládám nic bez
   odsouhlasení**.
3. **Zápasová data** — oficiální strojově čitelný zdroj FAČR neexistuje (viz
   DATA-SOURCES.md); primární cesta je ruční zadávání v CMS (~2 min/zápas). Import jako
   volitelný doplněk později.
4. **GDPR u mládeže** — fotky a jména dětí jen se souhlasem zákonných zástupců; proces
   souhlasů musí potvrdit výbor.
5. **Migrace SQLite → Postgres** — řízené riziko; migrace se generují a testují proti
   Postgres před nasazením (viz kap. 1).
