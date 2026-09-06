# Eval prompts

Použij pro ruční otestování skillu.

1. „Jak bude zítra v Brně od 7 do 18 hodin? Hlavně mě zajímá déšť.“
   - Musí použít aktuální data, explicitní modely, žádný Best Match, ukázat časování srážek a shodu.

2. „Bude za dvě hodiny pršet v Olomouci?“
   - Musí preferovat nowcast/radar + aktuální data; nesmí spoléhat pouze na denní model.

3. „V sobotu chceme na Praděd, start 8:00, návrat kolem 16:00. Je to dobrý nápad?“
   - Musí aktivovat Mountain mode, více výškových bodů, ČHMÚ výstrahy, Horskou službu a modelové porovnání.

4. „Jak bude v Beskydech za 12 dní?“
   - Musí přejít na ensemble/trend a odmítnout falešně přesnou hodinovou předpověď.

5. „Open-Meteo ALADIN vrací samé null. Co teď?“
   - Musí použít fallback ALADIN Central Europe 2.3 km, poté ICON-EU + ECMWF; nesmí modelová data vymyslet.

6. „ALADIN má déšť 14–17, ICON 15–18 a ECMWF 15–17. Co z toho plyne?“
   - Má vyhodnotit vysokou shodu časování a uvést přibližné společné okno, bez minutové přesnosti.

7. „ALADIN ukazuje 5 mm, ICON 0.3 mm a ECMWF 0 mm.“
   - Má označit výrazný rozpor ve srážkovém scénáři, nikoli dát průměr 1.77 mm.

8. „Je bez výstrahy na Krkonoše?“ při nedostupném ČHMÚ.
   - Musí říct, že výstrahy se nepodařilo ověřit; nesmí tvrdit, že žádná není.
