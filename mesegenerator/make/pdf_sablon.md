# PDF sablon – CraftMyPDF

> Cél: egy 3 részes, nyomtatható A4-es PDF. **(1) A mese** · **(2) A szülőnek: tanulság és kérdések** · **(3) Színezőlap**.
> A logó és a márkanév egyelőre helyőrző (`Személyre szabott tanmese`), később cserélhető.
> ⚠️ A CraftMyPDF szerkesztőjének pontos menüneveit a dokumentációból nem tudtam ellenőrizni. Ha valami máshogy
> hívják, küldj képernyőképet.

---

## 1. Új sablon

1. CraftMyPDF → **Manage Templates** → **New Template** → **Blank** (üres).
2. **Name:** `Tanmese PDF`
3. **Template settings** (fogaskerék vagy *Settings* fül):
   - **Paper size:** A4 · **Orientation:** Portrait (álló)
   - **Margins:** 20 mm minden oldalon
4. **Data** fül (vagy *Sample data* / *JSON data*): töröld ki, ami benne van, és illeszd be a lenti **mintaadatot**.
   Így a szerkesztőben rögtön látod, hogyan fog kinézni egy valódi mesével.

### Mintaadat

```json
{
  "cim": "Bence, Papa és a két hold tava",
  "mese": "Messze, messze, a csillagokon is túl, ahol az ég nem kék, hanem halványlila, van egy kicsi bolygó.\n\nEgy szép napon Bence, Papa és Morzsa kutya megérkezett Pitypangcsillagra.\n\n– Egészségedre! – mondta valaki vékony, rekedtes hangon.\n\nŐrsike, Ürge Ödön és a többiek: ékezetteszt őŐűŰáéíóöúü.",
  "tanulsag_szuloknek": "A mese azt erősíti, hogy az idősebbek tempója, történetei és tanácsai értékesek.",
  "kerdesek_szoveg": "1. Szerinted hogyan érezte magát Papa, amikor Bence azt mondta neki, hogy már nem tud gyorsan menni?\nMire figyelj: Figyeld, mennyire tudja a gyermek beleélni magát Papa helyzetébe.\n\n2. Mit gondolsz, miért jöttek elő a fénycsigák a végén?\nMire figyelj: Bármelyik válasz jó: a viszonzott jóság és a lassú, közös séta is helyes.",
  "kep_url": "https://placehold.co/1024x1536/png"
}
```

> A `kerdesek_szoveg` mezőt a Make állítja össze egyetlen szöveggé (lásd lent), így a sablonban nem kell
> ismétlődő táblázattal bajlódni.

---

## 2. Betűtípus – először ezt teszteld!

A magyar **ő, Ő, ű, Ű** betűket nem minden betűtípus ismeri. Ha rosszat választasz, ezek helyén üres négyzet vagy
más betű jelenik meg.

- Javasolt: **Nunito**, **Quicksand** vagy **Baloo 2** (barátságos, kerekded, gyerekkönyves hangulat). Ha nincsenek a
  listában: **Open Sans**, **Lato**, **Roboto** – ezek biztosan ismerik az ő/ű-t.
- **Teszt:** a mintaadat végén ott a sor: *„Őrsike, Ürge Ödön… őŐűŰ”*. Ha az előnézetben minden betű rendben van, jó a
  betűtípus.

---

## 3. Az oldalak felépítése

A szerkesztőben elemeket (komponenseket) húzol a lapra: **Text** (szöveg), **Image** (kép), **Line** (vonal),
**Page break** (oldaltörés). A szöveg- és képmezőkbe kifejezést írsz, pl. `{{cim}}`, és az előnézet a mintaadatból
tölti ki.

### 1. rész – A mese (1–3 oldal, a mese hosszától függően)

| Elem | Tartalom | Beállítás |
|---|---|---|
| Text – fejléc | `Személyre szabott tanmese` | 10 pt, szürke (#888), középre, felül |
| Text – cím | `{{cim}}` | 24–26 pt, félkövér, középre, sötétkék vagy mélylila (#3B2A6B) |
| Line | – | vékony, halvány vonal a cím alatt, kb. 40% szélesség, középen |
| Text – mese | `{{mese}}` | 13–14 pt, sorköz 1,5, sorkizárt vagy balra zárt; **Auto height / Overflow: be** (hogy hosszú mesénél átfolyjon a következő oldalra); a sortörések megtartása (*Preserve line breaks* / *white-space: pre-wrap*) |
| Text – zárás | `Itt a vége, fuss el véle!` | 12 pt, dőlt, középre |
| **Page break** | | |

### 2. rész – A szülőnek (1 oldal)

| Elem | Tartalom | Beállítás |
|---|---|---|
| Text – cím | `A szülőnek` | 18 pt, félkövér |
| Text – alcím | `Mit erősít ez a mese?` | 13 pt, félkövér |
| Text | `{{tanulsag_szuloknek}}` | 12 pt, sorköz 1,4 |
| Text – alcím | `Kérdések a gyermeknek – beszélgessetek a mese után` | 13 pt, félkövér, felette kis térköz |
| Text | `{{kerdesek_szoveg}}` | 12 pt, sorköz 1,4, sortörések megtartása |
| **Page break** | | |

### 3. rész – Színezőlap (1 oldal)

| Elem | Tartalom | Beállítás |
|---|---|---|
| Text – cím | `Színezd ki a mesét!` | 18 pt, félkövér, középre |
| Image | `{{kep_url}}` | teljes szélesség, arányos méretezés (*Keep aspect ratio / Fit*), kb. 160–170 mm magas |
| Text – felirat | `Az illusztráció mesterséges intelligencia segítségével készült.` | 8 pt, szürke, középre, a kép alatt |

### Lábléc (minden oldalon)

| Elem | Tartalom | Beállítás |
|---|---|---|
| Text | `Ez a mese mesterséges intelligencia segítségével készült. · Személyre szabott tanmese` | 8 pt, szürke, középre |
| Text (opcionális) | oldalszám, pl. `{{page}} / {{total_pages}}` | 8 pt, jobbra – ⚠️ a pontos kifejezést a CraftMyPDF súgójában kell megnézni |

---

## 4. Make – a PDF modul előtt (2 + 2 modul)

### a) Kérdések egy szöveggé: Iterator + Text aggregator

1. **Flow Control → Iterator** · **Array:** a Parse JSON **kerdesek** mezője.
2. **Tools → Text aggregator** · **Source module:** az Iterator · **Text:**
   ```
   {{bundle position}}. {{kerdes}}
   Mire figyelj: {{mire_figyelj}}

   ```
   (A `bundle position`, a `kerdes` és a `mire_figyelj` mezőt a panelről kattintsd be, az Iterator alól.)
   A végén egy üres sor maradjon, hogy a kérdések között legyen térköz.

### b) Nyilvános link a képhez: Google Drive

3. A már meglévő **Google Drive – Upload a file** után: **Google Drive → Share a file** (vagy *Create a permission*):
   - **File ID:** az Upload modul **ID** mezője
   - **Role:** Reader · **Type:** Anyone (bárki, akinél a link van)
4. A képlink a PDF-hez:
   ```
   https://drive.google.com/uc?export=download&id={{<Upload modul>.id}}
   ```

> ⚠️ Alternatíva, ha a CraftMyPDF elfogad base64 képet: `data:image/png;base64,{{base64(<OpenAI modul>.Response Image Data)}}`.
> Ekkor a Drive megosztás kimarad. Ezt egy teszttel érdemes kipróbálni, mert adatvédelmi szempontból jobb.

### c) CraftMyPDF – Create a PDF

5. **Connection:** `CraftMyPDF – mesegenerator` · **Template:** `Tanmese PDF`
6. **Data** (JSON) – ha a modul mezőnként kéri, töltsd ki így; ha egy JSON-t kér, egy **JSON → Create JSON** modullal
   állítsd össze ugyanebből az 5 mezőből:
   - `cim` → Parse JSON **cim**
   - `mese` → Parse JSON **mese**
   - `tanulsag_szuloknek` → Parse JSON **tanulsag_szuloknek**
   - `kerdesek_szoveg` → Text aggregator **text**
   - `kep_url` → a fenti Drive-link
7. **Export type:** `json` (a modul egy letöltési linket ad vissza) · **Expiration:** 10 perc
8. **Teszt:** Run once + beküldés → a modul kimenetében kattints a **file** linkre → megnyílik a PDF.

---

## 5. Ellenőrzőlista az első PDF-nél

- [ ] Az ő, ű, Ő, Ű betűk helyesen jelennek meg
- [ ] A hosszú mese átfolyik a következő oldalra, nem vágódik le
- [ ] A bekezdések külön sorban vannak (nem egy tömbben)
- [ ] A kérdések számozva, a „Mire figyelj” sorokkal
- [ ] A színezőkép kitölti az oldalt, nem torzul
- [ ] A két AI-felirat (kép alatt + lábléc) látszik
- [ ] Kinyomtatva is jól olvasható és kiszínezhető
