---
title: Uw scorekaart interpreteren | Microsoft Documenten
description: De veelgestelde vragen voor Azure Internet Analyzer.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512884"
---
# <a name="interpreting-your-scorecard"></a>Uw scorecard interpreteren

Het scorecardtabblad bevat de geaggregeerde en geanalyseerde resultaten van uw tests. Elke test heeft zijn eigen scorecards. Scorecards bieden snelle en zinvolle samenvattingen van meetresultaten om gegevensgestuurde resultaten te leveren voor uw netwerkvereisten. Internet Analyzer zorgt voor de analyse, zodat u zich concentreren op de beslissing.

Het tabblad scorecard is te vinden in het menu van de internetanalyzerbron. 


## <a name="filters"></a>Filters

* ***Test:*** Selecteer de test waarvoor u de resultaten wilt bekijken - elke test heeft zijn eigen scorecard. Testgegevens verschijnen zodra er voldoende gegevens zijn om de analyse te voltooien – in de meeste gevallen moet dit binnen 24 uur zijn. 
* ***Periode & einddatum:*** Drie scorecards worden dagelijks gegenereerd - elke scorecard weerspiegelt een andere aggregatieperiode - de 24 uur voorafgaand (dag), de zeven dagen voorafgaand (week), en de 30 dagen voorafgaand (maand). Gebruik het filter Einddatum om de laatste dag van de gewenste periode te selecteren. 
* ***Land:*** Voor elk land dat u eindgebruikers hebt, wordt een scorecard gegenereerd. Het globale filter bevat alle eindgebruikers.

## <a name="measurement-count"></a>Meetaantal

Het aantal metingen beïnvloedt het vertrouwen van de analyse. Hoe hoger het aantal, hoe nauwkeuriger het resultaat. De tests moeten ten minste 100 metingen per eindpunt per dag nastreven. Als het aantal metingen te laag is, configureert u de JavaScript-client om vaker in uw toepassing uit te voeren. De meetaantallen voor eindpunten A en B moeten zeer vergelijkbaar zijn, hoewel kleine verschillen worden verwacht en goed. In het geval van grote verschillen, moeten de resultaten niet worden vertrouwd.

## <a name="percentiles"></a>Percentiles

Latentie, gemeten in milliseconden, is een populaire statistiek voor het meten van snelheid tussen een bron en bestemming op het internet. Latentiegegevens worden normaal gesproken niet gedistribueerd (d.w.z. volgt geen "Belcurve") omdat er een "long-tail" is van grote latentiewaarden die resultaten scheeftrekken bij het gebruik van statistieken zoals het rekenkundig gemiddelde. Als alternatief bieden percentiels een "distributievrije" manier om gegevens te analyseren. Als voorbeeld, de mediaan, of 50e percentiel, vat het midden van de verdeling - de helft van de waarden zijn erboven en de helft zijn eronder. Een 75e percentielwaarde betekent dat het groter is dan 75% van alle waarden in de distributie. Internet Analyzer verwijst naar percentielen in steno als P50, P75 en P95.

Internet Analyzer percentiels zijn _voorbeeldstatistieken._ Dit in tegenstelling tot de werkelijke _populatiemetrische_. Bijvoorbeeld, de dagelijkse werkelijke bevolking mediane latentie tussen studenten aan de Universiteit van Zuid-Californië en Microsoft is de mediane latentie waarde van alle verzoeken gedurende die dag. In de praktijk is het meten van de waarde van alle verzoeken onpraktisch, dus we gaan ervan uit dat een redelijk grote steekproef representatief is voor de werkelijke populatie.

Voor analysedoeleinden is P50 (mediaan) nuttig als verwachte waarde voor een latentieverdeling. Hogere percentielen, zoals P95, zijn handig om te bepalen hoe hoog de latentie in het ergste geval is. Als u geïnteresseerd bent in het begrijpen van klantlatentie in het algemeen, p50 is de juiste metrische focus op. Als u zich bezighoudt met het begrijpen van prestaties voor de slechtst presterende klanten, dan p95 moet de focus. P75 is een evenwicht tussen deze twee.


## <a name="deltas"></a>Delta's

Een delta is het verschil in metrische waarden voor eindpunten A en B. Delta's worden berekend om het voordeel van B ten opzichte van A weer te geven. Positieve waarden geven aan dat B beter presteerde dan A, terwijl negatieve waarden aangeven dat de prestaties van B slechter zijn. Delta's kunnen absoluut zijn (bijvoorbeeld 10 milliseconden) of relatief (5%).

## <a name="confidence-interval"></a>Betrouwbaarheidsinterval 

Betrouwbaarheidsintervallen (CI) zijn een bereik van waarden die een waarschijnlijkheid hebben om de populatiestatistiek zoals mediaan, P75 of gemiddelde te bevatten. We volgen de gemeenschappelijke statistische conventie van het gebruik van de 95% CI.

Voor Internet Analyzer is een smal betrouwbaarheidsinterval goed omdat het laat zien dat de statistiek van de steekproef waarschijnlijk zeer dicht bij de werkelijke populatiestatistiek ligt. Een breed betrouwbaarheidsinterval betekent minder zekerheid dat onze steekproefstatistiek de werkelijke populatiestatistiek weerspiegelt. De beste manier om de CI te verbeteren is het verhogen van het aantal metingen.

## <a name="time-series"></a>Tijdreeks 

Een tijdreeks laat zien hoe een statistiek in de loop van de tijd verandert. Op het internet zijn er veel tijdelijke factoren die van invloed zijn op de prestaties, zoals piekverkeersperioden, weekdag-weekend bevolkingsverschillen en feestdagen.


## <a name="next-steps"></a>Volgende stappen

Zie ons Internet [Analyzer Overview](internet-analyzer-overview.md)voor meer informatie.
