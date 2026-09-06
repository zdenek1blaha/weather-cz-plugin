# Pravidla modelové shody

Tyto prahy jsou interní heuristika pro konzistentní popis rozdílů mezi modely. Nejsou to oficiální meteorologické pravděpodobnosti a nesmí být prezentovány jako procento spolehlivosti.

Hodnoť pouze modely s platnými daty ve stejném místě, čase a jednotkách.

## 1. Teplota

Pro konkrétní hodinu použij spread = max(modely) - min(modely).

- 🟢 vysoká shoda: spread <= 2 °C
- 🟡 střední shoda: > 2 °C a <= 4 °C
- 🔴 nízká shoda: > 4 °C

Pro denní minimum/maximum použij stejná pásma, ale neporovnávej denní agregaci s jednou hodinou.

## 2. Průměrný vítr

Pro `wind_speed_10m`:

- 🟢: spread <= 5 km/h
- 🟡: > 5 a <= 10 km/h
- 🔴: > 10 km/h

## 3. Nárazový vítr

Pro `wind_gusts_10m`:

- 🟢: spread <= 10 km/h
- 🟡: > 10 a <= 20 km/h
- 🔴: > 20 km/h

Na horách nepoužívej dobrou shodu jako synonymum bezpečnosti. Tři modely se mohou dobře shodnout na silných nárazech.

## 4. Srážky — výskyt

Definuj pro jednu hodinu:

- suchá: < 0.1 mm/h
- stopová/slabá: 0.1–0.4 mm/h
- měřitelná: >= 0.5 mm/h

Hlavní shodu posuzuj nad relevantním časovým oknem, ne izolovaně po jedné hodině.

### Vysoká shoda 🟢

- všechny platné modely jsou převážně suché; NEBO
- všechny modely mají měřitelné srážky a jejich hlavní srážkové okno se překrývá alespoň z poloviny; typicky je rozdíl začátku/ukončení <= 2 h.

### Střední shoda 🟡

- většina modelů (např. 2 ze 3) má srážky, jeden je suchý nebo jen stopový; NEBO
- všechny čekají srážky, ale hlavní časování se liší přibližně o 3–4 h; NEBO
- shoda na výskytu je dobrá, ale úhrny se významně liší.

### Nízká shoda 🔴

- alespoň jeden model je prakticky suchý, zatímco jiný ukazuje souvislejší/měřitelné srážky v relevantním okně; NEBO
- modely rozdělují srážky do zcela odlišných částí dne bez významného překryvu; NEBO
- scénář typu sníh vs. déšť je mezi modely zásadně rozdílný v místě/čase důležitém pro uživatele.

## 5. Srážky — úhrn

Pro okno 3–6 hodin nebo celý relevantní úsek:

Pokud jsou všechny úhrny < 1 mm, nepřeceňuj relativní rozdíly; důležitější je výskyt/ne-výskyt.

Pokud alespoň jeden model má >= 1 mm:

- 🟢: absolutní spread <= 2 mm NEBO nejvyšší úhrn není více než přibližně 2× nejnižší nenulový úhrn
- 🟡: spread > 2 a <= 5 mm NEBO poměr přibližně >2× až 4×
- 🔴: spread > 5 mm NEBO jeden model výrazný déšť a jiný téměř nic

Tyto prahy jsou pomocné. Při bouřkách dej vyšší váhu časování a oficiálním výstrahám než prostému dennímu součtu.

## 6. Viditelnost

Zařaď každý model do kategorií:

A: > 10 km
B: 4–10 km
C: 1–4 km
D: < 1 km

- 🟢: všechny modely ve stejné kategorii
- 🟡: maximálně sousední kategorie
- 🔴: rozdíl o dvě a více kategorií

Pro horskou túru zvýrazni C/D i při vysoké shodě.

## 7. Sníh / skupenství

- 🟢: modely se shodují na skupenství a přibližném časování
- 🟡: shoda na srážkách, ale přechod déšť/sníh se liší o několik hodin nebo výškovou úroveň
- 🔴: modely dávají v relevantním místě/čase zásadně jiné skupenství

## 8. Bouřky

Nevytvářej numerickou „shodu bouřek“ jen z CAPE.

Použij kvalitativně:

- 🟢 vyšší shoda na konvektivním scénáři: minimálně dva nezávislé modely mají konvektivní/thunder indikátor a srážkový scénář ve stejném časovém okně; nebo je podporuje oficiální výstraha/radar.
- 🟡 nejisté: pouze část modelů signalizuje konvekci nebo se výrazně liší lokalizace/čas.
- 🔴 rozpor: jeden model výrazný bouřkový scénář, ostatní stabilní/suchý scénář.

CAPE je podpůrný parametr. Bez dalších signálů neříkej, že bouřka nastane.

## 9. Celková shoda

Celkový štítek není prostý průměr barev.

Pro běžné počasí dej prioritu:

1. srážky,
2. nárazy větru,
3. teplota,
4. ostatní.

Pro hory:

1. výstrahy a bezpečnostní informace jsou mimo modelové skóre a mají absolutní prioritu,
2. bouřky,
3. srážky,
4. nárazy,
5. viditelnost,
6. teplota.

Pravidlo:

- pokud je bezpečnostně důležitá veličina 🔴, celkové hodnocení nesmí být 🟢;
- pokud jsou dvě nebo více klíčových veličin 🔴, celková shoda je 🔴;
- pokud jsou klíčové veličiny převážně 🟢 a žádná bezpečnostně důležitá není 🔴, celková shoda může být 🟢;
- jinak použij 🟡.

## 10. Dominantní scénář

Dominantní scénář lze formulovat, pokud:

- jsou k dispozici alespoň dva nezávislé platné modely; a
- většina se shoduje na hlavním praktickém výsledku.

Při 2 modelech a rozporu není většina. Ukaž oba scénáře.

Při 3 modelech může být 2:1 dominantní scénář, ale označ rozpor a nezamlč třetí model.

## 11. Falešná přesnost

Nepoužívej:

- „spolehlivost 83 %“ z heuristiky,
- přesný průměr typu 17.43 °C jako konsenzus,
- přesný čas začátku srážek na minuty.

Používej rozsah a časová okna:

- „16–18 °C“
- „přibližně 14–17 h“
- „modely se rozcházejí o 3–4 hodiny“
