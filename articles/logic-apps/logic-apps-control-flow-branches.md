---
title: Parallelle branches maken of aansluiten voor acties in werkstromen
description: Informatie over het maken of samenvoegen van parallelle lopende branches voor onafhankelijke werkstroomacties in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: c0b1519992ba930382a1987aed185ef3c92eded4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453436"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Parallelle branches maken of deelnemen voor werkstroomacties in Azure Logic Apps

Standaard worden uw acties in logische app-werkstromen achtereenvolgens uitgevoerd. Als u tegelijkertijd onafhankelijke acties wilt uitvoeren, u [parallelle branches](#parallel-branches)maken en deze branches later in uw stroom [aansluiten.](#join-branches) 

> [!TIP] 
> Als u een trigger hebt die een array ontvangt en een werkstroom wilt uitvoeren voor elk arrayitem, u die array *debatchen* met de eigenschap [ **SplitOn** trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Parallelle vertakking toevoegen

Als u tegelijkertijd onafhankelijke stappen wilt uitvoeren, u parallelle branches toevoegen naast een bestaande stap. 

![Stappen parallel uitvoeren](media/logic-apps-control-flow-branches/parallel.png)

Uw logische app wacht tot alle branches zijn voltooid voordat de werkstroom wordt voortgezet. Parallelle branches worden `runAfter` alleen uitgevoerd wanneer de eigenschapswaarden overeenkomen met de status van de voltooide bovenliggende stap. `branchAction1` Beide en `branchAction2` zijn bijvoorbeeld alleen ingesteld `parentAction` om te `Succeeded` worden uitgevoerd wanneer de status is voltooid.

> [!NOTE]
> Voordat u begint, moet uw logica-app al een stap hebben waarin u parallelle branches toevoegen.

1. Open uw logische app in de <a href="https://portal.azure.com" target="_blank">Azure-portal</a>in Logic App Designer.

1. Beweeg de aanwijzer over de pijl boven de stap waar u parallelle takken wilt toevoegen. Kies het **plusteken** (**+**) dat wordt weergegeven en kies **vervolgens Een parallelle vertakking toevoegen**. 

   ![Parallelle vertakking toevoegen](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Zoek en selecteer in het zoekvak de gewenste actie.

   ![De gewenste actie zoeken en selecteren](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   De geselecteerde actie wordt nu weergegeven in de parallelle vertakking, bijvoorbeeld:

   ![De gewenste actie zoeken en selecteren](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Voeg nu in elke parallelle tak de gewenste stappen toe. Als u een andere actie aan een vertakking wilt toevoegen, verplaatst u de aanwijzer onder de actie waarin u een opeenvolgende actie wilt toevoegen. Kies het**+** **plusteken** ( ) dat wordt weergegeven en selecteer **Vervolgens Een actie toevoegen**.

   ![Sequentiële actie toevoegen aan parallelle vertakking](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Zoek en selecteer in het zoekvak de gewenste actie.

   ![Sequentiële actie zoeken en selecteren](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   De geselecteerde actie wordt nu weergegeven in de huidige branch, bijvoorbeeld:

   ![Sequentiële actie zoeken en selecteren](media/logic-apps-control-flow-branches/added-sequential-action.png)

Als u branches weer wilt samenvoegen, [voegt u uw parallelle branches toe.](#join-branches) 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Parallelle branch definitie (JSON)

Als u in de codeweergave werkt, u in plaats daarvan de parallelle structuur in de JSON-definitie van uw logische app definiëren, bijvoorbeeld:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Parallelle branches aansluiten

Als u parallelle takken wilt samenvoegen, voegt u onderaan onder alle takken een stap toe. Deze stap loopt na alle parallelle takken klaar met draaien.

![Parallelle branches aansluiten](media/logic-apps-control-flow-branches/join.png)

1. Zoek en open uw logische app in de [Azure-portal](https://portal.azure.com)in Logic App Designer. 

1. Kies **Nieuwe stap**onder de parallelle branches waaraan u wilt deelnemen. 

   ![Stap toevoegen om lid te worden](media/logic-apps-control-flow-branches/add-join-step.png)

1. Zoek en selecteer in het zoekvak de gewenste actie als de stap die bij de branches komt.

   ![Zoeken en selecteren van de actie die parallelle branches verbindt](media/logic-apps-control-flow-branches/join-steps.png)

   Uw parallelle takken zijn nu samengevoegd.

   ![Samengevoegde vestigingen](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Lid worden van definitie (JSON)

Als u in de codeweergave werkt, u de joinstructuur definiëren in de JSON-definitie van uw logische app, bijvoorbeeld:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Ga naar de [feedbacksite](https://aka.ms/logicapps-wish)van Azure Logic Apps om functies en suggesties in te dienen of te stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voorwaarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren op basis van verschillende waarden (schakelinstructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Stappen uitvoeren op basis van gegroepeerde actiestatus (scopes)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
