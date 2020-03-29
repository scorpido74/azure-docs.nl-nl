---
title: Azure Data Explorer visualiseren met Redash
description: In dit artikel leert u hoe u gegevens in Azure Data Explorer visualiseert met de native connector van Redash.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773945"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Gegevens van Azure Data Explorer visualiseren in Redash

[Redash](https://redash.io/) verbindt en queriet uw gegevensbronnen, bouwt dashboards om gegevens te visualiseren en te delen met collega's. In dit artikel leert u hoe u Azure Data Explorer instelt als gegevensbron voor Redash en vervolgens gegevens visualiseert.

## <a name="prerequisites"></a>Vereisten

1. [Cluster en database maken](create-cluster-database-portal.md).
1. Gegevens opnemen zoals uitgelegd in [het opnemen van voorbeeldgegevens in Azure Data Explorer.](ingest-sample-data.md) Zie voor meer innameopties [het innameoverzicht](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Azure Data Explorer-connector maken in Redash 

1. Log hier [Redash](https://www.redash.io/)in Selecteer **Aan de slag** om een account te maken.
1. Selecteer een gegevensbron verbinden onder **Laten we aan de slag** **gaan.**

    ![Een gegevensbron verbinden](media/redash/connect-data-source.png)

1. Selecteer azure **data explorer (Kusto)** **in Een nieuw gegevensbronvenster maken** en selecteer Vervolgens **Maken**. 

    ![Gegevensbron van Azure Data Explorer selecteren](media/redash/select-adx-data-source.png)

1. Voer in het venster **Azure Data Explorer (Kusto)** het volgende formulier in en selecteer **Maken**.

    ![Instellingenvenster Azure Data Explorer (Kusto)](media/redash/adx-settings-window.png)

1. Selecteer **in het** venster **Instellingen De** optie Verbinding opslaan en **testen** om de **gegevensbronverbinding van Azure Data Explorer (Kusto)** te testen.

## <a name="create-queries-in-redash"></a>Query's maken in Redash

1. **Selecteer** > Query maken boven links boven op Redash **.** Klik op **Nieuwe query** en wijzig de naam van de query.

    ![Query maken](media/redash/create-query.png)

1. Typ de query in het bovenste bewerkingsvenster en selecteer **Opslaan** en **uitvoeren**. Selecteer **Publiceren** om query te publiceren voor toekomstig gebruik.

    ![Query opslaan en uitvoeren](media/redash/save-and-execute-query.png)

    In het linkerdeelvenster ziet u de naam van de gegevensbronverbinding **(Github-connector** in onze stroom) in het vervolgkeuzemenu en de tabellen in de geselecteerde database. 

1. Bekijk de queryresultaten in het onderste centrale deelvenster. Maak een visualisatie die bij de query past door de knop **Nieuwe visualisatie te** selecteren.

    ![Nieuwe visualisatie](media/redash/new-visualization.png)

1. Selecteer in het visualisatiescherm het **visualisatietype** en de relevante velden zoals **X-kolom** en **Y-kolom**. Sla de visualisatie **op.**

    ![Visualisatie configureren en opslaan](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Een query maken met een parameter

1. **Query** > **maken** om een nieuwe query te maken. Voeg er een parameter{}aan toe met { } krullende haakjes. Selecteer **{}{ }** om **het venster Parameter toevoegen** te openen. U ook het *instellingenpictogram* selecteren om de kenmerken van een bestaande parameter te wijzigen en de **<parameter_name>** venster openen. 

    ![invoegen, parameter](media/redash/insert-parameter.png)

1. Geef uw parameter een naam. Selecteer **Tekst:** **vervolgkeuzelijst op basis van query's** in het vervolgkeuzemenu. Selecteer **OK**

    ![vervolgkeuzelijst op basis van query's](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > De query gebruikt meerdere waarden, daarom `| where Type in ((split('{{Type}}', ',')))`moet u de volgende syntaxis opnemen. Zie [in operator](/azure/kusto/query/inoperator)voor meer informatie . Dit resulteert in [meerdere queryparameteropties in de redash-app](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Een dashboard maken in Redash

1. Als u uw dashboard wilt maken, **maakt u** > **Dashboard**maken . U ook bestaande **dashboards, dashboards** > een dashboard in de lijst selecteren.

    ![Dashboard maken](media/redash/create-dashboard.png)

1. Geef in het venster **Nieuw dashboard** een naam aan uw dashboard en selecteer **Opslaan**. Selecteer in **<Dashboard_name>** venster Widget **toevoegen** om een nieuwe widget te maken. 

1. Selecteer **in het** venster Widget toevoegen de naam van query' **s, Visualisatie kiezen**en **Parameters**. Selecteer **Toevoegen aan dashboard**

   ![Visualisaties kiezen en toevoegen aan dashboard](media/redash/add-widget-window.png)

1. Selecteer **Gereed bewerken** om het maken van dashboards te voltooien.

1.  Selecteer in de modus dashboardbewerking de optie **Dashboardniveaufilters gebruiken** om de parameter Type te gebruiken **die** eerder is gedefinieerd.

    ![Volledige dashboardcreatie](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Volgende stappen

* [Query's schrijven voor Azure Data Explorer](write-queries.md)


