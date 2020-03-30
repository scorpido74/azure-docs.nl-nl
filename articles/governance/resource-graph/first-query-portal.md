---
title: 'Snelstart: uw eerste portalquery'
description: In deze snelstart volgt u de stappen om uw eerste query vanuit Azure-portal uit te voeren met Azure Resource Graph Explorer.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: 5cf355e78ad51e06d7ba27d48dd352f35b4c0740
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74406800"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Snelstart: uw eerste resourcegrafiekquery uitvoeren met Azure Resource Graph Explorer

De kracht van Azure Resource Graph is rechtstreeks beschikbaar in Azure-portal via Azure Resource Graph Explorer. Resource Grafiek verkenner bevat voorop staande informatie over de azure resource manager-brontypen en -eigenschappen die u opvragen. Resource Graph Explorer biedt ook een schone interface voor het werken met meerdere query's, het evalueren van de resultaten en zelfs het omzetten van de resultaten van sommige query's in een grafiek die kan worden vastgemaakt aan een Azure-dashboard.

Aan het einde van deze quickstart hebt u Azure portal en Resource Graph Explorer gebruikt om uw eerste Resource Graph-query uit te voeren en de resultaten vast te maken aan een dashboard.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Open de [Azure-portal](https://portal.azure.com) om de Resource Graph Explorer te zoeken en te gebruiken volgens de volgende stappen om uw eerste ResourceGraph-query uit te voeren:

1. Selecteer **Alle services** in het linkerdeelvenster. Zoeken naar en selecteer **Resource Graph Explorer**.

1. Voer in het gedeelte **Query 1** `Resources | project name, type | limit 5` van het venster de query in en selecteer **Query uitvoeren**.

   > [!NOTE]
   > Aangezien dit queryvoorbeeld geen sorteermodifier biedt, zoals `order by`, levert het meerdere keren uitvoeren van deze query waarschijnlijk een andere set resources per aanvraag op.

1. Controleer het queryantwoord op het tabblad **Resultaten.** Selecteer het tabblad **Berichten** om details over de query te bekijken, inclusief het aantal resultaten en de duur van de query. Eventuele fouten worden onder dit tabblad weergegeven.

1. Werk de `order by` query bij `Resources | project name, type | limit 5 | order by name asc`naar de eigenschap **Naam:** . Selecteer vervolgens **Query uitvoeren**.

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Hiermee worden de queryresultaten eerst beperkt en daarna geordend.

1. Werk de query `order by` bij naar `limit` eerst de eigenschap `Resources | project name, type | order by name asc | limit 5` **Naam** en vervolgens naar de top vijf resultaten: . Selecteer vervolgens **Query uitvoeren**.

Wanneer de uiteindelijke query meerdere keren wordt uitgevoerd, ervan uitgaande dat er niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en zoals verwacht - geordend door de eigenschap **Naam,** maar nog steeds beperkt tot de top vijf resultaten.

### <a name="schema-browser"></a>Schemabrowser

De schemabrowser bevindt zich in het linkerdeelvenster van Resource Graph Explorer. In deze lijst met resources worden alle _resourcetypen_ Azure-bronnen weergegeven die beide worden ondersteund door Azure Resource Graph en die bestaan in een tenant waartoe u toegang hebt. Als u een resourcetype of subeigenschappen uitbreidt, worden onderliggende eigenschappen weergegeven die kunnen worden gebruikt om een resourcegrafiekquery te maken.

Het resourcetype `where type =="<resource type>"` plaatsen selecteren in het queryvak. Als u een van `where <propertyName> == "INSERT_VALUE_HERE"` de onderliggende eigenschappen selecteert, wordt dit toegevoegd in het queryvak.
De schemabrowser is een geweldige manier om eigenschappen te ontdekken voor gebruik in query's. Zorg ervoor dat u _INSERT\_VALUE\_HERE_ vervangt door uw eigen waarde, de query aanpast aan de voorwaarden, operators en functies om uw beoogde resultaten te bereiken.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Een grafiek maken op basis van de resourcegrafiekquery

Als u de laatste query hierboven hebt uitgevoerd, krijgt u een bericht dat 'de resultaatset niet compatibel is met een cirkeldiagramvisualisatie', als u het tabblad **Grafieken** selecteert. Query's met resultaten kunnen niet in een grafiek worden gemaakt, maar query's met tellingen van resources wel. Met behulp van de [voorbeeldquery - Virtuele machines tellen op OS-type](./samples/starter.md#count-virtual-machines-by-os-type), maken we een visualisatie op basis van de resourcegrafiekquery.

1. Voer in het gedeelte **Query 1** van het venster de volgende query in en selecteer **Query uitvoeren**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecteer het tabblad **Resultaten** en houd er rekening mee dat het antwoord voor deze query telt.

1. Selecteer het tabblad **Grafieken.** Nu resulteert de query in visualisaties. Wijzig het type van _Grafiektype selecteren..._ in _het staafdiagram_ of _het donutdiagram_ om te experimenteren met de beschikbare visualisatieopties.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>De queryvisualisatie vastmaken aan een dashboard

Wanneer u resultaten hebt van een query die kan worden gevisualiseerd, kan die gegevensvisualisatie vervolgens worden vastgemaakt aan een van uw dashboards. Voer de volgende stappen uit nadat u de bovenstaande query hebt uitgevoerd:

1. Selecteer **Opslaan** en geef de naam 'VM's op OS-type' op. Selecteer **vervolgens Opslaan** onder aan het rechterdeelvenster.

1. Selecteer **Query uitvoeren** om de query opnieuw uit te voeren nu deze is opgeslagen.

1. Selecteer op het tabblad **Grafieken** een gegevensvisualisatie. Selecteer vervolgens **Vastmaken aan het dashboard**.

1. Selecteer de portalmelding die wordt weergegeven of selecteer **Dashboard** in het linkerdeelvenster.

De query is nu beschikbaar op uw dashboard met de titel van de tegel die overeenkomt met de querynaam. Als de query niet is opgeslagen toen deze werd vastgemaakt, wordt deze in plaats daarvan 'Query 1' genoemd.

De query- en resulterende gegevensvisualisatie wordt uitgevoerd en bijgewerkt elke keer dat het dashboard wordt geladen, waardoor uw Azure-omgeving rechtstreeks in uw werkstroom realtime en dynamische inzichten krijgt.

> [!NOTE]
> Query's die resulteren in een lijst kunnen ook worden vastgemaakt aan het dashboard. De functie is niet beperkt tot gegevensvisualisaties van query's.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Voorbeeld Resource Graph Explorer-dashboards importeren

Probeer deze voorbeelddashboards om voorbeelden te geven van Resource Graph-query's en hoe Resource Graph Explorer kan worden gebruikt om uw Azure-portalwerkstroom te verbeteren.

- [Brongrafiekverkenner - Voorbeeld dashboard #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![Voorbeeldafbeelding voor voorbeelddashboard#1](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Brongrafiekverkenner - Voorbeeld dashboard #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![Voorbeeldafbeelding voor voorbeelddashboard#2](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> Het aantal tellingen en grafieken in de bovenstaande voorbeelddashboardscreenshots is afhankelijk van uw Azure-omgeving.

1. Selecteer en download het voorbeelddashboard dat u wilt evalueren.

1. Selecteer **Dashboard** in het linkerdeelvenster in Azure-portal.

1. Selecteer **Uploaden**en zoek en selecteer het gedownloade voorbeelddashboardbestand. Selecteer vervolgens **Openen**.

Het geïmporteerde dashboard wordt automatisch weergegeven. Aangezien deze nu in uw Azure-portal aanwezig is, u desbehoefte verkennen en wijzigingen aanbrengen of nieuwe dashboards maken in het voorbeeld om te delen met uw teams. Zie [Dashboards maken en delen in de Azure-portal](../../azure-portal/azure-portal-dashboards.md)voor meer informatie over het werken met dashboards.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de voorbeelddashboards van Resource Graph uit uw Azure-portalomgeving wilt verwijderen, u dit doen met de volgende stappen:

1. Selecteer **Dashboard** in het linkerdeelvenster.

1. Selecteer in de vervolgkeuzelijst dashboard het voorbeeld van resourcegrafiekdashboard dat u wilt verwijderen.

1. Selecteer **Verwijderen** in het dashboardmenu boven aan het dashboard en selecteer **Ok** om te bevestigen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u Azure Resource Graph Explorer gebruikt om uw eerste query uit te voeren en hebt u voorbeelden van dashboards bekeken die worden aangedreven door Resource Graph. Ga voor meer informatie over de taal van de resourcegrafiek verder naar de pagina met details van de querytaal.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)