---
title: Codefragmenten toevoegen en uitvoeren met inlinecode
description: Meer informatie over het maken en uitvoeren van codefragmenten met behulp van inlinecodeacties voor geautomatiseerde taken en werkstromen die u maakt met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453501"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Codefragmenten toevoegen en uitvoeren met inlinecode in Azure Logic Apps

Wanneer u een stukje code in uw logische app wilt uitvoeren, u de ingebouwde **inlinecodeactie** toevoegen als een stap in de werkstroom van uw logische app. Deze actie werkt het beste wanneer u code wilt uitvoeren die in dit scenario past:

* Wordt uitgevoerd in JavaScript. Meer talen binnenkort.
* Eindigt in vijf seconden of minder.
* Hiermee verwerkt u gegevens tot 50 MB groot.
* Vereist niet dat u werkt met de acties [ **Variabelen,** ](../logic-apps/logic-apps-create-variables-store-values.md)die nog niet worden ondersteund.
* Gebruikt Node.js versie 8.11.1. Zie [Standaard ingebouwde objecten voor](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)meer informatie. 

  > [!NOTE]
  > De `require()` functie wordt niet ondersteund door de actie **Inline Code** voor het uitvoeren van JavaScript.

Met deze actie wordt het codefragment uitgevoerd en wordt de uitvoer van dat fragment geretourneerd als een token met de naam **Resultaat**, die u gebruiken in volgende acties in uw logische app. Voor andere scenario's waarin u een functie voor uw code wilt maken, probeert u [een Azure-functie te maken en aan te roepen](../logic-apps/logic-apps-azure-functions.md) in uw logische app.

In dit artikel wordt de voorbeeldlogica-app geactiveerd wanneer een nieuwe e-mail wordt binnenkomt in een Office 365 Outlook-account. Het codefragment haalt en retourneert alle e-mailadressen die in de e-mailtekst worden weergegeven.

![Voorbeeldoverzicht](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app waarin u uw codefragment wilt toevoegen, inclusief een trigger. Zie [Snelstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)als u geen logische app hebt.

   De voorbeeldlogische app in dit onderwerp gebruikt deze Office 365 Outlook-trigger: **wanneer een nieuwe e-mail binnenkomt**

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw logische app

  > [!NOTE]
  > Zorg ervoor dat u een integratieaccount gebruikt dat geschikt is voor uw use case of scenario. [Integratieaccounts op vrije laag](../logic-apps/logic-apps-pricing.md#integration-accounts) zijn bijvoorbeeld alleen bedoeld voor verkennende scenario's en workloads, niet productiescenario's, zijn beperkt in gebruik en doorvoer en worden niet ondersteund door een SERVICE-Level Agreement (SLA). Andere lagen maken kosten met zich mee, maar omvatten SLA-ondersteuning, bieden meer doorvoer en hebben hogere limieten. Meer informatie over [integratieaccountlagen](../logic-apps/logic-apps-pricing.md#integration-accounts), [prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)en [limieten](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="add-inline-code"></a>Inline-code toevoegen

1. Als u dat nog niet hebt gedaan, opent u in de [Azure-portal](https://portal.azure.com)uw logische app in de Logic App Designer.

1. Voeg in de ontwerper de actie **Inline Code** toe op de gewenste locatie in de werkstroom van uw logische app.

   * Als u de actie aan het einde van uw werkstroom wilt toevoegen, kiest u **Nieuwe stap**.

   * Als u de actie tussen bestaande stappen wilt toevoegen, verplaatst u de muisaanwijzer over de pijl die deze stappen verbindt. Kies het plusteken (**+**) en selecteer Een actie **toevoegen**.

   In dit voorbeeld wordt de actie **Inline Code** toegevoegd onder de Office 365 Outlook-trigger.

   ![Nieuwe stap toevoegen](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Voer **onder Een actie kiezen**in het zoekvak 'inlinecode' in als filter. Selecteer deze actie in de lijst met acties: **JavaScript-code uitvoeren**

   ![Selecteer 'JavaScript-code uitvoeren'](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   De actie wordt weergegeven in de ontwerper en bevat een standaardvoorbeeldcode, waaronder een retourinstructie.

   ![Inline-code actie met standaard voorbeeldcode](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Verwijder in het vak **Code** de voorbeeldcode en voer de code in die u wilt uitvoeren. Schrijf code die u in een methode zou plaatsen, maar zonder de handtekening van de methode te definiëren. 

   Wanneer u een herkend trefwoord typt, wordt de lijst automatisch aanvullen weergegeven, zodat u kiezen uit beschikbare zoekwoorden, bijvoorbeeld:

   ![Lijst met trefwoord automatisch aanvullen](./media/logic-apps-add-run-inline-code/auto-complete.png)

   In dit voorbeeldcodefragment wordt eerst een variabele gemaakt die een *reguliere expressie*opslaat, die een patroon opgeeft dat overeenkomt met invoertekst. De code maakt vervolgens een variabele die de e-mailhoofdgegevens van de trigger opslaat.

   ![Variabelen maken](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Als u de resultaten van de trigger en eerdere acties gemakkelijker wilt verwijzen, wordt de lijst met dynamische inhoud weergegeven terwijl de cursor zich in het vak **Code** bevindt. In dit voorbeeld worden in de lijst beschikbare resultaten van de trigger weergegeven, waaronder het **bodytoken,** dat u nu selecteren.

   Nadat u het **teken van hoofdtekst hebt** geselecteerd, `workflowContext` wordt het token met `Body` inlinecode opgelost naar een object dat verwijst naar de eigenschapswaarde van de e-mail:

   ![Resultaat selecteren](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   In het vak **Code** kan het fragment `workflowContext` het alleen-lezen object als invoer gebruiken. Dit object heeft subeigenschappen die uw code toegang geven tot de resultaten van de trigger en eerdere acties in uw werkstroom.
   Zie deze sectie later in dit onderwerp voor meer informatie: [De resultaten van de naslagcampagne en actie in uw code](#workflowcontext).

   > [!NOTE]
   >
   > Als uw codefragment verwijst naar actienamen die de operator punt (.) gebruiken, moet u deze actienamen toevoegen aan de parameter [ **Acties** ](#add-parameters). Deze verwijzingen moeten ook de actienamen bijsluiten met vierkante haakjes ([]) en aanhalingstekens, bijvoorbeeld:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   De inline codeactie vereist `return` geen instructie, maar `return` de resultaten van een instructie zijn beschikbaar voor referentie in latere acties via het **resultaattoken.** 
   Het codefragment retourneert bijvoorbeeld het `match()` resultaat door de functie aan te roepen, die overeenkomsten in de e-mailtekst vindt met de reguliere expressie. De actie **Samenstellen** gebruikt het **resultaattoken** om te verwijzen naar de resultaten van de inlinecodeactie en maakt één resultaat.

   ![Voltooide logische app](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Wanneer u klaar bent, slaat u uw logica-app op.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Referentietrigger- en actieresultaten in uw code

Het `workflowContext` object heeft deze structuur, `trigger`die `workflow` de `actions`, , en subeigenschappen omvat:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Deze tabel bevat meer informatie over deze subeigenschappen:

| Eigenschap | Type | Beschrijving |
|----------|------|-------|
| `actions` | Objectverzameling | Resultaatobjecten uit acties die worden uitgevoerd voordat het codefragment wordt uitgevoerd. Elk object heeft een *sleutelwaardepaar* waarbij de sleutel de naam van een actie is en `@actions('<action-name>')`de waarde gelijk is aan het aanroepen van de [functie actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions) met . De naam van de actie gebruikt dezelfde actienaam die wordt gebruikt in de onderliggende werkstroomdefinitie, die spaties (" ") in de actienaam vervangt door underscores (_). Dit object biedt toegang tot waarden van actieeigenschap van de huidige werkstroominstantie uitvoeren. |
| `trigger` | Object | Resultaatobject van de trigger en gelijk aan het aanroepen van de [functie trigger().](../logic-apps/workflow-definition-language-functions-reference.md#trigger) Dit object biedt toegang tot eigenschapswaarden van de huidige werkstroominstantie. |
| `workflow` | Object | Het werkstroomobject en het equivalent van het aanroepen van de [functie workflow().](../logic-apps/workflow-definition-language-functions-reference.md#workflow) Dit object biedt toegang tot waarden voor werkstroomeigenschap, zoals de werkstroomnaam, voer-ID enzovoort uit de huidige werkstroominstantie uitvoeren. |
|||

In het voorbeeld van `workflowContext` dit onderwerp heeft het object de volgende eigenschappen waartoe uw code toegang heeft:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Parameters toevoegen

In sommige gevallen moet u expliciet vereisen dat de actie **Inline Code** resultaten bevat van de trigger of specifieke acties die uw code verwijst als afhankelijkheden door de parameters **Trigger** of Actions toe **te** voegen. Deze optie is handig voor scenario's waarin de resultaten waarnaar wordt verwezen niet worden gevonden tijdens de looptijd.

> [!TIP]
> Als u van plan bent uw code opnieuw te gebruiken, voegt u verwijzingen naar eigenschappen toe met het vak **Code,** zodat uw code de opgeloste tokenverwijzingen bevat, in plaats van de trigger of acties toe te voegen als expliciete afhankelijkheden.

Stel dat u code hebt die verwijst naar het resultaat van de **SelectedOption** uit de actie **Goedkeuringse-mail verzenden** voor de Office 365 Outlook-connector. Op het moment van het maken analyseert de Logic Apps-engine uw code om te bepalen of u naar een trigger of actieresultaten hebt verwezen en deze resultaten automatisch op te nemen. Als u bij het uitvoeren van de looptijd een foutmelding krijgt dat `workflowContext` de trigger of actieresultaat niet beschikbaar is in het opgegeven object, u die trigger of actie toevoegen als een expliciete afhankelijkheid. In dit voorbeeld voegt u de parameter **Acties** toe en geeft u op dat de actie **Inline-code** expliciet het resultaat van de **actie E-mail verzenden** bevat.

Als u deze parameters wilt toevoegen, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de gewenste parameters:

   ![Parameters toevoegen](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parameter | Beschrijving |
   |-----------|-------------|
   | **Acties** | Resultaten van eerdere acties opnemen. Zie [Actieresultaten opnemen](#action-results). |
   | **Trigger** | Resultaten van de trigger opnemen. Zie [Triggerresults opnemen](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Triggerresultaten opnemen

Als u **Triggers**selecteert, wordt u gevraagd of u triggerresultaten wilt opnemen.

* Selecteer **Trigger** **Ja**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Actieresultaten opnemen

Als u **Acties**selecteert, wordt u gevraagd naar de acties die u wilt toevoegen. Voordat u echter acties gaat toevoegen, hebt u de versie van de actienaam nodig die wordt weergegeven in de onderliggende werkstroomdefinitie van de logische app.

* Deze mogelijkheid ondersteunt geen variabelen, lussen en iteratie-indexen.

* Namen in de werkstroomdefinitie van uw logica-app gebruiken een underscore (_), geen spatie.

* Voor actienamen die gebruik maken van de dot operator (.), omvatten deze operatoren, bijvoorbeeld:

  `My.Action.Name`

1. Kies op de werkbalk Van de ontwerper `actions` **de codeweergave**en zoek in het kenmerk naar de actienaam.

   Is bijvoorbeeld `Send_approval_email_` de JSON-naam voor de **actie Goedkeuring-e-mail verzenden.**

   ![Actienaam zoeken in JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Als u wilt terugkeren naar de ontwerpweergave, kiest u op de werkbalk codeweergave de optie **Ontwerper**.

1. Als u de eerste actie wilt toevoegen, voert u in het vak **Actieitem - 1** de JSON-naam van de actie in.

   ![Eerste actie invoeren](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Als u een andere actie wilt toevoegen, kiest u **Nieuw item toevoegen**.

## <a name="reference"></a>Naslaginformatie

Zie de naslagdeel van de actie JavaScript-code uitvoeren voor meer informatie over de structuur en syntaxis van de actie **JavaScript-code uitvoeren** in de onderliggende werkstroomdefinitiedefinitie van uw logische app met behulp van de [verwijzingssectie Werkstroomdefinitie.](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Connectors voor Azure Logic Apps](../connectors/apis-list.md)
