---
title: Details van de structuur van de beleids toewijzing
description: Beschrijft de beleids toewijzings definitie die door Azure Policy wordt gebruikt om beleids definities en-para meters te koppelen aan resources voor evaluatie.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: c9f400aa72508822f8fff2fe41fb17ce98339737
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84205038"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-toewijzingsstructuur

Beleids toewijzingen worden gebruikt door Azure Policy om te definiëren welke resources worden toegewezen aan welke beleids regels of initiatieven. De beleids toewijzing kan de waarden van para meters voor die groep resources tijdens de toewijzing bepalen, waardoor beleids definities die overeenkomen met dezelfde resource-eigenschappen, opnieuw kunnen worden gebruikt met verschillende vereisten voor naleving.

U gebruikt JSON om een beleids toewijzing te maken. De beleids toewijzing bevat elementen voor:

- weergave naam
- description
- metagegevens
- Afdwingings modus
- uitgesloten bereiken
- beleids definitie
- parameters

Zo toont de volgende JSON een beleids toewijzing in de modus _DoNotEnforce_ met dynamische para meters:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
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

Alle Azure Policy-voor beelden zijn Azure Policy-voor [beelden](../samples/index.md).

## <a name="display-name-and-description"></a>Weergave naam en beschrijving

U gebruikt **DisplayName** en **Beschrijving** om de beleids toewijzing te identificeren en context te bieden voor het gebruik van de specifieke set resources. **DisplayName** heeft een maximale lengte van _128_ tekens en een **Beschrijving** van Maxi maal _512_ tekens.

## <a name="enforcement-mode"></a>Afdwingings modus

De eigenschap **enforcementMode** biedt klanten de mogelijkheid om het resultaat van een beleid op bestaande bronnen te testen zonder het beleids effect of de trigger vermeldingen in het [Azure-activiteiten logboek](../../../azure-monitor/platform/platform-logs-overview.md)te initiëren. Dit scenario wordt meestal ' What If ' genoemd en is afgestemd op veilige implementatie procedures. **enforcementMode** wijkt af van het [Uitgeschakelde](./effects.md#disabled) effect, wat betekent dat de resource-evaluatie helemaal niet kan plaatsvinden.

Deze eigenschap heeft de volgende waarden:

|Modus |JSON-waarde |Type |Hand matig herstellen |Vermelding in het activiteiten logboek |Beschrijving |
|-|-|-|-|-|-|
|Ingeschakeld |Standaard |tekenreeks |Ja |Ja |Het beleids effect wordt afgedwongen tijdens het maken of bijwerken van de resource. |
|Uitgeschakeld |DoNotEnforce |tekenreeks |Yes |Nee | Het beleids effect wordt niet afgedwongen tijdens het maken of bijwerken van resources. |

Als **enforcementMode** niet is opgegeven in een beleids-of initiatief definitie, wordt de _standaard_ waarde gebruikt. [Herstel taken](../how-to/remediate-resources.md) kunnen worden gestart voor [deployIfNotExists](./effects.md#deployifnotexists) -beleid, zelfs wanneer **enforcementMode** is ingesteld op _DoNotEnforce_.

## <a name="excluded-scopes"></a>Uitgesloten bereiken

Het **bereik** van de toewijzing bevat alle onderliggende resource containers en onderliggende resources. Als de definitie van een onderliggende bron container of onderliggende bron niet moet worden toegepast, kan deze worden uitgesloten van de evaluatie door **notScopes**in te stellen. Deze eigenschap is een matrix waarmee een of meer resource containers of bronnen kunnen worden uitgesloten. **notScopes** kunnen worden toegevoegd of bijgewerkt na het maken van de eerste toewijzing.

## <a name="policy-definition-id"></a>Beleids definitie-ID

Dit veld moet de volledige padnaam zijn van ofwel een beleids definitie of een initiatief definitie.
`policyDefinitionId`is een teken reeks en geen matrix. Het is raadzaam om in plaats daarvan een [initiatief](./initiative-definition-structure.md) te gebruiken als er vaak meerdere beleids regels aan elkaar worden toegewezen.

## <a name="parameters"></a>Parameters

Dit segment van de beleids toewijzing bevat de waarden voor de para meters die zijn gedefinieerd in de [beleids definitie of initiatief definitie](./definition-structure.md#parameters).
Dit ontwerp maakt het mogelijk om een beleid of initiatief definitie met verschillende resources te hergebruiken, maar te controleren op verschillende bedrijfs waarden of-resultaten.

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

In dit voor beeld zijn de para meters die eerder zijn gedefinieerd in de beleids definitie, `prefix` en `suffix` . Deze specifieke beleids toewijzing is ingesteld `prefix` op **afda** en `suffix` naar **-LC**. Dezelfde beleids definitie kan opnieuw worden gebruikt met een andere set para meters voor een andere afdeling, waardoor het dupliceren en de complexiteit van beleids definities wordt verkort tijdens het bieden van flexibiliteit.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de structuur van de [beleids definitie](./definition-structure.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).