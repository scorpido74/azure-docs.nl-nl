---
title: Details van de beleidsdefinitiestructuur
description: Beschrijft hoe beleidsdefinities worden gebruikt om conventies voor Azure-resources in uw organisatie vast te stellen.
ms.date: 02/26/2020
ms.topic: conceptual
ms.openlocfilehash: d7cb1ab7d045a0595f6949052ecedba6cd1bf694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239992"
---
# <a name="azure-policy-definition-structure"></a>Structuur van Azure-beleidsdefinities

Azure Policy stelt conventies voor resources vast. Beleidsdefinities beschrijven de [nalevingsvoorwaarden](#conditions) van resources en het effect dat moet worden genomen als aan een voorwaarde is voldaan. Een voorwaarde vergelijkt een [resourcepropertyveld](#fields) met een vereiste waarde. Resourceeigenschapvelden worden geopend met [aliassen](#aliases). Een resourcepropertyveld is een veld met één waarde of een [array](#understanding-the--alias) met meerdere waarden. Conditie evaluatie is anders op arrays.
Meer informatie over [voorwaarden](#conditions).

Door conventies te definiëren, u de kosten beheersen en uw resources eenvoudiger beheren. U bijvoorbeeld opgeven dat alleen bepaalde typen virtuele machines zijn toegestaan. U ook vereisen dat alle resources een bepaalde tag hebben. Beleid wordt overgenomen door alle onderliggende bronnen. Als een beleid wordt toegepast op een resourcegroep, is dit van toepassing op alle resources in die resourcegroep.

Het beleidsdefinitieschema vindt u hier:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

U gebruikt JSON om een beleidsdefinitie te maken. De beleidsdefinitie bevat elementen voor:

- mode
- parameters
- weergavenaam
- description
- beleidsregel
  - logische evaluatie
  - effect

In het volgende JSON wordt bijvoorbeeld een beleid weergegeven dat beperkt waar resources worden geïmplementeerd:

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

Alle azure-beleidsvoorbeelden bevinden zich op [Azure Policy-voorbeelden](../samples/index.md).

## <a name="mode"></a>Modus

**De modus** is geconfigureerd, afhankelijk van of het beleid is gericht op een eigenschap Azure Resource Manager of een eigenschap Resource Provider.

### <a name="resource-manager-modes"></a>Resourcemanager-modi

De **modus** bepaalt welke resourcetypen worden geëvalueerd voor een beleid. De ondersteunde modi zijn:

- `all`: resourcegroepen en alle resourcetypen evalueren
- `indexed`: evalueer alleen resourcetypen die tags en locatie ondersteunen

Resource `Microsoft.Network/routeTables` ondersteunt bijvoorbeeld tags en locatie en wordt in beide modi geëvalueerd. Resource `Microsoft.Network/routeTables/routes` kan echter niet worden getagd en `Indexed` wordt niet geëvalueerd in de modus.

We raden u **mode** aan `all` de modus in te stellen op in de meeste gevallen. Alle beleidsdefinities die via `all` de portal zijn gemaakt, gebruiken de modus. Als u PowerShell of Azure CLI gebruikt, u de **modusparameter** handmatig opgeven. Als de beleidsdefinitie geen **moduswaarde** bevat, wordt `all` deze standaard weergegeven `null` in Azure PowerShell en in Azure CLI. Een `null` modus is hetzelfde als gebruiken `indexed` om achterwaartse compatibiliteit te ondersteunen.

`indexed`moet worden gebruikt bij het maken van beleid dat tags of locaties afdwingt. Hoewel dit niet vereist is, voorkomt het dat resources die geen tags en locaties ondersteunen, worden weergegeven als niet-compatibel in de nalevingsresultaten. De uitzondering is **resourcegroepen**. Beleidsregels die locatie of tags op een `all` resourcegroep `Microsoft.Resources/subscriptions/resourceGroups` afdwingen, moeten **de modus** instellen op en specifiek op het type richten. Zie [Resourcegroeptags afdwingen](../samples/enforce-tag-rg.md)voor een voorbeeld . Zie [Ondersteuning voor Azure-bronnen voor](../../../azure-resource-manager/management/tag-support.md)een lijst met bronnen die tags ondersteunen.

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Resourceprovider-modi (voorbeeld)

De volgende resourceprovidermodi worden momenteel ondersteund tijdens de preview:

- `Microsoft.ContainerService.Data`voor het beheren van regels voor toegangsbeheerop [Azure Kubernetes Service](../../../aks/intro-kubernetes.md). Beleid met deze resourceprovidermodus **moet** het effect [EnforceRegoPolicy](./effects.md#enforceregopolicy) gebruiken.
- `Microsoft.Kubernetes.Data`voor het beheren van zelfbeheerde AKS Engine Kubernetes-clusters op Azure.
  Beleid met deze resourceprovidermodus **moet** het effect [HandhavingOPAConstraint](./effects.md#enforceopaconstraint) gebruiken.
- `Microsoft.KeyVault.Data`voor het beheren van kluizen en certificaten in [Azure Key Vault.](../../../key-vault/key-vault-overview.md)

> [!NOTE]
> Resourceprovider-modi ondersteunen alleen ingebouwde beleidsdefinities en ondersteunen geen initiatieven in een voorbeeld.

## <a name="parameters"></a>Parameters

Parameters helpen uw beleidsbeheer te vereenvoudigen door het aantal beleidsdefinities te verminderen. Denk aan parameters zoals de `name`velden `address` `city`op `state`een formulier – , , . Deze parameters blijven altijd hetzelfde, maar hun waarden veranderen op basis van de individuele invullen van het formulier.
Parameters werken op dezelfde manier bij het bouwen van beleid. Door parameters op te nemen in een beleidsdefinitie, u dat beleid opnieuw gebruiken voor verschillende scenario's met behulp van verschillende waarden.

> [!NOTE]
> Parameters kunnen worden toegevoegd aan een bestaande en toegewezen definitie. De nieuwe parameter moet de **eigenschap defaultValue** bevatten. Dit voorkomt dat bestaande opdrachten van het beleid of initiatief indirect ongeldig worden gemaakt.

### <a name="parameter-properties"></a>Parametereigenschappen

Een parameter heeft de volgende eigenschappen die worden gebruikt in de beleidsdefinitie:

- **naam:** de naam van uw parameter. Wordt gebruikt `parameters` door de implementatiefunctie binnen de beleidsregel. Zie [een parameterwaarde gebruiken voor](#using-a-parameter-value)meer informatie.
- `type`: hiermee bepaalt u of de parameter een **tekenreeks**, **array**, **object**, **booleaan,** **geheel getal**, **zweven**of **datumtijd**is .
- `metadata`: Definieert subeigenschappen die voornamelijk door de Azure-portal worden gebruikt om gebruiksvriendelijke informatie weer te geven:
  - `description`: De uitleg van waar de parameter voor wordt gebruikt. Kan worden gebruikt om voorbeelden van aanvaardbare waarden te geven.
  - `displayName`: De vriendelijke naam in het portaal voor de parameter.
  - `version`: (Optioneel) Houdt details bij over de versie van de inhoud van een beleidsdefinitie.

    > [!NOTE]
    > De Azure Policy-service gebruikt `version`en `preview` `deprecated` eigenschappen om het wijzigingsniveau over te brengen naar een ingebouwde beleidsdefinitie of -initiatief en -status. Het formaat `version` van `{Major}.{Minor}.{Patch}`is: . Specifieke toestanden, zoals _afgeschaft_ of _voorbeeld,_ worden `version` toegevoegd aan de eigenschap of in een andere eigenschap als **een booleaan**.

  - `category`: (Optioneel) Bepaalt onder welke categorie in azure-portal de beleidsdefinitie wordt weergegeven.
  - `strongType`: (Optioneel) Gebruikt bij het toewijzen van de beleidsdefinitie via de portal. Biedt een lijst met contextbewuste gegevens. Zie [strongType voor](#strongtype)meer informatie .
  - `assignPermissions`: (Optioneel) Stel _in_ dat Azure-portal roltoewijzingen maakt tijdens beleidstoewijzing. Deze eigenschap is handig als u machtigingen buiten het toewijzingsbereik wilt toewijzen. Er is één roltoewijzing per roldefinitie in het beleid (of per roldefinitie in alle beleidsvormen in het initiatief). De parameterwaarde moet een geldige resource of bereik zijn.
- `defaultValue`: (Optioneel) Hiermee stelt u de waarde van de parameter in een toewijzing in als er geen waarde wordt opgegeven.
  Vereist bij het bijwerken van een bestaande beleidsdefinitie die is toegewezen.
- `allowedValues`: (Optioneel) Biedt een array met waarden die de parameter accepteert tijdens de toewijzing.

Als voorbeeld u een beleidsdefinitie definiëren om de locaties te beperken waar resources kunnen worden geïmplementeerd. Een parameter voor die beleidsdefinitie kan worden **toegestaanLocaties**. Deze parameter wordt gebruikt door elke toewijzing van de beleidsdefinitie om de geaccepteerde waarden te beperken. Het gebruik van **strongType** biedt een verbeterde ervaring bij het voltooien van de opdracht via de portal:

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

### <a name="using-a-parameter-value"></a>Een parameterwaarde gebruiken

In de beleidsregel verwijst u `parameters` naar parameters met de volgende functiesyntaxis:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

In dit voorbeeld wordt verwezen naar de parameter **allowedLocations** die is aangetoond in [parametereigenschappen.](#parameter-properties)

### <a name="strongtype"></a>strongType (strongType)

Binnen `metadata` de eigenschap u **strongType** gebruiken om een lijst met opties met meerdere opties binnen de Azure-portal te bieden. **strongType** kan een ondersteund _resourcetype_ of een toegestane waarde zijn. Als u wilt bepalen of een _resourcetype_ geldig is voor **strongType,** gebruikt u [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Sommige _resourcetypen_ die niet worden geretourneerd door **Get-AzResourceProvider** worden ondersteund. Dat zijn:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

De _niet-resourcetype_ toegestane waarden voor **strongType** zijn:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Definitielocatie

Tijdens het maken van een initiatief of beleid is het noodzakelijk om de definitielocatie op te geven. De definitielocatie moet een beheergroep of een abonnement zijn. Deze locatie bepaalt de scope waaraan het initiatief of beleid kan worden toegewezen. Resources moeten directe leden van of kinderen zijn binnen de hiërarchie van de definitielocatie die moet worden toegewezen voor toewijzing.

Als de definitielocatie een:

- **Abonnement** - Alleen resources binnen dat abonnement kunnen aan het beleid worden toegewezen.
- **Beheergroep** - Alleen resources binnen onderliggende beheergroepen en onderliggende abonnementen kunnen aan het beleid worden toegewezen. Als u van plan bent de beleidsdefinitie toe te passen op meerdere abonnementen, moet de locatie een beheergroep zijn die deze abonnementen bevat.

## <a name="display-name-and-description"></a>Weergavenaam en beschrijving

U gebruikt **displayName** en **beschrijving** om de beleidsdefinitie te identificeren en context te bieden voor wanneer deze wordt gebruikt. **displayName** heeft een maximale lengte van _128_ tekens en **een beschrijving** met een maximale lengte van _512_ tekens.

> [!NOTE]
> Tijdens het maken of bijwerken van een beleidsdefinitie worden **id,** **type**en **naam** gedefinieerd door eigenschappen buiten het JSON en zijn niet nodig in het JSON-bestand. Als u de beleidsdefinitie via SDK ophaalt, worden de **id-,** **typ-** en **naameigenschappen** geretourneerd als onderdeel van de JSON, maar zijn dit alleen-lezen informatie met betrekking tot de beleidsdefinitie.

## <a name="policy-rule"></a>Beleidsregel

De beleidsregel bestaat uit **Als** en **vervolgens** blokken. In het blok **Als** definieert u een of meer voorwaarden die aangeven wanneer het beleid wordt afgedwongen. U logische operatoren toepassen op deze voorwaarden om het scenario voor een beleid nauwkeurig te definiëren.

In het blok **Vervolgens** definieert u het effect dat optreedt wanneer aan de **als-voorwaarden** is voldaan.

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

Ondersteunde logische operatoren zijn:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

Met **not** de niet-syntaxis wordt het resultaat van de voorwaarde omgekeerd. De **syntaxis allOf** (vergelijkbaar met de logische **en** bewerking) vereist dat alle voorwaarden waar zijn. De **syntaxis van anyOf** (vergelijkbaar met de logische **of** bewerking) vereist dat een of meer voorwaarden waar zijn.

U logische operatoren nesten. In het volgende voorbeeld wordt een **niet-bewerking** weergegeven die is genest binnen een **allOf-bewerking.**

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

Een voorwaarde evalueert of een **veld** of het **waardeaccessoire** aan bepaalde criteria voldoet. De ondersteunde voorwaarden zijn:

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

Wanneer u de voorwaarden **like** en **notLike** gebruikt, geeft u een wildcard `*` in de waarde.
De waarde mag niet meer `*`dan één wildcard hebben.

Wanneer u de **wedstrijd** en `#` **nietMatch-voorwaarden gebruikt,** moet u een cijfer, `?` voor een letter, `.` overeenkomen met een teken en elk ander teken dat overeenkomt met dat werkelijke teken. Hoewel, **match** en **notMatch** case-sensitive zijn, zijn alle andere voorwaarden die een _stringValue_ evalueren case-ongevoelig. Case-ongevoelige alternatieven zijn beschikbaar in **matchInsensitively** en **notMatchInsensitively**.

In ** \[ \* \] ** een aliasarrayveldwaarde wordt elk element in de array afzonderlijk geëvalueerd met logische **en** tussenliggende elementen. Zie [De \[ \* \] alias evalueren](../how-to/author-policies-for-arrays.md#evaluating-the--alias)voor meer informatie.

### <a name="fields"></a>Velden

Voorwaarden worden gevormd door het gebruik van velden. Een veld komt overeen met eigenschappen in de payload resourcerequest en beschrijft de status van de resource.

De volgende velden worden ondersteund:

- `name`
- `fullName`
  - Geeft als resultaat de volledige naam van de resource. De volledige naam van een resource is de resourcenaam die wordt voorbereid door alle bovenliggende resourcenamen (bijvoorbeeld 'myServer/myDatabase').
- `kind`
- `type`
- `location`
  - Gebruik **globaal** voor resources die locatieagnostisch zijn.
- `identity.type`
  - Geeft als resultaat het type [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md) dat is ingeschakeld op de resource.
- `tags`
- `tags['<tagName>']`
  - Deze syntaxis van haakjes ondersteunt tagnamen met interpunctie, zoals een koppelteken, een punt of een spatie.
  - Waar ** \<tagName\> ** de naam is van de tag om de voorwaarde voor te valideren.
  - Voorbeelden: `tags['Acct.CostCenter']` waarbij **Acct.CostCenter** de naam van de tag is.
- `tags['''<tagName>''']`
  - Deze syntaxis van haakjes ondersteunt tagnamen met apostrofs in het door te ontsnappen met dubbele apostrofs.
  - Waar **\<'\>tagName '** de naam is van de tag om de voorwaarde voor te valideren.
  - Voorbeeld: `tags['''My.Apostrophe.Tag''']` waar **'My.Apostrofhe.Tag'** de naam van de tag is.
- eigenschappenaliassen - zie [Aliassen](#aliases)voor een lijst .

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`en `tags[tag.with.dots]` zijn nog steeds acceptabele manieren om een tagsveld aan te bieden. De voorkeursuitdrukkingen zijn echter de hierboven genoemde expressies.

#### <a name="use-tags-with-parameters"></a>Tags met parameters gebruiken

Een parameterwaarde kan worden doorgegeven aan een tagveld. Als u een parameter doorgeeft aan een tagveld, verhoogt u de flexibiliteit van de beleidsdefinitie tijdens beleidstoewijzing.

In het volgende `concat` voorbeeld wordt gebruikt om een tagsveld op te zoeken voor de tag met de naam de waarde van de parameter **tagName.** Als die tag niet bestaat, wordt het **wijzigingseffect** gebruikt om de tag toe te voegen met de `resourcegroup()` waarde van dezelfde benoemde tagset op de bovenliggende resourcegroep van gecontroleerde resources met behulp van de opzoekfunctie.

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

Voorwaarden kunnen ook worden gevormd met behulp van **waarde**. **waarde** controleert voorwaarden op [parameters,](#parameters) [ondersteunde sjabloonfuncties](#policy-functions)of literals.
**waarde** wordt gekoppeld aan een ondersteunde [voorwaarde](#conditions).

> [!WARNING]
> Als het resultaat van een _sjabloonfunctie_ een fout is, mislukt de beleidsevaluatie. Een mislukte evaluatie is een impliciete **ontkenning**. Zie [Sjabloonfouten vermijden voor](#avoiding-template-failures)meer informatie . Gebruik [enforcementMode](./assignment-structure.md#enforcement-mode) van **DoNotEnforce** om impact van een mislukte evaluatie op nieuwe of bijgewerkte resources te voorkomen tijdens het testen en valideren van een nieuwe beleidsdefinitie.

#### <a name="value-examples"></a>Voorbeelden van waarden

In dit beleidsregelvoorbeeld wordt **waarde** `resourceGroup()` gebruikt om het resultaat van de `*netrg`functie en de eigenschap geretourneerde **naam** te vergelijken met een **gelijkvoorwaarde** van . De regel weigert elke `Microsoft.Network/*` resource die niet van het `*netrg` **type** in een resourcegroep is waarvan de naam eindigt in .

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

Met dit beleidsregelvoorbeeld wordt **waarde** gebruikt om te controleren of het resultaat van meerdere geneste functies **gelijk is aan** `true`. De regel ontkent elke bron die niet ten minste drie tags heeft.

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

#### <a name="avoiding-template-failures"></a>Sjabloonfouten vermijden

Het gebruik van _sjabloonfuncties_ in **waarde** zorgt voor veel complexe geneste functies. Als het resultaat van een _sjabloonfunctie_ een fout is, mislukt de beleidsevaluatie. Een mislukte evaluatie is een impliciete **ontkenning**. Een voorbeeld van een **waarde** die in bepaalde scenario's mislukt:

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

De bovenstaande beleidsregel gebruikt [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) om de eerste drie tekens van **naam** te vergelijken met **abc**. Als **de naam** korter is `substring()` dan drie tekens, resulteert de functie in een fout. Deze fout zorgt ervoor dat het beleid een **ontkennend** effect wordt.

Gebruik in plaats daarvan de functie [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) om te controleren of de eerste drie tekens van **naam** gelijk zijn aan **abc** zonder dat een **naam** korter dan drie tekens een fout veroorzaakt:

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

Met de herziene beleidsregel `if()` controleert u de **naamsduur** voordat u een `substring()` waarde met minder dan drie tekens probeert te krijgen. Als **de naam** te kort is, wordt de waarde "niet beginnen met abc" geretourneerd en vergeleken met **abc**. Een resource met een korte naam die niet begint met **abc** voldoet nog steeds niet aan de beleidsregel, maar niet langer een fout veroorzaakt tijdens de evaluatie.

### <a name="count"></a>Count

Voorwaarden die tellen hoeveel leden van een array in het laadvermogen voldoen aan een voorwaardeexpressie kunnen worden gevormd met behulp van **telexpressie.** Veelvoorkomende scenario's zijn het controleren of 'ten minste een van', 'precies een van', 'all of', of 'geen van' de array leden voldoen aan de voorwaarde. **count** evalueert elk [ \[ \* \] aliasarraylid](#understanding-the--alias) voor een voorwaardeexpressie en somt de _werkelijke_ resultaten op, die vervolgens wordt vergeleken met de expressieoperator.

De structuur van de **telexpressie** is:

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

De volgende eigenschappen worden met **telling**gebruikt:

- **count.field** (vereist): bevat het pad naar de array en moet een matrixalias zijn. Als de array ontbreekt, wordt de expressie als _fout_ geëvalueerd zonder rekening te houden met de voorwaardeexpressie.
- **count.where** (optioneel): de voorwaardeexpressie om [ \[ \* \] ](#understanding-the--alias) elk aliasarraylid van **count.field**afzonderlijk te evalueren. Als deze eigenschap niet wordt verstrekt, worden alle arrayleden met het pad van 'veld' geëvalueerd op _true_. Elke [voorwaarde](../concepts/definition-structure.md#conditions) kan worden gebruikt in deze eigenschap.
  [Logische operatoren](#logical-operators) kunnen binnen deze eigenschap worden gebruikt om complexe evaluatievereisten te creëren.
- voorwaarde (vereist): De waarde wordt vergeleken met het aantal items dat aan de **tel voldoet.waar** voorwaardeexpressie. ** \<\> ** Er moet een numerieke [voorwaarde](../concepts/definition-structure.md#conditions) worden gebruikt.

#### <a name="count-examples"></a>Voorbeelden tellen

Voorbeeld 1: Controleren of een array leeg is

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Voorbeeld 2: Controleren of slechts één arraylid voldoet aan de voorwaardeexpressie

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

Voorbeeld 3: Controleer of ten minste één arraylid voldoet aan de voorwaardeexpressie

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

Voorbeeld 4: Controleren of alle objectarrayleden voldoen aan de voorwaardeexpressie

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

Voorbeeld 5: Controleren of alle tekenreeksarrayleden voldoen aan de voorwaardeexpressie

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

Voorbeeld 6: Veld **binnenwaarde** gebruiken om te controleren of alle matrixleden voldoen aan de voorwaardeexpressie **value**

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

Voorbeeld 7: Controleer of ten minste één arraylid overeenkomt met meerdere eigenschappen in de voorwaardeexpressie

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

- **Toevoegen**: voegt de gedefinieerde set velden toe aan de aanvraag
- **Controle:** genereert een waarschuwingsgebeurtenis in het activiteitenlogboek, maar mislukt de aanvraag niet
- **AuditIfNotExists:** genereert een waarschuwingsgebeurtenis in het activiteitenlogboek als een gerelateerde resource niet bestaat
- **Weigeren:** genereert een gebeurtenis in het activiteitenlogboek en mislukt de aanvraag
- **DeployIfNotExists**: implementeert een gerelateerde resource als deze nog niet bestaat
- **Uitgeschakeld:** evalueert resources niet om aan de beleidsregel te voldoen
- **EnforceOPAConstraint** (preview): configureert de Accesss-controller open policy agent met Gatekeeper v3 voor zelfbeheerde Kubernetes-clusters op Azure (preview)
- **EnforceRegoPolicy** (preview): configureert de accesss controller open policy agent met Gatekeeper v2 in Azure Kubernetes Service
- **Wijzigen**: voegt de gedefinieerde tags uit een resource toe, werkt deze bij of verwijdert deze

Zie [Azure Policy Effects](effects.md)voor volledige details over elk effect, volgorde van evaluatie, eigenschappen en voorbeelden.

### <a name="policy-functions"></a>Beleidsfuncties

Alle [sjabloonfuncties resourcebeheer](../../../azure-resource-manager/templates/template-functions.md) zijn beschikbaar voor gebruik binnen een beleidsregel, met uitzondering van de volgende functies en door de gebruiker gedefinieerde functies:

- copyIndex()
- implementatie()
- lijst*
- newGuid()
- pickZones()
- aanbieders()
- referentie()
- resourceId()
- variabelen()

De volgende functie is beschikbaar voor gebruik in een beleidsregel, maar verschilt van het gebruik in een Azure Resource Manager-sjabloon:

- `utcNow()`- In tegenstelling tot een Resource Manager-sjabloon kan deze buiten standaardwaarde worden gebruikt.
  - Retourneert een tekenreeks die is ingesteld op de huidige datum en tijd in de Universele ISO 8601 DateTime-indeling 'yyyy-MM-ddTHH:mm:ss.fffffffZ'

De volgende functies zijn alleen beschikbaar in beleidsregels:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**: [Vereiste] tekenreeks - Tekenreeks in de Universele ISO 8601 DateTime-indeling 'yyyy-MM-ddTHH:mm:sss.fffffffZ'
  - **numberOfDaysToAdd**: [Vereist] geheel getal - Aantal dagen om toe te voegen
- `field(fieldName)`
  - **fieldName**: [Vereiste] tekenreeks - Naam van het [veld](#fields) op te halen
  - Geeft als resultaat de waarde van dat veld van de resource die wordt geëvalueerd door de voorwaarde Als
  - `field`wordt voornamelijk gebruikt met **AuditIfNotExists** en **DeployIfNotExists** voor referentievelden op de resource die wordt geëvalueerd. Een voorbeeld van dit gebruik is te zien in het [voorbeeld DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Geeft als resultaat de API-versie van `2019-09-01`de aanvraag die beleidsevaluatie heeft geactiveerd (voorbeeld: ). Dit is de API-versie die is gebruikt in de PUT/PATCH-aanvraag voor evaluaties over het maken/bijwerken van resources. De nieuwste API-versie wordt altijd gebruikt tijdens de nalevingsevaluatie van bestaande resources.
  


#### <a name="policy-function-example"></a>Voorbeeld van de functie Beleid

In dit voorbeeld `resourceGroup` van de beleidsregel wordt de resourcefunctie gebruikt om de `like` eigenschap **naam** op te halen, gecombineerd met de `concat` array- en objectfunctie om een voorwaarde te maken die de naam van de resource afdwingt om te beginnen met de naam van de resourcegroep.

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

U gebruikt eigenschapsaliassen om toegang te krijgen tot specifieke eigenschappen voor een resourcetype. Met aliassen u beperken welke waarden of voorwaarden zijn toegestaan voor een eigenschap op een resource. Elke alias wordt toegewezen aan paden in verschillende API-versies voor een bepaald resourcetype. Tijdens beleidsevaluatie krijgt de beleidsengine het eigenschappenpad voor die API-versie.

De lijst met aliassen groeit altijd. Als u wilt weten welke aliassen momenteel worden ondersteund door Azure Policy, gebruikt u een van de volgende methoden:

- Azure-beleidsextensie voor Visual Studio Code (aanbevolen)

  Gebruik de [Azure Policy-extensie voor Visual Studio Code](../how-to/extension-for-vscode.md) om aliassen voor resourceeigenschappen weer te geven en te ontdekken.

  ![Azure-beleidsextensie voor Visual Studio-code](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Gebruik `project` de operator om de **alias** van een resource weer te geven.

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

- REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>De alias [*] begrijpen

Verschillende van de aliassen die beschikbaar zijn hebben een versie die ** \[ \* ** wordt weergegeven als een 'normale' naam en een andere die eraan is gekoppeld. Bijvoorbeeld:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

De 'normale' alias vertegenwoordigt het veld als één waarde. Dit veld is voor exacte vergelijkingsscenario's wanneer de hele set waarden precies moet zijn zoals gedefinieerd, niet meer en niet minder.

** \[ De \* ** alias maakt het mogelijk om te vergelijken met de waarde van elk element in de array en specifieke eigenschappen van elk element. Deze aanpak maakt het mogelijk om elementeigenschappen te vergelijken voor 'als geen van', 'als een van', of 'als alle' scenario's. Voor complexere scenario's gebruikt u de expressie van de [telvoorwaarde.](#count) Met behulp van **ipRules\[\***, een voorbeeld zou zijn valideren dat elke _actie_ is _Deny_, maar geen zorgen te maken over hoeveel regels bestaan of wat de _IP-waarde_ is.
Deze voorbeeldregel controleert op eventuele overeenkomsten van **\[\*\]ipRules .value** to **10.0.4.1** en past **effectType** alleen toe als deze niet ten minste één overeenkomst vindt:

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



Zie voor meer informatie [de\*beoordeling van de [ ] alias](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Initiatieven

Met initiatieven u verschillende gerelateerde beleidsdefinities groeperen om toewijzingen en beheer te vereenvoudigen omdat u met een groep als één item werkt. U bijvoorbeeld gerelateerde tagging-beleidsdefinities groeperen in één initiatief. In plaats van elk beleid individueel toe te wijs, past u het initiatief toe.

> [!NOTE]
> Zodra een initiatief is toegewezen, kunnen parameters op initiatiefniveau niet worden gewijzigd. Daarom is het de aanbeveling om een **standaardwaarde** in te stellen bij het definiëren van de parameter.

In het volgende voorbeeld wordt uitgelegd hoe u `costCenter` `productName`een initiatief maakt voor het verwerken van twee tags: en . Het maakt gebruik van twee ingebouwde beleidsregels om de standaardtagwaarde toe te passen.

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
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
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

- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Begrijpen hoe [u programmatisch beleid maken.](../how-to/programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [herstellen van niet-conforme resources.](../how-to/remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
