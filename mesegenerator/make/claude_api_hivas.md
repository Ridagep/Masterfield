# Claude hívás – „Make an API Call” modul beállítása

> Azért ezt a modult használjuk a „Create a Prompt” helyett, mert így be lehet állítani a
> **strukturált kimenetet** (a válasz garantáltan a sémának megfelelő JSON), az effortot és
> a tartalék modellt. Ezek a „Create a Prompt” modulban nem biztos, hogy elérhetők.

## Modul mezők

| Mező | Érték |
|---|---|
| Connection | Anthropic Claude kapcsolat (Anthropic Console API-kulccsal) |
| URL | `/v1/messages` (ha a modul teljes URL-t kér: `https://api.anthropic.com/v1/messages`) |
| Method | `POST` |
| Headers | `anthropic-beta`: `server-side-fallback-2026-07-01` |
| Body | lásd lent |

Az `anthropic-version` és az `x-api-key` fejlécet a kapcsolat magától beteszi. Ha mégis hibát ad, add hozzá kézzel: `anthropic-version: 2023-06-01`.

## Body (JSON)

A `SYSTEM_PROMPT` helyére a `prompt_rendszer.md` szövege kerül (a `---` utáni rész), a `USER_UZENET` helyére a `prompt_felhasznalo_sablon.md` sablonja a Tally mezőivel. A `SCHEMA` helyére a `claude_valasz_schema.json` tartalma kerül.

```json
{
  "model": "claude-opus-5-5",
  "max_tokens": 16000,
  "fallbacks": "default",
  "output_config": {
    "effort": "medium",
    "format": { "type": "json_schema", "schema": SCHEMA }
  },
  "system": "SYSTEM_PROMPT",
  "messages": [
    { "role": "user", "content": "USER_UZENET" }
  ]
}
```

**Fontos a Make-ben:** a rendszerprompt és a felhasználói üzenet sok sortörést és idézőjelet tartalmaz. Ezért ne közvetlenül gépeld be a JSON-ba, hanem:
1. Egy **Set variable** modulban állítsd össze a felhasználói üzenet szövegét a Tally mezőiből.
2. Egy **JSON – Create JSON** modullal építsd fel a body-t (adatstruktúra: model, max_tokens, system, messages…). Ez helyesen escape-eli a szöveget.
3. A Create JSON kimenetét add meg a „Make an API Call” Body mezőjében.

## A beállítások magyarázata

- **`claude-opus-5-5`:** a jelenlegi legjobb ár-érték modell a minőségi magyar szöveghez (4 $ / 1M bemeneti token, 20 $ / 1M kimeneti token). Olcsóbb alternatíva a `claude-sonnet-5-5` (2 $ / 10 $). Érdemes ugyanazzal a 3 próba-űrlappal összehasonlítani a kettőt, és a minőség alapján dönteni.
- **`effort: "medium"`:** ennél a modellnél ez az alapérték. Kreatív íráshoz elég. Ha a mesék laposak, próbáld ki a `high`-ot (drágább és lassabb).
- **`max_tokens: 16000`:** a modell gondolkodása is ebből fogy, ezért bőven kell. Így a mese nem vágódik le félúton.
- **`fallbacks: "default"`:** ha a biztonsági szűrő ritkán, tévesen elutasít egy kérést, az API automatikusan egy másik modellel próbálja újra. A `anthropic-beta` fejléc ehhez kell.
- Hőmérsékletet (`temperature`) **nem lehet** megadni ennél a modellnél, 400-as hibát adna. A változatosságot a prompt adja.

## A válasz feldolgozása

A válasz `body.content` tömbjében előbb jöhet egy vagy több `thinking` blokk (üres szöveggel), és csak utána a `text` blokk a JSON-nal. Ezért **nem** a `content[1].text`-et kell venni, hanem a `text` típusú blokkot:

```
{{first(map(body.content; "text"; "type"; "text"))}}
```

Ezt add a **JSON – Parse JSON** modulnak (adatstruktúra: a `claude_valasz_schema.json` alapján, vagy a „Generate” gombbal egy mintaválaszból).

### Ellenőrzés (szűrő a Parse JSON előtt)

| Feltétel | Mit jelent | Teendő |
|---|---|---|
| `body.stop_reason` = `end_turn` | Rendben | Mehet tovább |
| `body.stop_reason` = `max_tokens` | Levágott válasz | Hiba-útvonal: értesítés Ritának, `max_tokens` növelése |
| `body.stop_reason` = `refusal` | A modell elutasította (a tartalék modellel is) | Udvarias email a szülőnek, értesítés Ritának |

## Költség mesénként (becslés)

- Bemenet: kb. 2500 token (rendszerprompt + űrlap) → kb. 0,01 $
- Kimenet: kb. 3000 token mese és JSON, plusz a gondolkodás, összesen kb. 5000–7000 token → kb. 0,10–0,14 $
- **Összesen kb. 0,10–0,15 $ / mese** az Opus 5.5-tel, kb. a fele a Sonnet 5.5-tel. Az első tesztek után a valós `usage` mezőkből pontosan kiszámolható.
