---
title: Verbinding maken met IBM MQ-server
description: Berichten verzenden en ophalen met een Azure- of on-premises IBM MQ-server en Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410271"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Verbinding maken met een IBM MQ-server vanuit Azure Logic Apps

De IBM MQ-connector verzendt en haalt berichten op die zijn opgeslagen in een IBM MQ-server op locatie of in Azure. Deze connector bevat een Microsoft MQ-client die communiceert met een externe IBM MQ-server via een TCP/IP-netwerk. Dit artikel biedt een startgids voor het gebruik van de MQ-connector. U beginnen met één bericht in een wachtrij te bladeren en vervolgens andere acties uit te proberen.

De IBM MQ-connector bevat deze acties, maar biedt geen triggers:

- Blader door één bericht zonder het bericht van de IBM MQ-server te verwijderen.
- Blader door een batch berichten zonder de berichten van de IBM MQ-server te verwijderen.
- Ontvang één bericht en verwijder het bericht van de IBM MQ-server.
- Ontvang een batch berichten en verwijder de berichten van de IBM MQ-server.
- Stuur één bericht naar de IBM MQ-server.

Hier zijn de officieel ondersteunde IBM WebSphere MQ versies:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

## <a name="prerequisites"></a>Vereisten

* Als u een on-premises MQ-server gebruikt, [installeert u de on-premises gegevensgateway](../logic-apps/logic-apps-gateway-install.md) op een server binnen uw netwerk. De server waarop de on-premises datagateway is geïnstalleerd, moet ook .NET Framework 4.6 hebben geïnstalleerd om de MQ-connector te laten werken.

  Nadat u de gateway hebt geïnstalleerd, moet u ook een bron in Azure maken voor de on-premises gegevensgateway. Zie De verbinding met [de gegevensgateway instellen](../logic-apps/logic-apps-gateway-connection.md)voor meer informatie .

  Als uw MQ-server openbaar beschikbaar is of beschikbaar is binnen Azure, hoeft u de gegevensgateway niet te gebruiken.

* De logische app waar u de MQ-actie wilt toevoegen. Deze logische app moet dezelfde locatie gebruiken als uw on-premises datagatewayverbinding en moet al een trigger hebben waarmee uw werkstroom wordt gestart.

  De MQ-connector heeft geen triggers, dus u moet eerst een trigger toevoegen aan uw logische app. U bijvoorbeeld de recidieftrigger gebruiken. Als u nieuw bent in logische apps, probeert u deze [snelle start om uw eerste logische app te maken.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>MQ-verbinding maken

Als u nog geen MQ-verbinding hebt wanneer u een MQ-actie toevoegt, wordt u gevraagd de verbinding te maken, bijvoorbeeld:

![Verbindingsgegevens verstrekken](media/connectors-create-api-mq/connection-properties.png)

1. Als u verbinding maakt met een on-premises MQ-server, selecteert u **Verbinding maken via on-premises gegevensgateway.**

1. Geef de verbindingsgegevens voor uw MQ-server op.

   * Voor **Server**u de naam mq-server invoeren of het IP-adres invoeren, gevolgd door een dubbele punt en het poortnummer.

   * Als u Secure Sockets Layer (SSL) wilt gebruiken, selecteert u **SSL inschakelen?**.

     De MQ-connector ondersteunt momenteel alleen serververificatie, niet clientverificatie. Zie [Verbindings- en verificatieproblemen](#connection-problems)voor meer informatie .

1. Voer in het **gatewaygedeelte** de volgende stappen uit:

   1. Selecteer **in** de lijst Abonnement het Azure-abonnement dat is gekoppeld aan uw Azure-gatewaybron.

   1. Selecteer in de lijst **Verbindingsgateway** de Azure-gatewaybron die u wilt gebruiken.

1. Als u gereed bent, selecteert u **Maken**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Verbindings- en verificatieproblemen

Wanneer uw logica-app verbinding probeert te maken met uw on-premises MQ-server, u deze fout opdeins:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Als u de MQ-connector rechtstreeks in Azure gebruikt, moet de MQ-server een certificaat gebruiken dat is uitgegeven door een vertrouwde [certificaatautoriteit.](https://www.ssl.com/faqs/what-is-a-certificate-authority/)

* Als u de on-premises gegevensgateway gebruikt, probeert u een certificaat te gebruiken dat waar mogelijk is uitgegeven door een vertrouwde [certificaatautoriteit.](https://www.ssl.com/faqs/what-is-a-certificate-authority/) Als deze optie echter niet mogelijk is, u een zelfondertekend certificaat gebruiken, dat niet is uitgegeven door een vertrouwde [certificaatautoriteit](https://www.ssl.com/faqs/what-is-a-certificate-authority/) en als minder veilig wordt beschouwd.

  Als u het zelfondertekende certificaat van de server wilt installeren, u het gereedschap **Windows Certification Manager** (certmgr.msc) gebruiken. Voor dit scenario moet u op uw lokale computer waar de on-premises datagatewayservice wordt uitgevoerd, het certificaat installeren in uw **lokale computercertificatenarchief** op het niveau **van de basiscertificeringsinstanties.**

  1. Open op de computer waar de on-premises-datagatewayservice wordt uitgevoerd het startmenu, zoekt en selecteert **U gebruikerscertificaten beheren.**

  1. Nadat het hulpprogramma Windows-certificeringsbeheer is geopend, gaat u naar de map Certificaten -**Vertrouwde basiscertificeringsinstanties voor** **basiscertificering** >  van de lokale computer en installeert u het certificaat.

     > [!IMPORTANT]
     > Zorg ervoor dat u het certificaat installeert in het archief **Certificaten - Lokale computer** > **vertrouwde rootcertificeringsinstanties.**

* De MQ-server vereist dat u de cijferspecificatie definieert die u wilt gebruiken voor SSL-verbindingen. SsLStream in .NET staat u echter niet toe om de volgorde voor cijferspecificaties op te geven. Als u deze beperking wilt omzeilen, u de configuratie van uw MQ-server aanpassen aan de eerste cijferspecificatie in de suite die de connector verzendt in de SSL-onderhandeling.

  Wanneer u de verbinding probeert, registreert de MQ-server een gebeurtenisbericht dat aangeeft dat de verbinding is mislukt omdat het andere uiteinde de onjuiste cijferspecificatie heeft gebruikt. Het gebeurtenisbericht bevat de cijferspecificatie die als eerste in de lijst wordt weergegeven. Werk de cijferspecificatie in de kanaalconfiguratie bij om aan de cijferspecificatie in het gebeurtenisbericht te voldoen.

## <a name="browse-single-message"></a>Blader door één bericht

1. Selecteer in uw logische app, onder de trigger of een andere actie, De optie **Nieuwe stap**.

1. Voer in het `mq`zoekvak de actie **Bericht bladeren** in en selecteer deze.

   ![De actie 'Bericht bladeren' selecteren](media/connectors-create-api-mq/browse-message.png)

1. Als u nog geen MQ-verbinding hebt gemaakt, wordt u gevraagd die verbinding te [maken.](#create-connection)

1. Nadat u de verbinding hebt gemaakt, stelt u de eigenschappen van de actie **Browse-bericht** in:

   | Eigenschap | Beschrijving |
   |----------|-------------|
   | **Wachtrij** | Als deze in de verbinding is opgegeven, geeft u die wachtrij op als deze verschilt van de wachtrij. |
   | **MessageId,** **CorrelationId,** **GroupId**en andere eigenschappen | Zoeken naar een bericht dat is gebaseerd op de verschillende MQ-berichteigenschappen |
   | **IncludeInfo** | Als u aanvullende berichtgegevens in de uitvoer wilt opnemen, selecteert u **True**. Als u aanvullende berichtgegevens in de uitvoer wilt weglaten, selecteert u **false**. |
   | **Timeout** | Voer een waarde in om te bepalen hoe lang u moet wachten tot een bericht in een lege wachtrij wordt weergegeven. Als er niets wordt ingevoerd, wordt het eerste bericht in de wachtrij opgehaald en wordt er geen tijd besteed aan het wachten tot een bericht wordt weergegeven. |
   |||

   Bijvoorbeeld:

   ![Eigenschappen voor de actie 'Bericht bladeren'](media/connectors-create-api-mq/browse-message-properties.png)

1. Wanneer u klaar bent, selecteert u op de werkbalk van de ontwerper de optie **Opslaan**. Als u uw app wilt testen, selecteert u **Uitvoeren**.

   Nadat de uitvoering is voltooid, toont de ontwerper de werkstroomstappen en hun status, zodat u de uitvoer bekijken.

1. Als u de details van elke stap wilt weergeven, klikt u op de titelbalk van de stap. Als u meer informatie wilt bekijken over de uitvoer van een stap, selecteert **u Ruwe uitvoer weergeven**.

   ![Berichtuitvoer bekijken](media/connectors-create-api-mq/browse-message-output.png)

   Hier is een aantal monster ruwe output:

   ![Bladeren door de onbewerkte uitvoer van berichten bladeren](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Als u **IncludeInfo** op **true instelt,** wordt extra uitvoer weergegeven:

   ![Browse bericht bevatten info](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Door meerdere berichten bladeren

De actie **Berichten bladeren** bevat een optie **BatchSize** om aan te geven hoeveel berichten er uit de wachtrij moeten worden teruggegeven. Als **BatchSize** geen waarde heeft, worden alle berichten geretourneerd. De geretourneerde uitvoer is een reeks berichten.

1. Volg de vorige stappen, maar voeg in plaats daarvan de actie **Berichten bladeren** toe.

1. Als u nog geen MQ-verbinding hebt gemaakt, wordt u gevraagd die verbinding te [maken.](#create-connection) Anders wordt standaard de eerste eerder geconfigureerde verbinding gebruikt. Als u een nieuwe verbinding wilt maken, selecteert u **Verbinding wijzigen**. Of selecteer een andere verbinding.

1. Geef de informatie voor de actie.

1. Sla de logische app op en voer deze uit.

   Nadat de logische app is uitgevoerd, vindt u hier een voorbeeld uitvoer van de actie **Berichten bladeren:**

   ![Voorbeeld van de uitvoer 'Berichten bladeren'](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Eén bericht ontvangen

De actie **Bericht ontvangen** heeft dezelfde ingangen en uitvoer als de actie **Bericht bladeren.** Wanneer u **Bericht Ontvangen gebruikt,** wordt het bericht uit de wachtrij verwijderd.

## <a name="receive-multiple-messages"></a>Meerdere berichten ontvangen

De actie **Berichten ontvangen** heeft dezelfde ingangen en uitvoer als de actie **Berichten bladeren.** Wanneer u **Berichten ontvangen**gebruikt, worden de berichten uit de wachtrij verwijderd.

> [!NOTE]
> Wanneer u een browse- of ontvangstactie uitvoert in een wachtrij die geen berichten heeft, mislukt de actie met deze uitvoer:
>
> ![MQ "geen bericht" fout](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Bericht verzenden

1. Volg de vorige stappen, maar voeg in plaats daarvan de actie **Bericht verzenden** toe.

1. Als u nog geen MQ-verbinding hebt gemaakt, wordt u gevraagd die verbinding te [maken.](#create-connection) Anders wordt standaard de eerste eerder geconfigureerde verbinding gebruikt. Als u een nieuwe verbinding wilt maken, selecteert u **Verbinding wijzigen**. Of selecteer een andere verbinding.

1. Geef de informatie voor de actie. Selecteer **voor MessageType**een geldig berichttype: **Datagram,** **Beantwoorden**of **Aanvragen**

   ![Eigenschappen voor 'Actie bericht verzenden'](media/connectors-create-api-mq/send-message-properties.png)

1. Sla de logische app op en voer deze uit.

   Nadat de logische app is uitgevoerd, vindt u hier een voorbeeld uitvoer van de actie **Bericht verzenden:**

   ![Voorbeeld van de uitvoer van bericht verzenden](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over acties en limieten, die worden beschreven in de Swagger-beschrijving van de connector, raadpleegt u de [referentiepagina](/connectors/mq/)van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
