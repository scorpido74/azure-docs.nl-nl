---
title: Overzicht van weergavedefinitie
description: Beschrijft het concept van het maken van weergavedefinitie voor Azure Managed Applications.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: d0c60f5738bf634f9d43d6d4f0d78c1239b7ff3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650693"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Definitieartefact weergeven in Azure Managed Applications

Weergavedefinitie is een optioneel artefact in Azure Managed Applications. Hiermee u de overzichtspagina aanpassen en meer weergaven toevoegen, zoals statistieken en aangepaste bronnen.

Dit artikel geeft een overzicht van het artefact met de weergavedefinitie en de mogelijkheden ervan.

## <a name="view-definition-artifact"></a>Definitie-artefact weergeven

Het artefact voor weergavedefinitie moet **viewDefinition.json** worden genoemd en op hetzelfde niveau worden geplaatst als **createUiDefinition.json** en **mainTemplate.json** in het .zip-pakket dat een beheerde toepassingsdefinitie maakt. Zie [Een Azure Managed Application-definitie publiceren](publish-managed-app-definition-quickstart.md) voor meer informatie over het maken van het .zip-pakket en het publiceren van een beheerde toepassingsdefinitie

## <a name="view-definition-schema"></a>Definitieschema weergeven

Het bestand **viewDefinition.json** heeft `views` slechts één eigenschap op het hoogste niveau, wat een array met weergaven is. Elke weergave wordt weergegeven in de gebruikersinterface van de beheerde toepassing als een afzonderlijk menu-item in de inhoudsopgave. Elke weergave `kind` heeft een eigenschap die het type weergave instelt. Het moet worden ingesteld op een van de volgende waarden: [Overzicht](#overview), [Statistieken](#metrics), [CustomResources](#custom-resources), [Verenigingen](#associations). Zie het huidige [JSON-schema voor viewDefinition.json voor](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)meer informatie.

Voorbeeld JSON voor weergavedefinitie:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Overzicht

`"kind": "Overview"`

Wanneer u deze weergave in **viewDefinition.json**opgeeft, wordt de standaardpagina Overzicht in uw beheerde toepassing overschreven.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Eigenschap|Vereist|Beschrijving|
|---------|---------|---------|
|koptekst|Nee|De koptekst van de overzichtspagina.|
|description|Nee|De beschrijving van uw beheerde toepassing.|
|opdrachten|Nee|De array met extra werkbalkknoppen van de [overzichtspagina,](#commands)zie opdrachten .|

![Overzicht](./media/view-definition/overview.png)

## <a name="metrics"></a>Metrische gegevens

`"kind": "Metrics"`

Met de weergave Metrische gegevens u gegevens verzamelen en samenvoegen uit uw beheerde toepassingsbronnen in [Azure Monitor Metrics](../../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Eigenschap|Vereist|Beschrijving|
|---------|---------|---------|
|displayName|Nee|De weergegeven titel van de weergave.|
|versie|Nee|De versie van het platform gebruikt om de weergave weer te geven.|
|Grafieken|Ja|De array met grafieken van de metrische gegevenspagina.|

### <a name="chart"></a>Grafiek

|Eigenschap|Vereist|Beschrijving|
|---------|---------|---------|
|displayName|Ja|De weergegeven titel van de grafiek.|
|chartType|Nee|De visualisatie die u voor deze grafiek moet gebruiken. Standaard wordt een lijndiagram gebruikt. Ondersteunde grafiektypen: `Bar, Line, Area, Scatter`.|
|metrics|Ja|De reeks statistieken die op deze grafiek moeten worden weergegeven. Zie [Ondersteunde statistieken met Azure Monitor](../../azure-monitor/platform/metrics-supported.md) voor meer informatie over statistieken die worden ondersteund in Azure-portal|

### <a name="metric"></a>Gegevens

|Eigenschap|Vereist|Beschrijving|
|---------|---------|---------|
|name|Ja|De naam van de statistiek.|
|aggregatieType|Ja|Het aggregatietype dat moet worden gebruikt voor deze statistiek. Ondersteunde aggregatietypen:`none, sum, min, max, avg, unique, percentile, count`|
|naamruimte|Nee|Aanvullende informatie om te gebruiken bij het bepalen van de juiste metrics provider.|
|resourceTagFilter|Nee|De array resourcetags (wordt `or` gescheiden met woord) waarvoor statistieken worden weergegeven. Past bovenop het filter van het brontype toe.|
|resourceType|Ja|Het resourcetype waarvoor statistieken worden weergegeven.|

![Metrische gegevens](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Aangepaste resources

`"kind": "CustomResources"`

U meerdere weergaven van dit type definiëren. Elke weergave vertegenwoordigt een **uniek** aangepast resourcetype van de aangepaste provider die u hebt gedefinieerd in **mainTemplate.json**. Zie Overzicht van Azure [Custom Providers Preview](../custom-providers/overview.md)voor een inleiding tot aangepaste providers.

In deze weergave u get-, put-, delete- en postbewerkingen uitvoeren voor uw aangepaste resourcetype. POST-bewerkingen kunnen algemene aangepaste acties of aangepaste acties zijn in een context van uw aangepaste resourcetype.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Eigenschap|Vereist|Beschrijving|
|---------|---------|---------|
|displayName|Ja|De weergegeven titel van de weergave. De titel moet **uniek** zijn voor elke weergave CustomResources in uw **viewDefinition.json**.|
|versie|Nee|De versie van het platform gebruikt om de weergave weer te geven.|
|resourceType|Ja|Het aangepaste resourcetype. Moet een **uniek** aangepast resourcetype van uw aangepaste provider zijn.|
|Pictogram|Nee|Het pictogram van de weergave. Lijst met voorbeeldpictogrammen wordt gedefinieerd in [JSON-schema](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Nee|Maak een ui-definitieschema voor het maken van de opdracht Aangepaste resource. Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md) voor een inleiding tot het maken van ui-definities|
|opdrachten|Nee|De array met extra werkbalkknoppen van de weergave CustomResources, zie [opdrachten](#commands).|
|kolommen|Nee|De array met kolommen van de aangepaste resource. Als deze niet `name` is gedefinieerd, wordt de kolom standaard weergegeven. De kolom `"key"` moet `"displayName"`hebben en . Geef voor sleutel de sleutel van de eigenschap op die in een weergave moet worden weergegeven. Als u zich hebt genest, gebruikt u `"key": "name"` `"key": "properties.property1"`bijvoorbeeld stip als scheidingsteken of . Geef voor weergavenaam de weergavenaam op van de eigenschap die in een weergave moet worden weergegeven. U ook `"optional"` een woning bieden. Wanneer de kolom is ingesteld op true, wordt deze standaard verborgen in een weergave.|

![Aangepaste resources](./media/view-definition/customresources.png)

## <a name="commands"></a>Opdrachten

Opdrachten is een reeks extra werkbalkknoppen die op de pagina worden weergegeven. Elke opdracht vertegenwoordigt een POST-actie van uw Azure Custom Provider gedefinieerd in **mainTemplate.json**. Zie overzicht van Azure [Custom Providers](../custom-providers/overview.md)voor een inleiding tot aangepaste providers.

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Eigenschap|Vereist|Beschrijving|
|---------|---------|---------|
|displayName|Ja|De weergegeven naam van de opdrachtknop.|
|path|Ja|De aangepaste actienaam van de provider. De actie moet worden gedefinieerd in **mainTemplate.json**.|
|Pictogram|Nee|Het pictogram van de opdrachtknop. Lijst met voorbeeldpictogrammen wordt gedefinieerd in [JSON-schema](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Nee|UI Definition-schema maken voor opdracht. Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.|

## <a name="associations"></a>Verenigingen

`"kind": "Associations"`

U meerdere weergaven van dit type definiëren. Met deze weergave u bestaande resources koppelen aan de beheerde toepassing via de aangepaste provider die u hebt gedefinieerd in **mainTemplate.json.** Zie Overzicht van Azure [Custom Providers Preview](../custom-providers/overview.md)voor een inleiding tot aangepaste providers.

In deze weergave u bestaande `targetResourceType`Azure-resources uitbreiden op basis van de. Wanneer een resource is geselecteerd, wordt een onboarding-aanvraag gemaakt voor de **openbare** aangepaste provider, die een bijwerking op de resource kan toepassen. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Eigenschap|Vereist|Beschrijving|
|---------|---------|---------|
|displayName|Ja|De weergegeven titel van de weergave. De titel moet **uniek** zijn voor elke weergave Verenigingen in uw **viewDefinition.json**.|
|versie|Nee|De versie van het platform gebruikt om de weergave weer te geven.|
|targetResourceType|Ja|Het doelbrontype. Dit is het resourcetype dat wordt weergegeven voor resource onboarding.|
|createUIDefinition|Nee|Maak een UI Definition-schema voor de opdracht Koppelingsbron maken. Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md) voor een inleiding tot het maken van ui-definities|

## <a name="looking-for-help"></a>Op zoek naar hulp

Als u vragen hebt over Azure Managed Applications, probeert u vragen te stellen op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Een soortgelijke vraag kan al zijn gesteld en beantwoord, dus controleer eerst voor het posten. Voeg de `azure-managedapps` tag toe om snel te reageren!

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht van door Azure beheerde toepassingen](overview.md) voor algemene informatie over beheerde toepassingen.
- Zie overzicht van Azure [Custom Providers](../custom-providers/overview.md)voor een inleiding tot aangepaste providers.
- Zie [Zelfstudie: Beheerde toepassing maken met aangepaste provideracties en resourcetypen](tutorial-create-managed-app-with-custom-provider.md) voor het maken van een Azure Managed-toepassing met Azure Custom Providers.
