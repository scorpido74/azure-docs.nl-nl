---
title: Een Logboekanalysewerkruimte maken met Azure CLI | Microsoft Documenten
description: Meer informatie over het maken van een Log Analytics-werkruimte om beheeroplossingen en gegevensverzameling uit uw cloud- en on-premises omgevingen met Azure CLI mogelijk te maken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 89d397574c423e28bcbb0fec5ddd45959a737a93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659880"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Een Log Analytics-werkruimte maken met Azure CLI 2.0

De Azure CLI 2.0 wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. Met deze snelstart ziet u hoe u Azure CLI 2.0 gebruiken om een Log Analytics-werkruimte in Azure Monitor te implementeren. Een Log Analytics-werkruimte is een unieke omgeving voor Azure Monitor-logboekgegevens. Elke werkruimte heeft zijn eigen gegevensopslagplaats en -configuratie en gegevensbronnen en -oplossingen zijn geconfigureerd om hun gegevens in een bepaalde werkruimte op te slaan. U hebt een Log Analytics-werkruimte nodig als u gegevens wilt verzamelen uit de volgende bronnen:

* Azure-bronnen in uw abonnement  
* On-premises computers gecontroleerd door System Center Operations Manager  
* Apparaatverzamelingen van Configuration Manager  
* Diagnose- of logboekgegevens van Azure Storage  

Zie de volgende onderwerpen voor andere bronnen, zoals Azure VM's en Windows- of Linux-VM's in uw omgeving:

* [Gegevens verzamelen van virtuele Azure-machines](../learn/quick-collect-azurevm.md)
* [Gegevens verzamelen van hybride Linux-computer](../learn/quick-collect-linux-computer.md)
* [Gegevens verzamelen van hybride Windows-computer](quick-collect-windows-computer.md)

Als u geen Azure-abonnement hebt, maakt u [een gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart de Azure CLI versie 2.0.30 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Een werkruimte maken
Maak een werkruimte met [de implementatie van AZ-groepen.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) In het volgende voorbeeld wordt een werkruimte op de *locatie Eastus* gemaakt met behulp van een resourcemanagersjabloon van uw lokale machine. De JSON-sjabloon is geconfigureerd om u alleen om de naam van de werkruimte te vragen en geeft een standaardwaarde op voor de andere parameters die waarschijnlijk als standaardconfiguratie in uw omgeving worden gebruikt. Of u de sjabloon opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) voor meer informatie over het werken met sjablonen voor resources

Zie [regio's Log Analytics is beschikbaar in](https://azure.microsoft.com/regions/services/) en zoek naar Azure Monitor in het veld Zoeken naar een **product** voor informatie over ondersteunde regio's.

De volgende parameters stellen een standaardwaarde in:

* locatie - standaard naar Oost-VS
* sku - standaard de nieuwe prijscategorie per GB die is uitgebracht in het prijsmodel van april 2018

>[!WARNING]
>Als u een Log Analytics-werkruimte maakt of configureert in een abonnement dat is gekozen voor het nieuwe prijsmodel van april 2018, is de enige geldige prijscategorie Log Analytics **PerGB2018**.
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

2. Bewerk de sjabloon om aan uw vereisten te voldoen. Bekijk [de sjabloonverwijzing microsoft.operationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) om te zien welke eigenschappen en waarden worden ondersteund.
3. Sla dit bestand op als **deploylaworkspacetemplate.json** in een lokale map.   
4. U kunt deze sjabloon nu implementeren. Gebruik de volgende opdrachten uit de map met de sjabloon. Wanneer u wordt gevraagd om een naam van een werkruimte, geeft u een naam op die wereldwijd uniek is voor alle Azure-abonnementen.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

De implementatie kan enkele minuten duren. Wanneer het is voltooid, ziet u een bericht dat vergelijkbaar is met het volgende dat het resultaat bevat:

![Voorbeeldresultaat wanneer de implementatie is voltooid](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Volgende stappen
Nu u een werkruimte beschikbaar hebt, u het verzamelen van bewakingstelemetrie configureren, logboekzoekopdrachten uitvoeren om die gegevens te analyseren en een beheeroplossing toevoegen om aanvullende gegevens en analytische inzichten te bieden.  

* Zie [Azure-servicelogboeken en -statistieken verzamelen voor gebruik in Logboekanalyse](../platform/collect-azure-metrics-logs.md)voor het maken van gegevens uit Azure-bronnen met Azure Diagnostics of Azure-opslag.  
* Voeg [System Center Operations Manager toe als gegevensbron](../platform/om-agents.md) om gegevens te verzamelen van agents die uw beheergroep Operations Manager rapporteren en deze op te slaan in uw Log Analytics-werkruimte.  
* Sluit [Configuratiebeheer](../platform/collect-sccm.md) aan bij het importeren van computers die lid zijn van verzamelingen in de hiërarchie.  
* Bekijk de beschikbare [bewakingsoplossingen](../insights/solutions.md) en hoe u een oplossing uit uw werkruimte toevoegen of verwijderen.
