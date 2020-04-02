---
title: Voorbeelden van gebruiksvoorbeelden van Microsoft Azure Azure Data Explorer Flow-connector (Preview)
description: Lees enkele voorbeelden van algemene voorbeelden van microsoft flow-connectorgebruikers.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 1b9d593b0f0895e2ba75fae7ab7e78ea883c8907
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521697"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Voorbeelden van gebruiksvoorbeelden van Microsoft Flow-connector (Preview)

Met de Stroomconnector azure Data Explorer kan Azure Data Explorer de [stroommogelijkheden van Microsoft Power Automate](https://flow.microsoft.com/) gebruiken om Kusto-query's en -opdrachten automatisch uit te voeren als onderdeel van een geplande of geactiveerde taak. Dit document bevat verschillende algemene voorbeelden van microsoft flow-connectorgebruik.

Zie [Microsoft Flow-connector (Voorbeeld) voor](flow.md)meer informatie.

* [Microsoft Flow-connector en SQL](#microsoft-flow-connector-and-sql)
* [Gegevens pushen naar de Power BI-gegevensset](#push-data-to-power-bi-dataset)
* [Voorwaardelijke query's](#conditional-queries)
* [Meerdere Azure Data Explorer Flow-diagrammen e-mailen](#email-multiple-azure-data-explorer-flow-charts)

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

1. Maak een nieuwe stroom met de recidieftrigger en definieer het interval van de stroom en de frequentie. 
1. Voeg een nieuwe stap toe, met een of meer Kusto - Voer query-en -resultatenacties uit. 

    ![Meerdere query's uitvoeren in een stroom](./media/flow-usage/flow-severalqueries.png)
1. Definieer voor elke Kusto - Run query en visualiseer het resultaat de volgende velden:
    * Cluster-URL
    * Databasenaam
    * Query en grafiektype (HTML-tabel, cirkeldiagram, tijdgrafiek, staafdiagram of voer een aangepaste waarde in).

    ![Resultaten visualiseren met meerdere bijlagen](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Voeg een actie Een e-mail verzenden (v2) toe: 
    1. Selecteer in de hoofdsectie het pictogram codeweergave.
    1. Plaats in het veld **Hoofdtekst** de vereiste BodyHtml in, zodat het gevisualiseerde resultaat van de query is opgenomen in de hoofdtekst van de e-mail.
    1. Als u een bijlage aan de e-mail wilt toevoegen, voegt u bijlagenaam en bijlageinhoud toe.
    
    ![Meerdere bijlagen e-mailen](./media/flow-usage/flow-email-multiple-attachments.png)

    Zie [E-mailkusto-queryresultaten](flow.md#email-kusto-query-results)voor volledige instructies over het maken van een e-mailactie. 

Resultaten:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Microsoft Azure Explorer Logic App-connector,](https://docs.microsoft.com/azure/kusto/tools/logicapps) een andere manier om Kusto-query's en -opdrachten automatisch uit te voeren als onderdeel van een geplande of geactiveerde taak.
