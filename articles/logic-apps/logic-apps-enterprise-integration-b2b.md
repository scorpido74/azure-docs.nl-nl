---
title: Berichten uitwisselen voor B2B-bedrijfsintegratiescenario's
description: B2B-berichten ontvangen en verzenden tussen handelspartners in Azure Logic Apps met het Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151120"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>B2B-berichten ontvangen en verzenden met Azure Logic Apps en Enterprise Integration Pack

Wanneer u een integratieaccount hebt waarin handelspartners en -overeenkomsten worden gedefinieerd, u een geautomatiseerde B2B-werkstroom (business to business) maken die berichten tussen handelspartners uitwisselt met behulp van [Azure Logic Apps](../logic-apps/logic-apps-overview.md) met het Enterprise Integration [Pack.](../logic-apps/logic-apps-enterprise-integration-overview.md) Azure Logic Apps werkt met connectoren die standaardprotocollen voor AS2, X12, EDIFACT en RosettaNet ondersteunen. U deze connectors ook combineren met andere [connectors die beschikbaar zijn in Logic Apps,](../connectors/apis-list.md)bijvoorbeeld Salesforce en Office 365 Outlook.

In dit artikel ziet u hoe u een logische app maakt die een HTTP-aanvraag ontvangt met behulp van een trigger verzoek, decodeert de inhoud van het bericht met behulp van de as2- en X12-acties en retourneert u vervolgens een antwoord met behulp van de actie Antwoord.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* Een lege logische app, zodat u de B2B-werkstroom maken met behulp van de [trigger Voor het uitvoeren](../connectors/connectors-native-reqres.md) die wordt gevolgd door de volgende acties:

  * [AS2 decoderen](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Voorwaarde](../logic-apps/logic-apps-control-flow-conditional-statement.md), die een [antwoord](../connectors/connectors-native-reqres.md) verzendt op basis van de vraag of de AS2 Decode-actie slaagt of mislukt

  * [X12-bericht decoderen](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-accounts.md) dat is gekoppeld aan uw Azure-abonnement en is gekoppeld aan uw logische app. Zowel uw logische app als integratieaccount moeten op dezelfde locatie of azure-regio bestaan.

* Ten minste twee [handelspartners](../logic-apps/logic-apps-enterprise-integration-partners.md) die u al hebt gedefinieerd in uw integratieaccount, samen met [AS2- en X12-overeenkomsten](logic-apps-enterprise-integration-agreements.md) voor die partners.

## <a name="add-request-trigger"></a>Trigger voor aanvragen toevoegen

In dit voorbeeld wordt de Logic App Designer gebruikt in de Azure-portal, maar u vergelijkbare stappen volgen voor de Logic App Designer in Visual Studio.

1. Open uw lege logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Voer in het `when a http request`zoekvak enter en selecteer **Wanneer een HTTP-aanvraag wordt ontvangen** om als trigger te gebruiken.

   ![Selecteer Trigger aanvragen om de werkstroom van uw logische app te starten](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Laat het **JSON-schema van** de instantie Aanvragen leeg omdat het X12-bericht een plat bestand is.

   ![Laat "Request body JSON Schema" leeg](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Wanneer u klaar bent, selecteert u op de werkbalk van de ontwerper de optie **Opslaan**.

   Met deze stap genereert u de **HTTP POST-URL** die u wilt gebruiken voor het verzenden van het verzoek dat de logische app activeert. Als u deze URL wilt kopiëren, selecteert u het kopieerpictogram naast de URL.

   ![URL gegenereerd voor trigger request om oproepen te ontvangen](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>As2-decodeactie toevoegen

Voeg nu de B2B-acties toe die u wilt gebruiken. In dit voorbeeld worden AS2- en X12-acties gebruikt.

1. Selecteer Onder de trigger de optie **Nieuwe stap**. Als u de triggergegevens wilt verbergen, klikt u op de titelbalk van de trigger.

   ![Een volgende stap toevoegen aan de werkstroom van uw logische app](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. Voer **onder Een actie kiezen**in `as2 decode`het zoekvak **AS2 Decoderen (v2)** in en selecteer .

   ![Zoeken en selecteren "AS2 Decode (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Voer **de** invoer in die de AS2-actie moet decoderen, de `body` inhoud die wordt ontvangen door de HTTP-aanvraagtrigger. U deze inhoud op meerdere manieren opgeven als invoer, hetzij in de lijst met dynamische inhoud, hetzij als expressie:

   * Als u wilt kiezen uit een lijst met de beschikbare triggeruitvoer, klikt u in het **vak Bericht om te decoderen.** Nadat de lijst met dynamische inhoud is weergegeven, selecteert u onder **Wanneer een HTTP-aanvraag is ontvangen,** de eigenschapswaarde **Van lichaam,** bijvoorbeeld:

     ![De waarde 'Hoofdlichaam' selecteren in trigger](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Als u een expressie wilt invoeren `body` die verwijst naar de uitvoer van de trigger, klikt u in het **vak Bericht om te decoderen.** Nadat de lijst met dynamische inhoud is weergegeven, selecteert u **Expressie**. Voer in de expressieeditor de expressie hier in en selecteer **OK:**

     `triggerOutputs()['body']`

     Of voer in het vak **Bericht om te decoderen** deze expressie rechtstreeks in:

     `@triggerBody()`

     De expressie wordt opgelost in het **teken Van lichaam.**

     ![Resolved body output from trigger Resolved body output from trigger Resolved body output from trigger Resolved](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Voer voor de eigenschap **Message-headers** alle kopteksten in die `headers` nodig zijn voor de AS2-actie, die worden beschreven door de inhoud die wordt ontvangen door de HTTP-aanvraagtrigger.

   Als u een expressie wilt invoeren `headers` die verwijst naar de uitvoer van de trigger, klikt u in het vak **Berichtkoppen.** Nadat de lijst met dynamische inhoud is weergegeven, selecteert u **Expressie**. Voer in de expressieeditor de expressie hier in en selecteer **OK:**

   `triggerOutputs()['Headers']`

   Als u deze expressie wilt oplossen als dit token, schakelt u tussen de ontwerper en de codeweergave, bijvoorbeeld:

   ![Resolved headers output from trigger Resolved headers output from trigger Resolved headers output from trigger Resolved](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Reactieactie toevoegen voor berichtontvangstmelding

Als u de handelspartner wilt laten weten dat het bericht is ontvangen, u een antwoord retourneren dat een AS2 Message Disposition Notification (MDN) bevat met behulp van de **actie Antwoord.** Door deze actie direct na de **AS2 Decode-actie** toe te voegen, als die actie mislukt, gaat de logische app niet verder met de verwerking.

1. Selecteer onder de actie **AS2 Decoderen** de optie **Nieuwe stap**.

1. Selecteer onder **Een actie kiezen**onder het zoekvak de optie **Ingebouwd**. Typ `condition` in het zoekvak. Selecteer **Voorwaarde**in de lijst **Acties** .

   ![De actie Voorwaarde toevoegen](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Nu wordt de voorwaardevorm weergegeven, inclusief de paden voor of aan de voorwaarde is voldaan of niet.

   ![Voorwaardevorm met beslissingspaden](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Geef nu de voorwaarde op om te evalueren. Voer **in** het vak Een waarde kiezen de als volgt uit:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Controleer in het middelste vak of de `is equal to`vergelijkingsbewerking is ingesteld op . Voer in het vak aan `Expected`de rechterkant de waarde in. Als u de expressie wilt oplossen als dit token, schakelt u tussen de ontwerper en de codeweergave.

   ![Voorwaardevorm met beslissingspaden](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Geef nu de antwoorden op om terug te keren of de **as2-decode-actie** slaagt of niet.

   1. Voor het geval dat de actie **AS2 Decoderen** slaagt, selecteert u in de ware vorm **Als** de optie **Een actie toevoegen**. Voer **onder Een actie kiezen**in `response`het zoekvak in en selecteer **Antwoord**.

      ![De actie 'Reactie' zoeken en selecteren](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Als u toegang wilt krijgen tot de AS2 MDN vanuit de uitvoer van de **as2-decoderen,** geeft u de volgende expressies op:

      * Voer **in** de eigenschap **Headers** van de actie Koppen van de actie Antwoord de als volgt te komen:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Voer **in** de eigenschap Body van de actie **Antwoord** de als volgt te komen:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Als u de expressies wilt oplossen als tokens, schakelt u tussen de ontwerper en de codeweergave:

      ![Opgeloste expressie om toegang te krijgen tot AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Voor het geval dat de actie **AS2 Decoderen** mislukt, selecteert u in de **foute** vorm Als **een actie toevoegen**. Voer **onder Een actie kiezen**in `response`het zoekvak in en selecteer **Antwoord**. Stel de **actie Antwoord** in om de gewenste status en fout terug te sturen.

1. Sla uw logische app op.

## <a name="add-decode-x12-message-action"></a>Actie X12-bericht decoderen toevoegen

1. Voeg nu de actie **X12-bericht decoderen** toe. Selecteer **onder** de actie Antwoord de optie **Een actie toevoegen**.

1. Voer **onder Een actie kiezen**in `x12 decode`het zoekvak in en selecteer **X12-bericht decoderen**.

   ![Actie 'X12-bericht decoderen' zoeken en selecteren](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Als de X12-actie u om verbindingsgegevens vraagt, geeft u de naam voor de verbinding op, selecteert u het integratieaccount dat u wilt gebruiken en selecteert u **Vervolgens Maken**.

   ![X12-verbinding maken met integratieaccount](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Geef nu de invoer voor de X12-actie op. In dit voorbeeld wordt de uitvoer van de AS2-actie gebruikt, de inhoud van het bericht, maar houd er rekening mee dat deze inhoud in JSON-objectindeling is en base64 gecodeerd is. Je moet deze inhoud dus converteren naar een tekenreeks.

   Voer in het **vak X12 Flat-bestands dat u wilt decoderen** deze expressie in om de AS2-uitvoer om te zetten:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Als u de expressie wilt oplossen als dit token, schakelt u tussen de ontwerper en de codeweergave.

    ![Basis64-gecodeerde inhoud converteren naar een tekenreeks](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Sla uw logische app op.

   Als u extra stappen nodig hebt voor deze logische app, bijvoorbeeld om de inhoud van het bericht te decoderen en die inhoud uit te voeren in json-objectindeling, gaat u verder met het bouwen van uw logische app.

U bent nu klaar met het instellen van uw B2B-logica-app. In een echte wereld-app u de gedecodeerde X12-gegevens opslaan in een LOB-app of gegevenswinkel (line-of-business) (LINE-of-Business). Zie bijvoorbeeld de volgende artikelen:

* [Verbinding maken met SAP-systemen in Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [SFTP-bestanden bewaken, maken en beheren met SSH en Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Als u uw eigen LOB-apps wilt verbinden en deze API's in uw logische app wilt gebruiken, u meer acties toevoegen of [aangepaste API's schrijven.](../logic-apps/logic-apps-create-api-app.md)

## <a name="next-steps"></a>Volgende stappen

* [Binnenkomende HTTPS-oproepen ontvangen en beantwoorden](../connectors/connectors-native-reqres.md)
* [Exchange AS2-berichten voor B2B-bedrijfsintegratie](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Exchange X12-berichten voor B2B-bedrijfsintegratie](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Meer informatie over het [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)