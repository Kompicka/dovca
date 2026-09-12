# Vestavba dodávky – plánovací stránka

Statická stránka (GitHub Pages) pro společné plánování koupě a dostavby obytné dodávky. Majitel: Kp (GitHub Kompicka). Jazyk stránky i komunikace: čeština.

Živá stránka: https://kompicka.github.io/dovca/

## Co je v repozitáři

- `index.html` – celá aplikace v jednom souboru (vanilla JS, žádný build). Záložky: Požadavky, Nálezy, Koupit vs. postavit, Elektro a design, Plán stavby, Deník.
- `data/db.json` – jediný zdroj dat. `plan.*` = dokumenty plánu, `deals[]` = inzeráty. Stránka ho načítá přes `fetch('data/db.json')`.
- `.nojekyll` – GitHub Pages bez Jekyllu.

Stránka umí běžet i jako claude.ai artifact s `db` capability (kód pro `window.claude.use('db')` je zachovaný). Na GitHub Pages `window.claude` neexistuje, takže se jede staticky: změny uživatele (stav inzerátu, poznámky, odškrtnuté úkoly, deník, úpravy rozpočtů) se ukládají jen do `localStorage` klíč `vb-local`. Tlačítko „Zkopírovat změny pro Claude“ v banneru zkopíruje ten JSON; uživatel ho pošle a Claude ho promítne do `data/db.json` a commitne.

## Jak dělat změny (workflow pro Claude)

1. Data měnit v `data/db.json`, ne v HTML. Po změně nastavit `exportedAt` na dnešní datum.
2. Nové inzeráty přidávat do `deals[]` jako objekty se schématem níže, `id` = `<zdroj>-<číslo inzerátu>` (písmena, číslice, pomlčky). Nikdy nevymýšlet inzeráty ani URL, jen to, co bylo skutečně načteno.
3. Commit a push na `main`. GitHub Pages se přegeneruje do minuty.
4. Když uživatel pošle JSON z „Zkopírovat změny“: `dealOv` jsou přepisy stavů/poznámek podle `id`, `localDeals` ručně přidané inzeráty, `state.*` celé dokumenty plánu. Sloučit do `db.json`.
5. Neměnit UI texty bez důvodu, drží se tón: stručně, česky, konkrétně, bez nadsázky.

## Schéma `data/db.json`

```
plan.requirements: {yearMin, kmMax, fuel, seats, eur (kurz EUR→CZK), len[], hgt[], models[], src[], kind[], budgetVan, budgetCamper, budgetBuild, dist, notes, updatedAt, updatedBy}
plan.compare:      {camper, camperFix, camperRes, van, hours, rate, buildRes, items[[název, Kč, poznámka, 'elec'?]]}  – položka se 4. prvkem 'elec' se počítá automaticky ze součtu plan.elec.parts
plan.elec:         {volt, bat (kWh využitelné), pv (Wp), alt (W), drive (h/den), eff (%), inv (W), peak (W), loads[[název, W, h/den, 230|12]], parts[[název, ks, Kč/ks, obchod, url, poznámka, kategorie]], pricedAt}
plan.layout:       {van: 'ducato-l3'|'ducato-l2'|'ducato-l4'|'transit-l3'|'sprinter-l3', blocks[{n,x,y,w,h,c}]}  – mm, x podél délky od kabiny
plan.build:        {phases[{name, tasks[[text, kdo, hotovo]]}]}
plan.log:          {entries[{date, who, text}]}
plan.scan:         {lastRun, sources[], found, note}
deals[]:           {id, title, kind: 'van'|'camper'|'partial', model, price, currency: 'CZK'|'EUR', priceNote?: 'netto'|'brutto', year, km, length, height, interiorHeightCm?, seats?, source, url, location, score 1–5, notes, status: 'new'|'watch'|'contacted'|'rejected', foundAt, addedBy: 'scanner'|'manual'}
```

Ceny v EUR se v UI přepočítávají kurzem `plan.requirements.eur` (24,5).

## Kdo to je a co chce (rozhodnutí k 6. 9. 2026)

- Posádka: 2 dospělí + 1 malé dítě. Majitel měří 189 cm a bude z auta pracovat: od 12. 9. 2026 notebook s GPU napájený USB-C (cca 150 W) + 2 monitory USB-C (60 W), cca 6 h denně (dříve PC se zdrojem 800 W – zrušeno). Chce lednici 130–140 l a místo Maxxfanu úspornou střešní DC klimatizaci (Velit 2000R 48 V, EU náhrada Dometic RTX 2000). Právě si půjčuje 7m McLouis a nesnáší ho (šířka, přesah, cedule zakazující obytné vozy). Dodávka je záměrně nenápadná.
- **Strategie (7. 9. 2026)**: hotovky prověřeny a zavrženy (chce vlastní řešení). Koupit prázdnou dodávku L3H3 nebo L4H3 za 500–550 tis. Kč (kandidáti: Boxer L3H3 2021 a Ducato Maxi L3H3 2020 u DAVO CAR Benešov, záloha Jumper L4H3 2024) a nechat udělat **vestavbu na míru podle vlastního návrhu** firmou. Elektro (48 V) montuje vestavbová firma nebo její elektrikář podle našeho návrhu (změna 12. 9. 2026, majitel si ho sám nedělá). LCD kabina není podmínka. Svépomoc jinak odmítnuta.
- **Rozhodnutí k vestavbě (7. 9. 2026)**: topení jen nafta (Autoterm Air 4D), v autě žádný plyn; kuchyň indukční deska + malá elektrická trouba 230 V; postel elektricky zvedací ke stropu (Lippert Project 2000); samostatná sedačka spolujezdce s otočnou konzolí; zadní dvojsedačka FASP Divan 503; klima a kamera v autě nutné, motor nerozhoduje.
- **Ceny vestavby**: firmy na míru (MS Camper, NarubyVans, New Age Nomads) ceny nezveřejňují, jen poptávka, 2–5 měsíců. Veřejný je jen katalogový ceník FullVans (380–673 tis. s DPH), ten NEPOUŽÍVAT jako cenu vestavby na míru. V plánu je 800 tis. jako odhad do doby 3 skutečných nabídek.
- Filtr: Ducato/Boxer/Jumper/Transit, L3 (5,99 m) cíl, L4 hraničně (skóre −1), rok 2018+, do 150 tis. km, prázdná dodávka 500–550 tis. Kč s DPH (rozhodnuto 7. 9. 2026). Majitel nemá celkový limit ani limit na vestavbu; „1 mil. Kč“ byl jen filtr skeneru hotovek, nikdy ho nepsat jako rozpočet majitele. 4 místa v TP. Postel 190 cm stačí. Vnitřní výška min. 190 cm; H3 základ je plus. Bezpečná koupě: 1. majitel, servisní kniha, dealer se zárukou, ne kurýr/taxi, bez rzi. Německo je v pořádku (majitel tam klidně zajede).
- **Otevřená otázka**: jestli majiteli stačí H2 (uvnitř ~190 cm). Má si to jít vyzkoušet do Ducata H2 (fáze 1 plánu). Když ne, jen H3 a sériové hotovky padají (sériové obytňáky na H3 neexistují, H3 jen jako vestavby na Transitu).
- Zamítnuto: Giottiline GiottiVan 60 Be Young 2026 (1,3 mil. s DPH): H2, postel 186 cm, žádné elektro na práci, nápadný.
- Zápis vozidla: kvůli dítěti vzadu je potřeba homologované sedadlo s pásem a zápis jako obytný automobil (M1). ISOFIX není nutný, tříbodový pás ano.

## Ověřená čísla (6. 9. 2026, odkazy v db.json)

- Vestavba firmou (bez auta): základ 330–380 tis. Kč (Vestavby dodávek na míru „od 330 000“, FullVans Siesta Classic 380 049 s DPH), standard až 672 670 (FullVans Siesta Via), ruční stavba 28–33 tis. €. Čekací doby 3 týdny až 5 měsíců. V ceně „od“ nebývá: auto, sedadla, schválení, solár, dražší okna.
- Sedadlo s tříbodovými pásy FASP Divan 503: 37 900 Kč (nomadem.cz). Schválení přestavby na obytný vč. zápisu: 6 776 Kč TOM-CAR Maršovice, úřad 1 000 Kč.
- 48V elektro (Pylontech 2× US5000, MultiPlus-II 48/3000, MPPT 150/45, 2× Orion-Tr 12/48, Orion 48/12-30, SmartShunt, Cerbo, Lynx, panely 2× 440 W, kabely, rozvaděč): cca 130 tis. Kč, položky v `plan.elec.parts` s odkazy na české e-shopy.
- Trh hotovek 2018+ L3: ČR 935 tis.–1,34 mil. Kč a téměř nic; Německo 38–41,5 tis. € za sériové (Adria Twin, Globecar, Pössl Roadcar) s 45–66 tis. km, svépomocné 25–35 tis. €. Německo je o 20–30 % levnější.
- Prázdné L3H3 2018+: ČR 444–460 tis. Kč (DAVO CAR Benešov), Německo Transit L3H3 2019–2021 za 15,9–21,5 tis. €.

## Skener inzerátů

- Skenuje se lokálně z tohoto počítače (WebFetch dosáhne na auto.bazos.cz výpisy, sauto.cz, kleinanzeigen.de, autoscout24.de výpisy). Blokované: mobile.de (403), willhaben.at, autobazar.eu, detaily na bazos.cz (404), tipcars detaily (403).
- Cloudová routine „Skener dodávek – vestavba“ (trig_01Tso9Lk1XNzKcHmzHjzx5pZ) je **vypnutá**: sandbox v cloudu má blokovaný egress na všechny inzertní weby. Zapnout jen pokud půjde povolit domény v nastavení prostředí.
- Postup skenu: přečíst `plan.requirements`, projít zdroje, přijmout jen inzeráty splňující filtr, dedupe podle `url`, uložit do `deals[]` se `status: 'new'`, `addedBy: 'scanner'`, aktualizovat `plan.scan` a přidat záznam do `plan.log`.

## Historie

- Původně to byl claude.ai artifact se sdílenou DB (https://claude.ai/code/artifact/12dea376-74ff-486d-82e0-b2b9aa6a2edc). 6. 9. 2026 přesunuto na GitHub Pages na přání majitele; artifact už není zdroj pravdy.
