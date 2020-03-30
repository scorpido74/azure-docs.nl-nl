---
title: Azure Monitor-werkmappen en Azure Resource Manager-sjablonen
description: Complexe rapportage vereenvoudigen met vooraf gebouwde en aangepaste geparameteriseerde Azure Monitor-werkmappen die zijn geïmplementeerd via Azure Resource Manager-sjablonen
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658401"
---
# <a name="programmatically-manage-workbooks"></a>Werkmappen programmatisch beheren

Broneigenaren hebben de mogelijkheid om hun werkmappen programmatisch te maken en te beheren via Resource Manager-sjablonen. 

Dit kan handig zijn in scenario's als:
* Het implementeren van org- of domeinspecifieke analyserapporten samen met resources-implementaties. U bijvoorbeeld org-specifieke prestatie- en foutwerkmappen implementeren voor uw nieuwe apps of virtuele machines.
* Standaardrapporten of dashboards implementeren met werkmappen voor bestaande bronnen.

De werkmap wordt gemaakt in de gewenste sub/resourcegroep en met de inhoud die is opgegeven in de Resource Manager-sjablonen.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Azure Resource Manager-sjabloon voor het implementeren van werkmappen
1. Open een werkmap die u programmatisch wilt implementeren.
2. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem _Bewerken_ te klikken.
3. Open de _Geavanceerde_ _</>_ editor met de knop op de werkbalk.
4. Schakel in de editor _over naar sjabloon type voor_ _resourcebeheer_.
5. De sjabloon Resourcemanager voor het maken wordt weergegeven in de editor. Kopieer de inhoud en gebruik as-is of voeg deze samen met een grotere sjabloon die ook de doelbron implementeert.

    ![Afbeelding met de manier waarop u de resourcemanagersjabloon binnen de gebruikersinterface van de werkmap halen](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Voorbeeld van sjabloon Azure Resource Manager
Deze sjabloon laat zien hoe u een eenvoudige werkmap implementeert met een 'Hello World!'
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Sjabloonparameters

| Parameter | Uitleg |
| :------------- |:-------------|
| `workbookDisplayName` | De vriendelijke naam voor de werkmap die wordt gebruikt in de galerie of opgeslagen lijst. Moet uniek zijn in het bereik van de resourcegroep en de bron |
| `workbookType` | De galerie waarop de werkmap wordt weergegeven. Ondersteunde waarden zijn werkmap, `tsg`Azure Monitor, enz. |
| `workbookSourceId` | De id van de resourceinstantie waaraan de werkmap is gekoppeld. De nieuwe werkmap wordt weergegeven met betrekking tot deze resourceinstantie, bijvoorbeeld in de inhoudstabel van de resource onder _Werkmap_. Als u wilt dat uw werkmap wordt weergegeven in de werkmapgalerie in Azure Monitor, gebruikt u de tekenreeks _Azure Monitor_ in plaats van een resource-id. |
| `workbookId` | De unieke guid voor deze werkmap instantie. Gebruik _[newGuid()]_ om automatisch een nieuwe guid te maken. |
| `kind` | Wordt gebruikt om op te geven of de gemaakte werkmap wordt gedeeld of privé. Gebruik _gedeelde_ waarde voor gedeelde werkmappen en _gebruikers_ voor privéboeken. |
| `location` | De Azure-locatie waar de werkmap wordt gemaakt. Gebruik _[resourceGroep().locatie]_ om deze te maken op dezelfde locatie als de resourcegroep |
| `serializedData` | Bevat de inhoud of payload die in de werkmap moet worden gebruikt. De sjabloon Resourcebeheer uit de gebruikersinterface van werkmappen gebruiken om de waarde op te halen |

### <a name="workbook-types"></a>Werkmaptypen
Werkmaptypen geven aan onder welk werkmapgalerietype de nieuwe werkmapinstantie wordt weergegeven. Een aantal opties:

| Type | Galerielocatie |
| :------------- |:-------------|
| `workbook` | De standaardinstelling die wordt gebruikt in de meeste rapporten, waaronder de galerie Met toepassingsinzichten voor werkmappen, Azure Monitor, enz.  |
| `tsg` | De galerie Problemen met hulplijnen in toepassingsinzichten |
| `usage` | De _galerie Meer_ onder _Gebruik_ in Toepassingsinzichten |

### <a name="limitations"></a>Beperkingen
Om een technische reden kan dit mechanisme niet worden gebruikt om werkmapinstanties te maken in de _werkmappengalerie_ van Toepassingsinzichten. We werken aan het aanpakken van deze beperking. In de tussentijd raden we u aan de galerie `tsg`Probleemoplossingshandleiding (werkmapType: ) te gebruiken om gerelateerde werkmappen met Application Insights te implementeren.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe werkmappen worden gebruikt om de nieuwe [Azure Monitor for Storage-ervaring van](../insights/storage-insights-overview.md)stroom te voorzien.

