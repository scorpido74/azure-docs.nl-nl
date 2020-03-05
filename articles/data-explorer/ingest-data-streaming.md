---
title: Stroom opname gebruiken om gegevens op te nemen in azure Data Explorer
description: Meer informatie over het opnemen (laden) van gegevens in azure Data Explorer met behulp van streaming-opname.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 279130fa310b107bd1a016c717c48af3d905251b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270145"
---
# <a name="streaming-ingestion-preview"></a>Stroom opname (preview-versie)

Het opnemen van streaming is gericht op scenario's die een lage latentie vereisen met een opname tijd van minder dan 10 seconden voor verschillende volume gegevens. Het wordt gebruikt om de operationele verwerking van veel tabellen in een of meer data bases te optimaliseren, waarbij de gegevens stroom in elke tabel relatief klein is (weinig records per seconde), maar het totale gegevens opname volume hoog is (duizenden records per seconde).

Gebruik de klassieke opname in (bulk) in plaats van het opnemen van opname wanneer de hoeveelheid gegevens groter wordt dan 1 MB per seconde per tabel. Lees [overzicht van gegevens opname](/azure/data-explorer/ingest-data-overview) voor meer informatie over de verschillende methoden voor opname.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* Meld u aan bij de [webgebruikersinterface](https://dataexplorer.azure.com/).
* [Een Azure Data Explorer-cluster en-data base](create-cluster-database-portal.md) maken

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Opname van streaming op uw cluster inschakelen

> [!WARNING]
> Controleer de [beperkingen](#limitations) voordat u het opnemen van de opname van stoom inschakelt.

1. Ga in het Azure Portal naar uw Azure Data Explorer-cluster. Selecteer **configuraties**in **instellingen**. 
1. Selecteer in het deel venster **configuraties** om opnemen **van** **streaming**in te scha kelen.
1. Selecteer **Opslaan**.
 
    ![opname van streaming op](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. Definieer in de [webgebruikersinterface](https://dataexplorer.azure.com/)het [beleid](/azure/kusto/management/streamingingestionpolicy) voor het opnemen van gegevens stromen op tabel (len) of data base (s) die streaminggegevens zullen ontvangen. 

    > [!NOTE]
    > * Als het beleid is gedefinieerd op database niveau, worden alle tabellen in de data base ingeschakeld voor streaming-opname.
    > * Het toegepaste beleid kan verwijzen naar alleen nieuwe opgenomen gegevens en niet naar andere tabellen in de data base.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Stroom opname gebruiken om gegevens op te nemen in uw cluster

Er zijn twee ondersteunde typen streaming-opname:


* [**Event hub**](/azure/data-explorer/ingest-data-event-hub) gebruikt als gegevens bron
* **Aangepaste opname** vereist dat u een toepassing schrijft die gebruikmaakt van een van de Azure Data Explorer-client bibliotheken. Zie voor [beeld van streaming opname](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) voor een voorbeeld toepassing.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Kies het juiste type stroom opname

|   |Event Hub  |Aangepaste opname  |
|---------|---------|---------|
|Gegevens vertraging tussen het initiëren van de opname en de beschik bare gegevens voor de query   |    langere vertraging     |   kortere vertraging      |
|Ontwikkelings overhead    |   snelle en eenvoudige installatie, geen ontwikkelings overhead    |   hoge ontwikkelings overhead voor toepassingen voor het afhandelen van fouten en het garanderen van gegevens consistentie     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Opname van streaming op uw cluster uitschakelen

> [!WARNING]
> Het uitschakelen van streaming-opname kan enkele uren duren.

1. Verwijder het beleid voor het opnemen van [streaming](/azure/kusto/management/streamingingestionpolicy) van alle relevante tabellen en data bases. Bij het verwijderen van het beleid voor het opnemen van inslikken wordt de gegevens verplaatsing van de eerste opslag gestreamd naar de permanente opslag in het kolom archief (afmetingen of Shards). De verplaatsing van gegevens kan een paar seconden tot enkele uren duren, afhankelijk van de hoeveelheid gegevens in de eerste opslag en hoe de CPU en het geheugen door het cluster wordt gebruikt.
1. Ga in het Azure Portal naar uw Azure Data Explorer-cluster. Selecteer **configuraties**in **instellingen**. 
1. Selecteer in het deel venster configuraties **uit** om het opnemen van **streaming**uit te scha kelen.
1. Selecteer **Opslaan**.

    ![Stroom inslikken uit](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Beperkingen

* Het streamen van opname prestaties en capaciteits schaal met verhoogde VM-en cluster groottes. Gelijktijdige opname is beperkt tot zes opname per kern. Bijvoorbeeld: voor 16 kern Sku's, zoals D14 en L16, is de Maxi maal ondersteunde belasting 96 gelijktijdige opname. Voor twee kern-Sku's, zoals D11, is de Maxi maal ondersteunde belasting 12 gelijktijdige opname.
* De limiet voor de gegevens grootte per opname aanvraag is 4 MB.
* Schema-updates, zoals het maken en wijzigen van tabellen en opname toewijzingen, kunnen tot vijf minuten duren voor de service streaming-opname.
* Het inschakelen van streaming-opname in een cluster, zelfs wanneer er geen gegevens worden opgenomen via streaming, maakt gebruik van de lokale SSD-schijf van de cluster machines voor het streamen van opname gegevens en vermindert de opslag ruimte die beschikbaar is voor warme cache.
* Er kunnen geen [gebieds Tags](/azure/kusto/management/extents-overview#extent-tagging) worden ingesteld voor de gegevens van de streaming-opname.

Streaming-opname ondersteunt de volgende functies niet:
* [Database cursors](/azure/kusto/management/databasecursor).
* [Gegevens toewijzing](/azure/kusto/management/mappings). Alleen [vooraf gemaakte](/azure/kusto/management/create-ingestion-mapping-command) gegevens toewijzing wordt ondersteund. 

## <a name="next-steps"></a>Volgende stappen

* [Query's uitvoeren op gegevens in azure Data Explorer](web-query-data.md)
