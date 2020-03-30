---
title: Een beheeroplossingsbestand maken in Azure | Microsoft Documenten
description: Beheeroplossingen bieden verpakte beheerscenario's die klanten kunnen toevoegen aan hun Azure-omgeving.  In dit artikel vindt u informatie over hoe u beheeroplossingen maken die in uw eigen omgeving kunnen worden gebruikt of beschikbaar worden gesteld aan uw klanten.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275176"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Een beheeroplossingsbestand maken in Azure (Voorbeeld)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van beheeroplossingen in Azure die momenteel in preview zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.  

Beheeroplossingen in Azure worden geïmplementeerd als [Resource Manager-sjablonen.](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)  De belangrijkste taak bij het leren hoe u beheeroplossingen moet schrijven, is het leren [schrijven van een sjabloon.](../../azure-resource-manager/templates/template-syntax.md)  In dit artikel vindt u unieke details van sjablonen die worden gebruikt voor oplossingen en hoe u typische oplossingsbronnen configureren.


## <a name="tools"></a>Hulpprogramma's

U elke teksteditor gebruiken om met oplossingsbestanden te werken, maar we raden u aan gebruik te maken van de functies in Visual Studio of Visual Studio Code zoals beschreven in de volgende artikelen.

- [Azure-resourcegroepen maken en implementeren met Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Werken met Azure Resource Manager-sjablonen in Visual Studio Code](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Structuur
De basisstructuur van een beheeroplossingsbestand is hetzelfde als een [Resource Manager-sjabloon,](../../azure-resource-manager/templates/template-syntax.md#template-format)wat als volgt is.  Elk van de onderstaande secties beschrijft de elementen op het hoogste niveau en de inhoud ervan in een oplossing.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parameters
[Parameters](../../azure-resource-manager/templates/template-syntax.md#parameters) zijn waarden die u van de gebruiker nodig hebt wanneer ze de beheeroplossing installeren.  Er zijn standaardparameters die alle oplossingen hebben en u extra parameters toevoegen zoals vereist voor uw specifieke oplossing.  Hoe gebruikers parameterwaarden opgeven wanneer ze uw oplossing installeren, hangt af van de specifieke parameter en hoe de oplossing wordt geïnstalleerd.

Wanneer een gebruiker [uw beheeroplossing installeert](solutions.md#install-a-monitoring-solution) via de Azure Marketplace- of Azure QuickStart-sjablonen, wordt deze gevraagd een [log Analytics-werkruimte en automatiseringsaccount](solutions.md#log-analytics-workspace-and-automation-account)te selecteren.  Deze worden gebruikt om de waarden van elk van de standaardparameters in te vullen.  De gebruiker wordt niet gevraagd om direct waarden voor de standaardparameters op te geven, maar ze worden gevraagd om waarden voor eventuele aanvullende parameters op te geven.


Hieronder wordt een voorbeeldparameter weergegeven.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

In de volgende tabel worden de kenmerken van een parameter beschreven.

| Kenmerk | Beschrijving |
|:--- |:--- |
| type |Gegevenstype voor de parameter. Het invoerbesturingselement dat voor de gebruiker wordt weergegeven, is afhankelijk van het gegevenstype.<br><br>bool - Drop down box<br>tekenreeks - Tekstvak<br>int - Tekstvak<br>securestring - Wachtwoordveld<br> |
| category |Optionele categorie voor de parameter.  Parameters in dezelfde categorie worden gegroepeerd. |
| control |Extra functionaliteit voor tekenreeksparameters.<br><br>datumtijd - Het besturingselement Datetime wordt weergegeven.<br>guid - Guid-waarde wordt automatisch gegenereerd en de parameter wordt niet weergegeven. |
| description |Optionele beschrijving voor de parameter.  Weergegeven in een informatieballon naast de parameter. |

### <a name="standard-parameters"></a>Standaardparameters
In de volgende tabel worden de standaardparameters voor alle beheeroplossingen weergegeven.  Deze waarden worden ingevuld voor de gebruiker in plaats van ernaar te vragen wanneer uw oplossing is geïnstalleerd vanuit de Azure Marketplace- of Quickstart-sjablonen.  De gebruiker moet waarden voor hen opgeven als de oplossing met een andere methode is geïnstalleerd.

> [!NOTE]
> De gebruikersinterface in de Azure Marketplace- en Quickstart-sjablonen verwacht de parameternamen in de tabel.  Als u verschillende parameternamen gebruikt, wordt de gebruiker hiervoor gevraagd en worden deze niet automatisch ingevuld.
>
>

| Parameter | Type | Beschrijving |
|:--- |:--- |:--- |
| accountName |tekenreeks |De naam van azure automation-account. |
| pricingTier |tekenreeks |Prijscategorie van zowel Log Analytics-werkruimte als Azure Automation-account. |
| regioId |tekenreeks |Regio van het Azure Automation-account. |
| solutionName |tekenreeks |Naam van de oplossing.  Als u uw oplossing implementeert via Quickstart-sjablonen, moet u solutionName definiëren als parameter, zodat u een tekenreeks definiëren in plaats waarin de gebruiker er een moet opgeven. |
| workspaceName |tekenreeks |Naam van de Log Analytics-werkruimte. |
| workspaceRegionId |tekenreeks |Regio van de werkruimte Log Analytics. |


Hieronder volgt de structuur van de standaardparameters die u kopiëren en plakken in uw oplossingsbestand.  

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


U verwijst naar parameterwaarden in andere elementen van de oplossing met de **syntaxisparameters('parameternaam').**  Als u bijvoorbeeld toegang wilt krijgen tot de naam van de werkruimte, gebruikt u **parameters('workspaceName')**

## <a name="variables"></a>Variabelen
[Variabelen](../../azure-resource-manager/templates/template-syntax.md#variables) zijn waarden die u gebruikt in de rest van de beheeroplossing.  Deze waarden worden niet blootgesteld aan de gebruiker die de oplossing installeert.  Ze zijn bedoeld om de auteur te voorzien van een enkele locatie waar ze waarden kunnen beheren die meerdere keren in de oplossing kunnen worden gebruikt. U moet alle waarden die specifiek zijn voor uw oplossing in variabelen plaatsen in tegenstelling tot harde codering in het **bronnenelement.**  Dit maakt de code leesbaarder en stelt u in staat om deze waarden eenvoudig in latere versies te wijzigen.

Hieronder volgt een voorbeeld van een **variabelenelement** met typische parameters die in oplossingen worden gebruikt.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

U verwijst via de oplossing naar variabele waarden met de **syntaxisvariabelen('variabele naam').**  Als u bijvoorbeeld toegang wilt krijgen tot de variabele SolutionName, gebruikt u **variabelen('SolutionName').**

U ook complexe variabelen definiëren die meerdere waarden instellen.  Deze zijn vooral handig in beheeroplossingen waarbij u meerdere eigenschappen definieert voor verschillende typen resources.  U bijvoorbeeld de bovenstaande oplossingsvariabelen herstructureren naar het volgende.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

In dit geval verwijst u via de oplossing naar variabele waarden met de eigenschap **Syntax('variabele naam').Eigenschap**.  Als u bijvoorbeeld toegang wilt krijgen tot de variabele Oplossingsnaam, gebruikt u **variabelen('Oplossing'). Naam**.

## <a name="resources"></a>Resources
[Resources](../../azure-resource-manager/templates/template-syntax.md#resources) definiëren de verschillende resources die uw beheeroplossing installeert en configureert.  Dit is het grootste en meest complexe gedeelte van de sjabloon.  U de structuur en volledige beschrijving van resourceelementen krijgen in [Azure Resource Manager-sjablonen voor ontwerpen.](../../azure-resource-manager/templates/template-syntax.md#resources)  Verschillende bronnen die u doorgaans definieert, worden beschreven in andere artikelen in deze documentatie. 


### <a name="dependencies"></a>Afhankelijkheden
Het element **dependsOn** geeft een [afhankelijkheid op van](../../azure-resource-manager/templates/define-resource-dependency.md) een andere resource.  Wanneer de oplossing is geïnstalleerd, wordt er pas een resource gemaakt nadat alle afhankelijkheden zijn gemaakt.  Uw oplossing kan bijvoorbeeld [een runbook starten](solutions-resources-automation.md#runbooks) wanneer deze is geïnstalleerd met behulp van een [taakbron.](solutions-resources-automation.md#automation-jobs)  De taakbron is afhankelijk van de runbook-bron om ervoor te zorgen dat de runbook wordt gemaakt voordat de taak wordt gemaakt.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-werkruimte en automatiseringsaccount
Beheeroplossingen vereisen een [Log Analytics-werkruimte](../../azure-monitor/platform/manage-access.md) die weergaven en een [Automatiseringsaccount](../../automation/automation-security-overview.md#automation-account-overview) bevat, om runbooks en gerelateerde bronnen te bevatten.  Deze moeten beschikbaar zijn voordat de middelen in de oplossing worden gemaakt en mogen niet worden gedefinieerd in de oplossing zelf.  De gebruiker [geeft een werkruimte en account op](solutions.md#log-analytics-workspace-and-automation-account) wanneer deze uw oplossing implementeert, maar als auteur moet u de volgende punten overwegen.


## <a name="solution-resource"></a>Oplossingsbron
Elke oplossing vereist een bronvermelding in het **resources-element** dat de oplossing zelf definieert.  Dit heeft een soort **Microsoft.OperationsManagement/oplossingen** en heeft de volgende structuur. Dit omvat [standaardparameters](#parameters) en [variabelen](#variables) die doorgaans worden gebruikt om eigenschappen van de oplossing te definiëren.


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
De oplossingsbron moet afhankelijk zijn [van](../../azure-resource-manager/templates/define-resource-dependency.md) elke andere resource in de oplossing, omdat deze moeten bestaan voordat de oplossing kan worden gemaakt.  U doet dit door een vermelding toe te voegen voor elke resource in het element **dependsOn.**

### <a name="properties"></a>Eigenschappen
De oplossingsbron heeft de eigenschappen in de volgende tabel.  Dit omvat de resources waarnaar wordt verwezen en die worden weergegeven door de oplossing die bepaalt hoe de resource wordt beheerd nadat de oplossing is geïnstalleerd.  Elke resource in de oplossing moet worden vermeld in de **eigenschap Resources waarnaar wordt verwezen** of de eigenschap **containedResources.**

| Eigenschap | Beschrijving |
|:--- |:--- |
| workspaceResourceId |ID van de werkruimte Log Analytics in het formulier * \<Resource Group ID\<>/providers/Microsoft.OperationalInsights/workspaces/WorkspaceName\>*. |
| bronnen waarnaar wordt verwezen |Lijst met bronnen in de oplossing die niet mogen worden verwijderd wanneer de oplossing wordt verwijderd. |
| containedResources (containedResources) |Lijst met bronnen in de oplossing die moeten worden verwijderd wanneer de oplossing wordt verwijderd. |

Het bovenstaande voorbeeld is voor een oplossing met een runbook, een planning en weergave.  Er wordt verwezen *naar* de planning en het loopboek in het **eigenschappenelement,** zodat ze niet worden verwijderd wanneer de oplossing wordt verwijderd.  De weergave is *opgenomen,* zodat deze wordt verwijderd wanneer de oplossing wordt verwijderd.

### <a name="plan"></a>Plannen
De **planentiteit** van de oplossingsbron heeft de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| name |Naam van de oplossing. |
| versie |Versie van de oplossing zoals bepaald door de auteur. |
| product |Unieke string om de oplossing te identificeren. |
| uitgever |Uitgever van de oplossing. |



## <a name="next-steps"></a>Volgende stappen
* [Voeg opgeslagen zoekopdrachten en waarschuwingen toe](solutions-resources-searches-alerts.md) aan uw beheeroplossing.
* [Weergaven toevoegen](solutions-resources-views.md) aan uw beheeroplossing.
* [Voeg runbooks en andere automatiseringsbronnen toe](solutions-resources-automation.md) aan uw beheeroplossing.
* Meer informatie over [azure resource manager-sjablonen voor ontwerpen](../../azure-resource-manager/templates/template-syntax.md).
* Zoeken in [Azure Quickstart-sjablonen](https://azure.microsoft.com/documentation/templates) voor voorbeelden van verschillende Resource Manager-sjablonen.
