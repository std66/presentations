# Információk
- **Szerző**: Sinku Tamás (sinkutamas@gmail.com)
- **Licenc**: Creative Commons BY-NC-SA
- **Utoljára módosítva**: 2026. szeptember 12.

# Mennyibe kerül a bankkártyás fizetés elfogadása a kereskedőnek?

Ebben a dokumentumban a **Fizetési Pont** és az **OFSZ** példáján keresztül szeretném elmagyarázni, hogy miképp számolható ki az, hogy egy bankkártyás fizetés után ténylegesen mennyi pénz marad a kereskedő zsebében.

**Fontos:**
1. Laikus vagyok, nem pedig pénzügyi szakértő. Ez a leírás nem minősül semmiféle pénzügyi tanácsadásnak.
2. A leírás akkor lesz érthető, ha elejétől a végéig elolvasod.

## Kik vesznek részt a bankkártyás fizetési folyamatban?

Neked, mint kereskedőnek
1. van egy valamelyik **bank** által (a példánkban az OFSZ) vezetett vállalkozói bankszámlája.
2. Emellett valamelyik **bankkártyás fizetési szolgáltató** (a példánkban a Fizetési Pont) bérbe ad egy bankkártya-terminált (POS-terminálnak is szokás hívni, Point of Sale).

A vásárlódnak
1. úgyszintén van egy bankszámlája, amelyet valamelyik **bank** vezet,
2. és van egy valamelyik **kártyakibocsátó vállalat** által, a bankon keresztül kibocsátott **bankkártyája**, amellyel a bankszámlájáról költheti a pénzt.

## Mire keressük a választ?

Neked, mint kereskedőnek, van egy számítástechnikai üzleted. Bemegy hozzád a vásárló, mert mondjuk akciós nálad az Intel Arc A310-es videókártya, és ezt meg akarja venni. A videókártya árát, amely 59.270 Ft, a bankkártyájával fizeti ki neked.

**A fő kérdések:**
1. Mennyi pénz marad meg a vásárló által kifizetett pénzből, miután a **a te bankod** és a **bankkártyás fizetési szolgáltató** levonta a díjait?
2. Hogyan lehet ezt kiszámolni?

## A beszerzendő dokumentumok

A pénzügyi szektor többnyire két elnevezést szeret használni azon dokumentumainak megnevezésére, amely a díjakat határozzák meg:
- hirdetmény
- kondíciók / kondíciós lista

Mivel kereskedőként két jogi személlyel állsz kapcsolatban (a bankkal és a bankkártyás fizetési szolgáltatóval), el kell menni mindkettő weboldalára és meg kell keresni ezeket a hirdetményeket.

Ezeket a dokumentumokat itt találod a példánkhoz (a hivatkozások dokumentum írásának pillanatában érvényesek):

**OFSZ:** [Tájékoztatók és dokumentumok](https://ofsz.hu/hu/tajekoztatok/hatalyos-dokumentumok) - OFSZ-Elfogadói Hirdetmény

**Fizetési Pont:** [Letölthető dokumentumok](https://www.fizetesipont.hu/letoltheto-dokumentumok) - Hirdetmény

## Mit fizetsz ki kereskedőként?

Nincs ingyen az, hogy a vásárló pénze eljut az ő bankszámlájáról a te bankszámládra a vásárlód kártyakibocsátójának és a te POS terminálodnak közreműködésével. Ezért fizetni fogsz
1. a **vásárló kártyakibocsátójának** (VISA, MasterCard, Maestro, AmEx, ...), aki eljuttatja a pénzt a két bank között,
2. valamint a **bankkártyás fizetési szolgáltatónak**, hogy lekommunikálja a tranzakciót a kártyakibocsátóval.

Ideális esetben a vásárlód bankszámlájának devizaneme és a te vállalkozói bankszámlád devizaneme megegyezik. Magyarul: te forintban kéred a pénzt, a vásárlód pedig forinttal fizet.

Ha nem ez a helyzet, és a vásárlód bankszámlája kínai yüanban van vezetve, a yüanban nyilvántartott devizát az éppen aktuális árfolyamon kettőtök közül valakinek át kell váltania. Erről a vásárló dönt a fizetés pillanatában - felkínálja neki a terminál, hogy forintban fizet és az ő bankja vált, vagy yüanban fizet és a te bankod vált. Ezt hívják DCC-nek, azaz Dynamic Currency Conversion-nek (dinamikus pénznem átváltás).

*Apróbetűs részként annyit megemlítenék, hogy ne felejtsük el, hogy van egy kártyakibocsátó is a képben, akin a fizetés keresztül megy, és ő is valamilyen devizában (többnyire USD-ben vagy EUR-ban) tartja nyilván a tranzakcióit, ha devizát kell váltani. Ez azt jelenti, hogy az átváltás yüanról forintra nem közvetlenül történik meg, hanem köztes lépésként mondjuk euróra váltódik át (tehát: Yüan -> Euró/USD -> Forint).*

**Fontos:** A továbbiakban kizárólag azt az esetet taglaljuk, ahol **nincs DCC** a fizetés során.

## A vásárló kártyakibocsátójának és a bankodnak fizetett díj

A vásárlási folyamat elkezdődik azzal, hogy
1. a POS terminálba bepötyögöd, hogy a vásárlód a videókártyáért 59.270 Ft-ot fog fizetni.
2. A vásárló bedugja/lehúzza/lecsipogja a bankkártyáját, amelyből a POS terminál a kártya száma alapján meghatározza, hogy melyik kártyakibocsátóval kell kommunikálnia. Hogyan történik ez?

**A BIN szám fontossága**: A BIN szám (Bank Identification Number - banki azonosítószám) a bankkártya számának első 6-8 számjegye. Ez azonosítja a kártyakibocsátót, a bankkártya típusát (lakossági vagy üzleti, betéti- vagy hitelkártya), valamint a vásárló számlavezető bankját. Próbáld ki a saját bankkártyáddal ezen az oldalon: https://spend.net/hu/bin-checker

A bankod (OFSZ) hirdetménye valószínűleg már alaposan megrémisztett a bonyolult táblázataival. Ebből ráadásul lehet több is, ha DCC-vel többféle devizanemet is elfogadsz. Erre azért van szükség, mert a bankkártya típusától függ, hogy milyen díjat fog felszámítani a kártyakibocsátó és a bank:

| O.F.SZ. Zrt által kibocsátott kártyák | Kereskedői díj | Bankközi jutalék | Kártyatársági díj |
| :--- | :---: | :---: | :---: |
| MasterCard lakossági betéti kártya | 1,2% | - | 18 Ft + 0,55% |
| MasterCard üzleti betéti kártya | 1,2% | - | 18 Ft + 0,55% |
| VISA lakossági betéti kártya | 1,2% | - | 18 Ft + 0,55% |
| VISA üzleti betéti kártya | 1,2% | - | 18 Ft + 0,55% |
| **Más bank által kibocsátott kártyák** | **Kereskedői díj** | **Bankközi jutalék** | **Kártyatársági díj** |
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

**Bankközi jutalékot** általában nem kell fizetni, ha a pénz bankon belül mozog, vagyis a te számlavezető bankod és a vásárlód számlavezető bankja egy és ugyanaz a bank.

A példában a vásárlónk a videókártyáját **VISA lakossági betéti kártyával** fizeti ki az **MBH Banknál** vezetett számláján (ami egy másik bank, tehát van bankközi jutalék). Ezek alapján az alábbi díjak lesznek levonva a kifizetett összegből. A százalékok mindig az eredeti kifizetett összegből számolódnak ki.
- **1,2% kereskedői díj**: 59270 Ft * 0,012 = 711,24 Ft
- **0,2% bankközi jutalék**: 59270 Ft * 0,002 = 118,54 Ft
- **18 Ft + 0,55% kártyatársasági díj**: 18 Ft + (59270 Ft * 0,0055) = 343,985 Ft

Ezeket a tételeket összeadjuk és kerekítjük: 1173,765 Ft, kerekítve 1174 Ft

### Hogyan kell értelmezni a bank által küldött bankkártya elfogadói kimutatást?

Ez a dokumentum az előző hónap bankkártyás vásárlásainak végösszegét, a vásárláshoz használt kártya típusát, és az ezek alapján számított díjakat és arányszámokat tartalmazza. Az OFSZ esetén így néz ki:

| Dátum | Kártyaszám | Tr.szám | Összeg | Jutalék | Bankközi jut. | Jut. % | Nettó | Megjegyzés |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| 2026. 07. 01. | 428312********** | *kitakarva* | 59 270 Ft | 1 174 Ft | 119 Ft | 1,95% | 58 096 Ft | VI_RET_NE_DB |

Az oszlopok jelentése:
- **Dátum**: A vásárlás napja
- **Kártyaszám**: A vásárló bankkártyájának első 6-8 számjegye (BIN), valamint az utolsó 4 számjegye (kitakartam a fenti példában)
- **Tranzakció szám**: A vásárlás egyedi azonosító kódja
- **Összeg**: Ennyit fizetett ki a vásárlód
- **Jutalék**: Ennyi jutalékot vontak le a vásárló által kifizetett összegből a bankodnak és a vásárló kártyatársaságának
- **Bankközi jutalék**: A `Jutalék` azon része, ami a két bank közti pénzmozgatásra ment el (nem fontos, csak érdekes)
- **Jutalék %**: A `Jutalék` ennyi százalékát teszi ki a vásárló által kifizetett összegnek
- **Nettó**: Ennyi marad neked (elvileg, mert amúgy nem - a bankkártyás fizetési szolgáltató díja még ebből jön le később)
- **Megjegyzés**: A fizetéshez használt kártya típusa

A megjegyzés rovat furcsa kódnak tűnhet, de megfejthető.
- **VI**: VISA
- **RET**: Retail (lakossági kártya)
- **DB**: Debit (betéti kártya)

További példák:
- **MC_RET_DB**: MasterCard lakossági betéti kártya
- **VI_BUS_CR**: VISA vállalati hitelkártya
- **MAESTRO**: Maestro kártya

### Hogyan jelenik meg a számlakivonatban?

Bankonként eltér a jóváírás időpontja. OFSZ esetében az előző napi bankkártyás fizetések jóváírása a következő banki nyitvatartási napon egyösszegben kerülnek jóváírásra két tranzakció formájában:
1. Az első jóváíró tranzakció jóváírja az előző napi bankkártyás vásárlások azon összegét, amelyet eredetileg kifizetett a vásárló. (Partner neve = `POS forgi jóváírás  kereskedő sz`)
2. A második terhelési tranzakció levonja az előző napi bankkártyás vásárlások összesített jutalékát. (Partner neve = `Kártyaelfogadói jut.terh.-nem OF`)

## A bankkártyás fizetési szolgáltatónak fizetett díjak

A bankkártyás fizetési szolgáltató kétféle díjat számít fel, mivel két feladata van. Üzemelteti és bérbe adja a POS terminált, valamint lebonyolítja a kártyakibocsátóval az egyes tranzakciókat.
1. Minden egyes tranzakciónak díja van, amely lehet fix, vagy arányos a vásárlás mértékével, vagy hibrid (fix + arányos díj összege)
2. A POS terminál üzemeltetésének és bérbeadásának (használatának), valamint infrastruktúrájának (mobilinternet-hozzáférés) díja.

### A tranzakciónkénti díj

Miután megtudtuk, hogy a vásárló által kifizetett 59.270 Ft-ból az első kör díjait kifizetve már csak 58.096 Ft maradt meg nekünk, ideje, hogy még kevesebbet tarthassunk csak meg belőle, hála a POS terminál díjainak. Hogy mennyivel?

A POS terminál díjait a bankkártyás fizetési szolgáltató hirdetménye írja le. Ez már barátibb:

| Terminálonként vagy tranzakciónként fizetendő díjak | |
| :--- | :---: |
| Alapdíj | 0,- Ft |
| Tranzakciós díj (tranzakciónként) | 7,- Ft |
| Hálózati kapcsolat díja | 0,- Ft |
| Forgalomarányos változó díj | 0,4 % |
| Terminál használati díj | 2350,- Ft |

**Nettó díjak.** A hirdetmény szerint:
>Jogszabály alapján a fenti díjak esetében áfa felszámítási kötelezettség keletkezhet.

Ez a táblázat vegyíti a kétfajta díjköltséget. Vásárlásonként a `Tranzakciós díj` és a `Forgalomarányos változó díj` lesz levonva a vásárló által kifizetett **eredeti összegből**.

**Példa:** 7 Ft + (59270 Ft * 0,004) = 244,08 Ft, amelyet kerekítünk: 244 Ft a Fizetési Pont díja nettóban. **Bruttóban 310 Ft**.

**Végül 57.786 Ft maradt a vállalkozásnak, összesen 1484 Ft-unkba került a vásárló bankkártyás fizetése.**

### A havi díj

Minden egyes POS terminál után ki kell fizetni az `Alapdíj`, `Hálózati kapcsolat díja`, valamint a `Terminál használati díj` díjtételeket.

Egy terminál esetén a Fizetési Pont esetében: (0 Ft + 0 Ft + 2350 Ft) * 1db terminál = 2350 Ft

### Hogyan jelenik meg a számlatörténetben a POS terminál díja?

A bankkártyás fizetési szolgáltató (a Fizetési Pont esetében legalábbis) egy terheléssel egyösszegben inkasszózza a számlánkról a díjait a következő hónapban. (Partner neve = `Fizetési Pont Kft.`)

A Fizetési Pont számlája:
| Szám | Megnevezés | Menny. | ME | Egységár | Nettó összeg | Eng. % | Áfa % | Áfa összeg | Bruttó összeg |
| :--- | :--- | :---: | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| HDD | Internet alapú hálózati kapcsolati díj | 1 | Terminál | 0 | 0 | 0 | 27 | 0 | 0 |
| TERMINA1 | Terminál használati díj | 1 | Terminál | 2 350 | 2 350 | 0 | 27 | 635 | 2 985 |
| FIXTRX | Tranzakciós díj | 1 | Darab | 7 | 7 | 0 | 27 | 2 | 9 |
| FORGALO | Forgalomarányos változó díj | 59 270 | Darab | 0,004 | 237 | 0 | 27 | 64 | 301 |
| | | | **FT összesen** | | **2 594** | | | **701** | **3 295** |