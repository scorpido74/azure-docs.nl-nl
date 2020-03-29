---
title: Schakelinstructies toevoegen aan werkstromen
description: Schakelinstructies maken die werkstroomacties beheren op basis van specifieke waarden in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 5c40feec2dca65e4bc9617a71a6d0a8e4c872a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793233"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Schakelinstructies maken die werkstroomacties uitvoeren op basis van specifieke waarden in Azure Logic Apps

Als u specifieke acties wilt uitvoeren op basis van de waarden van objecten, expressies of tokens, voegt u een *switchinstructie* toe. Deze structuur evalueert het object, de expressie of het token, kiest de case die overeenkomt met het resultaat en voert alleen specifieke acties uit voor die case. Wanneer de switchinstructie wordt uitgevoerd, moet slechts één geval overeenkomen met het resultaat.

Stel dat u een logische app wilt die verschillende stappen neemt op basis van een optie die in e-mail is geselecteerd. In dit voorbeeld controleert de logische app de RSS-feed van een website op nieuwe inhoud. Wanneer een nieuw item wordt weergegeven in de RSS-feed, stuurt de logische app e-mail naar een goedkeurder. Op basis van de vraag of de keurgever 'Goedkeuren' of 'Weigeren' selecteert, volgt de logische app verschillende stappen.

> [!TIP]
> Net als alle programmeertalen ondersteunen switchstatements alleen gelijkheidsoperatoren. Als u andere relationele operatoren nodig hebt, zoals "groter dan", gebruikt u een [voorwaardelijke instructie](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Om deterministisch uitvoeringsgedrag te garanderen, moeten aanvragen een unieke en statische waarde bevatten in plaats van dynamische tokens of expressies.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Als u het voorbeeld in dit artikel wilt volgen, [maakt u deze voorbeeldlogica-app](../logic-apps/quickstart-create-first-logic-app-workflow.md) met een Outlook.com- of Office 365 Outlook-account.

  1. Wanneer u de actie toevoegt om e-mail te verzenden, zoekt en selecteert u deze actie in plaats daarvan: **Een goedkeuringse-mail verzenden**

     ![Selecteer 'Een goedkeuringse-mail verzenden'](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Geef de vereiste velden op, zoals het e-mailadres voor de persoon die de goedkeuringse-mail krijgt. 
  Voer **onder Gebruikersopties**'Goedkeuren, afwijzen' in.

     ![E-mailgegevens invoeren](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Switch-instructie toevoegen

1. Voeg in dit voorbeeld een switchinstructie toe aan het einde van uw voorbeeldwerkstroom. Kies Na de laatste stap De optie **Nieuwe stap**.

   Wanneer u een switchinstructie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl waar u de switchinstructie wilt toevoegen. Kies het **plusteken** (**+**) dat wordt weergegeven en kies **vervolgens Een actie toevoegen**.

1. Voer in het zoekvak 'switch' in als filter. Selecteer deze actie: **Switch - Control**

   ![Schakelaar toevoegen](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Er verschijnt een switch-instructie met één aanvraag en een standaardaanvraag. 
   Standaard vereist een switch-instructie ten minste één case plus de standaardcase. 

   ![Standaardswitchinstructie leeg maken](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Klik in het vak **Aan,** zodat de lijst met dynamische inhoud wordt weergegeven. Selecteer in die lijst het veld **SelectedOption** waarvan de uitvoer de uit te voeren actie bepaalt. 

   ![Selecteer 'SelectedOption'](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Als u de gevallen wilt `Approve` afhandelen waarin de goedkeurder een andere aanvraag selecteert of `Reject`toevoegt tussen **Aanvraag** en **Standaard**. 

   ![Een ander aanvraag toevoegen](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Voeg deze acties toe aan de bijbehorende aanvragen:

   | Geval # | **Geselecteerde optie** | Actie |
   |--------|--------------------|--------|
   | Zaak 1 | **Goedkeuren** | Voeg de **outlook-actie Voor** het verzenden van gegevens over het RSS-item alleen toe als de goedgekeurde goedkeuring heeft geselecteerd **Goedkeuren**. |
   | Zaak 2 | **Afwijzen** | Voeg de actie Outlook **Toe Stuur een e-mailactie** toe om andere fiatteurs te laten weten dat het RSS-item is afgewezen. |
   | Standaard | Geen | Geen actie nodig. In dit voorbeeld is de **standaardaanvraag** leeg omdat **SelectedOption** slechts twee opties heeft. |
   |||

   ![Voltooide switchinstructie](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Sla uw logische app op. 

   Als u dit voorbeeld handmatig wilt testen, kiest u **Uitvoeren** totdat de logische app een nieuw RSS-item heeft gevonden en een goedkeuringse-mail verzendt. 
   Selecteer **Goedkeuren** om de resultaten te observeren.

## <a name="json-definition"></a>JSON-definitie

Nu u een logische app hebt gemaakt met behulp van een switch-instructie, kijken we naar de codedefinitie op hoog niveau achter de switch-instructie.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Label | Beschrijving |
|-------|-------------|
| `"Switch"`         | De naam van de switch-instructie, die u hernoemen voor leesbaarheid |
| `"type": "Switch"` | Hiermee geeft u op dat de actie een switch-instructie is |
| `"expression"`     | Hiermee geeft u de geselecteerde optie van de keurgever op die voor elk geval wordt geëvalueerd, zoals later in de definitie wordt aangegeven |
| `"cases"` | Hiermee definieert u een willekeurig aantal gevallen. Voor elk `"Case_*"` geval is dit de standaardnaam voor die aanvraag, waarmee u de naam voor leesbaarheid wijzigen |
| `"case"` | Hiermee geeft u de waarde van de aanvraag op, die een constante en unieke waarde moet zijn die de switchinstructie gebruikt voor vergelijking. Als er geen aanvragen overeenkomen met het `"default"` resultaat van de switchexpressie, worden de acties in de sectie uitgevoerd. | 
| | | 

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Ga naar de [feedbacksite](https://aka.ms/logicapps-wish)van Azure Logic Apps om functies of suggesties in te dienen of te stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voorwaarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Parallelle stappen uitvoeren of samenvoegen (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Stappen uitvoeren op basis van gegroepeerde actiestatus (scopes)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
