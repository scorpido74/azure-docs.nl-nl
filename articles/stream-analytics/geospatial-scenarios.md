---
title: Geofencing en georuimtelijke aggregatie met Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics gebruiken voor geofencing en georuimtelijke aggregatie.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 5a3aa3786469c3df37b53cb82bdd396871689297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443645"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Scenario's voor geofencing en georuimtelijke aggregatie met Azure Stream Analytics

Met ingebouwde georuimtelijke functies u Azure Stream Analytics gebruiken om toepassingen te bouwen voor scenario's zoals fleetmanagement, ritdelen, verbonden auto's en assettracking.

## <a name="geofencing"></a>Geofencing (Geofencing)

Azure Stream Analytics ondersteunt realtime geofencingberekeningen met lage latentie in de cloud en op de IoT Edge-runtime.

### <a name="geofencing-scenario"></a>Geofencing-scenario

Een productiebedrijf moet de activa van hun gebouwen bijhouden. Ze hebben elk apparaat uitgerust met een GPS en willen meldingen ontvangen als een apparaat een bepaald gebied verlaat.

Referentiegegevens die in dit voorbeeld worden gebruikt, hebben de geofence-informatie voor de gebouwen en de apparaten die in elk van de gebouwen zijn toegestaan. Houd er rekening mee dat referentiegegevens statisch of traag kunnen veranderen. Voor dit scenario worden statische referentiegegevens gebruikt. Een stroom van gegevens zendt continu de apparaat-ID en de huidige positie uit.

### <a name="define-geofences-in-reference-data"></a>Geofences definiëren in referentiegegevens

Een geofence kan worden gedefinieerd met behulp van een GeoJSON-object. Voor taken met compatibiliteitsversie 1.2 en hoger kunnen geofences ook worden gedefinieerd `NVARCHAR(MAX)`met bekende tekst (WKT) als . WKT is een Open Geospatial Consortium (OGC) standaard die wordt gebruikt om ruimtelijke gegevens in een tekstuele indeling weer te geven.

De ingebouwde georuimtelijke functies kunnen gedefinieerde geofences gebruiken om erachter te komen of een element zich in of uit een specifieke geofence-veelhoek bevindt.

De volgende tabel is een voorbeeld van geofence-referentiegegevens die kunnen worden opgeslagen in Azure blob-opslag of een Azure SQL-tabel. Elke site wordt vertegenwoordigd door een georuimtelijke veelhoek en elk apparaat is gekoppeld aan een toegestane site-id.

|Site-id|Sitenaam|Geofence (Geofence)|ToegestaneDeviceID|
|------|--------|--------|---------------|
|1|"Redmond Gebouw 41"|"POLYGON(-122.1337357922017 47,6378298329432,-122.13373042778369 47,637634793257305,-122.13346757130023 47,637642 022530954,-122,13348902897235 47,637508280806806806,-122,13361777500506 47,637508280806806806,-122,13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|"B"|
|2|"Redmond Gebouw 40"|"POLYGON(-122.1336154507967 47,6366745947009,-122.13361008637867 47,636483015064535,-122.13349206918201 47,636479 400347675,-122,13349743360004 47,63636372927573,-122,13372810357532 47,63636372927573,-122,1337334679335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|"A"|
|3|"Redmond Gebouw 22"|"POLYGON(-122.1361160248233 47,63758544698554,-122,13635263687564 47,637408329393018,-122,13622389084293 47,63733 603619712,-122,136222389084293 47,63717699101473,-122,1358161950726 47,63692757827657,-122,1355962539344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|"C"|

### <a name="generate-alerts-with-geofence"></a>Waarschuwingen genereren met geofence

Apparaten kunnen hun ID en locatie elke `DeviceStreamInput`minuut uitzenden via een stream genaamd . De volgende tabel is een stroom van invoer.

|DeviceID|Geopositie|
|--------|-----------|
|"A"|"PUNT(-122.13292341559497 47,636318374032726)"|
|"B"|"PUNT(-122.133847555553 47,63743531308874)"|
|"C"|"PUNT(-122.13354001095752 47,63627622505007)"|

U een query schrijven die de apparaatstroom aansluit bij de referentiegegevens van geofence en een waarschuwing genereert telkens wanneer een apparaat zich buiten een toegestaan gebouw bevindt.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

De volgende afbeelding vertegenwoordigt de geofences. U zien waar de apparaten zijn in overeenstemming met de invoer van stroomgegevens.

![Het bouwen van geofences](./media/geospatial-scenarios/building-geofences.png)

Apparaat "C" bevindt zich in gebouw ID 2, wat niet is toegestaan op basis van de referentiegegevens. Dit apparaat moet zich in gebouw ID 3 bevinden. Als u deze taak uitvoert, wordt een waarschuwing gegenereerd voor deze specifieke schending.

### <a name="site-with-multiple-allowed-devices"></a>Site met meerdere toegestane apparaten

Als een site meerdere apparaten toestaat, kan een array `AllowedDeviceID` met apparaat-id's worden gedefinieerd `WHERE` en kan een door de gebruiker gedefinieerde functie op de clausule worden gebruikt om te controleren of de stream-apparaat-id overeenkomt met een apparaat-id in die lijst. Bekijk de [Javascript UDF-zelfstudie](stream-analytics-javascript-user-defined-functions.md) voor cloudtaken en de [C# UDF-zelfstudie](stream-analytics-edge-csharp-udf.md) voor randtaken voor meer informatie.

## <a name="geospatial-aggregation"></a>Georuimtelijke aggregatie

Azure Stream Analytics ondersteunt realtime georuimtelijke aggregatie met lage latentie in de cloud en op de IoT Edge-runtime.

### <a name="geospatial-aggregation-scenario"></a>Georuimtelijk aggregatiescenario

Een taxibedrijf wil een real-time applicatie bouwen om hun taxichauffeurs te begeleiden op zoek naar een rit naar de gebieden van de steden die momenteel een hogere vraag ervaren.

Het bedrijf slaat logische regio's van de stad op als referentiegegevens. Elke regio wordt gedefinieerd door een RegionID, RegionName en Geofence.

### <a name="define-the-geofences"></a>Definieer de geofences

De volgende tabel is een voorbeeld van geofence-referentiegegevens die kunnen worden opgeslagen in Azure blob-opslag of een Azure SQL-tabel. Elke regio wordt vertegenwoordigd door een georuimtelijke veelhoek, die wordt gebruikt om te correleren met de verzoeken afkomstig van streaming gegevens.

Deze polygonen zijn alleen ter referentie en vertegenwoordigen geen werkelijke stad logische of fysieke scheidingen.

|Regio-ID|RegioNaam|Geofence (Geofence)|
|--------|----------|--------|
|1|"Soho"|"POLYGON(-74.00279525078275 40,72833625216264,-74,00547745979797 65 40.721929158663244,-74,00125029839018 40,718936802189 94,-73,99577859998 40,72521409075776,-73,9972377137039 40.72557184584898,-74,00279525078275 40,72833625216264) )"|
|2|"Chinatown"|"POLYGON(-73.99712367114876 40,71281582267133,-73,9901070123658 40,713681907936,-73,99023575839851 40,714523 59088633,-73,98976368961189 40,71554823078944,-73,99551434573982 40,717337246783735,-73,99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"POLYGON(-74.01091641815208 40,72583120006787,-74,01338405044578 40,7136586362705,-74,013705915552757 40,71361 7702123415,-74,00862044723533 40,711308107057235,-74,00194711120628 40,7194238654018,-74,01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Gegevens samenvoegen over een tijdsvenster

De volgende tabel bevat streaminggegevens van 'ritten'.

|UserID|FromLocation|ToLocatie|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"PUNT(-74,00726861389182 40,71610611981975)"|"PUNT(-73,98615095917779 40,703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"PUNT(-74,00249841021645 40,723827238895666)"|"PUNT(-74.01160699942085 40,713788884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"PUNT(-73,99680120565864 40,716439898624024)"|"PUNT(-73,98289663412544 40,72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"PUNT(-74,00741090068288 40,71615626755086)"|"PUNT(-73,97999843120539 40,73477895807408)"|"2019-03-12T07:03:00Z"|

De volgende query voegt de apparaatstroom samen met de referentiegegevens van geofence en berekent het aantal aanvragen per regio in een tijdvenster van 15 minuten per minuut.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Met deze query wordt elke minuut een aantal aanvragen voor de laatste 15 minuten uitgevoerd door elke regio binnen de stad. Deze informatie kan eenvoudig worden weergegeven door het Power BI-dashboard of kan als sms-berichten naar alle stuurprogramma's worden verzonden via integratie met services zoals Azure-functies.

De onderstaande afbeelding illustreert de uitvoer van de query naar het Power BI-dashboard. 

![Resultaatuitvoer op Power BI-dashboard](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot georuimtelijke functies van Stream Analytics](stream-analytics-geospatial-functions.md)
* [Georuimtelijke functies (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
