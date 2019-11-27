---
title: 'Zelf studie: Manage tag governance'
description: In deze zelf studie gebruikt u het effect wijzigen van Azure Policy om een tag governance model op nieuwe en bestaande resources te maken en af te dwingen.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: e3d6e279b293ea8063c690f9fb69a6f183b2838d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482266"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Zelf studie: tag governance met Azure Policy beheren

[Labels](../../../azure-resource-manager/resource-group-using-tags.md) zijn een belang rijk onderdeel van het organiseren van uw Azure-resources in een taxonomie. Bij het volgen van de [Aanbevolen procedures voor het beheer](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)van labels kunnen Tags de basis vormen voor het Toep assen van uw bedrijfs beleid met Azure Policy of het [bijhouden van kosten met Cost Management](../../../cost-management/cost-mgt-best-practices.md#organize-and-tag-your-resources).
Ongeacht hoe of waarom u tags gebruikt, is het belang rijk dat u deze Tags snel kunt toevoegen, wijzigen en verwijderen in uw Azure-resources.

Het [wijzigings](../concepts/effects.md#modify) effect van Azure Policy is ontworpen om u te helpen bij het beheer van labels, ongeacht de fase van het resource governance waarin u zich bevindt. **Wijzigen** helpt bij het volgende:

- U hebt nog geen ervaring met de Cloud en hebt geen tag governance
- Heeft al duizenden resources zonder tag governance
- U hebt al een bestaande taxonomie die u moet wijzigen

In deze zelf studie voert u de volgende taken uit:

> [!div class="checklist"]
> - Identificeer de vereisten van uw bedrijf
> - Elke vereiste toewijzen aan een beleids definitie
> - De code beleidsregels groeperen in een initiatief

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="identify-requirements"></a>Vereisten identificeren

Net als bij een goede implementatie van governance-besturings elementen, moeten de vereisten afkomstig zijn van uw bedrijfs behoeften en goed begrijpen voordat u technische controles kunt maken. Voor dit scenario zelf studie zijn de volgende items onze zakelijke vereisten:

- Twee vereiste labels voor alle resources: _CostCenter_ en _env_
- _CostCenter_ moet zijn opgenomen in alle containers en afzonderlijke resources
  - Bronnen nemen toe van de container waarin ze zich bevinden, maar kunnen afzonderlijk worden overschreven
- _Env_ moet zijn opgenomen in alle containers en afzonderlijke resources
  - Bronnen bepalen omgeving per container naam schema en kunnen niet worden overschreven
  - Alle resources in een container maken deel uit van dezelfde omgeving

## <a name="configure-the-costcenter-tag"></a>De CostCenter-tag configureren

In termen die specifiek zijn voor een Azure-omgeving die wordt beheerd door Azure Policy, oproep de _CostCenter_ -label vereisten voor het volgende:

- Niet toestaan dat resource groepen de _CostCenter_ -tag missen
- Resources wijzigen om de _CostCenter_ -code uit de bovenliggende resource groep toe te voegen wanneer deze ontbreekt

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Niet toestaan dat resource groepen de CostCenter-tag missen

Omdat de _CostCenter_ voor een resource groep niet kan worden bepaald door de naam van de resource groep, moet de tag zijn gedefinieerd voor de aanvraag om de resource groep te maken. Met de volgende beleids regel met het effect [deny](../concepts/effects.md#deny) wordt voor komen dat resource groepen worden gemaakt of bijgewerkt die geen _CostCenter_ -label hebben:

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
> Omdat deze beleids regel is gericht op een resource groep, moet de _modus_ voor de beleids definitie ' all ' zijn in plaats van ' Indexed '.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Resources wijzigen om de CostCenter-tag over te nemen als deze ontbreken

De tweede _CostCenter_ moet voor alle resources de tag overnemen van de bovenliggende resource groep wanneer deze ontbreekt. Als de tag al is gedefinieerd voor de resource, zelfs als deze afwijkt van de bovenliggende resource groep, moet deze op zichzelf blijven staan. De volgende beleids regel gebruikt u [wijzigen](../concepts/effects.md#modify):

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

Deze beleids regel maakt gebruik van de bewerking **toevoegen** in plaats van **addOrReplace** , omdat we de waarde van tag niet willen wijzigen als deze aanwezig zijn bij het [herstellen](../how-to/remediate-resources.md) van bestaande resources. Er wordt ook gebruikgemaakt van de functie `[resourcegroup()]`-sjabloon om de label waarde van de bovenliggende resource groep op te halen.

> [!NOTE]
> Omdat deze beleids regel is gericht op resources die labels ondersteunen, moet de _modus_ voor de beleids definitie Indexed zijn. Deze configuratie zorgt er ook voor dat er door dit beleid geen resource groepen worden overgeslagen.

## <a name="configure-the-env-tag"></a>De env-tag configureren

In termen die specifiek zijn voor een Azure-omgeving die wordt beheerd door Azure Policy, oproep de _env_ -label vereisten voor het volgende:

- Wijzig de _env_ -tag voor de resource groep op basis van het naamgevings schema van de resource groep
- Wijzig de _env_ -tag voor alle resources in de resource groep in dezelfde als de bovenliggende resource groep

### <a name="modify-resource-groups-env-tag-based-on-name"></a>De env-tag van de resource groepen wijzigen op basis van de naam

Een [wijzigings](../concepts/effects.md#modify) beleid is vereist voor elke omgeving die in uw Azure-omgeving bestaat. Het wijzigings beleid voor elk ziet er ongeveer uit als deze beleids definitie:

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
> Omdat deze beleids regel is gericht op een resource groep, moet de _modus_ voor de beleids definitie ' all ' zijn in plaats van ' Indexed '.

Dit beleid komt alleen overeen met resource groepen met het voorbeeld naamgevings schema dat wordt gebruikt voor productie resources van `prd-`. Complexere naamgevings regels kunnen worden bereikt met verschillende **match** -voor **waarden in plaats van in dit** voor beeld.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Resources wijzigen om de env-tag over te nemen

De zakelijke vereiste vraagt om alle resources de _env_ -tag te hebben voor de bovenliggende resource groep. Dit label kan niet worden overschreven. Daarom gebruiken we de bewerking **addOrReplace** met het [wijzigings](../concepts/effects.md#modify) effect. Het voor beeld-wijzigings beleid ziet er als volgt uit:

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
> Omdat deze beleids regel is gericht op resources die labels ondersteunen, moet de _modus_ voor de beleids definitie Indexed zijn. Deze configuratie zorgt er ook voor dat er door dit beleid geen resource groepen worden overgeslagen.

Deze beleids regel zoekt naar resources die geen waarde hebben voor de bovenliggende resource groepen voor de _env_ -tag of waarvoor de _env_ -code ontbreekt. Voor overeenkomende resources is de _env_ -tag ingesteld op de waarde van de bovenliggende resource groepen, zelfs als de tag al in de resource aanwezig is, maar met een andere waarde.

## <a name="assign-the-initiative-and-remediate-resources"></a>Het initiatief toewijzen en resources herstellen

Zodra de code beleidsregels zijn gemaakt, voegt u deze toe aan één initiatief voor tag governance en wijst u deze toe aan een beheer groep of abonnement. Het initiatief en het opgenomen beleid evalueren vervolgens de naleving van bestaande resources en wijzigt aanvragen voor nieuwe of bijgewerkte resources die overeenkomen met de **if** -eigenschap in de beleids regel. Het beleid werkt echter niet automatisch bestaande niet-compatibele resources bij met de gedefinieerde label wijzigingen.

Net als [deployIfNotExists](../concepts/effects.md#deployifnotexists) -beleid gebruikt het **Modify** -beleid herstel taken om bestaande niet-compatibele resources te wijzigen. Volg de instructies voor het [herstellen van resources](../how-to/remediate-resources.md) om uw niet-compatibele **wijzigings** resources te identificeren en de labels te corrigeren op uw gedefinieerde taxonomie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle bovenstaande toewijzingen of definities te verwijderen:

1. Selecteer **Definities** (of **Toewijzingen** als u een toewijzing wilt verwijderen) onder **Ontwerpen** in het linkerdeelvenster van de Azure Policy-pagina.

1. Zoek naar de nieuwe initiatief- of beleidsdefinitie (of toewijzing) die u zojuist hebt gemaakt.

1. Klik met de rechtermuisknop op de rij of selecteer de weglatingstekens aan het einde van de definitie (of de toewijzing) en selecteer **Definitie verwijderen** (of **Toewijzing verwijderen**).

## <a name="review"></a>Beoordelen

In deze zelf studie hebt u geleerd over de volgende taken:

> [!div class="checklist"]
> - De vereisten van uw bedrijf geïdentificeerd
> - Elke vereiste aan een beleids definitie toegewezen
> - Het code beleid is gegroepeerd in een initiatief

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel voor meer informatie over de structuur van beleidsdefinities:

> [!div class="nextstepaction"]
> [Structuur van Azure Policy-definities](../concepts/definition-structure.md)