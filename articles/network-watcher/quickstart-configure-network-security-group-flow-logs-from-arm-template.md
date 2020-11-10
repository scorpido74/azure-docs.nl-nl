---
title: 'Quickstart: NSG-stroomlogboeken configureren door de Azure Resource Manager-sjabloon te gebruiken'
description: Ontdek hoe u NSG-stroomlogboeken programmatisch kunt inschakelen met behulp van een Azure Resource Manager-sjabloon(ARM-sjabloon) en Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042758"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Quickstart: NSG-stroomlogboeken configureren met een ARM-sjabloon

In deze quickstart schakelt u [NSG-stroomlogboeken](network-watcher-nsg-flow-logging-overview.md) in met behulp van een [Azure Resource Manager](../azure-resource-manager/management/overview.md)-sjabloon (ARM-sjabloon) en Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

We beginnen door een overzicht te geven van de eigenschappen van het object NSG-stroomlogboeken, gevolgd door een paar voorbeeldsjablonen. Vervolgens implementeren we de sjabloon met behulp van een lokaal PowerShell-exemplaar.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Meerdere resources worden in de sjabloon gedefinieerd:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Object NSG-stroomlogboeken

Hieronder wordt het object NSG-stroomlogboeken met alle parameters weergegeven. Zie [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs)voor een volledig overzicht van de eigenschappen.

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
        "enabled": "boolean",
        "workspaceResourceId": "string",
        "trafficAnalyticsInterval": "integer"
      },
      "retentionPolicy": {
        "days": "integer",
        "enabled": "boolean"
      },
      "format": {
        "type": "string",
        "version": "integer"
      }
    }
  }
}
```

Om een `Microsoft.Network/networkWatchers/flowLogs`-resource te maken, voegt u de bovenstaande JSON toe aan de sectie Resources van uw sjabloon.

## <a name="creating-your-template"></a>Uw sjabloon maken

Als u voor het eerst ARM-sjablonen gebruikt, kunt u er meer informatie over vinden via de onderstaande koppelingen.

- [Resources implementeren met ARM-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Zelfstudie: uw eerste ARM-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Het onderstaande voorbeeld van een volledige sjabloon is de eenvoudigste versie waarbij minimale parameters zijn doorgegeven om NSG-stroomlogboeken in te stellen. Voor meer voorbeelden gaat u naar deze [instructiegids](network-watcher-nsg-flow-logging-azure-resource-manager.md).

**Voorbeeld** : Met de onderstaande sjabloon worden NSG-stroomlogboeken ingeschakeld op een doel-NSG en worden ze opgeslagen in een bepaald opslagaccount.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {},
        "retentionPolicy": {},
        "format": {}
      }
    }
  ]
}
```

> [!NOTE]
> - De naam van de resource heeft de indeling _Bovenliggende Resource_Onderliggende resource_. Hier is de bovenliggende resource het regionale Network Watcher-exemplaar (Indeling: NetworkWatcher_Regionaam. Voorbeeld: NetworkWatcher_vscentraaleuap)
> - `targetResourceId` is de resource-id van de doel-NSG.
> - `storageId` is de resource-id van het doelopslagaccount.

## <a name="deploy-the-template"></a>De sjabloon implementeren

In deze zelfstudie wordt ervan uitgegaan dat u een bestaande resourcegroep hebt en een NSG waarvoor u stroomlogboekregistratie kunt inschakelen.
U kunt elk van de bovenstaande voorbeeldsjablonen lokaal opslaan als `azuredeploy.json`. Werk de eigenschapswaarden bij zodat ze naar geldige resources in uw abonnement wijzen.

Voer de volgende opdracht in PowerShell uit om de sjabloon te implementeren.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Met de bovenstaande opdrachten wordt een resource geïmplementeerd in de resourcegroep NetworkWatcherRG en niet in de resourcegroep die de NSG bevat

## <a name="validate-the-deployment"></a>De implementatie valideren

Er zijn een paar manieren om te controleren of uw implementatie is geslaagd. De PowerShell-console moet `ProvisioningState` weergeven als `Succeeded`. Daarnaast kunt u de [portalpagina NSG-stroomlogboeken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) bezoeken om uw wijzigingen te bevestigen. Als er problemen zijn met de implementatie, raadpleegt u [Veelvoorkomende fouten in Azure-implementaties met Azure Resource Manager oplossen](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Resources opschonen

Azure maakt resourceverwijdering mogelijk via de implementatiemodus `Complete`. Om een Stroomlogboeken-resource te verwijderen, specificeert u een implementatie in de modus `Complete` zonder de resource op te nemen die u wilt verwijderen. Lees meer over de [implementatiemodus Volledig](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

U kunt ook een NSG-stroomlogboek uitschakelen in de Azure-portal door de onderstaande stappen te volgen:

1. Meld u aan bij de Azure-portal
1. Selecteer in de linkerbovenhoek van de portal de optie **Alle services**. Typ _Network Watcher_ in het vak **Filteren**. Selecteer **Network Watcher** in de zoekresultaten.
1. Selecteer onder **LOGBOEKEN** de optie **NSG-stroomlogboeken**.
1. Selecteer in de lijst met NSG’s de NSG waarvoor u stroomlogboeken wilt uitschakelen.
1. Stel onder **Instellingen voor stroomlogboeken** de stroomlogboekstatus in op **Uit**.
1. Schuif omlaag en selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u NSG-stroomlogboeken ingeschakeld. Nu moet u leren hoe u uw NSG-stroomgegevens visualiseert met behulp van:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Opensource-hulpprogramma’s](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Traffic Analytics](traffic-analytics.md)
