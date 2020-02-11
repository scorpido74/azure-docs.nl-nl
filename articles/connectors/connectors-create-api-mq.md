---
title: Verbinding maken met de IBM MQ-server
description: Berichten verzenden en ophalen met een Azure-of on-premises IBM MQ-server en Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 489f53a4f4c1c0d5bd782f42a9daf73217234793
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118046"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Verbinding maken met een IBM MQ-server vanaf Azure Logic Apps

De IBM MQ-connector verzendt en haalt berichten op die zijn opgeslagen in een IBM MQ-server on-premises of in Azure. Deze connector bevat een micro soft MQ-client die communiceert met een externe IBM MQ-server via een TCP/IP-netwerk. Dit artikel bevat een Start handleiding voor het gebruik van de MQ-connector. U kunt beginnen door één bericht in een wachtrij te bladeren en vervolgens andere acties uit te proberen.

De IBM MQ-connector bevat deze acties, maar biedt geen triggers:

- Door één bericht bladeren zonder het bericht te verwijderen van de IBM MQ-server
- Bladeren in een batch berichten zonder de berichten van de IBM MQ-server te verwijderen
- Eén bericht ontvangen en het bericht verwijderen van de IBM MQ-server
- Een batch berichten ontvangen en de berichten van de IBM MQ-server verwijderen
- Eén bericht verzenden naar de IBM MQ-server

## <a name="prerequisites"></a>Vereisten

* Als u een on-premises MQ-server gebruikt, [installeert u de on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md) op een server in uw netwerk. Op de server waarop de on-premises gegevens gateway is geïnstalleerd moet ook .NET Framework 4,6 zijn geïnstalleerd om de MQ-connector te kunnen gebruiken. U moet ook een resource in azure maken voor de on-premises gegevens gateway. Zie [de data gateway-verbinding instellen](../logic-apps/logic-apps-gateway-connection.md)voor meer informatie.

  Als uw MQ-server echter openbaar beschikbaar of beschikbaar is in azure, hoeft u de gegevens gateway niet te gebruiken.

* Officieel ondersteunde IBM WebSphere MQ-versies:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0

* De logische app waaraan u de MQ-actie wilt toevoegen. Deze logische app moet dezelfde locatie gebruiken als uw on-premises gegevens gateway-verbinding en moet al een trigger hebben waarmee de werk stroom wordt gestart. 

  De MQ-connector heeft geen triggers, dus u moet eerst een trigger toevoegen aan uw logische app. U kunt bijvoorbeeld de terugkeer patroon trigger gebruiken. Als u geen ervaring hebt met Logic apps, kunt u deze [Snelstartgids proberen om uw eerste logische app te maken](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Door één bericht bladeren

1. Kies in uw logische app, onder de trigger of andere actie, de optie **nieuwe stap**. 

1. Typ ' MQ ' in het zoekvak en selecteer deze actie: **door bericht bladeren**

   ![Bericht door bladeren](media/connectors-create-api-mq/Browse_message.png)

1. Als u geen bestaande MQ-verbinding hebt, maakt u de verbinding:  

   1. Selecteer in de actie **verbinding maken via on-premises gegevens gateway**.
   
   1. Voer de eigenschappen voor uw MQ-server in.  

      Voor de **Server**kunt u de naam van de MQ-server invoeren of het IP-adres invoeren, gevolgd door een dubbele punt en het poort nummer.
    
   1. Open de lijst met **gateways** , waarin eerder geconfigureerde gateway verbindingen worden weer gegeven. Selecteer uw gateway.
    
   1. Wanneer u klaar bent, kiest u **Maken**. 
   
      Uw verbinding ziet eruit als in dit voor beeld:

      ![Verbindingseigenschappen](media/connectors-create-api-mq/Connection_Properties.png)

1. De eigenschappen van de actie instellen:

   * **Wachtrij**: Geef een wachtrij op die verschilt van de verbinding.

   * **MessageId**, **CorrelationId**, **GroupId**en andere eigenschappen: bladeren naar een bericht op basis van de verschillende MQ-bericht eigenschappen

   * **IncludeInfo**: Geef **waar** op om aanvullende bericht gegevens op te geven in de uitvoer. Of geef **False** op als u geen aanvullende bericht gegevens wilt toevoegen aan de uitvoer.

   * **Time-out**: Voer een waarde in om te bepalen hoe lang moet worden gewacht tot een bericht in een lege wachtrij arriveert. Als niets wordt ingevoerd, wordt het eerste bericht in de wachtrij opgehaald en wordt er geen tijd besteed aan het wachten op het verschijnen van een bericht.

     ![Door bericht eigenschappen bladeren](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Sla** uw wijzigingen op en voer vervolgens uw logische app **uit** .

   ![Opslaan en uitvoeren](media/connectors-create-api-mq/Save_Run.png)

   Nadat de uitvoering is voltooid, worden de stappen van de uitvoering weer gegeven en kunt u de uitvoer bekijken.

1. Als u de Details voor elke stap wilt bekijken, kiest u het groene vinkje. Als u meer informatie over de uitvoer gegevens wilt bekijken, kiest u **onbewerkte uitvoer weer geven**.

   ![Bladeren in bericht uitvoer](media/connectors-create-api-mq/Browse_message_output.png)  

   Hier volgt een voor beeld van een onbewerkte uitvoer:

   ![Bladeren in bericht onbewerkte uitvoer](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Als u **IncludeInfo** instelt op True, wordt de volgende uitvoer weer gegeven:

   ![Bladeren in bericht gegevens toevoegen](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Door meerdere berichten bladeren

De actie **Bladeren door berichten** bevat een **BatchSize** -optie om aan te geven hoeveel berichten er moeten worden geretourneerd uit de wachtrij.  Als **BatchSize** geen vermelding heeft, worden alle berichten geretourneerd. De geretourneerde uitvoer is een matrix van berichten.

1. Wanneer u de actie **Bladeren door berichten** toevoegt, is de eerste eerder geconfigureerde verbinding standaard geselecteerd. Als u een nieuwe verbinding wilt maken, kiest u **verbinding wijzigen**. Of selecteer een andere verbinding.

1. Nadat de uitvoering van de logische app is voltooid, ziet u een voor beeld van de uitvoer van de actie **berichten door bladeren** :

   ![Door de uitvoer van berichten bladeren](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Eén bericht ontvangen

De actie **bericht ontvangen** heeft dezelfde invoer en uitvoer als de actie **bericht bladeren** . Wanneer u **bericht ontvangen**gebruikt, wordt het bericht uit de wachtrij verwijderd.

## <a name="receive-multiple-messages"></a>Meerdere berichten ontvangen

De actie **berichten ontvangen** heeft dezelfde invoer en uitvoer als de actie **berichten door bladeren** . Wanneer u **berichten ontvangen**gebruikt, worden de berichten uit de wachtrij verwijderd.

Als er geen berichten in de wachtrij staan wanneer u een Blader-of ontvangst bewerking uitvoert, mislukt de stap met deze uitvoer:  

![MQ geen bericht fout](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Bericht verzenden

Wanneer u de actie **berichten verzenden** toevoegt, is de eerste eerder geconfigureerde verbinding standaard geselecteerd. Als u een nieuwe verbinding wilt maken, kiest u **verbinding wijzigen**. Of selecteer een andere verbinding.

1. Selecteer een geldig bericht type: **Data gram**, **antwoord**of **aanvraag**  

   ![Bericht-eigenschappen verzenden](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Nadat de logische app is uitgevoerd, ziet u een voor beeld van de uitvoer van de actie **bericht verzenden** :

   ![Bericht uitvoer verzenden](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/mq/)van de connector voor technische informatie over acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
