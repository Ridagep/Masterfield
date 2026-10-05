# Make.com folyamat terv – Tanmese-generátor (MVP)

## Áttekintés

```
Tally űrlap
  └─► [1] Tally – Watch New Responses (azonnali trigger)
        └─► [2] OpenAI – Create a Moderation (szabad szöveges mezők ellenőrzése)
              ├─► (megjelölt tartalom) [2a] Email a szülőnek: kérjük, módosítsa az űrlapot → STOP
              └─► [3] Anthropic Claude – Create a Prompt (rendszerprompt + űrlap adatai)
                    └─► [4] JSON – Parse JSON (cím, mese, kérdések, képprompt)
                          └─► [5] OpenAI – Generate an image (színezőlap)
                                └─► [6] Google Drive – Upload a file (kép ideiglenes tárolása, URL-hez)
                                      └─► [7] CraftMyPDF – Create a PDF (mese + kérdések + színező)
                                            └─► [8] HTTP – Get a file (a kész PDF letöltése)
                                                  └─► [9] Email küldés a szülőnek, PDF melléklettel
                                                        └─► [10] Google Drive – Delete a file (takarítás)
Hibakezelő útvonal (minden modulon): értesítő email Ritának a hibáról
```

Becsült fogyasztás: kb. **10–12 Make kredit / mese** (1 modulművelet = 1 kredit), plusz az Anthropic és az OpenAI API-díja a saját API-kulcsokon.

---

## Modulok részletesen

### [1] Tally – Watch New Responses
- Azonnali (webhook) trigger: a Make magától létrehozza a webhookot a Tallyban.
- Űrlapmezők (lásd `prompt_felhasznalo_sablon.md`): főszereplő neve, kor, megjelenés a rajzon, társszereplők, helyszín, képesség, tanulság, érzés, egyéb kívánság, **szülő email címe**, **adatkezelési hozzájárulás** (kötelező jelölőnégyzet).
- Ahol lehet, legördülő lista, és mellette egy rövid „Más:” mező (max. ~60 karakter).

### [2] OpenAI – Create a Moderation
- Bemenet: a szabad szöveges mezők összefűzve (nevek, társszereplők, „Más:” mezők, egyéb kívánság).
- Router: ha `flagged = true` → [2a] udvarias email a szülőnek, hogy módosítsa a megadott adatokat; a folyamat leáll.
- Ez az első védelmi vonal. A második a rendszerprompt biztonsági szabályai, a harmadik a [4] utáni ellenőrzés (lásd lent).

### [3] Anthropic Claude – Create a Prompt
- **System:** a `prompt_rendszer.md` szövege (a `---` utáni rész).
- **User:** a `prompt_felhasznalo_sablon.md` sablonja, a `{{...}}` helyekre a Tally mezői.
- Javasolt beállítás: max tokens ~4000, hőmérséklet ~0,8–1,0 (változatos mesékhez).
- ⚠️ Tesztelni kell, hogy a modul visszaad-e tiszta JSON-t. Ha nem mindig, akkor a „Make an API Call” modullal, strukturált kimenettel érdemes hívni.

### [4] JSON – Parse JSON
- Adatstruktúra a rendszerprompt kimeneti formátuma alapján: `cim`, `mese`, `tanulsag_szuloknek`, `kerdesek[]` (`kerdes`, `mire_figyelj`), `szinezo.kep_prompt_en`, `biztonsagi_megjegyzes`.
- Szűrő utána: ha a `biztonsagi_megjegyzes` nem üres → értesítés Ritának (a mese ettől még kimehet, de látni kell, mit cserélt a modell).
- Opcionális második moderáció: a kész `mese` szövegét is át lehet küldeni a [2]-es moderáción.

### [5] OpenAI – Generate an image
- Prompt: `szinezo.kep_prompt_en`.
- Méret: álló formátum (pl. 1024×1536), hogy kitöltse az A4-es oldalt. Minőség: közepes (nyomtatáshoz elég, olcsóbb).
- Kimenet: PNG fájl. **Nem kap utólagos fekete-fehér konverziót** (lásd a „Kompromisszumok” részt).

### [6] Google Drive – Upload a file
- A PDF-készítőnek a kép URL-je kell, a képgenerátor viszont fájlt ad vissza. Ezért a kép egy külön, privát mappába kerül ideiglenesen, és onnan kap linket.
- ⚠️ Tesztelni kell: ha a CraftMyPDF el tud fogadni base64 képet, ez a lépés kihagyható. Az a jobb megoldás, mert akkor a kép nem kerül külső tárhelyre.

### [7] CraftMyPDF – Create a PDF
- Sablon (egyszer kell megtervezni a CraftMyPDF szerkesztőjében):
  1. **Mese:** cím, mese szövege (a `\n\n` bekezdéseket bekezdésekké alakítva).
  2. **A szülőnek:** tanulság, kérdések a „mire figyelj” útmutatóval (ismétlődő blokk a `kerdesek` tömbre).
  3. **Színezőlap:** a kép teljes oldalon, alatta kis felirat: *„Az illusztráció mesterséges intelligencia segítségével készült.”*
  4. Lábléc minden oldalon: *„Ez a mese mesterséges intelligencia segítségével készült.”*

### [8] HTTP – Get a file
- A CraftMyPDF a kész PDF URL-jét adja vissza; ez a modul letölti fájlként, hogy csatolni lehessen.

### [9] Email küldés
- **Prototípushoz:** Gmail – Send an email. @gmail.com fióknál a Make-hez **saját Google Cloud OAuth kliens** kell, ez egyszeri beállítás.
- **Éles termékhez:** tranzakciós email szolgáltatás (pl. Brevo, a Make-ben van modulja) vagy SMTP. Saját domainről küld, jobb a kézbesíthetőség, és nem a személyes Gmail-fiókból mennek ki a levelek.
- Melléklet: a PDF a [8]-ból. Gmailnél a Make-es melléklet felső határa 15 MB.

### [10] Takarítás
- Google Drive – Delete a file: az ideiglenes kép törlése.
- Make beállítás: a scenario futási adatainak megőrzését érdemes minimumra venni (a mese és a gyermek neve benne van a naplóban).

---

## Kompromisszumok és teendők

| Téma | Probléma | Megoldás az MVP-ben |
|---|---|---|
| Fekete-fehér konverzió | Nincs rá natív Make modul, külső képszerkesztő szolgáltatás kellene hozzá | Kihagyjuk. A képpromptban szigorúan kérjük a „no shading, no gray” vonalas stílust, és tesztekkel ellenőrizzük |
| AI-jelölés (EU AI Act 50. cikk (2)) | Az OpenAI képei C2PA metaadatot és SynthID vízjelet kapnak, de **a PDF-be ágyazáskor a C2PA metaadat jellemzően elvész** | Látható felirat a színezőlapon és a láblécben. ⚠️ Ellenőrizni kell, hogy a CraftMyPDF tud-e PDF-metaadatot (pl. leírás, kulcsszó) írni, és hogy a SynthID megmarad-e a PDF-ben |
| Kép URL | A képgenerátor fájlt ad, a PDF-sablonnak URL kell | Ideiglenes Google Drive feltöltés, utána törlés, vagy base64, ha a CraftMyPDF elfogadja |
| Személyes Gmail | Saját OAuth kliens kell, napi küldési korlát, nem professzionális feladó | Prototípushoz jó, élesben Brevo vagy SMTP saját domainről |

## Csomagválasztás

- **Free (1000 kredit/hó, 5 MB fájlkorlát):** a PNG kép és a PDF mérete megközelítheti az 5 MB-ot, ezért csak nagyon korai teszteléshez jó.
- **Core vagy Pro:** 10 000 kredit/hó, kb. 800–1000 mese havonta, nagyobb fájlkorlát, 40 perces futási idő. **Ez javasolt** már a béta-teszthez is.

## GDPR-teendők (nem technikai, de kötelező)

- Adatkezelési tájékoztató és kötelező hozzájárulás az űrlapon (gyermek neve és kora).
- Adattakarékosság: elég a keresztnév, vezetéknév és fotó nem kell.
- Megőrzési idő: a Tally-válaszok, a Make-naplók és a Drive-ra feltöltött ideiglenes képek rendszeres törlése.
- Adatfeldolgozók listája a tájékoztatóban: Tally, Make, Anthropic, OpenAI, CraftMyPDF, Google / az email szolgáltató.
