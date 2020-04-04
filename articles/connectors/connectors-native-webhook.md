---
title: Wachten en reageren op gebeurtenissen
description: Automatiseer werkstromen die activeren, onderbreken en hervatten op basis van gebeurtenissen op een serviceeindpunt met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656283"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Geautomatiseerde op gebeurtenissen gebaseerde werkstromen maken en uitvoeren met HTTP-webhooks in Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de ingebouwde HTTP Webhook-connector u werkstromen automatiseren die wachten en worden uitgevoerd op basis van specifieke gebeurtenissen die plaatsvinden op een HTTP- of HTTPS-eindpunt door logische apps te bouwen. U bijvoorbeeld een logische app maken die een serviceeindpunt bewaakt door te wachten op een specifieke gebeurtenis voordat u de werkstroom activeert en de opgegeven acties uitvoert, in plaats van dat eindpunt regelmatig te controleren of *te peilen.*

Hier volgen enkele voorbeeldwerkstromen op basis van gebeurtenissen:

* Wacht tot een item uit een [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) is gekomen voordat een logische app wordt uitgevoerd.
* Wacht op een goedkeuring voordat u een werkstroom voortzet.

## <a name="how-do-webhooks-work"></a>Hoe werken webhooks?

Een HTTP webhook trigger is event-based, wat niet afhankelijk is van het regelmatig controleren of peilen voor nieuwe items. Wanneer u een logische app opslaat die begint met een webhook-trigger of wanneer u uw logische app wijzigt van uitgeschakeld naar ingeschakeld, *abonneert* de webhooktrigger zich op een specifieke service of eindpunt door een *callback-URL* te registreren bij die service of eindpunt. De trigger wacht vervolgens tot die service of eindpunt de URL aanroept, waarmee de logische app wordt uitgevoerd. Net als bij de [trigger Van Aanvraag](connectors-native-reqres.md)wordt de logische app onmiddellijk geactiveerd wanneer de opgegeven gebeurtenis plaatsvindt. De trigger *meldt zich af* voor de service of het eindpunt als u de trigger verwijdert en uw logische app opslaat, of wanneer u uw logische app wijzigt van ingeschakeld naar uitgeschakeld.

Een HTTP-webhook-actie is ook op gebeurtenissen gebaseerd en *schrijft zich op* een specifieke service of eindpunt door een *terugbel-URL* te registreren bij die service of eindpunt. De webhook-actie pauzeert de werkstroom van de logische app en wacht totdat de service of het eindpunt de URL aanroept voordat de logische app wordt hervat. De app actielogica *meldt zich* in deze gevallen af voor de service of het eindpunt:

* Wanneer de webhook-actie is voltooid
* Als de logische app-run wordt geannuleerd in afwachting van een antwoord
* Voordat de logische app een tijd uitgaat

De [**e-mailactie**](connectors-create-api-office365-outlook.md) voor goedkeuring van de Office 365 Outlook-connector is bijvoorbeeld een voorbeeld van webhook-actie die dit patroon volgt. U dit patroon uitbreiden naar elke service met behulp van de webhook-actie.

> [!NOTE]
> Logic Apps dwingt TLS (Transport Layer Security) 1.2 af wanneer u de oproep terugontvangt naar de HTTP-webhooktrigger of -actie. Als u TLS-handshakefouten ziet, moet u TLS 1.2 gebruiken. Voor inkomende oproepen, hier zijn de ondersteunde cipher suites:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Zie deze onderwerpen voor meer informatie:

* [HTTP Webhook triggerparameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooks en abonnementen](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Aangepaste API's maken die een webhook ondersteunen](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De URL voor een al geïmplementeerd eindpunt of API die het webhook-abonnement en afmeldpatroon voor [webhook-triggers in logische apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) of [webhook-acties in logische apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) ondersteunt, naar gelang van het geval

* Basiskennis over [het maken van logische apps.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* De logische app waarin u wilt wachten op specifieke gebeurtenissen op het doeleindpunt. Als u wilt beginnen met de HTTP Webhook-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u de http-webhook-actie wilt gebruiken, start u uw logische app met de gewenste trigger. In dit voorbeeld wordt de HTTP-trigger als eerste stap gebruikt.

## <a name="add-an-http-webhook-trigger"></a>Een HTTP Webhook-trigger toevoegen

Deze ingebouwde trigger roept het abonnereneindpunt op de doelservice aan en registreert een callback-URL bij de doelservice. Uw logische app wacht vervolgens tot de `HTTP POST` doelservice een verzoek naar de terugbel-URL stuurt. Wanneer deze gebeurtenis plaatsvindt, wordt de trigger geactiveerd en worden alle gegevens in de aanvraag doorgegeven aan de werkstroom.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw lege logische app in Logic App Designer.

1. Voer in het zoekvak `http webhook` van de ontwerper in als filter. Selecteer in de lijst **Triggers** de **HTTP Webhook-trigger.**

   ![HTTP Webhook-trigger selecteren](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   In dit voorbeeld wordt `HTTP Webhook trigger` de naam van de trigger gewijzigd, zodat de stap een meer beschrijvende naam heeft. In het voorbeeld wordt later ook een HTTP Webhook-actie toegevoegd en moeten beide namen uniek zijn.

1. Geef de waarden op voor de [HTTP Webhook-triggerparameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) die u wilt gebruiken voor het abonneren en afmelden.

   In dit voorbeeld bevat de trigger de methoden, URI's en berichtinstanties die moeten worden gebruikt bij het uitvoeren van de bewerkingen abonneren en afmelden.

   ![Http Webhook-triggerparameters invoeren](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Abonnement - Methode** | Ja | De methode om te gebruiken bij het abonneren op het doeleindpunt |
   | **Inschrijven - URI** | Ja | De URL die u wilt gebruiken om u te abonneren op het doeleindpunt |
   | **Inschrijven - Lichaam** | Nee | Elke berichttekst die u wilt opnemen in het verzoek om u te abonneren. In dit voorbeeld wordt de url van de terugbelfunctie weergegeven die `@listCallbackUrl()` de abonnee, uw logische app, op unieke wijze identificeert door de expressie te gebruiken om de terugbel-URL van uw logische app op te halen. |
   | **Afmelden - Methode** | Nee | De methode om te gebruiken bij het afmelden van het doeleindpunt |
   | **Afmelden - URI** | Nee | De URL die moet worden gebruikt voor het afmelden van het doeleindpunt |
   | **Afmelden - Lichaam** | Nee | Een optionele berichttekst die u wilt opnemen in het afmeldverzoek <p><p>**Opmerking:** deze eigenschap ondersteunt geen `listCallbackUrl()` gebruik van de functie. De trigger bevat en verzendt echter automatisch `x-ms-client-tracking-id` `x-ms-workflow-operation-name`de headers en , die de doelservice kan gebruiken om de abonnee op unieke wijze te identificeren. |
   ||||

1. Als u andere triggereigenschappen wilt toevoegen, opent u de lijst **Nieuwe parameter toevoegen.**

   ![Meer triggereigenschappen toevoegen](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Als u bijvoorbeeld verificatie moet gebruiken, u de eigenschappen **Abonneren - Verificatie** en **Afmelden - Verificatie** toevoegen. Zie [Verificatie toevoegen aan uitgaande gesprekken](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over verificatietypen die beschikbaar zijn voor HTTP Webhook.

1. Ga door met het bouwen van de werkstroom van uw logische app met acties die worden uitgevoerd wanneer de trigger wordt geactiveerd.

1. Wanneer u klaar bent, klaar bent, moet u uw logische app opslaan. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

   Het opslaan van uw logische app roept het eindpunt van het abonnement op de doelservice op en registreert de terugbel-URL. Uw logische app wacht vervolgens tot de `HTTP POST` doelservice een verzoek naar de terugbel-URL stuurt. Wanneer deze gebeurtenis plaatsvindt, wordt de trigger geactiveerd en worden alle gegevens in de aanvraag doorgegeven aan de werkstroom. Als deze bewerking is voltooid, wordt de trigger afgemeld voor het eindpunt en wordt de resterende werkstroom voortgezet in de logische app.

## <a name="add-an-http-webhook-action"></a>Een HTTP Webhook-actie toevoegen

Deze ingebouwde actie roept het eindpunt voor het abonneren op de doelservice aan en registreert een callback-URL bij de doelservice. Uw logische app pauzeert en wacht vervolgens `HTTP POST` tot de doelservice een verzoek naar de terugbel-URL verzendt. Wanneer deze gebeurtenis plaatsvindt, geeft de actie alle gegevens in de aanvraag door aan de werkstroom. Als de bewerking is voltooid, wordt de actie afgemeld voor het eindpunt en wordt de resterende werkstroom dooruitgevoerd in de logica-app.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw logische app in Logic App Designer.

   In dit voorbeeld wordt de HTTP Webhook-trigger als eerste stap gebruikt.

1. Selecteer Onder de stap waarin u de actie HTTP Webhook wilt toevoegen, de optie **Nieuwe stap**.

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Voer in het zoekvak `http webhook` van de ontwerper in als filter. Selecteer in de lijst **Acties** de actie **HTTP Webhook.**

   ![Http Webhook-actie selecteren](./media/connectors-native-webhook/select-http-webhook-action.png)

   In dit voorbeeld wordt de naam van de actie gewijzigd in 'HTTP Webhook-actie' zodat de stap een meer beschrijvende naam heeft.

1. Geef de waarden op voor de actieparameters http Webhook, die vergelijkbaar zijn met de [HTTP Webhook-triggerparameters,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)die u wilt gebruiken voor het abonneren en afmelden.

   In dit voorbeeld bevat de actie de methoden, URI's en berichtinstanties die moeten worden gebruikt bij het uitvoeren van de bewerkingen voor abonneren en afmelden.

   ![Http Webhook-actieparameters invoeren](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Abonnement - Methode** | Ja | De methode om te gebruiken bij het abonneren op het doeleindpunt |
   | **Inschrijven - URI** | Ja | De URL die u wilt gebruiken om u te abonneren op het doeleindpunt |
   | **Inschrijven - Lichaam** | Nee | Elke berichttekst die u wilt opnemen in het verzoek om u te abonneren. In dit voorbeeld wordt de url van de terugbelfunctie weergegeven die `@listCallbackUrl()` de abonnee, uw logische app, op unieke wijze identificeert door de expressie te gebruiken om de terugbel-URL van uw logische app op te halen. |
   | **Afmelden - Methode** | Nee | De methode om te gebruiken bij het afmelden van het doeleindpunt |
   | **Afmelden - URI** | Nee | De URL die moet worden gebruikt voor het afmelden van het doeleindpunt |
   | **Afmelden - Lichaam** | Nee | Een optionele berichttekst die u wilt opnemen in het afmeldverzoek <p><p>**Opmerking:** deze eigenschap ondersteunt geen `listCallbackUrl()` gebruik van de functie. De actie bevat en verzendt echter automatisch `x-ms-client-tracking-id` `x-ms-workflow-operation-name`de headers en , die de doelservice kan gebruiken om de abonnee op unieke wijze te identificeren. |
   ||||

1. Als u andere actie-eigenschappen wilt toevoegen, opent u de lijst **Nieuwe parameter toevoegen.**

   ![Meer actie-eigenschappen toevoegen](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Als u bijvoorbeeld verificatie moet gebruiken, u de eigenschappen **Abonneren - Verificatie** en **Afmelden - Verificatie** toevoegen. Zie [Verificatie toevoegen aan uitgaande gesprekken](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over verificatietypen die beschikbaar zijn voor HTTP Webhook.

1. Wanneer u klaar bent, moet u uw logica-app opslaan. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

   Wanneer deze actie wordt uitgevoerd, roept uw logische app nu het eindpunt van abonneren op de doelservice aan en registreert u de terugbel-URL. De logische app pauzeert vervolgens de werkstroom en `HTTP POST` wacht tot de doelservice een verzoek naar de terugbel-URL verzendt. Wanneer deze gebeurtenis plaatsvindt, geeft de actie alle gegevens in de aanvraag door aan de werkstroom. Als de bewerking is voltooid, wordt de actie afgemeld voor het eindpunt en wordt de resterende werkstroom dooruitgevoerd in de logica-app.

## <a name="connector-reference"></a>Connector-verwijzing

Zie [HTTP Webhook-parameters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)voor meer informatie over trigger- en actieparameters die op elkaar lijken.

### <a name="output-details"></a>Uitvoerdetails

Hier vindt u meer informatie over de uitvoer van een HTTP Webhook trigger of actie, die deze informatie retourneert:

| Naam van eigenschap | Type | Beschrijving |
|---------------|------|-------------|
| Headers | object | De kopteksten van het verzoek |
| body | object | JSON-object | Het object met de inhoud van de hoofdtekst van het verzoek |
| statuscode | int | De statuscode van het verzoek |
|||

| Statuscode | Beschrijving |
|-------------|-------------|
| 200 | OK |
| 202 | Geaccepteerd |
| 400 | Slecht verzoek |
| 401 | Niet geautoriseerd |
| 403 | Verboden |
| 404 | Niet gevonden |
| 500 | Interne serverfout. Er is onbekende fout opgetreden. |
|||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
