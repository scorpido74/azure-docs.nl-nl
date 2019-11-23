---
title: Gegevens visualiseren met behulp van een Azure Data Explorer Kusto-query geïmporteerd in micro soft Excel
description: In dit artikel leert u hoe u een Azure Data Explorer Kusto-query in micro soft Excel kunt importeren.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 55198e0c38c2922d69b68d9ce62e16ea25e9cc44
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173727"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Gegevens visualiseren met behulp van een Azure Data Explorer Kusto-query geïmporteerd in micro soft Excel

Azure Data Explorer biedt twee opties voor het maken van verbinding met gegevens in Excel: gebruik de systeem eigen connector of importeer een query vanuit Azure Data Explorer. In dit artikel leest u hoe u een query vanuit Azure Data Explorer naar Excel kunt importeren om gegevens te visualiseren. Voeg Kusto query als Excel-gegevens bron toe om extra berekeningen of visualisaties op de gegevens uit te voeren.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een organisatie-e-mail account dat lid is van Azure Active Directory, zodat u verbinding kunt maken met het [azure Data Explorer Help-cluster](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>of</br>
* Maak [een test cluster en een Data Base](create-cluster-database-portal.md) en meld u aan bij [de Azure Data Explorer web UI-toepassing](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Kusto-query definiëren als een Excel-gegevens bron

1. Voer in [Azure Data Explorer web UI](https://dataexplorer.azure.com/clusters/help/databases/Samples)de query uit en controleer de resultaten.

1. Selecteer het tabblad **delen** en selecteer **Query om te Power bi**.

    ![Webgebruikersinterface-query naar Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Er wordt een venster met de volgende melding weer gegeven:

    ![query exporteren naar klem bord](media/excel-blank-query/query-exported-to-clipboard.png)

1. Open **Microsoft Excel**.

1. Op het tabblad **gegevens** selecteert u **gegevens ophalen** > **uit andere bronnen** > **lege query**.

    ![Gegevens ophalen en lege query selecteren](media/excel-blank-query/get-data-blank-query.png)

1. Het venster **Power query editor** wordt geopend. Selecteer **Geavanceerde editor**in het venster.

    ![Venster Power query-editor](media/excel-blank-query/power-query-editor.png)

1. Plak in het venster **Geavanceerde editor** de query die u hebt geëxporteerd naar het klem bord en selecteer **gereed**.

    ![Geavanceerde editor-query](media/excel-blank-query/advanced-editor-query.png)    

1. Selecteer **referenties bewerken**om te verifiëren.

    ![Referenties bewerken](media/excel-blank-query/edit-credentials.png)

1. Selecteer **organisatie account** en **Meld u aan**. Voltooi het aanmeldings proces en selecteer vervolgens **verbinding maken**.

    ![Aanmelding volt ooien](media/excel-blank-query/complete-sign-in.png)

    Herhaal de vorige stappen om meer query's toe te voegen. U kunt de naam van de query's wijzigen in meer herken bare namen.

1. Selecteer de knop **& laden sluiten** om uw gegevens in Excel op te halen.

    ![Selecteer sluiten en laden](media/excel-blank-query/close-and-load.png)

1. Uw gegevens bevindt zich nu in Excel. Selecteer de knop **vernieuwen** om de query te vernieuwen.

    ![Gegevens weer geven in Excel](media/excel-blank-query/data-in-excel.png)