---
title: Logische apps testen met voorbeeldgegevens
description: Statische resultaten instellen voor het testen van logische apps met nepgegevens zonder dat dit gevolgen heeft voor productieomgevingen
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790279"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Logische apps testen met nepgegevens door statische resultaten in te stellen

Wanneer u uw logische apps test, bent u mogelijk om verschillende redenen niet klaar om daadwerkelijk te bellen of toegang te krijgen tot apps, services en systemen. Meestal moet u in deze scenario's verschillende conditiepaden uitvoeren, fouten forceren, specifieke berichtresponsinstanties verstrekken of zelfs enkele stappen proberen over te slaan. Door statische resultaten in te stellen voor een actie in uw logische app, u uitvoergegevens van die actie bespotten. Als u statische resultaten inschakelt voor een actie, wordt de actie niet uitgevoerd, maar worden de nepgegevens geretourneerd.

Als u bijvoorbeeld statische resultaten instelt voor de outlook 365-actie voor het verzenden van e-mail, retourneert de logic-apps-engine alleen de mock-gegevens die u als statische resultaten hebt opgegeven, in plaats van Outlook te bellen en een e-mail te verzenden.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, <a href="https://azure.microsoft.com/free/" target="_blank">registreer u dan nu voor een gratis Azure-account</a>.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waarin u statische resultaten wilt instellen

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Statische resultaten instellen

1. Als u dat nog niet hebt gedaan, opent u in de [Azure-portal](https://portal.azure.com)uw logische app in de Logic Apps Designer.

1. Voer de volgende stappen uit voor de actie waarin u statische resultaten wilt instellen: 

   1. Kies in de rechterbovenhoek van de actie de knop ellipsen (*...*) en selecteer **Statisch resultaat,** bijvoorbeeld:

      ![Selecteer 'Statisch resultaat' > 'Statisch resultaat inschakelen'](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Kies **Statisch resultaat inschakelen**. Geef voor de vereiste eigenschappen (*) de mock-uitvoerwaarden op die u wilt retourneren voor het antwoord van de actie.

      Hier volgen bijvoorbeeld de vereiste eigenschappen voor de HTTP-actie:

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Status** | De status van de actie om terug te keren |
      | **Statuscode** | De specifieke statuscode die moet worden retournerd |
      | **Headers** | De header-inhoud die moet worden retournerd |
      |||

      ![Selecteer 'Statisch resultaat inschakelen'](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Als u de mockgegevens wilt invoeren in de JSON-indeling (JavaScript Object Notation)](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)kiest **u Overschakelen naar JSON-modus** (Switch![to JSON Mode").

   1. Open voor optionele eigenschappen de lijst **Optionele velden selecteren** en selecteer de eigenschappen die u wilt bespotten.

      ![Optionele eigenschappen selecteren](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Wanneer u klaar bent om op te slaan, kiest u **Gereed**.

   In de rechterbovenhoek van de actie toont de titelbalk nu![een pictogram](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)van de testbeker (Pictogram voor statische resultaten), wat aangeeft dat u statische resultaten hebt ingeschakeld.

   ![Pictogram met ingeschakelde statische resultaten](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Zie [Oploopuitvoeringen zoeken waarbij statische resultaten](#find-runs-mock-data) later in dit onderwerp worden gebruikt als u eerdere uitvoeringen wilt vinden die gebruikmaken van nepgegevens.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Eerdere uitvoer opnieuw gebruiken

Als uw logische app een eerdere run heeft uitgevoerd met uitvoer die u opnieuw gebruiken als mock-uitvoer, u de uitvoer van die uitvoer kopiëren en plakken.

1. Als u dat nog niet hebt gedaan, opent u in de [Azure-portal](https://portal.azure.com)uw logische app in de Logic Apps Designer.

1. Selecteer **Overzicht**in het hoofdmenu van uw logische app.

1. Selecteer in de sectie **Geschiedenis uitvoeren** de gewenste logica-app.

1. Zoek en vouw de actie uit die de gewenste uitvoer heeft.

1. Kies de koppeling **Ruwe uitvoer weergeven.**

1. Kopieer het object 'Complete JavaScript Object Notation' (JSON) of de specifieke onderafdeling die u bijvoorbeeld wilt gebruiken, de sectie uitvoer, of zelfs alleen de sectie kopteksten.

1. Volg de stappen voor het openen van het vak **Statisch resultaat** voor uw actie in Statische [resultaten instellen](#set-up-static-results).

1. Nadat het vak **Statisch resultaat** is geopend, kiest u een van beide stappen:

   * Als u een volledig JSON-object wilt![plakken, kiest **u Overschakelen naar JSON-modus** ( Kies 'Overschakelen naar JSON-modus'):](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)

     ![Kies 'Overschakelen naar JSON-modus' voor het volledige object](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Als u alleen een JSON-sectie wilt plakken, kiest u naast het label van die sectie de optie **Overschakelen naar JSON-modus** voor die sectie, bijvoorbeeld:

     ![Kies 'Overschakelen naar JSON-modus' voor uitvoer](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Plak in de JSON-editor uw eerder gekopieerde JSON.

   ![JSON-modus](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Wanneer u klaar bent, kiest u **Gereed**. Of als u wilt terugkeren naar de![ontwerper, kiest](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)u **switch editormode** (Kies 'Switch Editor Mode').

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Runs zoeken die statische resultaten gebruiken

De uitvoeringsgeschiedenis van uw logische app identificeert de uitvoeringen waar de acties statische resultaten gebruiken. Voer de volgende stappen uit om deze runs te vinden:

1. Selecteer **Overzicht**in het hoofdmenu van uw logische app. 

1. Zoek in het rechterdeelvenster onder **Geschiedenis uitzetten**de kolom **Statische resultaten.** 

   Elke run met acties met resultaten heeft de kolom **Statische resultaten** ingesteld op **Ingeschakeld,** bijvoorbeeld:

   ![Geschiedenis uitvoeren - kolom statische resultaten](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Als u acties wilt weergeven die statische resultaten gebruiken, selecteert u de gewenste run waarbij de kolom **Statische resultaten** is ingesteld op **Ingeschakeld**.

   Acties die statische resultaten gebruiken,![tonen het](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)pictogram testbeker ( Pictogram voor statische resultaten), bijvoorbeeld:

   ![Geschiedenis uitvoeren - acties die statische resultaten gebruiken](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Statische resultaten uitschakelen

Het uitschakelen van statische resultaten gooit de waarden van uw laatste installatie niet weg. Dus wanneer u de volgende keer statische resultaten inschakelt, u uw vorige waarden blijven gebruiken.

1. Zoek de actie waar u statische uitvoer wilt uitschakelen. Kies in de rechterbovenhoek van de actie het![pictogram testbeker](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)(Pictogram voor statische resultaten).

   ![Statische resultaten uitschakelen](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Kies **Statisch resultaat** > uitschakelen **.**

   ![Statische resultaten uitschakelen](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Naslaginformatie

Zie [Statische resultaten - Schemaverwijzing voor Werkstroomdefinitietaal](../logic-apps/logic-apps-workflow-definition-language.md#static-results) en [runtimeConfiguration.staticResult - Configuratie-instellingen voor Runtime - Runtime-](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Logic Apps](../logic-apps/logic-apps-overview.md)