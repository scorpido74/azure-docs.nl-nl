---
title: Expressie functies in de functie gegevens stroom toewijzen van Azure Data Factory
description: Meer informatie over expressie functies in het toewijzen van gegevens stroom.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: c062a75516a1b865c1ff6c35f00d4fbf7c4881c6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029368"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Gegevens transformatie expressies in gegevens stroom toewijzen 



## <a name="expression-functions"></a>Expressie functies

In Data Factory gebruikt u de expressie taal van de functie gegevens stroom toewijzen om gegevens transformaties te configureren.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Absolute waarde van een getal.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiermee wordt een inverse cosinus waarde berekend * ``acos(1) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Voegt een paar teken reeksen of getallen toe. Voegt een datum toe aan een aantal dagen. Voegt een duur toe aan een tijds tempel. Voegt een matrix van hetzelfde type toe aan een andere. Hetzelfde als de +-operator * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Voeg dagen toe aan een datum of tijds tempel. Hetzelfde als de operator + voor datum * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')`` @ no__t-1 @ no__t-2<br/><br/>
Voeg maanden toe aan een datum of tijds tempel. U kunt eventueel een tijd zone door geven * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Logische AND-operator. Hetzelfde als & & * ``and(true, false) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt een inverse sinus waarde berekend * ``asin(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt een inverse raaklijn waarde berekend * ``atan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Retourneert de hoek in radialen tussen de positieve x-as van een vlak en het punt dat wordt gegeven door de coördinaten * ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt het gemiddelde opgehaald van de waarden van een kolom * ``avg(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium wordt het gemiddelde van de waarden van een kolom * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2 opgehaald<br/><br/>
Selecteert een kolom waarde op naam in de stroom. U kunt een optionele naam van een stream door geven als het tweede argument. Als er meerdere overeenkomsten zijn, wordt de eerste overeenkomst geretourneerd. Als er geen overeenkomst wordt gevonden, wordt een NULL-waarde geretourneerd. De geretourneerde waarde moet van het type worden geconverteerd door een van de Type Conversion Functions (TO_DATE, TO_STRING...).  Kolom namen die bekend zijn tijdens de ontwerp fase moeten worden geadresseerd op basis van de naam. Berekende invoer wordt niet ondersteund, maar u kunt parameter substituties * ``toString(byName('parent'))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 gebruiken<br/><br/>
Selecteert een kolom waarde op basis van de relatieve positie (1 gebaseerd) in de stroom. Als de positie buiten het bereik valt, wordt een NULL-waarde geretourneerd. De geretourneerde waarde moet van het type worden geconverteerd door een van de Type Conversion Functions (TO_DATE, TO_STRING...) Berekende invoer wordt niet ondersteund, maar u kunt parameter substituties * ``toString(byPosition(1))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 gebruiken<br/><br/>
Op basis van afwisselende voor waarden geldt een waarde of de andere. Als het aantal invoer waarden even is, wordt de andere standaard ingesteld op NULL voor de laatste voor waarde * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
De derdemachts wortel van een getal berekenen * ``cbrt(8) -> 2.0`` @ no__t-1 @ no__t-2<br/><br/>
Retourneert het kleinste gehele getal dat niet kleiner is dan het getal * ``ceil(-0.1) -> 0`` @ no__t-1 @ no__t-2<br/><br/>
Retourneert de eerste not null-waarde uit een set invoer waarden. Alle invoer waarden moeten van hetzelfde type zijn * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vergelijkt twee waarden van hetzelfde type. Retourneert een negatief geheel getal als waarde1 < Value2, 0 als waarde1 = = waarde2, positieve waarde als waarde1 > Value2 * ``(compare(12, 24) < 1) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Voegt een variabele aantal teken reeksen samen. Hetzelfde als de operator + met teken reeksen * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Voegt een variabele aantal teken reeksen samen met een scheidings teken. De eerste para meter is het scheidings * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Retourneert waar als een element in de geleverde matrix als waar wordt geëvalueerd in het geleverde predicaat. Contains verwacht een verwijzing naar één element in de predicaat functie als #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt een cosinus waarde berekend * ``cos(10) -> -0.8390715290764524`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt een hyperbolische cosinus van een waarde berekend * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt het totale aantal waarden opgehaald. Als de optionele kolom (men) is opgegeven, worden NULL-waarden genegeerd in het aantal * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Hiermee wordt het totale aantal afzonderlijke waarden van een set kolommen opgehaald * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium wordt het totale aantal waarden opgehaald. Als de optionele kolom is opgegeven, worden NULL-waarden genegeerd in de Count * ``countIf(state == 'CA' && commission < 10000, name)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de afwijking van de populatie tussen twee kolommen opgehaald * ``covariancePopulation(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium, wordt de covariantie van de populatie opgehaald uit twee kolommen * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de voorbeeld covariantie van twee kolommen opgehaald * ``covarianceSample(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium wordt de voorbeeld covariantie van twee kolommen opgehaald * ``covarianceSampleIf(region == 'West', sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Berekent de CRC32-hash van een set kolom van verschillende primitieve gegevens typen met een bitlengte die alleen uit waarden 0 (256), 224, 256, 384, 512 kan zijn. Het kan worden gebruikt voor het berekenen van een vinger afdruk voor een rij * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L`` @ no__t-1 @ no__t-2<br/><br/>
De functie CumeDist berekent de positie van een waarde ten opzichte van alle waarden in de partitie. Het resultaat is het aantal rijen dat voorafgaat aan of gelijk is aan de huidige rij in de volg orde van de partitie gedeeld door het totaal aantal rijen in de venster partitie. Alle gelijke waarden in de volg orde worden geëvalueerd op dezelfde positie.
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Hiermee wordt de huidige datum opgehaald waarop deze taak wordt uitgevoerd. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt. Raadpleeg de SimpleDateFormat van Java voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Hiermee wordt het huidige tijds tempel opgehaald wanneer de taak wordt uitgevoerd met de lokale tijd zone * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de huidige tijds tempel opgehaald als UTC. Als u wilt dat uw huidige tijd wordt geïnterpreteerd in een andere tijd zone dan de tijd zone van uw cluster, kunt u een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De waarde wordt standaard ingesteld op de huidige tijd zone. Raadpleeg de SimpleDateFormat van Java voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.htmlTo zet de UTC-tijd om in een andere tijd zone gebruik fromUTC () * ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Hiermee wordt de dag van de maand opgehaald op basis van een datum * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de dag van de week opgehaald op basis van een datum. 1: zondag, 2-maandag..., 7-zaterdag * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de dag van het jaar opgehaald op basis van een datum * ``dayOfYear(toDate('2016-04-09')) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
De duur in milliseconden voor het aantal dagen * ``days(2) -> 172800000L`` @ no__t-1 @ no__t-2<br/><br/>
Converteert radialen naar graden * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de positie van een waarde in een groep waarden berekend. Het resultaat is één plus het aantal rijen dat voorafgaat aan of gelijk is aan de huidige rij in de volg orde van de partitie. De waarden veroorzaken geen hiaten in de reeks. Een dichte rang werkt zelfs wanneer er geen gegevens worden gesorteerd en er wordt gezocht naar wijziging in waarden * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Deelt een combi natie van getallen. Hetzelfde als de operator/* ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt gecontroleerd of de teken reeks eindigt met de opgegeven teken reeks * ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Operator voor vergelijking gelijk aan. Hetzelfde als = = operator * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
De vergelijkings operator is gelijk aan het hoofdletter gebruik negeren. Hetzelfde als < = > operator * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Bereken de faculteit van een getal * ``factorial(5) -> 120`` @ no__t-1 @ no__t-2<br/><br/>
Retourneert altijd een waarde ONWAAR. Gebruik de functie syntaxis (false ()) als er een kolom is met de naam False * ``(10 + 20 > 30) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee worden elementen van de matrix gefilterd die niet voldoen aan het gegeven predicaat. Filter verwacht een verwijzing naar één element in de predicaat functie als #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt de eerste waarde van een kolom groep opgehaald. Als de tweede para meter ignoreNulls wordt wegge laten, wordt ervan uitgegaan dat deze ONWAAR is * ``first(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Retourneert het grootste gehele getal dat niet groter is dan het getal * ``floor(-0.1) -> -1`` @ no__t-1 @ no__t-2<br/><br/>
Wordt geconverteerd naar de tijds tempel van UTC. U kunt eventueel de tijd zone in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden ' door geven. Het wordt standaard ingesteld op de huidige SimpleDateFormat van timezoneRefer Java voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Hogere operator voor vergelijking. Hetzelfde als > operator * ``greater(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
De vergelijking is groter dan of gelijk aan de operator. Hetzelfde als > = operator * ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Retourneert de grootste waarde tussen de lijst met waarden als invoer waarbij Null-waarden worden overgeslagen. Retourneert null als alle invoer waarden null zijn * ``greatest(10, 30, 15, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Hiermee wordt in de stroom op naam naar een kolom waarde gecontroleerd. U kunt een optionele naam van een stream door geven als het tweede argument.  Kolom namen die bekend zijn tijdens de ontwerp fase moeten worden geadresseerd op basis van de naam. Berekende invoer wordt niet ondersteund, maar u kunt parameter substituties * ``hasColumn('parent')`` @ no__t-1 @ no__t-2 gebruiken<br/><br/>
Hiermee wordt de uur waarde van een tijds tempel opgehaald. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt. Raadpleeg de SimpleDateFormat van Java voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
De duur in milliseconden voor het aantal uur * ``hours(2) -> 7200000L`` @ no__t-1 @ no__t-2<br/><br/>
Wijst elk element van de matrix toe aan een nieuw element met behulp van de gegeven expressie. De toewijzing verwacht een verwijzing naar één element in de expressie functie als #item en een verwijzing naar de element index als #index * ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een voor waarde geldt één waarde of de andere. Als de andere waarde niet wordt opgegeven, wordt deze als NULL beschouwd. Beide waarden moeten compatibel zijn (numeriek, teken reeks...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Hiermee wordt gecontroleerd of de waarde niet NULL is en wordt geretourneerd, anders wordt het alternatief geretourneerd. De test wordt uitgevoerd voor alle invoer totdat de eerste niet-null-waarde wordt gevonden * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Hiermee wordt gecontroleerd of een item zich in de matrix bevindt * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Converteert de eerste letter van elk woord naar hoofd letters. Woorden worden aangeduid als gescheiden door witruimte * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de positie (1 op basis van de subtekenreeks in een teken reeks) gezocht. 0 wordt geretourneerd als het niet is gevonden * ``instr('dumbo', 'mbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Hiermee wordt gecontroleerd of de rij is gemarkeerd voor verwijderen. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt gecontroleerd of de rij als fout is gemarkeerd. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt gecontroleerd of de rij is gemarkeerd om te worden genegeerd. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt gecontroleerd of de rij is gemarkeerd voor invoegen. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt gecontroleerd of de rij overeenkomt bij het opzoeken. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controleert of de waarde NULL is * ``isNull(NULL()) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt gecontroleerd of de rij is gemarkeerd voor bijwerken. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt gecontroleerd of de rij is gemarkeerd voor invoegen. Voor trans formaties waarbij meer dan één invoer stroom wordt gemaakt, kunt u de (1-op-basis) index van de stroom door geven. De standaard waarde voor de index van de stroom is 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt de kurtosis van een kolom * ``kurtosis(sales)`` @ no__t-1 @ no__t-2 opgehaald<br/><br/>
Op basis van een criterium, wordt de kurtosis van een kolom * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de waarde opgehaald van de eerste para meter die n rijen voor de huidige rij is geëvalueerd. De tweede para meter is het aantal rijen dat u wilt terugkijken en de standaard waarde is 1. Als er niet zoveel rijen zijn, wordt een waarde van Null geretourneerd, tenzij een standaard waarde is opgegeven * ``lag(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt de laatste waarde van een kolom groep opgehaald. Als de tweede para meter ignoreNulls wordt wegge laten, wordt ervan uitgegaan dat deze ONWAAR is * ``last(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt de laatste datum van de maand opgehaald op basis van een datum * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de waarde opgehaald van de eerste para meter, geëvalueerd n rijen na de huidige rij. De tweede para meter is het aantal rijen dat u wilt zoeken en de standaard waarde is 1. Als er niet zoveel rijen zijn, wordt een waarde van Null geretourneerd, tenzij een standaard waarde is opgegeven * ``lead(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vergelijkings operator kleiner dan of gelijk aan. Hetzelfde als < = operator * ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt een subtekenreeks geëxtraheerd die begint bij index 1 met het aantal tekens. Hetzelfde als subtekenreeks (str, 1, n) * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Retourneert de lengte van de teken reeks * ``length('dumbo') -> 5`` @ no__t-1 @ no__t-2<br/><br/>
Operator voor vergelijkings minus. Hetzelfde als < operator * ``lesser(12, 24) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Vergelijkings operator kleiner dan of gelijk aan. Hetzelfde als < = operator * ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt de Levenshtein-afstand van twee teken reeksen opgehaald * ``levenshtein('boys', 'girls') -> 4`` @ no__t-1 @ no__t-2<br/><br/>
Het patroon is een teken reeks die letterlijk overeenkomt. De uitzonde ringen zijn de volgende speciale symbolen: _ overeenkomen met een wille keurig teken in de invoer (vergelijkbaar met. in POSIX-reguliere expressies)% komt overeen met nul of meer tekens in de invoer (vergelijkbaar met. * in reguliere expressies van POSIX).
Het escape-teken is ' '. Als een escape-teken voorafgaat aan een speciaal symbool of een ander escape teken, wordt het volgende teken letterlijk vergeleken. Het is niet toegestaan om een ander teken te escapepen.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Hiermee wordt de positie (1 op basis van de subtekenreeks) binnen een teken reeks met een bepaalde positie gevonden. Als de positie wordt wegge laten, wordt deze in overweging genomen vanaf het begin van de teken reeks. 0 wordt geretourneerd als het niet is gevonden * ``locate('mbo', 'dumbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
De logboek waarde wordt berekend. Een optionele basis kan worden opgegeven als een Euler-getal bij gebruik * ``log(100, 10) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de logboek waarde berekend op basis van 10 basis * ``log10(100) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Kleine letters een teken reeks * ``lower('GunChus') -> 'gunchus'`` @ no__t-1 @ no__t-2<br/><br/>
Links wordt de teken reeks met de opgegeven opvulling gematteerd tot deze een bepaalde lengte heeft. Als de teken reeks gelijk is aan of groter is dan de lengte, wordt deze bijgesneden tot de lengte * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2 ' ' lpad (Dumbo ', 8, ' < > ')-> ' < > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5<br/><br/>
Hiermee wordt een teken reeks met voorloop tekens bijgesneden. Als de tweede para meter niet is opgegeven, wordt witruimte verkleind. Anders wordt het teken dat is opgegeven in de tweede para meter (* ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3) verkleind.<br/><br/>
Wijst elk element van de matrix toe aan een nieuw element met behulp van de gegeven expressie. De toewijzing verwacht een verwijzing naar één element in de expressie functie als #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt de maximum waarde opgehaald van een kolom * ``max(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium haalt de maximum waarde van een kolom op * ``maxIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de MD5-Digest van een set kolom van verschillende primitieve gegevens typen berekend en wordt een hexadecimale teken reeks van 32 tekens geretourneerd. Het kan worden gebruikt voor het berekenen van een vinger afdruk voor een rij * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt het gemiddelde van de waarden van een kolom opgehaald. Hetzelfde als AVG * ``mean(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium wordt het gemiddelde van de waarden van een kolom opgehaald. Hetzelfde als avgIf * ``meanIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de milliseconde-waarde van een datum opgehaald. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt. Raadpleeg de SimpleDateFormat van Java voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
De duur in milliseconden voor het aantal milliseconden * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de minimum waarde van een kolom * ``min(sales)`` @ no__t-1 @ no__t-2 opgehaald<br/><br/>
Op basis van een criterium retourneert de minimum waarde van een kolom * ``minIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Trekt getallen af. Aftrekken van het datum aantal dagen. De duur van de Substract van een tijds tempel. Substract twee time Stamps om het verschil in milliseconden op te halen. Hetzelfde als de-operator * ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Hiermee wordt de minuut waarde van een tijds tempel opgehaald. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt. Raadpleeg de SimpleDateFormat van Java voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
De duur in milliseconden voor het aantal minuten * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
Modulus van een combi natie van getallen. Hetzelfde als de operator% * ``mod(20, 8) -> 4`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt de maand waarde van een datum of tijds tempel opgehaald * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt het aantal maanden tussen twee datums opgehaald. U kunt de berekening afronden. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt. Raadpleeg de SimpleDateFormat van Java voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Vermenigvuldigt het paar getallen. Hetzelfde als de operator * * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Met de functie NTile worden de rijen voor elke venster partitie verdeeld over `n` buckets, variërend van 1 tot Maxi maal `n`. Bucket waarden kunnen Maxi maal 1 zijn. Als het aantal rijen in de partitie niet gelijkmatig is verdeeld over het aantal buckets, worden de waarden van de rest gedistribueerd per Bucket, beginnend met de eerste Bucket. De functie NTile is handig voor het berekenen van tertiles, kwartielen, deciles en andere algemene samenvattings statistieken. De functie berekent twee variabelen tijdens de initialisatie: Er wordt aan de grootte van een normale Bucket een extra rij toegevoegd. Beide variabelen zijn gebaseerd op de grootte van de huidige partitie. Tijdens het berekenings proces houdt de functie het huidige rijnummer, het huidige Bucket nummer en het rijnummer waarbij de Bucket wordt gewijzigd (bucketThreshold) bij. Wanneer het huidige rijnummer de Bucket drempel bereikt, wordt de Bucket waarde verhoogd met één en wordt de drempel verhoogd met de Bucket grootte (plus één extra als de huidige Bucket wordt opgevuld).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Een getal wordt genegeerd. Hiermee worden positieve getallen negatief en omgekeerd * ``negate(13) -> -13`` @ no__t-1 @ no__t-2<br/><br/>
Retourneert de volgende unieke sequentie. Het getal is opeenvolgend alleen binnen een partitie en wordt voorafgegaan door de partitionId * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2<br/><br/>
Normaliseren van de teken reeks waarde voor het scheiden van geaccentde Unicode-tekens * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2<br/><br/>
Logische negatie-operator * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
De vergelijkings operator is niet gelijk aan. Hetzelfde als! = operator * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Retourneert een NULL-waarde. Gebruik de functie syntaxis (Null ()) als er een kolom is met de naam null. Elke bewerking die wordt gebruikt, resulteert in een NULL-* ``isNull('dumbo' + null) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Logische OR-operator. Hetzelfde als | | * ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Positieve modulus van een paar getallen.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Retourneert de huidige partitie-id. de invoer rij bevindt zich in * ``partitionId()`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt één getal verhoogd naar de kracht van een andere * ``power(10, 2) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de positie van een waarde in een groep waarden berekend. Het resultaat is één plus het aantal rijen dat voorafgaat aan of gelijk is aan de huidige rij in de volg orde van de partitie. De waarden veroorzaken hiaten in de reeks. Rang werkt zelfs wanneer er geen gegevens worden gesorteerd en er wordt gezocht naar wijziging in waarden * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Verzamelt elementen in een matrix. Reductie verwacht een verwijzing naar een accumulator en één element in de eerste expressie functie als #acc en #item en verwacht de resulterende waarde als #result voor gebruik in de tweede expressie functie * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Een overeenkomende subtekenreeks ophalen voor een opgegeven regex-patroon. De laatste para meter identificeert de overeenkomende groep en wordt standaard ingesteld op 1 als dit wordt wegge laten. Gebruik ' <regex> ' (achterwaartse aanhalings tekens) om een teken reeks te zoeken zonder Escapes * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Controleert of de teken reeks overeenkomt met het opgegeven regex-patroon. Gebruik ' <regex> ' (achterwaartse aanhalings tekens) om een teken reeks te zoeken zonder Escapes * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Alle exemplaren van een regex-patroon vervangen door een andere subtekenreeks in de opgegeven teken reeks gebruik ' <regex> ' (achterwaartse aanhalings tekens) om een teken reeks te zoeken zonder Escapes * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Splitst een teken reeks op basis van een scheidings teken op basis van een regex en retourneert een matrix met teken reeksen * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Alle exemplaren van een subtekenreeks vervangen door een andere subtekenreeks in de opgegeven teken reeks. Als de laatste para meter wordt wegge laten, wordt standaard de lege teken reeks * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Hiermee wordt een teken reeks * ``reverse('gunchus') -> 'suhcnug'`` en no__t-1 @ no__t-2 omgekeerd<br/><br/>
Hiermee wordt een subtekenreeks geëxtraheerd met het aantal tekens vanaf de rechter kant. Hetzelfde als subtekenreeks (str, LENGTH (str)-n, n) * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Controleert of de teken reeks overeenkomt met het opgegeven regex-patroon * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Rondt een getal af op basis van een optionele schaal en een optionele Afrondings modus. Als de schaal wordt wegge laten, wordt deze standaard ingesteld op 0.  Als de modus wordt wegge laten, wordt deze standaard ingesteld op ROUND_HALF_UP (5). De waarden voor afronding bevatten 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Hiermee wijst u een sequentiële rijnummer nummering toe voor rijen in een venster, te beginnen met 1 * ``rowNumber()`` @ no__t-1 @ no__t-2<br/><br/>
Met rechts wordt de teken reeks met de opgegeven opvulling gematteerd tot deze een bepaalde lengte heeft. Als de teken reeks gelijk is aan of groter is dan de lengte, wordt deze Inge kort tot de lengte * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5<br/><br/>
Rechts knipt een teken reeks van voorloop tekens. Als de tweede para meter niet is opgegeven, wordt witruimte verkleind. Anders wordt het teken dat is opgegeven in de tweede para meter (* ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3) verkleind.<br/><br/>
Hiermee wordt de tweede waarde van een datum opgehaald. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. De lokale tijd zone wordt als standaard waarde gebruikt. Raadpleeg de SimpleDateFormat van Java voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
De duur in milliseconden voor het aantal seconden * ``seconds(2) -> 2000L`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt het SHA-1-overzicht van een set kolom van verschillende primitieve gegevens typen berekend en wordt een hexadecimale teken reeks van 40 tekens geretourneerd. Het kan worden gebruikt voor het berekenen van een vinger afdruk voor een rij * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt het SHA-2-overzicht van een set kolom van verschillende primitieve gegevens typen berekend met een bitlengte die alleen uit waarden 0 (256), 224, 256, 384, 512 kan zijn. Het kan worden gebruikt voor het berekenen van een vinger afdruk voor een rij * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt een sinus waarde berekend * ``sin(2) -> 0.9092974268256817`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt een hyperbolische sinus waarde berekend * ``sinh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de scheefheid van een kolom * ``skewness(sales)`` @ no__t-1 @ no__t-2 opgehaald<br/><br/>
Op basis van een criterium haalt de scheefheid van een kolom * ``skewnessIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt een subset van een matrix geëxtraheerd uit een positie. De positie is op basis van 1. Als de lengte wordt wegge laten, wordt deze standaard ingesteld op einde van de teken reeks * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Sorteert de matrix met behulp van de meegeleverde predicaat functie. Bij het sorteren wordt een verwijzing verwacht naar twee opeenvolgende elementen in de expressie-functie als #item1 en #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt de Soundex-code opgehaald voor de teken reeks * ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2<br/><br/>
Splitst een teken reeks op basis van een scheidings teken en retourneert een matrix met teken reeksen * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
Berekent de vierkantswortel van een getal * ``sqrt(9) -> 3`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt gecontroleerd of de teken reeks begint met de opgegeven teken reeks * ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de standaard afwijking van een kolom opgehaald * ``stdDev(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium haalt de standaard afwijking van een kolom * ``stddevIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de standaard deviatie van de populatie opgehaald van een kolom * ``stddevPopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium haalt de standaard deviatie van de populatie van een kolom * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de standaard deviatie van een kolom opgehaald * ``stddevSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium haalt de standaard afwijking van een kolom * ``stddevSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Maanden aftrekken van een datum of tijds tempel. Hetzelfde als de-operator voor datum * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t-1 @ no__t-2<br/><br/>
Maanden aftrekken van een datum of tijds tempel * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt een subtekenreeks van een bepaalde lengte opgehaald uit een positie. De positie is op basis van 1. Als de lengte wordt wegge laten, wordt deze standaard ingesteld op einde van de teken reeks * ``substring('Cat in the hat', 5, 2) -> 'in'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Hiermee wordt de cumulatieve som opgehaald van een numerieke kolom * ``sum(col)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de cumulatieve som opgehaald van afzonderlijke waarden van een numerieke kolom * ``sumDistinct(col)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van criteria haalt de cumulatieve som van een numerieke kolom op. De voor waarde kan worden gebaseerd op elke kolom * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Op basis van criteria haalt de cumulatieve som van een numerieke kolom op. De voor waarde kan worden gebaseerd op elke kolom * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt een raaklijn waarde berekend * ``tan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt een hyperbolische raaklijn waarde berekend * ``tanh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Converteert wille keurige numerieke/datum/tijds tempel/teken reeks naar binaire weer gave * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2<br/><br/>
Converteert een waarde van ('t ', ' True ', ' y ', ' Yes ', ' 1 ') naar True en (f, False, n, no ', ' 0 ') naar false en NULL voor een andere waarde * ``toBoolean('true') -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
De invoer datum teken reeks wordt geconverteerd naar een datum met een optionele notatie voor de invoer datum. Raadpleeg de SimpleDateFormat van Java voor beschik bare indelingen. Als de indeling van de invoer datum wordt wegge laten, is de standaard notatie JJJJ-[M] M-[d] d. Geaccepteerde notaties zijn: [jjjj, jjjj-[M] M, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Converteert een wille keurig getal of teken reeks naar een decimale waarde. Als er geen precisie en schaal is opgegeven, wordt het standaard ingesteld op (10, 2). Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Een optionele notatie voor land instellingen in de vorm van BCP47 taal zoals en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Converteert een wille keurig getal of teken reeks naar een dubbele waarde. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Een optionele notatie voor land instellingen in de vorm van BCP47 taal zoals en-US, de, zh-CN * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Converteert een wille keurig getal of teken reeks naar een float-waarde. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Verkapt dubbele * ``toFloat(123.45) -> 123.45f`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converteert een wille keurige numerieke waarde of teken reeks naar een geheel getal. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Kapt een Long, float, Double * ``toInteger(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4.<br/><br/>
Converteert een wille keurig getal of teken reeks naar een lange waarde. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Kapt een wille keurige float af, dubbel * ``toLong(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converteert een wille keurig getal of teken reeks naar een korte waarde. Een optionele Java-decimale notatie kan worden gebruikt voor de conversie. Kapt een geheel getal, lang, float, dubbel * ``toShort(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4.<br/><br/>
Hiermee wordt een primitieve gegevens type geconverteerd naar een teken reeks. Voor getallen en datum kan een notatie worden opgegeven. Als u niet opgeeft, wordt de standaard waarde van het systeem gekozen. De decimale notatie voor de Java wordt gebruikt voor getallen. Raadpleeg Java SimpleDateFormat voor alle mogelijke datum notaties. de standaard notatie is JJJJ-MM-DD * ``toString(10) -> '10'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Hiermee wordt een teken reeks geconverteerd naar een tijds tempel met een optionele time stamp-notatie. Raadpleeg Java SimpleDateFormat voor alle mogelijke indelingen. Als de tijds tempel wordt wegge laten, wordt het standaard patroon gebruikt. jjjj-[M] M-[d] d uu: mm: SS [. f...] U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. Time Stamp ondersteunt een nauw keurigheid van Maxi maal milliseconden met waarde van 999Refer Java SimpleDateFormat voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Hiermee wordt de tijds tempel geconverteerd naar UTC. U kunt een optionele tijd zone door geven in de vorm ' GMT ', ' PST ', ' UTC ', ' America/Caymaneilanden '. Het wordt standaard ingesteld op de huidige SimpleDateFormat van timezoneRefer Java voor beschik bare indelingen. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Een reeks tekens vervangen door een andere set tekens in de teken reeks. Tekens hebben 1 tot 1 vervanging * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hiermee wordt een teken reeks van voor loop-en volg tekens verkleind. Als de tweede para meter niet is opgegeven, wordt witruimte verkleind. Anders wordt het teken dat is opgegeven in de tweede para meter (* ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3) verkleind.<br/><br/>
Retourneert altijd een waarde waar. Gebruik de functie syntaxis (True ()) als er een kolom is met de naam True * ``(10 + 20 == 30) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Komt overeen met het type van de kolom. Kan alleen worden gebruikt in patroon expressies. getal komt overeen met kort, geheel getal, lang, dubbel, zwevend of decimaal teken, integraal komt overeen met short, integer, Long, Fractional komt overeen met de datum of het tijds tempel type * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__ t-3<br/><br/>
Hoofd letters een teken reeks * ``upper('bojjus') -> 'BOJJUS'`` @ no__t-1 @ no__t-2<br/><br/>
Retourneert de gegenereerde UUID * ``uuid()`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de variantie van een kolom * ``variance(sales)`` @ no__t-1 @ no__t-2 opgehaald<br/><br/>
Op basis van een criterium wordt de afwijking van een kolom * ``varianceIf(region == 'West', sales)`` @ no__t-1 @ no__t-2 opgehaald<br/><br/>
Hiermee wordt de variantie van de populatie van een kolom * ``variancePopulation(sales)`` @ no__t-1 @ no__t-2 opgehaald<br/><br/>
Op basis van een criterium haalt de populatie variantie op van een kolom * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de onzuivere variantie opgehaald van een kolom * ``varianceSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Op basis van een criterium, wordt de onzuivere variantie opgehaald van een kolom * ``varianceSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hiermee wordt de week van het jaar opgehaald op basis van een datum * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
De duur in milliseconden voor het aantal weken * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
Logische XOR-operator. Hetzelfde als ^ operator * ``xor(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Hiermee wordt de jaartal waarde opgehaald van een datum * ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het gebruik van Expression Builder](concepts-data-flow-expression-builder.md).
