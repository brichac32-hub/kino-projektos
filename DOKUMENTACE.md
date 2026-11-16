# Technická dokumentace — Kino Obzor

## Cíl a návaznost na wireframy

Web zpřístupňuje program kina a vede návštěvníka od výběru události přes detail až k rezervaci sedadla. Z původního návrhu zachovává pořadí: hlavička s navigací a účtem → filmový banner → karusel se třemi plakáty → kategorie → dny → nadpis aktuálního programu → vyhledávání a filtry → seznam projekcí → patička. Přihlášení a registrace se otevírají jako dialogy nad ztmaveným pozadím. Tablet zachovává náhled vedle údajů; úzké telefony skládají obsah pod sebe.

Vzhled je inspirovaný principy webů multiplexů CineStar a Cinema City: tmavá paleta, výrazné obrazové plochy, kontrastní červená tlačítka pro vstupenky a kondenzované plakátové nadpisy. Projekt má vlastní značku, texty a fotografie. Pro běžné texty a navigaci používá Roboto (řezy 400, 600 a 700), pro nadpisy Saira Semi Condensed Bold. Obě písma jsou uložená lokálně s licencemi OFL. Veškeré chování nadále zajišťuje původní JavaScript.

| Prvek wireframu | Zpracování ve výsledném webu |
| --- | --- |
| Hlavička | Logo vlevo, navigace uprostřed, účet a vyhledávání vpravo |
| Hlavní banner | Široká obrazová plocha s přepínáním tipů |
| Karusel | Tři portrétní plakáty a šipky po stranách |
| Program | Kategorie, dny, nadpis, filtry, projekce a vstupenky |
| Přihlášení a registrace | Modální okno nad ztmavenou stránkou |
| Tablet | Tři plakáty, náhledy vedle informací o projekci |
| Mobil | Sbalené menu, jeden posuvný plakát, úzké karty pod sebou |
| Patička | Adresa, odkazy, kontakt a otevírací doba |

## Použité technologie

| Technologie | Použití |
| --- | --- |
| HTML5 | Sémantická struktura, osm samostatných stránek, formuláře, dialog, details/summary |
| CSS3 | Proměnné, Grid, Flexbox, media queries, clamp(), tiskové styly |
| JavaScript ES2022+ | Události, práce s DOM, filtry, rezervace, validace, formátování dat |
| Web Storage API | Účty a rezervace v localStorage, přihlášení v sessionStorage |
| Web Crypto API | Náhodné identifikátory a PBKDF2 pro testovací hesla |
| URLSearchParams | Výběr filmu, projekce a předání vyhledávání mezi stránkami |
| Intl / Date | České datum, názvy dní a formátování měny |
| IntersectionObserver | Odhalování sekcí při vstupu do zorného pole a zastavení neviditelného banneru |
| MutationObserver | Animace nových výsledků filtrů a pozastavení banneru při otevřeném dialogu |
| Web Animations API | Prolínání fotografií, pohyb obrazu, přechody karuselu a indikátor času |

Framework, knihovna a build nástroj nejsou potřeba. Tři skripty se načítají atributem `defer` v pořadí `data.js`, `app.js`, `motion.js`. `data.js` vystaví `window.KinoData`; ostatní proměnné uzavírá funkce IIFE, aby neznečišťovaly globální prostor. `motion.js` přidává volitelnou vrstvu animací nad existující funkce.

## Animace a přístupnost pohybu

Hvězdné pozadí tvoří dvě pevně umístěné pseudovrstvy `body::before` a `body::after` s radiálními gradienty. Izolovaný kontext vrstvení drží hvězdy pod obsahem, `pointer-events: none` zachovává ovládání stránky. Jemné třpytění mění pouze průhlednost dekorativní vrstvy. Nastavení omezeného pohybu ponechá hvězdy statické; v tisku se obě vrstvy skryjí. Není potřeba další JavaScript ani obrázek.

Banner automaticky střídá tři tipy po 6,5 sekundách. Uživatel může střídání kdykoli pozastavit a spustit. Při najetí přesného ukazatele, práci klávesnicí s obsahem banneru, otevřeném modálním okně, skryté kartě nebo opuštění zorného pole se časovač zastaví. Před zakrytím původní fotografie se čeká na dekódování nové; čítač požadavků zabraňuje záměně obrázků při rychlém přepínání. Pozastavení časovače platí i při návratu stránky z historie.

Odhalování obsahu využívá `IntersectionObserver`. Při změně výsledků filtrů `MutationObserver` přihlásí nové karty a odhlásí odebrané, aby zbytečně nezůstávaly v paměti. Fokus na prvek okamžitě odkryje jeho sekci. Bez podpory pozorovatele se obsah vůbec neskrývá. Bez Web Animations API zůstává dostupné přímé přepínání filmových tipů.

`prefers-reduced-motion: reduce` vypíná CSS animace i automatické střídání a ruší probíhající animace JavaScriptu. Změna systémové preference se projeví i bez obnovení stránky. Tiskové styly odhalí všechny sekce bez čekání na animaci. Efekty nemění rozměry prvků a pořadí obsahu z wireframů zůstává zachované.

## Responzivita

| Šířka | Chování |
| --- | --- |
| Nad 1180 px | Plná navigace, vyhledávání v hlavičce, tři filmové karty |
| Do 1180 px | Kompaktní rozestupy a schované vyhledávání v hlavičce |
| Do 900 px | Dvousloupcová patička, rezervace nad souhrnem, kompaktní rozložení |
| Do 700 px | Rozbalovací navigace, posuvné plakáty a dny, formuláře a detail pod sebou |
| Do 400 px | Náhled projekce nad údaji, menší odsazení a upravené kontakty |

Stránka obsahuje `meta viewport`, obrázky respektují kontejner a písmo nadpisů používá plynulou velikost. Vodorovné posouvání je záměrné pouze u karuselu a výběru dnů. Pro `prefers-reduced-motion` se vypínají přechody a plynulé posouvání.

## Tok dat

1. `data.js` vytvoří šest fiktivních událostí a projekce pro 28 místních kalendářních dní.
2. Program kombinuje kategorii, přesný den, textový dotaz a formát promítání; výsledek se řadí podle času.
3. Odkaz předává ID filmu nebo projekce v URL. Neplatné ID zobrazí vysvětlení a návrat do programu.
4. Rozložení sálu má 8 řad po 10 sedadlech. Počáteční ukázkové obsazení je pro stejnou projekci stabilní.
5. Při potvrzení se ověří výběr, formulář, souhlas a aktuální místní obsazení. Rezervace se uloží a zobrazí se tisknutelné potvrzení.
6. Profil vybere rezervace se stejným normalizovaným e-mailem. Zrušení vyžaduje potvrzení a odstraní rezervaci z místního úložiště.
7. Událost `storage` aktualizuje sedadla po změně v jiné kartě stejného prohlížeče. Skutečné souběžné požadavky a atomické rezervace vyžadují backend.

## Formuláře a přístupnost

- Každé pole má viditelný label; e-mail, hesla a jméno používají příslušné typy a autocomplete.
- Vlastní české chyby se zobrazí u pole, nastaví `aria-invalid` a fokus se přesune na první chybu.
- Přihlášení a registrace kontrolují e-mail, délku hesla, duplicitu účtu a shodu hesel.
- Rezervace kontroluje počet 1–8 sedadel, jméno, e-mail a potvrzení ukázkového režimu.
- Výsledky programu používají `aria-live`, sedadla a filtry `aria-pressed`, mobilní menu `aria-expanded` a `aria-controls`.
- Nativní `<dialog>` udržuje fokus uvnitř otevřeného okna; podporuje Escape a zavření tlačítkem nebo kliknutím mimo obsah.
- Web obsahuje odkaz „Přeskočit na obsah“, navigační oblasti, textové alternativy a viditelné stavy focus-visible.
- Text z formulářů se při vložení do HTML escapuje. Kontaktní formulář zobrazí výsledek validace a transparentně vysvětlí, že nic neodesílá.

## SEO a obsah

Každá stránka má český jazyk dokumentu, vlastní title a meta description. Obsah používá hierarchii nadpisů a vnitřní odkazy. Detail filmu po načtení doplní konkrétní název do titulku. Pro produkci by bylo vhodné doplnit stabilní skutečný program, unikátní serverově vykreslené detaily, strukturovaná data Event/Movie, sitemap a canonical URL podle finální domény.

## Scénáře pro kontrolu a obhajobu

1. Na mobilu 375 px otevřít menu, přejít do programu a vyzkoušet data i filtry.
2. Vyhledat existující film a nesmyslný řetězec; ověřit stav bez výsledků a vymazání filtrů.
3. Otevřít registraci, odeslat prázdný formulář a vyplnit neshodující se hesla.
4. Vytvořit testovací účet, odhlásit se a přihlásit se správným i chybným heslem.
5. Vybrat projekci, obsazené místo, 1–8 volných míst a zkusit deváté místo.
6. Odeslat rezervaci bez e-mailu nebo souhlasu, pak ji správně dokončit.
7. Ověřit rezervaci v profilu se stejným e-mailem; zrušit ji a ověřit uvolnění míst.
8. Otevřít neplatný odkaz na film/projekci a zkontrolovat srozumitelný návrat do programu.
9. Projít hlavní prvky klávesnicí, otevřít dialog a zavřít jej klávesou Escape.
10. V náhledu tisku ověřit samotné potvrzení bez navigace a patičky.

## Možné navazující rozšíření

Serverová autentizace, centrální databáze projekcí a obsazení, skutečná platba, odesílání potvrzení, obnova hesla a reálné věrnostní odměny. Tyto funkce přesahují klientskou část a v současném projektu se nesimulují jako skutečné služby.
