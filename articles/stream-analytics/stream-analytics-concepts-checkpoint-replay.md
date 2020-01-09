---
title: Het controle punt en de concepten van herstel na opnieuw afspelen in Azure Stream Analytics
description: Dit artikel wordt beschreven controlepunt en herhaling recovery concepten van de taak die u in Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f5bb2b97d7da770828c2f4f03167483ad2044c79
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426394"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Controlepunt en herhaling concepten in Azure Stream Analytics-taken
In dit artikel beschrijft de interne controlepunt en herhaling concepten in Azure Stream Analytics en de impact die hebben voor herstel van de taak. Telkens wanneer wordt een Stream Analytics-taak wordt uitgevoerd, informatie over de status bijgehouden intern. Deze informatie over de status wordt opgeslagen in een controlepunt periodiek. De checkpoint-informatie wordt gebruikt voor herstel van de taak in sommige scenario's als een taak is mislukt of een upgrade optreedt. In andere gevallen kan het controlepunt kan niet worden gebruikt voor herstel en een replay nodig is.

## <a name="stateful-query-logicin-temporal-elements"></a>Stateful querylogica in de tijdelijke elementen
Een van de unieke mogelijkheid van Azure Stream Analytics-taak is stateful verwerking, zoals statische functies in vensters, tijdelijke joins en tijdelijke analytische functies uit te voeren. Elk van deze operators blijft informatie over de status wanneer de taak wordt uitgevoerd. De maximale venster grootte voor deze query-elementen is zeven dagen. 

Het concept tijdelijk venster wordt weergegeven in de verschillende elementen van de Stream Analytics-query:
1. Statistische functies in vensters, (groep door van Tumbling, Hopping plaatsvindt, en schuiven windows)

2. Tijdelijke joins (lid worden met DATEDIFF)

3. Tijdelijke analytische functies (ISFIRST, LAST en LAG met LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Taak herstel na een storing op knooppunt, met inbegrip van de upgrade van besturingssysteem
Telkens wanneer die een Stream Analytics-taak wordt uitgevoerd, is intern deze uitgebreid om te werken op meerdere worker-knooppunten. Status van elke worker-knooppunt wordt gecontroleerd om de paar minuten, waarmee het systeem herstellen als er een fout optreedt.

Soms een bepaald worker-knooppunt kan mislukken of de upgrade van een besturingssysteem kan optreden voordat die worker-knooppunt. Als u wilt herstellen automatisch, Stream Analytics een nieuw knooppunt in orde krijgt en de voorafgaande worker-knooppunt is hersteld vanaf het laatste controlepunt. Als u wilt doorgaan met het werk, is een kleine hoeveelheid opnieuw afspelen die nodig zijn voor het herstellen van de status van de tijd wanneer het controlepunt wordt ingesteld. De kloof terugzetten is meestal slechts een paar minuten. Wanneer er onvoldoende Streaming-eenheden zijn geselecteerd voor de taak, moet de herhaling snel worden uitgevoerd. 

In een volledig parallelle query is de tijd die nodig zijn om dingen die na een storing voor een worker-knooppunt in verhouding met:

[de snelheid van de invoer] x [de lengte van de tussenruimte] / [nummer van het verwerken van partities]

Als u ooit flink wat verwerkingstaken vertraging vanwege een storing op knooppunt observeren en OS-upgrade, Overweeg de query volledig parallel en schalen van de taak voor het toewijzen van meer Streaming-eenheden. Zie voor meer informatie, [schalen van een Azure Stream Analytics-taak voor een betere doorvoer](stream-analytics-scale-jobs.md).

Huidige Stream Analytics biedt een rapport niet weergegeven wanneer dit soort herstelproces plaatsvindt.

## <a name="job-recovery-from-a-service-upgrade"></a>Herstel van de taak van een service-upgrade 
Microsoft worden af en toe bijgewerkt voor de binaire bestanden die worden uitgevoerd van de Stream Analytics-taken in de Azure-service. Bij deze tijden taken die worden uitgevoerd voor de gebruikers zijn bijgewerkt naar een nieuwere versie en de taak wordt automatisch opnieuw opgestart. 

De herstel-controlepunt-indeling wordt op dit moment niet behouden tussen upgrades. De status van de streaming-query moet als gevolg hiervan worden hersteld met techniek opnieuw afspelen. Als u wilt toestaan dat de Stream Analytics-taken voor de replay van de exacte dezelfde invoer van voordat het is belangrijk om in te stellen het bewaarbeleid voor de brongegevens die moeten ten minste het venster-grootten in uw query. Niet doet, kan leiden tot onjuiste of gedeeltelijke resultaten tijdens de upgrade van de service, omdat de brongegevens niet moeten worden bewaard klein genoeg om op te nemen van de volledige venstergrootte.

De hoeveelheid opnieuw afspelen nodig is in het algemeen, evenredig aan de grootte van het venster vermenigvuldigd met de gemiddelde snelheid van gebeurtenissen. Als voorbeelden, voor een taak met een invoer-snelheid van 1000 gebeurtenissen per seconde, een groter is dan een uur venstergrootte wordt beschouwd als de grootte van een grote opnieuw afspelen. Maximaal één uur van de gegevens mogelijk worden opnieuw verwerkt om te initialiseren van de status, zodat deze volledig maken kunt en de juiste resultaten, wat kunnen leiden tot vertraagd uitvoer (Er is geen uitvoer) gedurende een langere periode. Query's met geen windows- of andere tijdelijke operators, zoals `JOIN` of `LAG`, nul opnieuw afspelen zou hebben.

## <a name="estimate-replay-catch-up-time"></a>Raming replay achterstallige tijd
Voor een schatting van de lengte van de vertraging is vanwege een upgrade van een service, kunt u deze techniek volgen:

1. De invoer Event Hub met voldoende gegevens voor de grootste venstergrootte in uw query, verwachte gebeurtenis tarief worden geladen. De gebeurtenissen tijdstempels moet dicht bij de kloktijd gedurende die periode als als het een live invoer feed is. Hebt u een venster 3 dagen in uw query, gebeurtenissen verzenden naar Event Hub voor drie dagen en doorgaan met het verzenden van gebeurtenissen. 

2. Start de taak met behulp van **nu** als de begintijd. 

3. Meten van de tijd tussen de begin- en wanneer de eerste uitvoer wordt gegenereerd. De tijd is ruwe hoeveel vertraging die de taak in tijdens de upgrade van een service gebracht rekening.

4. Als de vertraging te lang is, probeert u voor het partitioneren van de taak en verhogen aantal SUs, zodat de belasting op meer knooppunten wordt verdeeld. U kunt ook Verklein de grootte van het venster in de query en voert u verdere aggregatie of andere stateful-verwerking op de uitvoer die wordt geproduceerd door de Stream Analytics-taak in de downstream-sink (bijvoorbeeld met behulp van Azure SQL database).

Voor algemene service stabiliteit is tijdens de upgrade van essentiële taken kritiek taken, kunt u de uitvoering van dubbele taken in de gekoppelde Azure-regio's. Zie voor meer informatie, [garantie Stream Analytics-taak betrouwbaarheid tijdens het bijwerken van de service](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Taak herstel vanaf een door de gebruiker geïnitieerde stoppen en starten
De Query-syntaxis voor een streaming-taak bewerken, of om aan te passen van invoer en uitvoer, moet de taak stoppen om de wijzigingen aanbrengen en het ontwerp van de taak een upgrade uitvoert. In dergelijke scenario's wanneer een gebruiker de streaming-taak gestopt en opnieuw starten is de recovery-scenario vergelijkbaar met service-upgrade. 

Controlepuntgegevens kan niet worden gebruikt voor een door de gebruiker geïnitieerde taak opnieuw starten. Voor een schatting van de vertraging van uitvoer tijdens het opnieuw opstarten, gebruikt u dezelfde procedure zoals beschreven in de vorige sectie en vergelijkbare risicobeperking van toepassing als de vertraging te lang is.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over de betrouwbaarheid en schaalbaarheid:
- [Zelfstudie: Waarschuwingen instellen voor Azure Stream Analytics-taken](stream-analytics-set-up-alerts.md)
- [Een Azure Stream Analytics-taak voor een betere doorvoer schalen](stream-analytics-scale-jobs.md)
- [Betrouwbaarheid van de Stream Analytics-taken te garanderen tijdens de service-updates](stream-analytics-job-reliability.md)
