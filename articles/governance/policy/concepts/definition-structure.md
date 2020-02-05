---
title: Details van de structuur van de beleids definitie
description: Hierin wordt beschreven hoe beleids definities worden gebruikt om conventies voor Azure-resources in uw organisatie in te richten.
ms.date: 11/26/2019
ms.topic: conceptual
ms.openlocfilehash: 7502c1c9a2e125052abf71e50273fbd9bab15cd1
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989872"
---
# <a name="azure-policy-definition-structure"></a>Structuur van Azure-beleidsdefinities

Resource-beleidsdefinities worden gebruikt door Azure Policy tot stand brengen van conventies voor resources. Elke definitie beschrijft resourcenaleving en wat moet worden uitgevoerd wanneer een resource niet compatibel is zijn doorgevoerd.
Door te definiëren verdragen, kunt u kosten beheren en meer resources eenvoudig beheren. U kunt bijvoorbeeld opgeven dat alleen bepaalde typen virtuele machines zijn toegestaan. Of u kunt vereisen dat alle resources een bepaald label hebben. Beleidsregels worden overgenomen door alle onderliggende resources. Als een beleid wordt toegepast op een resourcegroep, is het van toepassing op alle resources in die resourcegroep.

Het schema voor het definitie beleid vindt u hier: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

JSON kunt u een beleidsdefinitie maken. De beleidsdefinitie bevat elementen voor:

- modus
- parameters
- Weergavenaam
- description
- beleidsregel
  - logische evaluatie
  - effect

De volgende JSON ziet u bijvoorbeeld een beleid dat beperkt welke resources zijn geïmplementeerd:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Alle Azure Policy-voor beelden zijn Azure Policy-voor [beelden](../samples/index.md).

## <a name="mode"></a>Modus

De **modus** is geconfigureerd, afhankelijk van of het beleid is gericht op een Azure Resource Manager eigenschap of een bron provider eigenschap.

### <a name="resource-manager-modes"></a>Resource Manager-modi

De **modus** bepaalt welke resourcetypen voor een beleid wordt geëvalueerd. De ondersteunde modi zijn:

- `all`: resourcegroepen en alle resourcetypen evalueren
- `indexed`: alleen evalueren resourcetypen die ondersteuning bieden voor labels en de locatie

We raden u aan **modus** naar `all` in de meeste gevallen. Alle beleidsdefinities die zijn gemaakt via de portal gebruiken de `all` modus. Als u PowerShell of Azure CLI gebruikt, kunt u opgeven de **modus** parameter handmatig. Als de beleidsdefinitie bevat geen een **modus** waarde, wordt standaard `all` in Azure PowerShell en in het `null` in de Azure CLI. Een `null` modus is hetzelfde als wanneer u `indexed` ter ondersteuning van achterwaartse compatibiliteit.

`indexed` moet worden gebruikt bij het maken van beleid dat labels of locaties worden afgedwongen. Hoewel dit niet vereist is, voor komt u dat resources die tags en locaties ondersteunen, niet kunnen worden weer gegeven als niet-compatibel in de nalevings resultaten. De uitzondering hierop is **resourcegroepen**. Voor die locatie of de tags voor een resourcegroep afdwingen moeten ingesteld **modus** naar `all` en een specifiek doel de `Microsoft.Resources/subscriptions/resourceGroups` type. Zie voor een voorbeeld [afdwingen groep resourcetags](../samples/enforce-tag-rg.md). Zie [tag-ondersteuning voor Azure-resources](../../../azure-resource-manager/management/tag-support.md)voor een lijst met resources die Tags ondersteunen.

### <a name="a-nameresource-provider-modes-resource-provider-modes-preview"></a><a name="resource-provider-modes" />resource provider modi (preview-versie)

De volgende resource provider modi worden momenteel ondersteund tijdens de preview-versie:

- `Microsoft.ContainerService.Data` voor het beheren van regels voor toegangs beheer in de [Azure Kubernetes-service](../../../aks/intro-kubernetes.md). Beleids regels die gebruikmaken van deze resource provider modus **moeten** het [EnforceRegoPolicy](./effects.md#enforceregopolicy) -effect gebruiken.
- `Microsoft.Kubernetes.Data` voor het beheren van zelf-beheerde AKS-engine Kubernetes-clusters op Azure.
  Beleids regels die gebruikmaken van deze resource provider modus **moeten** het [EnforceOPAConstraint](./effects.md#enforceopaconstraint) -effect gebruiken.
- `Microsoft.KeyVault.Data` voor het beheren van kluizen en certificaten in [Azure Key Vault](../../../key-vault/key-vault-overview.md).

> [!NOTE]
> De resource provider modi bieden alleen ondersteuning voor ingebouwde beleids definities en bieden geen ondersteuning voor initiatieven als er een preview-versie beschikbaar is.

## <a name="parameters"></a>Parameters

Parameters helpen uw beleidsbeheer te vereenvoudigen door het aantal beleidsdefinities te verminderen. Met parameters, zoals de velden in een formulier – `name`, `address`, `city`, `state`. Deze parameters altijd hetzelfde blijven, maar hun waarden worden gewijzigd op basis van het afzonderlijke invullen van het formulier.
Parameters werken op dezelfde manier als het samenstellen van beleid. Door parameters te nemen in de beleidsdefinitie van een, kunt u beleid voor verschillende scenario's met behulp van verschillende waarden gebruiken.

> [!NOTE]
> Para meters kunnen worden toegevoegd aan een bestaande en toegewezen definitie. De nieuwe para meter moet de eigenschap **DefaultValue** bevatten. Dit voorkomt dat bestaande toewijzingen van het beleid of initiatief indirect wordt ongeldig gemaakt.

### <a name="parameter-properties"></a>Parameter eigenschappen

Een para meter heeft de volgende eigenschappen die worden gebruikt in de beleids definitie:

- **naam**: de naam van de para meter. Wordt gebruikt door de implementatie functie `parameters` binnen de beleids regel. Zie [een parameter waarde gebruiken](#using-a-parameter-value)voor meer informatie.
- `type`: bepaalt of de para meter een **teken reeks**, een **matrix**, een **object**, een **Booleaanse waarde**, een **geheel getal**, een **float**of een **datum/tijd**is.
- `metadata`: definieert subeigenschappen die voornamelijk worden gebruikt door de Azure Portal om gebruikers vriendelijke informatie weer te geven:
  - `description`: de uitleg van het gebruik van de para meter voor. Kan worden gebruikt om voor beelden te bieden van acceptabele waarden.
  - `displayName`: de beschrijvende naam die wordt weer gegeven in de portal voor de para meter.
  - `strongType`: (optioneel) gebruikt bij het toewijzen van de beleids definitie via de portal. Biedt een context bewuste lijst. Zie [strongType](#strongtype)voor meer informatie.
  - `assignPermissions`: (optioneel) Stel in op _waar_ als u wilt Azure Portal roltoewijzingen wilt maken tijdens de beleids toewijzing. Deze eigenschap is handig voor het geval u machtigingen wilt toewijzen buiten het toewijzings bereik. Er is één roltoewijzing per roldefinitie in het beleid (of per functie definitie in alle beleids regels in het initiatief). De parameter waarde moet een geldige resource of een geldig bereik zijn.
- `defaultValue`: (optioneel) Hiermee stelt u de waarde van de para meter in een toewijzing in als er geen waarde is opgegeven.
  Vereist bij het bijwerken van een bestaande beleids definitie die is toegewezen.
- `allowedValues`: (optioneel) biedt een matrix met waarden die door de para meter worden geaccepteerd tijdens de toewijzing.

U kunt bijvoorbeeld een beleids definitie definiëren om de locaties te beperken waar resources kunnen worden geïmplementeerd. Een para meter voor die beleids definitie kan **allowedLocations**zijn. Deze para meter wordt gebruikt door elke toewijzing van de beleids definitie om de geaccepteerde waarden te beperken. Het gebruik van **strongType** biedt een verbeterde ervaring bij het volt ooien van de toewijzing via de portal:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Een parameter waarde gebruiken

In de beleids regel verwijst u naar para meters met de volgende `parameters` functie syntaxis:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Dit voor beeld verwijst naar de **allowedLocations** -para meter die is gedemonstreerd in [parameter eigenschappen](#parameter-properties).

### <a name="strongtype"></a>strongType

Binnen de `metadata` eigenschap kunt u **strongType** gebruiken om een multi-select lijst met opties te bieden in de Azure Portal. Toegestane waarden voor **strongType** momenteel opnemen:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Definitielocatie

Tijdens het maken van een initiatief of het beleid, is het nodig zijn om de definitielocatie te geven. De locatie van de definitie moet op een beheergroep of een abonnement. Deze locatie bepaalt het bereik waarvoor de initiatief of het beleid kan worden toegewezen. Resources moet directe leden van of kinderen binnen de hiërarchie van de locatie van de definitie om de doelgroep voor het toewijzen van te zijn.

Als de locatie van de definitie a:

- **Abonnement** - alleen de resources in dat abonnement aan het beleid kunnen worden toegewezen.
- **Beheergroep** - alleen resources in de onderliggende beheergroepen en onderliggende abonnementen kunnen worden toegewezen op het beleid. Als u van plan bent om toe te passen van de beleidsdefinitie voor verschillende abonnementen, is de locatie moet een beheergroep met deze abonnementen.

## <a name="display-name-and-description"></a>Weergavenaam en beschrijving

U gebruikt **displayName** en **beschrijving** om te bepalen van de beleidsdefinitie en een context bieden voor wanneer deze wordt gebruikt. **DisplayName** heeft een maximale lengte van _128_ tekens en een **Beschrijving** van Maxi maal _512_ tekens.

> [!NOTE]
> Tijdens het maken of bijwerken van een beleids definitie, de **id**, het **type**en de **naam** worden gedefinieerd door de Eigenschappen extern van de JSON en zijn deze niet nodig in het JSON-bestand. Het ophalen van de beleids definitie via SDK retourneert de eigenschappen **id**, **type**en **naam** als onderdeel van de JSON, maar elk is een alleen-lezen informatie met betrekking tot de beleids definitie.

## <a name="policy-rule"></a>Beleidsregel

De beleidsregel bestaat uit **als** en **vervolgens** blokken. In de **als** blok, definieert u een of meer voorwaarden die opgeven wanneer het beleid wordt afgedwongen. U kunt logische operators toepassen op deze voorwaarden voor het definiëren van het scenario voor een beleid nauwkeurig.

In de **vervolgens** blok, definieert u het effect dat gebeurt er wanneer de **als** voorwaarden is voldaan.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Logische operators

Ondersteunde logische operators zijn:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

De **niet** syntaxis keert het resultaat van de voorwaarde. De **allOf** syntaxis (vergelijkbaar met de logische **en** bewerking) vereist dat alle voorwaarden wordt voldaan. De **dragen** syntaxis (vergelijkbaar met de logische **of** bewerking) vereist een of meer voorwaarden wordt voldaan.

U kunt logische operators nesten. Het volgende voorbeeld wordt een **niet** bewerking die is genest in een **allOf** bewerking.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Voorwaarden

In een voor waarde wordt geëvalueerd of een **veld** of de **waarde** -accessor voldoet aan bepaalde criteria. De ondersteunde voorwaarden zijn:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

Wanneer u de **zoals** en **notlike zijn** voorwaarden bieden u een jokerteken `*` in de waarde.
De waarde mag niet meer dan één jokerteken hebben `*`.

Wanneer u de voor waarden **match** en **notMatch** gebruikt, geeft u `#` een cijfer, `?` voor een letter, `.` op te zoeken naar een wille keurig teken en elk ander teken dat overeenkomt met het werkelijke teken. Hoewel **match** en **notMatch** hoofdletter gevoelig zijn, zijn alle andere voor waarden die een _stringValue_ evalueren niet hoofdletter gevoelig. Hoofdletter gevoelige alternatieven zijn beschikbaar in **matchInsensitively** en **notMatchInsensitively**. Zie voor voorbeelden van [toestaan verschillende bestandsnaampatronen](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Velden

Voorwaarden zijn samengesteld met behulp van velden. Een veld overeenkomt met eigenschappen in de nettolading van de resource-aanvraag en een beschrijving van de status van de resource.

De volgende velden worden ondersteund:

- `name`
- `fullName`
  - Retourneert de volledige naam van de resource. De volledige naam van een resource is de naam van de resource voorafgegaan door een bovenliggende resource-namen (bijvoorbeeld ' MijnServer/MijnDatabase').
- `kind`
- `type`
- `location`
  - Gebruik **Global** voor resources die de locatie neutraal. Zie voor [beelden: toegestane locaties](../samples/allowed-locations.md)voor een voor beeld.
- `identity.type`
  - Retourneert het type [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md) dat is ingeschakeld voor de bron.
- `tags`
- `tags['<tagName>']`
  - De syntaxis van het haakje ondersteunt label namen met lees tekens zoals een afbreek streepje, een punt of een spatie.
  - Waar **\<tagName\>** is de naam van de code voor het valideren van de voorwaarde voor.
  - Voor beelden: `tags['Acct.CostCenter']` waarbij **acct. CostCenter** de naam van de tag is.
- `tags['''<tagName>''']`
  - De syntaxis van het haakje ondersteunt label namen met apostrofs in het teken met dubbele apostrofs.
  - Waarbij **'\<tagName\>'** de naam van het label is voor het valideren van de voor waarde voor.
  - Voor beeld: `tags['''My.Apostrophe.Tag''']` waarbij **' My. apostrof. tag '** de naam van de tag is.
- de eigenschap aliassen - Zie voor een lijst [aliassen](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`en `tags[tag.with.dots]` zijn nog steeds acceptabele manieren om een label veld te declareren. De voorkeurs expressies zijn echter die in de bovenstaande lijst.

#### <a name="use-tags-with-parameters"></a>Tags gebruiken met para meters

Een parameter waarde kan worden door gegeven aan een label veld. Door een para meter door te geven aan een label veld, verhoogt u de flexibiliteit van de beleids definitie tijdens beleids toewijzing.

In het volgende voor beeld wordt `concat` gebruikt voor het maken van een label veld met de naam de waarde van de para meter **tagName** . Als dat label niet bestaat, wordt het **wijzigings** effect gebruikt om de tag toe te voegen met behulp van de waarde van dezelfde benoemde tag die is ingesteld voor de bovenliggende resource groep van de gecontroleerde resources met behulp van de functie lookup `resourcegroup()`.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Waarde

Voor waarden kunnen ook worden gevormd met behulp van een **waarde**. **waarde** controleert de voor waarden op basis van [para meters](#parameters), [ondersteunde sjabloon functies](#policy-functions)of letterlijke tekens.
**waarde** wordt gekoppeld aan elke ondersteunde [voor waarde](#conditions).

> [!WARNING]
> Als het resultaat van een _sjabloon functie_ een fout is, mislukt de beleids evaluatie. Een mislukte evaluatie is een impliciete **weigering**. Zie voor meer informatie [sjabloon fouten voor komen](#avoiding-template-failures).

#### <a name="value-examples"></a>Voor beelden van waarden

In dit voor beeld van een beleids regel wordt de **waarde** gebruikt om het resultaat van de `resourceGroup()` functie en de eigenschap geretourneerde **naam** te vergelijken met een **like** -voor waarde van `*netrg`. De regel weigert resources die niet van het `Microsoft.Network/*` **type** zijn in een resource groep waarvan de naam eindigt op `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Dit voor beeld van een beleids regel gebruikt een **waarde** om te controleren of het resultaat van meerdere geneste functies **gelijk is aan** `true`. De regel weigert een resource die niet ten minste drie tags heeft.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Storingen in sjablonen voor komen

Het gebruik van _sjabloon functies_ in **waarde** biedt veel complexe geneste functies. Als het resultaat van een _sjabloon functie_ een fout is, mislukt de beleids evaluatie. Een mislukte evaluatie is een impliciete **weigering**. Een voor beeld van een **waarde** die mislukt in bepaalde scenario's:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

De voorbeeld beleidsregel hierboven maakt gebruik van [subtekenreeks ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) om de eerste drie tekens van de **naam** te vergelijken met **ABC**. Als de **naam** korter is dan drie tekens, resulteert de functie `substring()` in een fout. Als gevolg van deze fout wordt het beleid **geweigerd** .

Gebruik in plaats daarvan de functie [als ()](../../../azure-resource-manager/templates/template-functions-logical.md#if) om te controleren of de eerste drie tekens van de **naam** gelijk zijn aan **ABC** zonder dat een **naam** die korter is dan drie tekens, een fout veroorzaakt:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Met de gereviseerde beleids regel controleert `if()` de lengte van de **naam** voordat er wordt geprobeerd een `substring()` op te halen met een waarde van minder dan drie tekens. Als de **naam** te kort is, wordt de waarde ' niet beginnend met ABC ' geretourneerd in plaats van **ABC**. Een resource met een korte naam die niet met **ABC** begint, mislukt nog steeds de beleids regel, maar veroorzaakt geen fout meer tijdens de evaluatie.

### <a name="count"></a>Aantal

Voor waarden die tellen hoeveel leden van een matrix in de resource-nettolading voldoen aan een voor waarde-expressie, kunnen worden gevormd met de expressie **Count** . Bij algemene scenario's wordt gecontroleerd of ten minste één van ', ' precies één van ', ' alle of ' geen van ' de matrix leden voldoen aan de voor waarde. **aantal** evalueert elke [\[\*\]](#understanding-the--alias) lid van de alias matrix voor een voorwaarde expressie en geeft een som van de _werkelijke_ resultaten, die vervolgens vergeleken met de expressie operator.

De structuur van de **Count** -expressie is:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

De volgende eigenschappen worden gebruikt met **aantal**:

- **Count. Field** (vereist): bevat het pad naar de matrix en moet een matrix alias zijn. Als de matrix ontbreekt, wordt de expressie geëvalueerd naar _False_ zonder rekening te houden met de voor waarde-expressie.
- **Count. where** (optioneel): de voor waarde-expressie voor het afzonderlijk evalueren van elke [\[\*\] alias](#understanding-the--alias) matrix lid van **aantal. Field**. Als deze eigenschap niet is gegeven, worden alle matrix leden met het pad van ' Field ' geëvalueerd als _waar_. Elke [voor waarde](../concepts/definition-structure.md#conditions) kan worden gebruikt in deze eigenschap.
  [Logische Opera tors](#logical-operators) kunnen worden gebruikt in deze eigenschap om complexe evaluatie vereisten te maken.
- **\<voor waarde\>** (vereist): de waarde wordt vergeleken met het aantal items dat aan het **aantal is voldaan. where** -voor waarde-expressie. Er moet een numerieke [voor waarde](../concepts/definition-structure.md#conditions) worden gebruikt.

#### <a name="count-examples"></a>Aantal voor beelden

Voor beeld 1: controleren of een matrix leeg is

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Voor beeld 2: controleren op slechts één matrixlid om te voldoen aan de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Voor beeld 3: controleren of er ten minste één matrixlid is om te voldoen aan de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Voor beeld 4: controleren of alle leden van een object Matrix voldoen aan de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Voor beeld 5: controleren of alle leden van de teken reeks matrix voldoen aan de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Voor beeld 6: **veld** in **waarde** gebruiken om te controleren of alle matrix leden voldoen aan de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Voor beeld 7: Controleer of ten minste één matrixlid overeenkomt met meerdere eigenschappen in de voor waarde-expressie

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Effect

Azure Policy ondersteunt de volgende typen effect:

- **Toevoeg-** : voegt de gedefinieerde set velden toe aan de aanvraag
- **Audit**: genereert een waarschuwingsgebeurtenis in het activiteitenlogboek, maar niet de aanvraag mislukt
- **AuditIfNotExists**: er wordt een waarschuwings gebeurtenis in het activiteiten logboek gegenereerd als een gerelateerde resource niet bestaat
- **Weigeren**: genereert een gebeurtenis in het activiteitenlogboek en de aanvraag is mislukt
- **DeployIfNotExists**: implementeert een gerelateerde resource als deze nog niet bestaat
- **Uitgeschakelde**: resources voor naleving van de beleidsregel niet evalueren
- **EnforceOPAConstraint** (preview): Hiermee configureert u de open Policy Agent Admissions controller met gate keeper v3 voor zelf-beheerde Kubernetes-clusters in azure (preview-versie)
- **EnforceRegoPolicy** (preview): Hiermee configureert u de open Policy Agent Admissions-controller met gate keeper v2 in de Azure Kubernetes-service
- **Wijzigen**: de gedefinieerde labels worden toegevoegd, bijgewerkt of verwijderd uit een resource

Zie voor meer informatie over elk effect, de volg orde van evaluatie, eigenschappen en voor beelden [informatie over Azure Policy effecten](effects.md).

### <a name="policy-functions"></a>Beleidsfuncties

Alle [Resource Manager-sjabloon functies](../../../azure-resource-manager/templates/template-functions.md) zijn beschikbaar voor gebruik in een beleids regel, met uitzonde ring van de volgende functies en door de gebruiker gedefinieerde functies:

- copyIndex()
- implementatie ()
- orderverzamellijst
- newGuid()
- pickZones()
- providers ()
- verwijzing ()
- resourceId()
- variabelen ()

De volgende functies zijn beschikbaar voor gebruik in een beleids regel, maar verschillen van het gebruik in een Azure Resource Manager sjabloon:

- addDays (dateTime, numberOfDaysToAdd)
  - **DateTime**: [required] string-string in de indeling Universal ISO 8601 datetime ' jjjj-mm-ddTuu: mm: SS. fffffffZ '
  - **numberOfDaysToAdd**: [vereist] geheel getal-aantal dagen dat moet worden toegevoegd
- utcNow ()-in tegens telling tot een resource manager-sjabloon, kan dit buiten de standaard waarde worden gebruikt.
  - Retourneert een teken reeks die is ingesteld op de huidige datum en tijd 8601 in de indeling JJJJ-MM-DDTuu: mm: SS. fffffffZ

Bovendien de `field` functie is beschikbaar voor de regels. `field` wordt voornamelijk gebruikt met **AuditIfNotExists** en **DeployIfNotExists** verwijzing velden op de resource die worden geëvalueerd. Een voorbeeld van het gebruik kan worden weergegeven de [DeployIfNotExists voorbeeld](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Voor beeld van beleids functie

In dit voorbeeld van beleid voor regel maakt gebruik van de `resourceGroup` resource-functie om op te halen de **naam** eigenschap in combinatie met de `concat` matrix- en -functie voor het bouwen een `like` voorwaarde dat de naam van de resource te starten met de naam van de resourcegroep.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Aliassen

U de eigenschap aliassen gebruiken voor toegang tot specifieke eigenschappen voor een resourcetype. Aliassen kunnen u beperken welke waarden of voorwaarden zijn toegestaan voor een eigenschap van een resource. Elke alias wordt toegewezen aan paden in verschillende API-versies voor een gegeven resourcetype. Tijdens de evaluatie van het beleid haalt de beleidsengine het eigenschapspad voor die API-versie.

De lijst met aliassen groeien blijft. Als wilt weten welke aliassen worden momenteel ondersteund door Azure Policy, moet u een van de volgende methoden gebruiken:

- Azure Policy-extensie voor Visual Studio code (aanbevolen)

  Gebruik de [extensie Azure Policy voor Visual Studio code](../how-to/extension-for-vscode.md) om aliassen voor resource-eigenschappen weer te geven en te detecteren.

  ![Azure Policy-extensie voor Visual Studio code](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Gebruik de operator `project` om de **alias** van een resource weer te geven.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure-CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST-API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Inzicht krijgen in de alias [*]

Verschillende van de aliassen die beschikbaar zijn, hebben een versie die wordt weer gegeven als een ' normale ' naam en een andere die **\[\*\]** eraan is gekoppeld. Bijvoorbeeld:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

De ' normale ' alias vertegenwoordigt het veld als een enkele waarde. Dit veld is bedoeld voor vergelijkings scenario's die exact overeenkomen wanneer de volledige set waarden exact zo moet worden gedefinieerd, niet meer en niet minder.

Met de **\[\*\]** alias kunt u vergelijken met de waarde van elk element in de matrix en specifieke eigenschappen van elk element. Deze aanpak maakt het mogelijk om element eigenschappen te vergelijken voor ' If geen ', ' als een van ', ' of ' als alle scenario's. Gebruik de expressie [aantal](#count) voor waarde voor complexere scenario's. Als u **ipRules\[\*\]** gebruikt, wordt er een voor beeld van gevalideerd dat elke _actie_ wordt _geweigerd_, maar is er geen zorgen meer over hoeveel regels bestaan of wat de IP- _waarde_ is.
Met deze voorbeeld regel wordt gecontroleerd op eventuele overeenkomsten met **ipRules\[\*\]. waarde** voor **10.0.4.1** en wordt de **effectType** alleen toegepast als er ten minste één overeenkomst wordt gevonden:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```



Zie [de alias [\*] evalueren](../how-to/author-policies-for-arrays.md#evaluating-the--alias)voor meer informatie.

## <a name="initiatives"></a>Initiatieven

Initiatieven kunnen u verschillende beleidsdefinities toewijzingen en het beheer vereenvoudigen omdat u met een groep als één item werkt verwante groeperen. Bijvoorbeeld, kunt u gerelateerde tagging beleidsdefinities in een enkele initiatief groeperen. In plaats van elk beleid afzonderlijk toewijst, moet u het initiatief toepassen.

Het volgende voorbeeld wordt het maken van een initiatief voor het verwerken van twee labels: `costCenter` en `productName`. Het maakt gebruik van twee ingebouwde beleidsregels om toe te passen van de standaardwaarde van de tag.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
