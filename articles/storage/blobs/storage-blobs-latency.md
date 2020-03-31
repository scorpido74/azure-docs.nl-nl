---
title: Latentie in Blob-opslag - Azure Storage
description: Begrijp en meet latentie voor Blob-opslagbewerkingen en leer hoe u uw Blob-opslagtoepassingen ontwerpen voor lage latentie.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749725"
---
# <a name="latency-in-blob-storage"></a>Latentie in Blob-opslag

Latentie, soms aangeduid als responstijd, is de hoeveelheid tijd die een toepassing moet wachten tot een aanvraag is voltooid. Latentie kan direct van invloed zijn op de prestaties van een toepassing. Lage latentie is vaak belangrijk voor scenario's met mensen op de lus, zoals het uitvoeren van creditcardtransacties of het laden van webpagina's. Systemen die binnenkomende gebeurtenissen met hoge snelheden moeten verwerken, zoals telemetrielogboekregistratie of IoT-gebeurtenissen, vereisen ook een lage latentie. In dit artikel wordt beschreven hoe u latentie begrijpt en meet voor bewerkingen op blokblobs en hoe u uw toepassingen ontwerpen voor lage latentie.

Azure Storage biedt twee verschillende prestatieopties voor blokblobs: premium en standaard. Premium block blobs bieden aanzienlijk lagere en consistentere latentie dan standaard blokblobs via krachtige SSD-schijven. Zie **Premium performance block blob-opslag** in Azure [Blob-opslag: hot-, cool- en archieftoegangslagen](storage-blob-storage-tiers.md)voor meer informatie.

## <a name="about-azure-storage-latency"></a>Over azure-opslaglatentie

De latentie van Azure Storage is gerelateerd aan aanvraagsnelheden voor Azure Storage-bewerkingen. Aanvraagsnelheden worden ook wel input/output operations per seconde (IOPS) genoemd.

Als u de aanvraagsnelheid wilt berekenen, bepaalt u eerst de tijd die elke aanvraag nodig heeft om te voltooien en berekent u vervolgens hoeveel aanvragen per seconde kunnen worden verwerkt. Stel dat een aanvraag 50 milliseconden (ms) in beslag neemt. Een toepassing met behulp van een draad met een uitstekende lees-of schrijfbewerking moet bereiken 20 IOPS (1 seconde of 1000 ms / 50 ms per aanvraag). Theoretisch, als het aantal draad wordt verdubbeld tot twee, dan moet de toepassing in staat zijn om 40 IOPS te bereiken. Als de uitstaande asynchrone lees- of schrijfbewerkingen voor elke thread worden verdubbeld tot twee, moet de toepassing 80 IOPS kunnen bereiken.

In de praktijk schalen aanvraagsnelheden niet altijd zo lineair, vanwege overhead in de client van taakplanning, contextswitching, enzovoort. Aan de servicezijde kan er variabiliteit in latentie zijn als gevolg van druk op het Azure Storage-systeem, verschillen in de gebruikte opslagmedia, ruis van andere workloads, onderhoudstaken en andere factoren. Ten slotte kan de netwerkverbinding tussen de client en de server van invloed zijn op de latentie van Azure Storage als gevolg van congestie, omleiding of andere onderbrekingen.

Azure Storage-bandbreedte, ook wel doorvoer genoemd, is gerelateerd aan de aanvraagsnelheid en kan worden berekend door de aanvraagsnelheid (IOPS) te vermenigvuldigen met de aanvraaggrootte. Bijvoorbeeld, uitgaande van 160 aanvragen per seconde, elke 256 KiB van de gegevens resulteert in doorvoer van 40.960 KiB per seconde of 40 MiB per seconde.

## <a name="latency-metrics-for-block-blobs"></a>Latentiestatistieken voor blokblobs

Azure Storage biedt twee latentiestatistieken voor blokblobs. Deze statistieken kunnen worden weergegeven in de Azure-portal:

- **End-to-end (E2E) latentie** meet het interval vanaf het moment dat Azure Storage het eerste pakket van de aanvraag ontvangt totdat Azure Storage een clientbevestiging ontvangt op het laatste pakket van het antwoord.

- **Serverlatentie** meet het interval vanaf het moment dat Azure Storage het laatste pakket van de aanvraag ontvangt totdat het eerste pakket van het antwoord is geretourneerd vanuit Azure Storage.

In de volgende afbeelding ziet u de **gemiddelde e2e-latentie** en `Get Blob` de gemiddelde **successerverlatentie** voor een voorbeeldworkload die de bewerking aanroept:

![Schermafbeelding van latentiestatistieken voor Blob-bewerking](media/storage-blobs-latency/latency-metrics-get-blob.png)

Onder normale omstandigheden is er weinig kloof tussen end-to-end latentie en serverlatentie, wat de afbeelding laat zien voor de voorbeeldworkload.

Als u uw end-to-end- en serverlatentiestatistieken bekijkt en merkt dat de end-to-end latentie aanzienlijk hoger is dan de latentie van de server, onderzoekt en identificeert u de bron van de extra latentie.

Als uw end-to-end- en serverlatentie vergelijkbaar zijn, maar u een lagere latentie nodig hebt, u overwegen te migreren naar premium block blob-opslag.

## <a name="factors-influencing-latency"></a>Factoren die de latentie beïnvloeden

De belangrijkste factor die de latentie beïnvloedt, is de bedrijfsgrootte. Het duurt langer om grotere bewerkingen te voltooien, vanwege de hoeveelheid gegevens die via het netwerk wordt overgedragen en door Azure Storage worden verwerkt.

In het volgende diagram ziet u de totale tijd voor bewerkingen van verschillende groottes. Voor kleine hoeveelheden gegevens wordt het latentie-interval voornamelijk besteed aan het verwerken van de aanvraag, in plaats van het overdragen van gegevens. Het latentie-interval neemt slechts licht toe naarmate de bedrijfsgrootte toeneemt (gemarkeerd 1 in het onderstaande diagram). Naarmate de grootte van de bewerking verder toeneemt, wordt meer tijd besteed aan het overbrengen van gegevens, zodat het totale latentie-interval wordt verdeeld tussen de verwerking van aanvragen en gegevensoverdracht (gemarkeerd 2 in het onderstaande diagram). Bij grotere bewerkingsgroottes wordt het latentie-interval bijna uitsluitend besteed aan het overbrengen van gegevens en is de aanvraagafhandeling grotendeels onbeduidend (gemarkeerd 3 in het onderstaande diagram).

![Schermafbeelding van de totale bewerkingstijd op grootte van de bewerking](media/storage-blobs-latency/operation-time-size-chart.png)

Clientconfiguratiefactoren zoals gelijktijdigheid en threading hebben ook invloed op latentie. De totale doorvoer is afhankelijk van het aantal opslagaanvragen dat op een bepaald moment in de vlucht is en van hoe uw toepassing met threading omgaat. Clientbronnen, waaronder CPU, geheugen, lokale opslag en netwerkinterfaces, kunnen ook van invloed zijn op de latentie.

Voor het verwerken van Azure Storage-aanvragen zijn client-CPU- en geheugenbronnen vereist. Als de client onder druk staat als gevolg van een ondermaatse virtuele machine of een op hol geslagen proces in het systeem, zijn er minder resources beschikbaar om Azure Storage-aanvragen te verwerken. Elke geschil of gebrek aan clientresources zal resulteren in een toename van de end-to-end latentie zonder een toename van de serverlatentie, waardoor de kloof tussen de twee statistieken toeneemt.

Even belangrijk is de netwerkinterface en netwerkleiding tussen de client en Azure Storage. Fysieke afstand alleen kan een belangrijke factor zijn, bijvoorbeeld als een client-VM zich in een andere Azure-regio of on-premises bevindt. Andere factoren zoals netwerkhop, ISP-routering en internetstatus kunnen de algehele opslaglatentie beïnvloeden.

Als u de latentie wilt beoordelen, stelt u eerst basislijnstatistieken voor uw scenario vast. Basislijnstatistieken bieden u de verwachte end-to-end- en serverlatentie in de context van uw toepassingsomgeving, afhankelijk van uw werkbelastingprofiel, toepassingsconfiguratie-instellingen, clientbronnen, netwerkpipe en andere factoren. Wanneer u basislijnstatistieken hebt, u gemakkelijker abnormale versus normale omstandigheden identificeren. Met basislijnstatistieken u ook de effecten van gewijzigde parameters waarnemen, zoals toepassingsconfiguratie of VM-grootten.

## <a name="next-steps"></a>Volgende stappen

- [Schaalbaarheid en prestatiedoelen voor Blob-opslag](scalability-targets.md)
- [Checklist voor prestaties en schaalbaarheid voor Blob-opslag](storage-performance-checklist.md)
