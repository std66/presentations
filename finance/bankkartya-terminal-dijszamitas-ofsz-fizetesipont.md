# Mennyibe kerül a bankkártyás fizetés elfogadása a kereskedőnek?

## Információk
- **Szerző**: Sinku Tamás (sinkutamas@gmail.com)
- **Licenc**: Creative Commons BY-NC-SA
- **Utoljára módosítva**: 2026. szeptember 15.

## Felelősség és garancia

A szerző semmilyen felelősséget és garanciát nem vállal a dokumentumokban leírt információk helyességéért, sem azok pontosságáért. Az olvasó elfogadja, hogy az alábbi információkat teljes mértékben saját felelősségére használja fel.
A dokumentumban írtak felhasználása és alkalmazása során keletkezett közvetlen és közvetett károkért a szerző nem tartozik kártérítési felelősséggel és kártérítési kötelezettséggel sem terhelhető.

**Fontos:**
1. A szerző a pénzügyi szakmák világában laikus, nem pedig képzett szakértő. (*Meg úgy a jogban is, szóval lehet, hogy feleslegesen írja ezt a szöveget ide, de legalább megnyugtatja magát, hogy megpróbálta.*)
2. Amit a szerző leír, az az ő személyes véleménye; és nem pontosságra, valamint szakmai alaposságra törekszik, hanem bemutatja a saját interpretációját a témában tapasztaltakról, valamint megőrzi azt.
3. Ez a leírás nem minősül semmiféle pénzügyi tanácsadásnak. A szerző explicit módon kijelenti, hogy nem is használható annak.

Az olvasó kijelenti, hogy ennek teljes tudatában olvassa tovább a dokumentumot.

## Kontribúció és észrevételek

A szerző örömmel veszi az olvasók visszajelzését és tartalombeli hozzájárulásait a még pontosabb információk megosztásáért. Az olvasó ezt kétféleképp teheti meg:
1. Javaslatot vagy hibákat jelezhet GitHub Issue nyitásával.
2. Szerkesztési javaslatot adhat be GitHub Pull Request nyitásával.

Az eredeti GitHub repository elérhető itt: https://github.com/std66/presentations

## Bevezetés

Ebben a dokumentumban a **Fizetési Pont** és az **OFSZ** példáján keresztül szeretném elmagyarázni, hogy miképp számolható ki az, hogy egy bankkártyás fizetés után ténylegesen mennyi pénz marad a kereskedő zsebében.

## Kik vesznek részt a bankkártyás fizetési folyamatban?

Neked mint kereskedőnek
1. van egy valamelyik **bank** által (a példánkban az OFSZ) vezetett vállalkozói bankszámlád;
2. emellett valamelyik **bankkártyás fizetési szolgáltató** (a példánkban a Fizetési Pont) bérbe ad egy bankkártya-terminált (POS-terminálnak is szokás hívni: Point of Sale).

A vásárlódnak
1. úgyszintén van egy bankszámlája, amelyet valamelyik **bank** vezet,
2. és van egy valamelyik **kártyatársaság** által, a bankon keresztül kibocsátott **bankkártyája**, amellyel a bankszámlájáról költheti a pénzt.

## Mire keressük a választ?

Neked mint kereskedőnek van egy számítástechnikai üzleted. Bemegy hozzád a vásárló, mert mondjuk akciós nálad az Intel Arc A310-es videókártya, és ezt meg akarja venni. A videókártya árát, amely 59.270 Ft, a bankkártyájával fizeti ki neked.

**A fő kérdések:**
1. Mennyi pénz marad meg a vásárló által kifizetett pénzből, miután **a te bankod** és a **bankkártyás fizetési szolgáltató** levonta a díjait?
2. Hogyan lehet ezt kiszámolni?
3. Hol lehet még szívatásokat és meglepetés díjakat keresni?

## A beszerzendő dokumentumok

A pénzügyi szektor többnyire két elnevezést szeret használni azon dokumentumainak megnevezésére, amelyek a díjakat határozzák meg:
- hirdetmény
- kondíciók / kondíciós lista

Mivel kereskedőként két jogi személlyel állsz kapcsolatban (a bankkal és a bankkártyás fizetési szolgáltatóval), el kell menned mindkettő weboldalára, és meg kell keresned ezeket a hirdetményeket.

Ezeket a dokumentumokat itt találod a példánkhoz (a hivatkozások a dokumentum írásának pillanatában érvényesek):

**OFSZ:** [Tájékoztatók és dokumentumok](https://ofsz.hu/hu/tajekoztatok/hatalyos-dokumentumok) - OFSZ-Elfogadói Hirdetmény

**Fizetési Pont:** [Letölthető dokumentumok](https://www.fizetesipont.hu/letoltheto-dokumentumok) - Hirdetmény

## Mit fizetsz ki kereskedőként?

Nincs ingyen az, hogy a vásárló pénze eljut az ő bankszámlájáról a te bankszámládra a vásárlód kártyatársaságának és a te **POS-terminálodnak** a közreműködésével. Ezért fizetni fogsz
1. a **vásárló kártyatársaságának** (VISA, MasterCard, Maestro, AmEx, ...), aki eljuttatja a pénzt a két bank között,
2. valamint a **bankkártyás fizetési szolgáltatónak**, hogy lekommunikálja a tranzakciót a kártyatársasággal.

Ideális esetben a vásárlód bankszámlájának devizaneme és a te vállalkozói bankszámlád devizaneme megegyezik. Magyarul: te forintban kéred a pénzt, a vásárlód pedig forinttal fizet.

Ha nem ez a helyzet, és a vásárlód bankszámlája kínai jüanban van vezetve, a jüanban nyilvántartott devizát az éppen aktuális árfolyamon kettőtök közül valakinek át kell váltania. Erről a vásárló dönt a fizetés pillanatában – felkínálja neki a terminál, hogy forintban fizet és az ő bankja vált, vagy jüanban fizet és a te bankod vált. Ezt hívják **DCC**-nek, azaz **Dynamic Currency Conversionnek** (dinamikus pénznem-átváltás).

**Fontos:** A továbbiakban kizárólag azt az esetet taglaljuk, ahol **nincs DCC** a fizetés során.

## A vásárló kártyatársaságának és a bankodnak fizetett díj

A vásárlási folyamat elkezdődik azzal, hogy
1. a POS-terminálba bepötyögöd, hogy a vásárlód a videókártyáért 59.270 Ft-ot fog fizetni.
2. A vásárló bedugja/lehúzza/lecsipogja a bankkártyáját, amelyből a POS-terminál a kártya száma alapján meghatározza, hogy melyik kártyatársasággal kell kommunikálnia. Hogyan történik ez?

**A BIN-szám fontossága**: A **BIN-szám** (Bank Identification Number – banki azonosítószám) a bankkártya számának első 6–8 számjegye. Ez azonosítja a kártyatársaságot, a bankkártya típusát (lakossági vagy üzleti, betéti- vagy hitelkártya), valamint a vásárló számlavezető bankját. Próbáld ki a saját bankkártyáddal ezen az oldalon: https://spend.net/hu/bin-checker

### Díjszabási modellek a bankkártya-elfogadásban

A kártyaelfogadó bankok és szolgáltatók alapvetően háromféle elszámolási modellt alkalmazhatnak a hirdetményeikben:

1. **Interchange++ (IC++)**: A legtöbb felmerülő díjat tételesen jeleníti meg, ezáltal bár bonyolultnak tűnik elsőre, a legjobb betekintést nyújtja a konkrét fizetendő tételekbe. A szolgáltató teljesen megnyitja a lapjait: a tranzakciónként változó bankközi jutalékot (`Interchange`) és a kártyatársasági díjat (első `+`) tiszta önköltségi áron továbbhárítja rád, és kizárólag a saját, rögzített árrését (második `+`, pl. 0,25% + 10 Ft) teszi hozzá.
2. **Blended (egybeszámított / flat-rate)**: A szolgáltató egyetlen fix százalékot mond (például *"minden fizetés után 1,5%-ot vonunk le"*), amelyben az összes költségelem egyben benne van. Bár egyszerűen kalkulálható, a szolgáltató ebben beleszámolja a saját kockázatát is. Ha a vásárlóid főleg 0,2%-os bankközi jutalékú lakossági betéti kártyákkal fizetnek, a szolgáltató jelentős extra profitot tesz zsebre.
3. **Unbundled / Interchange Pass-Through (az OFSZ által használt modell)**: Ez egy átmenet a kettő között. A bank a számlán átlátható módon, külön tételekként jeleníti meg a bankközi jutalékot és a kártyatársasági díjat (tehát nem Blended), viszont a saját fizetendő részét nem egy piaci alapon mozgó pici árrésként (mint az IC++), hanem egy magas, fix kereskedői díjként (pl. 1,2%) határozza meg. Emiatt a szerkezete átláthatóbb a Blendednél, de a végső költsége a kereskedő számára hasonlóan magas lehet.

*Megjegyzés: Kisebb forgalmú kereskedőknél előfordulhat még a **Flat/Csomagalapú** árazás is, ahol a kereskedő egy fix havi díjat fizet bizonyos forgalmi keretig, pl. havonta 10 000 Ft legfeljebb 500 000 Ft-os kártyás forgalomig.*

### Az OFSZ Interchange Pass-Through modelljének értelmezése

A bankod (OFSZ) hirdetménye valószínűleg már alaposan megrémisztett a bonyolult táblázataival. Ebből ráadásul lehet több is, ha DCC-vel többféle devizanemet is elfogadsz. Erre azért van szükség, mert a bankkártya típusától függ, hogy milyen díjat fog felszámítani a kártyatársaság és a bank:

| O.F.SZ. Zrt. által kibocsátott kártyák | Kereskedői díj | Bankközi jutalék | Kártyatársági díj |
| :--- | :---: | :---: | :---: |
| MasterCard lakossági betéti kártya | 1,2% | - | 18 Ft + 0,55% |
| MasterCard üzleti betéti kártya | 1,2% | - | 18 Ft + 0,55% |
| VISA lakossági betéti kártya | 1,2% | - | 18 Ft + 0,55% |
| VISA üzleti betéti kártya | 1,2% | - | 18 Ft + 0,55% |

| Más bank által kibocsátott kártyák | Kereskedői díj | Bankközi jutalék | Kártyatársági díj |
| :--- | :---: | :---: | :---: |
| MasterCard lakossági betéti kártya | 1,2% | 0,2% | 18 Ft + 0,55% |
| MasterCard lakossági hitelkártya | 1,2% | 0,3% | 18 Ft + 0,55% |
| MasterCard üzleti hitelkártya | 1,2% | 1,75% | 18 Ft + 0,55% |
| MasterCard üzleti betéti kártya | 1,2% | 1,25% | 18 Ft + 0,55% |
| Maestro kártya | 1,2% | 0,2% | 18 Ft + 0,55% |
| Visa lakossági betéti kártya | 1,2% | 0,2% | 18 Ft + 0,55% |
| Visa lakossági hitelkártya | 1,2% | 0,3% | 18 Ft + 0,55% |
| Visa üzleti hitelkártya | 1,2% | 1,35% | 18 Ft + 0,55% |
| Visa üzleti betéti kártya | 1,2% | 1,35% | 18 Ft + 0,55% |
| V PAY | 1,2% | 0,2% | 18 Ft + 0,55% |

*(Az OFSZ 2026. augusztus 4-től hatályos hirdetménye szerint.)*

Ezek a díjak tárgyi adómentesek (nem kell áfát fizetni utánuk).
További díjtételek szerepelhetnek a bankkártya kibocsátásának régiója alapján is. Eltérő díjak vonatkozhatnak az EEA-ban és azon kívüli gazdasági területeken kibocsátott bankkártyákra.
Az EEA területén kibocsátott lakossági betéti- és hitelkártyák bankközi jutalékai a dokumentum írásának időpontjában az EU által maximalizálva vannak (0,2% betéti kártyára, 0,3% hitelkártyára).

**Bankközi jutalékot** általában nem kell fizetni, ha a pénz bankon belül mozog, vagyis a te számlavezető bankod és a vásárlód számlavezető bankja egy és ugyanaz a bank. *Ellenőrizd a hirdetményeket!*

A példában a vásárlónk a videókártyáját **VISA lakossági betéti kártyával** fizeti ki az **MBH Banknál** vezetett számláján (ami egy másik bank, tehát van bankközi jutalék). Ezek alapján az alábbi díjak lesznek levonva a kifizetett összegből. A százalékok mindig az eredeti kifizetett összegből számolódnak ki:
- **1,2% kereskedői díj**: 59.270 Ft * 0,012 = 711,24 Ft
- **0,2% bankközi jutalék**: 59.270 Ft * 0,002 = 118,54 Ft
- **18 Ft + 0,55% kártyatársasági díj**: 18 Ft + (59.270 Ft * 0,0055) = 343,985 Ft

Ezeket a tételeket összeadjuk és kerekítjük: 1173,765 Ft, kerekítve **1174 Ft**.

### A díjak összehasonlítása más díjszabási modellekkel

Mennyit bukik a kereskedő a videókártyán a különféle árazási modelleknél?

Annak szemléltetésére, hogy a fizetési szolgáltató elszámolási modellje mennyire drasztikusan befolyásolja a kereskedő zsebében maradó pénzt, nézzük meg, mennyibe kerülne a példánkban szereplő 59.270 Ft-os Intel Arc A310 videókártya kártyás fizetése (VISA lakossági betéti kártyával) a három fő árazási modellben.

*Fiktív összehasonlítás. A Blended és az IC++ modellhez általam kitalált értékeket használtam, amely nem a dokumentum írásakor érvényes valós banki díjakon alapszik.*

*(A kalkulációban a banki/kártyatársasági levonásokat számoljuk; ehhez jön még hozzá a fizetési szolgáltató termináldíja).*

#### 1. Blended (egybeszámított) modell (pl. fix 1,5%-os díj)
* **Tranzakció ára:** 59.270 Ft * 0,015
* **Levonás a bank részéről:** **889 Ft** (1,50%)
* **A kereskedőnél marad:** **58.381 Ft**

#### 2. Unbundled / Pass-Through modell (OFSZ példája)
* **Kereskedői díj (1,2%):** 711 Ft
* **Bankközi jutalék (0,2%):** 119 Ft
* **Kártyatársasági díj (18 Ft + 0,55%):** 344 Ft
* **Levonás a bank részéről:** **1174 Ft** (1,98%)
* **A kereskedőnél marad:** **58.096 Ft**

#### 3. Interchange++ (IC++) modell (pl. 0,3% + 10 Ft szolgáltatói árréssel)
* **Bankközi jutalék (0,2%):** 119 Ft
* **Kártyatársasági díj (18 Ft + 0,55%):** 344 Ft
* **Szolgáltatói árrés (0,3% + 10 Ft):** 188 Ft
* **Levonás a bank részéről:** **651 Ft** (1,10%)
* **A kereskedőnél marad:** **58.619 Ft**

#### Összehasonlító táblázat

| Elszámolási modell | Levont banki jutalék | Tényleges levonási arány | A kereskedőnél maradó összeg |
| :--- | :---: | :---: | :---: |
| **Interchange++ (IC++)** | **651 Ft** | **1,10%** | **58.619 Ft** |
| **Blended (fix 1,5%)** | **889 Ft** | **1,50%** | **58.381 Ft** |
| **OFSZ (Pass-Through)** | **1174 Ft** | **1,98%** | **58.096 Ft** |

#### Tanulság

A fiktív példa alapján ugyanannál a 59.270 Ft-os videókártyás vásárlásnál **több mint 500 Ft különbség** keletkezik a zsebedben csupán attól függően, hogy milyen árazási struktúrában állapodtál meg a kártyaelfogadóval.

Mivel az európai vásárlók túlnyomó többsége alacsony (0,2%-os) bankközi jutalékú lakossági betéti kártyát használ (*feltételezés*), az **Interchange++** modellből profitál a legtöbbet a kereskedő, míg az OFSZ-féle magas fix kereskedői díjas szerkezet jelenti a legmagasabb költséget.

### Hogyan kell értelmezni a bank által küldött bankkártya-elfogadói kimutatást?

Ez a dokumentum az előző hónap bankkártyás vásárlásainak végösszegét, a vásárláshoz használt kártya típusát, és az ezek alapján számított díjakat és arányszámokat tartalmazza. Az OFSZ esetén így néz ki:

| Dátum | Kártyaszám | Tr.szám | Összeg | Jutalék | Bankközi jut. | Jut. % | Nettó | Megjegyzés |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| 2026. 07. 01. | 428312********** | *kitakarva* | 59.270 Ft | 1174 Ft | 119 Ft | 1,95% | 58.096 Ft | VI_RET_NE_DB |

Az oszlopok jelentése:
- **Dátum**: A vásárlás napja
- **Kártyaszám**: A vásárló bankkártyájának első 6–8 számjegye (BIN), valamint az utolsó 4 számjegye (kitakartam a fenti példában)
- **Tranzakció szám**: A vásárlás egyedi azonosító kódja
- **Összeg**: Ennyit fizetett ki a vásárlód
- **Jutalék**: Ennyi jutalékot vontak le a vásárló által kifizetett összegből a bankodnak és a vásárló kártyatársaságának
- **Bankközi jutalék**: A `Jutalék` azon része, ami a két bank közti pénzmozgatásra ment el (nem fontos, csak érdekes)
- **Jutalék %**: A kereskedő egyedi szerződésében rögzített indikatív (becsült) átlagos jutalékkulcs a fizetéshez használt bankkártya típusára vonatkozóan. **Nem a konkrét tranzakcióból visszaszámolt tényleges matematikai arány.**
- **Nettó**: Ennyi marad neked (elvileg, mert amúgy nem – a bankkártyás fizetési szolgáltató díja még ebből jön le később)
- **Megjegyzés**: A fizetéshez használt kártya típusa

A megjegyzés rovat furcsa kódnak tűnhet, de megfejthető.
- **VI**: VISA
- **RET**: Retail (lakossági kártya)
- **DB**: Debit (betéti kártya)

További példák:
- **MC_RET_DB**: MasterCard lakossági betéti kártya
- **VI_BUS_CR**: VISA üzleti hitelkártya
- **MAESTRO**: Maestro kártya

### Hogyan jelenik meg a számlakivonatban?

Bankonként eltér a jóváírás időpontja. OFSZ esetében az előző napi bankkártyás fizetések jóváírása a következő banki nyitvatartási napon egyösszegben **történik**, két tranzakció formájában:
1. Az első jóváíró tranzakció jóváírja az előző napi bankkártyás vásárlások azon összegét, amelyet eredetileg kifizetett a vásárló. (Partner neve = `POS forgi jóváírás  kereskedő sz`)
2. A második terhelési tranzakció levonja az előző napi bankkártyás vásárlások összesített jutalékát. (Partner neve = `Kártyaelfogadói jut.terh.-nem OF`)

## A bankkártyás fizetési szolgáltatónak fizetett díjak

A bankkártyás fizetési szolgáltató kétféle díjat számít fel, mivel két feladata van: üzemelteti és bérbe adja a POS-terminált, valamint lebonyolítja a kártyatársasággal az egyes tranzakciókat.
1. Minden egyes tranzakciónak díja van, amely lehet fix, arányos a vásárlás mértékével, vagy hibrid (fix + arányos díj összege).
2. A POS-terminál üzemeltetésének és bérbeadásának (használatának), valamint infrastruktúrájának (mobilinternet-hozzáférés) díja.

### A tranzakciónkénti díj

Miután megtudtuk, hogy a vásárló által kifizetett 59.270 Ft-ból az első kör díjait kifizetve már csak 58.096 Ft maradt meg nekünk, ideje, hogy még kevesebbet tarthassunk meg belőle, hála a POS-terminál díjainak. Hogy mennyivel?

A POS-terminál díjait a bankkártyás fizetési szolgáltató hirdetménye írja le. Ez már barátibb:

| Terminálonként vagy tranzakciónként fizetendő díjak | |
| :--- | :---: |
| Alapdíj | 0,- Ft |
| Tranzakciós díj (tranzakciónként) | 7,- Ft |
| Hálózati kapcsolat díja | 0,- Ft |
| Forgalomarányos változó díj | 0,4 % |
| Terminál használati díj | 2350,- Ft |

*(A Fizetési Pont 2024. december 1-jétől hatályos hirdetménye szerint.)*

**Nettó díjak, 27% ÁFA-kötelezettséggel.** A hirdetmény szerint:
>Jogszabály alapján a fenti díjak esetében **áfa-felszámítási kötelezettség** keletkezhet.

Ez a táblázat ötvözi a kétfajta díjköltséget. Vásárlásonként a `Tranzakciós díj` és a `Forgalomarányos változó díj` lesz levonva a vásárló által kifizetett **eredeti összegből**.

**Példa:** 7 Ft + (59.270 Ft * 0,004) = 7 Ft + 237,08 Ft = 244,08 Ft, amelyet kerekítünk: **244 Ft** a Fizetési Pont díja nettóban. **Bruttóban 310 Ft** (244 Ft * 1,27 = 309,88 Ft).

**Végül 57.786 Ft maradt a vállalkozásnak, összesen 1484 Ft-unkba került a vásárló bankkártyás fizetése a tranzakcióra vonatkozó díjtételek tekintetében.**

### A havi díj

Minden egyes POS-terminál után ki kell fizetni az `Alapdíj`, `Hálózati kapcsolat díja`, valamint a `Terminál használati díj` díjtételeket.

Egy terminál esetén a Fizetési Pont esetében: (0 Ft + 0 Ft + 2350 Ft) * 1 db terminál = 2350 Ft, **bruttó 2985 Ft**.

Érdemes még a fix havi költségeket elosztani a havi tranzakciószámmal, és az így kapott összeget is hozzáadni a tranzakció tényleges díjához, hogy megkapjuk, pontosan mennyibe került. Például 100 darab kártyás vásárlás esetén **a valódi költség 29,85 Ft-tal nő, azaz 1514 Ft lesz, amivel 57.756 Ft marad**.

### Hogyan jelenik meg a számlatörténetben a POS-terminál díja?

A bankkártyás fizetési szolgáltató (a Fizetési Pont esetében legalábbis) egy terheléssel egyösszegben inkasszózza a számlánkról a díjait a következő hónapban. (Partner neve = `Fizetési Pont Kft.`)

A Fizetési Pont számlája:
| Szám | Megnevezés | Menny. | ME | Egységár | Nettó összeg | Eng. % | Áfa % | Áfa összeg | Bruttó összeg |
| :--- | :--- | :---: | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| HDD | Internet alapú hálózati kapcsolati díj | 1 | Terminál | 0 | 0 | 0 | 27 | 0 | 0 |
| TERMINA1 | Terminál használati díj | 1 | Terminál | 2 350 | 2 350 | 0 | 27 | 635 | 2 985 |
| FIXTRX | Tranzakciós díj | 1 | Darab | 7 | 7 | 0 | 27 | 2 | 9 |
| FORGALO | Forgalomarányos változó díj | 59 270 | Darab | 0,004 | 237 | 0 | 27 | 64 | 301 |
| | | | **FT összesen** | | **2 594** | | | **701** | **3 295** |

### Kötbérfizetési kötelezettség

A bankkártyás fizetési szolgáltatóknál érdemes odafigyelni, hogy a szerződésben (vagy ÁSZF-es szerződés esetén az ÁSZF-ben) milyen kötbérfizetési kötelezettségekkel terhelhetnek. Néhány potenciális eset:
1. A POS-terminál nincs bekapcsolva nyitva tartási időben.
2. A POS-terminál technikai hibáját (pl. akkumulátor-hiba, lefagyás, kártyaolvasó hibája, internet-kapcsolati és hálózati hibák, stb.) a bérlő nem jelzi a bérbeadó részére a szerződésben meghatározott határidőn belül.
3. A POS-terminál kellékanyag hiánya (pl. hőpapír) miatt nem üzemel.
4. A vásárlók által fizetett bankkártyás tranzakciók összege nem éri el a szerződésben meghatározott minimum napi/havi forgalmi követelményt.
5. Jogosulatlan használat: Ha a bérlő nem a szerződésben rögzített tevékenységi körnek megfelelően fogad el kártyás fizetést, vagy tiltott termék/szolgáltatás értékesítésére használja a terminált.
6. Adatbiztonsági (PCI DSS) bírságok és kötbérek: Ha a bérlő megsérti a kártyatársasági adatbiztonsági előírásokat (pl. kártyaadatokat szivárogtat ki, eltárolja a CVV-kódot), a szolgáltató az őt ért kártyatársasági (Visa/Mastercard) bírságokat továbbhárítja kötbér formájában.
7. Túlzott visszaterhelési büntetődíj: Ha a bérlő a visszaterhelések (chargeback) aránya meghaladja a kártyatársaságok által engedélyezett határértéket, külön büntetődíj vagy tranzakciónkénti extra kötbér kerül felszámításra.
8. Hűségidő előtti, vagy nem a szerződés szerint meghatározott felmondási időablakban történő felmondás kötbére: Amennyiben a bérlő határozott idejű szerződést kötött (pl. 24–36 hónap) és azt idő előtt felmondja, a szolgáltató jogosult a hátralévő időszakra eső terminálbérleti díjakat vagy egyösszegű kötbért érvényesíteni.
9. POS-terminál sérülése, elvesztése vagy késedelmes visszaszolgáltatása: A fizikai terminál megrongálódása, elhagyása, vagy a szerződés megszűnése utáni át nem adása esetén a szolgáltató pótlási díjat vagy napialapú késedelmi kötbért számít fel.
10. A bankkártyás fizetés elfogadásának megtagadása a bérlő által egyoldalúan, önhatalmúlag meghatározott és szerződésbe nem foglalt minimum összeg alatt vagy maximum összeg felett. A szerződések többnyire explicit kijelentik, hogy bármilyen összegű tranzakciót köteles elfogadni a bérlő bankkártyás fizetéssel, akkor is, ha a fix díjak miatt többszörösen veszteséges is a tranzakció a bérlőnek (pl. 5 forintos vásárlás 7 Ft-os fix tranzakciós díj mellett).

### A Fizetési Pont botránya

A 8. pont különös figyelmet igényel a szerződés aláírása előtt.

Rengeteg ügyfél kötött a Fizetési Ponttal **határozott idejű és automatikusan megújuló, 3 vagy 5 éves ciklusú szerződést** kedvezményes díjak reményében. A vita egyik központi eleme a szerződés megújulására és a felmondási időszakra vonatkozó feltételek értelmezése volt. Sokan nem voltak tisztában azzal, hogy a szerződés hűségideje automatikusan megújul, és annak felmondására a ciklusfordulót megelőző 9-6. hónap közti időszakban van lehetőség megállító nyilatkozat benyújtásával. A nem megfelelő felmondás miatt a Fizetési Pont akár évekkel később több millió forint mértékű kötbér kifizetésére szólította fel az ügyfeleit, akik abban a tudatban voltak, hogy a szerződésük megszűnt.

Forrás: Telex - Krász Zsombor 2025. október 8-án közzétett cikke, ["Évekkel ezelőtt felmondták a szerződést a kártyás terminálra, most több millió forintos kötbérrel kopogtat a szolgáltató"](https://telex.hu/gazdasag/2025/10/08/fizetesi-pont-orszagos-fizetesi-szolgaltato-pos-terminal-millio-forintos-kotber-tartozas-feith-zoltan)