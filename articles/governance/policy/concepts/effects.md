---
title: Begrijpen hoe effecten werken
description: Azure Policy-definities hebben verschillende effecten die bepalen hoe compliance wordt beheerd en gerapporteerd.
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 631c941173a500a4159a37c7c31107b9a6eab872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239969"
---
# <a name="understand-azure-policy-effects"></a>Azure-beleidseffecten begrijpen

Elke beleidsdefinitie in Azure Policy heeft één effect. Dat effect bepaalt wat er gebeurt wanneer de beleidsregel wordt geëvalueerd om overeen te komen. De effecten gedragen zich anders als ze voor een nieuwe resource, een bijgewerkte resource of een bestaande resource zijn.

Deze effecten worden momenteel ondersteund in een beleidsdefinitie:

- [Toevoegen](#append)
- [Audit](#audit)
- [AuditifNotexists](#auditifnotexists)
- [Weigeren](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Handicap](#disabled)
- [AfdwingenOPAConstraint](#enforceopaconstraint) (voorbeeld)
- [Regobeleid afdwingen](#enforceregopolicy) (voorbeeld)
- [Wijzigen](#modify)

## <a name="order-of-evaluation"></a>Volgorde van evaluatie

Aanvragen voor het maken of bijwerken van een ​​resource via Azure Resource Manager worden eerst door Azure Policy geëvalueerd. Azure Policy maakt een lijst met alle toewijzingen die van toepassing zijn op de resource en evalueert de resource vervolgens voor elke definitie. Azure Policy verwerkt verschillende effecten voordat u de aanvraag overhandigt aan de juiste Resourceprovider. Dit voorkomt onnodige verwerking door een resourceprovider wanneer een resource niet voldoet aan de beheerbesturingselementen van Azure Policy.

- **Uitgeschakeld** wordt eerst gecontroleerd om te bepalen of de beleidsregel moet worden geëvalueerd.
- **Toevoegen** en **wijzigen** worden vervolgens geëvalueerd. Aangezien een van beide het verzoek kan wijzigen, kan een wijziging voorkomen dat een audit of een effect wordt ontkend.
- **Deny** wordt vervolgens geëvalueerd. Door weigeren voor controle te evalueren, wordt dubbele registratie van een ongewenste bron voorkomen.
- **De audit** wordt vervolgens geëvalueerd voordat de aanvraag naar de resourceprovider gaat.

Nadat de Resource Provider een succescode retourneert, evalueren **AuditIfNotExists** en **DeployIfNotExists** om te bepalen of aanvullende nalevingslogboekregistratie of actie vereist is.

Er is momenteel geen volgorde van evaluatie voor de **effecten EnforceOPAConstraint** of **EnforceRegoPolicy.**

## <a name="disabled"></a>Uitgeschakeld

Dit effect is handig voor het testen van situaties of voor wanneer de beleidsdefinitie het effect heeft geparameteriseerd. Deze flexibiliteit maakt het mogelijk om één opdracht uit te schakelen in plaats van alle toewijzingen van dat beleid uit te schakelen.

Een alternatief voor het effect Uitgeschakeld is **enforcementMode** die is ingesteld op de beleidstoewijzing.
Wanneer **enforcementMode** is _uitgeschakeld,_ worden resources nog steeds geëvalueerd. Logboekregistratie, zoals Activiteitslogboeken en het beleidseffect, treedt niet op. Zie [beleidstoewijzing - handhavingsmodus](./assignment-structure.md#enforcement-mode)voor meer informatie .

## <a name="append"></a>Toevoegen

Toevoegen wordt gebruikt om extra velden toe te voegen aan de gevraagde bron tijdens het maken of bijwerken. Een veelvoorkomend voorbeeld is het opgeven van toegestane IP's voor een opslagbron.

> [!IMPORTANT]
> Toevoegen is bedoeld voor gebruik met niet-tageigenschappen. Hoewel Toevoegen tags aan een resource kan toevoegen tijdens een aanvraag voor maken of bijwerken, wordt aanbevolen om in plaats daarvan het effect [Wijzigen](#modify) voor tags te gebruiken.

### <a name="append-evaluation"></a>Evaluatie toevoegen

Als u een evaluatie uitvoert voordat de aanvraag wordt verwerkt door een resourceprovider tijdens het maken of bijwerken van een resource. Als u de resource toevoegt, voegt u velden toe aan de resource wanneer aan de **als-voorwaarde** van de beleidsregel is voldaan. Als het toevoegeffect een waarde in het oorspronkelijke verzoek met een andere waarde overschrijft, fungeert het als een weigeringseffect en wijst het verzoek af. Als u een nieuwe waarde wilt toevoegen aan een bestaande array, gebruikt u de **[\*]** versie van de alias.

Wanneer een beleidsdefinitie met het toeterecht wordt uitgevoerd als onderdeel van een evaluatiecyclus, worden er geen wijzigingen aangebracht in de resources die al bestaan. In plaats daarvan markeert het elke resource die voldoet aan de **als-voorwaarde** als niet-compatibel.

### <a name="append-properties"></a>Eigenschappen toevoegen

Een toetenodigeffect heeft alleen een **detailsarray,** die vereist is. Aangezien **details** een array zijn, kan er één **veld/waardepaar** of veelvouden nodig zijn. Raadpleeg [de definitiestructuur](definition-structure.md#fields) voor de lijst met acceptabele velden.

### <a name="append-examples"></a>Voorbeelden toevoegen

Voorbeeld 1: Enkel **veld/waardepaar** met een**\*niet-[ ]** [alias](definition-structure.md#aliases) met een **matrixwaarde** om IP-regels in te stellen voor een opslagaccount. Wanneer de**niet-[ ]\*** alias een array is, voegt het effect de **waarde** toe als de hele array. Als de array al bestaat, vindt er een weigeringsgebeurtenis plaats van het conflict.

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

Voorbeeld 2: Enkel **veld/waardepaar** met behulp van een **[\*]** [alias](definition-structure.md#aliases) met een **matrixwaarde** om IP-regels in te stellen voor een opslagaccount. Door de **\*alias [ ] te gebruiken,** voegt het effect de **waarde** toe aan een mogelijk reeds bestaande array. Als de array nog niet bestaat, wordt deze gemaakt.

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

Wijzigen wordt gebruikt om tags op een resource toe te voegen, bij te werken of te verwijderen tijdens het maken of bijwerken. Een veelvoorkomend voorbeeld is het bijwerken van tags op resources zoals costCenter. Een beleid wijzigen `mode` moet altijd zijn ingesteld op _Geïndexeerd,_ tenzij de doelbron een resourcegroep is. Bestaande niet-conforme resources kunnen worden gesaneerd met een [hersteltaak.](../how-to/remediate-resources.md) Eén regel wijzigen kan een willekeurig aantal bewerkingen hebben.

> [!IMPORTANT]
> Wijzigen is momenteel alleen voor gebruik met tags. Als u tags beheert, wordt aanbevolen om Wijzigen te gebruiken in plaats van Toevoegen als Wijzigen biedt extra bewerkingstypen en de mogelijkheid om bestaande bronnen te herstellen. Toevoegen wordt echter aanbevolen als u geen beheerde identiteit maken.

### <a name="modify-evaluation"></a>Evaluatie wijzigen

Wijzig evalueert voordat de aanvraag wordt verwerkt door een resourceprovider tijdens het maken of bijwerken van een resource. Tags toevoegen of bijwerken op een resource wijzigen wanneer aan de **als-voorwaarde** van de beleidsregel is voldaan.

Wanneer een beleidsdefinitie met het effect Wijzigen wordt uitgevoerd als onderdeel van een evaluatiecyclus, worden er geen wijzigingen aangebracht in de resources die al bestaan. In plaats daarvan markeert het elke resource die voldoet aan de **als-voorwaarde** als niet-compatibel.

### <a name="modify-properties"></a>Eigenschappen wijzigen

De **eigenschap Details** van het effect Wijzigen heeft alle subeigenschappen die de machtigingen definiëren die nodig zijn voor herstel en de **bewerkingen** die worden gebruikt om tagwaarden toe te voegen, bij te werken of te verwijderen.

- **rolDefinitieIds** [vereist]
  - Deze eigenschap moet een reeks tekenreeksen bevatten die overeenkomen met de functie-id voor toegangsbeheer op basis van rollen die toegankelijk zijn voor het abonnement. Zie [Herstel - Beleidsdefinitie configureren](../how-to/remediate-resources.md#configure-policy-definition)voor meer informatie.
  - De gedefinieerde rol moet alle bewerkingen omvatten die aan de rol [van bijdrager worden](../../../role-based-access-control/built-in-roles.md#contributor) toegekend.
- **handelingen** [vereist]
  - Een array van alle tagbewerkingen die moeten worden voltooid op overeenkomende resources.
  - Eigenschappen:
    - **bewerking** [vereist]
      - Hiermee bepaalt u welke actie u moet ondernemen op een overeenkomende resource. Opties zijn: _addorReplace,_ _Add_, _Remove_. _Toevoegen_ gedraagt zich vergelijkbaar met het [effect Toevoegen.](#append)
    - **veld** [vereist]
      - De tag die u wilt toevoegen, vervangen of verwijderen. Tagnamen moeten voldoen aan dezelfde naamgevingsconventie voor andere [velden](./definition-structure.md#fields).
    - **waarde** (optioneel)
      - De waarde om de tag in te stellen.
      - Deze eigenschap is vereist als **de bewerking** _addOrReplace_ of _Add_is.

### <a name="modify-operations"></a>Bewerkingen wijzigen

De array **operations** property maakt het mogelijk om meerdere tags op verschillende manieren te wijzigen dan één beleidsdefinitie. Elke bewerking bestaat uit **bewerkings-, veld-** en **waardeeigenschappen.** **operation** De bewerking bepaalt wat de hersteltaak met de tags doet, het veld bepaalt welke tag wordt gewijzigd en de waarde definieert de nieuwe instelling voor die tag. In het onderstaande voorbeeld worden de volgende tagwijzigingen aangebracht:

- Hiermee `environment` stelt u de tag in op 'Testen', zelfs als deze al bestaat met een andere waarde.
- Hiermee verwijdert `TempResource`u de tag .
- Hiermee `Dept` stelt u de tag in op de beleidsparameter _DeptName_ die is geconfigureerd voor de beleidstoewijzing.

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

De **eigenschap operation** heeft de volgende opties:

|Bewerking |Beschrijving |
|-|-|
|addOrVervangen |Hiermee voegt u de gedefinieerde tag en waarde toe aan de resource, zelfs als de tag al bestaat met een andere waarde. |
|Toevoegen |Hiermee voegt u de gedefinieerde tag en waarde toe aan de resource. |
|Verwijderen |Hiermee verwijdert u de gedefinieerde tag uit de bron. |

### <a name="modify-examples"></a>Voorbeelden wijzigen

Voorbeeld 1: `environment` Voeg de `environment` tag toe en vervang bestaande tags door 'Testen':

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

Voorbeeld 2: `env` Verwijder de `environment` tag en `environment` voeg de tag toe of vervang bestaande tags door een parameterwaarde:

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

Weigeren wordt gebruikt om te voorkomen dat een resourceaanvraag niet overeenkomt met gedefinieerde standaarden via een beleidsdefinitie en mislukt.

### <a name="deny-evaluation"></a>Evaluatie weigeren

Wanneer u een overeenkomende bron maakt of bijwerkt, voorkomt weigeren dat de aanvraag wordt verzonden voordat deze naar de resourceprovider wordt verzonden. Het verzoek wordt `403 (Forbidden)`geretourneerd als een . In de portal kan het Verboden worden gezien als een status op de implementatie die is verhinderd door de beleidstoewijzing.

Tijdens de evaluatie van bestaande resources worden resources die overeenkomen met een beleidsdefinitie voor weigeren gemarkeerd als niet-compatibel.

### <a name="deny-properties"></a>Eigenschappen weigeren

Het weigeringseffect heeft geen extra eigenschappen voor gebruik in de **toenmalige** voorwaarde van de beleidsdefinitie.

### <a name="deny-example"></a>Voorbeeld van weigeren weigeren

Voorbeeld: Het weigeren-effect gebruiken.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Controleren

De controle wordt gebruikt om een waarschuwingsgebeurtenis in het activiteitenlogboek te maken bij het evalueren van een niet-compatibele resource, maar het stopt de aanvraag niet.

### <a name="audit-evaluation"></a>Evaluatie van de controle

Audit is het laatste effect dat wordt gecontroleerd door Azure Policy tijdens het maken of bijwerken van een resource. Azure Policy stuurt de bron vervolgens naar de Resourceprovider. Audit werkt hetzelfde voor een resourceaanvraag en een evaluatiecyclus. Azure Policy `Microsoft.Authorization/policies/audit/action` voegt een bewerking toe aan het activiteitenlogboek en markeert de bron als niet-compatibel.

### <a name="audit-properties"></a>Controle-eigenschappen

Het controle-effect heeft geen extra eigenschappen voor gebruik in de **toenmalige** voorwaarde van de beleidsdefinitie.

### <a name="audit-example"></a>Controlevoorbeeld

Voorbeeld: Het controle-effect gebruiken.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditifNotexists

AuditIfNotExists maakt controle op resources die overeenkomen met de **als-voorwaarde,** maar heeft niet de componenten die zijn opgegeven in de **details** van de **toenmalige** voorwaarde.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists evaluatie

AuditIfNotExists wordt uitgevoerd nadat een resourceprovider een claim voor maken of bijwerken heeft verwerkt en een statuscode voor succes heeft geretourneerd. De controle vindt plaats als er geen gerelateerde resources zijn of als de resources die door **ExistenceCondition** zijn gedefinieerd, niet naar true worden geëvalueerd. Azure Policy `Microsoft.Authorization/policies/audit/action` voegt een bewerking op dezelfde manier toe aan het activiteitenlogboek als het controle-effect. Wanneer de resource die **if** voldoet aan de als-voorwaarde, is de resource die is gemarkeerd als niet-compatibel.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists eigenschappen

De **eigenschap details** van de effecten AuditIfNotExists heeft alle subeigenschappen die de bijbehorende resources definiëren die overeenkomen.

- **Type** [vereist]
  - Hiermee geeft u het type van de gerelateerde resource op dat moet worden overeenkomen.
  - Als **details.type** een resourcetype is onder de **bron als,** worden de beleidsquery's voor dit **type** resources binnen het bereik van de geëvalueerde resource geplaatst. Anders worden beleidsquery's binnen dezelfde resourcegroep als de geëvalueerde resource gebruikt.
- **Naam** (optioneel)
  - Hiermee geeft u de exacte naam op van de resource die overeenkomt en wordt het beleid één specifieke resource opgehaald in plaats van alle resources van het opgegeven type.
  - Wanneer de voorwaardewaarden voor **if.field.type** en **then.details.type** overeenkomen, `[field('name')]`wordt **naam** _vereist_ en moet . In plaats daarvan moet echter rekening worden gehouden met een [auditeffect.](#audit)
- **ResourceGroupName** (optioneel)
  - Hiermee kan de matching van de gerelateerde resource afkomstig zijn uit een andere resourcegroep.
  - Is niet van toepassing als **type** een resource is die onder de **bron als** voorwaarde zou zijn.
  - Standaard is de resourcegroep van de resource **als** voorwaarde.
- **ExistenceScope** (optioneel)
  - Toegestane waarden zijn _Subscription_ en _ResourceGroup_.
  - Hiermee stelt u het bereik in van waar u de gerelateerde resource moet ophalen om van af te komen.
  - Is niet van toepassing als **type** een resource is die onder de **bron als** voorwaarde zou zijn.
  - Voor _ResourceGroup_wordt de resourcegroep van de **resource of** de resourcegroep resource die is opgegeven in **ResourceGroupName**, beperkt tot de resourcegroep van de resourcegroep .
  - Voor _Abonnement_stelt u het volledige abonnement voor de gerelateerde bron op.
  - Standaard is _ResourceGroup_.
- **ExistenceCondition** (optioneel)
  - Als dit niet is opgegeven, voldoet elke gerelateerde resource van **het type** aan het effect en wordt de audit niet geactiveerd.
  - Gebruikt dezelfde taal als de **if** beleidsregel voor de als-voorwaarde, maar wordt afzonderlijk geëvalueerd op elke gerelateerde resource.
  - Als een overeenkomende gerelateerde resource wordt geëvalueerd naar true, wordt het effect bepaald en wordt de audit niet geactiveerd.
  - Kan [veld()] gebruiken om de gelijkwaardigheid met waarden in de **als-voorwaarde** te controleren.
  - U bijvoorbeeld valideren dat de bovenliggende **if** resource (in de als-voorwaarde) zich op dezelfde resourcelocatie bevindt als de overeenkomende gerelateerde resource.

### <a name="auditifnotexists-example"></a>Voorbeeld van AuditIfNotExists

Voorbeeld: Evalueert virtuele machines om te bepalen of de antimalware-extensie bestaat en controleert vervolgens wanneer deze ontbreekt.

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

Net als bij AuditIfNotExists voert een beleidsdefinitie van DeployIfNotExists een sjabloonimplementatie uit wanneer aan de voorwaarde is voldaan.

> [!NOTE]
> [Geneste sjablonen](../../../azure-resource-manager/templates/linked-templates.md#nested-template) worden ondersteund met **deployIfNotExists,** maar [gekoppelde sjablonen](../../../azure-resource-manager/templates/linked-templates.md#linked-template) worden momenteel niet ondersteund.

### <a name="deployifnotexists-evaluation"></a>Evaluatie deployIfNotExists

DeployIfNotExists wordt ongeveer 15 minuten nadat een resourceprovider een claim- of update-bronaanvraag heeft verwerkt en een statuscode voor succes heeft geretourneerd. Een sjabloonimplementatie vindt plaats als er geen gerelateerde resources zijn of als de resources die door **ExistenceCondition** zijn gedefinieerd, niet worden geëvalueerd naar true.
De duur van de implementatie is afhankelijk van de complexiteit van de resources die in de sjabloon zijn opgenomen.

Tijdens een evaluatiecyclus worden beleidsdefinities met een effect DeployIfNotExists die overeenkomen met resources gemarkeerd als niet-compatibel, maar er wordt geen actie ondernomen met die resource. Bestaande niet-conforme resources kunnen worden gesaneerd met een [hersteltaak.](../how-to/remediate-resources.md)

### <a name="deployifnotexists-properties"></a>Eigenschappen deployIfNotExists

De **eigenschap details** van het effect DeployIfNotExists heeft alle subeigenschappen die de bijbehorende resources definiëren die overeenkomen en de sjabloonimplementatie die moet worden uitgevoerd.

- **Type** [vereist]
  - Hiermee geeft u het type van de gerelateerde resource op dat moet worden overeenkomen.
  - Begin met het ophalen van een resource onder de **bron als** en query's binnen dezelfde resourcegroep als de **bronals.**
- **Naam** (optioneel)
  - Hiermee geeft u de exacte naam op van de resource die overeenkomt en wordt het beleid één specifieke resource opgehaald in plaats van alle resources van het opgegeven type.
  - Wanneer de voorwaardewaarden voor **if.field.type** en **then.details.type** overeenkomen, `[field('name')]`wordt **naam** _vereist_ en moet .
- **ResourceGroupName** (optioneel)
  - Hiermee kan de matching van de gerelateerde resource afkomstig zijn uit een andere resourcegroep.
  - Is niet van toepassing als **type** een resource is die onder de **bron als** voorwaarde zou zijn.
  - Standaard is de resourcegroep van de resource **als** voorwaarde.
  - Als een sjabloonimplementatie wordt uitgevoerd, wordt deze geïmplementeerd in de resourcegroep van deze waarde.
- **ExistenceScope** (optioneel)
  - Toegestane waarden zijn _Subscription_ en _ResourceGroup_.
  - Hiermee stelt u het bereik in van waar u de gerelateerde resource moet ophalen om van af te komen.
  - Is niet van toepassing als **type** een resource is die onder de **bron als** voorwaarde zou zijn.
  - Voor _ResourceGroup_wordt de resourcegroep van de **resource of** de resourcegroep resource die is opgegeven in **ResourceGroupName**, beperkt tot de resourcegroep van de resourcegroep .
  - Voor _Abonnement_stelt u het volledige abonnement voor de gerelateerde bron op.
  - Standaard is _ResourceGroup_.
- **ExistenceCondition** (optioneel)
  - Als deze niet is opgegeven, voldoet elke gerelateerde resource van **het type** aan het effect en wordt de implementatie niet geactiveerd.
  - Gebruikt dezelfde taal als de **if** beleidsregel voor de als-voorwaarde, maar wordt afzonderlijk geëvalueerd op elke gerelateerde resource.
  - Als een overeenkomende gerelateerde resource wordt geëvalueerd naar true, wordt het effect bepaald en wordt de implementatie niet geactiveerd.
  - Kan [veld()] gebruiken om de gelijkwaardigheid met waarden in de **als-voorwaarde** te controleren.
  - U bijvoorbeeld valideren dat de bovenliggende **if** resource (in de als-voorwaarde) zich op dezelfde resourcelocatie bevindt als de overeenkomende gerelateerde resource.
- **rolDefinitieIds** [vereist]
  - Deze eigenschap moet een reeks tekenreeksen bevatten die overeenkomen met de functie-id voor toegangsbeheer op basis van rollen die toegankelijk zijn voor het abonnement. Zie [Herstel - Beleidsdefinitie configureren](../how-to/remediate-resources.md#configure-policy-definition)voor meer informatie.
- **DeploymentScope** (optioneel)
  - Toegestane waarden zijn _Subscription_ en _ResourceGroup_.
  - Hiermee stelt u het type implementatie in dat moet worden geactiveerd. _Abonnement_ geeft een [implementatie op abonnementsniveau](../../../azure-resource-manager/templates/deploy-to-subscription.md)aan , _ResourceGroup_ geeft een implementatie aan naar een resourcegroep.
  - Een _locatieeigenschap_ moet worden opgegeven in de _implementatie_ bij het gebruik van implementaties op abonnementsniveau.
  - Standaard is _ResourceGroup_.
- **Implementatie** [vereist]
  - Deze eigenschap moet de volledige sjabloonimplementatie bevatten, `Microsoft.Resources/deployments` omdat deze zou worden doorgegeven aan de PUT-API. Zie de API [Implementaties REST voor](/rest/api/resources/deployments)meer informatie .

  > [!NOTE]
  > Alle functies binnen de eigenschap **Implementatie** worden geëvalueerd als onderdelen van de sjabloon, niet als het beleid. De uitzondering is de eigenschap **parameters** die waarden van het beleid doorgeeft aan de sjabloon. De **waarde** in deze sectie onder de naam van een sjabloonparameter wordt gebruikt om deze waarde door te geven (zie _fullDbName_ in het voorbeeld DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Voorbeeld van DeployIfNotExists

Voorbeeld: evalueert SQL Server-databases om te bepalen of transparentDataEncryption is ingeschakeld. Als dit niet het zo is, wordt een implementatie uitgevoerd om in te schakelen.

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

## <a name="enforceopaconstraint"></a>OpA-beperking afdwingen

Dit effect wordt gebruikt met `Microsoft.Kubernetes.Data`een beleidsdefinitiemodus van . *mode* Het wordt gebruikt om Gatekeeper v3-toegangsbeheerregels die zijn gedefinieerd met [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) om Beleidsagent (OPA) te [openen,](https://www.openpolicyagent.org/) door te geven aan Kubernetes-clusters op Azure.

> [!NOTE]
> [Azure Policy for Kubernetes](aks-engine.md) bevindt zich in Preview en ondersteunt alleen ingebouwde beleidsdefinities.

### <a name="enforceopaconstraint-evaluation"></a>Evaluatie van opaconstraint afdwingen

De toegangsverantwoordelijke voor open beleidsagent evalueert elke nieuwe aanvraag op het cluster in realtime.
Elke 15 minuten wordt een volledige scan van het cluster voltooid en worden de resultaten gerapporteerd aan Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Eigenschappen van OpAConstraint afdwingen

De **eigenschap Details** van het effect EnforceOPAConstraint heeft de subeigenschappen die de gatekeeper v3-toelatingsregel beschrijven.

- **constraintTemplate** [vereist]
  - De constrainttemplate CustomResourceDefinition (CRD) die nieuwe beperkingen definieert. De sjabloon definieert de rego-logica, het constraint-schema en de beperkingsparameters die worden doorgegeven via **waarden** uit Azure-beleid.
- **beperking** [vereist]
  - De CRD-implementatie van de sjabloon Beperking. Gebruikt parameters **values** die `{{ .Values.<valuename> }}`via waarden worden doorgegeven als . In het onderstaande voorbeeld `{{ .Values.cpuLimit }}` `{{ .Values.memoryLimit }}`zou dit zijn en .
- **waarden** [optioneel]
  - Hiermee definieert u alle parameters en waarden die u wilt doorgeven aan de beperking. Elke waarde moet bestaan in de CRD-sjabloon beperking.

### <a name="enforceopaconstraint-example"></a>Voorbeeld van Afdwingen opaconstraint

Voorbeeld: Gatekeeper v3-toegangsbesturingselementregel om container-CPU- en geheugenbronlimieten in Te stellen in Kubernetes.

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

## <a name="enforceregopolicy"></a>HandhavingRegoBeleid

Dit effect wordt gebruikt met `Microsoft.ContainerService.Data`een beleidsdefinitiemodus van . *mode* Het wordt gebruikt om Gatekeeper v2-toegangsbeheerregels te passeren die zijn gedefinieerd met [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) naar [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) op Azure [Kubernetes-service.](../../../aks/intro-kubernetes.md)

> [!IMPORTANT]
> [Azure Policy for Kubernetes](rego-for-aks.md) bevindt zich in Preview en ondersteunt alleen ingebouwde beleidsdefinities. Ingebouwd beleid bevalt in de categorie **Kubernetes.** Het **effect EnforceRegoPolicy** en het bijbehorende **Kubernetes Service-categoriebeleid** worden _afgeschaft._ Gebruik in plaats daarvan het bijgewerkte effect [EnforceOPAConstraint.](#enforceopaconstraint)

### <a name="enforceregopolicy-evaluation"></a>Evaluatie RegoPolicy afdwingen

De toegangsverantwoordelijke voor open beleidsagent evalueert elke nieuwe aanvraag op het cluster in realtime.
Elke 5 minuten wordt een volledige scan van het cluster voltooid en worden de resultaten gerapporteerd aan Azure Policy.

### <a name="enforceregopolicy-properties"></a>Eigenschappen RegoPolicy afdwingen

De **eigenschap details** van het effect EnforceRegoPolicy heeft de subeigenschappen die de gatekeeper v2-toelatingsregel beschrijven.

- **policyId** [vereist]
  - Een unieke naam die als parameter wordt doorgegeven aan de rego-toelatingscontroleregel.
- **beleid** [vereist]
  - Hiermee geeft u de URI van de regel voor het toegangscontrolebesturingselement van Rego op.
- **policyParameters** [optioneel]
  - Hiermee definieert u alle parameters en waarden die u wilt doorgeven aan het rego-beleid.

### <a name="enforceregopolicy-example"></a>Voorbeeld van RegoBeleid afdwingen

Voorbeeld: Gatekeeper v2-toelatingscontroleregel om alleen de opgegeven containerafbeeldingen in AKS toe te staan.

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

## <a name="layering-policies"></a>Laaglaagbeleid

Een resource kan worden beïnvloed door verschillende toewijzingen. Deze toewijzingen kunnen zich op hetzelfde bereik of op verschillende scopes begeven. Elk van deze toewijzingen heeft waarschijnlijk ook een ander effect gedefinieerd. De conditie en het effect voor elk beleid wordt onafhankelijk geëvalueerd. Bijvoorbeeld:

- Beleid 1
  - Beperkt de locatie van de resource tot 'westus'
  - Toegewezen aan abonnement A
  - Het effect Weigeren
- Beleid 2
  - Hiermee beperkt u de locatie van resources tot 'eastus'
  - Toegewezen aan resourcegroep B in abonnement A
  - Controle-effect
  
Deze instelling zou resulteren in de volgende uitkomst:

- Elke resource die al in resourcegroep B in 'eastus' is, voldoet aan beleid 2 en voldoet niet aan beleid 1
- Elke resource die al in resourcegroep B zit en niet in 'eastus' is niet-conform beleid 2 en niet voldoet aan beleid 1, zo niet in 'westus'
- Elke nieuwe bron in abonnement A niet in 'westus' wordt geweigerd door beleid 1
- Elke nieuwe resource in abonnement A en resourcegroep B in 'westus' wordt gemaakt en niet-compatibel met beleid 2

Als zowel beleid 1 als beleid 2 effect van ontkenning hadden, verandert de situatie in:

- Elke resource die al in resourcegroep B zit en niet in 'eastus' is niet in overeenstemming met beleid 2
- Elke bron die al in resourcegroep B zit en niet in 'westus' is niet in overeenstemming met beleid 1
- Elke nieuwe bron in abonnement A niet in 'westus' wordt geweigerd door beleid 1
- Elke nieuwe bron in resourcegroep B van abonnement A wordt geweigerd

Elke opdracht wordt individueel geëvalueerd. Als zodanig is er geen mogelijkheid voor een resource om door een kloof van verschillen in bereik te glippen. Het nettoresultaat van gelaagdheidsbeleid of beleidsoverlapping wordt beschouwd als **cumulatief meest beperkend**. Als beleid 1 en 2 bijvoorbeeld een ontkennend effect hebben, wordt een resource geblokkeerd door het overlappende en conflicterende beleid. Als u nog steeds de resource moet maken in het doelbereik, controleert u of de uitsluitingen voor elke toewijzing om het juiste beleid te valideren, van invloed zijn op de juiste scopes.

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](definition-structure.md).
- Begrijpen hoe [u programmatisch beleid maken.](../how-to/programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [herstellen van niet-conforme resources.](../how-to/remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
