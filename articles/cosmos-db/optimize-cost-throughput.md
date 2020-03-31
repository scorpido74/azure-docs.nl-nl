---
title: Doorvoerkosten optimaliseren in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe u de doorvoerkosten voor de gegevens die zijn opgeslagen in Azure Cosmos DB optimaliseren.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: c80ab4acd745717e2e68ae7d9dc818594ad1ce9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501462"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB

Door het aanbieden van een ingerichte doorvoermodel biedt Azure Cosmos DB voorspelbare prestaties op elke schaal. Het reserveren of inrichten van doorvoer van tevoren elimineert het "luidruchtige neighbor-effect" op uw prestaties. U geeft de exacte hoeveelheid doorvoer op die u nodig hebt en Azure Cosmos DB garandeert de geconfigureerde doorvoer, ondersteund door SLA.

U beginnen met een minimale doorvoer van 400 RU/sec en maximaal tientallen miljoenen aanvragen per seconde of zelfs meer opschalen. Elk verzoek dat u uitgeeft tegen uw Azure Cosmos-container of -database, zoals een leesaanvraag, schrijfaanvraag, queryaanvraag, opgeslagen procedures, heeft een overeenkomstige kosten die worden afgetrokken van uw ingerichte doorvoer. Als u 400 RU/s indient en een query uitgeeft die 40 RU's kost, u 10 van dergelijke query's per seconde uitgeven. Elk verzoek verder dan dat krijgt tarief-beperkt en je moet opnieuw proberen het verzoek. Als u clientstuurprogramma's gebruikt, ondersteunen ze de logica voor automatische opnieuw proberen.

U kunt doorvoer inrichten in databases of containers, en elke strategie kan helpen kosten te besparen, afhankelijk van het scenario.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimaliseren door doorvoer op verschillende niveaus in te richten

* Als u doorvoer indient op een database, kunnen alle containers, bijvoorbeeld verzamelingen/tabellen/grafieken in die database, de doorvoer delen op basis van de belasting. Doorvoer gereserveerd op databaseniveau wordt ongelijk gedeeld, afhankelijk van de werkbelasting op een specifieke set containers.

* Als u doorvoer op een container indient, wordt de doorvoer gegarandeerd voor die container, ondersteund door de SLA. De keuze van een logische partitiesleutel is cruciaal voor een gelijkmatige verdeling van de belasting over alle logische partities van een container. Zie [Artikelen partitioneren](partitioning-overview.md) en [horizontaal schalen](partition-data.md) voor meer informatie.

Hieronder volgen enkele richtlijnen om te beslissen over een ingerichte doorvoerstrategie:

**Overweeg doorvoer in te richten op een Azure Cosmos-database (met een set containers) als:**

1. U hebt enkele tientallen Azure Cosmos-containers en wilt de doorvoer over sommige of alle containers delen. 

2. U migreert vanuit een database met één tenant die is ontworpen om te worden uitgevoerd op vm's die door IaaS zijn gehost of on-premises, bijvoorbeeld NoSQL- of relationele databases naar Azure Cosmos DB. En als u veel collecties / tabellen / grafieken en u wilt geen wijzigingen aan te brengen in uw gegevensmodel. Houd er rekening mee dat u een aantal voordelen van Azure Cosmos DB moet compromitteren als u uw gegevensmodel niet bijwerkt wanneer u migreert vanuit een on-premises database. Het wordt aanbevolen dat u altijd uw gegevensmodel opnieuw toegang krijgt om het meeste uit de prestaties te halen en ook te optimaliseren voor kosten. 

3. U wilt ongeplande pieken in workloads absorberen op grond van gepoolde doorvoer op databaseniveau, onderworpen aan onverwachte piek in werkbelasting. 

4. In plaats van specifieke doorvoer in te stellen op afzonderlijke containers, wilt u de totale doorvoer over een set containers in de database krijgen.

**Overweeg de doorvoer op een afzonderlijke container in te richten als:**

1. Je hebt een paar Azure Cosmos containers. Omdat Azure Cosmos DB schema-agnostisch is, kan een container items bevatten met heterogene schema's en hoeven klanten geen meerdere containertypen te maken, één voor elke entiteit. Het is altijd een optie om te overwegen of het groeperen van afzonderlijke zeggen 10-20 containers in een enkele container zinvol is. Met een minimum van 400 R's voor containers zou het bundelen van alle 10-20 containers in één kosteneffectiever kunnen zijn. 

2. U wilt de doorvoer op een specifieke container regelen en de gegarandeerde doorvoer krijgen op een bepaalde container die wordt ondersteund door SLA.

**Overweeg een hybride van de bovenstaande twee strategieën:**

1. Zoals eerder vermeld, Azure Cosmos DB u de bovenstaande twee strategieën mixen en matchen, zodat u nu enkele containers in azure cosmos-database hebben, die de doorvoer kunnen delen die in de database is ingericht, evenals sommige containers in dezelfde database , die mogelijk specifieke hoeveelheden ingerichte doorvoer hebben. 

2. U de bovenstaande strategieën toepassen om te komen met een hybride configuratie, waarbij u zowel databaseniveau ingerichte doorvoer hebt met een aantal containers met een speciale doorvoer.

Zoals in de volgende tabel wordt weergegeven, u, afhankelijk van de keuze van de API, doorvoer inrichten op verschillende granulariteiten.

|API|Configureren **voor gedeelde** doorvoer |Configureren **voor speciale** doorvoer |
|----|----|----|
|SQL-API|Database|Container|
|Azure Cosmos DB-API voor MongoDB|Database|Verzameling|
|Cassandra-API|Sleutelruimte|Tabel|
|Gremlin-API|Databaseaccount|Graph|
|Tabel-API|Databaseaccount|Tabel|

Door de doorvoer op verschillende niveaus in te richten, u uw kosten optimaliseren op basis van de kenmerken van uw werkbelasting. Zoals eerder vermeld, u programmatisch en op elk moment uw ingerichte doorvoer voor afzonderlijke containers(en) of collectief over een set containers verhogen of verlagen. Door de doorvoer elastisch te schalen als uw werkbelasting verandert, betaalt u alleen voor de doorvoer die u hebt geconfigureerd. Als uw container of een set containers over meerdere regio's wordt verdeeld, wordt de doorvoer die u op de container configureert of een set containers gegarandeerd beschikbaar gesteld in alle regio's.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimaliseren met tariefbeperkende verzoeken

Voor workloads die niet gevoelig zijn voor latentie, u minder doorvoer inrichten en de snelheid van de toepassing beperken wanneer de werkelijke doorvoer de ingerichte doorvoer overschrijdt. De server beëindigt de aanvraag `RequestRateTooLarge` preventief met (HTTP-statuscode `x-ms-retry-after-ms` 429) en retourneert de koptekst met vermelding van de hoeveelheid tijd, in milliseconden, die de gebruiker moet wachten voordat de aanvraag opnieuw wordt geprobeerd. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logica opnieuw proberen in SDK's 

De native SDKs (.NET/.NET Core, Java, Node.js en Python) vangen impliciet dit antwoord, respecteren de server-opgegeven retry-after header en proberen het verzoek opnieuw. Tenzij uw account gelijktijdig door meerdere clients wordt geopend, slaagt de volgende poging.

Als u meer dan één client cumulatief boven het aanvraagpercentage hebt, is het aantal standaardopnieuw proberen, dat momenteel is ingesteld op 9, mogelijk niet voldoende. In dergelijke gevallen gooit `RequestRateTooLargeException` de client een met statuscode 429 naar de toepassing. Het aantal standaardwijzigingen kan worden `RetryOptions` gewijzigd door de instantie Verbindingsbeleid in te stellen. Standaard wordt `RequestRateTooLargeException` de met statuscode 429 geretourneerd na een cumulatieve wachttijd van 30 seconden als de aanvraag boven het aanvraagpercentage blijft werken. Dit gebeurt zelfs wanneer het aantal huidige opnieuw proberen lager is dan het maximale aantal nieuwe try's, of het nu de standaardwaarde van 9 of een door de gebruiker gedefinieerde waarde is. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) is ingesteld op 3, dus in dit geval, als een aanvraagbewerking wordt beperkt door de gereserveerde doorvoer voor de container te overschrijden, wordt de aanvraagbewerking drie keer opnieuw geprobeerd voordat de uitzondering op de toepassing wordt geplaatst. [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) is ingesteld op 60, dus in dit geval als de cumulatieve wachttijd opnieuw proberen in seconden sinds het eerste verzoek meer dan 60 seconden, wordt de uitzondering gegooid.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Partitioneringsstrategie en kosten van ingerichte doorvoer

Een goede scheidingsstrategie is belangrijk om de kosten in Azure Cosmos DB te optimaliseren. Zorg ervoor dat er geen scheeftrekking van partities is, die worden weergegeven via opslagstatistieken. Zorg ervoor dat er geen scheeftrekking van doorvoer is voor een partitie, die wordt weergegeven met doorvoerstatistieken. Zorg ervoor dat er geen scheeftrekking is ten opzichte van bepaalde partitiesleutels. Dominante sleutels in opslag worden weergegeven door middel van metrische gegevens, maar de sleutel is afhankelijk van uw toepassingstoegangspatroon. Het is het beste om na te denken over de juiste logische partitiesleutel. Een goede partitiesleutel zal naar verwachting de volgende kenmerken hebben:

* Kies een partitiesleutel die de werkbelasting gelijkmatig over alle partities en gelijkmatig in de loop van de tijd verspreidt. Met andere woorden, moet je niet over een aantal sleutels met de meerderheid van de gegevens en sommige sleutels met minder of geen gegevens. 

* Kies een partitiesleutel waarmee toegangspatronen gelijkmatig kunnen worden verdeeld over logische partities. De werklast is redelijk zelfs over alle toetsen. Met andere woorden, het grootste deel van de werkbelasting moet niet worden gericht op een paar specifieke toetsen. 

* Kies een partitiesleutel met een breed scala aan waarden. 

Het basisidee is om de gegevens en de activiteit in uw container over de set logische partities te verspreiden, zodat bronnen voor gegevensopslag en -doorvoer over de logische partities kunnen worden verdeeld. Kandidaten voor partitiesleutels kunnen de eigenschappen bevatten die vaak als filter in uw query's worden weergegeven. Query's kunnen efficiënt worden gerouteerd door de partitiesleutel op te nemen in het filterpredicaat. Met een dergelijke partitioneringsstrategie zal het optimaliseren van de ingerichte doorvoer een stuk eenvoudiger zijn. 

### <a name="design-smaller-items-for-higher-throughput"></a>Kleinere items ontwerpen voor een hogere doorvoer 

De aanvraagkosten of de kosten voor het verwerken van aanvragen van een bepaalde bewerking zijn rechtstreeks gerelateerd aan de grootte van het artikel. Bewerkingen op grote artikelen kosten meer dan bewerkingen op kleinere artikelen. 

## <a name="data-access-patterns"></a>Patronen voor gegevenstoegang 

Het is altijd een goede gewoonte om uw gegevens logisch te scheiden in logische categorieën op basis van hoe vaak u toegang hebt tot de gegevens. Door het te categoriseren als warme, gemiddelde of koude gegevens u de verbruikte opslag en de benodigde doorvoer verfijnen. Afhankelijk van de frequentie van de toegang u de gegevens in afzonderlijke containers plaatsen (bijvoorbeeld tabellen, grafieken en verzamelingen) en de ingerichte doorvoer erop afstemmen om aan de behoeften van dat gegevenssegment te voldoen. 

Als u Azure Cosmos DB gebruikt en u weet dat u niet op bepaalde gegevenswaarden gaat zoeken of er zelden toegang toe hebt, moet u de gecomprimeerde waarden van deze kenmerken opslaan. Met deze methode bespaart u op opslagruimte, indexruimte en ingerichte doorvoer en resulteert u in lagere kosten.

## <a name="optimize-by-changing-indexing-policy"></a>Optimaliseren door indexeringsbeleid te wijzigen 

Azure Cosmos DB indexeert standaard automatisch elke eigenschap van elke record. Dit is bedoeld om de ontwikkeling te vergemakkelijken en uitstekende prestaties te garanderen voor veel verschillende soorten ad-hocquery's. Als u grote records met duizenden eigenschappen hebt, is het mogelijk dat het betalen van de doorvoerkosten voor het indexeren van elke eigenschap mogelijk niet nuttig is, vooral als u alleen vraag naar 10 of 20 van deze eigenschappen. Naarmate u dichter bij het krijgen van een greep op uw specifieke werklast, onze richtlijnen is om uw indexbeleid af te stemmen. De volledige details over azure cosmos DB-indexeringsbeleid vindt u [hier](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitoring van de ingerichte en verbruikte doorvoer 

U het totale aantal ingerichte RU's, het aantal aanvragen met beperkte tariefaanvragen en het aantal RU's dat u hebt verbruikt in de Azure-portal controleren. In de volgende afbeelding ziet u een voorbeeldgebruiksstatistiek:

![Aanvraageenheden in de Azure-portal controleren](./media/optimize-cost-throughput/monitoring.png)

U ook waarschuwingen instellen om te controleren of het aantal aanvragen met beperkte tarief een specifieke drempelwaarde overschrijdt. Zie [Hoe u het DB-artikel van Azure Cosmos DB](use-metrics.md) controleren voor meer informatie. Deze waarschuwingen kunnen een e-mail verzenden naar de accountbeheerders of een aangepaste HTTP Webhook of een Azure-functie aanroepen om de ingerichte doorvoer automatisch te verhogen. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Schaal uw doorvoer elastisch en on-demand 

Aangezien u wordt gefactureerd voor de ingerichte doorvoer, kan het afstemmen van de ingerichte doorvoer op uw behoeften u helpen de kosten voor de ongebruikte doorvoer te vermijden. U uw ingerichte doorvoer op elk gewenst moment naar boven of omlaag schalen. Als uw doorvoerbehoeften zeer voorspelbaar zijn, u Azure-functies gebruiken en een timertrigger gebruiken om de doorvoer op een planning te [verhogen of te verlagen.](scale-on-schedule.md) 

* Het monitoren van het verbruik van uw RU's en de verhouding van tariefbeperkte aanvragen kan aantonen dat u de hele dag of week niet constant hoeft te blijven. U 's nachts of in het weekend minder verkeer ontvangen. Door azure portal of Azure Cosmos DB native SDKs of REST API te gebruiken, u uw ingerichte doorvoer op elk gewenst moment schalen. De REST API van Azure Cosmos DB biedt eindpunten om het prestatieniveau van uw containers programmatisch bij te werken, waardoor het eenvoudig is om de doorvoer van uw code aan te passen, afhankelijk van het tijdstip van de dag of de dag van de week. De bewerking wordt uitgevoerd zonder downtime en wordt meestal in minder dan een minuut uitgevoerd. 

* Een van de gebieden die u moet schalen doorvoer is wanneer u gegevens inneemt in Azure Cosmos DB, bijvoorbeeld tijdens de gegevensmigratie. Zodra u de migratie hebt voltooid, u de ingerichte doorvoer naar beneden schalen om de steady state van de oplossing te verwerken.  

* Vergeet niet dat de facturering is op de granulariteit van een uur, dus je bespaart geen geld als u uw ingerichte doorvoer vaker dan een uur per keer.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>De doorvoer bepalen die nodig is voor een nieuwe werkbelasting 

Als u de ingerichte doorvoer voor een nieuwe werkbelasting wilt bepalen, u de volgende stappen gebruiken: 

1. Voer een eerste, ruwe evaluatie uit met behulp van de capaciteitsplanner en pas uw schattingen aan met behulp van de Azure Cosmos Explorer in de Azure-portal. 

2. Het wordt aanbevolen om de containers te maken met een hogere doorvoer dan verwacht en vervolgens af te bouwen als dat nodig is. 

3. Het wordt aanbevolen om een van de native Azure Cosmos DB SDK's te gebruiken om te profiteren van automatische pogingen wanneer aanvragen beperkt worden. Als u werkt aan een platform dat niet wordt ondersteund en de REST API van `x-ms-retry-after-ms` Cosmos DB gebruikt, implementeert u uw eigen beleid voor nieuwe technieken met behulp van de header. 

4. Zorg ervoor dat de toepassingscode de aanvraag op een elegante manier ondersteunt wanneer alle pogingen mislukken. 

5. U waarschuwingen van de Azure-portal configureren om meldingen te ontvangen voor beperking van de snelheid. U beginnen met conservatieve limieten zoals 10 tarief-beperkte verzoeken in de afgelopen 15 minuten en overschakelen naar meer gretige regels als je eenmaal erachter te komen uw werkelijke verbruik. Af en toe tarieflimieten zijn prima, ze laten zien dat je speelt met de limieten die je hebt ingesteld en dat is precies wat je wilt doen. 

6. Gebruik monitoring om inzicht te krijgen in uw verkeerspatroon, zodat u overwegen of u uw doorvoerinrichting gedurende de dag of een week dynamisch aanpassen. 

7. Controleer uw ingerichte versus verbruikte doorvoerratio regelmatig om ervoor te zorgen dat u niet meer hebt ingericht dan het vereiste aantal containers en databases. Na een beetje meer dan ingerichte doorvoer is een goede veiligheidscontrole.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Aanbevolen procedures om de ingerichte doorvoer te optimaliseren 

Met de volgende stappen u uw oplossingen zeer schaalbaar en kosteneffectief maken bij het gebruik van Azure Cosmos DB.  

1. Als u de ingerichte doorvoer in containers en databases aanzienlijk overhebt, moet u de in provisioned Vs-aanbevolen VS controleren en de workloads verfijnen.  

2. Een methode voor het schatten van de hoeveelheid gereserveerde doorvoer die door uw toepassing vereist is, is het registreren van de RU-kosten voor aanvraageenheden die zijn gekoppeld aan het uitvoeren van typische bewerkingen tegen een representatieve Azure Cosmos-container of -database die door uw toepassing wordt gebruikt en schat vervolgens het aantal bewerkingen dat u per seconde verwacht uit te voeren. Zorg ervoor dat u typische query's en het gebruik ervan ook meet en opneemt. Zie [De kosten van query's optimaliseren](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)voor meer informatie over het programmatisch schatten van ru-kosten van query's of het gebruik van portal. 

3. Een andere manier om bewerkingen en hun kosten in R's te krijgen, is door Azure Monitor-logboeken in te schakelen, waardoor u de indeling van de bewerking/duur en de aanvraagkosten krijgt. Azure Cosmos DB biedt aanvraagkosten voor elke bewerking, zodat elke bewerkingskosten kunnen worden opgeslagen vanaf het antwoord en vervolgens kan worden gebruikt voor analyse. 

4. U de ingerichte doorvoer elastisch op en neer schalen als u aan uw werkbelastingbehoeften moet voldoen. 

5. U regio's toevoegen en verwijderen die zijn gekoppeld aan uw Azure Cosmos-account als u dat nodig hebt en de kosten beheren. 

6. Zorg ervoor dat u zelfs gegevens en workloads over logische partities van uw containers hebt. Als u een ongelijke partitieverdeling hebt, kan dit leiden tot een hogere hoeveelheid doorvoer dan de waarde die nodig is. Als u vastgeeft dat u een scheve verdeling hebt, raden we u aan de werkbelasting gelijkmatig over de partities te verdelen of de gegevens opnieuw te verdelen. 

7. Als u veel containers hebt en deze containers geen SLA's nodig hebben, u de op de database gebaseerde aanbieding gebruiken voor de gevallen waarin de SLA's per containerdoorvoer niet van toepassing zijn. U moet bepalen welke van de Azure Cosmos-containers u wilt migreren naar de doorvoeraanbieding op databaseniveau en deze vervolgens migreren met behulp van een oplossing op basis van een wijzigingsfeed. 

8. Overweeg het gebruik van de "Cosmos DB Free Tier" (gratis voor een jaar), Probeer Cosmos DB (maximaal drie regio's) of downloadbare Cosmos DB emulator voor dev / test scenario's. Door gebruik te maken van deze opties voor test-dev, u uw kosten aanzienlijk verlagen.  

9. U workloadspecifieke kostenoptimalisaties verder uitvoeren, bijvoorbeeld door batchgrootte, load-balancing reads in meerdere regio's te vergroten en gegevens te dedupliceren, indien van toepassing.

10. Met azure cosmos DB gereserveerde capaciteit, u aanzienlijke kortingen krijgen voor maximaal 65% voor drie jaar. Azure Cosmos DB gereserveerde capaciteitsmodel is een vooraf toezegging op aanvragen eenheden die nodig zijn in de tijd. De kortingen zijn gelaagd zodanig dat hoe meer aanvraag eenheden die u gebruikt over een langere periode, hoe meer uw korting zal zijn. Deze kortingen worden onmiddellijk toegepast. Alle R's die boven uw ingerichte waarden worden gebruikt, worden in rekening gebracht op basis van de niet-gereserveerde capaciteitskosten. Zie [Cosmos DB gereserveerde capaciteit](cosmos-db-reserved-capacity.md)) voor meer details. Overweeg de gereserveerde capaciteit in te kopen om uw ingerichte doorvoerkosten verder te verlagen.  

## <a name="next-steps"></a>Volgende stappen

Vervolgens u meer te weten komen over kostenoptimalisatie in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [het begrijpen van uw Azure Cosmos DB-factuur](understand-your-bill.md)
* Meer informatie over [het optimaliseren van opslagkosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lezen en schrijven](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten van Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)

