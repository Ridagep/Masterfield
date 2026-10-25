# Varázskönyv – hol tartunk

_Utolsó frissítés: 2026-10-25_

## Kész ✅

| Rész | Megjegyzés |
|---|---|
| Make Core előfizetés | eu1.make.com, „Tanmese-generátor” scenario |
| Anthropic és OpenAI API-kulcsok, Make-kapcsolatok | OpenAI: külön „Mesegenerator” projekt, korlátozott kulcs |
| Tally űrlap | 4 oldal, „Más” mezők feltételes logikával |
| Mesegenerálás (Claude) | sémás JSON válasz, sorsolt meglepetés-irány, bekezdések egy sortöréssel |
| Színezőkép (OpenAI) | 1024×1536, fix vonalas stílus-utótag, C2PA-jelölés igazolva |
| Google Drive feltöltés + megosztás | `mesegenerator_tmp` mappa |
| PDF Google Docs sablonból | A4, Varázskönyv vízjel-keret, logó csak az 1. oldal fejlécében, 2:3-as színezőkép, „Oldaltörés előtte” a címeknél |
| Email a szülőnek | Gmail modul a meglévő Google Restricted kapcsolattal; csatolmány: PDF + szinezo.png |
| Bemenet moderálása | OpenAI Moderation a szabad szöveges mezőkre + Router: kiszűrt → elutasító email a szülőnek + értesítés Ritának (tesztelve) |
| Takarítás | 2 × Google Drive Delete (Document ID, Upload File ID) az email után; a Make véglegesen töröl (nem a Kukába) |

## Hátravan ⬜

4. **Hibakezelés:** Break hibakezelő + értesítő email Ritának; a `biztonsagi_megjegyzes` nem üres → értesítés
5. **Heti takarító scenario** a `mesegenerator_tmp` mappára
6. **Scenario beállítások élesítéskor:** Data is confidential: BE
7. **Tesztterv** (`make/epitesi_utmutato.md` 12. pont), köztük a gyermekvédelmi esetek
8. **Adatkezelési tájékoztató:** kitöltés, ellenőrzés, nyilvános link a Tally hozzájárulás mezőjébe
9. **Emlékeztető:** Google-kapcsolat újraengedélyezése 2027. április 23. előtt

## Fontos azonosítók

- Google Docs sablon: `1qcWBheYhwjO3zZBn0lImb5WGayushf7Tu4nYmERnhRw`
- A színezőkép helyőrzője a sablonban: „Tab 1 – Body: Image No. 1” (a Make magától felismeri)
