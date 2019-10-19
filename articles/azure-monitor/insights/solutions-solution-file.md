---
title: Een bestand met een beheer oplossing maken in azure | Microsoft Docs
description: Beheer oplossingen bieden verpakte beheer scenario's die klanten aan hun Azure-omgeving kunnen toevoegen.  In dit artikel vindt u informatie over het maken van beheer oplossingen voor gebruik in uw eigen omgeving of voor uw klanten beschikbaar.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47ee691186da7f915ca8fcf87415784ab12ef1e0
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553855"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Een bestand met een beheer oplossing maken in azure (preview)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van beheer oplossingen in azure die momenteel als preview-versie beschikbaar zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.  

Beheer oplossingen in Azure worden geïmplementeerd als [Resource Manager-sjablonen](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).  De belangrijkste taak bij het leren van beheer oplossingen is leren hoe u [een sjabloon](../../azure-resource-manager/resource-group-authoring-templates.md)maakt.  In dit artikel vindt u unieke details over de sjablonen die worden gebruikt voor oplossingen en over het configureren van typische bronnen voor oplossingen.


## <a name="tools"></a>Tools

U kunt een tekst editor gebruiken om te werken met oplossings bestanden, maar we raden u aan de functies van Visual Studio of Visual Studio code te benutten zoals beschreven in de volgende artikelen.

- [Azure-resource groepen maken en implementeren met Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Werken met Azure Resource Manager sjablonen in Visual Studio code](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>structuur
De basis structuur van een beheer oplossings bestand is hetzelfde als een [Resource Manager-sjabloon](../../azure-resource-manager/resource-group-authoring-templates.md#template-format). Dit is als volgt.  In elk van de volgende secties worden de elementen op het hoogste niveau en hun inhoud in een oplossing beschreven.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parameters
[Para meters](../../azure-resource-manager/resource-group-authoring-templates.md#parameters) zijn waarden die u nodig hebt van de gebruiker bij de installatie van de beheer oplossing.  Er zijn standaard parameters voor alle oplossingen, en u kunt aanvullende para meters toevoegen voor uw specifieke oplossing.  Hoe gebruikers parameter waarden opgeven wanneer ze uw oplossing installeren, zijn afhankelijk van de specifieke para meter en hoe de oplossing wordt geïnstalleerd.

Wanneer een gebruiker [uw beheer oplossing installeert](solutions.md#install-a-monitoring-solution) via de Azure Marketplace-of Azure Quick Start-sjablonen, wordt u gevraagd een [log Analytics-werk ruimte en een Automation-account](solutions.md#log-analytics-workspace-and-automation-account)te selecteren.  Deze worden gebruikt om de waarden van elk van de standaard parameters in te vullen.  De gebruiker wordt niet gevraagd om rechtstreeks waarden op te geven voor de standaard parameters, maar er wordt gevraagd om waarden op te geven voor aanvullende para meters.


Hieronder ziet u een voor beeld van een para meter.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

In de volgende tabel worden de kenmerken van een para meter beschreven.

| Kenmerk | Beschrijving |
|:--- |:--- |
| type |Gegevens type voor de para meter. Het invoer besturings element dat voor de gebruiker wordt weer gegeven, is afhankelijk van het gegevens type.<br><br>BOOL-vervolg keuzelijst<br>teken reeks-tekstvak<br>int-tekstvak<br>securestring-wachtwoord veld<br> |
| category |Optionele categorie voor de para meter.  De para meters in dezelfde categorie worden samen gegroepeerd. |
| Over |Aanvullende functionaliteit voor teken reeks parameters.<br><br>besturings element DateTime-DateTime wordt weer gegeven.<br>GUID-GUID-waarde wordt automatisch gegenereerd en de para meter wordt niet weer gegeven. |
| description |Optionele beschrijving voor de para meter.  Wordt weer gegeven in een informatie ballon naast de para meter. |

### <a name="standard-parameters"></a>Standaard parameters
De volgende tabel bevat de standaard parameters voor alle beheer oplossingen.  Deze waarden worden gevuld voor de gebruiker in plaats van om ze te vragen wanneer uw oplossing wordt geïnstalleerd vanuit de sjablonen voor Azure Marketplace of Quick Start.  De gebruiker moet waarden opgeven voor deze als de oplossing wordt geïnstalleerd met een andere methode.

> [!NOTE]
> De gebruikers interface in de sjablonen Azure Marketplace en Quick Start verwacht de parameter namen in de tabel.  Als u verschillende parameter namen gebruikt, wordt de gebruiker gevraagd deze in te vullen en worden ze niet automatisch ingevuld.
>
>

| Parameter | Type | Beschrijving |
|:--- |:--- |:--- |
| accountName |string |Azure Automation account naam. |
| pricingTier |string |Prijs categorie van zowel Log Analytics werk ruimte als Azure Automation account. |
| regionId |string |De regio van het Azure Automation-account. |
| solutionName |string |De naam van de oplossing.  Als u de oplossing implementeert via Quick Start-sjablonen, moet u de naam van de oplossing definiëren als para meter, zodat u een teken reeks kunt definiëren in plaats daarvan een gebruiker in te stellen. |
| workspaceName |string |Naam van Log Analytics werk ruimte. |
| workspaceRegionId |string |De regio van de Log Analytics-werk ruimte. |


Hieronder ziet u de structuur van de standaard parameters die u kunt kopiëren en plakken in het oplossings bestand.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


U verwijst naar parameter waarden in andere elementen van de oplossing met de syntaxis **parameters (' parameter naam ')** .  Als u bijvoorbeeld de naam van de werk ruimte wilt openen, gebruikt u de **para meters (' workspacenaam ')**

## <a name="variables"></a>Variabelen
[Variabelen](../../azure-resource-manager/resource-group-authoring-templates.md#variables) zijn waarden die u wilt gebruiken in de rest van de beheer oplossing.  Deze waarden worden niet weer gegeven aan de gebruiker die de oplossing installeert.  Ze zijn bedoeld om de auteur te voorzien van één locatie waar ze waarden kunnen beheren die meerdere keren kunnen worden gebruikt in de oplossing. U moet alle waarden die specifiek zijn voor uw oplossing in variabelen opnemen in plaats van de vaste code in het element **resources** .  Hierdoor is de code leesbaarder en kunt u deze waarden eenvoudig wijzigen in latere versies.

Hieronder volgt een voor beeld van een element **Varia bles** met gebruikelijke para meters die in oplossingen worden gebruikt.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

U verwijst naar variabelen waarden via de oplossing met de syntaxis **variabelen (' naam variabele ')** .  Als u bijvoorbeeld toegang wilt krijgen tot de variabele Solutionnaam, gebruikt u **variabelen (' solutionnaam ')** .

U kunt ook complexe variabelen definiëren die meerdere sets waarden hebben.  Deze zijn vooral nuttig in beheer oplossingen waarbij u meerdere eigenschappen voor verschillende typen resources definieert.  U kunt bijvoorbeeld de bovenstaande oplossings variabelen opnieuw structureren in het volgende.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

In dit geval verwijst u naar variabele waarden via de oplossing met de syntaxis **variabelen (' naam variabele '). eigenschap**.  Als u bijvoorbeeld de variabele oplossings naam wilt openen, gebruikt u **variabelen (' oplossing '). Naam**.

## <a name="resources"></a>Bronnen
[Resources](../../azure-resource-manager/resource-group-authoring-templates.md#resources) definiëren de verschillende bronnen die door uw beheer oplossing worden geïnstalleerd en geconfigureerd.  Dit is het grootste en meest complexe gedeelte van de sjabloon.  U kunt de structuur en volledige beschrijving van resource-elementen in [ontwerp Azure Resource Manager sjablonen](../../azure-resource-manager/resource-group-authoring-templates.md#resources)ophalen.  Andere resources die u doorgaans definieert, worden in andere artikelen in deze documentatie beschreven. 


### <a name="dependencies"></a>Afhankelijkheden
Het **dependsOn** -element bevat een [afhankelijkheid](../../azure-resource-manager/resource-group-define-dependencies.md) van een andere resource.  Wanneer de oplossing is geïnstalleerd, wordt een resource pas gemaakt nadat alle afhankelijkheden ervan zijn gemaakt.  Uw oplossing kan bijvoorbeeld [een runbook starten](solutions-resources-automation.md#runbooks) wanneer het wordt geïnstalleerd met behulp van een [taak resource](solutions-resources-automation.md#automation-jobs).  De taak resource is afhankelijk van de runbook-resource om er zeker van te zijn dat het runbook wordt gemaakt voordat de taak wordt gemaakt.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-werk ruimte en Automation-account
Voor beheer oplossingen is een [log Analytics-werk ruimte](../../azure-monitor/platform/manage-access.md) vereist voor weer gaven en een [Automation-account](../../automation/automation-security-overview.md#automation-account-overview) dat runbooks en gerelateerde resources bevat.  Deze moeten beschikbaar zijn voordat de resources in de oplossing worden gemaakt en niet in de oplossing zelf moeten worden gedefinieerd.  De gebruiker [geeft een werk ruimte en account](solutions.md#log-analytics-workspace-and-automation-account) op wanneer de oplossing wordt geïmplementeerd, maar als de auteur moet u rekening houden met de volgende punten.


## <a name="solution-resource"></a>Resource oplossing
Voor elke oplossing is een bron vermelding vereist in het **resources** -element waarmee de oplossing zelf wordt gedefinieerd.  Dit is een type van **micro soft. OperationsManagement/Solutions** en de volgende structuur hebben. Dit omvat [standaard parameters](#parameters) en [variabelen](#variables) die doorgaans worden gebruikt voor het definiëren van eigenschappen van de oplossing.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Afhankelijkheden
De oplossings resource moet [afhankelijk](../../azure-resource-manager/resource-group-define-dependencies.md) zijn van elke andere resource in de oplossing, omdat deze moet bestaan voordat de oplossing kan worden gemaakt.  U doet dit door een vermelding toe te voegen voor elke resource in het **dependsOn** -element.

### <a name="properties"></a>Eigenschappen
De oplossings resource heeft de eigenschappen in de volgende tabel.  Dit omvat de resources waarnaar wordt verwezen en die zijn opgenomen in de oplossing die definieert hoe de bron wordt beheerd nadat de oplossing is geïnstalleerd.  Elke resource in de oplossing moet worden weer gegeven in de **referencedResources** of de eigenschap **containedResources** .

| Eigenschap | Beschrijving |
|:--- |:--- |
| workspaceResourceId |ID van de Log Analytics werk ruimte in het formulier *\<Resource groeps-ID >/providers/Microsoft.OperationalInsights/workspaces/\<Workspace naam \>* . |
| referencedResources |Lijst met resources in de oplossing die niet moeten worden verwijderd wanneer de oplossing wordt verwijderd. |
| containedResources |Lijst met resources in de oplossing die moeten worden verwijderd wanneer de oplossing wordt verwijderd. |

Het bovenstaande voor beeld is voor een oplossing met een runbook, een planning en een weer gave.  In het element **Properties** wordt *verwezen naar* de planning en het runbook, zodat deze niet worden verwijderd wanneer de oplossing wordt verwijderd.  De weer gave is *opgenomen* zodat deze wordt verwijderd wanneer de oplossing wordt verwijderd.

### <a name="plan"></a>Plan
De **plan** entiteit van de oplossings resource heeft de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| name |De naam van de oplossing. |
| versie |De versie van de oplossing, zoals bepaald door de auteur. |
| product |Unieke teken reeks voor het identificeren van de oplossing. |
| Uitgever |Uitgever van de oplossing. |



## <a name="next-steps"></a>Volgende stappen
* [Voeg opgeslagen Zoek opdrachten en waarschuwingen](solutions-resources-searches-alerts.md) toe aan uw beheer oplossing.
* [Voeg weer gaven](solutions-resources-views.md) toe aan uw beheer oplossing.
* [Voeg runbooks en andere Automation-resources](solutions-resources-automation.md) toe aan uw beheer oplossing.
* Meer informatie over het [ontwerpen van Azure Resource Manager sjablonen](../../azure-resource-manager/resource-group-authoring-templates.md).
* Zoek in [Azure Quick](https://azure.microsoft.com/documentation/templates) start-sjablonen naar voor beelden van verschillende Resource Manager-sjablonen.
