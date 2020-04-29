---
title: 'Patroon: resources implementeren met een beleids definitie'
description: Dit Azure Policy patroon bevat een voor beeld van het implementeren van resources met een beleids definitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172672"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure Policy patroon: resources implementeren

Het effect [deployIfNotExists](../concepts/effects.md#deployifnotexists) maakt het mogelijk om een [Azure Resource Manager sjabloon](../../../azure-resource-manager/templates/overview.md) te implementeren bij het maken of bijwerken van een resource die niet aan het beleid voldoet. Deze methode kan worden gebruikt om het afwijzen te gebruiken omdat er resources kunnen worden gemaakt, maar zorgt ervoor dat de wijzigingen worden [doorgevoerd om](../concepts/effects.md#deny) ze compatibel te maken.

## <a name="sample-policy-definition"></a>Voor beeld van beleids definitie

Deze beleids definitie maakt gebruik van de operator **Field** om `type` de resource te evalueren die is gemaakt of bijgewerkt. Wanneer die resource een _micro soft. Network/virtualNetworks_is, zoekt het beleid naar een netwerk-Watcher op de locatie van de nieuwe of bijgewerkte bron. Als er geen overeenkomende Network Watcher is gevonden, wordt de Resource Manager-sjabloon geïmplementeerd om de ontbrekende bron te maken.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Uitleg

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

De **Eigenschappen. policyRule. then. Details** blok vertelt Azure Policy wat er moet worden gezocht naar de gemaakte of bijgewerkte bron in het **Eigenschappen. policyRule. If** -blok. In dit voor beeld moet een netwerk-Watcher in het **networkWatcherRG** van de resource groep bestaan met een **veld** `location` dat gelijk is aan de locatie van de nieuwe of bijgewerkte bron. Met de `field()` functie krijgt de **existenceCondition** toegang tot de eigenschappen van de nieuwe of bijgewerkte resource, met `location` name de eigenschap.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

De **roleDefinitionIds** _matrix_ eigenschap roleDefinitionIds in de **Eigenschappen. policyRule. then. Details** blok vertelt de beleids definitie die de beheerde identiteit nodig heeft voor het implementeren van de opgenomen Resource Manager-sjabloon. Deze eigenschap moet worden ingesteld op rollen met de machtigingen die nodig zijn voor de sjabloon implementatie, maar moet het concept ' beginsel van minimale bevoegdheid ' gebruiken, maar alleen de benodigde bewerkingen en niets meer.

#### <a name="deployment-template"></a>Implementatiesjabloon

Het gedeelte **implementatie** van de beleids definitie heeft een **Eigenschappen** blok dat de drie kern onderdelen definieert:

- **mode** : met deze eigenschap wordt de [implementatie modus](../../../azure-resource-manager/templates/deployment-modes.md) van de sjabloon ingesteld.

- **sjabloon** : deze eigenschap bevat de sjabloon zelf. In dit voor beeld stelt de **locatie** sjabloon parameter de locatie van de nieuwe Network Watcher-resource in.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **para meters** : deze eigenschap definieert de para meters die aan de **sjabloon**worden gegeven. De parameter namen moeten overeenkomen met wat zijn gedefinieerd in de **sjabloon**. In dit voor beeld heeft de para meter de naam **locatie** die overeenkomt met. De waarde van de **locatie** gebruikt `field()` de functie opnieuw om de waarde van de geëvalueerde resource op te halen. Dit is het virtuele netwerk in de **policyRule. If** blok keren.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).