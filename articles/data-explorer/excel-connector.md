---
title: Gegevens visualiseren met de Azure Data Explorer-connector voor Microsoft Excel
description: In dit artikel leert u hoe u de Excel-connector voor Azure Data Explorer gebruikt.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849051"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Gegevens visualiseren met de Azure Data Explorer-connector voor Excel

Azure Data Explorer biedt twee opties voor het maken van verbinding met gegevens in Excel: gebruik maken van de native connector of een query importeren uit Azure Data Explorer. In dit artikel ziet u hoe u de native connector in Excel gebruikt en verbinding maakt met het Azure Data Explorer-cluster om gegevens op te halen en te visualiseren.

De Azure Data Explorer Excel-connector biedt de mogelijkheid om queryresultaten naar Excel te exporteren. U ook een KQL-query toevoegen als Excel-gegevensbron voor aanvullende berekeningen of visualisaties.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Kusto-query definiëren als een Excel-gegevensbron en de gegevens laden naar Excel

1. **Microsoft Excel openen**.
1. Selecteer op het tabblad **Gegevens** de optie **Gegevens** > **ophalen uit Azure** > **vanuit Azure Data Explorer**.

    ![Gegevens ophalen uit Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. Voer in het venster **Azure Data Explorer (Kusto)** de volgende velden in en selecteer **OK**.

    ![Azure Data Explorer (Kusto) venster](media/excel-connector/adx-connection-window.png)
    
    |Veld   |Beschrijving |
    |---------|---------|
    |**Cluster**   |   Naam van cluster (verplicht)      |    
    |**Database**     |    Naam van de database      |    
    |**Tabelnaam of Azure Data Explorer-query**    |     Naam van tabel of Azure Data Explorer-query    | 
    
    **Geavanceerde opties:**

     |Veld   |Beschrijving |
    |---------|---------|
    |**Queryresultaatrecordnummer beperken**     |     Het aantal records beperken dat is geladen in Excel  |    
    |**Gegevensgrootte van queryresultaten beperken (bytes)**    |    De gegevensgrootte beperken      |   
    |**Resultaatset-truncation uitschakelen**    |         |      
    |**Aanvullende setinstructies (gescheiden door puntkomma's)**    |    Instructies `set` toevoegen om toe te passen op gegevensbron     |   

1.  Navigeer in het deelvenster **Navigator** naar de juiste tabel. Selecteer in het venster van de tabelvoorbeeld de optie **Gegevens transformeren** om wijzigingen aan te brengen in uw gegevens of selecteer **Laden** om deze in Excel te laden.

![Tabelvoorbeeldvenster](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Als **database-** en/of **tabelnaam of Azure Data Explorer-query** al is opgegeven, wordt het juiste tabelvoorbeeldvenster automatisch geopend. 

## <a name="analyze-and-visualize-data-in-excel"></a>Gegevens analyseren en visualiseren in Excel

Zodra de gegevens worden geladen om uit te blinken en beschikbaar zijn in uw Excel-blad, u de gegevens analyseren, samenvatten en visualiseren door relaties en visuals te maken. 

1.  Selecteer op het tabblad **Tabelontwerp** de optie **Samenvatten met draaitabel**. Selecteer in het venster **Draaitabel maken** de relevante tabel en selecteer **OK**.

    ![Draaitabel maken](media/excel-connector/create-pivot-table.png)

1. Selecteer in het deelvenster **Draaitabelvelden** de relevante tabelkolommen om overzichtstabellen te maken. In het onderstaande voorbeeld worden **EventId** en **Status** geselecteerd.
    
    ![Draaitabelvelden selecteren](media/excel-connector/pivot-table-pick-fields.png)

1. Selecteer op het tabblad **Draaitabelanalyse** de optie **Draaigrafiek** om visuals te maken op basis van de tabel. 

    ![Draaigrafiek](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. Gebruik in het onderstaande voorbeeld **Gebeurtenis-id,** **StartTime**en **EventType** om aanvullende informatie over de weergebeurtenissen weer te geven.

    ![Gegevens visualiseren](media/excel-connector/visualize-excel-data.png)

1. Maak volledige dashboards om uw gegevens te controleren.

## <a name="next-steps"></a>Volgende stappen

[Gegevens visualiseren met een Azure Data Explorer Kusto-query die is geïmporteerd in Microsoft Excel](excel-blank-query.md)