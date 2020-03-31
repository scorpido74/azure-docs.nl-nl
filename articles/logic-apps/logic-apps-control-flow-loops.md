---
title: Lussen toevoegen aan herhaalde acties
description: Herhalingen maken waarmee werkstroomacties worden herhaald of matrices worden verwerkt in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270574"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Herhalingen maken waarmee werkstroomacties worden herhaald of matrices worden verwerkt in Azure Logic Apps

Als u een array wilt verwerken in uw logische app, u een [lus 'Foreach' maken.](#foreach-loop) Met deze lus worden een of meer acties op elk item in de array herhaald. Zie [Limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md)voor beperkingen op het aantal matrixitems dat 'Foreach'-lussen kunnen verwerken. 

Als u acties wilt herhalen totdat aan een voorwaarde wordt voldaan of een status wordt gewijzigd, u een [lus 'Tot' maken.](#until-loop) Uw logica-app voert eerst alle acties binnen de lus uit en controleert vervolgens de voorwaarde of status. Als aan de voorwaarde is voldaan, stopt de lus. Anders wordt de lus herhaald. Zie [Limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md)voor beperkingen op het aantal "Until"-lussen in een logische app. 

> [!TIP]
> Als u een trigger hebt die een array ontvangt en een werkstroom wilt uitvoeren voor elk arrayitem, u die array *debatchen* met de eigenschap [ **SplitOn** trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" lus

Een "Foreach loop" herhaalt een of meer acties op elk arrayitem en werkt alleen op arrays. Iteraties in een "Foreach" lus lopen parallel. U iteraties echter één voor één uitvoeren door een [sequentiële Foreach-lus](#sequential-foreach-loop)in te stellen. 

Hier volgen enkele overwegingen wanneer u "Foreach"-lussen gebruikt:

* In geneste lussen worden iteraties altijd opeenvolgend uitgevoerd, niet parallel. Als u bewerkingen parallel wilt uitvoeren voor items in een geneste lus, maakt en [roept u een app voor onderliggende logica aan](../logic-apps/logic-apps-http-endpoint.md).

* Voer deze lussen opeenvolgend uit om voorspelbare resultaten te krijgen van bewerkingen op variabelen tijdens elke lusiteratie. Wanneer een gelijktijdig lopende lus eindigt, geven de toename, decrement en de aanvulling op variabele bewerkingen bijvoorbeeld voorspelbare resultaten. Tijdens elke iteratie in de gelijktijdig lopende lus kunnen deze bewerkingen echter onvoorspelbare resultaten opleveren. 

* Acties in een "Foreach"-lus gebruiken de[`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
expressie om naar elk item in de array te verwijzen en te verwerken. Als u gegevens opgeeft die niet in een array zijn, mislukt de werkstroom van de logische app. 

Deze voorbeeldlogica-app verzendt een dagelijkse samenvatting voor een RSS-feed van de website. De app maakt gebruik van een "Foreach" lus die een e-mail stuurt voor elk nieuw item.

1. [Maak deze voorbeeldlogica-app](../logic-apps/quickstart-create-first-logic-app-workflow.md) met een Outlook.com- of Office 365 Outlook-account.

2. Voeg tussen de RSS-trigger en stuur e-mailactie een "Foreach"-lus toe. 

   1. Als u een lus tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen deze stappen. 
   Kies het **plusteken** (**+**) dat wordt weergegeven en selecteer **Vervolgens Een actie toevoegen**.

      ![Selecteer 'Een actie toevoegen'](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Kies **Alle** onder het zoekvak. Typ 'voor elk' in het zoekvak als filter. Selecteer in de lijst met acties deze actie: **Voor elk - besturingselement**

      ![Lus 'Voor elke' lus toevoegen](media/logic-apps-control-flow-loops/select-for-each.png)

3. Bouw nu de lus. Selecteer **onder Een uitvoer uit eerdere stappen selecteren** nadat de lijst Dynamische inhoud **toevoegen** is weergegeven, de array **Feedkoppelingen,** die wordt uitgevoerd vanaf de RSS-trigger. 

   ![Kiezen uit de lijst met dynamische inhoud](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > U *alleen* array-uitgangen uit de vorige stap selecteren.

   De geselecteerde array wordt nu hier weergegeven:

   ![Array selecteren](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Als u een actie voor elk arrayitem wilt uitvoeren, sleept u de actie **Een e-mail verzenden** naar de lus. 

   Uw logica-app ziet er mogelijk ongeveer zo uit als dit voorbeeld:

   ![Stappen toevoegen aan de lus 'Foreach'.](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Sla uw logische app op. Als u uw logische app handmatig wilt testen, kiest u op de werkbalk van de ontwerper de optie **Uitvoeren**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" lusdefinitie (JSON)

Als u in de codeweergave voor uw logische `Foreach` app werkt, u de lus in de JSON-definitie van uw logische app definiëren, bijvoorbeeld:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" lus: Sequentieel

Standaard worden cycli in een lus "Foreach" parallel uitgevoerd. Als u elke cyclus opeenvolgend wilt uitvoeren, stelt u de **optie Sequentieel van** de lus in. "Foreach"-lussen moeten opeenvolgend worden uitgevoerd wanneer u geneste lussen of variabelen in lussen hebt waar u voorspelbare resultaten verwacht. 

1. Kies in de rechterbovenhoek van de lus **ellipsen** (**...**) > **Instellingen**.

   ![Kies op "Foreach" de optie "..." > "Instellingen"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Schakel **onder Gelijktijdigheidscontrole**de instelling **Gelijktijdigheidscontrole** in **op Aan**. Verplaats de schuifregelaar **Mate van parallelisme** naar **1**en kies **Gereed**.

   ![Gelijktijdigheidscontrole inschakelen](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Als u werkt met de JSON-definitie van uw `Sequential` logische app, `operationOptions` u de optie gebruiken door de parameter toe te voegen, bijvoorbeeld:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Tot" lus
  
Als u acties wilt uitvoeren en herhalen totdat aan een voorwaarde wordt voldaan of een status wordt gewijzigd, plaatst u deze acties in een lus 'Tot'. Uw logische app voert eerst alle acties binnen de lus uit en controleert vervolgens de voorwaarde of status. Als aan de voorwaarde is voldaan, stopt de lus. Anders wordt de lus herhaald.

Hier volgen enkele veelvoorkomende scenario's waarin u een lus 'Tot' gebruiken:

* Bel een eindpunt totdat u het gewenste antwoord krijgt.

* Een record maken in een database. Wacht tot een bepaald veld in die record wordt goedgekeurd. Doorgaan met verwerken. 

Vanaf elke dag om 08:00 uur wordt in deze voorbeeldlogica-app een variabele verhoogd totdat de waarde van de variabele gelijk is aan 10. De logische app stuurt vervolgens een e-mail die de huidige waarde bevestigt. 

> [!NOTE]
> In deze stappen wordt Office 365 Outlook gebruikt, maar u elke e-mailprovider gebruiken die Logic Apps ondersteunt. 
> [Bekijk hier de lijst met connectoren.](https://docs.microsoft.com/connectors/) Als u een ander e-mailaccount gebruikt, blijven de algemene stappen hetzelfde, maar uw gebruikersinterface ziet er mogelijk iets anders uit. 

1. Een lege, logische app maken. Kies in Logic App Designer onder het zoekvak **Alles**. Zoek naar "herhaling". 
   Selecteer deze trigger in de lijst triggers: **Herhaling - Plannen**

   ![Trigger 'Herhaling - schema' toevoegen](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Geef op wanneer de trigger wordt geactiveerd door het interval, de frequentie en het uur van de dag in te stellen. Als u het uur wilt instellen, kiest **u Geavanceerde opties weergeven**.

   ![Herhalingsschema instellen](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Eigenschap | Waarde |
   | -------- | ----- |
   | **Interval** | 1 | 
   | **Frequentie** | Dag |
   | **Deze uren** | 8 |
   ||| 

1. Kies Onder de trigger De optie **Nieuwe stap**. 
   Zoeken naar 'variabelen' en selecteer deze actie: **Initialiseren variabele - Variabelen**

   ![Actie 'Variabele initialiseren - Variabelen' toevoegen](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Stel uw variabele in met de volgende waarden:

   ![Variabele eigenschappen instellen](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Eigenschap | Waarde | Beschrijving |
   | -------- | ----- | ----------- |
   | **Naam** | Limiet | De naam van uw variabele | 
   | **Type** | Geheel getal | Het gegevenstype van uw variabele | 
   | **Waarde** | 0 | De startwaarde van uw variabele | 
   |||| 

1. Kies onder de variabele actie **Initialiseren** de optie **Nieuwe stap**. 

1. Kies **Alle** onder het zoekvak. Zoek naar "tot", en selecteer deze actie: **Tot - Control**

   ![Lus 'Tot' toevoegen](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Bouw de afgeslotenvoorwaarde van de lus op door de variabele **Limiet** en de **gelijkte** operator te selecteren. 
   Voer **10** in als vergelijkingswaarde.

   ![De uitgangsvoorwaarde voor het tegenhouden van lijn bouwen](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Kies **Een actie toevoegen**in de lus. 

1. Kies **Alle** onder het zoekvak. Zoeken naar 'variabelen' en selecteer deze actie: **Increment variabele - Variabelen**

   ![Actie toevoegen voor het verhogen van variabele](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Selecteer **bij Naam**de variabele **Limiet.** Voer **voor waarde**"1" in. 

     ![Verhoging "Limiet" met 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Buiten en onder de lus kiest u **Nieuwe stap**. 

1. Kies **Alle** onder het zoekvak. 
     Zoek en voeg een actie toe die e-mail verzendt, bijvoorbeeld: 

     ![Actie toevoegen die e-mail verzendt](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Meld u aan bij uw e-mailaccount als dat wordt gevraagd.

1. Stel de eigenschappen van de e-mailactie in. Voeg de variabele **Limiet** toe aan het onderwerp. Op die manier u bevestigen dat de huidige waarde van de variabele voldoet aan de opgegeven voorwaarde, bijvoorbeeld:

      ![E-maileigenschappen instellen](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Eigenschap | Waarde | Beschrijving |
      | -------- | ----- | ----------- | 
      | **Aan** | *\<e-mailadres\@domein>* | Het e-mailadres van de ontvanger. Gebruik voor het testen uw eigen e-mailadres. | 
      | **Onderwerp** | Huidige waarde voor 'Limiet' is **Limiet** | Geef het e-mailonderwerp op. Zorg er in dit voorbeeld voor dat u de variabele **Limiet** opneemt. | 
      | **Hoofdtekst** | <*e-mailinhoud*> | Geef de inhoud van het e-mailbericht op die u wilt verzenden. Voer in dit voorbeeld de tekst in die u wilt. | 
      |||| 

1. Sla uw logische app op. Als u uw logische app handmatig wilt testen, kiest u op de werkbalk van de ontwerper de optie **Uitvoeren**.

      Nadat uw logica is gestart, krijgt u een e-mail met de inhoud die u hebt opgegeven:

      ![Ontvangen e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Voorkom eindeloze lussen

Een lus 'Tot' heeft standaardlimieten die de uitvoering stoppen als een van deze voorwaarden zich voordevolgende voorwaarden voorkomt:

| Eigenschap | Standaardwaarde | Beschrijving | 
| -------- | ------------- | ----------- | 
| **Count** | 60 | Het hoogste aantal lussen dat wordt uitgevoerd voordat de lus wordt afgesloten. De standaardinstelling is 60 cycli. | 
| **Timeout** | PT1H | De meeste tijd om een lus uit te voeren voordat de lus wordt afgesloten. De standaardinstelling is een uur en is opgegeven in de ISO 8601-indeling. <p>De time-outwaarde wordt voor elke luscyclus geëvalueerd. Als een actie in de lus langer duurt dan de time-outlimiet, stopt de huidige cyclus niet. De volgende cyclus wordt echter niet gestart omdat niet aan de limietvoorwaarde is voldaan. | 
|||| 

Als u deze standaardlimieten wilt wijzigen, kiest **u Geavanceerde opties weergeven** in de vorm van de lusactie.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definitie "Tot" (JSON)

Als u in de codeweergave voor uw logische `Until` app werkt, u in plaats daarvan een lus definiëren in de JSON-definitie van uw logische app, bijvoorbeeld:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

In dit voorbeeld 'Tot' wordt een HTTP-eindpunt aangemaakt, waarmee een resource wordt gemaakt. De lus stopt wanneer de `Completed` HTTP-antwoordinstantie met status terugkeert. Om eindeloze lussen te voorkomen, stopt de lus ook als een van deze voorwaarden zich voordoet:

* De lus liep 10 keer `count` zoals opgegeven door het kenmerk. De standaardinstelling is 60 keer. 

* De lus liep gedurende twee `timeout` uur zoals opgegeven door het kenmerk in de ISO 8601-indeling. De standaardinstelling is een uur.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Om functies en suggesties in te dienen of te stemmen, [u de feedbacksite van Azure Logic Apps indienen](https://aka.ms/logicapps-wish)of stemmen.

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voorwaarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren op basis van verschillende waarden (schakelinstructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Parallelle stappen uitvoeren of samenvoegen (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Stappen uitvoeren op basis van gegroepeerde actiestatus (scopes)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
