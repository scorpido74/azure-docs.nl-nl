---
title: Azure Data Lake Storage-queryversnelling (voorbeeld)
description: Queryversnelling (preview) is een nieuwe mogelijkheid voor Azure Data Lake Storage waarmee toepassingen en analyseframeworks de gegevensverwerking drastisch kunnen optimaliseren door alleen de gegevens op te halen die nodig zijn voor een verwerkingsbewerking.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 191a3280075403c8c5b57c5ffca1c7707d1ddb11
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771818"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Azure Data Lake Storage-queryversnelling (voorbeeld)

Queryversnelling (preview) is een nieuwe mogelijkheid voor Azure Data Lake Storage waarmee toepassingen en analyseframeworks de gegevensverwerking drastisch kunnen optimaliseren door alleen de gegevens op te halen die ze nodig hebben om een bepaalde bewerking uit te voeren. Dit vermindert de tijd en verwerkingskracht die nodig is om kritisch inzicht te krijgen in opgeslagen gegevens.

> [!NOTE]
> De functie queryversnelling bevindt zich in een openbare preview en is beschikbaar in de regio's Canada Centraal en France Central. Zie het artikel [Bekende problemen](data-lake-storage-known-issues.md) om beperkingen te bekijken. Zie [dit formulier](https://aka.ms/adls/qa-preview-signup)om u in te schrijven voor het voorbeeld.  

## <a name="overview"></a>Overzicht

Queryversnelling accepteert *filteringspredicaten* en *kolomprojecties* waarmee toepassingen rijen en kolommen kunnen filteren op het moment dat gegevens van de schijf worden gelezen. Alleen de gegevens die voldoen aan de voorwaarden van een predicaat worden via het netwerk naar de toepassing overgebracht. Dit vermindert de netwerklatentie en rekenkosten.  

U SQL gebruiken om de rijfilterpredicaten en kolomprojecties op te geven in een queryversnellingsaanvraag. Een aanvraag verwerkt slechts één bestand. Geavanceerde relationele functies van SQL, zoals joins en groeperen op aggregaten, worden daarom niet ondersteund. Queryversnelling ondersteunt CSV- en JSON-opgemaakte gegevens als invoer voor elke aanvraag.

De functie queryversnelling is niet beperkt tot Data Lake Storage (opslagaccounts waarop de hiërarchische naamruimte is ingeschakeld). Queryversnelling is volledig compatibel met de blobs in opslagaccounts waarop **geen** hiërarchische naamruimte is ingeschakeld. Dit betekent dat u dezelfde vermindering van de netwerklatentie- en rekenkosten bereiken wanneer u gegevens verwerkt die u al als blobs in opslagaccounts hebt opgeslagen.

Zie [Gegevens filteren met Azure Data Lake Storage-queryversnelling](data-lake-storage-query-acceleration-how-to.md)voor een voorbeeld van het gebruik van queryversnelling in een clienttoepassing.

## <a name="data-flow"></a>Gegevensstroom

In het volgende diagram ziet u hoe een typische toepassing queryversnelling gebruikt om gegevens te verwerken.

> [!div class="mx-imgBorder"]
> ![Overzicht van queryversnelling](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. De clienttoepassing vraagt bestandsgegevens aan door predicaten en kolomprojecties op te geven.

2. Queryversnelling onteert de opgegeven SQL-query en distribueert werk naar ontwende- en filtergegevens.

3. Processors lezen de gegevens van de schijf, ontleden de gegevens met behulp van de juiste indeling en filteren vervolgens gegevens door de opgegeven predicaten en kolomprojecties toe te passen.

4. Queryversnelling combineert de antwoordshards om terug te streamen naar clienttoepassing.

5. De clienttoepassing ontvangt en ontneemt de gestreamde reactie. De toepassing hoeft geen extra gegevens te filteren en kan de gewenste berekening of transformatie direct toepassen.

## <a name="better-performance-at-a-lower-cost"></a>Betere prestaties tegen lagere kosten

Queryversnelling optimaliseert de prestaties door de hoeveelheid gegevens die door uw toepassing wordt overgedragen en verwerkt, te verminderen.

Als u een geaggregeerde waarde wilt berekenen, halen toepassingen vaak **alle** gegevens uit een bestand op en verwerken en filteren ze de gegevens lokaal. Uit een analyse van de invoer-/uitvoerpatronen voor analytics-workloads blijkt dat toepassingen doorgaans slechts 20% van de gegevens die ze lezen nodig hebben om een bepaalde berekening uit te voeren. Deze statistiek geldt zelfs na het toepassen van technieken zoals [partitie snoeien](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). Dit betekent dat 80% van die gegevens onnodig wordt overgedragen over het netwerk, ontleed en gefilterd door toepassingen. Dit patroon, in wezen ontworpen om onnodige gegevens te verwijderen, brengt aanzienlijke rekenkosten met zich mee.  

Hoewel Azure beschikt over een toonaangevend netwerk, in termen van zowel doorvoer als latentie, is het onnodig overdragen van gegevens over dat netwerk nog steeds kostbaar voor de prestaties van toepassingen. Door de ongewenste gegevens tijdens de opslagaanvraag uit te filteren, elimineert queryversnelling deze kosten.

Bovendien vereist de CPU-belasting die nodig is om onnodige gegevens te ontleden en te filteren, dat uw toepassing een groter aantal en grotere VM's indient om zijn werk te kunnen doen. Door deze rekenbelasting over te zetten naar queryversnelling, kunnen toepassingen aanzienlijke kostenbesparingen realiseren.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Toepassingen die kunnen profiteren van queryversnelling

Queryversnelling is ontworpen voor gedistribueerde analyseframeworks en gegevensverwerkingstoepassingen. 

Gedistribueerde analyseframeworks zoals Apache Spark en Apache Hive bevatten een opslagabstractielaag binnen het framework. Deze engines bevatten ook queryoptimizers die kennis kunnen opnemen van de mogelijkheden van de onderliggende I/O-service bij het bepalen van een optimaal queryplan voor gebruikersquery's. Deze frameworks beginnen queryversnelling te integreren. Als gevolg hiervan krijgen gebruikers van deze frameworks een verbeterde latentie van query's en lagere totale eigendomskosten zonder dat ze wijzigingen in de query's hoeven aan te brengen. 

Queryversnelling is ook ontworpen voor gegevensverwerkingstoepassingen. Dit soort toepassingen voert doorgaans grootschalige gegevenstransformaties uit die mogelijk niet direct leiden tot analyse-inzichten, zodat ze niet altijd gevestigde gedistribueerde analyseframeworks gebruiken. Deze toepassingen hebben vaak een directere relatie met de onderliggende opslagservice, zodat ze direct kunnen profiteren van functies zoals queryversnelling. 

Zie [Gegevens filteren met Azure Data Lake Storage-queryversnelling](data-lake-storage-query-acceleration-how-to.md)voor een voorbeeld van hoe een toepassing queryversnelling kan integreren.

## <a name="pricing"></a>Prijzen

Vanwege de verhoogde rekenbelasting binnen de Azure Data Lake Storage-service verschilt het prijsmodel voor het gebruik van queryversnelling van het normale Azure Data Lake Storage-transactiemodel. Queryversnelling brengt kosten met zich mee voor de hoeveelheid gescande gegevens en kosten voor de hoeveelheid gegevens die aan de beller wordt geretourneerd.

Ondanks de wijziging in het factureringsmodel is het prijsmodel van Query versnelling ontworpen om de totale eigendomskosten voor een werkbelasting te verlagen, gezien de vermindering van de veel duurdere VM-kosten.

## <a name="next-steps"></a>Volgende stappen

- [Inschrijvingsformulier voor queryversnelling](https://aka.ms/adls/qa-preview-signup)    
- [Gegevens filteren met azure data lake storage-queryversnelling](data-lake-storage-query-acceleration-how-to.md)
- [SQL-taalverwijzing voor queryversnelling (voorbeeld)](query-acceleration-sql-reference.md)
- QueryversnellingRESTAPI-verwijzing



