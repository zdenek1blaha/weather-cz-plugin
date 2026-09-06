# Mountain mode — ČR

Použij při túře, horském výletu, vrcholu, hřebeni nebo výrazné změně nadmořské výšky.

## 1. Body trasy

Pokud znáš trasu, reprezentuj ji minimálně třemi meteorologickými body:

1. start / údolní bod,
2. nejvyšší nebo hřebenový bod,
3. důležitý mezibod nebo návratový bod.

Pokud je převýšení malé, lze počet bodů snížit. Pokud je trasa dlouhá a mění orientaci/hřeben, přidej relevantní body.

Pro každý bod použij skutečnou nebo rozumně doloženou nadmořskou výšku. Pokud Open-Meteo automatická DEM výška zjevně nesedí vrcholu, použij explicitní `elevation=`.

## 2. Časování trasy

Pokud uživatel uvede 8:00–16:00, hodnoť primárně tento interval.

Pokud máš odhad etap, přiřaď počasí k přibližnému času dosažení jednotlivých bodů. Pokud čas etap neznáš, neinventuj přesný itinerář; použij širší bloky ráno/dopoledne/odpoledne.

## 3. Povinné veličiny

Podle dostupnosti:

- teplota,
- pocitová teplota (jasně jako odvozená, pokud je),
- srážky a skupenství,
- průměrný vítr,
- nárazy,
- viditelnost,
- oblačnost,
- bouřkové indikátory,
- sníh/led v relevantní sezóně.

## 4. Povinné bezpečnostní zdroje

V ČR podle dostupnosti vždy zkontroluj:

- ČHMÚ výstrahy,
- ČHMÚ horskou předpověď pro příslušnou oblast,
- Horskou službu ČR — varování/aktuální podmínky,
- lavinovou předpověď, pokud je sezónně a geograficky relevantní.

Pokud se zdroj nepodaří načíst, napiš to. Nikdy netvrď „bez varování“, pokud jsi varování neověřil.

## 5. Hřeben vs. údolí

Neodvozuj podmínky na vrcholu pouze z obce.

Výstup musí, pokud jsou data dostupná, explicitně upozornit na rozdíl mezi startem a hřebenem zejména u:

- teploty,
- nárazů,
- viditelnosti,
- skupenství srážek.

## 6. Praktické hodnocení

Nezaváděj vlastní univerzální „bezpečnostní limit“ jako absolutní pravdu. Závažnost závisí na expozici, terénu, roční době a schopnostech turistů.

Můžeš ale vysvětlit praktický dopad načtených dat, například:

- silnější nárazy na exponovaném hřebeni,
- nízká viditelnost komplikuje orientaci,
- přechod deště do sněhu/namrzání mění povrch,
- odpolední bouřkový scénář podporuje dřívější start.

Oficiální výstrahy a pokyny Horské služby mají přednost.

## 7. Doporučený výstup

### [Cíl] — [datum], [čas túry]

**Verdikt:** jedna věta založená na datech.

**Start / nižší poloha:** teplota, srážky, vítr.

**Hřeben / vrchol:** teplota, pocitová, nárazy, viditelnost, srážky.

**Nejrizikovější časové okno:** pouze pokud je doloženo modely/varováním.

**Výstrahy a Horská služba:** samostatný blok.

**Shoda modelů:** srážky / nárazy / bouřky / viditelnost podle `agreement-rules.md`.

**Doporučení:** např. dřívější start, jiný den, další kontrola ráno; vysvětli proč.

## 8. Krátký horizont

V den túry nebo pár hodin před startem:

- dej vyšší váhu nejnovějším pozorováním a radaru,
- znovu zkontroluj výstrahy,
- znovu zkontroluj Horskou službu,
- modely stále porovnej, ale nepřehlížej skutečný vývoj.
