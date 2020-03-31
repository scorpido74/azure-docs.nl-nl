---
title: Web API's & REST API's voor Azure Logic Apps maken
description: Web API's maken & REST API's om uw API's, services of systemen te bellen voor systeemintegraties in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: bb6c99ea12e5b53631d42a04b36b7bfef2337e42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270535"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Aangepaste API's maken die u aanroepen vanuit Azure Logic Apps

Hoewel Azure Logic Apps [honderden connectors](../connectors/apis-list.md) biedt die u gebruiken in logische app-werkstromen, u API's, systemen en services aanroepen die niet beschikbaar zijn als connectoren. U uw eigen API's maken die acties en triggers bieden die u in logische apps gebruiken. Hier volgen andere redenen waarom u uw eigen API's wilt maken die u aanroepen vanuit logische app-werkstromen:

* Breid uw huidige systeemintegratie- en data-integratieworkflows uit.
* Help klanten uw service te gebruiken om professionele of persoonlijke taken te beheren.
* Vergroot het bereik, de vindbaarheid en het gebruik voor uw service.

In principe zijn connectors web-API's die REST gebruiken voor pluggable interfaces, [Swagger-metagegevensindeling](https://swagger.io/specification/) voor documentatie en JSON als hun indeling voor gegevensuitwisseling. Omdat connectors REST API's zijn die communiceren via HTTP-eindpunten, u elke taal, zoals .NET, Java, Python of Node.js, gebruiken voor het bouwen van connectoren. U uw API's ook hosten op [Azure App Service,](../app-service/overview.md)een PaaS-aanbod (platform-as-a-service) dat een van de beste, gemakkelijkste en meest schaalbare manieren biedt voor API-hosting. 

Als aangepaste API's met logische apps kunnen werken, kan uw API [*acties*](./logic-apps-overview.md#logic-app-concepts) bieden die specifieke taken uitvoeren in logische app-werkstromen. Uw API kan ook fungeren als een [*trigger*](./logic-apps-overview.md#logic-app-concepts) waarmee een logische app-werkstroom wordt gestart wanneer nieuwe gegevens of een gebeurtenis aan een bepaalde voorwaarde voldoet. In dit onderwerp worden veelvoorkomende patronen beschreven die u volgen voor het maken van acties en triggers in uw API, op basis van het gedrag dat u wilt dat uw API biedt.

U uw API's hosten op [Azure App Service](../app-service/overview.md), een platform-as-a-service (PaaS) aanbod dat zeer schaalbare, eenvoudige API-hosting biedt.

> [!TIP] 
> Hoewel u uw API's implementeren als web-apps, u overwegen uw API's te implementeren als API-apps, wat uw werk gemakkelijker kan maken wanneer u API's bouwt, host en verbruikt in de cloud en on-premises. U hoeft geen code in uw API's te wijzigen, alleen uw code implementeren in een API-app. Leer bijvoorbeeld hoe u API-apps bouwen die met deze talen zijn gemaakt: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [Php](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> Ga voor API-app-voorbeelden die zijn gemaakt voor logische apps naar de [GitHub-opslagplaats](https://github.com/logicappsio) of [blog](https://aka.ms/logicappsblog)van Azure Logic Apps .

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Hoe verschillen aangepaste API's van aangepaste connectors?

Aangepaste API's en [aangepaste connectors](../logic-apps/custom-connector-overview.md) zijn web-API's die REST gebruiken voor pluggable interfaces, [Swagger-metagegevensindeling](https://swagger.io/specification/) voor documentatie en JSON als hun indeling voor gegevensuitwisseling. En omdat deze API's en connectors REST API's zijn die communiceren via HTTP-eindpunten, u elke taal, zoals .NET, Java, Python of Node.js, gebruiken voor het bouwen van aangepaste API's en connectoren.

Met aangepaste API's u API's aanroepen die geen connectoren zijn en eindpunten bieden die u aanroepen met HTTP + Swagger, Azure API Management of App Services. Aangepaste connectors werken als aangepaste API's, maar hebben ook deze kenmerken:

* Geregistreerd als Logic Apps Connector-bronnen in Azure.
* Worden weergegeven met pictogrammen naast door Microsoft beheerde connectors in de Logic Apps Designer.
* Alleen beschikbaar voor de auteurs en logische app-gebruikers van de connectors die dezelfde Azure Active Directory-tenant en Azure-abonnement hebben in de regio waar de logische apps worden geïmplementeerd.

U ook geregistreerde connectors nomineren voor Microsoft-certificering. Dit proces controleert of geregistreerde connectors voldoen aan de criteria voor openbaar gebruik en maakt deze connectors beschikbaar voor gebruikers in Power Automate en Microsoft Power Apps.

Zie voor meer informatie over aangepaste connectors 

* [Overzicht van aangepaste connectors](../logic-apps/custom-connector-overview.md)
* [Aangepaste connectors maken op basis van web-API's](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Aangepaste connectors registreren in Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Handige hulpmiddelen

Een aangepaste API werkt het beste met logische apps wanneer de API ook een [Swagger-document](https://swagger.io/specification/) heeft waarin de bewerkingen en parameters van de API worden beschreven.
Veel bibliotheken, zoals [Swashbuckle,](https://github.com/domaindrivendev/Swashbuckle)kunnen automatisch het Swagger-bestand voor u genereren. Als u het Swagger-bestand annoteert voor weergavenamen, eigenschapstypen enzovoort, u ook [TRex](https://github.com/nihaue/TRex) gebruiken, zodat uw Swagger-bestand goed werkt met logische apps.

<a name="actions"></a>

## <a name="action-patterns"></a>Actiepatronen

Als u logische apps wilt uitvoeren, moet uw aangepaste API [*acties*](./logic-apps-overview.md#logic-app-concepts)bieden . Elke bewerking in uw API wordt toegewezen aan een actie. Een basisactie is een controller die HTTP-aanvragen accepteert en HTTP-antwoorden retourneert. Een logische app stuurt bijvoorbeeld een HTTP-verzoek naar uw web-app of API-app. Uw app retourneert vervolgens een HTTP-antwoord, samen met inhoud die de logische app kan verwerken.

Voor een standaardactie u een HTTP-aanvraagmethode in uw API schrijven en die methode beschrijven in een Swagger-bestand. U uw API dan rechtstreeks aanroepen met een [HTTP-actie](../connectors/connectors-native-http.md) of een [HTTP + Swagger-actie.](../connectors/connectors-native-http-swagger.md) Standaard moeten antwoorden worden geretourneerd binnen de [time-outlimiet van](./logic-apps-limits-and-config.md)de aanvraag. 

![Standaardactiepatroon](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>Als u een logische app wilt laten wachten terwijl uw API taken met een langere uitvoering voltooit, kan uw API het [asynchrone pollingpatroon](#async-pattern) of het [asynchrone webhookpatroon](#webhook-actions) volgen dat in dit onderwerp wordt beschreven. Voor een analogie die u helpt visualiseren van deze patronen 'verschillende gedragingen, stel je het proces voor het bestellen van een aangepaste taart van een bakkerij. Het stempatroon weerspiegelt het gedrag waarbij je elke 20 minuten de bakkerij belt om te controleren of de taart klaar is. Het webhookpatroon weerspiegelt het gedrag waarbij de bakkerij u om uw telefoonnummer vraagt, zodat ze u kunnen bellen wanneer de taart klaar is.

Ga voor voorbeelden naar de [GitHub-repository van Logic Apps](https://github.com/logicappsio). Lees ook meer over [gebruiksmeting voor acties.](logic-apps-pricing.md)

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Langlopende taken uitvoeren met het actiepatroon polling

Als u uw API taken wilt laten uitvoeren die langer kunnen duren dan de [time-outlimiet van](./logic-apps-limits-and-config.md)de aanvraag, u het asynchrone pollingpatroon gebruiken. Dit patroon heeft uw API werken in een aparte thread, maar houd een actieve verbinding met de Logic Apps-engine. Op die manier heeft de logische app geen time-out of gaat u verder met de volgende stap in de werkstroom voordat uw API klaar is met werken.

Hier is het algemene patroon:

1. Zorg ervoor dat de engine weet dat uw API de aanvraag heeft geaccepteerd en is gaan werken.
2. Wanneer de engine volgende aanvragen voor taakstatus uitvoert, laat de engine weten wanneer uw API de taak voltooit.
3. Breng relevante gegevens terug naar de engine, zodat de workflow van de logische app kan worden voortgezet.

<a name="bakery-polling-action"></a>Pas nu de vorige bakkerij analogie op de polling patroon, en stel je voor dat je een bakkerij bellen en bestel een aangepaste taart voor levering. Het proces voor het maken van de taart kost tijd, en je wilt niet aan de telefoon wachten terwijl de bakkerij werkt op de taart. De bakkerij bevestigt uw bestelling en laat u elke 20 minuten bellen voor de status van de taart. Na 20 minuten pass, belt u de bakkerij, maar ze vertellen u dat uw taart niet klaar is en dat u moet bellen in nog eens 20 minuten. Dit heen-en-weer proces gaat door totdat u belt, en de bakkerij vertelt u dat uw bestelling klaar is en levert uw taart. 

Laten we dit stempatroon in kaart brengen. De bakkerij vertegenwoordigt uw aangepaste API, terwijl u, de cakeklant, de Logic Apps-engine vertegenwoordigt. Wanneer de engine uw API aanroept met een aanvraag, bevestigt uw API de aanvraag en reageert met het tijdsinterval wanneer de engine de taakstatus kan controleren. De engine blijft de taakstatus controleren totdat uw API reageert dat de taak is uitgevoerd en retourneert gegevens naar uw logische app, die vervolgens de werkstroom voortzet. 

![Actiepatroon polling](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Dit zijn de specifieke stappen die uw API moet volgen, beschreven vanuit het perspectief van de API:

1. Wanneer uw API een HTTP-verzoek krijgt om `202 ACCEPTED` te `location` beginnen met werken, retourneert u onmiddellijk een HTTP-antwoord met de koptekst die later in deze stap wordt beschreven. Met dit antwoord weet de Logic Apps-engine dat uw API het verzoek heeft ontvangen, de payload van het verzoek (gegevensinvoer) heeft geaccepteerd en nu wordt verwerkt. 
   
   Het `202 ACCEPTED` antwoord moet de volgende kopteksten bevatten:
   
   * *Vereist:* `location` een koptekst die het absolute pad naar een URL opgeeft waar de Logische Apps-engine de taakstatus van uw API kan controleren

   * *Optioneel:* `retry-after` een koptekst die het aantal seconden aangeeft `location` dat de engine moet wachten voordat de URL wordt gecontroleerd op taakstatus. 

     Standaard controleert de motor elke 20 seconden. Als u een ander `retry-after` interval wilt opgeven, neemt u de koptekst en het aantal seconden op tot de volgende peiling.

2. Nadat de opgegeven tijd is vergaan, peilt de Logic Apps-engine de URL om de `location` taakstatus te controleren. Uw API moet deze controles uitvoeren en deze antwoorden retourneren:
   
   * Als de taak is uitgevoerd, retourneert u een HTTP-antwoord, `200 OK` samen met de reactiepayload (invoer voor de volgende stap).

   * Als de taak nog steeds `202 ACCEPTED` wordt verwerkt, retourneert u een ander HTTP-antwoord, maar met dezelfde kopteksten als het oorspronkelijke antwoord.

Wanneer uw API dit patroon volgt, hoeft u niets te doen in de definitie van de logische app-werkstroom om de taakstatus te blijven controleren. Wanneer de engine `202 ACCEPTED` een HTTP-antwoord en een geldige `location` koptekst krijgt, respecteert de engine het asynchrone patroon en controleert de `location` koptekst totdat uw API een niet-202-antwoord retourneert.

> [!TIP]
> Bekijk dit [asynchrone responsvoorbeeld voor](https://github.com/logicappsio/LogicAppsAsyncResponseSample)een voorbeeld van een asynchrone controllerin GitHub voor een voorbeeld asynchrone patroon.

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Langlopende taken uitvoeren met het webhook-actiepatroon

Als alternatief u het webhookpatroon gebruiken voor langlopende taken en asynchrone verwerking. Dit patroon heeft de logica app pauzeren en wachten op een "callback" van uw API te voltooien verwerking voordat de workflow voort te zetten. Deze callback is een HTTP-bericht dat een bericht naar een URL stuurt wanneer een gebeurtenis plaatsvindt. 

<a name="bakery-webhook-action"></a>Pas nu de vorige bakkerij analogie op de webhook patroon, en stel je voor dat je een bakkerij bellen en bestel een aangepaste taart voor levering. Het proces voor het maken van de taart kost tijd, en je wilt niet aan de telefoon wachten terwijl de bakkerij werkt op de taart. De bakkerij bevestigt uw bestelling, maar deze keer, geef je ze je telefoonnummer, zodat ze je kunnen bellen wanneer de taart klaar is. Deze keer vertelt de bakkerij u wanneer uw bestelling klaar is en levert u uw taart af.

Wanneer we dit webhook-patroon weer in kaart brengen, vertegenwoordigt de bakkerij uw aangepaste API, terwijl u, de taartklant, de Logic Apps-engine vertegenwoordigt. De engine roept uw API aan met een aanvraag en bevat een URL voor terugbellen.
Wanneer de taak is uitgevoerd, gebruikt uw API de URL om de engine op de hoogte te stellen en gegevens terug te sturen naar uw logische app, die vervolgens de workflow voortzet. 

Stel voor dit patroon twee eindpunten `subscribe` in op je controller: en`unsubscribe`

*  `subscribe`eindpunt: Wanneer de uitvoering de actie van uw API in `subscribe` de werkstroom bereikt, roept de Logic Apps-engine het eindpunt aan. Met deze stap maakt de logische app een terugroep-URL die uw API opslaat en wacht u vervolgens op de terugroep van uw API wanneer het werk is voltooid. Uw API roept vervolgens terug met een HTTP-bericht naar de URL en geeft alle geretourneerde inhoud en kopteksten door als invoer naar de logische app.

* `unsubscribe`eindpunt: als de logische app-run is geannuleerd, `unsubscribe` roept de Engine Logic Apps het eindpunt aan. Uw API kan vervolgens de terugroep-URL uitschrijven en processen waar nodig stoppen.

![Webhook-actiepatroon](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Momenteel ondersteunt de Logic App Designer geen het ontdekken van webhook-eindpunten via Swagger. Dus voor dit patroon moet je een [ **Webhook-actie** ](../connectors/connectors-native-webhook.md) toevoegen en de URL, headers en het hoofdvoor je aanvraag opgeven. Zie ook [Werkstroomacties en -triggers](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Als u de terugbel-URL wilt `@listCallbackUrl()` doorgeven, u de werkstroomfunctie in een van de vorige velden gebruiken als dat nodig is.

> [!TIP]
> Voor een voorbeeld webhook patroon, bekijk deze [webhook trigger monster in GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Triggerpatronen

Uw aangepaste API kan fungeren als een [*trigger*](./logic-apps-overview.md#logic-app-concepts) waarmee een logische app wordt gestart wanneer nieuwe gegevens of een gebeurtenis aan een bepaalde voorwaarde voldoet. Deze trigger kan regelmatig controleren of wachten en luisteren op nieuwe gegevens of gebeurtenissen op het eindpunt van uw service. Als nieuwe gegevens of een gebeurtenis aan de opgegeven voorwaarde voldoen, wordt de trigger geactiveerd en wordt de logische app gestart, die naar die trigger luistert. Als u logische apps op deze manier wilt starten, kan uw API de [*pollingtrigger*](#polling-triggers) of het [*webhook-triggerpatroon*](#webhook-triggers) volgen. Deze patronen zijn vergelijkbaar met hun tegenhangers voor [polling acties](#async-pattern) en [webhook acties](#webhook-actions). Lees ook meer over [gebruiksmeting voor triggers.](logic-apps-pricing.md)

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Controleer regelmatig op nieuwe gegevens of gebeurtenissen met het stemtriggerpatroon

Een *polling trigger* werkt net als de polling [actie](#async-pattern) eerder beschreven in dit onderwerp. De Logic Apps-engine roept en controleert periodiek het triggereindpunt op nieuwe gegevens of gebeurtenissen. Als de engine nieuwe gegevens of een gebeurtenis vindt die voldoet aan de opgegeven voorwaarde, wordt de trigger geactiveerd. Vervolgens maakt de engine een logische app-instantie die de gegevens verwerkt als invoer. 

![Polling triggerpatroon](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Elk polling-verzoek telt als een actie-uitvoering, zelfs als er geen logische app-instantie wordt gemaakt. Om te voorkomen dat dezelfde gegevens meerdere keren worden verwerkt, moet de trigger gegevens opschonen die al zijn gelezen en doorgegeven aan de logische app.

Hier volgen specifieke stappen voor een polling trigger, beschreven vanuit het perspectief van de API:

| Nieuwe gegevens of gebeurtenissen gevonden?  | API-antwoord | 
| ------------------------- | ------------ |
| Gevonden | Een HTTP-status `200 OK` retourneren met de responspayload (invoer voor de volgende stap). <br/>Met dit antwoord wordt een logische app-instantie gemaakt en wordt de werkstroom gestart. | 
| Niet gevonden | Een HTTP-status `202 ACCEPTED` `location` retourneren `retry-after` met een koptekst en een koptekst. <br/>Voor triggers `location` moet de koptekst ook een `triggerState` queryparameter bevatten, wat meestal een 'tijdstempel' is. Uw API kan deze id gebruiken om bij te houden wanneer de logische app voor het laatst is geactiveerd. | 
||| 

Als u bijvoorbeeld uw service periodiek wilt controleren op nieuwe bestanden, u een pollingtrigger maken met dit gedrag:

| Aanvraag `triggerState`omvat? | API-antwoord | 
| -------------------------------- | -------------| 
| Nee | Retourneer `202 ACCEPTED` een HTTP-status plus een `location` koptekst met `triggerState` ingesteld op de huidige tijd en het `retry-after` interval tot 15 seconden. | 
| Ja | Controleer uw service op `DateTime` bestanden `triggerState`die zijn toegevoegd na de voor. | 
||| 

| Aantal gevonden bestanden | API-antwoord | 
| --------------------- | -------------| 
| Eén bestand | Retourneer `200 OK` een HTTP-status en `triggerState` de `DateTime` inhoudspayload, werk `retry-after` bij naar het geretourneerde bestand en stel het interval in op 15 seconden. | 
| Meerdere bestanden | Retourneer één bestand tegelijk `200 OK` en een `triggerState`HTTP-status, werk het interval bij en stel het `retry-after` interval in op 0 seconden. </br>Deze stappen laten de engine weten dat er meer gegevens beschikbaar zijn en `location` dat de engine onmiddellijk de gegevens moet opvragen via de URL in de koptekst. | 
| Geen bestanden | Een HTTP-status `202 ACCEPTED` retourneren, `triggerState`niet wijzigen `retry-after` en het interval instellen op 15 seconden. | 
||| 

> [!TIP]
> Voor een voorbeeld polling trigger patroon, bekijk deze [poll trigger controller monster in GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Wachten en luisteren naar nieuwe gegevens of gebeurtenissen met het webhook triggerpatroon

Een webhook trigger is een *trigger* die wacht en luistert naar nieuwe gegevens of gebeurtenissen op het eindpunt van uw service. Als nieuwe gegevens of een gebeurtenis aan de opgegeven voorwaarde voldoen, wordt de trigger geactiveerd en wordt een logische app-instantie gemaakt, die vervolgens de gegevens als invoer verwerkt.
Webhook triggers handelen net als de [webhook acties](#webhook-actions) eerder beschreven `subscribe` `unsubscribe` in dit onderwerp, en zijn ingesteld met en eindpunten. 

* `subscribe`eindpunt: wanneer u een webhook-trigger toevoegt en opslaat in `subscribe` uw logische app, roept de Logic Apps-engine het eindpunt aan. Met deze stap maakt de logische app een callback-URL die uw API opslaat. Wanneer er nieuwe gegevens of een gebeurtenis zijn die aan de opgegeven voorwaarde voldoet, roept uw API terug met een HTTP-bericht naar de URL. De inhoud payload en headers doorgeven als input voor de logica app.

* `unsubscribe`eindpunt: als de webhooktrigger of de hele logische app wordt `unsubscribe` verwijderd, roept de Engine Logic Apps het eindpunt aan. Uw API kan vervolgens de terugroep-URL uitschrijven en processen waar nodig stoppen.

![Webhook triggerpatroon](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Momenteel ondersteunt de Logic App Designer geen het ontdekken van webhook-eindpunten via Swagger. Dus voor dit patroon moet je een [ **Webhook-trigger** ](../connectors/connectors-native-webhook.md) toevoegen en de URL, headers en body voor je aanvraag opgeven. Zie ook [HTTPWebhook trigger](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Als u de terugbel-URL wilt `@listCallbackUrl()` doorgeven, u de werkstroomfunctie in een van de vorige velden gebruiken als dat nodig is.
>
> Om te voorkomen dat dezelfde gegevens meerdere keren worden verwerkt, moet de trigger gegevens opschonen die al zijn gelezen en doorgegeven aan de logische app.

> [!TIP]
> Voor een voorbeeld webhook patroon, bekijk deze [webhook trigger controller monster in GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Beveiliging voor oproepen naar uw API's verbeteren vanuit logische apps

Nadat u uw aangepaste API's hebt gemaakt, stelt u verificatie in voor uw API's, zodat u ze veilig bellen vanuit logische apps. Meer informatie over [het verbeteren van de beveiliging van oproepen naar aangepaste API's van logische apps.](../logic-apps/logic-apps-custom-api-authentication.md)

## <a name="deploy-and-call-your-apis"></a>Uw API's implementeren en bellen

Nadat u verificatie hebt ingesteld, stelt u de implementatie in voor uw API's. Meer informatie over [het implementeren en aanroepen van aangepaste API's uit logische apps](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Aangepaste API's publiceren naar Azure

Als u uw aangepaste API's beschikbaar wilt maken voor andere Logic Apps-gebruikers in Azure, moet u beveiliging toevoegen en registreren als Logic App-connectors. Zie voor meer informatie het [Overzicht van aangepaste connectors](../logic-apps/custom-connector-overview.md). 

Als u uw aangepaste API's beschikbaar wilt maken voor alle gebruikers in Logic Apps, Power Automate en Microsoft Power Apps, moet u beveiliging toevoegen, uw API's registreren als Logic App-connectors en uw connectors nomineren voor het [Microsoft Azure Certified-programma.](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/) 

## <a name="get-support"></a>Ondersteuning krijgen

* Voor specifieke hulp bij aangepaste [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)API's u contact opnemen met.

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.

* Ter verbetering van Logic Apps kunt u stemmen op ideeën of ideeën indienen op de [site voor gebruikersfeedback van Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Volgende stappen

* [Fouten en uitzonderingen verwerken](../logic-apps/logic-apps-exception-handling.md)
* [Logische apps bellen, activeren of nesten met HTTP-eindpunten](../logic-apps/logic-apps-http-endpoint.md)
* [Gebruiksmeting voor acties en triggers](../logic-apps/logic-apps-pricing.md)
