---
title: Voorwaardelijke instructies toevoegen aan werkstromen
description: Voorwaarden maken die acties in werkstromen in Azure Logic Apps beheren
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: fe79cf5af86e1f303e4735214b993d8db4488a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793246"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Voorwaardelijke instructies maken die werkstroomacties in Azure Logic Apps beheren

Als u specifieke acties in uw logische app alleen wilt uitvoeren nadat u een bepaalde voorwaarde hebt doorgegeven, voegt u een *voorwaardelijke instructie toe*. Deze besturingselementstructuur vergelijkt de gegevens in uw werkstroom met specifieke waarden of velden. U vervolgens verschillende acties opgeven die worden uitgevoerd op basis van het feit of de gegevens al dan niet aan de voorwaarde voldoen. Je de omstandigheden in elkaar nestelen.

Stel dat u een logische app hebt die te veel e-mails verzendt wanneer nieuwe items worden weergegeven in de RSS-feed van een website. U een voorwaardelijke instructie alleen toevoegen om e-mail te verzenden wanneer het nieuwe item een specifieke tekenreeks bevat. 

> [!TIP]
> Als u verschillende stappen wilt uitvoeren op basis van verschillende specifieke waarden, gebruikt u in plaats daarvan een [*switchinstructie.*](../logic-apps/logic-apps-control-flow-switch-statement.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Als u het voorbeeld in dit artikel wilt volgen, [maakt u deze voorbeeldlogica-app](../logic-apps/quickstart-create-first-logic-app-workflow.md) met een Outlook.com- of Office 365 Outlook-account.

## <a name="add-condition"></a>Voorwaarde toevoegen

1. Open uw logische app in de <a href="https://portal.azure.com" target="_blank">Azure-portal</a>in Logic App Designer.

1. Voeg een voorwaarde toe op de gewenste locatie. 

   Als u een voorwaarde tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl waar u de voorwaarde wilt toevoegen. Kies het **plusteken** (**+**) dat wordt weergegeven en kies **vervolgens Een actie toevoegen**. Bijvoorbeeld:

   ![Actie tussen stappen toevoegen](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Wanneer u een voorwaarde aan het einde van uw werkstroom wilt toevoegen, kiest u onder aan uw logische app de optie **Nieuwe stap** > **Een actie toevoegen**.

1. Voer in het zoekvak 'voorwaarde' in als filter. Selecteer deze actie: **Voorwaarde - Besturingselement**

   ![Voorwaarde toevoegen](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Bouw in de vak **Conditie** je conditie op. 

   1. Geef in het linkervak de gegevens of het veld op dat u wilt vergelijken.

      Wanneer u in het linkervak klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u uitvoer uit eerdere stappen in uw logische app selecteren. 
      Selecteer in dit voorbeeld het RSS-feedoverzicht.

      ![Bouw je conditie op](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Selecteer in het middelste vak de bewerking die u wilt uitvoeren. 
   Selecteer in dit voorbeeld "**bevat**". 

   1. Geef in het rechtervak een waarde of veld op als criteria. 
   Geef in dit voorbeeld deze tekenreeks op: **Microsoft**

   Hier is de volledige voorwaarde:

   ![Voltooide voorwaarde](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Als u een andere rij aan uw voorwaarde wilt toevoegen, kiest u**Rij** **toevoegen** > . 
   Als u een groep met subvoorwaarden wilt toevoegen, **kiest** > u**Groep Toevoegen**. 
   Als u bestaande rijen wilt groeperen, schakelt u de selectievakjes voor die rijen in, kiest u de knop ellipsen (...) voor een rij en kiest u **Groep maken**.

1. Voeg **onder Als waar** en Als dit niet waar **is,** de stappen toe die moeten worden uitgevoerd op basis van de vraag of aan de voorwaarde is voldaan. Bijvoorbeeld:

   ![Voorwaarde met "Als waar" en "Als vals" paden](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > U bestaande acties naar de **ware en** **als valse** paden slepen.

1. Sla uw logische app op.

Deze logische app verzendt nu alleen e-mail wanneer de nieuwe items in de RSS-feed aan uw voorwaarde voldoen.

## <a name="json-definition"></a>JSON-definitie

Hier is de codedefinitie op hoog niveau achter een voorwaardelijke instructie:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Ga naar de [feedbacksite](https://aka.ms/logicapps-wish)van Azure Logic Apps om functies en suggesties in te dienen of te stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van verschillende waarden (schakelinstructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Parallelle stappen uitvoeren of samenvoegen (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Stappen uitvoeren op basis van gegroepeerde actiestatus (scopes)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
