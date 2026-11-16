# Aktualizace webu na GitHub Pages

1. Rozbalte ZIP a otevřete složku **Kino_Obzor**.
2. V repozitáři na GitHubu zvolte **Add file → Upload files**.
3. Z Průzkumníka přetáhněte všechny HTML soubory a **celé složky `css`, `js` a `assets` včetně jejich obsahu**. Zachovejte názvy a strukturu složek. `index.html` i tyto tři složky mají být přímo v kořeni repozitáře.
4. Potvrďte aktualizaci. Soubory se stejnou cestou nahradí starší verzi.
5. V záložce **Actions** vyčkejte na úspěšné dokončení nasazení GitHub Pages a obnovte web pomocí **Ctrl + F5**.

## Kontrola po nahrání

Na úvodní straně repozitáře musí být vidět zejména:

| Umístění | Co obsahuje |
| --- | --- |
| `index.html` a dalších sedm souborů HTML | Jednotlivé stránky webu |
| `css/style.css` | Základní rozložení |
| `css/multiplex.css` | Nový tmavý vzhled |
| `css/motion.css` a `js/motion.js` | Animace, prolínání obrázků a ovladatelné střídání banneru |
| `js/data.js` a `js/app.js` | Program, formuláře, účty a rezervace |
| `assets/` | Fotografie, favicon, lokální písmo a jeho licence |

Pokud chybí `css`, stránka bude bez správného vzhledu. Pokud chybí `js`, nebudou fungovat interaktivní části. Pokud chybí `assets`, neuvidíte obrázky a lokální písmo.

Soubor ZIP samotný se na GitHub Pages automaticky nerozbaluje. Nahrávají se rozbalené soubory a složky.
