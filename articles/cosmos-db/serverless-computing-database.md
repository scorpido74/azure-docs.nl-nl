---
title: Serverloze databasecomputing met Azure Cosmos DB- en Azure-functies
description: Ontdek hoe Azure Cosmos DB- en Azure-functies samen kunnen worden gebruikt om op gebeurtenissen gebaseerde serverloze computerapps te maken.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 079c246f87bb8294f3c7ad6dea3391f5c67ba0ad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985249"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Serverloze databasecomputing met Azure Cosmos DB- en Azure-functies

Serverless computing is alles over de mogelijkheid om zich te concentreren op individuele stukjes logica die herhaalbaar en stateloos zijn. Deze stukken vereisen geen infrastructuurbeheer en ze verbruiken resources alleen voor de seconden, of milliseconden, ze draaien voor. De kern van de serverless computing-beweging zijn functies, die beschikbaar worden gesteld in het Azure-ecosysteem door [Azure-functies.](https://azure.microsoft.com/services/functions) Zie [serverloos in Azure-pagina](https://azure.microsoft.com/solutions/serverless/) voor meer informatie over andere serverloze uitvoeringsomgevingen in Azure. 

Met de native integratie tussen [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) en Azure Functions u databasetriggers, invoerbindingen en uitvoerbindingen rechtstreeks vanuit uw Azure Cosmos DB-account maken. Met Azure Functions en Azure Cosmos DB u gebeurtenisgestuurde serverloze apps maken en implementeren met toegang tot uitgebreide gegevens met lage latentie voor een wereldwijde gebruikersbasis.

## <a name="overview"></a>Overzicht

Met Azure Cosmos DB- en Azure-functies u uw databases en serverloze apps op de volgende manieren integreren:

* Maak een trigger **voor Azure-functies op**basis van gebeurtenissen voor Cosmos DB . Deze trigger is afhankelijk van [wijzigingsfeedstromen](change-feed.md) om uw Azure Cosmos-container te controleren op wijzigingen. Wanneer er wijzigingen in een container worden aangebracht, wordt de feedstream wijziging naar de trigger verzonden, die de Azure-functie aanroept.
* U ook een Azure-functie binden aan een Azure Cosmos-container met behulp van een **invoerbinding**. Invoerbindingen lezen gegevens uit een container wanneer een functie wordt uitgevoerd.
* Bind een functie aan een Azure Cosmos-container met behulp van een **uitvoerbinding**. Uitvoerbindingen schrijven gegevens naar een container wanneer een functie is voltooid.

> [!NOTE]
> Op dit moment worden Azure-functies geactiveerd, invoerbindingen en uitvoerbindingen voor Cosmos DB, alleen ondersteund voor gebruik met de SQL-API. Voor alle andere Azure Cosmos DB API's moet u de database vanuit uw functie openen met behulp van de statische client voor uw API.


Het volgende diagram illustreert elk van deze drie integraties: 

![Hoe Azure Cosmos DB- en Azure-functies worden geïntegreerd](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

De trigger-, invoerbinding seactiveerings- en uitvoerbinding voor Azure Cosmos DB kan worden gebruikt in de volgende combinaties:

* Een Azure-functietrigger voor Cosmos DB kan worden gebruikt met een uitvoerbinding voor een andere Azure Cosmos-container. Nadat een functie een actie uitvoert op een item in de wijzigingsfeed, u deze naar een andere container schrijven (door het naar dezelfde container te schrijven waar het vandaan komt, zou het effectief een recursieve lus maken). U ook een Azure-functietrigger voor Cosmos DB gebruiken om alle gewijzigde items effectief van één container naar een andere container te migreren, met behulp van een uitvoerbinding.
* Invoerbindingen en uitvoerbindingen voor Azure Cosmos DB kunnen worden gebruikt in dezelfde Azure-functie. Dit werkt goed in gevallen waarin u bepaalde gegevens met de invoerbinding wilt vinden, deze wilt wijzigen in de Azure-functie en deze na de wijziging wilt opslaan in dezelfde container of een andere container.
* Een invoerbinding voor een Azure Cosmos-container kan worden gebruikt in dezelfde functie als een Azure-functietrigger voor Cosmos DB en kan ook met of zonder uitvoerbinding worden gebruikt. U deze combinatie gebruiken om up-to-date valuta-uitwisselingsinformatie (ingetrokken met een invoerbinding aan een wisselcontainer) toe te passen op de wijzigingsfeed van nieuwe bestellingen in uw winkelwagenservice. Het bijgewerkte winkelwagentotaal, waarbij de huidige valutaconversie is toegepast, kan naar een derde container worden geschreven met behulp van een uitvoerbinding.

## <a name="use-cases"></a>Gebruiksvoorbeelden

In de volgende use cases worden een aantal manieren gedemonstreerd waarop u optimaal gebruik maken van uw Azure Cosmos DB-gegevens door uw gegevens te koppelen aan azure-functies op basis van gebeurtenissen.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>IoT-gebruikscase - Azure-functies activeren en uitvoerbinding voor Cosmos DB

In IoT-implementaties u een functie aanroepen wanneer het lampje van de controlemotor wordt weergegeven in een verbonden auto.

**Uitvoering:** Een trigger- en uitvoerbinding voor Azure-functies gebruiken voor Cosmos DB

1. Een **Azure-functietrigger voor Cosmos DB** wordt gebruikt om gebeurtenissen met betrekking tot autowaarschuwingen te activeren, zoals het lampje van de controlemotor dat in een verbonden auto aangaat.
2. Wanneer het lampje van de controlemotor komt, worden de sensorgegevens verzonden naar Azure Cosmos DB.
3. Azure Cosmos DB maakt of werkt nieuwe sensorgegevensdocumenten samen, waarna deze wijzigingen worden gestreamd naar de trigger voor Azure-functies voor Cosmos DB.
4. De trigger wordt aangeroepen bij elke wijziging van de sensorgegevensverzameling, omdat alle wijzigingen worden gestreamd via de wijzigingsfeed.
5. In de functie wordt een drempelvoorwaarde gebruikt om de sensorgegevens naar de garantieafdeling te sturen.
6. Als de temperatuur ook boven een bepaalde waarde ligt, wordt er ook een waarschuwing naar de eigenaar verzonden.
7. De **uitvoerbinding** voor de functie werkt de autorecord bij in een andere Azure Cosmos-container om informatie over de gebeurtenis met de controleengine op te slaan.

In de volgende afbeelding ziet u de code die is geschreven in de Azure-portal voor deze trigger.

![Een Azure-functietrigger voor Cosmos DB maken in de Azure-portal](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Financiële use case - Timer trigger en input binding

Bij financiële implementaties u een functie aanroepen wanneer een bankrekeningsaldo onder een bepaald bedrag valt.

**Uitvoering:** Een timertrigger met een Azure Cosmos DB-invoerbinding

1. Met behulp van een [timertrigger](../azure-functions/functions-bindings-timer.md)u de saldogegevens van de bankrekening die met getimede intervallen in een Azure Cosmos-container zijn opgeslagen, ophalen met behulp van een **invoerbinding**.
2. Als het saldo lager is dan de door de gebruiker ingestelde lage balansdrempel, volgt u een actie van de Azure-functie op.
3. De uitvoerbinding kan een [SendGrid-integratie](../azure-functions/functions-bindings-sendgrid.md) zijn die een e-mail van een serviceaccount verzendt naar de e-mailadressen die voor elk van de lage saldo-accounts zijn geïdentificeerd.

In de volgende afbeeldingen wordt de code in de Azure-portal voor dit scenario weergegeven.

![Index.js-bestand voor een timertrigger voor een financieel scenario](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Run.csx-bestand voor een timertrigger voor een financieel scenario](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Gaming use case - Azure Functions trigger en output binding voor Cosmos DB 

In gaming u bij het maken van een nieuwe gebruiker zoeken naar andere gebruikers die ze misschien kennen met behulp van de [Azure Cosmos DB Gremlin API.](graph-introduction.md) U de resultaten vervolgens naar een [Azure Cosmos DB SQL-database] schrijven voor eenvoudig ophalen.

**Uitvoering:** Een trigger- en uitvoerbinding voor Azure-functies gebruiken voor Cosmos DB

1. Met behulp van een Azure Cosmos [DB-grafiekdatabase](graph-introduction.md) om alle gebruikers op te slaan, u een nieuwe functie maken met een Azure-functietrigger voor Cosmos DB. 
2. Wanneer een nieuwe gebruiker wordt ingevoegd, wordt de functie aangeroepen en vervolgens wordt het resultaat opgeslagen met behulp van een **uitvoerbinding**.
3. De functie query's de grafiek database om te zoeken naar alle gebruikers die direct gerelateerd zijn aan de nieuwe gebruiker en retourneert die gegevensset naar de functie.
4. Deze gegevens worden vervolgens opgeslagen in een Azure Cosmos DB die vervolgens gemakkelijk kan worden opgehaald door elke front-end toepassing die de nieuwe gebruiker hun verbonden vrienden toont.

### <a name="retail-use-case---multiple-functions"></a>Retail use case - Meerdere functies

Wanneer een gebruiker in retailimplementaties een item aan zijn winkelmandje toevoegt, hebt u nu de flexibiliteit om functies te maken en aan te roepen voor optionele onderdelen van zakelijke pijplijnen.

**Uitvoering:** Meerdere Azure-functies triggers voor Cosmos DB luisteren naar een container

1. U meerdere Azure-functies maken door Azure Functions-triggers voor Cosmos DB aan elk toe te voegen - die allemaal naar dezelfde wijzigingsfeed van winkelwagengegevens luisteren. Houd er rekening mee dat wanneer meerdere functies naar dezelfde wijzigingsfeed luisteren, voor elke functie een nieuwe lease-collectie vereist is. Zie [De bibliotheek Feedprocessor wijzigen](change-feed-processor.md)voor meer informatie over leaseverzamelingen.
2. Wanneer een nieuw item wordt toegevoegd aan een winkelwagentje voor gebruikers, wordt elke functie onafhankelijk aangeroepen door de wijzigingsfeed van de winkelwagencontainer.
   * Eén functie kan de inhoud van het huidige mandje gebruiken om de weergave van andere items te wijzigen waarin de gebruiker mogelijk geïnteresseerd is.
   * Een andere functie kan voorraadtotalen bijwerken.
   * Een andere functie kan klantgegevens voor bepaalde producten naar de marketingafdeling sturen, die hen een promotionele mailer stuurt. 

     Elke afdeling kan een Azure-functie voor Cosmos DB maken door naar de wijzigingsfeed te luisteren en er zeker van te zijn dat ze kritieke orderverwerkingsgebeurtenissen in het proces niet vertragen.

In al deze use cases, omdat de functie de app zelf heeft losgekoppeld, hoeft u niet steeds nieuwe app-exemplaren op te draaien. In plaats daarvan worden afzonderlijke functies van Azure-functies opgebruikt om afzonderlijke processen te voltooien wanneer dat nodig is.

## <a name="tooling"></a>Hulpprogramma's

Native integratie tussen Azure Cosmos DB en Azure Functions is beschikbaar in de Azure-portal en in Visual Studio 2019.

* In de Azure Functions-portal u een trigger maken. Zie [Een Azure-functietrigger voor Cosmos DB maken voor](../azure-functions/functions-create-cosmos-db-triggered-function.md)snelstartinstructies voor snelstart in de Azure-portal.
* In de Azure Cosmos DB-portal u een Azure-functietrigger voor Cosmos DB toevoegen aan een bestaande Azure Function-app in dezelfde brongroep.
* In Visual Studio 2019 u de trigger maken met behulp van de [Azure Functions Tools:](../azure-functions/functions-develop-vs.md)

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Waarom kiezen voor Azure Functions-integratie voor serverless computing?

Azure Functions biedt de mogelijkheid om schaalbare werkeenheden te maken, of beknopte onderdelen van logica die op aanvraag kunnen worden uitgevoerd, zonder infrastructuur in te richten of te beheren. Door Azure-functies te gebruiken, hoeft u geen volledige app te maken om te reageren op wijzigingen in uw Azure Cosmos-database, u kleine herbruikbare functies voor specifieke taken maken. Bovendien u azure cosmos DB-gegevens ook gebruiken als invoer of uitvoer naar een Azure-functie als reactie op gebeurtenissen zoals een HTTP-aanvragen of een getimede trigger.

Azure Cosmos DB is de aanbevolen database voor uw serverless computing-architectuur om de volgende redenen:

* **Directe toegang tot al uw gegevens:** U hebt gedetailleerde toegang tot elke waarde die is opgeslagen omdat Azure Cosmos DB automatisch standaard alle gegevens [indexeert](index-policy.md) en deze indexen onmiddellijk beschikbaar maakt. Dit betekent dat u voortdurend nieuwe items aan uw database opvragen, bijwerken en toevoegen en direct toegang hebt via Azure-functies.

* **Schemaloos**. Azure Cosmos DB is schemaloos - dus het is uniek in staat om alle gegevensuitvoer van een Azure-functie te verwerken. Deze "handle anything"-benadering maakt het eenvoudig om een verscheidenheid aan functies te maken die allemaal naar Azure Cosmos DB worden uitgevoerd.

* **Schaalbare doorvoer**. Doorvoer kan direct worden opgeschaald en omlaag in Azure Cosmos DB. Als u honderden of duizenden functies hebt die naar dezelfde container worden opgevraagd en geschreven, u uw [RU/s](request-units.md) opschalen om de belasting te verwerken. Alle functies kunnen parallel werken met behulp van uw toegewezen RU/s en uw gegevens zijn gegarandeerd [consistent.](consistency-levels.md)

* **Globale replicatie**. U Azure Cosmos DB-gegevens [over de hele wereld](distribute-data-globally.md) repliceren om de latentie te verminderen en uw gegevens te lokaliseren die het dichtst bij uw gebruikers liggen. Zoals bij alle Azure Cosmos DB-query's worden gegevens van gebeurtenisgestuurde triggers gelezen gegevens van de Azure Cosmos DB die het dichtst bij de gebruiker staat.

Als u wilt integreren met Azure-functies om gegevens op te slaan en geen diepgaande indexering nodig hebt of als u bijlagen en mediabestanden wilt opslaan, is de [Trigger voor Azure Blob Storage](../azure-functions/functions-bindings-storage-blob.md) mogelijk een betere optie.

Voordelen van Azure-functies: 

* **Event-driven**. Azure-functies zijn gebeurtenisgestuurd en kunnen luisteren naar een wijzigingsfeed van Azure Cosmos DB. Dit betekent dat u geen luisterlogica hoeft te maken, maar dat u alleen de veranderingen in de gaten houdt waar u naar luistert. 

* **Geen grenzen.** Functies worden parallel uitgevoerd en de service draait zoveel als u nodig hebt. Jij stelt de parameters in.

* **Goed voor snelle taken.** De service draait nieuwe exemplaren van functies wanneer een gebeurtenis wordt geactiveerd en sluit deze zodra de functie is voltooid. U betaalt alleen voor de tijd dat uw functies worden uitgevoerd.

Zie [Kiezen tussen Flow, Logic Apps, Functions en WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)als u niet zeker weet of Flow, Logic Apps, Azure of WebJobs het beste zijn voor uw implementatie.

## <a name="next-steps"></a>Volgende stappen

Laten we nu echt Azure Cosmos DB- en Azure-functies verbinden: 

* [Een Azure-functietrigger voor Cosmos DB maken in de Azure-portal](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Een Azure Functions HTTP-trigger maken met een Azure Cosmos DB-invoerbinding](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Azure Cosmos DB bindingen en triggers](../azure-functions/functions-bindings-cosmosdb-v2.md)