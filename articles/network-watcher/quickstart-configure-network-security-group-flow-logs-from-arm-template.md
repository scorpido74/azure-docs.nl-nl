---
title: 'Quickstart: NSG-stroomlogboeken configureren door de Azure Resource Manager-sjabloon te gebruiken'
description: Ontdek hoe u NSG-stroomlogboeken programmatisch kunt inschakelen met behulp van een Azure Resource Manager-sjabloon en Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87986314"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Quickstart: NSG-stroomlogboeken configureren met een ARM-sjabloon

In deze quickstart schakelt u [NSG-stroomlogboeken](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programmatisch in met behulp van een [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)-sjabloon (ARM-sjabloon) en Azure PowerShell. 

We beginnen door een overzicht te geven van de eigenschappen van het object NSG-stroomlogboeken, gevolgd door een paar voorbeeldsjablonen. Vervolgens implementeren we de sjabloon met behulp van een lokaal PowerShell-exemplaar.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="nsg-flow-logs-object"></a>Object NSG-stroomlogboeken

Hieronder wordt het object NSG-stroomlogboeken met alle parameters weergegeven.
Voor een volledig overzicht van de eigenschappen kunt u de [Sjabloonreferentie NSG-stroomlogboeken](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs) lezen.

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
Om een Microsoft.Network/networkWatchers/flowLogs-resource te maken, voegt u de bovenstaande JSON toe aan de sectie Resources van uw sjabloon.


## <a name="creating-your-template"></a>Uw sjabloon maken

Als u voor het eerst Azure Resource Manager-sjablonen gebruikt, kunt u er meer informatie over vinden via de onderstaande koppelingen.

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Zelfstudie: Uw eerste Azure Resource Manager-sjabloon maken en implementeren](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Het onderstaande voorbeeld van een volledige sjabloon is de eenvoudigste versie waarbij minimale parameters zijn doorgegeven om NSG-stroomlogboeken in te stellen. Voor meer voorbeelden gaat u naar deze [koppeling](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager).

**Voorbeeld**: Met de onderstaande sjabloon worden NSG-stroomlogboeken ingeschakeld op een doel-NSG en worden ze opgeslagen in een bepaald opslagaccount.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> * De naam van de resource heeft de indeling ‘Bovenliggende Resource_Onderliggende resource’. Hier is de bovenliggende resource het regionale Network Watcher-exemplaar (Indeling: NetworkWatcher_Regionaam. Voorbeeld: NetworkWatcher_vscentraaleuap)
> * targetResourceId is de resource-id van de doel-NSG
> * storageId is de resource-id van het doelopslagaccount


## <a name="deploying-your-azure-resource-manager-template"></a>Uw Azure Resource Manager-sjabloon implementeren

In deze zelfstudie wordt ervan uitgegaan dat u een bestaande resourcegroep hebt en een NSG waarvoor u stroomlogboekregistratie kunt inschakelen.
U kunt elk van de bovenstaande voorbeeldsjablonen lokaal opslaan als `azuredeploy.json`. Werk de eigenschapswaarden bij zodat ze naar geldige resources in uw abonnement wijzen.

Voer de volgende opdracht in PowerShell uit om de sjabloon te implementeren.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Met de bovenstaande opdrachten wordt een resource geïmplementeerd in de resourcegroep NetworkWatcherRG en niet in de resourcegroep die de NSG bevat


## <a name="validate-the-deployment"></a>De implementatie valideren

Er zijn een paar manieren om te controleren of uw implementatie is geslaagd. Uw PowerShell-console zou ‘Geslaagd’ moeten weergegeven bij ‘ProvisioningState’. Daarnaast kunt u de [portalpagina NSG-stroomlogboeken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) bezoeken om uw wijzigingen te bevestigen. Als er problemen zijn met de implementatie, raadpleegt u [Veelvoorkomende fouten in Azure-implementaties met Azure Resource Manager oplossen](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Uw resource verwijderen
Azure maakt resourceverwijdering mogelijk via de implementatiemodus ‘Volledig’. Om een Stroomlogboeken-resource te verwijderen, specificeert u een implementatie in de modus Volledig zonder de resource op te nemen die u wilt verwijderen. Lees meer over de [implementatiemodus Volledig](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

U kunt ook een NSG-stroomlogboek uitschakelen in de Azure-portal door de onderstaande stappen te volgen:
1. Meld u aan bij de Azure-portal
2. Selecteer in de linkerbovenhoek van de portal de optie **Alle services**. Typ *Network Watcher* in het vak **Filteren**. Selecteer **Network Watcher** in de zoekresultaten.
3. Selecteer onder **LOGBOEKEN** de optie **NSG-stroomlogboeken**.
4. Selecteer in de lijst met NSG’s de NSG waarvoor u stroomlogboeken wilt uitschakelen
5. Stel onder **Instellingen voor stroomlogboeken** de stroomlogboekstatus in op **Uit**.
6. Schuif omlaag en selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u NSG-stroomlogboeken ingeschakeld. Nu moet u leren hoe u uw NSG-stroomgegevens visualiseert met behulp van: 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Opensource-hulpprogramma’s](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
