# DATA-SOURCES.md — zápasová data soutěží FAČR (fotbal.cz, is.fotbal.cz)

> **Metodická poznámka:** Síťová politika prostředí blokovala přímý přístup na fotbal.cz,
> is.fotbal.cz i skplana.cz (`EGRESS_BLOCKED` na egress proxy). Přímá inspekce stránek,
> hlaviček a případných JSON endpointů proto **neproběhla**. Zjištění níže pocházejí
> z výsledků vyhledávače (srpen 2026) — u každého je uveden zdroj. Nic z toho není
> domněnka o existenci API: kde zdroj není, píšu to výslovně.

## 1. Verdikt (shrnutí)

**Oficiální strojově čitelný zdroj dat FAČR (API, export, iCal, RSS, oficiální embed
widget) nebyl nalezen** — a komunitní zdroje shodně uvádějí, že neexistuje. Neoficiální
řešení existují, ale stojí na screen-scrapingu a jsou křehká (jedno z nich vývoj ukončilo
právě kvůli změnám designu fotbal.cz).

**Primární cesta pro nový web: ruční zadávání v Payload CMS** (kolekce Matches, viz PLAN.md
kap. 3). Reálná zátěž: rozpis celé sezóny se nalije jednou (~30–60 min na tým), pak
~2 minuty po zápase na doplnění skóre. Volitelně ruční snímek tabulky (pole `standings`
na týmu) nebo jen odkaz na fotbal.cz.

**Import jako volitelný doplněk později** (fáze 7+, mimo rozsah): až bude síťově
neomezené prostředí, ověřit stav neoficiálních řešení (viz kap. 3) a případně napsat
vlastní import — vždy jako *doplněk plnící tentýž CMS model*, aby web na scrapingu nikdy
nezávisel. Scraper se v této fázi nestaví (dle zadání).

## 2. URL — co bylo kontrolováno a jak

### 2a. Pokusy o přímý přístup (všechny zablokovány prostředím, nikoli cílovým serverem)

| URL | Výsledek |
|---|---|
| `https://www.skplana.cz/robots.txt` | `EGRESS_BLOCKED` (egress proxy; přes curl i WebFetch) |
| `https://skplana.cz/robots.txt` | `EGRESS_BLOCKED` |
| `https://www.fotbal.cz/robots.txt` | `EGRESS_BLOCKED` |
| `https://web.archive.org/web/2026/https://www.skplana.cz/` | fetch selhal (archiv nedostupný z prostředí) |

### 2b. Zdroje identifikované vyhledáváním (bez přímého ověření obsahu)

| URL | Co to je (dle výsledků vyhledávání) |
|---|---|
| `https://www.fotbal.cz/souteze/club/club/c0c009e1-e646-44b8-9394-5959163592e3` | Klubová stránka SK Planá na fotbal.cz (GUID klubu — užitečné pro odkazy z nového webu) |
| `https://www.fotbal.cz/souteze/subjekty/subjekt/265` | Rozcestník soutěží Jihočeského kraje |
| `https://souteze.fotbal.cz/vysledky-a-tabulky/p316` | Výsledky a tabulky na fotbal.cz (HTML, bez dokumentovaného exportu) |
| `https://is.fotbal.cz/public/souteze/prehled-soutezi.aspx?sport=fotbal` | Veřejný přehled soutěží v IS FAČR (ASP.NET aplikace; žádný dokumentovaný veřejný datový endpoint nenalezen) |
| `https://www.fotbal.cz/facr/mobilniaplikace-faq/p570` | FAQ mobilní aplikace FAČR — potvrzuje, že data tečou z IS FAČR do vlastní aplikace; o veřejném API ani zmínka |
| `https://webtrh.cz/diskuse/stahovani-dat-vysledku-fotbalove-souteze/` | Komunitní diskuse: fotbal.cz oficiální API nenabízí; řeší se scraping |
| `https://scortes.rozpisyzapasu.cz/` | „Scortes — neoficiální API pro fotbal.cz": embed HTML výsledků na klubový web, technicky screen-scraping; dle vlastního popisu **při změně designu fotbal.cz přestává fungovat a vývoj byl ukončen** |
| `https://github.com/mikealdo/fotbal-cz-api` | „Unofficial API for fotbal.cz statistics and results" — neoficiální projekt; stav údržby [nezjištěno] (repozitář mimo přístup tohoto prostředí) |
| `https://fotbalunas.cz/klub/176` | Profil SK Planá na fotbalunas.cz (portál třetí strany s výsledky amatérského fotbalu) |
| `https://fotbalunas.cz/soutez/111` | Jihočeský kraj, 1.A třída sk. A — tabulky/rozlosování na fotbalunas.cz (zda A tým SK Planá hraje sk. A, nebo sk. B, **[nezjištěno]** — ověřit) |

### 2c. Co nebylo nalezeno vůbec (hledáno cíleně)

- **Oficiální API / datový export FAČR** — žádná dokumentace, žádný vývojářský portál.
- **iCal feed** rozpisu zápasů klubu či soutěže na fotbal.cz — nenalezen.
- **RSS** výsledků/tabulek na fotbal.cz — nenalezen (RSS mají jen weby jednotlivých klubů).
- **Oficiální embeddable widget** fotbal.cz pro klubové weby — nenalezen.

## 3. Hodnocení možností

| Cesta | Hodnocení |
|---|---|
| **Ruční zadávání v CMS** | ✅ **Primární.** Nulová závislost na třetích stranách, data pod kontrolou klubu, funguje i pro poháry a turnaje, které v IS nejsou. Náklad ~2 min/zápas. |
| Odkazy na fotbal.cz / fotbalunas.cz | ✅ Doplněk zdarma: u týmu a zápasu vést `facrUrl`, tabulku řešit odkazem, dokud se nepotvrdí ruční údržba snímku tabulky. |
| Scortes embed | ⚠️ Nedoporučeno jako závislost: cizí HTML vložené do webu, scraping, vývoj ukončen. |
| `mikealdo/fotbal-cz-api` a vlastní import | ⚠️ Jen jako budoucí volitelný doplněk (fáze 7+): ověřit funkčnost, právní stránku (podmínky užití fotbal.cz) a robustnost; nikdy jako jediný zdroj dat. |
| Oficiální cesta: napsat na FAČR / JčKFS | 💡 Stojí za pokus (nulové náklady): dotaz, zda pro klubové weby existuje datové řešení. **Neodesláno — dle zadání se bez odsouhlasení nikam neregistruji ani nepíšu.** |

## 4. Co ověřit, až bude přístup (nebo ručně)

1. `robots.txt` + skutečné chování stránek soutěží na fotbal.cz (zda frontend interně volá
   JSON endpointy — časté u moderních webů; **existenci nepředpokládám, jen ji ověřit**).
2. Přesná soutěž a skupina A týmu SK Planá v sezóně 2026/27 (fotbal.cz rozcestník JčK).
3. Stav projektu `mikealdo/fotbal-cz-api` (poslední commit, kompatibilita).
4. Zda fotbalunas.cz nabízí klubům widget/export (na webu třetí strany [nezjištěno]).
