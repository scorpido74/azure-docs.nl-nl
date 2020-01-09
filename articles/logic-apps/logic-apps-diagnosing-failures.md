---
title: Problemen met werk stromen oplossen en fouten opsporen
description: Meer informatie over het oplossen en diagnosticeren van problemen, fouten en fouten in uw werk stromen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 79cc9d1bf7aa9e8848197525646b0a3646a558d2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666802"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Problemen met werk stromen oplossen en fouten opsporen in Azure Logic Apps

Uw logische app genereert informatie die u kan helpen bij het vaststellen en oplossen van problemen in uw app. U kunt een logische app diagnosticeren door elke stap in de werk stroom te bekijken via de Azure Portal. U kunt ook enkele stappen toevoegen aan een werk stroom voor runtime-fout opsporing.

## <a name="review-trigger-history"></a>Trigger geschiedenis controleren

Elke logische app begint met trigger. Als de trigger niet wordt gestart, controleert u eerst de trigger geschiedenis. Deze geschiedenis bevat een lijst met alle trigger pogingen die uw logische app heeft gemaakt en informatie over invoer en uitvoer voor elke trigger poging.

1. Als u wilt controleren of de trigger is geactiveerd, kiest u **overzicht**in het menu van de logische app. Controleer onder **trigger geschiedenis**de status van de trigger.

   > [!TIP]
   > Als u het menu van de logische app niet ziet, probeer dan terug te gaan naar het Azure-dashboard. Open uw logische app dan opnieuw.

   ![Trigger geschiedenis controleren](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Als u de verwachte gegevens niet kunt vinden, kunt u op de werk balk op **vernieuwen** klikken.
   > * Als in de lijst veel trigger pogingen worden weer gegeven en u het gewenste item niet kunt vinden, kunt u de lijst filteren.

   Hier volgen de mogelijke statussen voor een trigger poging:

   | Status | Beschrijving | 
   | ------ | ----------- | 
   | **Geslaagd** | De trigger heeft het eind punt gecontroleerd en beschik bare gegevens gevonden. Normaal gesp roken wordt naast deze status ook de status ' geactiveerd ' weer gegeven. Als dat niet het geval is, kan de definitie van de trigger een voor waarde of `SplitOn` opdracht hebben die niet is voldaan. <p>Deze status kan van toepassing zijn op een hand matige trigger, een herhalings trigger of een polling-trigger. Een trigger kan worden uitgevoerd, maar de uitvoeringsrun zelf kan echter nog steeds mislukken wanneer de acties onverwerkte fouten genereren. | 
   | **Overgeslagen** | De trigger heeft het eind punt gecontroleerd, maar er zijn geen gegevens gevonden. | 
   | **Mislukt** | Er is een fout opgetreden. Als u gegenereerde fout berichten voor een mislukte trigger wilt controleren, selecteert u de trigger poging en kiest u **uitvoer**. U kunt bijvoorbeeld invoer zoeken die niet geldig is. | 
   ||| 

   Mogelijk hebt u meerdere trigger vermeldingen met dezelfde datum en tijd, wat er gebeurt wanneer uw logische app meerdere items vindt. 
   Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-Engine een exemplaar van een logische app om uw werk stroom uit te voeren. Elk exemplaar wordt standaard parallel uitgevoerd, zodat er geen werk stroom moet worden gewacht voordat een uitvoering wordt gestart.

   > [!TIP]
   > U kunt de trigger opnieuw controleren zonder te wachten op het volgende terugkeer patroon. Kies op de werk balk overzicht de optie **trigger uitvoeren**en selecteer de trigger die een controle afdwingt. U kunt ook **uitvoeren** op de werk balk van Logic apps Designer selecteren.

3. Als u de Details voor een trigger poging wilt controleren, selecteert u in trigger **geschiedenis**de trigger poging. 

   ![Een trigger poging selecteren](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Controleer de invoer en eventuele uitvoer die de trigger heeft gegenereerd. Trigger uitvoer geeft de gegevens weer die afkomstig zijn van de trigger. Deze uitvoer kan u helpen bepalen of alle eigenschappen worden geretourneerd zoals verwacht.

   > [!NOTE]
   > Als u inhoud vindt die u niet begrijpt, kunt u lezen hoe Azure Logic Apps [verschillende inhouds typen verwerkt](../logic-apps/logic-apps-content-type.md).

   ![Trigger uitvoer](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Uitvoeringsgeschiedenis controleren

Elke geactiveerde trigger start een uitvoering van de werk stroom. U kunt zien wat er is gebeurd tijdens de uitvoering, inclusief de status van elke stap in de werk stroom, plus de invoer en uitvoer voor elke stap.

1. Kies **Overzicht** in het menu van de logische app. Controleer onder uitvoerings **geschiedenis**de uitvoering van de geactiveerde trigger.

   > [!TIP]
   > Als u het menu van de logische app niet ziet, probeer dan terug te gaan naar het Azure-dashboard. Open uw logische app dan opnieuw.

   ![Geschiedenis van uitvoeringen controleren](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Als u de verwachte gegevens niet kunt vinden, kunt u op de werk balk op **vernieuwen** klikken.
   > * Als in de lijst veel uitvoeringen worden weer gegeven en u het gewenste item niet kunt vinden, kunt u de lijst filteren.

   Hier volgen de mogelijke statussen voor een uitvoering:

   | Status | Beschrijving | 
   | ------ | ----------- | 
   | **Geslaagd** | Alle acties zijn voltooid. <p>Als er fouten zijn opgetreden in een specifieke actie, wordt die fout door een volgende actie in de werk stroom verwerkt. | 
   | **Mislukt** | Ten minste één actie is mislukt en er zijn geen latere acties in de werk stroom ingesteld om de fout te verwerken. | 
   | **Gevraagd** | De werk stroom is uitgevoerd, maar er is een annulerings aanvraag ontvangen. | 
   | **Wordt uitgevoerd** | De werk stroom wordt momenteel uitgevoerd. <p>Deze status kan voor een beperkt aantal werk stromen optreden, of vanwege het huidige prijs plan. Zie de [pagina met prijzen voor de actie limieten](https://azure.microsoft.com/pricing/details/logic-apps/)voor meer informatie. Als u [Diagnostische logboek registratie](../logic-apps/logic-apps-monitor-your-logic-apps.md)instelt, kunt u ook informatie ophalen over eventuele vertragings gebeurtenissen die plaatsvinden. | 
   ||| 

2. Bekijk de details van elke stap in een specifieke uitvoering. Selecteer onder uitvoerings **geschiedenis**de uitvoering die u wilt onderzoeken.

   ![Geschiedenis van uitvoeringen controleren](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Of de uitvoering zelf is geslaagd of mislukt, de weer gave uitvoerings Details toont elke stap en of deze al dan niet is geslaagd of mislukt.

   ![Details bekijken van een run van een logische app](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Als u de invoer, uitvoer en eventuele fout berichten voor een specifieke stap wilt onderzoeken, kiest u die stap zodat de shape wordt uitgebreid en de details worden weer gegeven. Bijvoorbeeld:

   ![Details van de stappen bekijken](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Runtime-fout opsporing uitvoeren

Als u hulp nodig hebt bij het opsporen van fouten, kunt u diagnostische stappen toevoegen aan een werk stroom en de geschiedenis van de trigger en uitvoeringen bekijken. U kunt bijvoorbeeld stappen toevoegen die gebruikmaken van de [webhook tester](https://webhook.site/) -service, zodat u HTTP-aanvragen kunt inspecteren en de exacte grootte, vorm en indeling bepaalt.

1. Ga naar de [webhook-tester](https://webhook.site/) en kopieer de unieke URL die is gemaakt

2. Voeg in uw logische app een HTTP POST-actie toe met de inhoud van de hoofd tekst die u wilt testen, bijvoorbeeld een expressie of een andere stap uitvoer.

3. Plak de URL voor uw webhook-tester in de actie HTTP POST.

4. Als u wilt zien hoe een aanvraag wordt gevormd wanneer deze wordt gegenereerd vanuit de Logic Apps-Engine, voert u de logische app uit en raadpleegt u webhook tester voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Uw logische app bewaken](../logic-apps/logic-apps-monitor-your-logic-apps.md)
