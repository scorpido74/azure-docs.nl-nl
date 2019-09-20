---
title: Details van de structuur van de beleids definitie
description: Hierin wordt beschreven hoe resourcedefinitie beleid wordt gebruikt door Azure Policy tot stand brengen van conventies voor resources in uw organisatie door te beschrijven wanneer het beleid wordt afgedwongen en welk effect te nemen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: d7e264bda62753693cdd2333625313cf213f142a
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71145568"
---
# <a name="azure-policy-definition-structure"></a>Structuur van Azure-beleidsdefinities

Resource-beleidsdefinities worden gebruikt door Azure Policy tot stand brengen van conventies voor resources. Elke definitie beschrijft resourcenaleving en wat moet worden uitgevoerd wanneer een resource niet compatibel is zijn doorgevoerd.
Door te definiëren verdragen, kunt u kosten beheren en meer resources eenvoudig beheren. U kunt bijvoorbeeld opgeven dat alleen bepaalde typen virtuele machines zijn toegestaan. Of u kunt vereisen dat alle resources een bepaald label hebben. Beleidsregels worden overgenomen door alle onderliggende resources. Als een beleid wordt toegepast op een resourcegroep, is het van toepassing op alle resources in die resourcegroep.

Het schema dat wordt gebruikt door Azure Policy kunt dit hier vinden: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

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

`indexed` moet worden gebruikt bij het maken van beleid dat labels of locaties worden afgedwongen. Hoewel dit niet vereist is, voor komt u dat resources die tags en locaties ondersteunen, niet kunnen worden weer gegeven als niet-compatibel in de nalevings resultaten. De uitzondering hierop is **resourcegroepen**. Voor die locatie of de tags voor een resourcegroep afdwingen moeten ingesteld **modus** naar `all` en een specifiek doel de `Microsoft.Resources/subscriptions/resourceGroups` type. Zie voor een voorbeeld [afdwingen groep resourcetags](../samples/enforce-tag-rg.md). Zie [tag-ondersteuning voor Azure-resources](../../../azure-resource-manager/tag-support.md)voor een lijst met resources die Tags ondersteunen.

### <a name="resource-provider-modes"></a>Resource provider modi

De enige resource provider modus die momenteel wordt `Microsoft.ContainerService.Data` ondersteund, is voor het beheren van regels voor toegangs beheer in de [Azure Kubernetes-service](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy voor Kubernetes](rego-for-aks.md) is in open bare preview en ondersteunt alleen ingebouwde beleids definities.

## <a name="parameters"></a>Parameters

Parameters helpen uw beleidsbeheer te vereenvoudigen door het aantal beleidsdefinities te verminderen. Met parameters, zoals de velden in een formulier – `name`, `address`, `city`, `state`. Deze parameters altijd hetzelfde blijven, maar hun waarden worden gewijzigd op basis van het afzonderlijke invullen van het formulier.
Parameters werken op dezelfde manier als het samenstellen van beleid. Door parameters te nemen in de beleidsdefinitie van een, kunt u beleid voor verschillende scenario's met behulp van verschillende waarden gebruiken.

> [!NOTE]
> Para meters kunnen worden toegevoegd aan een bestaande en toegewezen definitie. De nieuwe para meter moet de eigenschap **DefaultValue** bevatten. Dit voorkomt dat bestaande toewijzingen van het beleid of initiatief indirect wordt ongeldig gemaakt.

### <a name="parameter-properties"></a>Parameter eigenschappen

Een para meter heeft de volgende eigenschappen die worden gebruikt in de beleids definitie:

- **name**: De naam van de para meter. Wordt gebruikt door `parameters` de implementatie functie binnen de beleids regel. Zie [een parameter waarde gebruiken](#using-a-parameter-value)voor meer informatie.
- `type`: Bepaalt of de para meter een **teken reeks**, een **matrix**, een **object**, een **Booleaanse waarde**, een **geheel getal**, een **float**of een **datum/tijd**is.
- `metadata`: Hiermee worden subeigenschappen gedefinieerd die voornamelijk worden gebruikt door de Azure Portal om gebruikers vriendelijke informatie weer te geven:
  - `description`: De uitleg van het gebruik van de para meter voor. Kan worden gebruikt om voor beelden te bieden van acceptabele waarden.
  - `displayName`: De beschrijvende naam die wordt weer gegeven in de portal voor de para meter.
  - `strongType`: Beschrijving Wordt gebruikt bij het toewijzen van de beleids definitie via de portal. Biedt een context bewuste lijst. Zie [strongType](#strongtype)voor meer informatie.
  - `assignPermissions`: Beschrijving Stel deze _waarde_ in op waar als u wilt dat Azure Portal roltoewijzingen tijdens beleids toewijzing maakt. Deze eigenschap is handig voor het geval u machtigingen wilt toewijzen buiten het toewijzings bereik. Er is één roltoewijzing per roldefinitie in het beleid (of per functie definitie in alle beleids regels in het initiatief). De parameter waarde moet een geldige resource of een geldig bereik zijn.
- `defaultValue`: Beschrijving Hiermee wordt de waarde van de para meter in een toewijzing ingesteld als er geen waarde is opgegeven.
  Vereist bij het bijwerken van een bestaande beleids definitie die is toegewezen.
- `allowedValues`: Beschrijving Biedt een matrix met waarden die door de para meter worden geaccepteerd tijdens de toewijzing.

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

In de beleidsregel u verwijzen naar parameters met de volgende `parameters` implementatie waarde functiesyntaxis:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Dit voor beeld verwijst naar de **allowedLocations** -para meter die is gedemonstreerd in [parameter eigenschappen](#parameter-properties).

### <a name="strongtype"></a>strongType

Binnen de `metadata` eigenschap kunt u **strongType** gebruiken om een multi-select lijst met opties in de Azure Portal op te geven. Toegestane waarden voor **strongType** momenteel opnemen:

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

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

Wanneer u de **zoals** en **notlike zijn** voorwaarden bieden u een jokerteken `*` in de waarde.
De waarde mag niet meer dan één jokerteken hebben `*`.

Wanneer u de `#` voor waarden **match** en **notMatch** gebruikt, moet u een `.` cijfer `?` , voor een letter, overeenkomen met een wille keurig teken en elk ander teken dat overeenkomt met het werkelijke teken.
**match** en **notMatch** zijn hoofdletter gevoelig. Hoofdletter gevoelige alternatieven zijn beschikbaar in **matchInsensitively** en **notMatchInsensitively**. Zie voor voorbeelden van [toestaan verschillende bestandsnaampatronen](../samples/allow-multiple-name-patterns.md).

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
  - Voor beelden `tags['Acct.CostCenter']` : waarbij **acct. CostCenter** de naam van de tag is.
- `tags['''<tagName>''']`
  - De syntaxis van het haakje ondersteunt label namen met apostrofs in het teken met dubbele apostrofs.
  - Waarbij **tagNamede\>naam is van het label voor het valideren van de voor waarde voor.\<**
  - Voor beeld `tags['''My.Apostrophe.Tag''']` : **'\<tagName\>'** is de naam van de tag.
- de eigenschap aliassen - Zie voor een lijst [aliassen](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` en`tags[tag.with.dots]` zijn nog steeds acceptabele manieren om een label veld te declareren. De voorkeurs expressies zijn echter die in de bovenstaande lijst.

#### <a name="use-tags-with-parameters"></a>Tags gebruiken met para meters

Een parameter waarde kan worden door gegeven aan een label veld. Door een para meter door te geven aan een label veld, verhoogt u de flexibiliteit van de beleids definitie tijdens beleids toewijzing.

In het volgende voor beeld `concat` wordt gebruikt om een label veld te maken voor de tag met de naam de waarde van de para meter **tagName** . Als deze tag niet bestaat, wordt het **toevoeg** effect gebruikt om de tag toe te voegen met de waarde van dezelfde benoemde tag die is ingesteld voor de bovenliggende resource groep van de `resourcegroup()` gecontroleerde resources met behulp van de functie lookup.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Value

Voor waarden kunnen ook worden gevormd met behulp van een **waarde**. **waarde** controleert de voor waarden op basis van [para meters](#parameters), [ondersteunde sjabloon functies](#policy-functions)of letterlijke tekens.
**waarde** wordt gekoppeld aan elke ondersteunde [voor waarde](#conditions).

> [!WARNING]
> Als het resultaat van een _sjabloon functie_ een fout is, mislukt de beleids evaluatie. Een mislukte evaluatie is een impliciete **weigering**. Zie voor meer informatie [sjabloon fouten voor komen](#avoiding-template-failures).

#### <a name="value-examples"></a>Voor beelden van waarden

In dit voor beeld van een beleids regel wordt de **waarde** gebruikt `resourceGroup()` om het resultaat van de functie en de eigenschap geretourneerde `*netrg` **naam** te vergelijken met een like-voor waarde van. De regel weigert resources die niet van het `Microsoft.Network/*` **type** zijn in een resource groep waarvan de naam `*netrg`eindigt op.

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

Dit voor beeld van een beleids regel gebruikt een **waarde** om te controleren of het resultaat van meerdere geneste functies `true` **gelijk is aan** . De regel weigert een resource die niet ten minste drie tags heeft.

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

De voorbeeld beleidsregel hierboven maakt gebruik van [subtekenreeks ()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) om de eerste drie tekens van de **naam** te vergelijken met **ABC**. Als de **naam** korter is dan drie tekens `substring()` , resulteert de functie in een fout. Als gevolg van deze fout wordt het beleid **geweigerd** .

Gebruik in plaats daarvan de functie [als ()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) om te controleren of de eerste drie tekens van de **naam** gelijk zijn aan **ABC** zonder dat een **naam** die korter is dan drie tekens, een fout veroorzaakt:

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

Met de gereviseerde beleids regel `if()` controleert u de lengte van de **naam** voordat u probeert `substring()` een waarde op te halen die korter is dan drie tekens. Als de **naam** te kort is, wordt de waarde ' niet beginnend met ABC ' geretourneerd in plaats van **ABC**. Een resource met een korte naam die niet met **ABC** begint, mislukt nog steeds de beleids regel, maar veroorzaakt geen fout meer tijdens de evaluatie.

### <a name="effect"></a>Effect

Azure Policy ondersteunt de volgende typen effect:

- **Weigeren**: genereert een gebeurtenis in het activiteitenlogboek en de aanvraag is mislukt
- **Audit**: genereert een waarschuwingsgebeurtenis in het activiteitenlogboek, maar niet de aanvraag mislukt
- **Toevoeg-** : voegt de gedefinieerde set velden toe aan de aanvraag
- **AuditIfNotExists**: kunt controleren als een resource bestaat niet
- **DeployIfNotExists**: implementeert een resource als deze nog niet bestaat
- **Uitgeschakelde**: resources voor naleving van de beleidsregel niet evalueren
- **EnforceRegoPolicy**: Hiermee configureert u de open Policy Agent Admissions-controller in de Azure Kubernetes-service (preview)
- **Wijzigen**: de gedefinieerde labels worden toegevoegd, bijgewerkt of verwijderd uit een resource

Voor **append**, moet u de volgende gegevens opgeven:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

De waarde kan een tekenreeks of een JSON-indeling-object zijn.

**AuditIfNotExists** en **DeployIfNotExists** sprake is van een resource gerelateerde evalueren en een regel toepast. Als de resource komt niet overeen met de regel, wordt het effect is geïmplementeerd. U kunt bijvoorbeeld vereisen dat een network watcher wordt geïmplementeerd voor alle virtuele netwerken. Zie voor meer informatie de [controleren als extensie bestaat](../samples/audit-ext-not-exist.md) voorbeeld.

De **DeployIfNotExists** effect vereist de **roleDefinitionId** eigenschap in de **details** gedeelte van de beleidsregel. Zie voor meer informatie, [herstel - beleidsdefinitie configureren](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**Wijzig** ook de eigenschap **roledefinitionid hebben** vereist in het gedeelte **Details** van de beleids regel voor de [herstel taak](../how-to/remediate-resources.md). Voor het **wijzigen** moet ook een **Operations** -matrix worden gedefinieerd om te bepalen welke acties moeten worden uitgevoerd voor de tags voor de resources.

Zie voor meer informatie over elk effect, de volg orde van evaluatie, eigenschappen en voor beelden [informatie over Azure Policy effecten](effects.md).

### <a name="policy-functions"></a>Beleidsfuncties

Alle [Resource Manager-sjabloon functies](../../../azure-resource-manager/resource-group-template-functions.md) zijn beschikbaar voor gebruik in een beleids regel, met uitzonde ring van de volgende functies en door de gebruiker gedefinieerde functies:

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

Aantal van de aliassen die beschikbaar zijn hebben een versie die wordt weergegeven als een 'normale' naam en een andere waarvoor **[\*]** gekoppeld. Bijvoorbeeld:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

De ' normale ' alias vertegenwoordigt het veld als een enkele waarde. Dit veld is bedoeld voor vergelijkings scenario's die exact overeenkomen wanneer de volledige set waarden exact zo moet worden gedefinieerd, niet meer en niet minder.

De **alias\*[]** maakt het mogelijk om te vergelijken met de waarde van elk element in de matrix en specifieke eigenschappen van elk element. Deze aanpak maakt het mogelijk om element eigenschappen te vergelijken voor ' If geen ', ' als een van ', ' of ' als alle scenario's. Als u **ipRules\*[]** gebruikt, wordt er een voor beeld van gevalideerd dat elke _actie_ wordt _geweigerd_, maar is er geen zorgen meer over hoeveel regels bestaan of wat de IP- _waarde_ is. Deze voorbeeld regel controleert op alle overeenkomsten van **ipRules [\*]. Value** to **10.0.4.1** en past de **effectType** alleen toe als er ten minste één overeenkomst wordt gevonden:

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
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/getting-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).