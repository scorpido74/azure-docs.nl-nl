---
title: Azure Data Explorer visualiseren met het streepje
description: In dit artikel leert u hoe u gegevens in azure Data Explorer kunt visualiseren met de restreep systeem eigen connector.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773945"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Gegevens uit Azure Data Explorer visualiseren in een herstreep

Als u de gegevens bronnen opnieuw [koppelt](https://redash.io/) , maakt u Dash boards om gegevens te visualiseren en te delen met peers. In dit artikel vindt u informatie over het instellen van Azure Data Explorer als gegevens bron voor het opnieuw insluiten en vervolgens visualiseren van gegevens.

## <a name="prerequisites"></a>Vereisten

1. [Maak een cluster en een Data Base](create-cluster-database-portal.md).
1. Gegevens opnemen zoals in de [voorbeeld gegevens van opname in Azure Data Explorer](ingest-sample-data.md)worden uitgelegd. Zie voor meer opname opties overzicht van [opname](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Een Azure Data Explorer-connector maken in een herstreep 

1. Meld u aan bij [restreep](https://www.redash.io/). Selecteer aan de **slag** om een account te maken.
1. Onder **aan de slag**selecteert u **verbinding maken met een gegevens bron**.

    ![Verbinding maken met een gegevens bron](media/redash/connect-data-source.png)

1. Selecteer in **het venster een nieuwe gegevens bron maken de** optie **Azure Data Explorer (Kusto)** en selecteer vervolgens **maken**. 

    ![Azure Data Explorer-gegevens bron selecteren](media/redash/select-adx-data-source.png)

1. Vul in het venster **Azure Data Explorer (Kusto)** het volgende formulier in en selecteer **maken**.

    ![Het venster instellingen van Azure Data Explorer (Kusto)](media/redash/adx-settings-window.png)

1. Selecteer in het venster **instellingen** de optie **Opslaan** en **verbinding testen** om uw **Azure Data Explorer-gegevens bron verbinding (Kusto)** te testen.

## <a name="create-queries-in-redash"></a>Query's maken in restreep

1. Selecteer in de linkerbovenhoek van restreep > **query** **maken** . Klik op **nieuwe query** en wijzig de naam van de query.

    ![Query maken](media/redash/create-query.png)

1. Typ uw query in het deel venster bovenin en selecteer **Opslaan** en **uitvoeren**. Selecteer **publiceren** om de query te publiceren voor toekomstig gebruik.

    ![Query opslaan en uitvoeren](media/redash/save-and-execute-query.png)

    In het linkerdeel venster ziet u de naam van de gegevens bron verbinding (**github-connector** in onze stroom) in de vervolg keuzelijst en de tabellen in de geselecteerde data base. 

1. Bekijk de query resultaten in het onderste middelste deel venster. Maak een visualisatie om met de query te gaan door de knop **nieuwe visualisatie** te selecteren.

    ![Nieuwe visualisatie](media/redash/new-visualization.png)

1. Selecteer in het visualisatie scherm het **visualisatie type** en de relevante velden, zoals **X-kolom** en **Y-kolom**. **Sla** de visualisatie op.

    ![Visualisatie configureren en opslaan](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Een query maken met behulp van een para meter

1. **Maak** > **query** om een nieuwe query te maken. Voeg een para meter toe aan deze met {{}} accolades. Selecteer **{{}}** om de **add para meter** -venster te openen. U kunt ook het *pictogram instellingen* selecteren om de kenmerken van een bestaande para meter te wijzigen en het venster **< parameter_name >** te openen. 

    ![para meter invoegen](media/redash/insert-parameter.png)

1. Geef een naam op voor de para meter. Selecteer **type**: **vervolg keuze lijst op basis** van een query in vervolg keuzemenu. Selecteer **OK**

    ![vervolg keuzelijst op basis van query's](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > De query gebruikt meerdere waarden. Daarom moet u de volgende syntaxis `| where Type in ((split('{{Type}}', ',')))`toevoegen. Zie [in operator](/azure/kusto/query/inoperator)voor meer informatie. Dit resulteert in [meerdere query parameter opties in herstreep-app](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Een dash board maken in opnieuw streepje

1. Als u uw dash board wilt maken, **maakt** u > **dash board**. U kunt ook bestaand dash board selecteren, **Dash boards** > een dash board selecteren in de lijst.

    ![Dash board maken](media/redash/create-dashboard.png)

1. Geef uw dash board een naam in het **nieuwe dashboard** venster en selecteer **Opslaan**. Selecteer in **< venster Dashboard_name >** de optie **widget toevoegen** om een nieuwe widget te maken. 

1. Selecteer in het venster **widget toevoegen** de optie query naam, **Kies visualisatie**en **para meters**. Selecteer **toevoegen aan dash board**

   ![Visualisaties kiezen en toevoegen aan het dash board](media/redash/add-widget-window.png)

1. Selecteer **gereed bewerken** om het maken van het dash board te volt ooien.

1.  Selecteer in de modus voor het dash board bewerken de optie **Dashboard niveau filters gebruiken** om de eerder gedefinieerde **type** para meter te gebruiken.

    ![Het maken van een dash board volt ooien](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Volgende stappen

* [Query's schrijven voor Azure Data Explorer](write-queries.md)


