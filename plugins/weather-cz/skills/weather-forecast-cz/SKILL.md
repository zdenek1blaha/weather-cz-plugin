---
name: weather-forecast-cz
description: Získává a porovnává aktuální počasí a předpovědi pro ČR, včetně hodinových/dlouhodobých předpovědí a horských túr. Použij při dotazech na počasí, déšť, vítr, bouřky, sníh, výstrahy, výlet nebo túru v ČR. Porovnává explicitně nezávislé modely přes Open-Meteo, autoritativní data ČHMÚ a v horách Horskou službu. Nikdy nesmí meteorologická data domýšlet.
compatibility: Vyžaduje síťový/webový přístup k aktuálním meteorologickým zdrojům; vhodné pro ChatGPT/Codex nebo jiného agenta podporujícího Agent Skills a web/API fetch.
metadata:
  author: user-custom
  version: "1.0.0"
  language: cs
  region: CZ
---

# Weather Forecast CZ

## Cíl

Poskytuj přesnou, aktuální a auditovatelnou předpověď počasí. Skill není meteorologický model. Je to sběrač dat, porovnávač nezávislých modelů, interpret a bezpečnostní kontrola.

## Absolutní pravidla

1. Nikdy nevymýšlej meteorologická data, čísla, čas srážek, výstrahy ani modelovou shodu.
2. Každá číselná hodnota musí pocházet z aktuálně načteného zdroje.
3. Chybějící hodnotu nenahrazuj odhadem. Řekni, že není dostupná.
4. Nepoužívej znalosti modelu jako náhradu za aktuální předpověď.
5. Nezaměňuj poskytovatele API za meteorologický model. Open-Meteo je technická brána, ne nezávislý model.
6. Nepoužívej Open-Meteo Best Match jako jednu stranu modelového porovnání.
7. Nepoužívej seamless model jako nezávislý model, pokud po vypršení svého nativního horizontu přechází na jiný model, který zároveň porovnáváš.
8. Nezprůměruj modely do jedné „pravdy“. Ukaž dominantní scénář a zachovej rozpory.
9. Oficiální výstrahy ČHMÚ a bezpečnostní informace Horské služby neagreguj s modely. Zobraz je samostatně a prioritně.
10. Při nejasnosti raději přiznej nejistotu než falešnou přesnost.

## Načti reference

Před meteorologickým dotazem načti podle potřeby:

- `references/sources-and-models.md` pro zdroje, endpointy, modelové identifikátory a horizonty.
- `references/agreement-rules.md` vždy, když porovnáváš dva nebo více modelů nebo uvádíš 🟢/🟡/🔴 shodu.
- `references/mountain-mode.md` při horské túře, vrcholu, hřebeni nebo výletu ve vyšší nadmořské výšce.

## 1. Rozpoznej režim

Použij jeden nebo kombinaci těchto režimů:

- `CURRENT`: co je teď.
- `NOWCAST`: příští přibližně 0–6 hodin; důležitý radar a aktuální měření.
- `HOURLY`: konkrétní hodiny v jednom nebo více dnech.
- `DAY`: jeden konkrétní den.
- `RANGE`: několik dnů nebo zadané období.
- `MOUNTAIN`: túra, vrchol, hřeben, horská oblast.
- `LONG_RANGE`: přibližně 8+ dní; preferuj ensemble a trend.

## 2. Urči místo a čas

1. Získej přesnou lokalitu nebo souřadnice.
2. Urči latitude, longitude, nadmořskou výšku a časové pásmo.
3. Pro horu použij souřadnice vrcholu/cíle, ne pouze nejbližší obec.
4. Pokud má trasa výrazný výškový profil, viz `references/mountain-mode.md`.
5. Všechny časové řady porovnávej ve stejném lokálním časovém pásmu.
6. U relativních dat používej konkrétní datum, pokud by mohlo dojít k nejasnosti.

## 3. Vyber modely podle horizontu

Pro ČR používej explicitně vybrané modely. Přesný modelový plán je v `references/sources-and-models.md`.

Základ:

- 0–72 h: CHMI ALADIN CZ 1 km + DWD ICON-EU + ECMWF IFS HRES 9 km.
- 4.–5. den: DWD ICON-EU + ECMWF IFS HRES + NOAA GFS.
- 6.–7. den: ECMWF IFS HRES + DWD ICON Global + NOAA GFS.
- 8.–10. den: deterministické modely pouze jako scénáře; přidej ECMWF/GFS ensemble.
- 11.–15. den: ensemble je hlavní; výstup je trend a rozsah.
- 16+ dní: pouze dlouhodobý trend; nikdy přesná hodinová předpověď.

Pokud konkrétní model nemá data pro požadovaný čas, neprodlužuj ho seamless náhradou bez jasného označení.

## 4. Načti meteorologická data

Pro hodinové porovnání požaduj podle dostupnosti minimálně:

- `temperature_2m`
- `precipitation`
- `rain`
- `snowfall`
- `wind_speed_10m`
- `wind_gusts_10m`
- `cloud_cover`
- `visibility`
- `relative_humidity_2m`

Pro bouřkové situace podle dostupnosti navíc:

- CAPE
- lightning/thunderstorm indikátory
- convective precipitation/showers
- oficiální výstrahy ČHMÚ
- radar pro krátký horizont

Pocitovou teplotu a weather code můžeš použít, ale pokud jsou odvozené Open-Meteo, nevydávej je za nativní pole původního modelu.

## 5. Validuj odpověď zdroje

Před použitím modelu:

1. Ověř, že HTTP/API odpověď uspěla.
2. Ověř, že lokalita leží v doméně regionálního modelu.
3. Ověř, že požadované časové období má skutečné hodnoty, ne pouze `null`.
4. Pokud je více než 30 % klíčových hodnot v požadovaném okně `null`, považuj model pro daný dotaz za nedostupný.
5. Pokud jsou `null` právě v nejdůležitější veličině dotazu (např. srážky při otázce na déšť), nepoužívej tento model pro hodnocení této veličiny.
6. Pokud je dostupný modelový čas běhu/aktualizace, ověř jeho čerstvost. Pokud není vystaven, netvrď konkrétní čas modelového běhu.

### Fallback ALADIN

Pokud `chmi_aladin_cz_1km` vrátí neplatná nebo převážně `null` data:

1. zkus `chmi_aladin_central_europe_2km`, pokud lokalita leží v jeho doméně;
2. model jasně označ jako ALADIN Central Europe 2.3 km, nikoli CZ 1 km;
3. pokud selže i ten, pokračuj ICON-EU + ECMWF a napiš, že ALADIN nebyl dostupný;
4. pro oficiální českou vrstvu stále zkontroluj ČHMÚ přímo.

## 6. ČHMÚ jako autoritativní česká vrstva

Pro ČR podle relevance kontroluj přímo ČHMÚ:

- oficiální výstrahy,
- oficiální textovou/horskou předpověď,
- radar pro nowcasting,
- aktuální měření ze stanic, pokud pomůže odpovědět na „teď“.

### Výstrahy

Při dotazech na dnešek, zítřek, cestování, výlet, túru nebo potenciálně nebezpečné počasí vždy zkontroluj relevantní výstrahy ČHMÚ, pokud je lze načíst.

Pokud výstraha existuje:

- zobraz ji před běžným doporučením,
- uveď druh jevu, úroveň, oblast a platnost,
- neříkej „bez výstrahy“, pokud se kontrola nepodařila.

## 7. Radar a aktuální realita

Pro horizont přibližně 0–6 hodin:

- pokud je dotaz o dešti/bouřce, použij radar ČHMÚ, pokud je dostupný;
- rozlišuj „radar ukazuje“ od „model předpovídá“;
- neextrapoluj pohyb srážek do přesného času bez odpovídajícího nowcast zdroje;
- aktuální pozorování má pro „co se děje teď“ přednost před modelovým stavem.

## 8. Porovnej modely

Použij `references/agreement-rules.md`.

Základní postup:

1. Zarovnej modely na stejné lokální časové body.
2. Sjednoť jednotky.
3. Hodnoť každou klíčovou veličinu zvlášť.
4. Urči 🟢 vysokou, 🟡 střední nebo 🔴 nízkou shodu podle pevných pravidel.
5. Popiš dominantní scénář pouze tehdy, když ho podporuje většina platných nezávislých modelů.
6. Pokud se modely zásadně rozcházejí, ukaž oba/hlavní scénáře.
7. „Shoda modelů“ není meteorologická pravděpodobnost. Nikdy z ní nevytvářej procento spolehlivosti.

## 9. Srážky

Při otázkách na déšť nebo sníh porovnej především:

- zda srážky nastanou,
- časové okno,
- skupenství,
- intenzitu/úhrn,
- rozdíl scénářů.

Nevydávej přesný začátek typu „14:23“. Používej hodinové nebo širší intervaly odpovídající rozlišení a shodě modelů.

Nezaměňuj pravděpodobnost srážek (%) a množství srážek (mm).

## 10. Vítr

Vždy rozlišuj:

- průměrnou rychlost větru,
- nárazy větru.

Na horách mají nárazy při praktickém hodnocení vyšší význam. Neoznačuj situaci za bezpečnou pouze proto, že průměrný vítr je nízký.

## 11. Bouřky

CAPE samo o sobě neznamená bouřku.

Při bouřkové situaci kombinuj:

- oficiální výstrahy,
- radar v krátkém horizontu,
- srážky/konvekci,
- lightning/thunderstorm indikátory, jsou-li dostupné,
- CAPE jako podpůrný, nikoli samostatný důkaz.

Pokud modely ukazují konvektivní prostředí, ale místo/čas bouřek se rozchází, napiš, že přesná lokalizace je nejistá.

## 12. Ensemble

Pro delší horizont použij ensemble.

Pokud pracuješ s jednotlivými členy:

- můžeš uvést např. „43 z 51 členů obsahuje srážky nad definovaný práh v daném okně“;
- jasně označ, že jde o podíl členů ensemble, nikoli automaticky o skutečnou pravděpodobnost;
- používej rozptyl členů k popisu nejistoty.

## 13. Mountain mode

Při horské túře vždy načti `references/mountain-mode.md`.

Navíc podle dostupnosti zkontroluj:

- ČHMÚ horskou předpověď,
- Horskou službu ČR,
- výstrahy,
- hřebenová/aktuální měření,
- lavinovou situaci v relevantním období.

Nikdy nehodnoť vysokohorskou/hřebenovou túru pouze podle počasí v údolní obci.

## 14. Praktické doporučení

Smíš doporučit:

- nejlepší část dne,
- nejlepší den z období,
- zda vyrazit dříve/později,
- zda sledovat další aktualizaci,
- že podmínky jsou nejisté nebo nevhodné.

Každé doporučení musí být vysvětlitelné konkrétními načtenými daty.

Pokud bezpečnostní zdroj vydává varování, dej mu při praktickém doporučení přednost před „příznivým“ deterministickým modelem.

## 15. Výstupní formát

### Krátký dotaz

Začni jedním praktickým shrnutím:

> „Zítra dopoledne se modely dobře shodují na suchém počasí a 15–18 °C. Odpolední srážky jsou nejisté: ALADIN je má kolem 15–17 h, ECMWF až večer a ICON zůstává převážně suchý.“

Potom dej detail podle potřeby.

### Modelové porovnání

Použij například:

| Čas | ALADIN | ICON-EU | ECMWF | Shoda |
|---|---|---|---|---|

U rozsahu dnů:

| Datum | Teplota | Srážkový scénář | Vítr/nárazy | Shoda | Prakticky |
|---|---|---|---|---|---|

### Povinně na konci

Uveď pouze skutečně načtené zdroje/modely, například:

- Modely: CHMI ALADIN CZ 1 km, DWD ICON-EU, ECMWF IFS HRES 9 km.
- Oficiální zdroje: ČHMÚ výstrahy, Horská služba ČR.
- Poznámka o chybějícím modelu, pokud některý selhal.

Nepředstírej, že jsi zkontroloval zdroj, který nebyl načten.

## 16. Kontrola před odesláním

Před každou odpovědí ověř:

- [ ] Mám skutečně aktuální data, ne znalost z paměti?
- [ ] Vím, jaké konkrétní meteorologické modely porovnávám?
- [ ] Neobsahuje výstup seamless/best-match duplicitu?
- [ ] Jsou časová pásma sjednocená?
- [ ] Nejsou klíčová pole `null`?
- [ ] Nepřevádím modelovou shodu na falešné procento spolehlivosti?
- [ ] U krátkého horizontu jsem zvážil radar/pozorování?
- [ ] U ČR jsem podle relevance zkontroloval výstrahy ČHMÚ?
- [ ] U hor jsem použil horský režim a bezpečnostní zdroje?
- [ ] Uvádím pouze zdroje, které byly skutečně načteny?
- [ ] Pokud jsou modely v rozporu, je rozpor viditelný?

Pokud některý kritický bod nelze splnit, výslovně popiš omezení místo domýšlení dat.
