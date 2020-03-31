---
title: Herstelconcepten voor controlepunten en opnieuw afspelen in Azure Stream Analytics
description: In dit artikel worden controlepunten beschreven en taakherstelconcepten opnieuw afspelen in Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f5bb2b97d7da770828c2f4f03167483ad2044c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426394"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Controlepunt- en replayconcepten in Azure Stream Analytics-taken
In dit artikel worden de interne controlepunten- en replayconcepten in Azure Stream Analytics beschreven en de impact die zijn op het herstel van de taak. Elke keer dat een Stream Analytics-taak wordt uitgevoerd, wordt de statusinformatie intern bijgehouden. Die statusinformatie wordt periodiek opgeslagen in een controlepunt. In sommige scenario's wordt de controlepuntgegevens gebruikt voor taakherstel als er een taakfout of upgrade optreedt. In andere omstandigheden kan het controlepunt niet worden gebruikt voor herstel en is een herhaling noodzakelijk.

## <a name="stateful-query-logicin-temporal-elements"></a>Stateful querylogica in tijdelijke elementen
Een van de unieke mogelijkheden van azure stream analytics-taak is het uitvoeren van stateful processing, zoals gevensterde aggregaten, tijdelijke joins en tijdelijke analytische functies. Elk van deze operatoren houdt staatsinformatie bij wanneer de taak wordt uitgevoerd.De maximale venstergrootte voor deze query-elementen is zeven dagen. 

Het tijdelijke vensterconcept wordt weergegeven in verschillende query-elementen van Stream Analytics:
1. Windowed aggregaten (GROUP BY of Tumbling, Hopping, and Sliding windows)

2. Tijdelijke joins (JOIN met DATEDIFF)

3. Tijdelijke analytische functies (ISFIRST, LAST en LAG met LIMIETDUUR)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Taakherstel van knooppuntfout, inclusief os-upgrade
Elke keer dat een Stream Analytics-taak wordt uitgevoerd, wordt deze intern geschaald om werk te doen tussen meerdere werknemersknooppunten. De status van elk werknemersknooppunt wordt om de paar minuten gecontroleerd, waardoor het systeem kan herstellen als er een storing optreedt.

Soms kan een bepaald werknemersknooppunt mislukken of kan er een upgrade van het besturingssysteem optreden voor dat werknemersknooppunt. Als u automatisch wilt herstellen, krijgt Stream Analytics een nieuw gezond knooppunt en wordt de status van het vorige werknemersknooppunt hersteld van het laatst beschikbare controlepunt. Om het werk te hervatten, is een kleine hoeveelheid herhaling nodig om de status te herstellen vanaf het moment dat het controlepunt wordt genomen. Meestal is de herstelkloof slechts een paar minuten. Wanneer er voldoende streaming-eenheden zijn geselecteerd voor de taak, moet de herhaling snel worden voltooid. 

In een volledig parallelle query is de tijd die nodig is om in te halen nadat een fout van een werknemersknooppunt evenredig is met:

[de invoergebeurtenissnelheid] x [de tussenlengte] / [aantal verwerkingspartities]

Als u ooit aanzienlijke verwerkingsvertraging waarneemt als gevolg van een knooppuntfout en een upgrade van het besturingssysteem, u overwegen de query volledig parallel te maken en de taak te schalen om meer streamingeenheden toe te wijzen. Zie [Een azure stream analytics-taak schalen om de doorvoer te verhogen](stream-analytics-scale-jobs.md)voor meer informatie.

Current Stream Analytics toont geen rapport wanneer dit soort herstelproces plaatsvindt.

## <a name="job-recovery-from-a-service-upgrade"></a>Taakherstel van een service-upgrade 
Microsoft heeft af en toe een upgrade uitgevoerd van de binaire bestanden die de Stream Analytics-taken in de Azure-service uitvoeren. Op deze momenten worden de actieve taken van gebruikers geüpgraded naar een nieuwere versie en wordt de taak automatisch opnieuw gestart. 

Momenteel blijft de indeling herstelcontrolepunt niet behouden tussen upgrades. Als gevolg hiervan moet de status van de streamingquery volledig worden hersteld met behulp van replay-techniek. Om stream analytics-taken in staat te stellen exact dezelfde invoer van voorheen opnieuw af te spelen, is het belangrijk om het bewaarbeleid voor de brongegevens in te stellen op ten minste de venstergroottes in uw query. Als u dit niet doet, kan dit leiden tot onjuiste of gedeeltelijke resultaten tijdens de service-upgrade, omdat de brongegevens mogelijk niet ver genoeg worden bewaard om de volledige venstergrootte op te nemen.

Over het algemeen is de benodigde hoeveelheid herhaling evenredig met de grootte van het venster vermenigvuldigd met de gemiddelde gebeurtenissnelheid. Als voorbeeld, voor een taak met een invoersnelheid van 1000 gebeurtenissen per seconde, wordt een venstergrootte van meer dan een uur beschouwd als een grote replaygrootte. Mogelijk moet maximaal een uur aan gegevens opnieuw worden verwerkt om de status te initialiseren, zodat deze volledige en correcte resultaten kan opleveren, wat een langere periode vertraging (geen uitvoer) kan veroorzaken. Query's zonder vensters of andere `JOIN` `LAG`tijdelijke operatoren, zoals of , zou hebben nul replay.

## <a name="estimate-replay-catch-up-time"></a>Schat de inhaaltijd voor replay
Als u de lengte van de vertraging als gevolg van een service-upgrade wilt schatten, u deze techniek volgen:

1. Laad de invoergebeurtenishub met voldoende gegevens om de grootste venstergrootte in uw query te dekken, tegen de verwachte gebeurtenissnelheid. De tijdstempel van de gebeurtenissen moet gedurende die periode dicht bij de kloktijd van de muur zijn, alsof het een live invoerfeed is. Als u bijvoorbeeld een periode van drie dagen in uw query hebt, stuurt u gebeurtenissen drie dagen naar gebeurtenishub en blijft u gebeurtenissen verzenden. 

2. Start de taak met **Nu** als begintijd. 

3. Meet de tijd tussen de begintijd en wanneer de eerste uitvoer wordt gegenereerd. De tijd is ruw hoeveel vertraging de taak zou oplopen tijdens een service-upgrade.

4. Als de vertraging te lang is, probeert u uw taak te verdelen en het aantal SU's te verhogen, zodat de belasting wordt verspreid naar meer knooppunten. U ook overwegen de venstergroottes in uw query te verkleinen en verdere aggregatie of andere stateful processing uit te voeren op de uitvoer die wordt geproduceerd door de streamanalytics-taak in de downstream-sink (bijvoorbeeld met Azure SQL-database).

Voor algemene servicestabiliteitsproblemen tijdens de upgrade van bedrijfskritieke taken u overwegen dubbele taken uit te voeren in gekoppelde Azure-regio's. Zie [Betrouwbaarheid van de betrouwbaarheid van de functie Garantie Stream Analytics tijdens serviceupdates](stream-analytics-job-reliability.md)voor meer informatie.

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Taakherstel van een door een gebruiker geïnitieerde stop en start
Als u de syntaxis query voor een streamingtaak wilt bewerken of om invoer en uitvoer aan te passen, moet de taak worden gestopt om de wijzigingen aan te brengen en het taakontwerp te upgraden. In dergelijke scenario's, wanneer een gebruiker stopt met de streaming baan, en start het opnieuw, het herstelscenario is vergelijkbaar met service-upgrade. 

Controlepuntgegevens kunnen niet worden gebruikt voor het opnieuw opstarten van een door de gebruiker gestarte taak. Als u de vertraging van de productie tijdens een dergelijke herstart wilt schatten, gebruikt u dezelfde procedure als in het vorige gedeelte en past u een vergelijkbare beperking toe als de vertraging te lang is.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over betrouwbaarheid en schaalbaarheid:
- [Zelfstudie: Waarschuwingen instellen voor Azure Stream Analytics-taken](stream-analytics-set-up-alerts.md)
- [Een Azure Stream Analytics-taak schalen om de doorvoer te verhogen](stream-analytics-scale-jobs.md)
- [Garandeer de betrouwbaarheid van de taak van Stream Analytics tijdens service-updates](stream-analytics-job-reliability.md)
