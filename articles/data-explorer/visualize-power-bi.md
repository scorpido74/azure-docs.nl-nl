---
title: 'Zelfstudie: gegevens visualiseren van Azure Data Explorer in Power BI'
description: In deze zelfstudie leert u hoe u verbinding met Azure Data Explorer kunt maken in Power BI om uw gegevens te visualiseren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: ec1579792a6e247bf49946bb8609a626154fbd46
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037359"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Zelfstudie: gegevens visualiseren van Azure Data Explorer in Power BI

Azure Data Explorer is een snelle en zeer schaalbare service om gegevens in logboeken en telemetrie te verkennen. Power BI is een business analytics-oplossing waarmee u uw gegevens kunt visualiseren en de gegevens kunt delen in uw organisatie. In deze zelfstudie leert u hoe u visuele elementen kunt genereren in Azure Data Explorer. Vervolgens maakt u via Power BI verbinding met Azure Data Explorer, stelt u een rapport samen op basis van voorbeeldgegevens en publiceert u het rapport in de Power BI-service.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint. [Meld u aan voor een gratis proefversie](https://app.powerbi.com/signupredirect?pbi_source=web) voordat u begint als u nog niet bent aangemeld voor Power BI Pro.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Visuele elementen genereren in Azure Data Explorer
> * Verbinding maken met Azure Data Explorer in Power BI Desktop
> * Werken met gegevens in Power BI Desktop
> * Een rapport maken met visuele elementen
> * Het rapport publiceren en delen

## <a name="prerequisites"></a>Vereisten

Naast Azure- en Power BI-abonnementen hebt u ook het volgende nodig om deze zelfstudie te voltooien:

* [Een cluster en database voor testdoeleinden](create-cluster-database-portal.md)

* [De StormEvents-voorbeeldgegevens](ingest-sample-data.md). [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecteer **GRATIS DOWNLOADEN**)

## <a name="render-visuals-in-azure-data-explorer"></a>Visuele elementen genereren in Azure Data Explorer

Laten we eens kijken hoe u visuele elementen in Azure Data Explorer kunt genereren voordat we in Power BI duiken. Dit is ideaal voor een snelle analyse.

1. Meld u aan bij [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Selecteer in het linkerdeelvenster de testdatabase die de StormEvents-voorbeeldgegevens bevat.

1. Plak de volgende query in het rechtervenster en selecteer **Uitvoeren**.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Deze query telt weergebeurtenissen per staat. Vervolgens wordt een kolomdiagram gegenereerd voor alle staten die meer dan 1800 weergebeurtenissen tellen.

    ![Kolomdiagram van gebeurtenissen](media/visualize-power-bi/events-column-chart.png)

1. Plak de volgende query in het rechtervenster en selecteer **Uitvoeren**.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Deze query telt weergebeurtenissen per type voor de maand juli in de staat Washington. Vervolgens wordt een cirkeldiagram gegenereerd met daarin het percentage per type gebeurtenis.

    ![Cirkeldiagram van gebeurtenissen](media/visualize-power-bi/events-pie-chart.png)

Het is nu tijd om naar Power BI te kijken, maar u kunt nog veel meer doen met visuele elementen in Azure Data Explorer.

## <a name="connect-to-azure-data-explorer"></a>Verbinding maken met Azure Data Explorer

U maakt nu verbinding met Azure Data Explorer in Power BI Desktop.

1. Ga in Power BI Desktop naar het tabblad **Start**, selecteer de optie **Gegevens ophalen** en vervolgens **Meer**.

    ![Gegevens ophalen](media/visualize-power-bi/get-data-more.png)

1. Zoek naar *Azure Data Explorer*, selecteer **Azure Data Explorer (bèta)** en vervolgens **Verbinding maken**.

    ![Gegevens zoeken en ophalen](media/visualize-power-bi/search-get-data.png)

1. Selecteer in het scherm **Preview-connector** de opties **Doorgaan**.

1. Voer in het volgende scherm de naam van uw testcluster en database in. Clusters moeten de vorm `https://<ClusterName>.<Region>.kusto.windows.net` hebben. Voer als naam van de tabel *StormEvents* in. Laat voor alle andere opties de standaardwaarden staan en selecteer **OK**.

    ![Cluster-, database-, tabelopties](media/visualize-power-bi/cluster-database-table.png)

1. Selecteer **Bewerken** in het voorbeeldscherm voor gegevens.

    De tabel wordt geopend in Power Query-editor. Hier kunt u rijen en kolommen bewerken voordat u de gegevens importeert.

## <a name="work-with-data-in-power-bi-desktop"></a>Werken met gegevens in Power BI Desktop

Nu u een verbinding met Azure Data Explorer hebt, kunt u de gegevens bewerken in Power Query-editor. U sleept rijen met null-waarden naar de kolom **BeginLat** en sleept de **StormSummary**-JSON-kolom in zijn geheel. Dit zijn eenvoudige bewerkingen, maar u kunt ook complexe transformaties uitvoeren als u gegevens importeert.

1. Selecteer het pijltje bij de kolom **BeginLat**, schakel het selectievakje **null** uit en selecteer daarna **OK**.

    ![Kolom filteren](media/visualize-power-bi/filter-column.png)

1. Klik met de rechtermuisknop op de kolomkop **StormSummary** en selecteer vervolgens **Verwijderen**.

    ![Kolom verwijderen](media/visualize-power-bi/remove-column.png)

1. Wijzig in het deelvenster **QUERY-INSTELLINGEN** de naam van *Query1* naar *StormEvents*.

    ![Querynaam wijzigen](media/visualize-power-bi/query-name.png)

1. Selecteer op het tabblad **Home** op het lint **Sluiten en toepassen**.

    ![Sluiten en toepassen](media/visualize-power-bi/close-apply.png)

    Power Query past uw wijzigingen toe en importeert daarna de voorbeeldgegevens in een *gegevensmodel*. De volgende stappen laten u zien hoe u dat model kunt verrijken. Nogmaals, dit is gewoon een eenvoudig voorbeeld om een idee te geven van de mogelijkheden.

1. Selecteer de gegevensweergave aan de linkerkant van het hoofdvenster.

    ![Gegevensweergave](media/visualize-power-bi/data-view.png)

1. Selecteer op het tabblad **Modellering** op het lint de optie **Nieuwe kolom**.

    ![Nieuwe kolom](media/visualize-power-bi/new-column.png)

1. Voer de volgende formule voor expressie van gegevensanalyse (DAX) in de formulebalk in en druk daarna op Enter.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Formulebalk](media/visualize-power-bi/formula-bar.png)

    Deze formule maakt de kolom *DurationHours*, die berekent hoeveel uur elke weergebeurtenis duurde. U gebruikt deze kolom in een visueel element in het volgende gedeelte.

1. Schuif naar de rechterkant van de tabel om de kolom te zien.

## <a name="create-a-report-with-visuals"></a>Een rapport maken met visuele elementen

Nu de gegevens zijn geïmporteerd en u het gegevensmodel hebt verbeterd, is het tijd om een rapport met visuele elementen samen te stellen. U voegt een kolomdiagram op basis van de duur van gebeurtenis en een kaart over gewasschade toe.

1. Selecteer de rapportweergave aan de linkerkant van het venster.

    ![Rapportweergave](media/visualize-power-bi/report-view.png)

1. Selecteer in het deelvenster **VISUALISATIES** het gegroepeerde kolomdiagram.

    ![Kolomdiagram toevoegen](media/visualize-power-bi/add-column-chart.png)

    Er wordt een leeg diagram aan het tekenvel toegevoegd.

    ![Leeg diagram](media/visualize-power-bi/blank-chart.png)

1. Selecteer in de lijst **VELDEN** **DurationHours** en **State**.

    ![Velden selecteren](media/visualize-power-bi/select-fields.png)

    U hebt nu een diagram die het totaal aantal uren van weergebeurtenissen per staat weergeeft voor een jaar.

    ![Kolomdiagram voor duur](media/visualize-power-bi/duration-column-chart.png)

1. Klik ergens op het tekenveld buiten het kolomdiagram.

1. Selecteer de kaart in het deelvenster **VISUALISATIES**.

    ![Kaart toevoegen](media/visualize-power-bi/add-map.png)

1. Selecteer in de lijst **VELDEN** **CropDamage** en **State**. Wijzig het formaat van de kaart, zodat u de staten van de VS duidelijk kunt zien.

    ![Kaart met gewasschade](media/visualize-power-bi/crop-damage-map.png)

    De grootte van de bellen geeft de financiële waarde van gewasschade in dollars weer. Ga met de muis over de bellen om meer informatie weer te geven.

1. Verplaats en vergroot of verklein de visuele elementen, zodat u een rapport hebt dat op de volgende afbeelding lijkt.

    ![Voltooid rapport](media/visualize-power-bi/finished-report.png)

1. Sla het rapport op met de naam *storm-events.pbix*.

## <a name="publish-and-share-the-report"></a>Het rapport publiceren en delen

Tot nu toe zijn alle werkzaamheden die u in Power BI hebt uitgevoerd, lokaal geweest met behulp van Power BI Desktop. Nu publiceert u het rapport in de Power BI-service, waar u het kunt delen met anderen.

1. Selecteer op het tabblad **Start** in het lint van Power BI Desktop de optie **Publiceren**.

    ![De knop Publiceren](media/visualize-power-bi/publish-button.png)

1. Doorloop het aanmeldproces als u nog niet bent aangemeld bij Power BI.

1. Selecteer **Mijn werkruimte** en klik vervolgens op **Selecteren**.

    ![Werkruimte selecteren](media/visualize-power-bi/select-workspace.png)

1. Selecteer na het publiceren **storm-events.pbix openen in Power BI**.

    ![Het publiceren is voltooid](media/visualize-power-bi/publishing-succeeded.png)

    Het rapport wordt in de service geopend met dezelfde visuele elementen en indeling die u in Power BI Desktop hebt gedefinieerd.

1. Selecteer **Delen** in de rechterbovenhoek van het rapport.

    ![Knop Delen](media/visualize-power-bi/share-button.png)

1. Voeg in het scherm **Rapport delen** een collega in uw organisatie toe, voeg een opmerking toe en selecteer vervolgens **Delen**.

    ![Rapport delen](media/visualize-power-bi/share-report.png)

    Als uw collega de juiste machtigingen heeft, kan deze het rapport openen dat u hebt gedeeld.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het rapport dat u hebt gemaakt niet wilt houden, kunt u gewoon het bestand *storm-events.pbix* verwijderen. Volg deze stappen als u het rapport wilt verwijderen dat u hebt gepubliceerd.

1. Schuif onder **Mijn werkruimte** omlaag naar **RAPPORTEN** en zoek **storm-events**.

1. Selecteer het weglatingsteken ( **. . .** ) naast **storm-events** en selecteer vervolgens **VERWIJDEREN**.

    ![Rapport verwijderen](media/visualize-power-bi/remove-report.png)

1. Bevestig de verwijdering.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Query's schrijven](write-queries.md)
