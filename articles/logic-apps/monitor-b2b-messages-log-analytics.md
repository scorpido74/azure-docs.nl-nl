---
title: B2B-berichten bewaken met Azure Monitor
description: Problemen met AS2-, X12- en EDIFACT-berichten oplossen door Azure Monitor-logboeken in te stellen en diagnostische gegevens voor Azure Logic Apps te verzamelen
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907979"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Azure Monitor-logboeken instellen en diagnostische gegevens verzamelen voor B2B-berichten in Azure Logic Apps

Nadat u B2B-communicatie tussen handelspartners hebt ingesteld in uw integratieaccount, kunnen deze partners berichten uitwisselen met protocollen zoals AS2, X12 en EDIFACT. Als u wilt controleren of deze communicatie werkt zoals u verwacht, u [Azure Monitor-logboeken](../azure-monitor/platform/data-platform-logs.md) instellen voor uw integratieaccount. [Azure Monitor](../azure-monitor/overview.md) helpt u uw cloud- en on-premises omgevingen te bewaken, zodat u hun beschikbaarheid en prestaties gemakkelijker behouden. Met Azure Monitor-logboeken u gegevens over runtime-gegevens en -gebeurtenissen opnemen en opslaan, zoals triggergebeurtenissen, uitvoeren van gebeurtenissen en actiegebeurtenissen in een [werkruimte logboekanalyse](../azure-monitor/platform/resource-logs-collect-workspace.md). Voor berichten verzamelt logboekregistratie ook informatie zoals:

* Aantal berichten en status
* Status van bevestigingen
* Correlaties tussen berichten en bevestigingen
* Gedetailleerde foutbeschrijvingen voor fouten

Met Azure Monitor u [logboekquery's](../azure-monitor/log-query/log-query-overview.md) maken om u te helpen deze informatie te vinden en te controleren. U deze diagnostische gegevens ook [gebruiken met andere Azure-services,](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)zoals Azure Storage en Azure Event Hubs.

Als u logboekregistratie voor uw integratieaccount wilt instellen, [installeert u de B2B-oplossing van Logic Apps](#install-b2b-solution) in de Azure-portal. Deze oplossing biedt geaggregeerde informatie voor B2B-berichtgebeurtenissen. Als u vervolgens logboekregistratie en het maken van query's voor deze informatie wilt inschakelen, stelt u [Azure Monitor-logboeken](#set-up-resource-logs)in.

In dit artikel ziet u hoe u Azure Monitor-logboekregistratie inschakelt voor uw integratieaccount.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

* Een Log Analytics-werkruimte. Als u geen werkruimte loganalytics hebt, leest u [hoe u een werkruimte Log Analytics maakt.](../azure-monitor/learn/quick-create-workspace.md)

* Een logische app die is ingesteld met Azure Monitor-logboekregistratie en die informatie naar een log analytics-werkruimte verzendt. Meer informatie over [het instellen van Azure Monitor-logboeken voor uw logische app](../logic-apps/monitor-logic-apps.md).

* Een integratieaccount dat is gekoppeld aan uw logische app. Meer informatie over [het koppelen van uw integratieaccount aan uw logica-app](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Logic Apps B2B-oplossing installeren

Voordat Azure Monitor-logboeken de B2B-berichten voor uw logische app kunnen bijhouden, voegt u de **B2B-oplossing logic apps** toe aan uw log analytics-werkruimte.

1. Voer [Azure portal](https://portal.azure.com)in het zoekvak van `log analytics workspaces`azure portal de optie Log Analytics-werkruimten in en selecteer **vervolgens Logboekanalysewerkruimten**.

   ![Selecteer 'Log Analytics-werkruimten'](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. Selecteer **onder Werkruimten voor Logboekanalyse**uw werkruimte.

   ![Selecteer uw Logboekanalysewerkruimte](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. Selecteer in het deelvenster Overzicht onder **Aan de slag met monitoringoplossingen** > voor logboekanalyse**configureren**de optie **Oplossingen weergeven**.

   ![Selecteer in het deelvenster Overzicht de optie 'Oplossingen weergeven'](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. Selecteer **Toevoegen in**het deelvenster Overzicht .

   ![Voeg in het overzichtsvenster nieuwe oplossing toe](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Nadat de **Marketplace** is geopend, `logic apps b2b`voert u in het zoekvak **Logica-apps B2B**in en selecteer .

   ![Selecteer in Marketplace 'Logic Apps Management'](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. Selecteer in het deelvenster oplossingsbeschrijving de optie **Maken**.

   ![Selecteer 'Maken' om de Oplossing 'Logische apps B2B' toe te voegen](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Controleer en bevestig de werkruimte Log Analytics waar u de oplossing wilt installeren en selecteer Opnieuw **maken.**

   ![Selecteer 'Maken' voor 'Logische apps B2B'](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Nadat Azure de oplossing heeft geïmplementeerd in de Azure-brongroep die uw Log Analytics-werkruimte bevat, wordt de oplossing weergegeven in het overzichtsvenster van uw werkruimte. Wanneer B2B-berichten worden verwerkt, wordt het aantal berichten in dit deelvenster bijgewerkt.

   ![Deelvenster Werkruimteoverzicht](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor-logboeken instellen

U Azure Monitor-logboekregistratie rechtstreeks vanuit uw integratieaccount inschakelen.

1. Zoek en selecteer uw integratieaccount in de [Azure-portal.](https://portal.azure.com)

   ![Uw integratieaccount zoeken en selecteren](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. Selecteer **diagnostische instellingen**in het menu van uw integratieaccount onder **Controle**. Selecteer **Diagnostische instelling toevoegen**.

   ![Selecteer onder 'Controleren' de optie 'Diagnostische instellingen'](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Voer de volgende stappen uit om de instelling te maken:

   1. Geef een naam op voor de instelling.

   1. Selecteer **Verzenden naar logboekanalyse**.

   1. Selecteer **voor Abonnement**het Azure-abonnement dat is gekoppeld aan uw Log Analytics-werkruimte.

   1. Selecteer **voor Log Analytics Workspace**de werkruimte die u wilt gebruiken.

   1. Selecteer **onder logboek**de categorie **IntegrationAccountTrackingEvents,** waarin de gebeurteniscategorie wordt opgegeven die u wilt opnemen.

   1. Selecteer **Opslaan** als u klaar bent.

   Bijvoorbeeld: 

   ![Azure Monitor-logboeken instellen om diagnostische gegevens te verzamelen](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Berichtstatus weergeven

Nadat uw logische app is uitgevoerd, u de status en gegevens over die berichten bekijken in uw werkruimte Log Analytics.

1. Zoek en open uw werkruimte Log Analytics in het zoekvak azure [portal.](https://portal.azure.com)

1. Selecteer in het menu van uw werkruimte De optie**Logica-apps B2B**voor **werkruimteoverzicht** > .

   ![Deelvenster Werkruimteoverzicht](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Als de B2B-tegel Logic Apps niet onmiddellijk resultaten na een run weergeeft, probeert u **Vernieuwen** te selecteren of een korte tijd te wachten voordat u het opnieuw probeert.

   Standaard worden in de **B2B-tegel Logic Apps** gegevens weergegeven op basis van één dag. Als u het gegevensbereik wilt wijzigen in een ander interval, selecteert u het bereikbesturingselement boven aan de pagina:

   ![Interval wijzigen](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Nadat het dashboard met de berichtstatus is weergegeven, u meer details weergeven voor een specifiek berichttype, dat gegevens op basis van één dag weergeeft. Selecteer de tegel voor **AS2,** **X12**of **EDIFACT**.

   ![Statussen weergeven voor berichten](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Er wordt een lijst met berichten weergegeven voor de door u gekozen tegel. Zo ziet een AS2-berichtenlijst er bijvoorbeeld uit:

   ![Statussen en details voor AS2-berichten](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Zie de eigenschappenbeschrijvingen van het bericht voor meer informatie over de eigenschappen voor elk berichttype:

   * [AS2-berichteigenschappen](#as2-message-properties)
   * [Eigenschappen van X12-berichten](#x12-message-properties)
   * [EIGENSCHAPPEN van EDIFACT-berichten](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Eigenschappenbeschrijvingen en naamnotaties voor AS2-, X12- en EDIFACT-berichten

Voor elk berichttype zijn hier de eigenschapsbeschrijvingen en naamindelingen voor gedownloade berichtbestanden.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Beschrijvingen van as2-berichten

Hier zijn de eigenschapbeschrijvingen voor elk AS2-bericht.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Afzender** | De gastpartner die is opgegeven in **Ontvangstinstellingen**of de hostpartner die is opgegeven in **Verzendinstellingen** voor een AS2-overeenkomst |
| **Ontvanger** | De hostpartner die is opgegeven in **Ontvangstinstellingen**of de gastpartner die is opgegeven in **Verzendinstellingen** voor een AS2-overeenkomst |
| **Logische apps** | De logische app waarin de AS2-acties zijn ingesteld |
| **Status** | De AS2-berichtstatus <br>Succes = Ontvangen of verzonden een geldig AS2 bericht. Er is geen MDN ingesteld. <br>Succes = Ontvangen of verzonden een geldig AS2 bericht. MDN is ingesteld en ontvangen, of MDN wordt verzonden. <br>Mislukt = Een ongeldig AS2-bericht ontvangen. Er is geen MDN ingesteld. <br>In behandeling = Ontvangen of verzonden een geldig AS2-bericht. MDN is opgezet en MDN wordt verwacht. |
| **Ack** | De MDN-berichtstatus <br>Geaccepteerd = Ontvangen of verzonden een positieve MDN. <br>In behandeling = Wachten om een MDN te ontvangen of te verzenden. <br>Afgewezen = Ontvangen of verzonden een negatieve MDN. <br>Niet vereist = MDN is niet ingesteld in de overeenkomst. |
| **Richting** | De AS2-berichtrichting |
| **Tracking-id** | De id die alle triggers en acties in een logische app correleert |
| **Bericht-id** | De AS2-bericht-id van de AS2-berichtkoppen |
| **Timestamp** | Het tijdstip waarop de AS2-actie het bericht heeft verwerkt |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Beschrijvingen van de eigenschappen van x12-berichten

Hier zijn de eigenschapbeschrijvingen voor elk X12-bericht.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Afzender** | De gastpartner die is opgegeven in **Ontvangstinstellingen**of de hostpartner die is opgegeven in **Verzendinstellingen** voor een X12-overeenkomst |
| **Ontvanger** | De hostpartner die is opgegeven in **Ontvangstinstellingen**of de gastpartner die is opgegeven in **Verzendinstellingen** voor een X12-overeenkomst |
| **Logische apps** | De logische app waarin de X12-acties zijn ingesteld |
| **Status** | De X12-berichtstatus <br>Succes = Ontvangen of verzonden een geldig X12 bericht. Er is geen functionele ack ingesteld. <br>Succes = Ontvangen of verzonden een geldig X12 bericht. Functionele ack wordt ingesteld en ontvangen, of een functionele ack wordt verzonden. <br>Mislukt = Ontvangen of verzonden een ongeldig X12-bericht. <br>In behandeling = Ontvangen of verzonden een geldig X12-bericht. Functionele ack is opgezet, en een functionele ack wordt verwacht. |
| **Ack** | Functionele Ack (997) status <br>Geaccepteerd = Ontvangen of verzonden een positieve functionele ack. <br>Afgewezen = Ontvangen of verzonden een negatieve functionele ack. <br>In behandeling = Verwacht een functionele ack, maar niet ontvangen. <br>In behandeling = Gegenereerd een functionele ack, maar kan niet verzenden naar partner. <br>Niet vereist = Functionele ack is niet ingesteld. |
| **Richting** | De richting van het X12-bericht |
| **Tracking-id** | De id die alle triggers en acties in een logische app correleert |
| **Msg-type** | Het edi X12-berichttype |
| **Icn** | Het interchange control-nummer voor het X12-bericht |
| **TSCN TSCN** | Het besturingselementnummer voor de transactieset voor het X12-bericht |
| **Timestamp** | Het tijdstip waarop de X12-actie het bericht heeft verwerkt |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Beschrijvingen van edifact-berichten

Hier zijn de eigenschapbeschrijvingen voor elk EDIFACT-bericht.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Afzender** | De gastpartner die is opgegeven in **Ontvangstinstellingen**of de hostpartner die is opgegeven in **Verzendinstellingen** voor een EDIFACT-overeenkomst |
| **Ontvanger** | De hostpartner die is opgegeven in **Ontvangstinstellingen**of de gastpartner die is opgegeven in **Verzendinstellingen** voor een EDIFACT-overeenkomst |
| **Logische apps** | De logische app waarin de EDIFACT-acties zijn ingesteld |
| **Status** | De status van het EDIFACT-bericht <br>Succes = Ontvangen of verzonden een geldig EDIFACT bericht. Er is geen functionele ack ingesteld. <br>Succes = Ontvangen of verzonden een geldig EDIFACT bericht. Functionele ack wordt ingesteld en ontvangen, of een functionele ack wordt verzonden. <br>Mislukt = Ontvangen of verzonden een ongeldig EDIFACT-bericht <br>In behandeling = Ontvangen of verzonden een geldig EDIFACT-bericht. Functionele ack is opgezet, en een functionele ack wordt verwacht. |
| **Ack** | Functionele Ack (CONTRL) status <br>Geaccepteerd = Ontvangen of verzonden een positieve functionele ack. <br>Afgewezen = Ontvangen of verzonden een negatieve functionele ack. <br>In behandeling = Verwacht een functionele ack, maar niet ontvangen. <br>In behandeling = Gegenereerd een functionele ack, maar kan niet verzenden naar partner. <br>Niet vereist = Functionele Ack is niet ingesteld. |
| **Richting** | De richting van het EDIFACT-bericht |
| **Tracking-id** | De id die alle triggers en acties in een logische app correleert |
| **Msg-type** | Het edifact-berichttype |
| **Icn** | Het interchange control number voor het EDIFACT-bericht |
| **TSCN TSCN** | Het besturingselementnummer transactieset voor het EDIFACT-bericht |
| **Timestamp** | Het tijdstip waarop de EDIFACT-actie het bericht verwerkte |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Volgende stappen

* [Bewakings- en traceringsquery's maken](../logic-apps/create-monitoring-tracking-queries.md)