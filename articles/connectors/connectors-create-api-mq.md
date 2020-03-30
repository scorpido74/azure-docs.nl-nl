---
title: Verbinding maken met IBM MQ-server
description: Berichten verzenden en ophalen met een Azure- of on-premises IBM MQ-server en Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650944"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Verbinding maken met een IBM MQ-server vanuit Azure Logic Apps

De IBM MQ-connector verzendt en haalt berichten op die zijn opgeslagen in een IBM MQ-server op locatie of in Azure. Deze connector bevat een Microsoft MQ-client die communiceert met een externe IBM MQ-server via een TCP/IP-netwerk. Dit artikel biedt een startgids voor het gebruik van de MQ-connector. U beginnen met één bericht in een wachtrij te bladeren en vervolgens andere acties uit te proberen.

De IBM MQ-connector bevat deze acties, maar biedt geen triggers:

- Blader door één bericht zonder het bericht van de IBM MQ-server te verwijderen
- Blader door een batch berichten zonder de berichten van de IBM MQ-server te verwijderen
- Ontvang één bericht en verwijder het bericht van de IBM MQ-server
- Ontvang een batch berichten en verwijder de berichten van de IBM MQ-server
- Eén bericht verzenden naar de IBM MQ-server

## <a name="prerequisites"></a>Vereisten

* Als u een on-premises MQ-server gebruikt, [installeert u de on-premises gegevensgateway](../logic-apps/logic-apps-gateway-install.md) op een server binnen uw netwerk. De server waarop de on-premises datagateway is geïnstalleerd, moet ook .NET Framework 4.6 hebben geïnstalleerd om de MQ-connector te laten werken. U moet ook een bron maken in Azure voor de on-premises gegevensgateway. Zie De verbinding met [de gegevensgateway instellen](../logic-apps/logic-apps-gateway-connection.md)voor meer informatie .

  Als uw MQ-server echter openbaar beschikbaar is of beschikbaar is in Azure, hoeft u de gegevensgateway niet te gebruiken.

* Officieel ondersteunde IBM WebSphere MQ versies:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* De logische app waar u de MQ-actie wilt toevoegen. Deze logische app moet dezelfde locatie gebruiken als uw on-premises datagatewayverbinding en moet al een trigger hebben waarmee uw werkstroom wordt gestart. 

  De MQ-connector heeft geen triggers, dus u moet eerst een trigger toevoegen aan uw logische app. U bijvoorbeeld de recidieftrigger gebruiken. Als u nieuw bent in logische apps, probeert u deze [snelle start om uw eerste logische app te maken.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="browse-a-single-message"></a>Blader door één bericht

1. Kies in uw logica-app, onder de trigger of een andere actie, De optie **Nieuwe stap**. 

1. Typ in het zoekvak 'mq' en selecteer deze actie: **Blader door bericht**

   ![Bladeren door bericht](media/connectors-create-api-mq/Browse_message.png)

1. Als u geen bestaande MQ-verbinding hebt, maakt u de verbinding:  

   1. Selecteer in de actie **Verbinding maken via on-premises datagateway.**
   
   1. Voer de eigenschappen voor uw MQ-server in.  

      Voor **Server**u de naam mq-server invoeren of het IP-adres invoeren, gevolgd door een dubbele punt en het poortnummer.
    
   1. Open de **gatewaylijst,** waarin eerder geconfigureerde gatewayverbindingen worden weergegeven. Selecteer uw gateway.
    
   1. Wanneer u klaar bent, kiest u **Maken**. 
   
      Uw verbinding ziet er als volgt uit:

      ![Verbindingseigenschappen](media/connectors-create-api-mq/Connection_Properties.png)

1. De eigenschappen van de actie instellen:

   * **Wachtrij:** Geef een wachtrij op die verschilt van de verbinding.

   * **MessageId,** **CorrelationId,** **GroupId**en andere eigenschappen: Blader naar een bericht op basis van de verschillende MQ-berichteigenschappen

   * **IncludeInfo**: Geef **True** op om aanvullende berichtgegevens op te nemen in de uitvoer. Of geef **False** op om geen aanvullende berichtgegevens in de uitvoer op te nemen.

   * **Time-out:** voer een waarde in om te bepalen hoe lang u moet wachten tot een bericht in een lege wachtrij wordt weergegeven. Als er niets wordt ingevoerd, wordt het eerste bericht in de wachtrij opgehaald en wordt er geen tijd besteed aan het wachten tot een bericht wordt weergegeven.

     ![Blader door berichteigenschappen](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Sla** uw wijzigingen op en voer uw logica-app **uit.**

   ![Opslaan en uitvoeren](media/connectors-create-api-mq/Save_Run.png)

   Nadat de run is voltooid, worden de stappen van de run weergegeven en u de uitvoer bekijken.

1. Als u de details voor elke stap wilt bekijken, kiest u het groene vinkje. Als u meer informatie over de uitvoergegevens wilt bekijken, kiest **u Ruwe uitvoer weergeven**.

   ![Berichtuitvoer bekijken](media/connectors-create-api-mq/Browse_message_output.png)  

   Hier is een aantal monster ruwe output:

   ![Bladeren door de onbewerkte uitvoer van berichten bladeren](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Als u **IncludeInfo** op true instelt, wordt de volgende uitvoer weergegeven:

   ![Browse bericht bevatten info](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Door meerdere berichten bladeren

De actie **Berichten bladeren** bevat een optie **BatchSize** om aan te geven hoeveel berichten uit de wachtrij moeten worden geretourneerd.  Als **BatchSize** geen vermelding heeft, worden alle berichten geretourneerd. De geretourneerde uitvoer is een reeks berichten.

1. Wanneer u de actie **Browse-berichten** toevoegt, wordt de eerste eerder geconfigureerde verbinding standaard geselecteerd. Als u een nieuwe verbinding wilt maken, kiest u **Verbinding wijzigen**. Of selecteer een andere verbinding.

1. Nadat de logische app-run is voltooid, vindt u hier een voorbeeld uitvoer van de actie **Berichten bladeren:**

   ![Berichten uitvoer bekijken](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Eén bericht ontvangen

De actie **Bericht ontvangen** heeft dezelfde ingangen en uitvoer als de actie **Bericht bladeren.** Wanneer u **Bericht Ontvangen gebruikt,** wordt het bericht uit de wachtrij verwijderd.

## <a name="receive-multiple-messages"></a>Meerdere berichten ontvangen

De actie **Berichten ontvangen** heeft dezelfde ingangen en uitvoer als de actie **Berichten bladeren.** Bij gebruik **Van Berichten ontvangen,** worden de berichten uit de wachtrij verwijderd.

Als er geen berichten in de wachtrij staan bij het bladeren of ontvangen, mislukt de stap met deze uitvoer:  

![MQ Geen berichtfout](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Bericht verzenden

Wanneer u de actie **Berichten verzenden** toevoegt, wordt de eerste eerder geconfigureerde verbinding standaard geselecteerd. Als u een nieuwe verbinding wilt maken, kiest u **Verbinding wijzigen**. Of selecteer een andere verbinding.

1. Een geldig berichttype selecteren: **Datagram,** **Beantwoorden**of **Aanvragen**  

   ![Stuur Msg Rekwisieten](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Nadat de logische app is uitgevoerd, vindt u hier een voorbeeld uitvoer van de actie **Bericht verzenden:**

   ![Msg-uitvoer verzenden](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/mq/)de connector voor meer technische details over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
