---
title: Expressie functies in de functie gegevens stroom toewijzen van Azure Data Factory
description: Meer informatie over expressie functies in het toewijzen van gegevens stroom.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 72c516f0a6e377cc16205917967482a29b4fdfbd
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "69036223"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Gegevens transformatie expressies in gegevens stroom toewijzen 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Expressie functies

In Data Factory gebruikt u de expressie taal van de functie gegevens stroom toewijzen om gegevens transformaties te configureren.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Positieve modulus van een paar getallen.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een inverse cosinus waarde berekend* ``acos(1) -> 0.0``
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Voegt een paar teken reeksen of getallen toe. Voegt een datum toe aan een aantal dagen. Voegt een matrix van hetzelfde type toe aan een andere. Hetzelfde als de operator +* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Voeg dagen toe aan een datum of tijds tempel. Hetzelfde als de operator + voor datum* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Maanden toevoegen aan een datum of tijds tempel* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logische AND-operator. Hetzelfde als & &* ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een inverse sinus waarde berekend* ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een inverse raaklijn waarde berekend* ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Retourneert de hoek in radialen tussen de positieve x-as van een vlak en het punt dat door de coördinaten wordt gegeven* ``atan2(0, 0) -> 0.0``
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Hiermee wordt het gemiddelde van de waarden van een kolom opgehaald* ``avg(sales) -> 7523420.234``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Op basis van een criterium wordt het gemiddelde van de waarden van een kolom opgehaald* ``avgIf(region == 'West', sales) -> 7523420.234``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Selecteert een kolom waarde op naam in de stroom. Als er meerdere overeenkomsten zijn, wordt de eerste overeenkomst geretourneerd. Als er geen overeenkomst wordt gevonden, wordt een NULL-waarde geretourneerd. De geretourneerde waarde moet van het type worden geconverteerd door een van de Type Conversion Functions (TO_DATE, TO_STRING...).  Kolom namen die bekend zijn tijdens de ontwerp fase moeten worden geadresseerd op basis van de naam. Berekende invoer waarden worden niet ondersteund, maar u kunt parameter vervangingen gebruiken* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Selecteert een kolom waarde op basis van de relatieve positie (1 gebaseerd) in de stroom. Als de positie buiten het bereik valt, wordt een NULL-waarde geretourneerd. De geretourneerde waarde moet van het type worden geconverteerd door een van de Type Conversion Functions (TO_DATE, TO_STRING...) Berekende invoer waarden worden niet ondersteund, maar u kunt parameter vervangingen gebruiken* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Op basis van afwisselende voor waarden geldt een waarde of de andere. Als het aantal invoer waarden even is, is de andere NULL voor de laatste voor waarde* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
De derdemachts wortel van een getal berekenen* ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Retourneert het kleinste gehele getal dat niet kleiner is dan het getal* ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Retourneert de eerste not null-waarde uit een set invoer waarden. Alle invoer waarden moeten van hetzelfde type zijn* ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Vergelijkt twee waarden van hetzelfde type. Retourneert een negatief geheel getal als waarde1 < Value2, 0 als waarde1 = = waarde2, positieve waarde als waarde1 > Value2* ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Voegt een variabele aantal teken reeksen samen. Hetzelfde als de operator + met teken reeksen* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Voegt een variabele aantal teken reeksen samen met een scheidings teken. De eerste para meter is het scheidings teken* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een cosinus waarde berekend* ``cos(10) -> -0.83907152907``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een hyperbolische cosinus van een waarde berekend* ``cosh(0) -> 1.0``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Hiermee wordt het totale aantal waarden opgehaald. Als de optionele kolom (men) is opgegeven, worden de NULL-waarden in het aantal genegeerd* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Hiermee wordt het totale aantal afzonderlijke waarden van een set kolommen opgehaald* ``countDistinct(custId, custName) -> 60``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Op basis van een criterium wordt het totale aantal waarden opgehaald. Als de optionele kolom is opgegeven, worden de NULL-waarden in het aantal genegeerd* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de afwijking van de populatie tussen twee kolommen opgehaald* ``covariancePopulation(sales, profit) -> 122.12``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Op basis van een criterium, wordt de afwijking van de populatie van twee kolommen opgehaald* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de voorbeeld covariantie van twee kolommen opgehaald* ``covarianceSample(sales, profit) -> 122.12``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Op basis van een criterium, wordt de voorbeeld covariantie van twee kolommen opgehaald* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Berekent de CRC32-hash van een set kolom van verschillende primitieve gegevens typen met een bitlengte die alleen uit waarden 0 (256), 224, 256, 384, 512 kan zijn. Het kan worden gebruikt voor het berekenen van een vinger afdruk voor een rij* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
De functie CumeDist berekent de positie van een waarde ten opzichte van alle waarden in de partitie. Het resultaat is het aantal rijen dat voorafgaat aan of gelijk is aan de huidige rij in de volg orde van de partitie gedeeld door het totaal aantal rijen in de venster partitie. Alle gelijke waarden in de volg orde worden geëvalueerd op dezelfde positie.
* ``cumeDist() -> 1``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Hiermee wordt de huidige datum opgehaald waarop deze taak wordt uitgevoerd. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Hiermee wordt het huidige tijds tempel opgehaald wanneer de taak wordt uitgevoerd met de lokale tijd zone* ``currentTimestamp() -> 12-12-2030T12:12:12``
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Hiermee wordt de huidige tijds tempel opgehaald als UTC. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. Deze wordt standaard ingesteld op de huidige tijd zone* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hiermee wordt de dag van de maand opgehaald op basis van een datum* ``dayOfMonth(toDate('2018-06-08')) -> 08``
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hiermee wordt de dag van de week opgehaald op basis van een datum. 1: zondag, 2-maandag..., 7-zaterdag* ``dayOfWeek(toDate('2018-06-08')) -> 7``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hiermee wordt de dag van het jaar opgehaald op basis van een datum* ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Converteert radialen naar graden* ``degrees(3.141592653589793) -> 180``
___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Hiermee wordt de positie van een waarde in een groep waarden berekend. Het resultaat is één plus het aantal rijen dat voorafgaat aan of gelijk is aan de huidige rij in de volg orde van de partitie. De waarden veroorzaken geen hiaten in de reeks. Een dichte rang werkt zelfs wanneer er geen gegevens worden gesorteerd en er wordt gekeken naar een wijziging in waarden* ``denseRank(salesQtr, salesAmt) -> 1``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Deelt een combi natie van getallen. Hetzelfde als de operator/* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Controleert of de teken reeks eindigt met de opgegeven teken reeks* ``endsWith('great', 'eat') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator voor vergelijking gelijk aan. Hetzelfde als = = operator* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
De vergelijkings operator is gelijk aan het hoofdletter gebruik negeren. Hetzelfde als < = >-operator* ``'abc'<==>'abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
De faculteit van een getal berekenen* ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Retourneert altijd een waarde ONWAAR. Gebruik de functie syntaxis (false ()) als er een kolom is met de naam ' false '* ``isDiscounted == false()``
* ``isDiscounted() == false``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Hiermee wordt de eerste waarde van een kolom groep opgehaald. Als de tweede para meter ignoreNulls wordt wegge laten, wordt aangenomen dat deze ONWAAR is* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Retourneert het grootste gehele getal dat niet groter is dan het aantal* ``floor(-0.1) -> -1``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Wordt geconverteerd naar de tijds tempel van UTC. U kunt eventueel de tijd zone in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden ' door geven. Deze wordt standaard ingesteld op de huidige tijd zone* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Hogere operator voor vergelijking. Hetzelfde als >-operator* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
De vergelijking is groter dan of gelijk aan de operator. Hetzelfde als > = operator* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Retourneert de grootste waarde uit de lijst met waarden als invoer. Retourneert null als alle invoer waarden null zijn* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Hiermee wordt de uur waarde van een tijds tempel opgehaald. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Op basis van een voor waarde geldt één waarde of de andere. Als de andere waarde niet wordt opgegeven, wordt deze als NULL beschouwd. Beide waarden moeten compatibel zijn (numeriek, teken reeks...)* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Hiermee wordt gecontroleerd of een item zich in de matrix bevindt* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Converteert de eerste letter van elk woord naar hoofd letters. Woorden worden geïdentificeerd als gescheiden door witruimte* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Hiermee wordt de positie (1 op basis van de subtekenreeks in een teken reeks) gezocht. 0 wordt geretourneerd als het niet is gevonden* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Hiermee wordt gecontroleerd of de rij is gemarkeerd voor verwijderen. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1* ``isDelete() -> true``
* ``isDelete(1) -> false``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Hiermee wordt gecontroleerd of de rij als fout is gemarkeerd. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1* ``isError() -> true``
* ``isError(1) -> false``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Hiermee wordt gecontroleerd of de rij is gemarkeerd om te worden genegeerd. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1* ``isIgnore() -> true``
* ``isIgnore(1) -> false``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Hiermee wordt gecontroleerd of de rij is gemarkeerd voor invoegen. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1* ``isInsert() -> true``
* ``isInsert(1) -> false``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Hiermee wordt gecontroleerd of de rij overeenkomt bij het opzoeken. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1* ``isMatch() -> true``
* ``isMatch(1) -> false``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Controleert of de waarde NULL is* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Hiermee wordt gecontroleerd of de rij is gemarkeerd voor bijwerken. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1* ``isUpdate() -> true``
* ``isUpdate(1) -> false``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de kurtosis van een kolom opgehaald* ``kurtosis(sales) -> 122.12``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de kurtosis van een kolom opgehaald op basis van een criterium* ``kurtosisIf(region == 'West', sales) -> 122.12``
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Hiermee wordt de waarde opgehaald van de eerste para meter die n rijen voor de huidige rij is geëvalueerd. De tweede para meter is het aantal rijen dat u wilt terugkijken en de standaard waarde is 1. Als er niet zoveel rijen zijn, wordt een waarde van Null geretourneerd, tenzij een standaard waarde is opgegeven* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Hiermee wordt de laatste waarde van een kolom groep opgehaald. Als de tweede para meter ignoreNulls wordt wegge laten, wordt aangenomen dat deze ONWAAR is* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Hiermee wordt de laatste datum van de maand opgehaald op basis van een datum* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Hiermee wordt de waarde opgehaald van de eerste para meter, geëvalueerd n rijen na de huidige rij. De tweede para meter is het aantal rijen dat u wilt zoeken en de standaard waarde is 1. Als er niet zoveel rijen zijn, wordt een waarde van Null geretourneerd, tenzij een standaard waarde is opgegeven* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Vergelijkings operator kleiner dan of gelijk aan. Hetzelfde als < = operator* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Hiermee wordt een subtekenreeks geëxtraheerd die begint bij index 1 met het aantal tekens. Hetzelfde als subtekenreeks (str, 1, n)* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Retourneert de lengte van de teken reeks* ``length('kiddo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator voor vergelijkings minus. Hetzelfde als <-operator* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Vergelijkings operator kleiner dan of gelijk aan. Hetzelfde als < = operator* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Hiermee wordt de Levenshtein-afstand tussen twee teken reeksen opgehaald* ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Het patroon is een teken reeks die letterlijk overeenkomt. De uitzonde ringen zijn de volgende speciale symbolen: _ overeenkomen met een wille keurig teken in de invoer (vergelijkbaar met. in POSIX-reguliere expressies)% komt overeen met nul of meer tekens in de invoer (vergelijkbaar met. * in reguliere expressies van POSIX).
Het escape-teken is ' '. Als een escape-teken voorafgaat aan een speciaal symbool of een ander escape teken, wordt het volgende teken letterlijk vergeleken. Het is niet toegestaan om een ander teken te escapepen.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Hiermee wordt de positie (1 op basis van de subtekenreeks) binnen een teken reeks met een bepaalde positie gevonden. Als de positie wordt wegge laten, wordt deze in overweging genomen vanaf het begin van de teken reeks. 0 wordt geretourneerd als het niet is gevonden* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
De logboek waarde wordt berekend. Een optionele basis kan worden opgegeven als een Euler-nummer bij gebruik* ``log(100, 10) -> 2``
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de logboek waarde berekend op basis van 10 basis* ``log10(100) -> 2``
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Kleine letters een teken reeks* ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Links wordt de teken reeks met de opgegeven opvulling gematteerd tot deze een bepaalde lengte heeft. Als de teken reeks gelijk is aan of groter is dan de lengte, wordt deze beschouwd als een No * ``lpad('great', 10, '-') -> '___--great'`` -op 
* ``lpad('great', 4, '-') -> 'great'`` 
* ' ' lpad (' Great ', 8, ' < > ')-> '<great'``
___
### <code>< > LTrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Hiermee wordt een teken reeks met voorloop tekens bijgesneden. Als de tweede para meter niet is opgegeven, wordt witruimte verkleind. Anders wordt het teken dat in de tweede para meter is opgegeven, verkleind* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Hiermee wordt de maximum waarde van een kolom opgehaald* ``MAX(sales) -> 12312131.12``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Hiermee wordt de maximum waarde van een kolom opgehaald op basis van een criterium* ``maxIf(region == 'West', sales) -> 99999.56``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Hiermee wordt de MD5-Digest van een set kolom van verschillende primitieve gegevens typen berekend en wordt een hexadecimale teken reeks van 32 tekens geretourneerd. Het kan worden gebruikt voor het berekenen van een vinger afdruk voor een rij* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Hiermee wordt het gemiddelde van de waarden van een kolom opgehaald. Hetzelfde als Gem* ``mean(sales) -> 7523420.234``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Op basis van een criterium wordt het gemiddelde van de waarden van een kolom opgehaald. Hetzelfde als avgIf* ``meanIf(region == 'West', sales) -> 7523420.234``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Hiermee wordt de milliseconde-waarde van een datum opgehaald. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt.
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Hiermee wordt de minimum waarde van een kolom opgehaald* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Hiermee wordt de minimum waarde van een kolom opgehaald op basis van een criterium* ``minIf(region == 'West', sales) -> 00.01``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Trekt getallen af. Aftrekken van het datum aantal dagen. Hetzelfde als de-operator* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Hiermee wordt de minuut waarde van een tijds tempel opgehaald. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Modulus van een combi natie van getallen. Hetzelfde als de operator%* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hiermee wordt de maand waarde van een datum of tijds tempel opgehaald* ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Hiermee wordt het aantal maanden tussen twee datesYou opgehaald, waarmee een optionele tijd zone kan worden door gegeven in de vorm van ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Vermenigvuldigt het paar getallen. Hetzelfde als de operator ** ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
De functie NTile verdeelt de rijen voor elke venster partitie `n` in buckets, variërend van 1 tot de `n`meeste. Bucket waarden kunnen Maxi maal 1 zijn. Als het aantal rijen in de partitie niet gelijkmatig is verdeeld over het aantal buckets, worden de waarden van de rest gedistribueerd per Bucket, beginnend met de eerste Bucket. De functie NTile is handig voor het berekenen van tertiles, kwartielen, deciles en andere algemene samenvattings statistieken. De functie berekent twee variabelen tijdens de initialisatie: Er wordt aan de grootte van een normale Bucket een extra rij toegevoegd. Beide variabelen zijn gebaseerd op de grootte van de huidige partitie. Tijdens het berekenings proces houdt de functie het huidige rijnummer, het huidige Bucket nummer en het rijnummer waarbij de Bucket wordt gewijzigd (bucketThreshold) bij. Wanneer het huidige rijnummer de Bucket drempel bereikt, wordt de Bucket waarde verhoogd met één en wordt de drempel verhoogd met de Bucket grootte (plus één extra als de huidige Bucket wordt opgevuld).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Een getal wordt genegeerd. Hiermee worden positieve getallen negatief en omgekeerd* ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Retourneert de volgende unieke sequentie. Het getal is opeenvolgend alleen binnen een partitie en wordt voorafgegaan door de partitionId* ``nextSequence() -> 12313112``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
De teken reeks waarde normaliseren om Unicode-tekens met accenten te scheiden* ``normalize('boys') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logische negatie-operator* ``not(true) -> false``
* ``not(premium)``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
De vergelijkings operator is niet gelijk aan. Hetzelfde als! =-operator* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Retourneert een NULL-waarde. Gebruik de functie syntaxis (Null ()) als er een kolom is met de naam null. Elke bewerking die gebruikmaakt, resulteert in een NULL-waarde* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logische OR-operator. Hetzelfde als | |* ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Positieve modulus van een paar getallen.
* ``pmod(-20, 8) -> 4``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt één getal verhoogd naar de macht van een andere.* ``power(10, 2) -> 100``
___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Hiermee wordt de positie van een waarde in een groep waarden berekend. Het resultaat is één plus het aantal rijen dat voorafgaat aan of gelijk is aan de huidige rij in de volg orde van de partitie. De waarden veroorzaken hiaten in de reeks. Rang werkt zelfs wanneer er geen gegevens worden gesorteerd en er wordt gekeken naar wijziging in waarden* ``rank(salesQtr, salesAmt) -> 1``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Een overeenkomende subtekenreeks ophalen voor een opgegeven regex-patroon. De laatste para meter identificeert de overeenkomende groep en wordt standaard ingesteld op 1 als dit wordt wegge laten. Gebruik '<regex>' (back-quote) om een teken reeks zonder aanhalings tekens te zoeken* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Controleert of de teken reeks overeenkomt met het opgegeven regex-patroon. Gebruik '<regex>' (back-quote) om een teken reeks zonder aanhalings tekens te zoeken* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Alle exemplaren van een regex-patroon vervangen door een andere subtekenreeks in de opgegeven teken reeks<regex>gebruik ' ' (back quote) om een teken reeks zonder aanhalings tekens te zoeken.* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Splitst een teken reeks op basis van een scheidings teken op basis van een reguliere expressie en retourneert een matrix met teken reeksen* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Alle exemplaren van een subtekenreeks vervangen door een andere subtekenreeks in de opgegeven teken reeks* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Hiermee wordt een teken reeks omgekeerd* ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Hiermee wordt een subtekenreeks geëxtraheerd met het aantal tekens vanaf de rechter kant. Gelijk aan subtekenreeks (str, LENGTH (str)-n, n)* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Controleert of de teken reeks overeenkomt met het opgegeven regex-patroon* ``rlike('200.50', '(\d+).(\d+)') -> true``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Rondt een getal af op basis van een optionele schaal en een optionele Afrondings modus. Als de schaal wordt wegge laten, wordt deze standaard ingesteld op 0.  Als de modus wordt wegge laten, wordt deze standaard ingesteld op ROUND_HALF_UP (5). De waarden voor afronding bevatten 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Hiermee wijst u een sequentiële rijnummer nummering toe voor rijen in een venster, te beginnen met 1* ``rowNumber() -> 1``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Met rechts wordt de teken reeks met de opgegeven opvulling gematteerd tot deze een bepaalde lengte heeft. Als de teken reeks gelijk is aan of groter is dan de lengte, wordt deze beschouwd als een niet * ``rpad('great', 10, '-') -> 'great___--'``-op RTrim 
* ``rpad('great', 4, '-') -> 'great'`` 
* ``rpad('great', 8, '<>') -> 'great<><'`` 
___
### <code>rtrim</code></code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Rechts knipt een teken reeks van voorloop tekens. Als de tweede para meter niet is opgegeven, wordt witruimte verkleind. Anders wordt het teken dat in de tweede para meter is opgegeven, verkleind* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Hiermee wordt de tweede waarde van een datum opgehaald. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Hiermee wordt het SHA-1-overzicht van een set kolom van verschillende primitieve gegevens typen berekend en wordt een hexadecimale teken reeks van 40 tekens geretourneerd. Het kan worden gebruikt voor het berekenen van een vinger afdruk voor een rij* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Hiermee wordt het SHA-2-overzicht van een set kolom van verschillende primitieve gegevens typen berekend met een bitlengte die alleen uit waarden 0 (256), 224, 256, 384, 512 kan zijn. Het kan worden gebruikt voor het berekenen van een vinger afdruk voor een rij* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een sinus waarde berekend* ``sin(2) -> 0.90929742682``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een hyperbolische sinus waarde berekend* ``sinh(0) -> 0.0``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de scheefheid van een kolom opgehaald* ``skewness(sales) -> 122.12``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Op basis van een criterium, haalt de scheefheid van een kolom op* ``skewnessIf(region == 'West', sales) -> 122.12``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Hiermee wordt een subset van een matrix geëxtraheerd uit een positie. De positie is op basis van 1. Als de lengte wordt wegge laten, wordt deze standaard ingesteld op einde van de teken reeks* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Hiermee wordt de Soundex-code voor de teken reeks opgehaald* ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Splitst een teken reeks op basis van een scheidings teken en retourneert een matrix met teken reeksen* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de vierkantswortel van een getal berekend* ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Hiermee wordt gecontroleerd of de teken reeks begint met de opgegeven teken reeks* ``startsWith('great', 'gr') -> true``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de standaard deviatie van een kolom opgehaald* ``stdDev(sales) -> 122.12``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Op basis van een criterium, wordt de standaard deviatie van een kolom opgehaald* ``stddevIf(region == 'West', sales) -> 122.12``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de standaard deviatie van de populatie van een kolom opgehaald* ``stddevPopulation(sales) -> 122.12``
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Op basis van een criterium, wordt de standaard deviatie van de populatie van een kolom opgehaald* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de voor beeld-standaard deviatie van een kolom opgehaald* ``stddevSample(sales) -> 122.12``
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Op basis van een criterium, wordt de standaard afwijking van een kolom opgehaald* ``stddevSampleIf(region == 'West', sales) -> 122.12``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Maanden van een datum aftrekken. Hetzelfde als de-operator voor datum* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Maanden aftrekken van een datum of tijds tempel* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Hiermee wordt een subtekenreeks van een bepaalde lengte opgehaald uit een positie. De positie is op basis van 1. Als de lengte wordt wegge laten, wordt deze standaard ingesteld op einde van de teken reeks* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Hiermee wordt de cumulatieve som van een numerieke kolom opgehaald* ``sum(col) -> value``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Hiermee wordt de cumulatieve som opgehaald van afzonderlijke waarden van een numerieke kolom* ``sumDistinct(col) -> value``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Op basis van criteria haalt de cumulatieve som van een numerieke kolom op. De voor waarde kan worden gebaseerd op elke kolom* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Op basis van criteria haalt de cumulatieve som van een numerieke kolom op. De voor waarde kan worden gebaseerd op elke kolom* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een raaklijn waarde berekend* ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een hyperbolische tangens waarde berekend* ``tanh(0) -> 0.0``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Converteert een waarde van ('t ', ' True ', ' y ', ' Yes ', ' 1 ') naar waar en (' f ', ' onwaar ', ' n ', ' nee ', ' 0 ') naar onwaar en NULL voor een andere waarde* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Converteert een teken reeks naar een datum op basis van een optionele datum notatie. Raadpleeg Java SimpleDateFormat voor alle mogelijke indelingen. Als de datum notatie wordt wegge laten, worden combi Naties van de volgende gegevens geaccepteerd. [jjjj, yyyy-[M] M, yyyy-[M] M-[d] d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *]* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Converteert een wille keurig getal of teken reeks naar een decimale waarde. Als er geen precisie en schaal is opgegeven, wordt het standaard ingesteld op (10, 2). Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Een optionele indeling voor land instellingen in de vorm van BCP47 taal zoals en-US, de, zh-CN* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Converteert een wille keurig getal of teken reeks naar een dubbele waarde. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Een optionele indeling voor land instellingen in de vorm van BCP47 taal zoals en-US, de, zh-CN* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Converteert een wille keurig getal of teken reeks naar een float-waarde. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Verkapt een dubbel* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Converteert een wille keurige numerieke waarde of teken reeks naar een geheel getal. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Kapte Long, float, double* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Converteert een wille keurig getal of teken reeks naar een lange waarde. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Verkapt wille keurig zweven, dubbel* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Converteert een wille keurig getal of teken reeks naar een korte waarde. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Kapt geheel getal, lang, zweven, dubbel* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Hiermee wordt een primitieve gegevens type geconverteerd naar een teken reeks. Voor getallen en datum kan een notatie worden opgegeven. Als u niet opgeeft, wordt de standaard waarde van het systeem gekozen. De decimale notatie voor de Java wordt gebruikt voor getallen. Raadpleeg Java SimpleDateFormat voor alle mogelijke datum notaties. de standaard indeling is JJJJ-MM-DD* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converteert een teken reeks naar een datum op basis van een optionele time stamp-notatie. Raadpleeg Java SimpleDateFormat voor alle mogelijke indelingen. Als de tijds tempel wordt wegge laten, wordt het standaard patroon jjjj-[M] M-[d] d uu: mm: SS [. f...] gebruikt* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Hiermee wordt de tijds tempel geconverteerd naar UTC. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. Deze wordt standaard ingesteld op de huidige tijd zone* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Een reeks tekens vervangen door een andere set tekens in de teken reeks. Tekens hebben 1 tot 1 vervanging* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Hiermee wordt een teken reeks van voor loop-en volg tekens verkleind. Als de tweede para meter niet is opgegeven, wordt witruimte verkleind. Anders wordt het teken dat in de tweede para meter is opgegeven, verkleind* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Retourneert altijd een waarde waar. Gebruik de functie syntaxis (True ()) als er een kolom is met de naam True* ``isDiscounted == true()``
* ``isDiscounted() == true``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Komt overeen met het type van de kolom. Kan alleen worden gebruikt in patroon expressies. getal komt overeen met kort, geheel getal, lang, dubbel, zwevend of decimaal teken, integraal komt overeen met short, integer, Long, breuken die overeenkomen met de datum of het tijds tempel type* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Een teken reeks in hoofd letters* ``upper('bojjus') -> 'BOJJUS'``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de variantie van een kolom opgehaald* ``variance(sales) -> 122.12``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de variantie van een kolom opgehaald op basis van een criterium* ``varianceIf(region == 'West', sales) -> 122.12``
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de populatie variantie van een kolom opgehaald* ``variancePopulation(sales) -> 122.12``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Op basis van een criterium, wordt de populatie variantie van een kolom opgehaald* ``variancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt de onzuivere variantie van een kolom opgehaald* ``varianceSample(sales) -> 122.12``
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Op basis van een criterium, wordt de onzuivere variantie van een kolom opgehaald* ``varianceSampleIf(region == 'West', sales) -> 122.12``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hiermee wordt de week van het jaar opgehaald op basis van een datum* ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logische XOR-operator. Zelfde als ^-operator* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hiermee wordt de jaar waarde van een datum opgehaald* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het gebruik van Expression Builder](concepts-data-flow-expression-builder.md).
