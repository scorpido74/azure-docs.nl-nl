---
title: Standpunten in managementoplossingen | Microsoft Documenten
description: 'Beheeroplossingen bevatten doorgaans een of meer weergaven om gegevens te visualiseren.  In dit artikel wordt beschreven hoe u een weergave exporteren die is gemaakt door de View Designer en deze opnemen in een beheeroplossing. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663042"
---
# <a name="views-in-management-solutions-preview"></a>Weergaven in beheeroplossingen (Preview)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van managementoplossingen die momenteel in preview zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.    


[Beheeroplossingen](solutions.md) bevatten doorgaans een of meer weergaven om gegevens te visualiseren.  In dit artikel wordt beschreven hoe u een weergave exporteren die is gemaakt door de [View Designer](../../azure-monitor/platform/view-designer.md) en deze opnemen in een beheeroplossing.  

> [!NOTE]
> De voorbeelden in dit artikel gebruiken parameters en variabelen die vereist of gemeenschappelijk zijn voor beheeroplossingen en worden beschreven in [Ontwerp en een beheeroplossing bouwen in Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al weet hoe u [een beheeroplossing](solutions-creating.md) en de structuur van een oplossingsbestand maken.

## <a name="overview"></a>Overzicht
Als u een weergave wilt opnemen in een beheeroplossing, maakt u er een **bron** voor in het [oplossingsbestand.](solutions-creating.md)  De JSON die de gedetailleerde configuratie van de weergave beschrijft, is meestal wel complex en niet iets dat een typische oplossingsauteur handmatig zou kunnen maken.  De meest voorkomende methode is om de weergave te maken met behulp van de [View Designer,](../../azure-monitor/platform/view-designer.md)exporteren en vervolgens de gedetailleerde configuratie toe te voegen aan de oplossing.

De basisstappen om een weergave aan een oplossing toe te voegen zijn als volgt.  Elke stap wordt in de onderstaande secties nader beschreven.

1. Exporteer de weergave naar een bestand.
2. Maak de weergavebron in de oplossing.
3. Voeg de weergavedetails toe.

## <a name="export-the-view-to-a-file"></a>De weergave exporteren naar een bestand
Volg de instructies bij [Log Analytics View Designer](../../azure-monitor/platform/view-designer.md) om een weergave naar een bestand te exporteren.  Het geëxporteerde bestand is in JSON-indeling met dezelfde [elementen als het oplossingsbestand.](solutions-solution-file.md)  

Het **bronnenelement** van het weergavebestand heeft een bron met een type **Microsoft.OperationalInsights/workspaces** dat de werkruimte Log Analytics vertegenwoordigt.  Dit element heeft een subelement met een type **weergaven** dat de weergave vertegenwoordigt en de gedetailleerde configuratie bevat.  U kopieert de details van dit element en kopieert het vervolgens naar uw oplossing.

## <a name="create-the-view-resource-in-the-solution"></a>De weergavebron in de oplossing maken
Voeg de volgende weergavebron toe aan het **bronnenelement** van uw oplossingsbestand.  Dit maakt gebruik van variabelen die hieronder worden beschreven die u ook moet toevoegen.  Houd er rekening mee dat de eigenschappen **Dashboard** en **OverviewTile** tijdelijke aanduidingen zijn die u overschrijft met de bijbehorende eigenschappen uit het geëxporteerde weergavebestand.

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

Voeg de volgende variabelen toe aan het element variabelen van het oplossingsbestand en vervang de waarden aan die voor uw oplossing.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Houd er rekening mee dat u de volledige weergavebron uit uw geëxporteerde weergavebestand kopiëren, maar dat u de volgende wijzigingen moet aanbrengen om deze in uw oplossing te laten werken.  

* Het **type** voor de weergavebron moet worden gewijzigd van **weergaven** naar **Microsoft.OperationalInsights/workspaces**.
* De **eigenschap naam** voor de weergavebron moet worden gewijzigd om de naam van de werkruimte op te nemen.
* De afhankelijkheid van de werkruimte moet worden verwijderd omdat de werkruimtebron niet is gedefinieerd in de oplossing.
* De eigenschap **DisplayName** moet aan de weergave worden toegevoegd.  De **id,** **naam**en **displayname** moeten allemaal overeenkomen.
* Parameternamen moeten worden gewijzigd om overeen te komen met de vereiste set parameters.
* Variabelen moeten in de oplossing worden gedefinieerd en in de juiste eigenschappen worden gebruikt.

### <a name="log-analytics-api-version"></a>Log Analytics API-versie
Alle Log Analytics-resources die zijn gedefinieerd in een Resource Manager-sjabloon hebben een **eigenschapsapiVersie** die de versie van de API definieert die de bron moet gebruiken.  Deze versie is anders voor weergaven met query's die de [oudere en de bijgewerkte querytaal](../../azure-monitor/log-query/log-query-overview.md)gebruiken.  

 In de volgende tabel worden de API-versies van Log Analytics opgegeven voor weergaven in verouderde en bijgewerkte werkruimten: 

| Werkruimteversie | API-versie | Query’s uitvoeren |
|:---|:---|:---|
| v1 (nalatenschap)   | 2015-11-01-preview | Legacy-indeling.<br> Voorbeeld: type=gebeurtenisgebeurtenisLevelName = Fout  |
| v2 (geüpgraded) | 2015-11-01-preview | Legacy-indeling.  Geconverteerd naar een geüpgradede indeling bij installatie.<br> Voorbeeld: type=gebeurtenisgebeurtenisLevelName = Fout<br>Geconverteerd naar: gebeurtenis &#124; waar EventLevelName == "Fout"  |
| v2 (geüpgraded) | 2017-03-03-preview | Upgrade-indeling. <br>Voorbeeld: gebeurtenis &#124; waarbij EventLevelName == "Fout"  |


## <a name="add-the-view-details"></a>De weergavedetails toevoegen
De weergavebron in het geëxporteerde weergavebestand bevat twee elementen in het **eigenschappenelement** **Dashboard** en **OverviewTile** die de gedetailleerde configuratie van de weergave bevatten.  Kopieer deze twee elementen en de inhoud ervan naar het **eigenschappenelement** van de weergavebron in uw oplossingsbestand.

## <a name="example"></a>Voorbeeld
In het volgende voorbeeld wordt bijvoorbeeld een eenvoudig oplossingsbestand met een weergave weergegeven.  Ellipsen (...) worden om ruimteredenen weergegeven voor de inhoud **van dashboard** en **OverzichtTegel.**

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
* Meer informatie over het maken van [beheeroplossingen.](solutions-creating.md)
* Neem [automatiseringsboeken op in uw beheeroplossing.](solutions-resources-automation.md)
