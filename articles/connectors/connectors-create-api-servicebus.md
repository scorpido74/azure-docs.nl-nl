---
title: Berichten uitwisselen met Azure Service Bus
description: Geautomatiseerde taken en werkstromen maken die berichten verzenden en ontvangen met Azure Service Bus in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261616"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Berichten uitwisselen in de cloud met Azure Logic Apps en Azure Service Bus

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de Azure Service [Bus-connector](../service-bus-messaging/service-bus-messaging-overview.md) u geautomatiseerde taken en werkstromen maken die gegevens overdragen, zoals verkoop- en inkooporders, dagboeken en voorraadbewegingen in toepassingen voor uw organisatie. De connector controleert, verzendt en beheert ook berichten, maar voert ook acties uit met wachtrijen, sessies, onderwerpen, abonnementen, enzovoort, bijvoorbeeld:

* Controleer wanneer berichten binnenkomen (automatisch aanvullen) of worden ontvangen (peek-lock) in wachtrijen, onderwerpen en onderwerpabonnementen.
* Stuur berichten.
* Onderwerpabonnementen maken en verwijderen.
* Beheer berichten in wachtrijen en onderwerpabonnementen, bijvoorbeeld, ontvang, ontvang uitgesteld, voltooid, uitstellen, verlaten en dode letter.
* Sloten voor berichten en sessies in wachtrijen en onderwerpabonnementen verlengen.
* Sluit sessies in wachtrijen en onderwerpen.

U triggers gebruiken die reacties van Service Bus ontvangen en de uitvoer beschikbaar maken voor andere acties in uw logische apps. U ook andere acties laten gebruiken voor de uitvoer van servicebusacties. Als u nieuw bent in Service Bus en Logic Apps, controleert u [Wat is Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) en Wat is Azure Logic [Apps?](../logic-apps/logic-apps-overview.md)

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een naamruimte voor servicebus en een berichtenentiteit, zoals een wachtrij. Deze items en uw logica-app moeten hetzelfde Azure-abonnement gebruiken. Als u deze items niet hebt, leest u hoe [u de naamruimte servicebus en een wachtrij maakt.](../service-bus-messaging/service-bus-create-namespace-portal.md)

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waarin u de naamruimte en berichtenentiteit servicebus gebruikt. Uw logica-app en de servicebus moeten hetzelfde Azure-abonnement gebruiken. Als u uw werkstroom wilt starten met een trigger van servicebus, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een servicebusactie in uw werkstroom wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de [recidieftrigger](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Machtigingen controleren

Controleer of uw logische app machtigingen heeft voor toegang tot de naamruimte van uw ServiceBus.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Ga naar de naamruimte van uw *servicebus.* Selecteer op de pagina naamruimte onder **Instellingen**de optie **Beleid voor gedeelde toegang**. Controleer **onder Claims**of u machtigingen voor **beheren** voor die naamruimte hebt.

   ![Machtigingen beheren voor naamruimte servicebus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Download de verbindingstekenreeks voor de naamruimte van uw servicebus. U hebt deze tekenreeks nodig wanneer u de verbindingsgegevens in uw logische app opgeeft.

   1. Selecteer **RootManageSharedAccessKey**in het deelvenster **Beleid voor gedeelde toegang** .
   
   1. Selecteer naast de primaire verbindingstekenreeks de knop Kopiëren. Sla de verbindingstekenreeks op voor later gebruik.

      ![Verbindingstekenreeks servicebus kopiëren](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Als u wilt bevestigen of uw verbindingstekenreeks is gekoppeld aan de naamruimte van uw `EntityPath`  ServiceBus of aan een berichtenentiteit, zoals een wachtrij, zoekt u in de verbindingstekenreeks naar de parameter. Als u deze parameter vindt, is de verbindingstekenreeks voor een specifieke entiteit en is deze niet de juiste tekenreeks die u gebruiken met uw logische app.

## <a name="add-service-bus-trigger"></a>Trigger servicebus toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw lege logische app in de Logic App Designer.

1. Voer in het zoekvak 'azure service bus' in als uw filter. Selecteer in de lijst triggers de gewenste trigger.

   Als u bijvoorbeeld uw logische app wilt activeren wanneer een nieuw item naar een wachtrij voor servicebus wordt verzonden, selecteert u de trigger **Wanneer een bericht wordt ontvangen in een wachtrij (automatisch aanvullen).**

   ![Trigger servicebus selecteren](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Alle Service Bus triggers zijn *long-polling* triggers. Deze beschrijving betekent dat wanneer de trigger wordt geactiveerd, de trigger alle berichten verwerkt en vervolgens 30 seconden wacht tot er meer berichten in de wachtrij of het onderwerpabonnement worden weergegeven. Als er binnen 30 seconden geen berichten worden weergegeven, wordt de triggerrun overgeslagen. Anders blijft de trigger berichten lezen totdat de wachtrij of het onderwerpabonnement leeg is. De volgende triggerpoll is gebaseerd op het herhalingsinterval dat is opgegeven in de eigenschappen van de trigger.

   Sommige triggers, zoals de **trigger Wanneer een of meer berichten in een wachtrij (automatisch aanvullen) aankomen,** kunnen een of meer berichten retourneren. Wanneer deze triggers worden geactiveerd, keren ze terug tussen één en het aantal berichten dat is opgegeven door de **eigenschap Maximum aantal berichten** van de trigger.

1. Als uw trigger voor het eerst verbinding maakt met de naamruimte van uw ServiceBus, voert u deze stappen uit wanneer de Logic App Designer u om verbindingsgegevens vraagt.

   1. Geef een naam op voor uw verbinding en selecteer de naamruimte van de servicebus.

      ![ServiceBusverbinding maken, deel 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Als u in plaats daarvan handmatig de verbindingstekenreeks wilt invoeren, selecteert u **Handmatig verbindingsgegevens invoeren**. Als u uw verbindingstekenreeks niet hebt, leest u [hoe u uw verbindingstekenreeks vinden.](#permissions-connection-string)

   1. Selecteer uw servicebusbeleid en selecteer **Maken**.

      ![ServiceBusverbinding maken, deel 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Selecteer de gewenste berichtenentiteit, zoals een wachtrij of onderwerp. Selecteer in dit voorbeeld de wachtrij servicebus.
   
      ![Wachtrij servicebus selecteren](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Geef de nodige informatie voor de geselecteerde trigger. Als u andere beschikbare eigenschappen aan de actie wilt toevoegen, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de gewenste eigenschappen.

   Selecteer voor de trigger van dit voorbeeld het polling-interval en de frequentie voor het controleren van de wachtrij.

   ![Polling-interval instellen](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Zie de [referentiepagina](/connectors/servicebus/)van de connector voor meer informatie over beschikbare triggers en eigenschappen.

1. Ga door met het bouwen van uw logische app door de gewenste acties toe te voegen.

   U bijvoorbeeld een actie toevoegen die e-mail verzendt wanneer een nieuw bericht binnenkomt. Wanneer de trigger uw wachtrij controleert en een nieuw bericht vindt, voert uw logische app de geselecteerde acties voor het gevonden bericht uit.

## <a name="add-service-bus-action"></a>Servicebus-actie toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in de Logic App Designer.

1. Selecteer Nieuwe **stap**onder de stap waarin u een actie wilt toevoegen .

   Of als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen deze stappen. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer Een actie **toevoegen**.

1. Voer **onder Een actie kiezen**in het zoekvak 'azure servicebus' in als filter. Selecteer in de lijst met acties de gewenste actie. 

   Selecteer in dit voorbeeld de actie **Bericht verzenden.**

   ![Servicebusactie selecteren](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Als uw actie voor het eerst verbinding maakt met de naamruimte van uw ServiceBus, voert u deze stappen uit wanneer de Logic App Designer u om verbindingsgegevens vraagt.

   1. Geef een naam op voor uw verbinding en selecteer de naamruimte van de servicebus.

      ![ServiceBusverbinding maken, deel 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Als u in plaats daarvan handmatig de verbindingstekenreeks wilt invoeren, selecteert u **Handmatig verbindingsgegevens invoeren**. Als u uw verbindingstekenreeks niet hebt, leest u [hoe u uw verbindingstekenreeks vinden.](#permissions-connection-string)

   1. Selecteer uw servicebusbeleid en selecteer **Maken**.

      ![ServiceBusverbinding maken, deel 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Selecteer de gewenste berichtenentiteit, zoals een wachtrij of onderwerp. Selecteer in dit voorbeeld de wachtrij servicebus.

      ![Wachtrij servicebus selecteren](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Geef de benodigde details op voor uw geselecteerde actie. Als u andere beschikbare eigenschappen aan de actie wilt toevoegen, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de gewenste eigenschappen.

   Selecteer bijvoorbeeld de eigenschappen **Inhoud** en **Inhoudstype,** zodat u deze aan de actie toevoegt. Geef vervolgens de inhoud op voor het bericht dat u wilt verzenden.

   ![Inhoud en details van berichten opgeven](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Zie de [referentiepagina](/connectors/servicebus/)van de connector voor meer informatie over beschikbare acties en hun eigenschappen.

1. Ga door met het bouwen van uw logische app door andere acties toe te voegen die u wilt.

   U bijvoorbeeld een actie toevoegen die e-mail verzendt om te bevestigen dat uw bericht is verzonden.

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

## <a name="connector-reference"></a>Connector-verwijzing

De Service Bus-connector kan tot 1.500 unieke sessies tegelijk opslaan, van een servicebus naar de connectorcache. Als het aantal sessies deze limiet overschrijdt, worden oude sessies uit de cache verwijderd. Zie [Berichtensessies voor](../service-bus-messaging/message-sessions.md)meer informatie .

Voor andere technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/servicebus/)van de connector. Zie [Wat is Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)voor meer informatie over Azure Service Bus Messaging?

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
