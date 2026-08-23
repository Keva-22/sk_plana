# CONTENT-INVENTORY.md — inventura obsahu skplana.cz

> **Jak číst tento dokument:** Síťová politika prostředí blokovala přímý přístup na
> skplana.cz (`EGRESS_BLOCKED`), takže **`robots.txt` nebylo možné přečíst a web nebyl
> crawlován** (v souladu se zadáním). Inventura je proto sestavena výhradně z výsledků
> vyhledávače (titulky stránek, URL, úryvky) a ze zadání klienta. Co nebylo možné doložit,
> je označeno **[nezjištěno]**. Sekce B je závazný seznam stránek k ručnímu uložení —
> po jejich dodání se tato inventura zpřesní na úroveň jednotlivých textů a obrázků.
>
> Legenda sloupce „Verdikt": **převzít** (obsah migrovat, jen redakčně učesat) ·
> **přepsat** (stránka zůstává, obsah nový) · **vypustit** (bez náhrady / jen redirect).
>
> Legenda zdrojů: `[S]` = výsledek vyhledávače (titulek/URL/úryvek, srpen 2026) ·
> `[Z]` = zadání klienta · `[T]` = katalogy třetích stran (fotbalunas.cz, sportvokoli.cz,
> sportmap.cz — nutno ověřit) · `[N]` = [nezjištěno]

## A. Inventura stránek

| Stránka (URL) | Současný obsah | Verdikt | Potřebné podklady | Zdroj |
|---|---|---|---|---|
| Úvod — `/` | Existuje, titulek „Úvod - SK Planá". Skladba homepage (hero, widgety) [nezjištěno]. | přepsat | hero fotky areálu/týmu v tiskové kvalitě, aktuální znak klubu (SVG/zdrojové PNG) | `[S]` |
| O klubu — `/o-klubu/` | Existuje. Úryvky: založení 1938 partou přátel (Jan Tejml, Jindřich Zevl, Ludva Diviš, Václav Lerch, Tomáš Čermák, František Pilečka), klub provozuje všechny kategorie dospělých i mládeže. | převzít (redakčně učesat, doplnit výbor a poslání) | text ověřit proti uložené stránce; jména a funkce výboru od klubu | `[S]` |
| Historie — `/historie/` | Existuje. Úryvky: začátky ve Vrábči a okolí, po válce hřiště v Plané na dnešním místě, 1955–1968 název Sokol Planá, titul v sezóně 1979/80 (okres/kraj — upřesnit), nejlepší umístění A týmu: 7. místo I.A 2017/18, poté 4. místo 2019/20. | převzít (ověřit letopočty, doplnit fotky z kroniky) | historické fotografie, kronika; křížově ověřit s legacy webem skplana.estranky.cz/clanky/klub.html | `[S]` |
| Areál — `/areal/` | Existuje. Úryvky: nová UMT s osvětlením na kraji Plané, zázemí (kabiny, klubovna, občerstvení, regenerace), tribuna pro 120 diváků; slavnostní otevření 2. srpna (rok [nezjištěno]). | převzít + rozšířit (mapka, jak se k nám dostat, parkování) | aktuální fotky areálu, letecký snímek, GPS/adresa | `[S]` |
| Aktuality — výpis (URL výpisu [nezjištěno]; WP posty ve tvaru `/[slug]/` v rootu) | Web je žurnalisticky živý: doloženy články z 28. 3. 2026 (hodnocení kategorií: starší přípravka, mladší přípravka, A muži) a 23. 6. 2026 (dopis rodičům, hráčům, trenérům); dále např. „Hodnocení podzimní části sezóny: Mladší žáci (ročníky 2013–2014) — týmy A a B". | převzít (migrovat do `/aktuality/`, min. poslední 2 sezóny) | úplný seznam URL článků z ručního uložení (sitemap/výpis), fotky u článků | `[S]` |
| Týmové stránky — pod `/klub/…` (doložena `/klub/sk-plana/`; ostatní URL [nezjištěno]) | Struktura týmových stránek [nezjištěno]. Dle zadání obsahují **demo texty šablony** („Some description text for this item"). Kategorie doložené nepřímo z článků: A muži, mladší žáci (A i B), starší přípravka, mladší přípravka; B tým dle zadání. Úplný výčet [nezjištěno]. | přepsat kompletně (datově z CMS) | seznam všech týmů + soutěží od klubu, soupisky, realizační týmy, tréninkové časy, týmová fota | `[Z]` `[S]` |
| Profily hráčů — `/hrac/[slug]/` (doloženy `/hrac/jan-pinter/`, `/hrac/jiri-carda/`) | Reálná data, ne demo: u Jana Pintéra úryvek uvádí nar. 18. 6. 1985, předchozí kluby SK Dynamo ČB, Jiskra Třeboň, FK Táborsko, SK Strakonice, Slavoj Český Krumlov. Počet profilů celkem [nezjištěno]. | převzít (migrovat strukturovaně do kolekce Players; URL `/hrac/…` zachovat) | úplný seznam profilů, portrétní fotky, souhlasy hráčů (GDPR) | `[S]` |
| Partneři/sponzoři (URL [nezjištěno]) | Existence stránky ani seznam partnerů se vyhledáváním nepodařily doložit. **Žádného partnera si nevymýšlím.** | přepsat (postavit nově) | závazný seznam partnerů + loga (SVG/PNG s průhledností) + úrovně partnerství od výboru | `[N]` |
| Kontakt (URL [nezjištěno]) | Dle zadání obsahuje **demo junk šablony**: `info@example.com`, „8 (800) 123-45-67". Katalogy třetích stran uvádějí adresu Planá 59, 370 01 a tel. +420 777 773 430 — **nutno ověřit klubem**. | přepsat (jen ověřené údaje: adresa, IČO, č. účtu, kontaktní osoby) | oficiální kontakty od výboru, fakturační údaje | `[Z]` `[T]` |
| Nábor (samostatná stránka [nezjištěno]) | Katalogy třetích stran uvádějí nábor mládeže v pátek od 17:00 na hřišti — ověřit. | přepsat (nová stránka `/nabor/` s formulářem) | potvrzené časy náborů, kontakt na šéftrenéra mládeže | `[T]` |
| Legacy články — `/clanky/*.html` na www.skplana.cz (doloženy: „Pojeďte na Setkání Planých!", „Halový memoriál Jana Vobořila…", „Tomáš Pintér") | Stará struktura (eStránky-styl) stále v indexu vyhledávačů; zda URL dosud fungují [nezjištěno]. | vypustit (případné perly jednorázově migrovat do `/aktuality/`; jinak 301 na `/aktuality/`) | rozhodnutí klubu, které staré články mají archivní hodnotu | `[S]` |
| Legacy web — `skplana.estranky.cz` (mimo doménu) | Starý klubový web, stránka „KLUB" s historií. Není součástí přestavby. | vypustit (použít jen jako pramen pro Historie) | — | `[S]` |
| robots.txt, sitemap.xml | Obsah [nezjištěno] — přístup blokován politikou prostředí. | — (vstup pro mapu přesměrování) | uložit ručně (viz sekce B) | `[N]` |

**Demo junk k odstranění (dle zadání `[Z]`):** `info@example.com`, telefon „8 (800) 123-45-67",
výplňové texty „Some description text for this item" na týmových stránkách. Na novém webu nesmí
zůstat jediný výskyt; kontrola bude součástí QA (fáze 5) fulltextovým hledáním v CMS.

**Obrázky a výkon [nezjištěno]:** kvalitu fotografií, chování na mobilu a výkonnostní problémy
současného webu nebylo možné bez přístupu posoudit. Doplní se z ručně uložených stránek
(sekce B) — u WordPress+ThemeREX je typickým rizikem těžký JS/CSS balast šablony a
neoptimalizované obrázky, ale **konkrétní nález zatím žádný netvrdím**.

## B. Seznam k ručnímu uložení (fáze 0 — práce klienta)

Protože crawling nebyl možný, prosím o uložení následujícího. Postup pro každou stránku:
**(1)** v prohlížeči Ctrl+S → „Webová stránka, kompletní", **(2)** celostránkový screenshot
z desktopu i mobilu, **(3)** u obrázků, které stojí za převzetí, uložit originál
(pravý klik → Uložit obrázek).

1. `https://www.skplana.cz/robots.txt` a `https://www.skplana.cz/sitemap.xml`
   (+ případně `/sitemap_index.xml` — WordPress/SEO pluginy dělí sitemapu) — **nejdůležitější
   položka**: dá úplný seznam URL a pravidla crawlování.
2. `https://www.skplana.cz/` (úvod) — desktop i mobil.
3. `https://www.skplana.cz/o-klubu/`
4. `https://www.skplana.cz/historie/`
5. `https://www.skplana.cz/areal/`
6. Výpis aktualit (URL dle menu) + **5–10 reprezentativních článků** vč. jednoho s galerií.
7. **Screenshot rozbaleného hlavního menu** (desktop i mobilní hamburger) — z něj se odvodí
   úplný seznam týmových a ostatních stránek, který teď je [nezjištěno].
8. Všechny týmové stránky z menu (vč. doložené `https://www.skplana.cz/klub/sk-plana/`).
9. Všechny profily hráčů `/hrac/…` (minimálně: `jan-pinter`, `jiri-carda` + ostatní z výpisu).
10. Stránka partnerů/sponzorů (pokud v menu existuje) — jinak napsat „neexistuje".
11. Kontaktní stránka (kvůli dokumentaci demo junku i případným reálným údajům).
12. **Znak klubu ve zdrojové kvalitě** (SVG, PDF nebo PNG ≥ 1000 px) — nutný pro kalibraci
    barevné palety; z webu stačí prozatím uložený obrázek, ideál je zdrojový soubor od klubu.
13. Pokud existují: stanovy/dokumenty ke stažení, výroční zprávy, ceník pronájmu areálu.

Bonus (není blokující): export „Nástroje → Export" z WP adminu, pokud by přístup nakonec byl —
zkrátil by fázi migrace obsahu o většinu ručního přepisování.
