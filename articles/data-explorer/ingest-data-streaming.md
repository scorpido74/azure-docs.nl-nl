---
title: Streaming gebruiken om gegevens in te nemen in Azure Data Explorer
description: Meer informatie over het innemen van (laden) gegevens in Azure Data Explorer met behulp van streaming.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297081"
---
# <a name="streaming-ingestion-preview"></a>Streaming opname (Preview)

Gebruik streaming opname wanneer u een lage latentie met een innametijd van minder dan 10 seconden voor gevarieerde volumegegevens. Het wordt gebruikt om de operationele verwerking van veel tabellen te optimaliseren, in een of meer databases, waar de stroom van gegevens in elke tabel relatief klein is (weinig records per seconde), maar het totale gegevensopnamevolume hoog is (duizenden records per seconde). 

Gebruik bulkopname in plaats van streaming opname wanneer de hoeveelheid gegevens groeit tot meer dan 1 MB per seconde per tabel. Lees [het overzicht gegevensopname](/azure/data-explorer/ingest-data-overview) voor meer informatie over de verschillende innamemethoden.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Meld u aan bij de [webgebruikersinterface](https://dataexplorer.azure.com/).
* Een [Azure Data Explorer-cluster en -database maken](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Streaming opname op uw cluster inschakelen

> [!WARNING]
> Bekijk de [beperkingen](#limitations) voordat u stomen inschakelt.

1. Ga in de Azure-portal naar uw Azure Data Explorer-cluster. Selecteer **Configuraties**in **Instellingen**. 
1. Selecteer **in** het deelvenster Configuraties de optie **Aan** om **Streaming in te**schakelen.
1. Selecteer **Opslaan**.
 
    ![streaming opname op](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. Definieer in de [webgebruikersinterface](https://dataexplorer.azure.com/)het beleid voor [streamingopname](/azure/kusto/management/streamingingestionpolicy) op tafel(en) of database(s) die streaminggegevens ontvangt. 

    > [!NOTE]
    > * Als het beleid is gedefinieerd op databaseniveau, zijn alle tabellen in de database ingeschakeld voor streaming.
    > * Het toegepaste beleid kan alleen verwijzen naar nieuw ingenomen gegevens en niet op andere tabellen in de database.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Gebruik streaming ingestion om gegevens in uw cluster in te nemen

Er zijn twee ondersteunde typen streaming:


* [**Gebeurtenishub**](/azure/data-explorer/ingest-data-event-hub), die wordt gebruikt als gegevensbron
* **Voor aangepaste opname** moet u een toepassing schrijven die een van de Azure Data Explorer-clientbibliotheken gebruikt. Zie [voorbeeld van streaminginname](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) voor een voorbeeldtoepassing.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Kies het juiste type streamingopname

|   |Event Hub  |Aangepaste opname  |
|---------|---------|---------|
|Gegevensvertraging tussen opname-initiatie en de beschikbare gegevens voor query   |    Langere vertraging     |   Kortere vertraging      |
|Overhead voor ontwikkeling    |   Snelle en eenvoudige installatie, geen ontwikkeling overhead    |   Hoge ontwikkelingsoverhead voor toepassing om fouten te verwerken en de consistentie van gegevens te waarborgen     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Streaming opnemen op uw cluster uitschakelen

> [!WARNING]
> Het uitschakelen van streaming opname kan een paar uur duren.

1. Drop [streaming ingestion policy](/azure/kusto/management/streamingingestionpolicy) from all relevant tables and databases. De verwijdering van het streamingbeleid leidt tot het streamen van gegevensverkeer van de initiële opslag naar de permanente opslag in het kolomarchief (omvang of shards). De gegevensverplaatsing kan enkele seconden tot een paar uur duren, afhankelijk van de hoeveelheid gegevens in de oorspronkelijke opslag en hoe de CPU en het geheugen door het cluster worden gebruikt.
1. Ga in de Azure-portal naar uw Azure Data Explorer-cluster. Selecteer **Configuraties**in **Instellingen**. 
1. Selecteer **in** het deelvenster Configuraties de optie **Uit** om **Streaming in te**schakelen.
1. Selecteer **Opslaan**.

    ![Streaming opname uit](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Beperkingen

* Streaming opname biedt geen ondersteuning [voor Databasecursors](/azure/kusto/management/databasecursor) of [Data mapping](/azure/kusto/management/mappings). Alleen [vooraf gemaakte](/azure/kusto/management/create-ingestion-mapping-command) gegevenstoewijzing wordt ondersteund. 
* Streaming opnameprestaties en capaciteitsschalen met verhoogde VM- en clustergroottes. Gelijktijdige inname is beperkt tot zes inname per kern. Voor 16 core SKU's, zoals D14 en L16, is de maximale ondersteunde belasting bijvoorbeeld 96 gelijktijdige inname. Voor twee kernSKU's, zoals D11, is de maximale ondersteunde belasting 12 gelijktijdige inname.
* De beperking van de gegevensgrootte per opnameverzoek is 4 MB.
* Het kan tot vijf minuten duren voordat schema-updates, zoals het maken en wijzigen van tabellen en innametoewijzingen, tot vijf minuten duren voordat de streamingservice is opgenomen.
* Als u streaming ineenschakelt op een cluster, zelfs wanneer gegevens niet via streaming worden ingenomen, wordt een deel van de lokale SSD-schijf van de clustermachines gebruikt voor het streamen van opnamegegevens en wordt de beschikbare opslag voor hot cache verminderd.
* [Extent tags](/azure/kusto/management/extents-overview#extent-tagging) kunnen niet worden ingesteld op de streaming opnamegegevens.

## <a name="next-steps"></a>Volgende stappen

* [Query's uitvoeren op gegevens in Azure Data Explorer](web-query-data.md)
