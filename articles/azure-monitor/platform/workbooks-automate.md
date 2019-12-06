---
title: Azure Monitor werkmappen en Azure Resource Manager sjablonen
description: Vereenvoudig complexe rapportage met vooraf opgebouwde en aangepaste werkmappen met para meters Azure Monitor die via Azure Resource Manager sjablonen zijn geïmplementeerd
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: e3d29beab7b1d82f73c706897af9650b68be11f5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872907"
---
# <a name="programmatically-manage-workbooks"></a>Werkmappen programmatisch beheren

Resource-eigen aren hebben de mogelijkheid om hun werkmappen programmatisch te maken en beheren via Resource Manager-sjablonen. 

Dit kan handig zijn in scenario's zoals:
* Implementatie van organisatie-of domein-specifieke analyse rapporten samen met de implementaties van resources. U kunt bijvoorbeeld voor uw nieuwe apps of virtuele machines organisatie-specifieke prestaties en mislukte werkmappen implementeren.
* Het implementeren van standaard rapporten of-Dash boards met behulp van werkmappen voor bestaande resources.

De werkmap wordt gemaakt in de gewenste sub/resource-groep en met de inhoud die is opgegeven in de Resource Manager-sjablonen.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Azure Resource Manager sjabloon voor het implementeren van werkmappen
1. Open een werkmap die u programmatisch wilt implementeren.
2. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
3. Open de _Geavanceerde editor_ met behulp van de knop _</>_ op de werk balk.
4. Schakel in de editor van het _sjabloon type_ over naar de _Resource Manager-sjabloon_.
5. De Resource Manager-sjabloon voor het maken van wordt weer gegeven in de editor. Kopieer de inhoud en gebruik deze as-is of voeg deze samen met een grotere sjabloon waarmee ook de doel resource wordt geïmplementeerd.

    ![Afbeelding die laat zien hoe u de Resource Manager-sjabloon kunt ophalen in de gebruikers interface van de werkmap](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Voor beeld Azure Resource Manager sjabloon
In deze sjabloon ziet u hoe u een eenvoudige werkmap implementeert waarin een ' Hallo wereld! ' wordt weer gegeven.
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
| `workbookDisplayName` | De beschrijvende naam voor de werkmap die wordt gebruikt in de galerie of de opgeslagen lijst. Moet uniek zijn binnen het bereik van de resource groep en de bron |
| `workbookType` | De galerie waarin de werkmap wordt weer gegeven. Ondersteunde waarden zijn: werkmap, `tsg`, Azure Monitor, enzovoort. |
| `workbookSourceId` | De ID van het resource-exemplaar waaraan de werkmap wordt gekoppeld. De nieuwe werkmap wordt weer gegeven met betrekking tot deze resource-instantie, bijvoorbeeld in de tabel met inhoud onder _werkmap_van de resource. Als u wilt dat uw werkmap wordt weer gegeven in de galerie met werkmappen in Azure Monitor, gebruikt u de teken reeks _Azure monitor_ in plaats van een resource-id. |
| `workbookId` | De unieke GUID voor dit exemplaar van de werkmap. Gebruik _[newGuid ()]_ om automatisch een nieuwe GUID te maken. |
| `kind` | Wordt gebruikt om op te geven of de gemaakte werkmap gedeeld of privé is. Gebruik een waarde die wordt _gedeeld_ voor gedeelde werkmappen en de _gebruiker_ voor persoonlijk. |
| `location` | De Azure-locatie waar de werkmap wordt gemaakt. Gebruik _[resourceGroup (). locatie]_ om deze te maken op dezelfde locatie als de resource groep |
| `serializedData` | Bevat de inhoud of Payload die in de werkmap moet worden gebruikt. De Resource Manager-sjabloon uit de werkmappen-gebruikers interface gebruiken om de waarde op te halen |

### <a name="workbook-types"></a>Werkmap typen
Met werkmap typen geeft u op in welke werkmap galerie het nieuwe exemplaar van de werkmap wordt weer gegeven onder. Een aantal opties:

| Type | Galerie locatie |
| :------------- |:-------------|
| `workbook` | De standaard die wordt gebruikt in de meeste rapporten, met inbegrip van de galerie met werkmappen van Application Insights, Azure Monitor, enzovoort.  |
| `tsg` | De galerie met gidsen voor probleem oplossing in Application Insights |
| `usage` | De galerie _meer_ onder _gebruik_ in Application Insights |

### <a name="limitations"></a>Beperkingen
Dit mechanisme kan om een technische reden niet worden gebruikt voor het maken van werkmap exemplaren in de galerie met _werkmappen_ van Application Insights. We werken aan het adresseren van deze beperking. In de tussen tijd raden we u aan om Application Insights gerelateerde werkmappen te implementeren met behulp van de galerie voor probleem oplossing (workbookType: `tsg`).

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe werkmappen worden gebruikt om de nieuwe [Azure monitor voor de opslag ervaring](../insights/storage-insights-overview.md)uit te scha kelen.

