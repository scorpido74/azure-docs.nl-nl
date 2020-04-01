---
title: Voorbeelden van gebruiksvoorbeelden van Microsoft Azure Azure Data Explorer Flow-connector (Preview)
description: Lees enkele voorbeelden van algemene voorbeelden van microsoft flow-connectorgebruikers.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 796b37f98fed7e389fa71a15b5e6697a14db1a16
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397196"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Voorbeelden van gebruiksvoorbeelden van Microsoft Flow-connector (Preview)

Met de Stroomconnector azure Data Explorer kan Azure Data Explorer de [stroommogelijkheden van Microsoft Power Automate](https://flow.microsoft.com/) gebruiken om Kusto-query's en -opdrachten automatisch uit te voeren als onderdeel van een geplande of geactiveerde taak. Dit document bevat verschillende algemene voorbeelden van microsoft flow-connectorgebruik.

Zie [Microsoft Flow-connector (Voorbeeld) voor](flow.md)meer informatie.

* [Microsoft Flow-connector en SQL](#microsoft-flow-connector-and-sql)
* [Gegevens pushen naar de Power BI-gegevensset](#push-data-to-power-bi-dataset)
* [Voorwaardelijke query's](#conditional-queries)
* [Meerdere Azure Data Explorer Flow-diagrammen e-mailen](#email-multiple-azure-data-explorer-flow-charts)
* [Een andere e-mail verzenden naar verschillende contactpersonen](#send-a-different-email-to-different-contacts)
* [Een aangepaste HTML-tabel maken](#create-a-custom-html-table)

## <a name="microsoft-flow-connector-and-sql"></a>Microsoft Flow-connector en SQL

Gebruik de Microsoft Flow-connector om uw gegevens op te vragen en te aggregeren in een SQL-database.

> [!Note]
> SQL-invoegsel wordt voor elke rij afzonderlijk gedaan. Gebruik de Microsoft Flow-connector alleen voor kleine hoeveelheden uitvoergegevens. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> Voer in het veld *Clusternaam* de cluster-URL in.

## <a name="push-data-to-power-bi-dataset"></a>Gegevens pushen naar de Power BI-gegevensset

De Microsoft Flow-connector kan samen met de Power BI-connector worden gebruikt om gegevens van Kusto-query's naar Power BI-streaminggegevenssets te pushen.

1. Maak een nieuwe actie Query uitvoeren en resultaten lijst.
1. Selecteer **Nieuwe stap**.
1. Selecteer **Een actie toevoegen** en zoeken naar Power BI.
1. Selecteer **Power BI**.
1. Selecteer **Rijen toevoegen aan een gegevensset**. 

    ![Flow Power BI-connector](./media/flow-usage/flow-powerbiconnector.png)
1. Voer de **werkruimte**, **gegevensset**en **tabel** in naar welke gegevens worden gepusht.
1. Voeg in het dialoogvenster Dynamische inhoud een payload toe met uw gegevenssetschema en de relevante Kusto-queryresultaten.

    ![Flow Power BI-velden](./media/flow-usage/flow-powerbifields.png)

Flow past automatisch de Power BI-actie toe voor elke rij van de kusto-queryresultatentabel. 

![Flow Power BI-actie voor elke rij](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Voorwaardelijke query's

De resultaten van Kusto-query's kunnen worden gebruikt als invoer of voorwaarden voor de volgende stroomacties.

In het volgende voorbeeld vragen we Kusto naar incidenten die zich tijdens de laatste dag hebben voorgedaan. Voor elk opgelost incident wordt een slap bericht geplaatst en wordt een pushmelding gemaakt.
Voor elk incident dat nog actief is, wordt Kusto opgevraagd voor meer informatie over vergelijkbare incidenten. Het stuurt die informatie als een e-mail en opent een gerelateerde TFS-taak.

Volg deze instructies om een vergelijkbare stroom te maken:

1. Maak een nieuwe actie Query uitvoeren en resultaten lijst.
1. Selecteer **Nieuwe stap**.
1. Selecteer **Voorwaardebesturingselement**.
1. Selecteer in het venster dynamische inhoud de parameter die u wilt gebruiken als voorwaarde voor volgende acties.
1. Selecteer het type *relatie* en *waarde* om een specifieke voorwaarde op de opgegeven parameter in te stellen.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow past deze voorwaarde toe op elke rij van de queryresultatentabel.
1. Acties toevoegen voor wanneer de voorwaarde waar en onwaar is.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

U de resultaatwaarden uit de Kusto-query gebruiken als invoer voor de volgende acties. Selecteer de resultaatwaarden in het venster dynamische inhoud.
In het onderstaande voorbeeld is een actie Slack - Post Message en Visual Studio - Een nieuwe actie voor werkitems gemaakt met gegevens uit de Kusto-query.

![Slack - Actie Bericht](./media/flow-usage/flow-slack.png)

![Visual Studio-actie](./media/flow-usage/flow-visualstudio.png)

Als een incident nog steeds actief is, vraagt u Kusto opnieuw om informatie te krijgen over hoe incidenten uit dezelfde bron in het verleden zijn opgelost.

![Query voor stroomvoorwaarde](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> Voer in het veld *Clusternaam* de cluster-URL in.

Visualiseer deze informatie als een cirkeldiagram en e-mail deze naar het team.

![E-mail voorwaarde voor stroom](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Meerdere Azure Data Explorer Flow-diagrammen e-mailen

1. Maak een nieuwe stroom met 'Recidief'-trigger en definieer het interval van de stroom en de frequentie. 
1. Voeg een nieuwe stap toe, met een of meer Kusto - Voer query-en -resultatenacties uit. 

    ![Meerdere query's uitvoeren in een stroom](./media/flow-usage/flow-severalqueries.png)
1. Definieer voor elke Kusto - Run query en visualiseer het resultaat de volgende velden:
    * Cluster-URL (in het veld *Clusternaam)*
    * Databasenaam
    * Query- en grafiektype (HTML-tabel/ cirkeldiagram/ tijdgrafiek/ staafdiagram/ Voer aangepaste waarde in).

    ![Resultaten visualiseren met meerdere bijlagen](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > Voer in de velden *Clusternaam* de cluster-URL in.

1. Een actie Een e-mail verzenden toevoegen. 
    * Plaats in het veld *Hoofdlichaam* de vereiste hoofdtekst zodat het gevisualiseerde resultaat van de query in de hoofdtekst van de e-mail wordt opgenomen.
    * Als u een bijlage aan de e-mail wilt toevoegen, voegt u bijlagenaam en bijlageinhoud toe.
    
    ![Meerdere bijlagen e-mailen](./media/flow-usage/flow-email-multiple-attachments.png)

    Zie [E-mailkusto-queryresultaten](flow.md#email-kusto-query-results)voor volledige instructies over het maken van een e-mailactie. 

Resultaten:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>Een andere e-mail verzenden naar verschillende contactpersonen

U Microsoft Flow gebruiken om verschillende aangepaste e-mails naar verschillende contactpersonen te verzenden. De e-mailadressen en de e-mailinhoud zijn het resultaat van een Kusto-query.

Voorbeeld:

![Dynamische e-mail met een Kusto-query](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> Voer in het veld *Clusternaam* de cluster-URL in.

![Dynamische e-mail in de stroomactie](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>Een aangepaste HTML-tabel maken

U Microsoft Flow gebruiken om aangepaste HTML-elementen te maken en te gebruiken, zoals een aangepaste HTML-tabel.

In het volgende voorbeeld wordt uitgelegd hoe u een aangepaste HTML-tabel maakt. In de HTML-tabel worden de rijen gekleurd op logboekniveau (hetzelfde als in Azure Data Explorer).

Volg deze instructies om een vergelijkbare stroom te maken:

1. Maak een nieuwe Kusto - Voer query- en lijstresultatenactie uit.

    ![Resultaten voor een HTML-tabel weergeven](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> Voer in het veld *Clusternaam* de cluster-URL in.

1. Loop over de queryresultaten en maak de HTML-tabeltekst: 
    1. Als u een variabele wilt maken om de HTML-tekenreeks vast te houden, selecteert u **Nieuwe stap**
    1. Selecteer **Een actie toevoegen** en zoeken naar variabelen. 
    1. Selecteer **Variabelen - Variabele initialiseren**. 
    1. Initialiseer een tekenreeksvariabele als volgt:

    ![Een variabele initialiseren](./media/flow-usage/flow-initializevariable.png)

1. Loop over de resultaten:
    1. Selecteer **Nieuwe stap**.
    1. Selecteer **Een actie toevoegen**.
    1. Zoek naar variabelen. 
    1. Selecteer **Variabelen - Toevoegen aan tekenreeksvariabele**. 
    1. Selecteer de variabele naam die u eerder hebt geïnitialeerd en maak de HTML-tabelrijen met behulp van de queryresultaten. 
    Bij het selecteren van de queryresultaten wordt Toepassen op elk automatisch toegevoegd.

    In het onderstaande `if` voorbeeld wordt de expressie gebruikt om de stijl van elke rij te definiëren:

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. De volledige HTML-inhoud maken: 
    1. Een nieuwe actie toevoegen buiten Toepassen op elke actie. 
    In het volgende voorbeeld wordt een e-mail verzenden.
    1. Definieer uw HTML-tabel met de variabele uit de vorige stappen. 
    1. Als u een e-mail verzendt, selecteert u **Geavanceerde opties weergeven** en selecteert u onder Is HTML **Ja**.

    ![Aangepaste HTML-tabele-mail](./media/flow-usage/flow-customhtmltablemail.png)

Resultaat:

![Aangepast HTML-tabele-e-mailresultaat](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Microsoft Azure Explorer Logic App-connector,](https://docs.microsoft.com/azure/kusto/tools/logicapps) een andere manier om Kusto-query's en -opdrachten automatisch uit te voeren als onderdeel van een geplande of geactiveerde taak.
