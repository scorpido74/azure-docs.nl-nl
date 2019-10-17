---
title: Voer uw eerste query uit met Azure resource Graph Explorer
description: Dit artikel begeleidt u stapsgewijs door de stappen voor het uitvoeren van uw eerste query vanuit Azure Portal met behulp van Azure resource Graph Explorer.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 755556b9ba049da7542494ee580215d29c1eb5f4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387609"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Snelstartgids: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer

De kracht van Azure resource Graph is rechtstreeks beschikbaar in Azure Portal via Azure resource Graph Explorer. Resource Graph Explorer biedt doorzoekbaar informatie over de Azure Resource Manager resource typen en eigenschappen die u kunt opvragen. Resource Graph Explorer biedt ook een schone interface voor het werken met meerdere query's, het evalueren van de resultaten en het converteren van de resultaten van sommige query's in een grafiek die kan worden vastgemaakt aan een Azure-dash board.

Aan het einde van deze Quick Start hebt u Azure Portal en resource Graph Explorer gebruikt voor het uitvoeren van uw eerste resource grafiek query en de resultaten vastgemaakt aan een dash board.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Open de [Azure Portal](https://portal.azure.com) om de resource Graph Explorer te vinden en gebruiken volg deze stappen om uw eerste resource grafiek query uit te voeren:

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **resource Graph Explorer**.

1. Voer in het gedeelte **query 1** van het venster de query `Resources | project name, type | limit 5` in en selecteer **query uitvoeren**.

   > [!NOTE]
   > Zoals in dit voor beeld van een query geen sorteer volgorde biedt, zoals `order by`, is het uitvoeren van deze query waarschijnlijk meerdere malen dat een andere set resources per aanvraag kan worden verkregen.

1. Controleer het antwoord op de query op het tabblad **resultaten** . Selecteer het tabblad **berichten** om de details van de query te bekijken, inclusief het aantal resultaten en de duur van de query. Eventuele fouten worden weer gegeven op dit tabblad.

1. Werk de query bij naar `order by` de **naam** eigenschap: `Resources | project name, type | limit 5 | order by name asc`. Selecteer vervolgens **query uitvoeren**.

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Hiermee worden de queryresultaten eerst beperkt en daarna geordend.

1. Werk de query bij naar eerste `order by` de eigenschap **name** en vervolgens `limit` op de vijf belangrijkste resultaten: `Resources | project name, type | order by name asc | limit 5`. Selecteer vervolgens **query uitvoeren**.

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat er niets in uw omgeving wordt gewijzigd, worden de geretourneerde resultaten consistent en zoals verwacht: besteld op basis van de eigenschap **name** , maar is nog steeds beperkt tot de vijf belangrijkste resultaten.

### <a name="schema-browser"></a>Schema browser

De schema browser bevindt zich in het linkerdeel venster van de resource Graph Explorer. Deze lijst met resources bevat alle _resource typen_ van Azure-resources die beide worden ondersteund door Azure resource Graph en die zich bevinden in een Tenant waartoe u toegang hebt. Als u een resource type of subeigenschappen uitbreidt, worden onderliggende eigenschappen weer gegeven die kunnen worden gebruikt om een resource grafiek query te maken. Als u het resource type selecteert, wordt `where type =="<resource type>"` geplaatst in het query venster. Als u een van de onderliggende eigenschappen selecteert, wordt `where <propertyName> == "INSERT_VALUE_HERE"` toegevoegd aan het query-venster. De schema browser is een uitstekende manier om eigenschappen te ontdekken voor gebruik in query's. Zorg ervoor dat u _Insert @ no__t-1VALUE @ no__t-2HERE_ vervangt door uw eigen waarde, door de query aan te passen met voor waarden, Opera tors en functies om uw beoogde resultaten te behalen.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Een grafiek maken op basis van de resource grafiek query

Als u na het uitvoeren van de laatste query hierboven het tabblad **grafieken** selecteert, krijgt u een bericht dat de resultatenset niet compatibel is met een diagram van een cirkel diagram. Query's waarin de resultaten van een lijst worden weer gegeven, kunnen niet worden gemaakt in een grafiek, maar query's die tellingen van resources bieden, zijn mogelijk. Met behulp van het [voor beeld van het aantal virtuele machines voor het tellen van query's per OS-type](./samples/starter.md#count-virtual-machines-by-os-type), gaan we een visualisatie maken op basis van de resource Graph-query.

1. Voer in het gedeelte **query 1** van het venster de volgende query in en selecteer **query uitvoeren**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecteer het tabblad **resultaten** en houd er rekening mee dat de reactie op deze query aantallen bevat.

1. Selecteer het tabblad **grafieken** . De query resulteert nu in visualisaties. Wijzig het type van het _grafiek type selecteren..._ voor een _staaf diagram_ of een ring _diagram_ om te experimenteren met de beschik bare visualisatie opties.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>De query visualisatie vastmaken aan een dash board

Wanneer u resultaten hebt van een query die kan worden gevisualiseerd, kan deze gegevens visualisatie worden vastgemaakt aan een van uw Dash boards. Voer de volgende stappen uit nadat u de bovenstaande query hebt uitgevoerd:

1. Selecteer **Opslaan** en geef de naam ' VM'S per OS-type ' op. Selecteer vervolgens **Opslaan** onder aan het rechterdeel venster.

1. Selecteer **query uitvoeren** om de query nu opnieuw uit te voeren, zodat deze is opgeslagen.

1. Selecteer op het tabblad **grafieken** een gegevens visualisatie. Selecteer vervolgens **vastmaken aan dash board**.

1. Selecteer de portal melding die wordt weer gegeven of selecteer **dash board** in het linkerdeel venster.

De query is nu beschikbaar op uw dash board met de titel van de tegel die overeenkomt met de naam van de query. Als de query niet is opgeslagen toen deze werd vastgemaakt, wordt in plaats daarvan ' query 1 ' genoemd.

De query en de resulterende gegevens visualisatie worden telkens uitgevoerd en bijgewerkt wanneer het dash board wordt geladen, waardoor realtime en dynamische inzichten in uw Azure-omgeving rechtstreeks in uw werk stroom worden geboden.

> [!NOTE]
> Query's die resulteren in een lijst kunnen ook worden vastgemaakt aan het dash board. De functie is niet beperkt tot gegevens visualisaties van query's.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Voor beeld van resource Graph Explorer-Dash boards importeren

Als u voor beelden van resource Graph-query's en hoe resource Graph Explorer kan worden gebruikt om uw Azure Portal werk stroom te verbeteren, kunt u deze voorbeeld dashboards uitproberen.

- [Resource grafiek Verkenner-voorbeeld dashboard #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![Example-afbeelding voor voorbeeld dashboard #1](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Resource grafiek Verkenner-voorbeeld Dashboard #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![Example-afbeelding voor voorbeeld Dashboard #2](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> Het aantal en de grafieken in het bovenstaande voor beeld van Dashboard afbeeldingen zijn afhankelijk van uw Azure-omgeving.

1. Selecteer en down load het voorbeeld dashboard dat u wilt evalueren.

1. Selecteer in Azure Portal **dash board** in het linkerdeel venster.

1. Selecteer **uploaden**, zoek naar het gedownloade voorbeeld dashboard bestand en selecteer dit. Selecteer vervolgens **openen**.

Het geïmporteerde dash board wordt automatisch weer gegeven. Omdat deze nu aanwezig is in uw Azure Portal, kunt u naar behoefte verkennen en wijzigingen aanbrengen of nieuwe Dash boards maken op basis van het voor beeld om te delen met uw teams. Zie [Dash boards maken en delen in de Azure Portal](../../azure-portal/azure-portal-dashboards.md)voor meer informatie over het werken met Dash boards.

## <a name="clean-up-resources"></a>Opschonen van resources

Als u de voorbeeld resource grafiek dashboards uit uw Azure Portal omgeving wilt verwijderen, kunt u dit doen door de volgende stappen uit te voeren:

1. Selecteer **dash board** in het linkerdeel venster.

1. Selecteer in de vervolg keuzelijst dash board het voorbeeld Dashboard van de resource die u wilt verwijderen.

1. Selecteer **verwijderen** in het menu dash board aan de bovenkant van het dash board en selecteer **OK** om te bevestigen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [querytaal](./concepts/query-language.md)
- [Resources verkennen](./concepts/explore-resources.md)
- Uw eerste query uitvoeren met [Azure CLI](first-query-azurecli.md)
- Voorbeelden uit [Starter query's](./samples/starter.md) bekijken
- Voorbeelden uit [Geavanceerde query's](./samples/advanced.md) bekijken
- Feedback geven op [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)