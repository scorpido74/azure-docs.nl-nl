---
title: Berichten verzenden en ontvangen met Azure Service Bus-Azure Logic Apps | Microsoft Docs
description: Enter prise Cloud Messa ging met Azure Service Bus in Azure Logic Apps instellen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 944bac44c1fc6504dfe1a93df5760ccf4ee46fa0
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982213"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Berichten uitwisselen in de Cloud met Azure Service Bus en Azure Logic Apps

Met Azure Logic Apps en de Azure Service Bus-connector kunt u geautomatiseerde taken en werkstromen maken voor het overdragen van gegevens, zoals verkoop- en inkooporders, journalen en voorraadverplaatsingen tussen toepassingen voor uw organisatie. Met de connector worden berichten niet alleen bewaakt, verzonden en beheerd, maar worden ook acties uitgevoerd met wacht rijen, sessies, onderwerpen, abonnementen, enzovoort, bijvoorbeeld:

* Bewaken wanneer berichten binnenkomen (automatisch volt ooien) of ontvangen (kort weer geven) in wacht rijen, onderwerpen en onderwerp-abonnementen. 
* Berichten verzenden.
* Onderwerp-abonnementen maken en verwijderen.
* Het beheren van berichten in wacht rijen en onderwerp abonnementen, bijvoorbeeld ophalen, ophalen, volt ooien, uitstellen, afwijzen, afwijzen en onbestelbare letters.
* Verleng de vergren delingen van berichten en sessies in wacht rijen en onderwerp abonnementen.
* Sluit sessies in wacht rijen en onderwerpen.

U kunt triggers gebruiken die reacties ophalen van Service Bus en de uitvoer beschikbaar maken voor andere acties in uw Logic apps. U kunt ook andere acties uitvoeren met de uitvoer van Service Bus acties. Als u niet bekend bent met Service Bus en Logic Apps, raadpleegt u [Wat is Azure service bus?](../service-bus-messaging/service-bus-messaging-overview.md) en [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Een Service Bus naam ruimte en bericht entiteit, zoals een wachtrij. Als u deze items niet hebt, leert u hoe u [uw service bus naam ruimte en een wachtrij kunt maken](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Deze items moeten zich in hetzelfde Azure-abonnement bevinden als uw logische apps die gebruikmaken van deze items.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u Service Bus wilt gebruiken. Uw logische app moet zich in hetzelfde Azure-abonnement als uw service bus bevinden. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een service bus trigger. Als u een Service Bus actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Machtigingen controleren

Controleer of uw logische app machtigingen heeft om toegang te krijgen tot uw Service Bus-naam ruimte. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

2. Ga naar uw Service Bus *naam ruimte*. Selecteer op de pagina naam ruimte onder **instellingen**de optie **beleid voor gedeelde toegang**. Onder **claims**controleert u of u machtigingen voor het **beheren** van die naam ruimte hebt

   ![Machtigingen voor uw Service Bus-naam ruimte beheren](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Haal de connection string voor uw Service Bus naam ruimte op. U hebt deze teken reeks nodig wanneer u de verbindings gegevens invoert in uw logische app.

   1. Selecteer **RootManageSharedAccessKey**. 
   
   1. Klik naast uw primaire connection string op de knop kopiëren. Sla de connection string op voor later gebruik.

      ![Service Bus naam ruimte connection string kopiëren](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Als u wilt controleren of uw Connection String is gekoppeld aan uw service bus naam ruimte of een bericht entiteit, zoals een wachtrij, zoekt u de `EntityPath`Connection String voor de  para meter. Als u deze para meter vindt, is de connection string voor een specifieke entiteit en is de juiste teken reeks niet geschikt voor gebruik met uw logische app.

## <a name="add-trigger-or-action"></a>Trigger of actie toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Als u een *trigger* wilt toevoegen aan een lege logische app, voert u in het zoekvak ' Azure service bus ' in als uw filter. Selecteer de gewenste trigger onder de lijst met triggers. 

   Als u bijvoorbeeld uw logische app wilt activeren wanneer een nieuw item wordt verzonden naar een Service Bus wachtrij, selecteert u deze trigger: **Wanneer een bericht wordt ontvangen in een wachtrij (automatisch voltooid)**

   ![Service Bus trigger selecteren](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Sommige triggers kunnen een of meer berichten retour neren, bijvoorbeeld de trigger, **Wanneer een of meer berichten binnenkomen in een wachtrij (automatisch volt ooien)** . Wanneer deze triggers worden geactiveerd, retour neren ze tussen één en het aantal berichten dat is opgegeven door de eigenschap **maximum aantal** berichten van de trigger.

   *Alle service bus triggers zijn lange polling triggers*, wat betekent dat wanneer de trigger wordt geactiveerd, de trigger alle berichten verwerkt en vervolgens 30 seconden wacht totdat er meer berichten worden weer gegeven in de wachtrij of het onderwerp. 
   Als er gedurende 30 seconden geen berichten worden weer gegeven, wordt de trigger wordt uitgevoerd. 
   Anders wordt het lezen van berichten voortgezet totdat de wachtrij of het onderwerp leeg is. De volgende trigger poll is gebaseerd op het interval van het terugkeer patroon dat is opgegeven in de eigenschappen van de trigger.

1. Voer de volgende stappen uit om een *actie* toe te voegen aan een bestaande logische app: 

   1. Kies **nieuwe stap**onder de laatste stap waarin u een actie wilt toevoegen. 

      Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
      Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

   1. Voer in het zoekvak ' Azure Service Bus ' in als uw filter. 
   Selecteer in de lijst acties de gewenste actie. 
 
      Selecteer bijvoorbeeld deze actie: **Bericht verzenden**

      ![Service Bus actie selecteren](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Als u de logische app voor het eerst verbindt met uw Service Bus-naam ruimte, wordt u door de ontwerp functie voor logische apps gevraagd om uw verbindings gegevens. 

   1. Geef een naam op voor de verbinding en selecteer uw Service Bus naam ruimte.

      ![Service Bus verbinding maken, deel 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Als u de connection string in plaats daarvan hand matig wilt invoeren, kiest u **hand matig verbindings gegevens invoeren**. 
      Als u uw connection string niet hebt, kunt u hier meer informatie [vinden over uw Connection String](#permissions-connection-string).

   1. Selecteer nu uw Service Bus-beleid en kies vervolgens **maken**.

      ![Service Bus verbinding maken, deel 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. Selecteer voor dit voor beeld de gewenste Messa ging-entiteit, zoals een wachtrij of onderwerp. Selecteer in dit voor beeld uw Service Bus wachtrij. 
   
   ![Service Bus wachtrij selecteren](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Geef de benodigde gegevens op voor de trigger of actie. Voor dit voor beeld voert u de relevante stappen uit voor de trigger of actie: 

   * **Voor de voorbeeld trigger**: Stel het polling-interval en de frequentie voor het controleren van de wachtrij in.

     ![Polling-interval instellen](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Wanneer u klaar bent, kunt u door gaan met het bouwen van de werk stroom van uw logische app door de gewenste acties toe te voegen. U kunt bijvoorbeeld een actie toevoegen waarmee een e-mail wordt verzonden wanneer er een nieuw bericht binnenkomt.
     Als uw trigger uw wachtrij controleert en er een nieuw bericht vindt, voert uw logische app de geselecteerde acties uit voor het gevonden bericht.

   * **Voor de voorbeeld actie**: Voer de bericht inhoud en eventuele andere gegevens in. 

     ![Bericht inhoud en-Details opgeven](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Wanneer u klaar bent, kunt u door gaan met het bouwen van de werk stroom van uw logische app door de gewenste acties toe te voegen. U kunt bijvoorbeeld een actie toevoegen die een e-mail verzendt waarin wordt bevestigd dat uw bericht is verzonden.

1. Sla uw logische app op. Kies **Opslaan** op de werkbalk van de ontwerper.

## <a name="connector-reference"></a>Connector-verwijzing

De Service Bus-Connector kan Maxi maal 1.500 unieke sessies tegelijk van een service bus naar de connector cache opslaan. Als het aantal sessies groter is dan deze limiet, worden oude sessies verwijderd uit de cache. Raadpleeg de [referentie pagina](/connectors/servicebus/)van de connector voor andere technische informatie over triggers, acties en limieten die worden beschreven door de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors
