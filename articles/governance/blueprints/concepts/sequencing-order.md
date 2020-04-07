---
title: De volgorde van de implementatiereeks begrijpen
description: Meer informatie over de standaardvolgorde waarin blauwdrukartefacten worden geïmplementeerd tijdens een blauwdruktoewijzing en hoe u de implementatievolgorde aanpassen.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 41b1b1ada5b7c6c919f227927001570332eeccbf
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677564"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>De implementatiereeks in Azure Blueprints begrijpen

Azure Blueprints gebruikt een **volgorde om** de volgorde van het maken van resources te bepalen bij het verwerken van de toewijzing van een blauwdrukdefinitie. In dit artikel worden de volgende concepten uitgelegd:

- De standaardvolgorde die wordt gebruikt
- De bestelling aanpassen
- Hoe de aangepaste order wordt verwerkt

Er zijn variabelen in de JSON-voorbeelden die u moet vervangen door uw eigen waarden:

- Vervang `{YourMG}` door de naam van uw beheergroep

## <a name="default-sequencing-order"></a>Standaardvolgorde voor sequencing

Als de blauwdrukdefinitie geen richtlijn bevat voor het plaatsen van artefacten of als de richtlijn null is, wordt de volgende volgorde gebruikt:

- Artefacten op **abonnementsniveauroltoewijzing** gesorteerd op artefactnaam
- Kenmerken van **beleidstoewijzing** op abonnementsniveau gesorteerd op artefactnaam
- Azure **Resource Manager-sjabloonartefacten** op abonnementsniveau gesorteerd op artefactnaam
- **Artefacten van resourcegroepen** (inclusief onderliggende artefacten) gesorteerd op tijdelijke aanduidingsnaam

Binnen elk artefact **van de resourcegroep** wordt de volgende volgorde gebruikt voor artefacten die binnen die resourcegroep moeten worden gemaakt:

- Onderliggende **rolartefacten** van resourcegroep die zijn gesorteerd op artefactnaam
- Onderliggende **onderliggende beleidsartefacten** van resourcegroep gesorteerd op artefactnaam
- Azure **Resource Manager-sjabloonartefacten** voor resourcegroepdie is gesorteerd op artefactnaam

> [!NOTE]
> Het gebruik van [artefacten()](../reference/blueprint-functions.md#artifacts) creëert een impliciete afhankelijkheid van het artefact waarnaar wordt verwezen.

## <a name="customizing-the-sequencing-order"></a>De volgorde aanpassen

Bij het samenstellen van grote blauwdrukdefinities kan het nodig zijn dat resources in een specifieke volgorde worden gemaakt. Het meest voorkomende gebruikspatroon van dit scenario is wanneer een blauwdrukdefinitie verschillende Azure Resource Manager-sjablonen bevat. Azure Blueprints verwerkt dit patroon door de volgorde te definiëren.

De bestelling wordt uitgevoerd door `dependsOn` het definiëren van een woning in de JSON. De blauwdrukdefinitie voor resourcegroepen en artefactobjecten ondersteunen deze eigenschap. `dependsOn`is een reeks artefactnamen die het specifieke artefact moet maken voordat het wordt gemaakt.

> [!NOTE]
> Bij het maken van blauwdrukobjecten krijgt elke artefactbron zijn naam van de bestandsnaam, als u [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)of het URL-eindpunt gebruikt, als u [DE API REST gebruikt.](/rest/api/blueprints/artifacts/createorupdate)
> _resourceGroepverwijzingen_ in artefacten moeten overeenkomen met de referenties die zijn gedefinieerd in de blauwdrukdefinitie.

### <a name="example---ordered-resource-group"></a>Voorbeeld - geordende resourcegroep

Deze voorbeeldblauwdrukdefinitie heeft een resourcegroep die een aangepaste volgorde heeft `dependsOn`gedefinieerd door een waarde voor , samen met een standaardresourcegroep, aan te geven. In dit geval wordt het artefact met de naam **assignPolicyTags** verwerkt vóór de **geordende-rg-brongroep.**
**standaard-rg** wordt verwerkt volgens de standaardvolgorde.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Voorbeeld - artefact met aangepaste volgorde

Dit voorbeeld is een beleidsartefact dat afhankelijk is van een Azure Resource Manager-sjabloon. Standaard wordt een beleidsartefact gemaakt vóór de sjabloon Azure Resource Manager. Met deze volgorde kan het beleidsartefact wachten tot de azure resource manager-sjabloon is gemaakt.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Voorbeeld - artefact op abonnementsniveau, afhankelijk van een resourcegroep

Dit voorbeeld is dat een resourcemanagersjabloon die op abonnementsniveau is geïmplementeerd, afhankelijk is van een resourcegroep. In standaardvolgorde worden artefacten op abonnementsniveau gemaakt voordat resourcegroepen en onderliggende artefacten in die brongroepen worden gebruikt. De resourcegroep wordt als volgt gedefinieerd in de blauwdrukdefinitie:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Het sjabloonartefact op abonnementsniveau, afhankelijk van de **resourcegroep wachten op mij,** wordt als volgt gedefinieerd:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>De aangepaste reeks verwerken

Tijdens het creatieproces wordt een topologische soort gebruikt om de afhankelijkheidsgrafiek van de blauwdrukkenartefacten te maken. De controle zorgt ervoor dat elk niveau van afhankelijkheid tussen resourcegroepen en artefacten wordt ondersteund.

Als een artefactafhankelijkheid wordt gedeclareerd die de standaardvolgorde niet zou wijzigen, wordt er geen wijziging aangebracht. Een voorbeeld is een resourcegroep die afhankelijk is van een beleid op abonnementsniveau. Een ander voorbeeld is een toewijzing van een resourcegroep 'standaard-rg' onderliggend beleid die afhankelijk is van de toewijzing van een onderliggende onderliggende groep resourcegroep. In beide gevallen `dependsOn` zou de standaardvolgorde niet zijn gewijzigd en zouden er geen wijzigingen worden aangebracht.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van de blauwdruk](lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](parameters.md)te gebruiken.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)