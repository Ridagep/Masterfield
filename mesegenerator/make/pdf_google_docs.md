# PDF Google Docs sablonból (a CraftMyPDF helyett)

Sablon: **Lélekfonál – mese sablon (Make)** a Google Drive-ban
(https://docs.google.com/document/d/1qcWBheYhwjO3zZBn0lImb5WGayushf7Tu4nYmERnhRw/edit)

Benne vannak a helyőrzők: `{{cim}}`, `{{mese}}`, `{{tanulsag_szuloknek}}`, `{{kerdesek_szoveg}}`, és egy szürke
helyőrző kép („SZINEZO KEP”), amit a Make minden mesénél a színezőre cserél.

---

## A. Kézi befejezés a sablonban (egyszer, kb. 10 perc)

A Google a feltöltéskor néhány beállítást nem vett át, ezeket kézzel kell megcsinálni:

1. **Betűtípus:** Ctrl+A (mindent kijelöl) → betűtípus-választó → **További betűtípusok** → keresd: **Nunito** → pipa → OK.
   Utána Ctrl+A-val kijelölve válaszd ki a Nunitót. (A színek és a méretek megmaradnak.)
2. **Margók:** Fájl → **Oldalbeállítás** → Felső: **4,8 cm** · Alsó: **2,8 cm** · Bal és jobb: **2,2 cm** → OK.
   (A felső margó azért nagy, hogy a szöveg ne lógjon rá a háttér logójára.)
3. **Oldaltörések** (két helyen): kattints a **„A szülőnek”** sor elejére → Beszúrás → **Törés** → **Oldaltörés**.
   Ugyanígy a **„Színezd ki a mesét!”** sor elé.
4. **Háttér (vízjel):** Beszúrás → **Vízjel** → **Kép** → töltsd fel a `hatter_1_mese.png`-t → a jobb oldali panelen
   a **Halványítás (Fade)** pipát **vedd ki**, és a méretezést állítsd úgy, hogy a díszítés a lap széléig érjen.
   ⚠️ Ha a vízjel nem tölti ki a teljes lapot, az sem baj: a díszítés kisebb lesz, de a szöveg nem csúszik rá.
5. **Lábléc:** kattints duplán a lap aljára (vagy Beszúrás → Fejlécek és láblécek → Lábléc) → írd be:
   `Lélekfonál · Ez a mese mesterséges intelligencia segítségével készült.` → 8 pt, szürke, középre.
6. **A helyőrző kép mérete:** kattints a szürke képre, és húzd akkorára, amekkorának a színezőt szeretnéd
   (kb. az oldal szélessége, a lábléc fölött). A Make az új képet ugyanebbe a méretbe teszi.

**Fontos:** a `{{…}}` helyőrzőket ne írd át, és ne formázd őket részenként (pl. félig félkövérre), mert akkor a Make nem
találja meg őket.

---

## B. A kép azonosítója (egyszer)

A Make a képet az azonosítója alapján cseréli. Ezt egyszer kell kiolvasni:

1. A scenarióban, külön (nem bekötve), adj hozzá egy **Google Docs → Get Content of a Document** modult.
2. **Document:** a `Lélekfonál – mese sablon (Make)`.
3. Jobb klikk → **Run this module only**.
4. A kimenetben: **Inline Objects** (vagy *Inline Object Array*) → az első elem **Object ID**-ja, valami ilyesmi:
   `kix.abc123xyz`. **Másold ki és jegyezd fel.**
5. Ezt a modult utána törölheted.

---

## C. Make modulok (a Google Drive feltöltés után)

### 1. Kérdések egy szöveggé: Iterator + Text aggregator
- **Iterator** · Array: a Parse JSON **kerdesek** mezője
- **Text aggregator** · Source module: Iterator · Text:
  ```
  {{bundle position}}. {{kerdes}}
  Mire figyelj: {{mire_figyelj}}

  ```
  (a három mezőt a panelről kattintsd be)

### 2. A kép nyilvános linkje: Google Drive → Share a file
- File ID: a Drive **Upload a file** modul **ID**-ja · Role: **Reader** · Type: **Anyone**
- Képlink: `https://drive.google.com/uc?export=download&id=` + az Upload modul **ID**-ja

### 3. Google Docs → Create a Document from a Template
- **Choose a Drive:** My Drive · **Document ID:** `Lélekfonál – mese sablon (Make)`
- **Values** (a mezőnevek `{{}}` nélkül; ha a modul nem ismeri fel őket, próbáld kapcsos zárójellel):
  | Tag | Érték |
  |---|---|
  | `cim` | Parse JSON → cim |
  | `mese` | Parse JSON → mese |
  | `tanulsag_szuloknek` | Parse JSON → tanulsag_szuloknek |
  | `kerdesek_szoveg` | Text aggregator → text |
- **Images:** *Add item* → **Image Object ID:** a B. pontban feljegyzett azonosító · **Image URL:** a fenti képlink
- **Title:** `Mese – {{cim}}` · **New document's location:** a `mesegenerator_tmp` mappa

### 4. Google Docs → Download a Document
- **Document ID:** az előző modul kimenetének ID-ja · **Type:** **PDF**

### 5. Takarítás (az email elküldése UTÁN)
- **Google Drive → Delete a file:** a kitöltött Google Doc (3. modul ID) és a feltöltött kép (Upload modul ID).
  Így nem marad a gyermek neve és meséje a Drive-on.

---

## D. Teszt
Run once + beküldés → a **Download a Document** modul kimenetében lesz a PDF (*Data*). A `mesegenerator_tmp`
mappában közben megnézheted a kitöltött Google Docot is (a takarító modul bekötése előtt).

**Ellenőrizd:** a mese bekezdései külön sorban vannak · az ő/ű rendben van · az oldaltörések jó helyen vannak ·
a színezőkép a helyén van, nem torzul · látszik a lábléc és a vízjel.
