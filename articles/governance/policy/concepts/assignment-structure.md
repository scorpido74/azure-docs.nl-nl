---
title: Details van de beleidstoewijzingsstructuur
description: Beschrijft de beleidstoewijzingsdefinitie die wordt gebruikt door Azure Policy om beleidsdefinities en parameters te relateren aan resources voor evaluatie.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265296"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-toewijzingsstructuur

Beleidstoewijzingen worden gebruikt door Azure Policy om te bepalen welke resources worden toegewezen aan welk beleid of welke initiatieven. De beleidstoewijzing kan de waarden van parameters voor die groep resources bepalen tijdens de toewijzing, zodat beleidsdefinities die dezelfde resourceeigenschappen met verschillende nalevingsbehoeften worden aangepakt, opnieuw kunnen worden gebruikt.

U gebruikt JSON om een beleidstoewijzing te maken. De beleidstoewijzing bevat elementen voor:

- weergavenaam
- description
- metagegevens
- handhavingsmodus
- beleidsdefinitie
- parameters

In de volgende JSON wordt bijvoorbeeld een beleidstoewijzing weergegeven in _de DoNotEnforce-modus_ met dynamische parameters:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Alle azure-beleidsvoorbeelden bevinden zich op [Azure Policy-voorbeelden](../samples/index.md).

## <a name="display-name-and-description"></a>Weergavenaam en beschrijving

U gebruikt **displayName** en **beschrijving** om de beleidstoewijzing te identificeren en context te bieden voor het gebruik ervan met de specifieke set resources. **displayName** heeft een maximale lengte van _128_ tekens en **een beschrijving** met een maximale lengte van _512_ tekens.

## <a name="enforcement-mode"></a>Handhavingsmodus

De eigenschap **enforcementMode** biedt klanten de mogelijkheid om de resultaten van een beleid op bestaande resources te testen zonder het beleidseffect te initiëren of items in het [Azure Activity-logboek te](../../../azure-monitor/platform/platform-logs-overview.md)activeren. Dit scenario wordt vaak aangeduid als 'Wat als' genoemd en wordt uitgelijnd op veilige implementatiepraktijken. **enforcementMode** is anders dan het [effect Uitgeschakeld,](./effects.md#disabled) omdat dat effect voorkomt dat resourceevaluatie helemaal niet gebeurt.

Deze eigenschap heeft de volgende waarden:

|Modus |JSON-waarde |Type |Handmatig herstellen |Vermelding van activiteitenlogboeken |Beschrijving |
|-|-|-|-|-|-|
|Ingeschakeld |Standaard |tekenreeks |Ja |Ja |Het beleidseffect wordt afgedwongen tijdens het maken of bijwerken van resources. |
|Uitgeschakeld |DoNotEnforce |tekenreeks |Ja |Nee | Het beleidseffect wordt niet afgedwongen tijdens het maken of bijwerken van resources. |

Als **enforcementMode** niet is opgegeven in een beleids- of initiatiefdefinitie, wordt de waarde _Standaard_ gebruikt. [Hersteltaken](../how-to/remediate-resources.md) kunnen worden gestart voor [het implementeren van IfNotExists-beleid,](./effects.md#deployifnotexists) zelfs wanneer **enforcementMode** is ingesteld op _DoNotEnforce_.

## <a name="policy-definition-id"></a>Beleidsdefinitie-id

Dit veld moet de volledige padnaam zijn van een beleidsdefinitie of een initiatiefdefinitie.
`policyDefinitionId`is een tekenreeks en geen array. Het wordt aanbevolen dat als meerdere beleidsregels vaak samen worden toegewezen, in plaats daarvan een [initiatief](./definition-structure.md#initiatives) gebruiken.

## <a name="parameters"></a>Parameters

Dit segment van de beleidstoewijzing bevat de waarden voor de parameters die zijn gedefinieerd in de [beleidsdefinitie of initiatiefdefinitie](./definition-structure.md#parameters).
Dit ontwerp maakt het mogelijk om een beleids- of initiatiefdefinitie met verschillende bronnen te hergebruiken, maar te controleren op verschillende bedrijfswaarden of -resultaten.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

In dit voorbeeld zijn de parameters die `prefix` eerder `suffix`in de beleidsdefinitie zijn gedefinieerd en . `prefix` Deze specifieke beleidsopdracht wordt ingesteld `suffix` op **DeptA** en **-LC**. Dezelfde beleidsdefinitie is herbruikbaar met een andere reeks parameters voor een andere afdeling, waardoor de duplicatie en complexiteit van beleidsdefinities worden verminderd en flexibiliteit wordt geboden.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [beleidsdefinitiestructuur](./definition-structure.md).
- Begrijpen hoe [u programmatisch beleid maken.](../how-to/programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [herstellen van niet-conforme resources.](../how-to/remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)