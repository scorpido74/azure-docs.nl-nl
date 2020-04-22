---
title: 'Zelfstudie: Taggovernance beheren'
description: In deze zelfstudie gebruikt u het effect Wijzigen van Azure Policy om een taggovernancemodel te maken en af te dwingen op nieuwe en bestaande resources.
ms.date: 04/21/2020
ms.topic: tutorial
ms.openlocfilehash: 6319bbde2fdc8f78e2743dd5f1565c8680433fea
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759060"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Zelfstudie: Tagbeheer beheren met Azure-beleid beheren

[Tags](../../../azure-resource-manager/management/tag-resources.md) zijn een cruciaal onderdeel van het organiseren van uw Azure-resources in een taxonomie. Wanneer u [aanbevolen procedures voor tagbeheer volgt,](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)kunnen tags de basis vormen voor het toepassen van uw bedrijfsbeleid met Azure-beleid of [het bijhouden van kosten met Kostenbeheer.](../../../cost-management-billing/costs/cost-mgt-best-practices.md#organize-and-tag-your-resources)
Het maakt niet uit hoe of waarom u tags gebruikt, het is belangrijk dat u deze tags op uw Azure-bronnen snel toevoegen, wijzigen en verwijderen. Zie [Ondersteuning](../../../azure-resource-manager/management/tag-support.md)voor tags als u wilt zien of uw Azure-bron tagging ondersteunt.

Het effect [Wijzigen](../concepts/effects.md#modify) van Azure Policy is ontworpen om te helpen bij het beheer van tags, ongeacht in welke fase van resourcegovernance u zich bevindt. **Wijzigen** helpt wanneer:

- Je bent nieuw in de cloud en hebt geen taggovernance
- Heb al duizenden resources zonder tag governance
- Al een bestaande taxonomie die u moet veranderen

In deze zelfstudie voltooit u de volgende taken:

> [!div class="checklist"]
> - Identificeer de vereisten van uw bedrijf
> - Elke vereiste toewijzen aan een beleidsdefinitie
> - Het tagbeleid groeperen in een initiatief

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als je nog geen account hebt, maak je een [gratis account](https://azure.microsoft.com/free/) aan voordat je begint.

## <a name="identify-requirements"></a>Vereisten identificeren

Net als elke goede implementatie van governance-controles, moeten de vereisten afkomstig zijn van uw zakelijke behoeften en goed worden begrepen voordat u technische controles maakt. Voor deze scenario-zelfstudie zijn de volgende items onze zakelijke vereisten:

- Twee vereiste tags op alle resources: _CostCenter_ en _Env_
- _CostCenter_ moet bestaan op alle containers en afzonderlijke resources
  - Resources erven van de container waarin ze zich bevindt, maar kunnen afzonderlijk worden overschreven
- _Env_ moet bestaan op alle containers en individuele bronnen
  - Resources bepalen omgeving op containernaamgevingsschema en mogen niet worden overschreven
  - Alle resources in een container maken deel uit van dezelfde omgeving

## <a name="configure-the-costcenter-tag"></a>De CostCenter-tag configureren

In termen die specifiek zijn voor een Azure-omgeving die wordt beheerd door Azure Policy, vragen de _CostCenter-tagvereisten_ om het volgende:

- Resourcegroepen weigeren die de _CostCenter-tag_ missen
- Resources wijzigen om de _CostCenter-tag_ toe te voegen uit de bovenliggende resourcegroep wanneer deze ontbreekt

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Resourcegroepen weigeren die de CostCenter-tag missen

Aangezien de _CostCenter_ voor een resourcegroep niet kan worden bepaald door de naam van de resourcegroep, moet de tag zijn gedefinieerd op het verzoek om de resourcegroep te maken. Met de volgende beleidsregel met het effect [Weigeren](../concepts/effects.md#deny) voorkomt u het maken of bijwerken van resourcegroepen die niet over de _CostCenter-tag_ beschikken:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Aangezien deze beleidsregel zich richt op een resourcegroep, moet de _modus_ voor de beleidsdefinitie 'Alles' zijn in plaats van 'Geïndexeerd'.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Resources wijzigen om de CostCenter-tag over te nemen wanneer deze ontbreekt

De tweede _CostCenter-behoefte_ is dat resources de tag overnemen van de bovenliggende resourcegroep wanneer deze ontbreekt. Als de tag al is gedefinieerd op de resource, zelfs als deze verschilt van de bovenliggende resourcegroep, moet deze met rust worden gelaten. De volgende beleidsregel gebruikt [Wijzigen:](../concepts/effects.md#modify)

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Deze beleidsregel gebruikt de **add-bewerking in** plaats van **addOrReplace** omdat we de tagwaarde niet willen wijzigen als deze aanwezig is bij het [herstellen van](../how-to/remediate-resources.md) bestaande resources. Het maakt `[resourcegroup()]` ook gebruik van de sjabloonfunctie om de tagwaarde uit de bovenliggende resourcegroep te halen.

> [!NOTE]
> Aangezien deze beleidsregel zich richt op resources die tags ondersteunen, moet de _modus_ voor de beleidsdefinitie 'Geïndexeerd' zijn. Deze configuratie zorgt er ook voor dat dit beleid resourcegroepen overslaat.

## <a name="configure-the-env-tag"></a>De Env-tag configureren

In termen die specifiek zijn voor een Azure-omgeving die wordt beheerd door Azure Policy, vragen de _Env-tagvereisten_ om het volgende:

- De _Env-tag_ op de resourcegroep wijzigen op basis van het naamgevingsschema van de resourcegroep
- De _Env-tag_ op alle resources in de resourcegroep wijzigen in hetzelfde als de bovenliggende resourcegroep

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Resourcegroepen Env-tag wijzigen op basis van naam

Voor elke omgeving die in uw Azure-omgeving bestaat, is een [wijzigingsbeleid](../concepts/effects.md#modify) vereist. Het beleid wijzigen voor elk ziet er ongeveer als deze beleidsdefinitie uit:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Aangezien deze beleidsregel zich richt op een resourcegroep, moet de _modus_ voor de beleidsdefinitie 'Alles' zijn in plaats van 'Geïndexeerd'.

Dit beleid komt alleen overeen met resourcegroepen `prd-`met het steekproefnaamgevingsschema dat wordt gebruikt voor productieresources van . Complexere naamgevingsschema's kunnen worden bereikt met verschillende **wedstrijdvoorwaarden** in plaats van de enkele **zoals** in dit voorbeeld.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Resources wijzigen om de Env-tag over te nemen

De zakelijke vereiste vereist dat alle resources de _Env-tag_ hebben die hun bovenliggende resourcegroep heeft. Deze tag kan niet worden overschreven, dus we gebruiken de **bewerking addOrReplace** met het effect [Wijzigen.](../concepts/effects.md#modify) Het voorbeeld Beleid Wijzigen ziet eruit als de volgende regel:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Aangezien deze beleidsregel zich richt op resources die tags ondersteunen, moet de _modus_ voor de beleidsdefinitie 'Geïndexeerd' zijn. Deze configuratie zorgt er ook voor dat dit beleid resourcegroepen overslaat.

Met deze beleidsregel wordt gezocht naar resources die geen waarde hebben voor bovenliggende resourcegroepen voor de _Env-tag_ of de _Env-tag_ missen. Bij passende resources is de _Env-tag_ ingesteld op de waarde van de bovenliggende resourcegroepen, zelfs als de tag al op de resource bestond, maar met een andere waarde.

## <a name="assign-the-initiative-and-remediate-resources"></a>Het initiatief toewijzen en resources herstellen

Zodra het bovenstaande tagbeleid is gemaakt, sluit u ze aan bij één initiatief voor tagbeheer en wijst u ze toe aan een beheergroep of -abonnement. Het initiatief en opgenomen beleid evalueren vervolgens de naleving van bestaande resources en wijzigt aanvragen voor nieuwe of bijgewerkte resources die overeenkomen met de **eigenschap if** in de beleidsregel. Het beleid werkt bestaande niet-conforme resources echter niet automatisch bij met de gedefinieerde tagwijzigingen.

Net als het beleid [voor implementatieAlsNiet-bestaan](../concepts/effects.md#deployifnotexists) gebruikt het beleid **Voor het wijzigen** herstel hersteltaken om bestaande niet-compatibele resources te wijzigen. Volg de aanwijzingen [voor Het herstellen van resources](../how-to/remediate-resources.md) om uw niet-conforme resources **wijzigen** te identificeren en de tags te corrigeren op uw gedefinieerde taxonomie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle bovenstaande toewijzingen of definities te verwijderen:

1. Selecteer **Definities** (of **Toewijzingen** als u een toewijzing wilt verwijderen) onder **Ontwerpen** in het linkerdeelvenster van de Azure Policy-pagina.

1. Zoek naar de nieuwe initiatief- of beleidsdefinitie (of toewijzing) die u zojuist hebt gemaakt.

1. Klik met de rechtermuisknop op de rij of selecteer de weglatingstekens aan het einde van de definitie (of de toewijzing) en selecteer **Definitie verwijderen** (of **Toewijzing verwijderen**).

## <a name="review"></a>Beoordelen

In deze zelfstudie leert u over de volgende taken:

> [!div class="checklist"]
> - De vereisten van uw bedrijf geïdentificeerd
> - Elke vereiste toegewezen aan een beleidsdefinitie
> - Het tagbeleid gegroepeerd in een initiatief

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel voor meer informatie over de structuur van beleidsdefinities:

> [!div class="nextstepaction"]
> [Structuur van Azure-beleidsdefinities](../concepts/definition-structure.md)