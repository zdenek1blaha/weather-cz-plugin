# Zdroje a modelová politika

Tento soubor určuje technické zdroje a výběr modelů. Identifikátory ověřeny proti dokumentaci Open-Meteo v září 2026.

## Open-Meteo

Základní endpoint pro explicitní modely:

`https://api.open-meteo.com/v1/forecast`

Používej parametr `models=` s konkrétními modely. Nepoužívej `best_match` pro modelové porovnání.

Doporučené společné parametry:

- `timezone=auto` nebo explicitní IANA timezone cíle
- `temperature_unit=celsius`
- `wind_speed_unit=kmh`
- `precipitation_unit=mm`
- pro hory explicitní `elevation=` podle skutečného bodu, pokud je výška známá

### Kanonické modelové identifikátory

- CHMI ALADIN CZ 1 km: `chmi_aladin_cz_1km`
- CHMI ALADIN Central Europe 2.3 km: `chmi_aladin_central_europe_2km`
- CHMI seamless: `chmi_aladin_seamless` — NEPOUŽÍVAT jako nezávislý model pro porovnání
- DWD ICON-EU: `icon_eu`
- DWD ICON Global: `icon_global`
- ECMWF IFS HRES 9 km: `ecmwf_ifs`
- ECMWF IFS 0.25°: `ecmwf_ifs025`
- NOAA GFS Global: preferuj kanonické `ncep_gfs_global` podle aktuální OpenAPI; pokud konkrétní endpoint podporuje pouze alias `gfs_global`, použij dokumentovaný alias a označ model stejně jako NOAA GFS Global

Poznámka: Open-Meteo API se vyvíjí. Pokud server odmítne identifikátor, ověř aktuální dokumentaci a použij aktuálně dokumentovaný ekvivalent. Nikdy potichu nepřejdi na Best Match.

## Horizonty a role

### 0–72 hodin

1. `chmi_aladin_cz_1km` — hlavní lokální model pro ČR, 1 km, hodinově, přibližně 3 dny, aktualizace každých 6 h.
2. `icon_eu` — nezávislý regionální model DWD, přibližně 7 km, 5 dní; po ~78 h může mít nativně řidší časový krok interpolovaný na hodinový.
3. `ecmwf_ifs` — nezávislý globální IFS HRES, 9 km, až 15 dní; nativně 1 h do ~90 h, později řidší krok interpolovaný Open-Meteo.

### 4.–5. den

1. `icon_eu`
2. `ecmwf_ifs`
3. NOAA GFS Global

ALADIN již není nativně dostupný. Nepoužívej jeho seamless pokračování jako „ALADIN“.

### 6.–7. den

1. `ecmwf_ifs`
2. `icon_global`
3. NOAA GFS Global

### 8.–10. den

Deterministické modely uváděj jako scénáře. Přidej ensemble:

- ECMWF IFS 0.25° ensemble: `ecmwf_ifs025_ensemble`
- GFS ensemble 0.25°: `ncep_gefs025`

Ensemble endpoint:

`https://ensemble-api.open-meteo.com/v1/ensemble`

### 11.–15. den

Primární:

- ECMWF IFS ensemble (51 členů)
- GFS ensemble (31 členů) podle dostupného horizontu

Deterministický ECMWF může být referenční scénář, ne „jistá předpověď“.

### 16–35 dní

Pouze dlouhodobý trend. GFS Ensemble 0.5° (`ncep_gefs05`) má dlouhý horizont až přibližně 35 dní, ale nízké prostorové rozlišení kolem 50 km. Nepoužívej jej pro lokální hodinové tvrzení.

## Open-Meteo odvozené hodnoty

U ALADINu jsou nativní mimo jiné teplota, vlhkost, vítr, tlak, oblačnost, srážky, radiace, CAPE, viditelnost a sunshine duration. Open-Meteo některé veličiny dopočítává, například weather code a apparent temperature.

U ICON/GFS/ECMWF může být část pohodlných API veličin také odvozena nebo časově interpolována. Pokud je původ veličiny významný pro závěr, ověř dokumentaci modelu.

## ČHMÚ — autoritativní zdroje

### Výstrahy CAP

Index:

`https://opendata.chmi.cz/meteorology/weather/alerts/cap/`

Používej aktuální platnou CAP zprávu, nikoli náhodný historický XML soubor. Ověř platnost jevu pro cílovou oblast a čas.

### Předpověď / horská předpověď

Open data root:

`https://opendata.chmi.cz/meteorology/weather/forecast/`

Pro horské oblasti mohou být dostupné strojově čitelné JSON předpovědi. Používej pouze soubor odpovídající oblasti a aktuálnímu vydání.

### ALADIN open data

`https://opendata.chmi.cz/meteorology/weather/nwp_aladin/`

Přímý GRIB je autoritativní upstream, ale pokud runtime neumí GRIB spolehlivě zpracovat, nepředstírej jeho načtení. Pro modelové porovnání použij Open-Meteo a ČHMÚ přímo jako oficiální text/výstrahy.

### Radar / stanice

Pro krátkodobý dotaz používej aktuální oficiální radar/pozorování ČHMÚ, pokud je lze spolehlivě načíst v daném runtime. Neodvozuj přesné časy pohybu buněk pouze z jednoho radarového snímku.

## Horská služba ČR

Hlavní sekce:

`https://www.horskasluzba.cz/cz/pocasi-na-horach`

Používej podle oblasti:

- varování HS,
- počasí na hřebenech / aktuální podmínky,
- lavinovou předpověď,
- sněhové podmínky.

Horská služba uvádí, že předpovědi jsou orientační. Její varování a místní pozorování však používej jako samostatnou praktickou bezpečnostní vrstvu.

## Selhání a fallback

- Jeden nedostupný model: pokračuj se zbývajícími a explicitně to řekni.
- Jen jeden platný model: předpověď může být zobrazena, ale bez tvrzení o nezávislé shodě.
- Žádný platný model: neposkytuj modelovou předpověď z paměti.
- ALADIN CZ 1 km s převahou null: fallback na ALADIN Central Europe 2.3 km; poté ICON-EU + ECMWF.
- Chybějící výstražný zdroj: neříkej „žádná výstraha“, ale „výstrahy se nepodařilo ověřit“.

## Poznámka k přesnosti

Vyšší prostorové rozlišení neznamená automaticky vždy přesnější předpověď. Modely porovnávej jako nezávislé scénáře a důvěru opírej o shodu, aktuální pozorování a oficiální varování, ne pouze o počet kilometrů mřížky.
