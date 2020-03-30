---
title: Gegevens visualiseren met Azure Data Explorer-connector voor Power BI
description: 'In dit artikel leert u hoe u een van de drie opties gebruikt voor het visualiseren van gegevens in Power BI: de Power BI-connector voor Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a95d45481bed17e46429e3a22dff4b8cc62354a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560487"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Gegevens visualiseren met de Azure Data Explorer-connector voor Power BI

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Power BI is een business analytics-oplossing waarmee u uw gegevens kunt visualiseren en de gegevens kunt delen in uw organisatie. Azure Data Explorer biedt drie opties waarmee u verbinding kunt maken met gegevens in Power BI: de ingebouwde connector gebruiken, een query importeren uit Azure Data Explorer, of een SQL-query gebruiken. In dit artikel ziet u hoe u de ingebouwde connector gebruiken om gegevens te verzamelen en te visualiseren in een Power BI-rapport. Het gebruik van de azure Data Explorer-native connector voor het maken van Power BI-dashboards is eenvoudig. De Power BI-connector ondersteunt [import- en direct query-connectiviteitsmodi](https://docs.microsoft.com/power-bi/desktop-directquery-about). U dashboards maken met **de import-** of **DirectQuery-modus,** afhankelijk van de vereisten voor scenario,schaal en prestaties. 

## <a name="prerequisites"></a>Vereisten

Je hebt het volgende nodig om dit artikel te voltooien:

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Een organisatie-e-mailaccount dat lid is van Azure Active Directory, zodat u verbinding kunt maken met het [Azure Data Explorer-helpcluster](https://dataexplorer.azure.com/clusters/help/databases/samples).
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecteer **GRATIS DOWNLOADEN**)

## <a name="get-data-from-azure-data-explorer"></a>Gegevens ophalen uit Azure Data Explorer

Eerst maakt u verbinding met het Azure Data Explorer-helpcluster en daarna haalt u een subset gegevens op uit de tabel *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Selecteer in Power BI-bureaublad op het tabblad **Start** de optie **Gegevens opvragen** dan **Meer**.

    ![Gegevens ophalen](media/power-bi-connector/get-data-more.png)

1. Zoek naar *Azure Data Explorer,* selecteer **Azure Data Explorer** en maak vervolgens **Verbinding**.

    ![Gegevens zoeken en ophalen](media/power-bi-connector/search-get-data.png)

1. Vul in het scherm **Azure Data Explorer (Kusto)** het formulier in met de volgende gegevens.

    ![Cluster-, database-, tabelopties](media/power-bi-connector/cluster-database-table.png)

    **Instelling** | **Waarde** | **Veldbeschrijving**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | De URL voor het helpcluster. Voor andere clusters bevindt de URL zich in het formulier *https://\<ClusterName\>.\< Regio\>.kusto.windows.net*. |
    | Database | Leeg laten | Een database die wordt gehost op het cluster waarmee u verbinding maakt. Deze selecteren we in een latere stap. |
    | Tabelnaam | Leeg laten | Een van de tabellen in de <code>StormEvents \| take 1000</code>database, of een query als . Deze selecteren we in een latere stap. |
    | Geavanceerde opties | Leeg laten | Opties voor uw query's, zoals grootte van resultatenset. |
    | Gegevensverbindingsmodus | *DirectQuery* | Bepaalt of Power BI de gegevens importeert of rechtstreeks verbinding maakt met de gegevensbron. Met deze connector kunt u een van beide opties gebruiken. |
    | | | |
    
    > [!NOTE]
    > In **de importmodus** worden gegevens verplaatst naar Power BI. In **de DirectQuery-modus** worden gegevens rechtstreeks vanuit uw Azure Data Explorer-cluster opgevraagd.
    >
    > De **Import** importmodus gebruiken wanneer:
    > * Uw gegevensset is klein.
    > * U hebt geen bijna realtime gegevens nodig. 
    > * Uw gegevens zijn al geaggregeerd of u voert [aggregatie](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions) uit in Kusto    
    >
    > De **modus DirectQuery** gebruiken wanneer:
    > * Uw gegevensset is zeer groot. 
    > * Je hebt bijna realtime gegevens nodig.   

1. Meld u aan als u nog geen verbinding hebt met het helpcluster. Meld u aan met een organisatieaccount en selecteer **Verbinding maken**.

    ![Aanmelden](media/power-bi-connector/sign-in.png)

1. In het scherm **Navigator** vouwt u de database **Voorbeelden** uit en selecteert u eerst **StormEvents** en vervolgens **Bewerken**.

    ![Tabel selecteren](media/power-bi-connector/select-table.png)

    De tabel wordt geopend in Power Query-editor. Hier kunt u rijen en kolommen bewerken voordat u de gegevens importeert.

1. In de Power Query-editor selecteert u de pijl naast de kolom **DamageCrops** en klikt u op **Aflopend sorteren**.

    ![DamageCrops aflopend sorteren](media/power-bi-connector/sort-descending.png)

1. Op het tabblad **Start** selecteert u **Rijen behouden** en vervolgens **Bovenste rijen behouden**. Voer de waarde *1000* in om de bovenste 1000 rijen van de gesorteerde tabel te behouden.

    ![Bovenste rijen behouden](media/power-bi-connector/keep-top-rows.png)

1. Selecteer op het tabblad **Start** de optie **Sluiten en toepassen**.

    ![Sluiten en toepassen](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Gegevens visualiseren in een rapport

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u het rapport dat u voor dit artikel hebt gemaakt, niet meer nodig hebt, verwijdert u het Power BI-bureaubladbestand (.pbix).

## <a name="next-steps"></a>Volgende stappen

[Tips voor het gebruik van de Azure Data Explorer-connector voor Power BI om gegevens op te vragen](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
