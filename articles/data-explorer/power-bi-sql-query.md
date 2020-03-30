---
title: Gegevens uit Azure Data Explorer visualiseren met een Power BI SQL-query
description: 'In dit artikel leert u hoe u een van de drie opties gebruikt voor het visualiseren van gegevens in Power BI: een SQL-query ten opzichte van een Azure Data Explorer-cluster.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: d402d4c1ee77d0f97d2a5c3bdf43d0cc62aac096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560470"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Gegevens uit Azure Data Explorer visualiseren met een SQL-query in Power BI

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Power BI is een business analytics-oplossing waarmee u uw gegevens kunt visualiseren en de gegevens kunt delen in uw organisatie.

Azure Data Explorer biedt drie opties waarmee u verbinding kunt maken met gegevens in Power BI: de ingebouwde connector gebruiken, een query importeren uit Azure Data Explorer, of een SQL-query gebruiken. In dit artikel ziet u hoe u een SQL-query gebruikt om gegevens te verzamelen en te visualiseren in een Power BI-rapport.

Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Je hebt het volgende nodig om dit artikel te voltooien:

* Een organisatie-e-mailaccount dat lid is van Azure Active Directory, zodat u verbinding kunt maken met het [Azure Data Explorer-helpcluster](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecteer **GRATIS DOWNLOADEN**)

## <a name="get-data-from-azure-data-explorer"></a>Gegevens ophalen uit Azure Data Explorer

Eerst maakt u verbinding met het Azure Data Explorer-helpcluster en daarna haalt u een subset gegevens op uit de tabel *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Doorgaans gebruikt u de systeemeigen querytaal met Azure Data Explorer, maar deze ondersteunt ook SQL-query's, en die gaat u hier gebruiken. Azure Data Explorer vertaalt de SQL-query voor u in een systeemeigen query.

1. Selecteer in Power BI-bureaublad op het tabblad **Start** de optie **Gegevens opvragen** dan **Meer**.

    ![Gegevens ophalen](media/power-bi-sql-query/get-data-more.png)

1. Zoek naar *Azure SQL Database*, selecteer **Azure SQL Database** en vervolgens **Verbinding maken**.

    ![Gegevens zoeken en ophalen](media/power-bi-sql-query/search-get-data.png)

1. Vul het formulier in het scherm **SQL Server-database** in met de volgende informatie.

    ![Database-, tabel-, queryopties](media/power-bi-sql-query/database-table-query.png)

    **Instelling** | **Waarde** | **Veldbeschrijving**
    |---|---|---|
    | server | *help.kusto.windows.net* | De URL voor het helpcluster (zonder *https://*). Voor andere clusters bevindt de URL zich in het formulier * \<ClusterNaam\>.\< Regio\>.kusto.windows.net*. |
    | Database | *Monsters* | De voorbeelddatabase die wordt gehost op het cluster waarmee u verbinding maakt. |
    | Gegevensverbindingsmodus | *Importeren* | Bepaalt of Power BI de gegevens importeert of rechtstreeks verbinding maakt met de gegevensbron. Met deze connector kunt u een van beide opties gebruiken. |
    | Time-out van opdracht | Leeg laten | Hoe lang de query wordt uitgevoerd voordat deze een time-outfout genereert. |
    | SQL-instructie | Kopieer de query onder deze tabel | De SQL-instructie die door Azure Data Explorer wordt vertaald in een systeemeigen query. |
    | Andere opties | Behoud de standaardwaarden | Opties zijn niet van toepassing op Azure Data Explorer-clusters. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Meld u aan als u nog geen verbinding hebt met het helpcluster. Meld u aan met een Microsoft-account en selecteer vervolgens **Verbinding maken**.

    ![Aanmelden](media/power-bi-sql-query/sign-in.png)

1. Selecteer in het scherm **help.kusto.windows.net: Voorbeelden** de optie **Laden**.

    ![Gegevens laden](media/power-bi-sql-query/load-data.png)

    De tabel wordt geopend in het hoofdvenster van de Power BI, in de rapportweergave waarin u rapporten kunt maken op basis van de voorbeeldgegevens.

## <a name="visualize-data-in-a-report"></a>Gegevens visualiseren in een rapport

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u het rapport dat u voor dit artikel hebt gemaakt, niet meer nodig hebt, verwijdert u het Power BI-bureaubladbestand (.pbix).

## <a name="next-steps"></a>Volgende stappen

[Gegevens visualiseren met de Azure Data Explorer-connector voor Power BI](power-bi-connector.md)