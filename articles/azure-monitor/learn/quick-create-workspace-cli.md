---
title: Een Log Analytics-werkruimte met behulp van Azure CLI maken | Microsoft Docs
description: Informatie over het maken van een werkruimte voor logboekanalyse voor het beheer van oplossingen en gegevens te verzamelen van uw cloud en on-premises omgevingen met Azure CLI.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: b696d57919383e87f8e5e647b774fc9e4dbdf16b
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513470"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Een Log Analytics-werkruimte maken met Azure CLI 2.0

De Azure CLI 2.0 wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze Quick start ziet u hoe u met Azure CLI 2,0 een Log Analytics-werk ruimte implementeert in Azure Monitor. Een Log Analytics-werk ruimte is een unieke omgeving voor Azure Monitor logboek gegevens. Elke werk ruimte heeft een eigen gegevens opslagplaats en-configuratie, en gegevens bronnen en-oplossingen zijn geconfigureerd om hun gegevens op te slaan in een bepaalde werk ruimte. U hebt een Log Analytics-werk ruimte nodig als u van plan bent om gegevens te verzamelen uit de volgende bronnen:

* Azure-resources in uw abonnement  
* On-premises computers bewaakt door System Center Operations Manager  
* Verzamelingen van apparaten uit Configuration Manager  
* Diagnose- of logboekgegevens van Azure Storage  

Zie de volgende onderwerpen voor andere bronnen, zoals Azure-VM's en Windows of Linux-VM's in uw omgeving:

* [Verzamelen van gegevens van virtuele machines van Azure](../learn/quick-collect-azurevm.md)
* [Gegevens verzamelen van hybride Linux-computer](../learn/quick-collect-linux-computer.md)
* [Gegevens verzamelen van hybride Windows-computer](quick-collect-windows-computer.md)

Als u geen Azure-abonnement hebt, maakt u [een gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart de Azure CLI versie 2.0.30 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Een werkruimte maken
Maak een werk ruimte met [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). In het volgende voor beeld wordt een werk ruimte op de locatie *ooster* gemaakt met behulp van een resource manager-sjabloon van uw lokale machine. De JSON-sjabloon is geconfigureerd om te vragen u alleen voor de naam van de werkruimte en een standaardwaarde opgegeven voor de andere parameters die waarschijnlijk moet worden gebruikt als een standaardconfiguratie in uw omgeving. Of u kunt de sjabloon opslaan in Azure storage-account voor gedeelde toegang in uw organisatie. Zie voor meer informatie over het werken met sjablonen [resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Zie [regio's log Analytics is beschikbaar in](https://azure.microsoft.com/regions/services/) en zoek naar Azure monitor in het veld **zoeken naar een product** voor meer informatie over regio's die worden ondersteund.

De volgende parameters instelt een standaardwaarde:

* locatie - standaard ingesteld op VS-Oost
* SKU - standaard ingesteld op de nieuwe prijzen Per GB-laag met de uitgebracht in het prijsmodel van April 2018

>[!WARNING]
>Als het maken of configureren van een Log Analytics-werkruimte in een abonnement dat is deelneemt aan het nieuwe prijsmodel April 2018, is het de enige geldige Log Analytics prijscategorie **PerGB2018**.
>

### <a name="create-and-deploy-template"></a>Sjabloon maken en implementeren

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

2. De sjabloon bijwerken om aan uw eisen voldoen. Beoordeling [Microsoft.OperationalInsights/workspaces sjabloon](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) verwijzing voor meer informatie over welke eigenschappen en waarden worden ondersteund.
3. Sla dit bestand als **deploylaworkspacetemplate.json** naar een lokale map.   
4. U kunt deze sjabloon nu implementeren. Gebruik de volgende opdrachten in de map met de sjabloon. Wanneer u wordt gevraagd om de naam van een werk ruimte, geeft u een naam op die wereld wijd uniek is voor alle Azure-abonnementen.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

De implementatie kan enkele minuten duren. Als deze is voltooid, ziet u een bericht dat lijkt op de volgende mogelijkheden van het resultaat:

![Voorbeeld van resultaat wanneer de implementatie is voltooid](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Volgende stappen
Nu dat u een werkruimte beschikbaar hebt, kunt u verzamelen van telemetrie bewaking configureren, uitvoeren van zoekopdrachten in Logboeken om die gegevens te analyseren en toevoegen van een oplossing om aanvullende gegevens en analytische informatie te bieden.  

* Zie voor het inschakelen van verzamelen van gegevens uit Azure-resources met Azure Diagnostics of Azure storage [Azure verzamelen van Logboeken en metrische gegevens voor gebruik in Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Voeg [System Center Operations Manager als een gegevensbron](../platform/om-agents.md) voor het verzamelen van gegevens van agents die rapporteren van uw Operations Manager-beheergroep en sla deze op in uw Log Analytics-werkruimte.  
* Verbinding maken met [Configuration Manager](../platform/collect-sccm.md) voor het importeren van computers die lid van verzamelingen in de hiërarchie zijn.  
* Bekijk de beschik bare [bewakings oplossingen](../insights/solutions.md) en hoe u een oplossing kunt toevoegen aan of verwijderen uit uw werk ruimte.
