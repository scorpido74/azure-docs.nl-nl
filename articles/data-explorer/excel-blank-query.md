---
title: Gegevens visualiseren met een Azure Data Explorer Kusto-query die is geïmporteerd in Microsoft Excel
description: In dit artikel leert u hoe u een Azure Data Explorer Kusto-query importeert in Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849085"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Gegevens visualiseren met een Azure Data Explorer Kusto-query die is geïmporteerd in Microsoft Excel

Azure Data Explorer biedt twee opties voor het maken van verbinding met gegevens in Excel: gebruik maken van de native connector of een query importeren uit Azure Data Explorer. In dit artikel ziet u hoe u een query importeert vanuit Azure Data Explorer naar Excel om gegevens te visualiseren. Voeg Kusto-query toe als een Excel-gegevensbron om extra berekeningen of visualisaties op de gegevens uit te voeren.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Een organisatie-e-mailaccount dat lid is van de Azure Active-map, zodat u verbinding maken met het [Help-cluster Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>of</br>
* Maak [een testcluster en -database](create-cluster-database-portal.md) en meld u aan bij [de Web-gebruikersinterfacetoepassing azure Data Explorer](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Kusto-query definiëren als een Excel-gegevensbron

1. Voer de query uit in [Azure Data Explorer Web UI](https://dataexplorer.azure.com/clusters/help/databases/Samples)en controleer de resultaten.

1. Selecteer het tabblad **Delen** en selecteer **Query naar Power BI**.

    ![Web-gebruikersinterfacequery naar Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Er verschijnt een venster met de volgende melding:

    ![query exporteren naar klembord](media/excel-blank-query/query-exported-to-clipboard.png)

1. **Microsoft Excel openen**.

1. Selecteer op het tabblad **Gegevens** **de** > optie Gegevens**ophalen uit lege query's van andere bronnen** > **Blank Query**.

    ![Gegevens verzamelen en lege query's selecteren](media/excel-blank-query/get-data-blank-query.png)

1. Het venster **Power Query Editor** wordt geopend. Selecteer **Geavanceerde editor**in het venster .

    ![Venster Power queryeditor](media/excel-blank-query/power-query-editor.png)

1. Plak in het venster **Geavanceerde editor** de query die u naar het klembord hebt geëxporteerd en selecteer **Gereed**.

    ![Geavanceerde editorquery](media/excel-blank-query/advanced-editor-query.png)    

1. Als u wilt verifiëren, selecteert u **Referenties bewerken**.

    ![Referenties bewerken](media/excel-blank-query/edit-credentials.png)

1. Selecteer **Organisatieaccount** en **Meld u aan**. Voltooi het aanmeldingsproces en selecteer **Verbinding maken**.

    ![Volledig aanmelden](media/excel-blank-query/complete-sign-in.png)

    Herhaal de vorige stappen om meer query's toe te voegen. U de naam van de query's wijzigen in zinvollere namen.

1. Selecteer de knop **Sluiten & laden** om uw gegevens in Excel te krijgen.

    ![Sluiten en laden selecteren](media/excel-blank-query/close-and-load.png)

1. Nu staan uw gegevens in Excel. Selecteer de knop **Vernieuwen** om de query te vernieuwen.

    ![Gegevens weergeven in Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Volgende stappen

[Gegevens visualiseren met de Azure Data Explorer-connector voor Excel](excel-connector.md)