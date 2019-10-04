---
title: Een Log Analytics-werkruimte met behulp van Azure PowerShell maken | Microsoft Docs
description: Informatie over het maken van een Log Analytics-werkruimte om het beheer van oplossingen en gegevens verzamelen van uw cloud en on-premises omgevingen met Azure PowerShell mogelijk.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: b81495f19ce596d689778e6ab75512d744ae4588
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836574"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Een Log Analytics-werkruimte maken met Azure PowerShell

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources vanaf de PowerShell-opdrachtregel of in scripts. In deze Quick start ziet u hoe u de Azure PowerShell module kunt gebruiken om een Log Analytics-werk ruimte in Azure Monitor te implementeren. Een Log Analytics-werk ruimte is een unieke omgeving voor Azure Monitor logboek gegevens. Elke werk ruimte heeft een eigen gegevens opslagplaats en-configuratie, en gegevens bronnen en-oplossingen zijn geconfigureerd om hun gegevens op te slaan in een bepaalde werk ruimte. U hebt een Log Analytics-werk ruimte nodig als u van plan bent om gegevens te verzamelen uit de volgende bronnen:

* Azure-resources in uw abonnement  
* On-premises computers bewaakt door System Center Operations Manager  
* Apparaatverzamelingen van System Center Configuration Manager  
* Diagnose- of logboekgegevens van Azure Storage  
 
Zie de volgende onderwerpen voor andere bronnen, zoals Azure-VM's en Windows of Linux-VM's in uw omgeving:

* [Verzamelen van gegevens van virtuele machines van Azure](../learn/quick-collect-azurevm.md)
* [Gegevens verzamelen van hybride Linux-computer](../learn/quick-collect-linux-computer.md)
* [Gegevens verzamelen van hybride Windows-computer](quick-collect-windows-computer.md)

Als u geen Azure-abonnement hebt, maakt u [een gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, is voor deze zelf studie de Azure PowerShell AZ-module vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Zie [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps) als u een upgrade wilt uitvoeren. Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-workspace"></a>Een werkruimte maken
Een werk ruimte maken met [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). In het volgende voor beeld wordt een werk ruimte op de locatie *ooster* gemaakt met behulp van een resource manager-sjabloon van uw lokale machine. De JSON-sjabloon is geconfigureerd om te vragen u alleen voor de naam van de werkruimte en een standaardwaarde opgegeven voor de andere parameters die waarschijnlijk moet worden gebruikt als een standaardconfiguratie in uw omgeving. 

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

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

De implementatie kan enkele minuten duren. Als deze is voltooid, ziet u een bericht dat lijkt op de volgende mogelijkheden van het resultaat:

![Voorbeeld van resultaat wanneer de implementatie is voltooid](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Volgende stappen
Nu dat u een werkruimte beschikbaar hebt, kunt u verzamelen van telemetrie bewaking configureren, uitvoeren van zoekopdrachten in Logboeken om die gegevens te analyseren en toevoegen van een oplossing om aanvullende gegevens en analytische informatie te bieden.  

* Zie [Azure service-logboeken en-metrische gegevens verzamelen voor gebruik in azure monitor](../platform/collect-azure-metrics-logs.md)voor informatie over het inschakelen van het verzamelen van data van Azure-resources met Azure Diagnostics of Azure Storage.  
* Voeg [System Center Operations Manager als een gegevensbron](../platform/om-agents.md) voor het verzamelen van gegevens van agents die rapporteren van uw Operations Manager-beheergroep en sla deze op in uw Log Analytics-werkruimte.  
* Verbinding maken met [Configuration Manager](../platform/collect-sccm.md) voor het importeren van computers die lid van verzamelingen in de hiërarchie zijn.  
* Bekijk de beschik bare [bewakings oplossingen](../insights/solutions.md) en hoe u een oplossing kunt toevoegen aan of verwijderen uit uw werk ruimte.
