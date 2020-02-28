---
title: Weer gaven in beheer oplossingen | Microsoft Docs
description: 'Beheer oplossingen bevatten meestal een of meer weer gaven voor het visualiseren van gegevens.  In dit artikel wordt beschreven hoe u een weer gave exporteert die is gemaakt met de ontwerp functie voor weer gaven en deze opneemt in een beheer oplossing. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663042"
---
# <a name="views-in-management-solutions-preview"></a>Weer gaven in beheer oplossingen (preview-versie)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van beheer oplossingen die momenteel als preview-versie beschikbaar zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.    


[Beheer oplossingen](solutions.md) bevatten meestal een of meer weer gaven voor het visualiseren van gegevens.  In dit artikel wordt beschreven hoe u een weer gave exporteert die is gemaakt met de [ontwerp functie voor weer gaven](../../azure-monitor/platform/view-designer.md) en deze opneemt in een beheer oplossing.  

> [!NOTE]
> In de voor beelden in dit artikel worden para meters en variabelen gebruikt die vereist zijn of gemeen schappelijk zijn voor beheer oplossingen en die worden beschreven in [ontwerp en bouw een beheer oplossing in azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al bekend bent met het [maken van een beheer oplossing](solutions-creating.md) en de structuur van een oplossings bestand.

## <a name="overview"></a>Overzicht
Als u een weer gave in een beheer oplossing wilt gebruiken, maakt u een **bron** voor deze in het [oplossings bestand](solutions-creating.md).  De JSON die de gedetailleerde configuratie van de weer gave beschrijft, is doorgaans complex, maar niet iets dat een typische auteur van de oplossing hand matig zou kunnen maken.  De meest voorkomende methode is om de weer gave te maken met behulp van de [weer gave Designer](../../azure-monitor/platform/view-designer.md), deze te exporteren en vervolgens de gedetailleerde configuratie aan de oplossing toe te voegen.

De basis stappen om een weer gave aan een oplossing toe te voegen, zijn als volgt.  Elke stap wordt uitvoerig beschreven in de volgende secties.

1. De weer gave exporteren naar een bestand.
2. Maak de weergave resource in de oplossing.
3. Voeg de weergave Details toe.

## <a name="export-the-view-to-a-file"></a>De weer gave naar een bestand exporteren
Volg de instructies in [log Analytics Designer weer geven](../../azure-monitor/platform/view-designer.md) om een weer gave te exporteren naar een bestand.  Het geëxporteerde bestand heeft een JSON-indeling met dezelfde [elementen als het oplossings bestand](solutions-solution-file.md).  

Het element **resources** van het weergave bestand heeft een resource met het type **micro soft. OperationalInsights/Workspaces** dat de log Analytics-werk ruimte vertegenwoordigt.  Dit element heeft een subelement met een type **weer gaven** dat de weer gave vertegenwoordigt en de gedetailleerde configuratie bevat.  U kopieert de details van dit element en kopieert deze vervolgens naar uw oplossing.

## <a name="create-the-view-resource-in-the-solution"></a>De bron van de weer gave maken in de oplossing
Voeg de volgende weergave resource toe aan het element **resources** van het oplossings bestand.  Dit maakt gebruik van variabelen die hieronder worden beschreven en die u ook moet toevoegen.  Houd er rekening mee dat de eigenschappen **dash board** en **OverviewTile** tijdelijke aanduidingen zijn die u overschrijft met de bijbehorende eigenschappen uit het geëxporteerde weergave bestand.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Voeg de volgende variabelen toe aan het element Varia bles van het oplossings bestand en vervang de waarden door die voor uw oplossing.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

U kunt de volledige weergave resource kopiëren vanuit het geëxporteerde weergave bestand, maar u moet wel de volgende wijzigingen aanbrengen om te werken in uw oplossing.  

* Het **type** voor de weergave resource moet worden gewijzigd van **views** in **micro soft. OperationalInsights/Workspaces**.
* De **naam** eigenschap van de weergave resource moet worden gewijzigd om de naam van de werk ruimte op te geven.
* De afhankelijkheid van de werk ruimte moet worden verwijderd omdat de werkruimte resource niet is gedefinieerd in de oplossing.
* De eigenschap **DisplayName** moet worden toegevoegd aan de weer gave.  De **id**, **naam**en **DisplayName** moeten overeenkomen.
* Parameter namen moeten worden gewijzigd om overeen te komen met de vereiste set para meters.
* Variabelen moeten worden gedefinieerd in de oplossing en worden gebruikt in de relevante eigenschappen.

### <a name="log-analytics-api-version"></a>Log Analytics API-versie
Alle Log Analytics resources die in een resource manager-sjabloon zijn gedefinieerd, hebben een eigenschap **apiVersion** die de versie definieert van de API die door de resource moet worden gebruikt.  Deze versie is anders voor weer gaven met query's die gebruikmaken van de [oude en bijgewerkte query taal](../../azure-monitor/log-query/log-query-overview.md).  

 De volgende tabel bevat de Log Analytics-API-versies voor weer gaven in oude en bijgewerkte werk ruimten: 

| Werkruimte versie | API-versie | Query's uitvoeren |
|:---|:---|:---|
| V1 (verouderd)   | 2015-11-01-preview | Verouderde indeling.<br> Voor beeld: type = Event EventLevelName = error  |
| v2 (bijgewerkt) | 2015-11-01-preview | Verouderde indeling.  Geconverteerd naar een bijgewerkte indeling bij de installatie.<br> Voor beeld: type = Event EventLevelName = error<br>Geconverteerd naar: gebeurtenis &#124; waarbij EventLevelName = = "Error"  |
| v2 (bijgewerkt) | 2017-03-03-preview | Upgrade-indeling. <br>Voor beeld: &#124; event where EventLevelName = = "Error"  |


## <a name="add-the-view-details"></a>Details van de weer gave toevoegen
De resource weer geven in het geëxporteerde weergave bestand bevat twee elementen in het element **Properties** met de naam **dash board** en **OverviewTile** , die de gedetailleerde configuratie van de weer gave bevatten.  Kopieer deze twee elementen en hun inhoud naar het **Eigenschappen** element van de resource weer geven in het oplossings bestand.

## <a name="example"></a>Voorbeeld
In het volgende voor beeld ziet u bijvoorbeeld een eenvoudig oplossings bestand met een weer gave.  Er worden weglatings tekens (...) weer gegeven voor de inhoud van het **dash board** en de **OverviewTile** om redenen van ruimte.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het maken van [beheer oplossingen](solutions-creating.md).
* Neem [Automation-runbooks op in uw beheer oplossing](solutions-resources-automation.md).
