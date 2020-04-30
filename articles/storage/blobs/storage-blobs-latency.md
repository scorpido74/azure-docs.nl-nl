---
title: Latentie in Blob Storage-Azure Storage
description: Begrijp en meet latentie voor Blob Storage-bewerkingen en leer hoe u uw Blob Storage-toepassingen kunt ontwerpen voor een lage latentie.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75749725"
---
# <a name="latency-in-blob-storage"></a>Latentie in Blob-opslag

Een latentie, soms waarnaar wordt verwezen als reactie tijd, is de hoeveelheid tijd die een toepassing moet wachten voordat een aanvraag is voltooid. Latentie kan rechtstreeks van invloed zijn op de prestaties van een toepassing. Lage latentie is vaak belang rijk voor scenario's met mensen in de lus, zoals het uitvoeren van creditcard transacties of het laden van webpagina's. Systemen die binnenkomende gebeurtenissen met hoge tarieven moeten verwerken, zoals telemetrie logboek registratie of IoT-gebeurtenissen, vereisen ook een lage latentie. In dit artikel wordt beschreven hoe u de latentie kunt begrijpen en meten voor bewerkingen op blok-blobs en hoe u uw toepassingen kunt ontwerpen voor een lage latentie.

Azure Storage biedt twee verschillende prestatie opties voor blok-blobs: Premium en Standard. Premium blok-blobs bieden aanzienlijk minder en consistente latentie dan standaard blok-blobs via hoogwaardige SSD-schijven. Zie voor meer informatie **Premium-prestaties blok-blobopslag** in [Azure Blob-opslag: dynamische, coole en archief toegangs lagen](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>Over Azure Storage latentie

Azure Storage latentie is gerelateerd aan aanvraag tarieven voor Azure Storage bewerkingen. Aanvraag tarieven worden ook wel invoer/uitvoer-bewerkingen per seconde (IOPS) genoemd.

Als u de aanvraag frequentie wilt berekenen, bepaalt u eerst de duur van de voltooiing van elke aanvraag en vervolgens berekent u hoeveel aanvragen per seconde kunnen worden verwerkt. Stel bijvoorbeeld dat een aanvraag 50 milliseconden (MS) moet worden voltooid. Een toepassing die één thread met een openstaande Lees-of schrijf bewerking gebruikt, moet 20 IOPS (1 seconde of 1000 MS/50 MS per aanvraag) bezorgen. Theoretisch, als het aantal threads is verdubbeld naar twee, dan moet de toepassing 40 IOPS kunnen bezorgen. Als de openstaande asynchrone Lees-of schrijf bewerkingen voor elke thread worden verdubbeld naar twee, moet de toepassing 80 IOPS kunnen belopen.

In de praktijk worden de aanvraag tarieven niet altijd lineair geschaald als gevolg van de overhead van taken in de client van taak planning, het omschakelen van de context, enzovoort. Aan de kant van de service kan er sprake zijn van een latentie als gevolg van de druk op het Azure Storage systeem, verschillen in de gebruikte opslag media, lawaai van andere werk belastingen, onderhouds taken en andere factoren. Ten slotte kan de netwerk verbinding tussen de client en de server van invloed zijn op Azure Storage latentie als gevolg van congestie, route ring of andere onderbrekingen.

Azure Storage band breedte, ook wel door Voer genoemd, is gerelateerd aan de aanvraag snelheid en kan worden berekend door de aanvraag frequentie (IOPS) te vermenigvuldigen met de aanvraag grootte. Als er bijvoorbeeld 160 aanvragen per seconde worden uitgegaan, resulteert elk 256 KiB van gegevens in de door Voer van 40.960 KiB per seconde of een 40 MiB per seconde.

## <a name="latency-metrics-for-block-blobs"></a>Metrische latentie gegevens voor blok-blobs

Azure Storage biedt twee latentie-metrische gegevens voor blok-blobs. Deze metrische gegevens kunnen worden weer gegeven in de Azure Portal:

- **End-to-end (E2E) latentie** meet het interval van wanneer Azure Storage het eerste pakket van de aanvraag ontvangt, totdat Azure Storage een bevestiging van de client ontvangt op het laatste pakket van de reactie.

- **Server latentie** meet het interval van wanneer Azure Storage het laatste pakket van de aanvraag ontvangt, totdat het eerste pakket van de reactie wordt geretourneerd door Azure Storage.

De volgende afbeelding toont de **gemiddelde E2E-latentie** en de **gemiddelde geslaagde server latentie** voor een voorbeeld workload die `Get Blob` de bewerking aanroept:

![Scherm afbeelding met metrische gegevens over latentie voor Get BLOB-bewerking](media/storage-blobs-latency/latency-metrics-get-blob.png)

Onder normale omstandigheden bevindt zich weinig tussen ruimte tussen end-to-end latentie en server latentie. Dit is de afbeelding die wordt weer gegeven voor de voor beeld-workload.

Als u de metrische gegevens van uw end-to-end-en server latentie controleert, en u merkt dat de end-to-end-latentie aanzienlijk hoger is dan de latentie van de server, onderzoekt en verhelpt u de bron van de extra latentie.

Als uw end-to-end-en server latentie vergelijkbaar zijn, maar u een lagere latentie nodig hebt, kunt u overwegen om te migreren naar Premium Block Blob Storage.

## <a name="factors-influencing-latency"></a>Factoren die van invloed zijn op de latentie

De grootte van de hoofd factor heeft invloed op de bewerking. Het duurt langer om grotere bewerkingen te volt ooien, omdat de hoeveelheid gegevens die via het netwerk worden overgedragen, wordt verwerkt door Azure Storage.

Het volgende diagram toont de totale tijd voor bewerkingen van verschillende grootten. Voor kleine hoeveel heden gegevens is het latentie-interval een hoofd zakelijk besteed aan het verwerken van de aanvraag, in plaats van gegevens over te dragen. Het latentie-interval neemt alleen enigszins toe als de grootte van de bewerking toeneemt (gemarkeerd 1 in het onderstaande diagram). Naarmate de grootte van de bewerking verder toeneemt, wordt er meer tijd besteed aan het overdragen van gegevens, zodat het totale latentie-interval wordt gesplitst tussen de verwerking van aanvragen en gegevens overdracht (gemarkeerde 2 in het diagram hieronder). Met grotere bewerkings grootten wordt het latentie-interval bijna alleen besteed aan het overdragen van gegevens en is de verwerking van aanvragen in het onderstaande diagram grotendeels onbeduidend (gemarkeerd met 3).

![Scherm opname van de totale bewerkings tijd per bewerkings grootte](media/storage-blobs-latency/operation-time-size-chart.png)

Client configuratie factoren zoals gelijktijdigheid en threading beïnvloeden ook de latentie. De totale door Voer is afhankelijk van het aantal opslag aanvragen dat zich op een bepaald moment in de vlucht bevindt en over hoe uw toepassing threading verwerkt. Client bronnen met inbegrip van CPU, geheugen, lokale opslag en netwerk interfaces kunnen ook van invloed zijn op latentie.

De verwerking van Azure Storage aanvragen vereist CPU-en geheugen bronnen van de client. Als de client onder druk staat vanwege een onderbelaste virtuele machine of een onbuitensporig proces in het systeem, zijn er minder resources beschikbaar voor het verwerken van Azure Storage aanvragen. Conflicten of het ontbreken van client bronnen leiden tot een toename van de end-to-end latentie zonder toename van de server latentie, waardoor de kloof tussen de twee metrische gegevens wordt verhoogd.

Net zo belang rijk is de netwerk interface en netwerk-pipe tussen de client en Azure Storage. Alleen fysieke afstanden kunnen een aanzienlijke factor zijn, bijvoorbeeld als een virtuele machine van de client zich in een andere Azure-regio of on-premises bevindt. Andere factoren, zoals netwerk-hops, ISP-route ring en Internet status, kunnen invloed hebben op de algehele opslag latentie.

Als u de latentie wilt beoordelen, stelt u eerst de metrische gegevens voor de basis lijn in voor uw scenario. Met metrische basislijn gegevens krijgt u de verwachte end-to-end-en server latentie in de context van uw toepassings omgeving, afhankelijk van uw werkbelasting profiel, toepassings configuratie-instellingen, client bronnen, netwerk-pipe en andere factoren. Wanneer u metrische gegevens voor de basis lijn hebt, kunt u op eenvoudige wijze abnormale en normale omstandigheden identificeren. Met metrische gegevens van basis lijn kunt u de gevolgen van gewijzigde para meters, zoals toepassings configuratie of VM-grootten, observeren.

## <a name="next-steps"></a>Volgende stappen

- [Schaalbaarheids-en prestatie doelen voor Blob Storage](scalability-targets.md)
- [Controle lijst voor prestaties en schaal baarheid voor Blob Storage](storage-performance-checklist.md)
