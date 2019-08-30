---
title: Gegevens visualiseren met de Azure Data Explorer-connector voor micro soft Excel
description: In dit artikel leert u hoe u de Excel-connector voor Azure Data Explorer kunt gebruiken.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 18c6499afe450a0387b0d9f0f13ee4378e0a21a9
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173831"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Gegevens visualiseren met de Azure Data Explorer-connector voor Excel

Azure Data Explorer biedt twee opties voor het maken van verbinding met gegevens in Excel: gebruik de systeem eigen connector of importeer een query vanuit Azure Data Explorer. In dit artikel leest u hoe u de systeem eigen connector in Excel gebruikt en hoe u verbinding maakt met het Azure Data Explorer-cluster om gegevens op te halen en te visualiseren.

De systeem eigen Azure Data Explorer-connector biedt de mogelijkheid om query resultaten naar Excel te exporteren. U kunt ook een KQL-query toevoegen als een Excel-gegevens bron voor aanvullende berekeningen of visualisaties.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Kusto query definiëren als een Excel-gegevens bron en de gegevens laden in Excel

1. Open **Microsoft Excel**.
1. Selecteer gegevens > ophalenvanAzure > in**Azure Data Explorer**op het tabblad gegevens.

    ![Gegevens ophalen uit Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. In het venster **Azure Data Explorer (Kusto)** vult u de volgende velden in en selecteert u **OK**.

    ![Venster Azure Data Explorer (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Veld   |Description |
    |---------|---------|
    |**Cluster**   |   De naam van het cluster (verplicht)      |    
    |**Database**     |    Naam van database      |    
    |**Tabel naam of Azure Data Explorer query**    |     De naam van de tabel of Azure-Data Explorer query    | 
    
    **Geavanceerde opties:**

     |Veld   |Description |
    |---------|---------|
    |**Record nummer van query resultaat beperken**     |     Het aantal records dat in Excel is geladen beperken  |    
    |**Gegevens grootte van query resultaat beperken (bytes)**    |    De grootte van de gegevens beperken      |   
    |**Afbreking van resultaten sets uitschakelen**    |         |      
    |**Aanvullende set-instructies (gescheiden door punt komma's)**    |    Instructies `set` toevoegen die moeten worden toegepast op de gegevens bron     |   

1.  Navigeer in het **Navigatie** deel venster naar de juiste tabel. Selecteer in het voorbeeld venster voor tabellen de optie **gegevens transformeren** om wijzigingen aan te brengen aan uw gegevens of selecteer **laden** om deze naar Excel te laden.

![Voorbeeld venster voor tabellen](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Als er al een **Data Base** -en/of **tabel naam of Azure Data Explorer-query** is opgegeven, wordt het juiste voorbeeld venster voor tabellen automatisch geopend. 

## <a name="analyze-and-visualize-data-in-excel"></a>Gegevens in Excel analyseren en visualiseren

Zodra de gegevens zijn geladen in Excel en beschikbaar zijn in uw Excel-werk blad, kunt u de gegevens analyseren, samenvatten en visualiseren door relaties en visuele elementen te maken. 

1.  Op het tabblad **tabel ontwerp** selecteert u **samenvatten met PivotTable**. Selecteer in het venster **draai tabel maken** de relevante tabel en selecteer **OK**.

    ![Draai tabel maken](media/excel-connector/create-pivot-table.png)

1. Selecteer in het deel venster **draai tabel velden** de relevante tabel kolommen om samenvattings tabellen te maken. In het onderstaande voor beeld zijn de **gebeurtenis** -instellingen en de **status** geselecteerd.
    
    ![Draai tabel velden selecteren](media/excel-connector/pivot-table-pick-fields.png)

1. Selecteer in het tabblad **analyseren van draai tabel** de optie **draai grafiek** om visuele elementen te maken op basis van de tabel. 

    ![Draai grafiek](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. In het onderstaande voor beeld gebruikt u **gebeurtenis-id**, **StartTime**en **Event** type om aanvullende informatie over de weer te geven gebeurtenissen te bekijken.

    ![Gegevens visualiseren](media/excel-connector/visualize-excel-data.png)

1. Maak volledige Dash boards om uw gegevens te bewaken.

