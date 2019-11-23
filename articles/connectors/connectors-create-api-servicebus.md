---
title: Berichten uitwisselen met Azure Service Bus-Azure Logic Apps
description: Berichten verzenden en ontvangen met behulp van Azure Service Bus in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 477fdc2291d875c57bfb0ae3cb87b6df9bd41398
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024293"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Berichten uitwisselen in de Cloud met behulp van Azure Logic Apps en Azure Service Bus

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de [Azure service bus](../service-bus-messaging/service-bus-messaging-overview.md) -connector kunt u geautomatiseerde taken en werk stromen maken waarmee gegevens worden overgedragen, zoals verkoop-en inkoop orders, journalen en voorraad verplaatsingen tussen toepassingen voor uw organisatie. Met de connector worden berichten niet alleen bewaakt, verzonden en beheerd, maar worden ook acties uitgevoerd met wacht rijen, sessies, onderwerpen, abonnementen, enzovoort, bijvoorbeeld:

* Bewaken wanneer berichten binnenkomen (automatisch volt ooien) of ontvangen (kort weer geven) in wacht rijen, onderwerpen en onderwerp-abonnementen.
* Berichten verzenden.
* Onderwerp-abonnementen maken en verwijderen.
* Het beheren van berichten in wacht rijen en onderwerp abonnementen, bijvoorbeeld ophalen, ophalen, volt ooien, uitstellen, afwijzen, afwijzen en onbestelbare letters.
* Verleng de vergren delingen van berichten en sessies in wacht rijen en onderwerp abonnementen.
* Sluit sessies in wacht rijen en onderwerpen.

U kunt triggers gebruiken die reacties ophalen van Service Bus en de uitvoer beschikbaar maken voor andere acties in uw Logic apps. U kunt ook andere acties uitvoeren met de uitvoer van Service Bus acties. Als u niet bekend bent met Service Bus en Logic Apps, raadpleegt u [Wat is Azure service bus?](../service-bus-messaging/service-bus-messaging-overview.md) en [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md)?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een Service Bus naam ruimte en bericht entiteit, zoals een wachtrij. Deze items en uw logische app moeten gebruikmaken van hetzelfde Azure-abonnement. Als u deze items niet hebt, leert u hoe u [uw service bus naam ruimte en een wachtrij kunt maken](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u de Service Bus naam ruimte en de bericht entiteit gebruikt. Uw logische app en de service bus moeten hetzelfde Azure-abonnement gebruiken. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om uw werk stroom te starten met een service bus trigger. Als u een Service Bus actie in uw werk stroom wilt gebruiken, start u de logische app met een andere trigger, bijvoorbeeld de [trigger voor terugkeer patroon](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Machtigingen controleren

Controleer of uw logische app machtigingen heeft om toegang te krijgen tot uw Service Bus-naam ruimte.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Ga naar uw Service Bus *naam ruimte*. Selecteer op de pagina naam ruimte onder **instellingen**de optie **beleid voor gedeelde toegang**. Onder **claims**controleert u of u machtigingen voor het **beheren** van die naam ruimte hebt.

   ![Machtigingen voor Service Bus naam ruimte beheren](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Haal de connection string voor uw Service Bus naam ruimte op. U hebt deze teken reeks nodig wanneer u de verbindings gegevens in uw logische app opgeeft.

   1. Selecteer **RootManageSharedAccessKey**in het deel venster **Shared Access policies** .
   
   1. Selecteer de knop kopiëren naast uw primaire connection string. Sla de connection string op voor later gebruik.

      ![Service Bus naam ruimte connection string kopiëren](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Als u wilt controleren of uw connection string is gekoppeld aan uw Service Bus naam ruimte of een bericht entiteit, zoals een wachtrij, zoekt u de connection string voor de para meter `EntityPath` . Als u deze para meter vindt, is de connection string voor een specifieke entiteit en is de juiste teken reeks niet geschikt voor gebruik met uw logische app.

## <a name="add-service-bus-trigger"></a>Service Bus trigger toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open uw lege logische app in de ontwerp functie voor logische apps.

1. Voer in het zoekvak ' Azure service bus ' in als uw filter. Selecteer de gewenste trigger in de lijst met triggers.

   Als u bijvoorbeeld uw logische app wilt activeren wanneer een nieuw item wordt verzonden naar een Service Bus wachtrij, selecteert u de trigger **Wanneer een bericht wordt ontvangen in een wachtrij (automatisch volt ooien)** .

   ![Service Bus trigger selecteren](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Alle Service Bus triggers zijn *lange polling* triggers. Deze beschrijving betekent dat wanneer de trigger wordt geactiveerd, de trigger alle berichten verwerkt en vervolgens 30 seconden wacht totdat er meer berichten worden weer gegeven in de wachtrij of het onderwerp. Als er gedurende 30 seconden geen berichten worden weer gegeven, wordt de trigger wordt uitgevoerd. Anders wordt het lezen van berichten voortgezet totdat de wachtrij of het onderwerp leeg is. De volgende trigger poll is gebaseerd op het interval van het terugkeer patroon dat is opgegeven in de eigenschappen van de trigger.

   Sommige triggers, zoals het **tijdstip waarop een of meer berichten binnenkomen in een wachtrij (automatisch volt ooien)** , kunnen een of meer berichten retour neren. Wanneer deze triggers worden geactiveerd, retour neren ze tussen één en het aantal berichten dat is opgegeven door de eigenschap **maximum aantal berichten** van de trigger.

1. Als uw trigger voor de eerste keer verbinding maakt met uw Service Bus-naam ruimte, voert u de volgende stappen uit wanneer de ontwerp functie voor logische apps u om verbindings informatie vraagt.

   1. Geef een naam op voor de verbinding en selecteer uw Service Bus naam ruimte.

      ![Service Bus verbinding maken, deel 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Als u de connection string in plaats daarvan hand matig wilt invoeren, selecteert u **hand matig verbindings gegevens invoeren**. Als u uw connection string niet hebt, kunt u hier meer informatie [vinden over uw Connection String](#permissions-connection-string).

   1. Selecteer uw Service Bus-beleid en selecteer **maken**.

      ![Service Bus verbinding maken, deel 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Selecteer de gewenste Messa ging-entiteit, zoals een wachtrij of onderwerp. Selecteer voor dit voor beeld uw Service Bus wachtrij.
   
      ![Service Bus wachtrij selecteren](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Geef de benodigde informatie op voor de geselecteerde trigger. Als u andere beschik bare eigenschappen aan de actie wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste eigenschappen.

   Selecteer voor de trigger van dit voor beeld het polling-interval en de frequentie voor het controleren van de wachtrij.

   ![Polling-interval instellen](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Zie de [referentie pagina](/connectors/servicebus/)van de connector voor meer informatie over de beschik bare triggers en eigenschappen.

1. Ga door met het bouwen van uw logische app door de gewenste acties toe te voegen.

   U kunt bijvoorbeeld een actie toevoegen waarmee een e-mail wordt verzonden wanneer er een nieuw bericht binnenkomt. Als uw trigger uw wachtrij controleert en er een nieuw bericht vindt, voert uw logische app de geselecteerde acties uit voor het gevonden bericht.

## <a name="add-service-bus-action"></a>Service Bus actie toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **nieuwe stap**onder de stap waar u een actie wilt toevoegen.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer **een actie toevoegen**.

1. Onder **Kies een actie**, voert u in het zoekvak ' Azure service bus ' in als uw filter. Selecteer in de lijst acties de gewenste actie. 

   Selecteer voor dit voor beeld de actie **bericht verzenden** .

   ![Service Bus actie selecteren](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Als uw actie voor de eerste keer verbinding maakt met uw Service Bus-naam ruimte, voert u de volgende stappen uit wanneer de ontwerp functie voor logische apps u vraagt om verbindings gegevens.

   1. Geef een naam op voor de verbinding en selecteer uw Service Bus naam ruimte.

      ![Service Bus verbinding maken, deel 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Als u de connection string in plaats daarvan hand matig wilt invoeren, selecteert u **hand matig verbindings gegevens invoeren**. Als u uw connection string niet hebt, kunt u hier meer informatie [vinden over uw Connection String](#permissions-connection-string).

   1. Selecteer uw Service Bus-beleid en selecteer **maken**.

      ![Service Bus verbinding maken, deel 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Selecteer de gewenste Messa ging-entiteit, zoals een wachtrij of onderwerp. Selecteer voor dit voor beeld uw Service Bus wachtrij.

      ![Service Bus wachtrij selecteren](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Geef de benodigde gegevens op voor de geselecteerde actie. Als u andere beschik bare eigenschappen aan de actie wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste eigenschappen.

   Selecteer bijvoorbeeld de eigenschappen **Content** en **Content type** , zodat u deze toevoegt aan de actie. Geef vervolgens de inhoud op voor het bericht dat u wilt verzenden.

   ![Bericht inhoud en-Details opgeven](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Zie de [referentie pagina](/connectors/servicebus/)van de connector voor meer informatie over beschik bare acties en hun eigenschappen.

1. Ga door met het bouwen van uw logische app door alle gewenste acties toe te voegen.

   U kunt bijvoorbeeld een actie toevoegen waarmee een e-mail wordt verzonden om te bevestigen dat uw bericht is verzonden.

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

## <a name="connector-reference"></a>Connector-verwijzing

De Service Bus-Connector kan Maxi maal 1.500 unieke sessies tegelijk van een service bus naar de connector cache opslaan. Als het aantal sessies groter is dan deze limiet, worden oude sessies verwijderd uit de cache. Raadpleeg de [referentie pagina](/connectors/servicebus/)van de connector voor andere technische informatie over triggers, acties en limieten die worden beschreven door de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
