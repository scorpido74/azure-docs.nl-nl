---
title: Problemen met throttling afhandelen, of fouten '429 - Te veel aanvragen'
description: Problemen met beperking oplossen of fouten met 'HTTP 429 Te veel aanvragen' in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272675"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Beperkingsproblemen verwerken (429 - fouten met te veel aanvragen) in Azure Logic Apps

In [Azure Logic Apps](../logic-apps/logic-apps-overview.md)retourneert uw logische app een fout ['HTTP 429 Te veel aanvragen'](https://developer.mozilla.org/docs/Web/HTTP/Status/429) bij het ervaren van beperking, wat gebeurt wanneer het aantal aanvragen hoger is dan de snelheid waarmee de bestemming gedurende een bepaalde tijd kan worden verwerkt. Beperking kan problemen veroorzaken, zoals vertraagde gegevensverwerking, lagere prestatiesnelheid en fouten zoals het overschrijden van het opgegeven beleid voor nieuwe inspanningen.

![Beperking in SQL Server-connector](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Hier volgen enkele veelvoorkomende typen beperking die uw logica-app kan ervaren:

* [Logische app](#logic-app-throttling)
* [Connector](#connector-throttling)
* [Bestemmingsservice of -systeem](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Logische app-beperking

De Azure Logic Apps-service heeft zijn eigen [doorvoerlimieten.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Als uw logica-app deze limieten overschrijdt, wordt uw logische app-bron dus beperkt, niet alleen een specifieke instantie of wordt uitgevoerd.

Als u beperkingsgebeurtenissen op dit niveau wilt vinden, controleert u het deelvenster **Metrische gegevens van** uw logische app in de Azure-portal.

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Selecteer in het menu logica-app onder **Controleren**de optie **Metrische gegevens**.

1. Selecteer **onder Grafiektitel**De optie **Metrische toevoegen** zodat u een andere statistiek aan het bestaande toevoegt.

1. Selecteer op de eerste metrische balk in de **lijst MET METRISCHE** gegevens **actiebeperkende gebeurtenissen**. Selecteer in de tweede metrische balk in de **lijst MET METRISCHE** gegevens de optie Gebeurtenissen met **terugzetten**.

Als u beperking op dit niveau wilt verwerken, hebt u de volgende opties:

* Beperk het aantal logische app-exemplaren dat tegelijkertijd kan worden uitgevoerd.

  Als de triggervoorwaarde van uw logische app standaard meer dan één keer tegelijk is vervuld, worden meerdere triggerexemplaren voor uw logische app gelijktijdig of *parallel*uitgevoerd. Dit gedrag betekent dat elke triggerinstantie wordt geactiveerd voordat de vorige werkstroominstantie is uitgevoerd.

  Hoewel het standaardaantal triggerexemplaren dat gelijktijdig kan worden uitgevoerd [onbeperkt](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)is, u dit aantal beperken door [de gelijktijdigheidsinstelling](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)van de trigger in te schakelen en, indien nodig, een andere limiet dan de standaardwaarde selecteren.

* Schakel de modus Met hoge doorvoer in.

  Een logische app heeft een [standaardlimiet voor het aantal acties dat kan worden uitgevoerd over een rolinterval van 5 minuten.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Als u deze limiet wilt verhogen tot het maximum aantal acties, schakelt u [de modus Hoge doorvoer](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) in in uw logische app.

* Het gedrag van arraydebatching ('opgesplitst in') uitschakelen in triggers.

  Als een trigger een array retourneert voor de resterende werkstroomacties die moeten worden verwerkt, splitst de instelling [ **van de** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) trigger de arrayitems op en start een werkstroomexemplaar voor elk arrayitem, waardoor in feite meerdere gelijktijdige uitvoeringen worden geactiveerd tot de [ **limiet splitsen** ](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Als u beperking wilt beheren, schakelt u het gedrag **Splitsen op** uit en laat u uw logische app de hele array verwerken met één gesprek, in plaats van één item per gesprek af te handelen.

* Refactor acties in kleinere logica apps.

  Zoals eerder vermeld, is een logische app beperkt tot een [standaard aantal acties die over een periode van 5 minuten kunnen worden uitgevoerd.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Hoewel u deze limiet verhogen door [een hoge doorvoermodus](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)in te schakelen, u ook overwegen of u de acties van uw logische app wilt opsplitsen in kleinere logische apps, zodat het aantal acties dat in elke logische app wordt uitgevoerd, onder de limiet blijft. Op die manier vermindert u de belasting van één logische app-bron en verdeelt u de belasting over meerdere logische apps. Deze oplossing werkt beter voor acties die grote gegevenssets verwerken of zoveel gelijktijdig lopende acties, loopiteraties of acties binnen elke lusiteratie uitvoeren dat ze de limiet voor actieuitvoering overschrijden.

  Deze logische app doet bijvoorbeeld al het werk om tabellen uit een SQL Server-database te halen en krijgt de rijen uit elke tabel. De actie **Voor elke** lus wordt gelijktijdig door elke tabel herhaald, zodat de rijen voor elke tabel met de actie **Rijen weergeven** wordt geretourneerd. Op basis van de hoeveelheden gegevens in die tabellen kunnen deze acties de limiet voor actieuitvoeringen overschrijden.

  ![Logica app "voor" refactoring](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Na refactoring is de logische app nu een app voor bovenliggende en onderliggende logica. De ouder haalt de tabellen uit SQL Server en roept vervolgens een onderliggende logica-app aan voor elke tabel om de rijen te krijgen:

  ![Logische app maken voor één actie](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Hier is de app onderliggende logica die wordt aangeroepen door de bovenliggende logica-app om de rijen voor elke tabel op te halen:

  ![Een andere logische app maken voor een tweede actie](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Beperking van connectoren

Elke connector heeft zijn eigen beperkingslimieten, die u vinden op de technische referentiepagina van de connector. De Azure [Service Bus-connector](https://docs.microsoft.com/connectors/servicebus/) heeft bijvoorbeeld een beperkingslimiet die maximaal 6.000 aanroepen per minuut toestaat, terwijl de SQL Server-connector [beperkingslimieten heeft die variëren op basis van het bewerkingstype.](https://docs.microsoft.com/connectors/sql/)

Sommige triggers en acties, zoals HTTP, hebben een ['beleid voor opnieuw proberen'](../logic-apps/logic-apps-exception-handling.md#retry-policies) dat u aanpassen op basis van de [beleidslimieten voor het opnieuw proberen](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) om de verwerking van uitzonderingen te implementeren. Dit beleid geeft aan of en hoe vaak een trigger of actie een aanvraag opnieuw indient wanneer de oorspronkelijke aanvraag mislukt of een keer uitvalt en resulteert in een respons van 408, 429 of 5xx. Dus, wanneer beperking begint en een 429-fout retourneert, volgt Logic Apps het beleid voor opnieuw proberen waar dit wordt ondersteund.

Als u wilt weten of een trigger of actie het beleid voor nieuwe try's ondersteunt, controleert u de instellingen van de trigger of actie. Als u de pogingen van een trigger of actie opnieuw wilt bekijken, gaat u naar de uitvoeringsgeschiedenis van uw logische app, selecteert u de uitvoering die u wilt bekijken en vouwt u die trigger of actie uit om details over invoer, uitvoer en eventuele nieuwe pogingen weer te geven, bijvoorbeeld:

![De runsgeschiedenis, nieuwe pogingen, invoer en uitvoer van de actieweergeven](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Hoewel de geschiedenis opnieuw proberen foutinformatie biedt, u problemen hebben met het onderscheiden tussen beperking van de connector en [doelbeperking.](#destination-throttling) In dit geval moet u mogelijk de gegevens van het antwoord bekijken of bepaalde intervalberekeningen uitvoeren om de bron te identificeren.

Voor logische apps in de wereldwijde Azure Logic Apps-service met meerdere tenant's vindt beperking plaats op *verbindingsniveau.* Dus, bijvoorbeeld, voor logische apps die worden uitgevoerd in een [integratie service omgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), throttling nog steeds gebeurt voor niet-ISE-verbindingen, omdat ze worden uitgevoerd in de wereldwijde, multi-tenant Logic Apps service. ISE-verbindingen, die zijn gemaakt door ISE-connectors, worden echter niet beperkt omdat ze in uw ISE worden uitgevoerd.

Als u beperking op dit niveau wilt verwerken, hebt u de volgende opties:

* Stel meerdere verbindingen in voor één actie, zodat de logische app de gegevens voor verwerking partities.

  Overweeg voor deze optie of u de werkbelasting distribueren door de aanvragen van een actie te delen over meerdere verbindingen met dezelfde bestemming met dezelfde referenties.

  Stel dat uw logica-app tabellen uit een SQL Server-database krijgt en vervolgens de rijen uit elke tabel krijgt. Op basis van het aantal rijen dat u moet verwerken, u meerdere verbindingen en meerdere **voor elke** lussen gebruiken om het totale aantal rijen in kleinere sets te verdelen voor verwerking. In dit scenario worden **twee voor elke** lussen gebruikt om het totale aantal rijen in tweeën te splitsen. De eerste **voor elke** lus maakt gebruik van een expressie die de eerste helft krijgt. De andere **Voor elke** lus wordt een andere expressie gebruikt die de tweede helft krijgt, bijvoorbeeld:<p>

    * Expressie 1: `take()` De functie krijgt de voorkant van een verzameling. Zie voor meer [ **`take()`** ](workflow-definition-language-functions-reference.md#take)informatie de functie .

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Expressie 2: `skip()` De functie verwijdert de voorkant van een verzameling en retourneert alle andere items. Zie voor meer [ **`skip()`** ](workflow-definition-language-functions-reference.md#skip)informatie de functie .

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Hier is een visueel voorbeeld dat laat zien hoe u deze expressies gebruiken:

    ![Meerdere verbindingen maken en gebruiken voor één actie](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Voor elke actie een andere verbinding instellen.

  Overweeg voor deze optie of u de werkbelasting distribueren door de aanvragen van elke actie over hun eigen verbinding te spreiden, zelfs wanneer acties verbinding maken met dezelfde service of hetzelfde systeem en dezelfde referenties gebruiken.

  Stel dat uw logica-app de tabellen uit een SQL Server-database haalt en elke rij in elke tabel krijgt. U afzonderlijke verbindingen gebruiken, zodat de tabellen één verbinding gebruiken, terwijl het verkrijgen van elke rij een andere verbinding gebruikt.

  ![Voor elke actie een andere verbindingen maken en gebruiken](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Wijzig de gelijktijdigheid of parallellisme op een [lus "Voor elke" lus](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Standaard worden 'Voor elke' lusiteraties tegelijkertijd uitgevoerd tot de [gelijktijdigheidslimiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u een connector hebt die wordt beperkt in een lus Voor elke lus, u het aantal lusiteraties verminderen dat parallel wordt uitgevoerd. Zie deze onderwerpen voor meer informatie:
  
  * ["Voor elke" lussen - wissel gelijktijdigheid of voer achtereenvolgens uit](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Taalschema voor werkstroomdefinitie - Voor elke lussen](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Taalschema voor werkstroomdefinitie - Wijziging van de lusgelijktijdigvaluta voor elke lus](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Taalschema voor werkstroomdefinitie - Looplussen voor elke lus en -programma uitvoeren](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Bestemmingsservice of systeembeperking

Hoewel een connector zijn eigen beperkingslimieten heeft, kan de doelservice of het systeem dat door de connector wordt aangeroepen, ook beperkingslimieten hebben. Sommige API's in Microsoft Exchange Server hebben bijvoorbeeld strengere beperkingslimieten dan de Office 365 Outlook-connector.

Standaard worden de exemplaren van een logische app en eventuele lussen of vertakkingen in die instanties *parallel uitgevoerd.* Dit gedrag betekent dat meerdere instanties tegelijkertijd hetzelfde eindpunt kunnen aanroepen. Elke instantie weet niet over het bestaan van de ander, dus pogingen om mislukte acties opnieuw te proberen kunnen [racevoorwaarden](https://en.wikipedia.org/wiki/Race_condition) creëren waarbij meerdere oproepen tegelijkertijd proberen uit te voeren, maar om te slagen, moeten die oproepen aankomen op de bestemmingsservice of -systeem voordat throttling begint te gebeuren.

Stel dat u een array hebt met 100 items. U gebruikt een lus voor elke lus om door de array te herhalen en het gelijktijdigheidsbesturingselement van de lus in te schakelen, zodat u het aantal parallelle iteraties beperken tot 20 of de [huidige standaardlimiet.](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits) In die lus voegt een actie een item uit de array in een SQL Server-database in, waardoor slechts 15 aanroepen per seconde mogelijk is. Dit scenario resulteert in een beperkingsprobleem omdat een achterstand van nieuwe pogingen zich opbouwt en nooit kan worden uitgevoerd.

In deze tabel wordt de tijdlijn beschreven voor wat er in de lus gebeurt wanneer het interval voor nieuwe try van de actie 1 seconde is:

| Punt in de tijd | Aantal acties dat wordt uitgevoerd | Aantal acties dat mislukt | Aantal wachtende pogingen |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 seconden | 20 wisselplaten | 5 mislukken, als gevolg van SQL-limiet | 5 nieuwe pogingen |
| T + 0,5 seconden | 15 wisselplaten, als gevolg van eerdere 5 pogingen te wachten | Alle 15 mislukken, als gevolg van eerdere SQL-limiet nog steeds van kracht voor nog eens 0,5 seconden | 20 nieuwe pogingen <br>(vorige 5 + 15 nieuw) |
| T + 1 seconde | 20 wisselplaten | 5 mislukken plus eerdere 20 nieuwe pogingen, als gevolg van SQL-limiet | 25 nieuwe pogingen (vorige 20 + 5 nieuwe)
|||||

Als u beperking op dit niveau wilt verwerken, hebt u de volgende opties:

* Maak logische apps zodat elk één bewerking afhandelt.

  * Als u doorgaan met het voorbeeld SQL Server-scenario in deze sectie, u een logische app maken die arrayitems in een wachtrij plaatst, zoals een [Azure Service Bus-wachtrij.](../connectors/connectors-create-api-servicebus.md) Vervolgens maakt u een andere logische app die alleen de invoegbewerking uitvoert voor elk item in die wachtrij. Op die manier wordt slechts één logische app-instantie op een bepaald tijdstip uitgevoerd, waardoor de invoegbewerking wordt voltooid en naar het volgende item in de wachtrij wordt verplaatst, of dat de instantie 429 fouten krijgt, maar geen pogingen doet om onproductieve pogingen te doen.

  * Maak een bovenliggende logica-app die een onderliggende of geneste logische app aanroept voor elke actie. Als de ouder verschillende onderliggende apps moet aanroepen op basis van de uitkomst van de ouder, u een conditieactie gebruiken of actie schakelen die bepaalt welke onderliggende app u wilt aanroepen. Dit patroon kan u helpen het aantal oproepen of bewerkingen te verminderen.

    Stel dat u twee logische apps hebt, elk met een pollingtrigger die uw e-mailaccount elke minuut controleert op een specifiek onderwerp, zoals 'Succes' of 'Falen'. Deze instelling resulteert in 120 gesprekken per uur. In plaats daarvan, als u een eenouderlogica-app maakt die elke minuut wordt gepeild, maar een app voor onderliggende logica aanroept die wordt uitgevoerd op basis van of het onderwerp 'Succes' of 'Mislukt' is, snijdt u het aantal pollingcontroles tot de helft, of 60 in dit geval.

* Batchverwerking instellen.

  Als de doelservice batchbewerkingen ondersteunt, u beperking aanpakken door artikelen in groepen of batches te verwerken, in plaats van afzonderlijk. Als u de oplossing voor batchverwerking wilt implementeren, maakt u een logische app voor batch-ontvanger en een logische app voor batchafzender. De afzender van de batch verzamelt berichten of items totdat aan de opgegeven criteria is voldaan en verzendt deze berichten of items vervolgens in één groep. De batch-ontvanger accepteert die groep en verwerkt deze berichten of items. Zie [Batchprocesberichten in groepen](../logic-apps/logic-apps-batch-process-send-receive-messages.md)voor meer informatie.

* Gebruik de webhook-versies voor triggers en acties, in plaats van de stemversies.

  Hoe komt dat? Een polling trigger blijft de doelservice of het doelsysteem met specifieke intervallen controleren. Een zeer frequent interval, zoals elke seconde, kan throttling problemen veroorzaken. Een webhook-trigger of actie, zoals [HTTP Webhook,](../connectors/connectors-native-webhook.md)maakt echter slechts één oproep naar de doelservice of -systeem, wat gebeurt op abonnementstijd en verzoekt de bestemming de trigger of actie alleen te melden wanneer een gebeurtenis plaatsvindt. Op die manier hoeft de trigger of actie niet voortdurend de bestemming te controleren.
  
  Dus, als de doelservice of het doelsysteem webhooks ondersteunt of een connector biedt met een webhook-versie, is deze optie beter dan het gebruik van de polling-versie. Als u webhook-triggers en acties `ApiConnectionWebhook` wilt identificeren, bevestigt u dat ze het type hebben of dat ze niet vereisen dat u een herhaling opgeeft. Zie [APIConnectionWebhook trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) en [APIConnectionWebhook actie](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [limieten en configuratie van Logic Apps](../logic-apps/logic-apps-limits-and-config.md)
