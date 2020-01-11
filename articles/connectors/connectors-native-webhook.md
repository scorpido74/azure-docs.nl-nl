---
title: Wachten en reageren op gebeurtenissen
description: Werk stromen automatiseren die op basis van gebeurtenissen in een service-eind punt activeren, onderbreken en hervatten met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: 14daa37a414e814ed6de036bbb625933ce2532e0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888121"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Geautomatiseerde op gebeurtenis gebaseerde werk stromen maken en uitvoeren met behulp van HTTP-webhooks in Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de ingebouwde http-webhook-connector kunt u werk stromen automatiseren die wachten en worden uitgevoerd op basis van specifieke gebeurtenissen die plaatsvinden op een HTTP-of https-eind punt door Logic apps te bouwen. U kunt bijvoorbeeld een logische app maken die een service-eind punt bewaakt door te wachten op een specifieke gebeurtenis voordat de werk stroom wordt geactiveerd en de opgegeven acties worden uitgevoerd, in plaats van het regel matig controleren of *pollen* van dat eind punt.

Hier volgen enkele voor beelden van op gebeurtenissen gebaseerde werk stromen:

* Wacht totdat een item is ontvangen van een [Azure Event hub](https://github.com/logicappsio/EventHubAPI) voordat de uitvoering van een logische app wordt geactiveerd.
* Wacht op een goed keuring voordat u doorgaat met een werk stroom.

## <a name="how-do-webhooks-work"></a>Hoe werken webhooks?

Een HTTP-webhook-trigger is gebaseerd op gebeurtenissen. Dit is niet afhankelijk van het controleren of pollen regel matig voor nieuwe items. Wanneer u een logische app opslaat die begint met een webhook-trigger of wanneer u de logische app van *uitgeschakeld naar ingeschakeld* , wordt de webhook geactiveerd voor een specifieke service of een eind punt door een *call back-URL* met die service of dit eind punt te registreren. De trigger wacht vervolgens op die service of dit eind punt om de URL aan te roepen, waardoor de logische app wordt uitgevoerd. Net als bij de [aanvraag trigger](connectors-native-reqres.md)wordt de logische app onmiddellijk geactiveerd wanneer de opgegeven gebeurtenis plaatsvindt. De trigger wordt *afgemeld* bij de service of het eind punt als u de trigger verwijdert en uw logische app opslaat, of wanneer u de logische app wijzigt van ingeschakeld in uitgeschakeld.

Een HTTP-webhook-actie is ook op gebeurtenis gebaseerd en *abonneert* zich op een specifieke service of eind punt door een *call back-URL* te registreren bij die service of dit eind punt. De webhook-actie pauzeert de werk stroom van de logische app en wacht totdat de service of het eind punt de URL aanroept voordat de logische app wordt hervat. De actie logica-app heeft *zich afgemeld* bij de service of het eind punt in de volgende gevallen:

* Wanneer de webhook-actie is voltooid
* Als de uitvoering van de logische app wordt geannuleerd tijdens het wachten op een reactie
* Voordat er een time-out optreedt voor de logische app

Zo is de actie [**e-mail bericht verzenden goed keuren**](connectors-create-api-office365-outlook.md) van Office 365 Outlook Connector een voor beeld van een webhook-actie die volgt op dit patroon. U kunt dit patroon uitbreiden in een service met behulp van de webhook-actie.

> [!NOTE]
> Logic Apps afdwingt Transport Layer Security (TLS) 1,2 wanneer de aanroep terugkeert naar de HTTP-webhook-trigger of-actie. Als er SSL-Handshake-fouten worden weer geven, moet u ervoor zorgen dat u TLS 1,2 gebruikt.

Zie deze onderwerpen voor meer informatie:

* [Trigger parameters voor HTTP-webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooks en abonnementen](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Aangepaste Api's maken die ondersteuning bieden voor een webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De URL voor een al geïmplementeerd eind punt of API die ondersteuning biedt voor het abonneren en abonneren van webhooks voor [webhook-triggers in Logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) of [webhook-acties in Logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) , indien van toepassing

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

* De logische app waar u wilt wachten op specifieke gebeurtenissen op het doel eindpunt. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met de http-webhook-trigger. Als u de actie HTTP-webhook wilt gebruiken, start u uw logische app met een wille keurige trigger. In dit voor beeld wordt de HTTP-trigger als eerste stap gebruikt.

## <a name="add-an-http-webhook-trigger"></a>Een HTTP-webhook-trigger toevoegen

Deze ingebouwde trigger registreert een call back-URL met de opgegeven service en wacht op de service om een HTTP POST-aanvraag naar die URL te verzenden. Wanneer deze gebeurtenis plaatsvindt, wordt de trigger geactiveerd en wordt de logische app onmiddellijk uitgevoerd.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). Open uw lege logische app in de ontwerp functie voor logische apps.

1. Voer op de ontwerp functie in het zoekvak ' http-webhook ' in als uw filter. Selecteer in de lijst **Triggers** de **http-webhook-** trigger.

   ![Trigger voor HTTP-webhook selecteren](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   In dit voor beeld wordt de naam van de trigger gewijzigd in ' HTTP-webhook-trigger ' zodat de stap een meer beschrijvende naam heeft. Daarnaast voegt het voor beeld een HTTP-webhook-actie toe en beide namen moeten uniek zijn.

1. Geef de waarden voor de [para meters voor de http-webhook-trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) op die u wilt gebruiken voor de abonnementen Subscriber en opzeg ging, bijvoorbeeld:

   ![Para meters voor HTTP-webhook-trigger opgeven](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

   Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over de beschik bare verificatie typen voor http-webhook.

1. Ga door met het bouwen van de werk stroom van uw logische app met acties die worden uitgevoerd wanneer de trigger wordt geactiveerd.

1. Wanneer u klaar bent, kunt u uw logische app opslaan. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

   Als u uw logische app opslaat, wordt het eind punt van de abonnee aangeroepen en wordt de call back-URL geregistreerd voor het activeren van deze logische app.

1. Wanneer de doel service nu een `HTTP POST` aanvraag verzendt naar de call back-URL, wordt de logische app geactiveerd en worden alle gegevens opgenomen die via de aanvraag zijn door gegeven.

## <a name="add-an-http-webhook-action"></a>Een HTTP-webhook-actie toevoegen

Met deze ingebouwde actie wordt een call back-URL voor de opgegeven service geregistreerd, wordt de werk stroom van de logische app onderbroken en wordt gewacht tot de betreffende service een HTTP POST-aanvraag verzendt naar die URL. Wanneer deze gebeurtenis plaatsvindt, wordt de logische app uitgevoerd met de actie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). Open uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt de HTTP-webhook-trigger als de eerste stap gebruikt.

1. Selecteer **nieuwe stap**onder de stap waarin u de http-webhook-actie wilt toevoegen.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer op de ontwerp functie in het zoekvak ' http-webhook ' in als uw filter. Selecteer in de lijst **acties** de actie **http-webhook** .

   ![Actie voor HTTP-webhook selecteren](./media/connectors-native-webhook/select-http-webhook-action.png)

   In dit voor beeld wordt de naam van de actie gewijzigd in HTTP-webhook-actie zodat de stap een meer beschrijvende naam heeft.

1. Geef de waarden op voor de para meters van de HTTP-webhook-actie, die vergelijkbaar zijn met de [para meters voor de http-webhook-trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) die u wilt gebruiken voor de abonnementen Subscriber and opzeg ging, bijvoorbeeld:

   ![Actie parameters voor HTTP-webhook opgeven](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Tijdens runtime roept de logische app het abonnements eindpunt aan wanneer deze actie wordt uitgevoerd. De logische app onderbreekt vervolgens de werk stroom en wacht tot de doel service een `HTTP POST` aanvraag naar de call back-URL verzendt. Als de actie is voltooid, wordt de actie afgemeld bij het eind punt en wordt uw logische app hervat met het uitvoeren van de werk stroom.

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

   Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over de beschik bare verificatie typen voor http-webhook.

1. Wanneer u klaar bent, moet u uw logische app opslaan. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

## <a name="connector-reference"></a>Connector-verwijzing

Zie [http-webhook-para meters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)voor meer informatie over trigger-en actie parameters, die vergelijkbaar zijn met elkaar.

### <a name="output-details"></a>Uitvoer Details

Hier vindt u meer informatie over de uitvoer van een HTTP-webhook-trigger of-actie, waarmee deze gegevens worden geretourneerd:

| Naam van eigenschap | Type | Beschrijving |
|---------------|------|-------------|
| koppen | object | De headers van de aanvraag |
| body | object | JSON-object | Het object met de inhoud van de hoofd tekst van de aanvraag |
| statuscode | int | De status code van de aanvraag |
|||

| Statuscode | Beschrijving |
|-------------|-------------|
| 200 | OK |
| 202 | Geaccepteerd |
| 400 | Ongeldig verzoek |
| 401 | Niet geautoriseerd |
| 403 | Verboden |
| 404 | Niet gevonden |
| 500 | Interne serverfout. Er is een onbekende fout opgetreden. |
|||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
