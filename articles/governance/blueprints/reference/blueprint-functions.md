---
title: Azure Blueprints-functies
description: Beschrijft de functies die beschikbaar zijn voor gebruik met blauwdrukartefacten in azure blueprints-definities en -toewijzingen.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280675"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Functies voor gebruik met Azure Blueprints

Azure Blueprints biedt functies die een blauwdrukdefinitie dynamischer maken. Deze functies zijn voor gebruik met blauwdrukdefinities en blauwdrukartefacten. Een artefact resourcemanagersjabloon ondersteunt het volledige gebruik van Resource Manager-functies, naast het verkrijgen van een dynamische waarde via een blauwdrukparameter.

De volgende functies worden ondersteund:

- [Artefacten](#artifacts)
- [Concat](#concat)
- [parameters](#parameters)
- [resourceGroep](#resourcegroup)
- [resourceGroepen](#resourcegroups)
- [Abonnement](#subscription)

## <a name="artifacts"></a>Artefacten

`artifacts(artifactName)`

Geeft als resultaat een object met eigenschappen die zijn gevuld met uitvoer van blauwdrukken.

> [!NOTE]
> De `artifacts()` functie kan niet worden gebruikt vanuit een resourcemanagersjabloon. De functie kan alleen worden gebruikt in de blauwdrukdefinitie JSON of in het artefact JSON bij het beheren van de blauwdruk met Azure PowerShell of REST API als onderdeel van [Blueprints-as-code](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| artefactNaam |Ja |tekenreeks |De naam van een blauwdruk artefact. |

### <a name="return-value"></a>Retourwaarde

Een object met uitvoereigenschappen. De **eigenschappen van de uitvoer** zijn afhankelijk van het type blauwdrukartefact waarnaar wordt verwezen. Alle typen volgen de indeling:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefact beleidstoewijzing

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefact resourcemanager-sjabloon

De **uitvoereigenschappen** van het geretourneerde object worden gedefinieerd in de sjabloon Resourcebeheer en worden geretourneerd door de implementatie.

#### <a name="role-assignment-artifact"></a>Artefact roltoewijzing

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Voorbeeld

Een resourcemanager-sjabloonartefact met de id _myTemplateArtifact_ met de volgende eigenschap voorbeelduitvoer:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Enkele voorbeelden van het ophalen van gegevens uit het _myTemplateArtifact-voorbeeld_ zijn:

| Expressie | Type | Waarde |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Matrix | \["eerst", "tweede"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Tekenreeks | "Eerst" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Tekenreeks | "Mijn string waarde" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "mijn waarde", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Tekenreeks | "Mijn waarde" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Booleaanse waarde | True |

## <a name="concat"></a>Concat

`concat(string1, string2, string3, ...)`

Combineert meerdere tekenreekswaarden en retourneert de samengevoegde tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| tekenreeks1 |Ja |tekenreeks |De eerste waarde voor samenvoeging. |
| aanvullende argumenten |Nee |tekenreeks |Aanvullende waarden in opeenvolgende volgorde voor samenvoeging |

### <a name="return-value"></a>Retourwaarde

Een reeks samengevoegde waarden.

### <a name="remarks"></a>Opmerkingen

De azure blueprint-functie verschilt van de sjabloonfunctie Azure Resource Manager, omdat deze alleen met tekenreeksen werkt.

### <a name="example"></a>Voorbeeld

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Geeft als resultaat een parameterwaarde voor een blauwdruk. De opgegeven parameternaam moet worden gedefinieerd in de blauwdrukdefinitie of in blauwdrukartefacten.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |tekenreeks |De naam van de parameter om terug te keren. |

### <a name="return-value"></a>Retourwaarde

De waarde van de opgegeven parameter blauwdruk of blauwdrukartefact.

### <a name="remarks"></a>Opmerkingen

De azure blueprint-functie verschilt van de sjabloonfunctie Azure Resource Manager, omdat deze alleen werkt met blauwdrukparameters.

### <a name="example"></a>Voorbeeld

_ParameterprincipalIds definiëren_ in de blauwdrukdefinitie:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Gebruik vervolgens _principalIds_ als `parameters()` argument voor in een blauwdruk artefact:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Geeft als resultaat een object dat de huidige resourcegroep vertegenwoordigt.

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object heeft de volgende indeling:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Opmerkingen

De azure blueprint-functie verschilt van de sjabloonfunctie Azure Resource Manager. De `resourceGroup()` functie kan niet worden gebruikt in een artefact op abonnementsniveau of de blauwdrukdefinitie. Het kan alleen worden gebruikt in blauwdrukartefacten die deel uitmaken van een artefact van een brongroep.

Een veelgebruikt `resourceGroup()` gebruik van de functie is het maken van resources op dezelfde locatie als het artefact van de resourcegroep.

### <a name="example"></a>Voorbeeld

Als u de locatie van de resourcegroep wilt gebruiken, moet u de blauwdrukdefinitie of tijdens toewijzing als locatie voor een ander artefact instellen, een object met tijdelijke aanduiding voor resourcegroepen declareren in uw blauwdrukdefinitie. In dit voorbeeld is _NetworkingPlaceholder_ de naam van de tijdelijke aanduiding voor de resourcegroep.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Gebruik de `resourceGroup()` functie vervolgens in de context van een blauwdrukartefact dat zich richt op een object met tijdelijke aanduiding voor resourcegroepen. In dit voorbeeld wordt het sjabloonartefact geïmplementeerd in de resourcegroep _NetworkingPlaceholder_ en biedt het _parameterbronLocatie_ dynamisch gevuld met de locatie van de _brongroep NetworkingPlaceholder_ bron groep aan de sjabloon. De locatie van de resourcegroep _NetworkingPlaceholder_ kan statisch zijn gedefinieerd op basis van de blauwdrukdefinitie of dynamisch zijn gedefinieerd tijdens de toewijzing. In beide gevallen wordt het sjabloonartefact verstrekt die informatie als parameter en gebruikt deze om de resources naar de juiste locatie te implementeren.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroepen

`resourceGroups(placeholderName)`

Geeft als resultaat een object dat het opgegeven artefact van de resourcegroep vertegenwoordigt. In `resourceGroup()`tegenstelling tot , waarvoor context van het artefact vereist is, wordt deze functie gebruikt om de eigenschappen van een specifieke tijdelijke aanduiding voor resourcegroepen te krijgen wanneer deze niet in de context van die resourcegroep is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| tijdelijke aanduidingNaam |Ja |tekenreeks |De tijdelijke aanduidingsnaam van het artefact van de resourcegroep om terug te keren. |

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object heeft de volgende indeling:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Voorbeeld

Als u de locatie van de resourcegroep wilt gebruiken, moet u de blauwdrukdefinitie of tijdens toewijzing als locatie voor een ander artefact instellen, een object met tijdelijke aanduiding voor resourcegroepen declareren in uw blauwdrukdefinitie. In dit voorbeeld is _NetworkingPlaceholder_ de naam van de tijdelijke aanduiding voor de resourcegroep.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Gebruik vervolgens `resourceGroups()` de functie uit de context van een blauwdrukartefact om een verwijzing naar het object tijdelijke aanduiding voor de brongroep te krijgen. In dit voorbeeld wordt het sjabloonartefact buiten de brongroep _NetworkingPlaceholder_ geïmplementeerd en _parameterartefacten biedenLocatie_ dynamisch gevuld met de locatie van de _brongroep NetworkingPlaceholder_ bron groep aan de sjabloon. De locatie van de resourcegroep _NetworkingPlaceholder_ kan statisch zijn gedefinieerd op basis van de blauwdrukdefinitie of dynamisch zijn gedefinieerd tijdens de toewijzing. In beide gevallen wordt het sjabloonartefact verstrekt die informatie als parameter en gebruikt deze om de resources naar de juiste locatie te implementeren.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>abonnement

`subscription()`

Retourneert details over het abonnement voor de huidige blauwdruktoewijzing.

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object heeft de volgende indeling:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Voorbeeld

Gebruik de weergavenaam van `concat()` het abonnement en de functie om een naamgevingsconventie te maken die als _parameterresourceNaam_ is doorgegeven aan het sjabloonartefact.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van de blauwdruk](../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)