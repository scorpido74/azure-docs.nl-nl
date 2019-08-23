---
title: Verbinding maken met SAP Systems-Azure Logic Apps | Microsoft Docs
description: Toegang tot en beheer van SAP-bronnen door werk stromen te automatiseren met Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 31f6a3fc281b8dc309ddcd237246c870c85ae20b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971627"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Verbinding maken met SAP-systemen via Azure Logic Apps

> [!NOTE]
> Deze SAP-connector is gepland voor afschaffing. Gebruik of Migreer naar de [nieuwere en geavanceerdere sap-connector](./logic-apps-using-sap-connector.md). 

In dit artikel wordt beschreven hoe u vanuit een logische app toegang hebt tot uw SAP-resources met behulp van de SAP-toepassings server en SAP Message server-connectors. Op die manier kunt u taken, processen en werk stromen automatiseren die uw SAP-gegevens en-resources beheren door logische apps te maken.

In dit voor beeld wordt een logische app gebruikt die u kunt activeren met een HTTP-aanvraag. De logische app verzendt een tussenliggend document (IDoc) naar een SAP-server en retourneert een antwoord naar de aanvrager die de logische app wordt genoemd.
De huidige SAP-connectors hebben acties, maar geen triggers. in dit voor beeld wordt de [HTTP-aanvraag trigger](../connectors/connectors-native-reqres.md) gebruikt als eerste stap in de werk stroom van de logische app. Voor SAP-connector-specifieke technische informatie raadpleegt u deze referentie artikelen: 

* <a href="https://docs.microsoft.com/connectors/sap" target="blank">Connector voor SAP-toepassings server</a>
* <a href="https://docs.microsoft.com/connectors/sap/#send-message-to-sap" target="blank">SAP Message server-connector</a>

Als u nog geen Azure-abonnement hebt, <a href="https://azure.microsoft.com/free/" target="_blank">meldt u zich aan voor een gratis Azure-account</a>.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt volgen, hebt u de volgende items nodig:

* De logische app die u wilt gebruiken om toegang te krijgen tot uw SAP-systeem en een trigger waarmee de werk stroom van de logische app wordt gestart. De SAP-connectors bieden momenteel alleen acties. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: Maak uw eerste logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uw <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP-toepassings server</a> of <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP-berichten server</a>

* Down load en installeer de meest recente [on-premises gegevens gateway](https://www.microsoft.com/download/details.aspx?id=53127) op elke on-premises computer. Zorg ervoor dat u uw gateway in de Azure Portal hebt ingesteld voordat u doorgaat. De gateway helpt u veilig toegang te krijgen tot gegevens en resources zijn on-premises. Zie [on-premises gegevens gateway installeren voor Azure Logic apps](../logic-apps/logic-apps-gateway-install.md)voor meer informatie.

* Down load en installeer de meest recente SAP-client bibliotheek, die momenteel <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP connector (NCo) 3.0.20.0 voor Microsoft .NET Framework 4,0 en Windows 64-bits (x64)</a>, op dezelfde computer als de on-premises gegevens gateway. Installeer deze versie of later om de volgende redenen:

  * Eerdere SAP NCo-versies kunnen worden gedeadlockd wanneer meer dan één IDoc-berichten tegelijk worden verzonden. 
  Deze voor waarde blokkeert alle latere berichten die worden verzonden naar de SAP-bestemming, waardoor er een time-out optreedt voor de berichten.

  * De on-premises gegevens gateway wordt alleen uitgevoerd op 64-bits systemen. 
  Anders krijgt u een fout bericht over een onjuiste afbeelding omdat de data gateway-hostservice geen 32-bits-assembly's ondersteunt.

  * Zowel de data gateway-hostservice als de micro soft SAP-adapter gebruiken .NET Framework 4,5. De SAP-NCo voor .NET Framework 4,0 werkt met processen die .NET runtime 4,0 gebruiken in 4.7.1. 
  De SAP-NCo voor .NET Framework 2,0 werkt met processen die .NET runtime 2,0 naar 3,5 gebruiken en werkt niet meer met de meest recente on-premises gegevens gateway.

* Bericht inhoud die u kunt verzenden naar uw SAP-server, zoals een voor beeld van een IDoc-bestand. Deze inhoud moet de XML-indeling hebben en de naam ruimte bevatten voor de SAP-actie die u wilt gebruiken.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>HTTP-aanvraag trigger toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer aan een bepaalde voor waarde wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-Engine een exemplaar van een logische app en begint de werk stroom van uw app uit te voeren.

In dit voor beeld maakt u een logische app met een eind punt in azure, zodat u *http post-aanvragen* kunt verzenden naar uw logische app. Wanneer uw logische app deze HTTP-aanvragen ontvangt, wordt de trigger geactiveerd en wordt de volgende stap in uw werk stroom uitgevoerd.

1. Maak in de Azure Portal een lege logische app, waarmee de ontwerp functie voor logische apps wordt geopend. 

2. Voer in het zoekvak ' HTTP-aanvraag ' in als uw filter. Selecteer in de lijst triggers deze trigger: **Aanvraag: wanneer een HTTP-aanvraag wordt ontvangen**

   ![HTTP-aanvraag trigger toevoegen](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Sla de logische app nu op zodat u een eind punt-URL kunt genereren voor uw logische app.
Kies **Opslaan** op de werkbalk van de ontwerper. 

   De eind punt-URL wordt nu weer gegeven in de trigger, bijvoorbeeld:

   ![URL voor eind punt genereren](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>SAP-actie toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werk stroom die volgt op een trigger of een andere actie. Als u nog geen trigger hebt toegevoegd aan uw logische app en u dit voor beeld wilt volgen, [voegt u de in deze sectie beschreven trigger toe](#add-trigger).

1. Kies in de ontwerp functie voor apps onder de trigger **nieuwe stap** > **een actie toevoegen**.

   ![Een actie toevoegen](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. Voer in het zoekvak ' SAP-server ' in als uw filter. Selecteer in de lijst acties de actie voor uw SAP-server: 

   * **SAP-toepassings server: verzenden naar SAP**
   * **SAP-berichten server: verzenden naar SAP**

   In dit voor beeld wordt deze actie gebruikt: **SAP-toepassings server: verzenden naar SAP**

   ![Selecteer SAP-toepassings server of SAP-bericht server](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Als u wordt gevraagd om de verbindings gegevens, maakt u nu uw SAP-verbinding. Als uw verbinding al bestaat, gaat u verder met de volgende stap, zodat u uw SAP-actie kunt instellen. 

   **On-premises SAP-verbinding maken**

   1. Voor **gateways**selecteert u **verbinding maken via een on-premises gegevens gateway** zodat de eigenschappen van de on-premises verbinding worden weer gegeven.

   2. Geef de verbindings gegevens voor uw SAP-server op. 
   Selecteer voor de **Gateway** -eigenschap de gegevens gateway die u hebt gemaakt in de Azure portal voor de gateway-installatie, bijvoorbeeld:

      **SAP-toepassings server**

      ![Een SAP-toepassings server verbinding maken](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **SAP-berichten server**

      ![SAP Message server-verbinding maken](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Wanneer u klaar bent, kiest u **Maken**.

      Logic Apps stelt uw verbinding in en test deze om te controleren of de verbinding goed werkt.

4. Nu vindt u een actie op de SAP-server en selecteert u deze. 

   1. Kies in het tekstvak **SAP** het mappictogram. 
   Zoek en selecteer de actie die u wilt gebruiken in de lijst met mappen. 

      In dit voor beeld wordt de categorie **IDOC** geselecteerd voor de actie IDOC. 

      ![Zoek en selecteer IDoc-actie](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Als de gewenste actie niet kan worden gevonden, kunt u hand matig een pad invoeren, bijvoorbeeld:

      ![Hand matig het pad naar de IDoc-actie opgeven](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Zie [bericht schema's voor IDOC-bewerkingen](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) voor meer informatie over IDOC-bewerkingen

   2. Klik in het vak **invoer bericht** zodat de lijst met dynamische inhoud wordt weer gegeven. 
   Selecteer in deze lijst, onder **Wanneer een HTTP-aanvraag wordt ontvangen**, het veld **hoofd tekst** . 

      In deze stap wordt de inhoud van de hoofd tekst van uw HTTP-aanvraag trigger opgenomen en wordt die uitvoer naar uw SAP-server verzonden.

      ![Selecteer het veld hoofd tekst](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Wanneer u klaar bent, ziet uw SAP-actie eruit als in dit voor beeld:

      ![SAP-actie volt ooien](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Sla uw logische app op. Kies **Opslaan** op de werkbalk van de ontwerper.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>HTTP-antwoord actie toevoegen

Voeg nu een reactie actie toe aan de werk stroom van uw logische app en neem de uitvoer op uit de SAP-actie. Op die manier retourneert uw logische app de resultaten van uw SAP-server naar de oorspronkelijke aanvrager. 

1. Klik in de ontwerp functie voor logische apps, onder de actie SAP, op **nieuwe stap** > **een actie toevoegen**.

2. Voer in het zoekvak ' respons ' in als uw filter. Selecteer in de lijst acties deze actie: **Aanvraag-antwoord**

3. Klik in het vak **hoofd tekst** zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst onder **verzenden naar SAP**het veld **hoofd tekst** . 

   ![SAP-actie volt ooien](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Sla uw logische app op. 

## <a name="test-your-logic-app"></a>Uw logische app testen

1. Als uw logische app nog niet is ingeschakeld, kiest u **overzicht**in het menu van de logische app. Kies **inschakelen**op de werk balk. 

2. Kies **uitvoeren**op de werk balk van de Logic app-ontwerp functie. Met deze stap wordt de logische app hand matig gestart.

3. Activeer uw logische app door een HTTP POST-aanvraag te verzenden naar de URL in uw HTTP-aanvraag trigger en de inhoud van uw bericht op te vragen. U kunt een hulp programma zoals postman gebruiken om de aanvraag te [](https://www.getpostman.com/apps)verzenden. 

   Voor dit artikel verzendt de aanvraag een IDoc-bestand, dat moet de XML-indeling hebben en de naam ruimte bevatten voor de SAP-actie die u gebruikt, bijvoorbeeld: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Nadat u uw HTTP-aanvraag hebt verzonden, moet u wachten op de reactie van de logische app.

> [!NOTE]
> Mogelijk is er een time-out opgetreden voor uw logische app als alle stappen die vereist zijn voor het antwoord niet binnen de [time-outlimiet](./logic-apps-limits-and-config.md)van de aanvraag vallen. Als dit probleem optreedt, worden aanvragen mogelijk geblokkeerd. Lees hoe u [uw Logic apps kunt controleren en controleren](../logic-apps/logic-apps-monitor-your-logic-apps.md)voor meer informatie over het vaststellen van problemen.

Gefeliciteerd, u hebt nu een logische app gemaakt die kan communiceren met uw SAP-server. Nu u een SAP-verbinding hebt ingesteld voor uw logische app, kunt u andere beschik bare SAP-acties, zoals BAPI en RFC, verkennen.

## <a name="connector-reference"></a>Connector-verwijzing

Zie deze Naslag informatie voor technische informatie over de connector zoals beschreven door de Swagger-bestanden van de connectors: 

* [SAP-toepassings server](/connectors/sap)
* [SAP-berichten server](/connectors/sap/#send-message-to-sap)

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met on-premises systemen](../logic-apps/logic-apps-gateway-connection.md) vanuit Logic apps
* Meer informatie over het valideren, transformeren en andere bericht bewerkingen met de [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors
