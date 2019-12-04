---
title: B2B-berichten bijhouden met Azure Monitor-logboeken
description: B2B-communicatie bijhouden voor integratie accounts en Azure Logic Apps met Azure Log Analytics
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3726b0c8c22614d2acc797295543e69f9358d69c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792928"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>B2B-berichten bijhouden met Azure Monitor-logboeken

Nadat u B2B-communicatie tussen handels partners in uw integratie account hebt ingesteld, kunnen deze partners berichten uitwisselen met protocollen zoals AS2, X12 en EDIFACT. U kunt deze berichten volgen met [Azure monitor-logboeken](../log-analytics/log-analytics-overview.md)om te controleren of deze berichten correct worden verwerkt. U kunt bijvoorbeeld de volgende webgebaseerde tracking mogelijkheden gebruiken voor het bijhouden van berichten:

* Aantal berichten en status
* Status van bevestigingen
* Berichten met bevestigingen correleren
* Gedetailleerde fout beschrijvingen voor fouten
* Zoek mogelijkheden

> [!NOTE]
> Op deze pagina eerder beschreven stappen voor het uitvoeren van deze taken met de Microsoft Operations Management Suite (OMS), die [in januari 2019 worden buiten gebruik gesteld](../azure-monitor/platform/oms-portal-transition.md), worden deze stappen vervangen door Azure log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

* Een logische app die is ingesteld met diagnostische logboek registratie. Meer informatie [over het maken van een logische app](quickstart-create-first-logic-app-workflow.md) en [het instellen van logboek registratie voor die logische app](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Een integratie account dat is ingesteld met bewaking en logboek registratie. Meer informatie [over het maken van een integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) en [het instellen van controle en logboek registratie voor dat account](../logic-apps/logic-apps-monitor-b2b-message.md).

* Als u dat nog niet hebt [gedaan, kunt u Diagnostische gegevens publiceren naar Azure monitor-logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Nadat u aan de vorige vereisten hebt voldaan, hebt u ook een Log Analytics-werk ruimte nodig die u kunt gebruiken voor het volgen van B2B-communicatie via Log Analytics. Als u geen Log Analytics-werk ruimte hebt, leert u [hoe u een log Analytics-werk ruimte maakt](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Logische apps B2B oplossing installeren

Voordat u Azure Monitor Logboeken kunt volgen voor het bijhouden van B2B-berichten voor uw logische app, voegt u de **logische apps B2B** -oplossing toe aan Azure monitor Logboeken. Meer informatie over [het toevoegen van oplossingen aan Azure monitor-logboeken](../azure-monitor/learn/quick-create-workspace.md).

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Alle services**. Zoek in het zoekvak ' Log Analytics ' en selecteer **log Analytics**.

   ![Log Analytics selecteren](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. Zoek en selecteer de Log Analytics-werk ruimte onder **log Analytics**. 

   ![Log Analytics werk ruimte selecteren](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Kies onder aan de **slag met Log Analytics** > **bewakings oplossingen configureren**de optie **oplossingen weer geven**.

   ![Kies oplossingen weer geven](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Kies op de pagina overzicht de optie **toevoegen**, waarmee de lijst met **beheer oplossingen** wordt geopend. Selecteer in de lijst **logische apps B2B**. 

   ![Logische apps B2B oplossing selecteren](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Als u de oplossing niet kunt vinden, kiest u aan de onderkant van de lijst de optie **laden meer** totdat de oplossing wordt weer gegeven.

1. Kies **maken**, bevestig de log Analytics werk ruimte waar u de oplossing wilt installeren en kies vervolgens opnieuw **maken** .   

   ![Kies maken voor Logische apps B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Als u geen bestaande werk ruimte wilt gebruiken, kunt u op dit moment ook een nieuwe werk ruimte maken.

1. Als u klaar bent, gaat u terug naar de pagina **overzicht** van de werk ruimte. 

   De Logische apps B2B-oplossing wordt nu weer gegeven op de pagina overzicht. 
   Wanneer B2B-berichten worden verwerkt, wordt het aantal berichten op deze pagina bijgewerkt.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>B2B-bericht gegevens weer geven

Nadat B2B-berichten zijn verwerkt, kunt u de status en Details voor deze berichten weer geven op de tegel **logische apps B2B** .

1. Ga naar de werk ruimte van de Logic Analytics en open de pagina overzicht. Kies **logische apps B2B**.

   ![Aantal bijgewerkte berichten](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Standaard toont de tegel **logische apps B2B** gegevens op basis van één dag. Als u het gegevens bereik wilt wijzigen in een ander interval, kiest u het bereik besturings element aan de bovenkant van de pagina:
   > 
   > ![Interval wijzigen](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Nadat het dash board bericht status wordt weer gegeven, kunt u meer informatie weer geven voor een specifiek bericht type, waarin gegevens worden weer gegeven op basis van één dag. Kies de tegel voor **AS2**, **X12**of **EDIFACT**.

   ![Bericht status weer geven](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Er wordt een lijst met berichten weer gegeven voor de gekozen tegel. 
   Zie voor meer informatie over de eigenschappen voor elk bericht type deze beschrijving van de bericht eigenschap:

   * [Eigenschappen van AS2-bericht](#as2-message-properties)
   * [Eigenschappen van X12-bericht](#x12-message-properties)
   * [Eigenschappen van EDIFACT-bericht](#EDIFACT-message-properties)

   Zo kan een AS2-berichten lijst er als volgt uitzien:

   ![AS2-berichten weer geven](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Als u de invoer en uitvoer voor specifieke berichten wilt weer geven of exporteren, selecteert u deze berichten en kiest u **downloaden**. Wanneer u hierom wordt gevraagd, slaat u het zip-bestand op uw lokale computer op en pakt u dat bestand uit. 

   De uitgepakte map bevat een map voor elk geselecteerd bericht. 
   Als u bevestigingen instelt, bevat de map bericht ook bestanden met bevestigings gegevens. 
   Elke map van het bericht heeft ten minste de volgende bestanden: 
   
   * Menselijk Lees bare bestanden met de invoer-Payload en uitvoer van de nettolading van de gegevens
   * Versleutelde bestanden met de invoer en uitvoer

   Voor elk bericht type vindt u hier de map-en bestands naam indelingen:

   * [AS2-map en bestands naam indeling](#as2-folder-file-names)
   * [X12-map en bestands naam indeling](#x12-folder-file-names)
   * [EDIFACT-map en bestands naam indeling](#edifact-folder-file-names)

   ![Bericht bestanden downloaden](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Als u alle acties wilt weer geven die dezelfde run-ID hebben, kiest u op de pagina **Zoeken in logboek** een bericht in de berichten lijst.

   U kunt deze acties sorteren op kolom of zoeken naar specifieke resultaten.

   ![Acties met dezelfde run-ID](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Als u resultaten wilt doorzoeken met vooraf opgebouwde query's, kiest u **Favorieten**.

   * Meer informatie [over het maken van query's door filters toe te voegen](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   U kunt ook meer informatie [vinden over het zoeken naar gegevens met logboek zoekopdrachten in azure monitor logs](../log-analytics/log-analytics-log-searches.md).

   * Als u de query in het zoekvak wilt wijzigen, werkt u de query bij met de kolommen en waarden die u wilt gebruiken als filters.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Beschrijvingen van eigenschappen en naam indelingen voor AS2-, X12-en EDIFACT-berichten

Hier volgen de beschrijvingen van eigenschappen en naam indelingen voor gedownloade bericht bestanden voor elk bericht type.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Beschrijvingen van AS2-bericht eigenschappen

Hier volgen de beschrijvingen van de eigenschappen voor elk AS2-bericht.

| Eigenschap | Beschrijving |
| --- | --- |
| Afzender | De gast partner die is opgegeven in de **instellingen voor ontvangen**of de host-partner die is opgegeven in instellingen voor het **verzenden** van een AS2-overeenkomst |
| Ontvanger | De host-partner die is opgegeven in de instellingen voor **ontvangen**of de gast partner die is opgegeven in **instellingen verzenden** voor een AS2-overeenkomst |
| Logische app | De logische app waar de AS2 acties worden ingesteld |
| Status | De status van het AS2-bericht <br>Geslaagd = er is een geldig AS2-bericht ontvangen of verzonden. Er is geen MDN ingesteld. <br>Geslaagd = er is een geldig AS2-bericht ontvangen of verzonden. MDN wordt ingesteld en ontvangen, of MDN wordt verzonden. <br>Mislukt = er is een ongeldig AS2-bericht ontvangen. Er is geen MDN ingesteld. <br>In behandeling = er is een geldig AS2-bericht ontvangen of verzonden. MDN is ingesteld en MDN wordt verwacht. |
| Terug | De status van het MDN-bericht <br>Geaccepteerd = er is een positief MDN ontvangen of verzonden. <br>In behandeling = wachten op ontvangen of verzenden van een MDN. <br>Afgewezen = er is een negatieve MDN ontvangen of verzonden. <br>Niet vereist = MDN is niet ingesteld in de overeenkomst. |
| Richting | De richting van het AS2-bericht |
| Correlatie-id | De ID die overeenkomt met alle triggers en acties in een logische app |
| Bericht-ID | De AS2 bericht-ID van de AS2-bericht koppen |
| Tijdstempel | Het tijdstip waarop de AS2-actie het bericht heeft verwerkt |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>AS2 naam indelingen voor gedownloade bericht bestanden

Dit zijn de naam notaties voor elke gedownloade AS2-bericht map en bestanden.

| Map of bestand | Naam indeling |
| :------------- | :---------- |
| Map bericht | [Sender]\_[receiver]\_AS2\_[correlatie-ID]\_[Message-ID]\_[Time Stamp] |
| Invoer, uitvoer en indien ingesteld, bevestigings bestanden | **Invoer lading**: [Sender]\_[receiver]\_AS2\_[correlatie-ID]\_input_payload. txt </p>**Uitvoer lading**: [Sender]\_[receiver]\_AS2\_[correlatie-ID]\_uitvoer\_payload. txt </p></p>**Invoer**: [Sender]\_[receiver]\_AS2\_[correlatie-ID]\_inputs. txt </p></p>**Uitvoer**: [Sender]\_[receiver]\_AS2\_[correlatie-ID]\_outputs. txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Beschrijvingen van X12-bericht eigenschappen

Hier volgen de beschrijvingen van de eigenschappen voor elk X12-bericht.

| Eigenschap | Beschrijving |
| --- | --- |
| Afzender | De gast partner die is opgegeven in de **instellingen voor ontvangen**of de host-partner die is opgegeven in instellingen voor het **verzenden** van een X12-overeenkomst |
| Ontvanger | De host-partner die is opgegeven in de instellingen voor **ontvangen**of de gast partner die is opgegeven in **instellingen verzenden** voor een X12-overeenkomst |
| Logische app | De logische app waar de X12 acties worden ingesteld |
| Status | De status van het X12-bericht <br>Geslaagd = er is een geldig X12-bericht ontvangen of verzonden. Er is geen functionele ACK ingesteld. <br>Geslaagd = er is een geldig X12-bericht ontvangen of verzonden. Functionele ACK is ingesteld en ontvangen, of er wordt een functionele ACK verzonden. <br>Mislukt = er is een ongeldig X12-bericht ontvangen of verzonden. <br>In behandeling = er is een geldig X12-bericht ontvangen of verzonden. Functionele ACK is ingesteld en er wordt een functionele ACK verwacht. |
| Terug | Status van functionele ACK (997) <br>Geaccepteerd = een positieve functionele Ack ontvangen of verzonden. <br>Afgewezen = er is een negatieve functionele Ack ontvangen of verzonden. <br>In behandeling = er is een functionele ACK verwacht, maar deze is niet ontvangen. <br>In behandeling = er is een functionele ACK gegenereerd, maar er kan niet naar de partner worden verzonden. <br>Niet vereist = functionele ACK is niet ingesteld. |
| Richting | De richting van het X12-bericht |
| Correlatie-id | De ID die overeenkomt met alle triggers en acties in een logische app |
| Msg-type | Het EDI X12-bericht type |
| ICN | Het uitwisselings controle nummer voor het X12-bericht |
| TSCN | Het controle nummer voor de Transactieset voor het X12-bericht |
| Tijdstempel | Het tijdstip waarop de X12-actie het bericht heeft verwerkt |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 naam indelingen voor gedownloade bericht bestanden

Dit zijn de naam notaties voor elke gedownloade X12-bericht map en bestanden.

| Map of bestand | Naam indeling |
| :------------- | :---------- |
| Map bericht | [Sender]\_[receiver]\_X12\_[Interchange Control-Number]\_[Global-Control-Number]\_[Trans Action-Set-Control-Number]\_[Time Stamp] |
| Invoer, uitvoer en indien ingesteld, bevestigings bestanden | **Invoer lading**: [Sender]\_[receiver]\_X12\_[Interchange Control-number]\_input_payload. txt </p>**Uitvoer lading**: [Sender]\_[receiver]\_X12\_[Interchange Control-number]\_output\_payload. txt </p></p>**Invoer**: [Sender]\_[receiver]\_X12\_[Interchange Control-number]\_inputs. txt </p></p>**Uitvoer**: [Sender]\_[receiver]\_X12\_[Interchange Control-number]\_outputs. txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Beschrijvingen van EDIFACT-bericht eigenschappen

Hier volgen de beschrijvingen van de eigenschappen voor elk EDIFACT-bericht.

| Eigenschap | Beschrijving |
| --- | --- |
| Afzender | De gast partner die is opgegeven in de **instellingen voor ontvangen**of de host-partner die is opgegeven in instellingen voor het **verzenden** van een Edifact-overeenkomst |
| Ontvanger | De host-partner die is opgegeven in de instellingen voor **ontvangen**of de gast partner die is opgegeven in **instellingen verzenden** voor een Edifact-overeenkomst |
| Logische app | De logische app waar de EDIFACT acties worden ingesteld |
| Status | De status van het EDIFACT-bericht <br>Geslaagd = er is een geldig EDIFACT-bericht ontvangen of verzonden. Er is geen functionele ACK ingesteld. <br>Geslaagd = er is een geldig EDIFACT-bericht ontvangen of verzonden. Functionele ACK is ingesteld en ontvangen, of er wordt een functionele ACK verzonden. <br>Mislukt = er is een ongeldig EDIFACT-bericht ontvangen of verzonden <br>In behandeling = er is een geldig EDIFACT-bericht ontvangen of verzonden. Functionele ACK is ingesteld en er wordt een functionele ACK verwacht. |
| Terug | Status van functionele ACK (CONTRL) <br>Geaccepteerd = een positieve functionele Ack ontvangen of verzonden. <br>Afgewezen = er is een negatieve functionele Ack ontvangen of verzonden. <br>In behandeling = er is een functionele ACK verwacht, maar deze is niet ontvangen. <br>In behandeling = er is een functionele ACK gegenereerd, maar er kan niet naar de partner worden verzonden. <br>Niet vereist = functionele ACK is niet ingesteld. |
| Richting | De richting van het EDIFACT-bericht |
| Correlatie-id | De ID die overeenkomt met alle triggers en acties in een logische app |
| Msg-type | Het EDIFACT-bericht type |
| ICN | Het uitwisselings controle nummer voor het EDIFACT-bericht |
| TSCN | Het controle nummer voor de Transactieset voor het EDIFACT-bericht |
| Tijdstempel | Het tijdstip waarop de EDIFACT-actie het bericht heeft verwerkt |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>EDIFACT naam indelingen voor gedownloade bericht bestanden

Dit zijn de naam notaties voor elke gedownloade EDIFACT-bericht map en bestanden.

| Map of bestand | Naam indeling |
| :------------- | :---------- |
| Map bericht | [Sender]\_[receiver]\_EDIFACT\_[Interchange Control-Number]\_[Global-Control-Number]\_[Trans Action-Set-Control-Number]\_[Time Stamp] |
| Invoer, uitvoer en indien ingesteld, bevestigings bestanden | **Invoer lading**: [Sender]\_[receiver]\_EDIFACT\_[Interchange Control-number]\_input_payload. txt </p>**Uitvoer lading**: [Sender]\_[receiver]\_EDIFACT\_[Interchange Control-number]\_output\_payload. txt </p></p>**Invoer**: [Sender]\_[receiver]\_EDIFACT\_[Interchange Control-number]\_inputs. txt </p></p>**Uitvoer**: [Sender]\_[receiver]\_EDIFACT\_[Interchange Control-number]\_outputs. txt |
|          |             |

## <a name="next-steps"></a>Volgende stappen

* [Query's uitvoeren voor B2B-berichten in Azure Monitor-logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Volgschema’s voor X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Aangepaste tracking schema's](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
