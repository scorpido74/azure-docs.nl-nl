---
title: Inzicht krijgen in de werking van effecten
description: Azure Policy definities hebben verschillende effecten die bepalen hoe de naleving wordt beheerd en gerapporteerd.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: fec2f966260d997b45be50554e0f41d5fd0491aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436365"
---
# <a name="understand-azure-policy-effects"></a>Informatie over Azure Policy-effecten

Elke beleidsdefinitie in Azure Policy is één effect. Dat effect bepaalt wat er gebeurt wanneer de beleidsregel zodat deze overeenkomen met wordt geëvalueerd. De effecten zich anders gedragen als ze voor een nieuwe resource, een bijgewerkte resource of een bestaande resource zijn.

Deze effecten worden momenteel ondersteund in een beleids definitie:

- [Toevoegen](#append)
- [Controleren](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Toestaan](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Uitgeschakeld](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (preview-versie)
- [EnforceRegoPolicy](#enforceregopolicy) (preview-versie)
- [Wijzigen](#modify)

## <a name="order-of-evaluation"></a>Volgorde van de evaluatie

Aanvragen voor het maken of bijwerken van een ​​resource via Azure Resource Manager worden eerst door Azure Policy geëvalueerd. Azure Policy maakt een lijst met alle toewijzingen die van toepassing zijn op de resource en evalueert vervolgens de resource op basis van elke definitie. Azure Policy verschillende effecten verwerkt voordat de aanvraag aan de juiste resource provider wordt door gegeven. Dit voor komt het voor komen van onnodige verwerking door een resource provider wanneer een resource niet voldoet aan de ontworpen governance-besturings elementen van Azure Policy.

- **Uitgeschakelde** wordt eerst gecontroleerd om te bepalen als de beleidsregel moet worden geëvalueerd.
- **Toevoegen** en **wijzigen** worden vervolgens geëvalueerd. Omdat de aanvraag kan worden gewijzigd, is het mogelijk dat er een wijziging is aangebracht waardoor een controle of weigering van een trigger kan worden verhinderd.
- **Weigeren** wordt vervolgens geëvalueerd. Weigeren voordat controleren, dubbele logboekregistratie van een resource die ongewenst is voorkomen door te evalueren.
- **Audit** wordt vervolgens geëvalueerd vóór de aanvraag naar de Resource Provider.

Nadat de Resource Provider een succescode, **AuditIfNotExists** en **DeployIfNotExists** evalueren om te bepalen of extra naleving logboekregistratie of actie vereist is.

Er is momenteel geen volg orde voor de evaluatie van de **EnforceOPAConstraint** -of **EnforceRegoPolicy** -effecten.

## <a name="disabled"></a>Uitgeschakeld

Dit effect is handig voor het testen van situaties of voor wanneer het effect heeft parameters in de beleidsdefinitie. Deze flexibiliteit maakt het mogelijk om uit te schakelen van de toewijzing van een enkel in plaats van alle toewijzingen van dat beleid uit te schakelen.

Een alternatief voor het uitgeschakelde effect is **enforcementMode** die is ingesteld op de beleids toewijzing.
Wanneer **enforcementMode** is _uitgeschakeld_, worden er nog steeds resources geëvalueerd. Logboek registratie, zoals activiteiten logboeken, en het beleids effect vindt niet plaats. Zie [beleids toewijzing-afdwingings modus](./assignment-structure.md#enforcement-mode)voor meer informatie.

## <a name="append"></a>Toevoegen

Toevoeg-wordt gebruikt voor het toevoegen van extra velden naar de aangevraagde resource tijdens het maken of bijwerken. Een voor beeld hiervan is het opgeven van toegestane IP-adressen voor een opslag resource.

> [!IMPORTANT]
> Append is bedoeld voor gebruik met niet-label eigenschappen. Bij toevoegen kunnen Tags aan een resource worden toegevoegd tijdens het maken of bijwerken van een aanvraag, maar het is raadzaam om in plaats daarvan het [wijzigings](#modify) effect voor Tags te gebruiken.

### <a name="append-evaluation"></a>Toevoeg-evaluatie

Toevoeg-evalueert voordat de aanvraag wordt verwerkt door een Resourceprovider tijdens het maken of bijwerken van een resource. Toevoeg-velden toevoegt aan de resource als de **als** wordt voldaan aan de voorwaarde van de beleidsregel. Als het effect append een waarde in de oorspronkelijke aanvraag met een andere waarde overschrijven zou, klikt u vervolgens deze fungeert als een weigeractie en weigert de aanvraag. Als u een nieuwe waarde aan een bestaande matrix wilt toevoegen, gebruikt u de **[\*]** -versie van de alias.

Wanneer de beleidsdefinitie van een met behulp van het effect toevoegen wordt uitgevoerd als onderdeel van een evaluatiecyclus van een, aanbrengen niet het wijzigingen in resources die al bestaan. In plaats daarvan het markeert een resource die voldoet aan de **als** voorwaarde als niet-compatibel.

### <a name="append-properties"></a>Eigenschappen toevoegen

Een toevoeg-effect heeft alleen een **details** matrix, maar dit vereist is. Als **details** is een matrix, duurt het één **veld/waarde** paar of veelvouden. Raadpleeg [structuur van beleidsdefinities](definition-structure.md#fields) voor een lijst van toegestane velden.

### <a name="append-examples"></a>Append-voorbeelden

Voor beeld 1: een combi natie van één **veld/waarde** met een niet- **[\*]** - [alias](definition-structure.md#aliases) met een matrix **waarde** om IP-regels in te stellen voor een opslag account. Wanneer de niet- **[\*]** -alias een matrix is, voegt het effect de **waarde** toe als de volledige matrix. Als de matrix al bestaat, treedt er een gebeurtenis deny op van het conflict.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Voor beeld 2: een combi natie van één **veld/waarde** met een **[\*]** - [alias](definition-structure.md#aliases) met een matrix **waarde** om IP-regels in te stellen op een opslag account. Door gebruik te maken van de alias **[\*]** , voegt het effect de **waarde** toe aan een mogelijk vooraf bestaande matrix. Als de matrix nog niet bestaat, wordt deze gemaakt.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="modify"></a>Wijzigen

Modify wordt gebruikt om tags toe te voegen, bij te werken of te verwijderen tijdens het maken of bijwerken van een resource. Een voor beeld hiervan is het bijwerken van tags op resources, zoals costCenter. Voor een wijzigings beleid moet altijd `mode` zijn ingesteld op _geïndexeerd_ , tenzij de doel resource een resource groep is. Bestaande niet-compatibele resources kunnen worden hersteld met een [herstel taak](../how-to/remediate-resources.md). Eén wijzigings regel kan elk wille keurig aantal bewerkingen hebben.

> [!IMPORTANT]
> Modify is momenteel alleen voor gebruik met tags. Als u labels beheert, is het raadzaam om wijzigen te gebruiken in plaats van toevoegen als wijzigen biedt extra bewerkings typen en de mogelijkheid om bestaande resources te herstellen. Toevoegen wordt echter aanbevolen als u geen beheerde identiteit kunt maken.

### <a name="modify-evaluation"></a>Evaluatie wijzigen

Met modify worden geëvalueerd voordat de aanvraag wordt verwerkt door een resource provider tijdens het maken of bijwerken van een resource. Met modify worden tags voor een resource toegevoegd of bijgewerkt wanneer wordt voldaan aan de **indienings** voorwaarde van de beleids regel.

Wanneer een beleids definitie die gebruikmaakt van het Modify-effect, wordt uitgevoerd als onderdeel van een evaluatie cyclus, worden er geen wijzigingen aangebracht in resources die al bestaan. In plaats daarvan het markeert een resource die voldoet aan de **als** voorwaarde als niet-compatibel.

### <a name="modify-properties"></a>Eigenschappen wijzigen

De eigenschap **Details** van het effect Modify heeft alle subeigenschappen die de machtigingen definiëren die nodig zijn voor herstel en de **bewerkingen** die worden gebruikt om label waarden toe te voegen, bij te werken of te verwijderen.

- **roleDefinitionIds** (vereist)
  - Deze eigenschap moet een matrix met tekenreeksen die overeenkomen met toegankelijk is op basis van de rol beheer rol-ID van het abonnement zijn. Zie voor meer informatie, [herstel - beleidsdefinitie configureren](../how-to/remediate-resources.md#configure-policy-definition).
  - De gedefinieerde rol moet alle bewerkingen bevatten die zijn toegewezen aan de rol [Inzender](../../../role-based-access-control/built-in-roles.md#contributor) .
- **bewerkingen** [vereist]
  - Een matrix met alle label bewerkingen die moeten worden voltooid voor overeenkomende resources.
  - Eigenschappen:
    - **bewerking** [vereist]
      - Definieert welke actie moet worden uitgevoerd op een overeenkomende resource. Opties zijn: _addOrReplace_, _toevoegen_, _verwijderen_. Een gezichte lijkt op [](#append) het _toevoegen_ van het effect.
    - **veld** [vereist]
      - Het label dat moet worden toegevoegd, vervangen of verwijderd. Label namen moeten voldoen aan dezelfde naam Conventie voor andere [velden](./definition-structure.md#fields).
    - **waarde** (optioneel)
      - De waarde waarop de tag moet worden ingesteld.
      - Deze eigenschap is vereist als de bewerking _addOrReplace_ of _add_is.

### <a name="modify-operations"></a>Bewerkingen wijzigen

De eigenschap array **Operations** maakt het mogelijk verschillende labels op verschillende manieren te wijzigen vanuit één beleids definitie. Elke bewerking bestaat uit **bewerking**-, **veld**-en **waarde** -eigenschappen. Bewerking bepaalt wat de herstel taak doet op de tags, het veld bepaalt welke tag wordt gewijzigd en waarde definieert de nieuwe instelling voor die tag. In het onderstaande voor beeld wordt de volgende tag gewijzigd:

- Hiermee stelt u de `environment`-tag in op ' test ', zelfs als deze al bestaat met een andere waarde.
- Hiermee verwijdert u het label `TempResource`.
- Hiermee stelt u de `Dept`-tag in op de _beleids parameter voor_ de beleids toewijzing geconfigureerd.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

De eigenschap **Operation** heeft de volgende opties:

|Bewerking |Beschrijving |
|-|-|
|addOrReplace |Voegt de gedefinieerde tag en waarde toe aan de resource, zelfs als de tag al bestaat met een andere waarde. |
|Toevoegen |Voegt de gedefinieerde tag en waarde toe aan de resource. |
|Verwijderen |Hiermee verwijdert u de gedefinieerde tag uit de resource. |

### <a name="modify-examples"></a>Voor beelden wijzigen

Voor beeld 1: Voeg het `environment` tag toe en vervang bestaande `environment` Tags door ' test ':

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Voor beeld 2: de `env` tag verwijderen en het `environment` label toevoegen of bestaande `environment` Tags vervangen door een waarde met para meters:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>Weigeren

Weigeren wordt gebruikt om te voorkomen dat een resource-aanvraag komt niet overeen met de gedefinieerde standaarden via de beleidsdefinitie van een en de aanvraag is mislukt.

### <a name="deny-evaluation"></a>Evaluatie weigeren

Wanneer het maken of bijwerken van een overeenkomende resource weigeren, wordt voorkomen dat de aanvraag voordat het wordt verzonden naar de Resource Provider. De aanvraag wordt geretourneerd als een `403 (Forbidden)`. In de portal, kan de verboden worden weergegeven als de status van de implementatie die is verhinderd doordat de beleidstoewijzing.

Tijdens de evaluatie van bestaande resources, resources die overeenkomen met een beleidsdefinitie weigeren gemarkeerd als niet-compatibel.

### <a name="deny-properties"></a>Eigenschappen weigeren

Het effect weigeren geen eventuele aanvullende eigenschappen voor gebruik in de **vervolgens** voorwaarde van de beleidsdefinitie.

### <a name="deny-example"></a>Voorbeeld weigeren

Voorbeeld: Met behulp van het effect weigeren.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Controleren

Controle wordt gebruikt om u te maken van een waarschuwingsgebeurtenis in het activiteitenlogboek bij het evalueren van een niet-compatibele resource, maar deze de aanvraag niet stoppen.

### <a name="audit-evaluation"></a>Audit-evaluatie

Controle is het laatste effect dat door Azure Policy is gecontroleerd tijdens het maken of bijwerken van een resource. Azure Policy verzendt vervolgens de resource naar de resource provider. Controle werkt hetzelfde voor een resource-aanvraag en een evaluatiecyclus van een. Azure Policy voegt een `Microsoft.Authorization/policies/audit/action` bewerking aan het activiteiten logboek toe en markeert de resource als niet-compatibel.

### <a name="audit-properties"></a>Audit-eigenschappen

Het effect van de audit heeft geen eventuele aanvullende eigenschappen voor gebruik in de **vervolgens** voorwaarde van de beleidsdefinitie.

### <a name="audit-example"></a>Audit-voorbeeld

Voorbeeld: Met behulp van het effect van de audit.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists kunt controleren voor bronnen die voldoen aan de **als** voorwaarde, maar beschikt niet over de onderdelen die zijn opgegeven in de **details** van de **vervolgens** voorwaarde.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists-evaluatie

AuditIfNotExists wordt uitgevoerd nadat een Resourceprovider is afgehandeld door een resourceaanvraag maken of bijwerken en een code van de status geslaagd heeft geretourneerd. De controle vindt plaats als er geen verwante bronnen zijn of als de resources die zijn gedefinieerd door **ExistenceCondition** niet resulteren in waar. Azure Policy voegt een `Microsoft.Authorization/policies/audit/action` bewerking aan het activiteiten logboek op dezelfde manier toe als het controle-effect. Wanneer ze worden geactiveerd, de resource die voldaan aan de **als** situatie doet zich voor de resource die is gemarkeerd als niet-compatibel.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists eigenschappen

De **details** eigenschap van de gevolgen AuditIfNotExists heeft alle subeigenschappen die de gerelateerde resources zodat deze overeenkomen met definiëren.

- **Type** (vereist)
  - Hiermee geeft u het type van de bijbehorende resource aan.
  - Als **Details. type** een resource type onder de **if** -voor waarde-resource is, wordt in het beleid query's voor bronnen van dit **type** binnen het bereik van de geëvalueerde resource beschreven. Anders worden er beleids query's uitgevoerd binnen dezelfde resource groep als de geëvalueerde resource.
- **Naam** (optioneel)
  - Hiermee geeft u de exacte naam van de resource waarop en zorgt ervoor dat het beleid voor het ophalen van een bepaalde resource in plaats van alle resources van het opgegeven type.
  - Wanneer de voorwaarde waarden voor **if. Field. type** en **then. Details. type** overeenkomen, wordt de **naam** _vereist_ en moet `[field('name')]`zijn. In plaats daarvan moet echter een [controle](#audit) -effect worden overwogen.
- **ResourceGroupName** (optioneel)
  - Kan de overeenkomst van de bijbehorende resource afkomstig zijn van een andere resourcegroep.
  - Niet van toepassing als **type** is een resource die onder de **als** resource-voorwaarde.
  - Standaard is de **als** resourcegroep van de resource-voorwaarde.
- **ExistenceScope** (optioneel)
  - Toegestane waarden zijn _abonnement_ en _ResourceGroup_.
  - Hiermee stelt u het bereik van de locatie voor het ophalen van de bijbehorende resource waarop uit.
  - Niet van toepassing als **type** is een resource die onder de **als** resource-voorwaarde.
  - Voor _ResourceGroup_, wilt beperken tot de **als** voorwaarde van de resource, resourcegroep of de resourcegroep die is opgegeven in **ResourceGroupName**.
  - Voor _abonnement_, vraagt het hele abonnement voor de bijbehorende resource.
  - De standaardwaarde is _ResourceGroup_.
- **ExistenceCondition** (optioneel)
  - Indien niet opgegeven, alle verwante resources van **type** voldoet aan de kracht en de controle niet activeren.
  - Maakt gebruik van dezelfde taal als de beleidsregel voor de **als** voorwaarde echter geëvalueerd op basis van elke resource gerelateerde afzonderlijk.
  - Als een overeenkomende gerelateerde resource in waar resulteert, wordt het effect is voldaan aan en de controle niet activeren.
  - [Field()] kunt gebruiken om te controleren of gelijkwaardig met waarden in de **als** voorwaarde.
  - Bijvoorbeeld, kan worden gebruikt om te controleren of de bovenliggende resource (in de **als** voorwaarde) is op dezelfde Resourcelocatie als de overeenkomende resource gerelateerde.

### <a name="auditifnotexists-example"></a>Voorbeeld van de AuditIfNotExists

Voorbeeld: Evalueert virtuele Machines om te bepalen of de anti-malware-extensie bestaat voert een controle uit als deze ontbreekt.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Net als bij AuditIfNotExists voert een DeployIfNotExists-beleids definitie een sjabloon implementatie uit wanneer aan de voor waarde wordt voldaan.

> [!NOTE]
> [Geneste sjablonen](../../../azure-resource-manager/templates/linked-templates.md#nested-template) worden ondersteund met **deployIfNotExists**, maar [gekoppelde sjablonen](../../../azure-resource-manager/templates/linked-templates.md#linked-template) worden momenteel niet ondersteund.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists-evaluatie

DeployIfNotExists uitgevoerd nadat een Resourceprovider is afgehandeld door een resourceaanvraag maken of bijwerken en een code van de status geslaagd heeft geretourneerd. De sjabloonimplementatie van een treedt op als er geen verwante bronnen zijn of als de resources die zijn gedefinieerd door **ExistenceCondition** niet resulteren in waar.

Tijdens een evaluatiecyclus beleidsdefinities met een DeployIfNotExists-effect die overeenkomen met de resources zijn gemarkeerd als niet-compatibel, maar er is geen actie ondernomen voor die bron.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists-eigenschappen

De eigenschap **Details** van het effect DeployIfNotExists heeft alle subeigenschappen waarmee de gerelateerde resources worden gedefinieerd en de sjabloon implementatie moet worden uitgevoerd.

- **Type** (vereist)
  - Hiermee geeft u het type van de bijbehorende resource aan.
  - Begint met het ophalen van een resource onder de **als** voorwaarde resource en vervolgens query's in dezelfde resourcegroep bevinden als de **als** resource-voorwaarde.
- **Naam** (optioneel)
  - Hiermee geeft u de exacte naam van de resource waarop en zorgt ervoor dat het beleid voor het ophalen van een bepaalde resource in plaats van alle resources van het opgegeven type.
  - Wanneer de voorwaarde waarden voor **if. Field. type** en **then. Details. type** overeenkomen, wordt de **naam** _vereist_ en moet `[field('name')]`zijn.
- **ResourceGroupName** (optioneel)
  - Kan de overeenkomst van de bijbehorende resource afkomstig zijn van een andere resourcegroep.
  - Niet van toepassing als **type** is een resource die onder de **als** resource-voorwaarde.
  - Standaard is de **als** resourcegroep van de resource-voorwaarde.
  - Als de sjabloonimplementatie van een wordt uitgevoerd, wordt deze geïmplementeerd in de resourcegroep van deze waarde.
- **ExistenceScope** (optioneel)
  - Toegestane waarden zijn _abonnement_ en _ResourceGroup_.
  - Hiermee stelt u het bereik van de locatie voor het ophalen van de bijbehorende resource waarop uit.
  - Niet van toepassing als **type** is een resource die onder de **als** resource-voorwaarde.
  - Voor _ResourceGroup_, wilt beperken tot de **als** voorwaarde van de resource, resourcegroep of de resourcegroep die is opgegeven in **ResourceGroupName**.
  - Voor _abonnement_, vraagt het hele abonnement voor de bijbehorende resource.
  - De standaardwaarde is _ResourceGroup_.
- **ExistenceCondition** (optioneel)
  - Indien niet opgegeven, alle verwante resources van **type** voldoet aan de kracht en de implementatie niet worden geactiveerd.
  - Maakt gebruik van dezelfde taal als de beleidsregel voor de **als** voorwaarde echter geëvalueerd op basis van elke resource gerelateerde afzonderlijk.
  - Als een overeenkomende gerelateerde resource in waar resulteert, wordt het effect is voldaan aan en de implementatie niet worden geactiveerd.
  - [Field()] kunt gebruiken om te controleren of gelijkwaardig met waarden in de **als** voorwaarde.
  - Bijvoorbeeld, kan worden gebruikt om te controleren of de bovenliggende resource (in de **als** voorwaarde) is op dezelfde Resourcelocatie als de overeenkomende resource gerelateerde.
- **roleDefinitionIds** (vereist)
  - Deze eigenschap moet een matrix met tekenreeksen die overeenkomen met toegankelijk is op basis van de rol beheer rol-ID van het abonnement zijn. Zie voor meer informatie, [herstel - beleidsdefinitie configureren](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (optioneel)
  - Toegestane waarden zijn _abonnement_ en _ResourceGroup_.
  - Hiermee stelt u het type implementatie in dat moet worden geactiveerd. Met het _abonnement_ wordt een [implementatie op abonnements niveau](../../../azure-resource-manager/deploy-to-subscription.md)aangegeven. _ResourceGroup_ wijst op een implementatie naar een resource groep.
  - Een _locatie_ -eigenschap moet worden opgegeven in de _implementatie_ bij het gebruik van implementaties op abonnements niveau.
  - De standaardwaarde is _ResourceGroup_.
- **Implementatie** (vereist)
  - Deze eigenschap moet de volledige sjabloonimplementatie bevatten, zoals deze zou worden doorgegeven aan de `Microsoft.Resources/deployments` API plaatsen. Zie voor meer informatie de [implementaties REST-API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alle functies in de **implementatie** eigenschap worden geëvalueerd als onderdelen van de sjabloon, niet van het beleid. De uitzondering hierop is de **parameters** eigenschap waarmee waarden worden doorgegeven van het beleid aan de sjabloon. De **waarde** in deze sectie onder een sjabloon de parameternaam van de wordt gebruikt om uit te voeren van deze waarde wordt doorgegeven (Zie _fullDbName_ in het voorbeeld DeployIfNotExists).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-voorbeeld

Voorbeeld: Evalueert SQL Server-databases om te bepalen of transparentDataEncryption is ingeschakeld. Als dat niet het geval is, wordt er een implementatie uitgevoerd om in te scha kelen.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Dit effect wordt gebruikt in combi natie met de beleids definitie *modus* van `Microsoft.Kubernetes.Data`. Het wordt gebruikt voor het door geven van gate keeper-toegangs beheer regels die zijn gedefinieerd met [opa CONSTRAINT Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) om de [beleids agent](https://www.openpolicyagent.org/) (opa) te openen voor zelf-beheerde Kubernetes-clusters in Azure.

> [!NOTE]
> [Azure Policy voor de AKS-engine](aks-engine.md) bevindt zich in de open bare preview en ondersteunt alleen ingebouwde beleids definities.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint-evaluatie

De open Policy Agent Admission controller evalueert elke nieuwe aanvraag op het cluster in realtime.
Elke 5 minuten wordt een volledige scan van het cluster voltooid en worden de resultaten gerapporteerd aan Azure Policy.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint-eigenschappen

De eigenschap **Details** van het effect EnforceOPAConstraint heeft de subeigenschappen waarmee de gate keeper v3 Admission Control-regel wordt beschreven.

- **constraintTemplate** [vereist]
  - De beperkings sjabloon CustomResourceDefinition (CRD) waarmee nieuwe beperkingen worden gedefinieerd. De sjabloon definieert de Rego Logic, het beperkings schema en de beperkings parameters die worden door gegeven via **waarden** van Azure Policy.
- **beperking** [vereist]
  - De CRD-implementatie van de beperkings sjabloon. Maakt gebruik van para meters die via **waarden** worden door gegeven als `{{ .Values.<valuename> }}`. In het onderstaande voor beeld is dit `{{ .Values.cpuLimit }}` en `{{ .Values.memoryLimit }}`.
- **waarden** [Optioneel]
  - Hiermee definieert u de para meters en waarden die moeten worden door gegeven aan de beperking. Elke waarde moet bestaan in de beperkings sjabloon CRD.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy-voor beeld

Voor beeld: gate keeper v3 Admission Control regel voor het instellen van limieten voor container CPU en geheugen bronnen in de AKS-engine.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Dit effect wordt gebruikt in combi natie met de beleids definitie *modus* van `Microsoft.ContainerService.Data`. Het wordt gebruikt voor het door geven van de regels voor toegangs beheer van gate keeper v2 die zijn gedefinieerd met [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) voor het [openen van beleids agent](https://www.openpolicyagent.org/) (opa) op de [Azure Kubernetes-service](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy voor AKS](rego-for-aks.md) is een beperkte preview-versie en ondersteunt alleen ingebouwde beleids definities

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy-evaluatie

De open Policy Agent Admission controller evalueert elke nieuwe aanvraag op het cluster in realtime.
Elke 5 minuten wordt een volledige scan van het cluster voltooid en worden de resultaten gerapporteerd aan Azure Policy.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy-eigenschappen

De eigenschap **Details** van het effect EnforceRegoPolicy heeft de subeigenschappen waarmee de gate keeper v2 Admission Control-regel wordt beschreven.

- **policyId** [vereist]
  - Een unieke naam die als een para meter wordt door gegeven aan de Rego Admission Control-regel.
- **beleid** [vereist]
  - Hiermee geeft u de URI op van de Rego Admission Control-regel.
- **policyParameters** [Optioneel]
  - Hiermee worden alle para meters en waarden gedefinieerd die moeten worden door gegeven aan het Rego-beleid.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy-voor beeld

Voor beeld: gate keeper v2 Admission Control regel om alleen de opgegeven container installatie kopieën in AKS toe te staan.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>Beleid voor lagen

Een resource wordt mogelijk beïnvloed door meerdere toewijzingen. Deze toewijzingen mogelijk hetzelfde bereik of op verschillende niveaus. Elk van deze toewijzingen waarschijnlijk ook een andere effect gedefinieerd hebben. De voorwaarde en de gevolgen voor elk beleid wordt afzonderlijk geëvalueerd. Bijvoorbeeld:

- Beleid 1
  - Hiermee beperkt u Resourcelocatie naar 'westus'
  - Toegewezen aan een abonnement
  - Effect weigeren
- Beleid 2
  - Hiermee beperkt u Resourcelocatie 'eastus'
  - Toegewezen aan de resourcegroep B in een abonnement
  - Audit-effect
  
Deze installatie zou leiden tot het volgende resultaat:

- Alle bronnen in resourcegroep B in 'eastus' al is compatibel met beleid 2 en niet-compatibele beleid 1
- Elke resource al in de resourcegroep B niet in 'eastus' is niet-compatibele beleid 2 en niet-compatibele beleid 1 als dat niet in 'westus'
- Een nieuwe resource in abonnement A niet in 'westus' wordt geweigerd door beleid 1
- Een nieuwe resource in abonnement A en B-resourcegroep in 'westus' wordt gemaakt en niet-compatibel is op beleid 2

Als heeft gevolgen voor zowel beleid 1 en 2 van weigeren, wordt de situatie gewijzigd in:

- Elke resource al in de resourcegroep B niet in 'eastus' is niet-compatibele beleid 2
- Elke resource al in de resourcegroep B niet in 'westus' is niet-compatibele beleid 1
- Een nieuwe resource in abonnement A niet in 'westus' wordt geweigerd door beleid 1
- Een nieuwe resource in de resourcegroep B voor abonnement die a is geweigerd

Elke toewijzing afzonderlijk geëvalueerd. Daarom is er geen een kans op een resource voor vertraging via een onderbreking van de verschillen in het bereik. Het resultaat van het beleid voor lagen of het beleid overlapping wordt beschouwd als **cumulatieve meest beperkende**. Een voorbeeld: als beide beleid 1 en 2 had een weigeractie een bron zou worden geblokkeerd door de overlappende en conflicterende beleidsregels. Als u nog steeds de resource moet worden gemaakt in de doel-scope, Controleer de uitsluitingen voor elke toewijzing voor het valideren van de juiste beleidsregels van invloed zijn op de juiste bereiken.

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](definition-structure.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
