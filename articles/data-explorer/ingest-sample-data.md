---
title: Voorbeeldgegevens innemen in Azure Data Explorer
description: Meer informatie over het innemen (laden) van weergerelateerde voorbeeldgegevens in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74975173"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Snelstart: Voorbeeldgegevens opnemen in Azure Data Explorer

In dit artikel ziet u hoe u voorbeeldgegevens innemen (laden) in een Azure Data Explorer-database. Er zijn [verschillende manieren om gegevens in te nemen;](ingest-data-overview.md) dit artikel richt zich op een basisaanpak die geschikt is voor testdoeleinden.

> [!NOTE]
> U hebt deze gegevens al als u innamegegevens hebt voltooid [met de Azure Data Explorer Python-bibliotheek.](python-ingest-data.md)

## <a name="prerequisites"></a>Vereisten

[Een cluster en database voor testdoeleinden](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Gegevens opnemen

De **StormEvents-gegevensset** bevat weergerelateerde gegevens van de [National Centers for Environmental Information.](https://www.ncdc.noaa.gov/stormevents/)

1. Log hier [https://dataexplorer.azure.com](https://dataexplorer.azure.com)in

1. Selecteer in de linkerbovenhoek van de toepassing **Add Cluster**.

1. Voer **in** het dialoogvenster Cluster toevoegen de `https://<ClusterName>.<Region>.kusto.windows.net/`URL van het cluster in het formulier in en selecteer **Toevoegen**.

1. Plak de volgende opdracht en selecteer **Uitvoeren** om een tabel StormEvents te maken.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Plak de volgende opdracht en selecteer **Uitvoeren** om gegevens in te nemen in de tabel StormEvents.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Nadat de inname is voltooid, plakt u de volgende query, selecteert u de query in het venster en selecteert u **Uitvoeren**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    De query retourneert de volgende resultaten van de ingenomen voorbeeldgegevens.

    ![Queryresultaten](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Volgende stappen

* [Gegevensopname in Azure Data Explorer](ingest-data-overview.md) voor meer informatie over innamemethoden.
* [Snelstart: querygegevens in Azure Data Explorer](web-query-data.md) Web-gebruikersinterface.
* [Query's schrijven](write-queries.md) met Kusto Query Language.
