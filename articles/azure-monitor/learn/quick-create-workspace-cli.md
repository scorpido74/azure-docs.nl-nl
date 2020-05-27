---
title: Een Log Analytics-werk ruimte maken met behulp van Azure CLI | Microsoft Docs
description: Meer informatie over het maken van een Log Analytics-werk ruimte voor het inschakelen van beheer oplossingen en gegevens verzameling vanuit uw Cloud en on-premises omgevingen met Azure CLI.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 11fb2b7785540f24b0a8318428da01a4edd5cb5b
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860627"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Een Log Analytics-werk ruimte maken met Azure CLI 2,0

De Azure CLI 2.0 wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze Quick start ziet u hoe u met Azure CLI 2,0 een Log Analytics-werk ruimte implementeert in Azure Monitor. Een Log Analytics-werk ruimte is een unieke omgeving voor Azure Monitor logboek gegevens. Elke werk ruimte heeft een eigen gegevens opslagplaats en-configuratie, en gegevens bronnen en-oplossingen zijn geconfigureerd om hun gegevens op te slaan in een bepaalde werk ruimte. U hebt een Log Analytics-werk ruimte nodig als u van plan bent om gegevens te verzamelen uit de volgende bronnen:

* Azure-resources in uw abonnement  
* On-premises computers die worden bewaakt door System Center Operations Manager  
* Verzamelingen van apparaten uit Configuration Manager  
* Diagnose- of logboekgegevens van Azure Storage  

Zie de volgende onderwerpen voor andere bronnen, zoals virtuele Azure-machines en Windows-of Linux-Vm's in uw omgeving:

* [Gegevens verzamelen van virtuele machines van Azure](../learn/quick-collect-azurevm.md)
* [Gegevens verzamelen van een hybride Linux-computer](../learn/quick-collect-linux-computer.md)
* [Gegevens verzamelen van de hybride Windows-computer](quick-collect-windows-computer.md)

Als u nog geen abonnement op Azure hebt, maak dan [een gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart de Azure CLI versie 2.0.30 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Een werkruimte maken
Maak een werk ruimte met [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). In het volgende voor beeld wordt een werk ruimte op de locatie *ooster* gemaakt met behulp van een resource manager-sjabloon van uw lokale machine. De JSON-sjabloon is zo geconfigureerd dat alleen u wordt gevraagd om de naam van de werk ruimte en geeft een standaard waarde op voor de andere para meters die waarschijnlijk worden gebruikt als een standaard configuratie in uw omgeving. Of u kunt de sjabloon opslaan in een Azure-opslag account voor gedeelde toegang in uw organisatie. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../../azure-resource-manager/templates/deploy-cli.md) voor meer informatie over het werken met sjablonen.

Zie [regio's log Analytics is beschikbaar in](https://azure.microsoft.com/regions/services/) en zoek naar Azure monitor in het veld **zoeken naar een product** voor meer informatie over regio's die worden ondersteund.

Met de volgende para meters wordt een standaard waarde ingesteld:

* locatie-standaard naar VS-Oost
* SKU: wordt standaard ingesteld op de nieuwe prijs categorie per GB die is uitgebracht in het prijs model van april 2018

>[!WARNING]
>Als u een Log Analytics-werk ruimte maakt of configureert in een abonnement dat is aangemeld met het nieuwe prijs model van april 2018, is de enige geldige Log Analytics prijs categorie **PerGB2018**.
>

### <a name="create-and-deploy-template"></a>Een sjabloon maken en implementeren

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Bewerk de sjabloon om te voldoen aan uw vereisten. Raadpleeg de naslag informatie over [micro soft. OperationalInsights/werkruimte sjablonen](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) als u wilt weten welke eigenschappen en waarden worden ondersteund.
3. Sla dit bestand op als **deploylaworkspacetemplate. json** naar een lokale map.   
4. U kunt deze sjabloon nu implementeren. Gebruik de volgende opdrachten in de map met de sjabloon. Wanneer u wordt gevraagd om de naam van een werk ruimte, geeft u een naam op die wereld wijd uniek is voor alle Azure-abonnementen.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

De implementatie kan enkele minuten duren. Wanneer de bewerking is voltooid, ziet u een bericht dat lijkt op het volgende:

![Voor beeld van resultaat wanneer de implementatie is voltooid](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>Problemen oplossen
Wanneer u een werk ruimte maakt die in de afgelopen 14 dagen is verwijderd en de status voor het [voorlopig verwijderen](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#soft-delete-behavior)heeft, kan de bewerking afwijken, afhankelijk van de configuratie van uw werk ruimte:
1. Als u dezelfde naam voor de werk ruimte, de resource groep, het abonnement en de regio opgeeft als in de verwijderde werk ruimte, wordt uw werk ruimte hersteld, met inbegrip van de bijbehorende gegevens, configuratie en verbonden agents.
2. Als u dezelfde naam voor de werk ruimte gebruikt, maar een andere resource groep, abonnement of regio, krijgt u een fout melding *de naam van de werk ruimte is niet uniek*of *conflict*. Volg deze stappen om de werk ruimte eerst te herstellen en permanent verwijderen uit te voeren om de tijdelijke verwijdering te onderdrukken en uw werk ruimte permanent te verwijderen en een nieuwe werk ruimte met dezelfde naam te maken:
   * Uw werk ruimte [herstellen](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace)
   * Uw werk ruimte [permanent verwijderen](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete)
   * Een nieuwe werk ruimte maken met dezelfde werkruimte naam

## <a name="next-steps"></a>Volgende stappen
Nu u een werk ruimte beschikbaar hebt, kunt u een verzameling van de telemetrie-bewaking configureren, Zoek opdrachten in Logboeken uitvoeren om die gegevens te analyseren en een beheer oplossing toevoegen om extra gegevens en analytische inzichten te leveren.  

* Zie [Azure service-logboeken en-metrische gegevens verzamelen voor gebruik in log Analytics](../platform/collect-azure-metrics-logs.md)voor informatie over het inschakelen van het verzamelen van data van Azure-resources met Azure Diagnostics of Azure Storage.  
* Voeg [System Center Operations Manager als gegevens bron](../platform/om-agents.md) toe om gegevens te verzamelen van agents die uw Operations Manager beheer groep rapporteren en op te slaan in uw log Analytics-werk ruimte.  
* Verbind [Configuration Manager](../platform/collect-sccm.md) om computers te importeren die lid zijn van verzamelingen in de hiërarchie.  
* Bekijk de beschik bare [bewakings oplossingen](../insights/solutions.md) en hoe u een oplossing kunt toevoegen aan of verwijderen uit uw werk ruimte.
