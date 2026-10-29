# Tally űrlap – kimásolható mezők

> Minden listát a kódblokkból másolj ki. A Tally a soronként beillesztett szöveget általában
> külön opciókká bontja. Ha mégsem, opciónként kell beírni.
>
> **„Más” mezők:** a lista utolsó opciója a „Más”. Utána jön egy rövid szöveges mező, amely
> **feltételes logikával** csak akkor jelenik meg, ha a szülő a „Más”-t választotta
> (a mező … menüje → *Add conditional logic* vagy *Show when*). Így minden „Más” válasz külön mezőbe kerül,
> és a Make könnyen kezeli.

---

## Oldal 1 – A főszereplő

### 1. Mi a mese főszereplőjének a neve?
- Típus: **Short answer** · Kötelező · Max. 30 karakter
- Súgó szöveg: `Elég a keresztnév vagy a becenév. Vezetéknevet ne írj.`

### 2. Hány éves a gyermek?
- Típus: **Dropdown** · Kötelező
```
3
4
5
6
7
8
9
10
11
12
```

### 3. Hogyan jelenjen meg a főszereplő a színezőn?
- Típus: **Multiple choice** · Kötelező
```
fiú
lány
nem adom meg
```

### 4. Kik legyenek még a mesében?
- Típus: **Checkboxes** · Nem kötelező
```
nagypapa
nagymama
anya
apa
testvér
barát
háziállat
plüssjáték
```

### 5. A társszereplők neve (ha szeretnéd)
- Típus: **Short answer** · Nem kötelező · Max. 80 karakter
- Súgó szöveg: `Például: Papa, Morzsa kutya`

---

## Oldal 2 – A mese világa

### 6. Hol játszódjon a mese?
- Típus: **Dropdown** · Kötelező
```
idegen bolygó
űrállomás
a Hold
felhővár
szivárványhíd
víz alatti világ
korallzátony
barátságos kalózhajó
világítótorony szigete
varázslatos tó
varázserdő
tündérkert
óriásfák lombkoronája
kristálybarlang
dzsungel
szavanna
virágmező
jégbirodalom
az északi fény hazája
sivatagi oázis
alvó vulkán szigete
dinoszauruszok kora
lovagvár
a régi idők tanyája
a jövő városa
a saját kertünk, kicsiben
a gyerekszoba éjjel, amikor a játékok életre kelnek
a padlás titkai
nagyi konyhája
mézeskalács falu
egy könyv belseje
cirkusz
állatkert éjjel
Lepj meg!
Más
```
- **6b. Melyik helyszín?** · Short answer · Max. 50 karakter · *csak ha a 6. = „Más”*

### 7. Milyen különleges képessége legyen a főszereplőnek?
- Típus: **Dropdown** · Kötelező
```
érti az állatok beszédét
beszél a fákkal és virágokkal
növényeket növeszt
állattá tud változni
időjárást varázsol
repülni tud
víz alatt lélegzik
nagyon kicsire zsugorodik
óriásira nő
villámgyors
belép a képekbe és a könyvekbe
láthatatlanná válik
a rajzai életre kelnek
egy percre megállítja az időt
fényt gyújt a tenyerében
álmokba utazik
beszél a csillagokkal
érzi mások érzéseit
gyógyító öleléssel elmulasztja a kis bánatokat
minden nyelvet megért
bármit meg tud javítani
feltaláló
Lepj meg!
Más
```
- **7b. Melyik képesség?** · Short answer · Max. 50 karakter · *csak ha a 7. = „Más”*

---

## Oldal 3 – A mese üzenete

### 8. Mit tanítson a mese?
- Típus: **Dropdown** · Kötelező
```
tisztelet az idősebbek és a szülők iránt
barátság
testvéri szeretet (és a testvérféltékenység)
családi összetartás
segítőkészség
megosztás
bocsánatkérés és megbocsátás
őszinteség
türelem
kitartás („nem adom fel”)
felelősségvállalás
hála
szerénység
az ígéret betartása
bátorság a félelemmel szemben
az érzések kimondása (düh, szomorúság)
a hibázás a tanulás része
önbizalom, önelfogadás
a vereség elviselése, sportszerűség
a másság elfogadása
empátia, együttérzés
a természet és az állatok szeretete
vigyázunk a környezetünkre
a kíváncsiság és a tanulás öröme
beilleszkedés az óvodába vagy iskolába
új kistestvér érkezése
költözés
félelem a sötéttől, elalvás
Más
```
- **8b. Mi legyen a tanulság?** · Short answer · Max. 80 karakter · *csak ha a 8. = „Más”*

### 9. Milyen érzést keltsen a gyermekben? (legfeljebb 2)
- Típus: **Checkboxes** · Kötelező · ⚠️ a beállításoknál: legfeljebb 2 választás (*Limit choices / Max selections*, ha van ilyen)
```
biztonság
nyugalom, ellazulás (elalváshoz)
elfogadottság („szeretnek úgy, ahogy vagyok”)
otthonosság
melegség, szeretet
összetartozás
hála
gyengédség
bátorság
önbizalom („képes vagyok rá”)
büszkeség
remény
vidámság
játékosság
csodálat, ámulat
izgalom, kalandvágy
kíváncsiság
elégedettség
együttérzés
megkönnyebbülés (a bocsánatkérés, megbocsátás után)
Lepj meg!
Más
```
- **9b. Milyen érzés?** · Short answer · Max. 50 karakter · *csak ha a 9. tartalmazza a „Más”-t*

### 10. Van még valami kívánságod?
- Típus: **Long answer** · Nem kötelező · Max. 200 karakter
- Súgó szöveg: `Például: szerepeljen benne egy piros sárkány. Kérjük, ne írj címet, iskolanevet vagy más azonosító adatot.`

---

## Oldal 4 – Küldés

### 11. Hova küldjük a mesét?
- Típus: **Email** · Kötelező

### Szövegblokk a jelölőnégyzetek fölé
```
Az adatkezelési tájékoztatót itt olvashatod: [link]
```

### 12. Hozzájárulás
- Típus: **Checkbox** (egyetlen jelölőnégyzet) · Kötelező
```
Elolvastam az adatkezelési tájékoztatót, és hozzájárulok, hogy a gyermekemre és rám vonatkozó, itt megadott adatokat a mese elkészítéséhez és elküldéséhez felhasználják. A hozzájárulást bármikor visszavonhatom.
```

### 13. Nagykorúság
- Típus: **Checkbox** · Kötelező
```
Elmúltam 18 éves, és a gyermek szülője vagy törvényes képviselője vagyok.
```

---

## Köszönőoldal
```
Köszönjük! ✨
A mese pár percen belül megérkezik az email címedre. Ha 15 percen belül sem jön meg, nézd meg a Spam / Promóciók mappát is.
```

## Űrlap beállítások (Settings)
- **Form name:** Személyre szabott tanmese
- **Language:** Hungarian (így a gombok és a hibaüzenetek is magyarul jelennek meg)
- **Progress bar:** be (4 oldal)
- **Collect IP address / metadata:** ha van ilyen kapcsoló, **ki** (adattakarékosság)
- **Close form / limit responses:** most nem kell
