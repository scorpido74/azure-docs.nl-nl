---
title: Verbinding maken met Bing Search
description: Taken en werkstromen automatiseren die resultaten in Bing Search vinden met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: e547ae59f7b3260f46756825bca2bef1c10bcc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665884"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Resultaten zoeken in Bing Search met Azure Logic Apps

In dit artikel ziet u hoe u nieuws, video's en andere items vinden via Bing Search vanuit een logische app met de Bing Search-connector. Op die manier u logische apps maken die taken en werkstromen automatiseren voor het verwerken van zoekresultaten en deze items beschikbaar maken voor andere acties. 

U bijvoorbeeld nieuwsberichten vinden op basis van zoekcriteria en Twitter deze items als tweets in uw Twitter-feed laten plaatsen.

Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Zie de verwijzing naar de [Bing-connectorvoor](https://docs.microsoft.com/connectors/bingsearch/)connector voor specifieke technische informatie over connectoren .

## <a name="prerequisites"></a>Vereisten

* Een [Cognitive Services-account](../cognitive-services/cognitive-services-apis-create-account.md)

* Een [Bing Search API-toets](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), die toegang biedt vanuit uw logische app tot de Bing Search API's

* De logische app waar u toegang wilt krijgen tot uw gebeurtenishub. Als u uw logische app wilt starten met een Trigger voor Zoeken in Bing, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Een Trigger voor Zoeken naar Bing toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een specifieke gebeurtenis plaatsvindt of wanneer aan een specifieke voorwaarde is voldaan. Elke keer dat de trigger wordt geactiveerd, maakt de Logic Apps-engine een logische app-instantie en wordt de werkstroom van uw app gestart.

1. Maak in de Azure-portal of Visual Studio een lege logische app, waarmee Logic App Designer wordt geopend. In dit voorbeeld wordt de Azure-portal gebruikt.

2. Voer in het zoekvak 'Bing-zoeken' in als filter. Selecteer in de lijst triggers de gewenste trigger.

   In dit voorbeeld wordt deze trigger gebruikt: **Bing Search - In een nieuw nieuwsartikel**

   ![Trigger voor Zoeken naar Bing-zoekopdrachten](./media/connectors-create-api-bing-search/add-trigger.png)

3. Als u wordt gevraagd om verbindingsgegevens, [maakt u nu uw Bing Search-verbinding](#create-connection).
Of, als uw verbinding al bestaat, de nodige informatie voor de trigger.

   Geef in dit voorbeeld criteria op voor het retourneren van overeenkomende nieuwsartikelen van Bing Search.

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | Zoeken naar zoekopdracht | Ja | <*zoekwoorden*> | Voer de zoekwoorden in die u wilt gebruiken. |
   | Markt | Ja | <*Locale*> | De zoekplaats. De standaardinstelling is 'en-US', maar u een andere waarde selecteren. |
   | Veilig zoeken | Ja | <*zoekniveau*> | Het filterniveau voor het uitsluiten van inhoud voor volwassenen. De standaardinstelling is 'Matig', maar u selecteert een ander niveau. |
   | Count | Nee | <*aantal resultaten*> | Geef het opgegeven aantal resultaten terug. De standaardwaarde is 20, maar u een andere waarde opgeven. Het werkelijke aantal geretourneerde resultaten is mogelijk kleiner dan het opgegeven aantal. |
   | Offset | Nee | <*skip-value*> | Het aantal resultaten dat moet worden overgeslagen voordat de resultaten worden retournerd |
   |||||

   Bijvoorbeeld:

   ![Trigger instellen](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Selecteer het interval en de frequentie voor hoe vaak u wilt dat de trigger controleert op resultaten.

5. Wanneer u klaar bent, selecteert u op de werkbalk van de ontwerper de optie **Opslaan**.

6. Ga nu verder met het toevoegen van een of meer acties aan uw logische app voor de taken die u wilt uitvoeren met de triggerresultaten.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Een actie Bing-zoekopdracht toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werkstroom die een trigger of een andere actie volgt. In dit voorbeeld begint de logische app met een Bing Search-trigger die nieuwsartikelen retourneert die overeenkomen met de opgegeven criteria.

1. Open uw logische app in de Azure-portal of Visual Studio in Logic App Designer. In dit voorbeeld wordt de Azure-portal gebruikt.

2. Selecteer onder de trigger of actie De optie **Nieuwe stap** > **Een actie toevoegen**.

   In dit voorbeeld wordt de als volgt gebruikt:

   **Bing Search - Op nieuw nieuwsartikel**

   ![Actie toevoegen](./media/connectors-create-api-bing-search/add-action.png)

   Als u een actie tussen bestaande stappen wilt toevoegen, verplaatst u de muis over de verbindingspijl. 
   Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

3. Voer in het zoekvak 'Bing-zoeken' in als filter.
Selecteer in de lijst met acties de gewenste actie.

   In dit voorbeeld wordt de actie gebruikt:

   **Bing Zoeken - Nieuws per query weergeven**

   ![Zoeken in Bing zoeken zoeken](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Als u wordt gevraagd om verbindingsgegevens, [maakt u nu uw Bing Search-verbinding](#create-connection). Of, als uw verbinding al bestaat, de nodige informatie voor de actie.

   Geef in dit voorbeeld de criteria op voor het retourneren van een subset van de resultaten van de trigger.

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | Zoeken naar zoekopdracht | Ja | <*zoekexpressie*> | Voer een expressie in voor het opvragen van de triggerresultaten. U kiezen uit de velden in de lijst met dynamische inhoud of een expressie maken met de opbouwfunctie voor expressies. |
   | Markt | Ja | <*Locale*> | De zoekplaats. De standaardinstelling is 'en-US', maar u een andere waarde selecteren. |
   | Veilig zoeken | Ja | <*zoekniveau*> | Het filterniveau voor het uitsluiten van inhoud voor volwassenen. De standaardinstelling is 'Matig', maar u selecteert een ander niveau. |
   | Count | Nee | <*aantal resultaten*> | Geef het opgegeven aantal resultaten terug. De standaardwaarde is 20, maar u een andere waarde opgeven. Het werkelijke aantal geretourneerde resultaten is mogelijk kleiner dan het opgegeven aantal. |
   | Offset | Nee | <*skip-value*> | Het aantal resultaten dat moet worden overgeslagen voordat de resultaten worden retournerd |
   |||||

   Stel dat u de resultaten wilt waarvan de categorienaam het woord 'tech' bevat.

   1. Klik in het vak **Zoekquery** zodat de lijst met dynamische inhoud wordt weergegeven. 
   Selecteer **expressie** in die lijst zodat de opbouwfunctie voor expressie wordt weergegeven. 

      ![Trigger voor Bing-zoekopdrachten](./media/connectors-create-api-bing-search/bing-search-action.png)

      Nu u beginnen met het maken van uw expressie.

   2. Selecteer in de lijst met functies de functie **bevat()** die vervolgens wordt weergegeven in het expressievak. Klik op **Dynamische inhoud** zodat de veldenlijst opnieuw wordt weergegeven, maar zorg ervoor dat de cursor in de haakjes blijft.

      ![Selecteer een functie](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Selecteer in de veldenlijst **Categorie**, die wordt omgezet in een parameter. 
   Voeg een komma toe na de eerste parameter en voeg na de komma het thema toe:`'tech'` 

      ![Een veld selecteren](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Wanneer u gereed bent, selecteert u **OK**.

      De expressie wordt nu weergegeven in het vak **Zoekopdracht in** deze indeling:

      ![Voltooide expressie](./media/connectors-create-api-bing-search/resolved-expression.png)

      In de codeweergave wordt deze expressie in deze indeling weergegeven:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Wanneer u klaar bent, selecteert u op de werkbalk van de ontwerper de optie **Opslaan**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Verbinding maken met Bing Search

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Wanneer u om verbindingsgegevens wordt gevraagd, geeft u de volgende gegevens op:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | Verbindingsnaam | Ja | <*verbindingsnaam*> | De naam die u wilt maken voor uw verbinding |
   | API-versie | Ja | <*API-versie*> | Standaard is de Bing Search API-versie ingesteld op de huidige versie. U indien nodig een eerdere versie selecteren. |
   | API-sleutel | Ja | <*API-toets*> | De Bing Search API-toets die u eerder hebt gekregen. Als u geen sleutel hebt, haalt u nu uw [API-sleutel](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)op. |  
   |||||  

   Bijvoorbeeld:

   ![Verbinding maken](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Als u gereed bent, selecteert u **Maken**.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](/connectors/bingsearch/)de connector voor technische details, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
