---
title: Veelgestelde vragen over verschillende API's in Azure Cosmos DB
description: Krijg antwoorden op veelgestelde vragen over Azure Cosmos DB, een wereldwijd gedistribueerde databaseservice met meerdere modellen. Meer informatie over capaciteit, prestatieniveaus en schaling.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 0c994caf094918b39204437b5194d81a64affc66
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545660"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Veelgestelde vragen over verschillende API's in Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Wat zijn de typische use cases voor Azure Cosmos DB?

Azure Cosmos DB is een goede keuze voor nieuwe web-, mobiele, gaming- en IoT-toepassingen waarbij automatische schaal, voorspelbare prestaties, snelle volgorde van milliseconde responstijden en de mogelijkheid om gegevens zonder schema te bevragen belangrijk zijn. Azure Cosmos DB leent zich voor snelle ontwikkeling en ondersteuning van de continue iteratie van toepassingsgegevensmodellen. Toepassingen die door gebruikers gegenereerde inhoud en gegevens beheren, zijn [veelvoorkomende use cases voor Azure Cosmos DB.](use-cases.md)

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hoe biedt Azure Cosmos DB voorspelbare prestaties?

Een [aanvraageenheid](request-units.md) (RU) is de meting van de doorvoer in Azure Cosmos DB. Een 1RU-doorvoer komt overeen met de doorvoer van de GET van een document van 1 KB. Elke bewerking in Azure Cosmos DB, inclusief lees-, schrijf-, SQL-query's en opgeslagen procedureuitvoeringen, heeft een deterministische RU-waarde die is gebaseerd op de doorvoer die nodig is om de bewerking te voltooien. In plaats van na te denken over CPU, IO en geheugen en hoe ze elk van invloed op uw applicatie doorvoer, u denken in termen van een enkele RU maatregel.

U elke Azure Cosmos-container configureren met ingerichte doorvoer in termen van RU's met doorvoer per seconde. Voor toepassingen van elke schaal u afzonderlijke aanvragen benchmarken om hun RU-waarden te meten en een container inrichten om het totaal van aanvraageenheden voor alle aanvragen te verwerken. U ook de doorvoer van uw container opschalen of opschalen naarmate de behoeften van uw toepassing evolueren. Voor meer informatie over aanvraageenheden en voor hulp bij het bepalen van uw containerbehoeften, probeert u de [doorvoercalculator](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hoe ondersteunt Azure Cosmos DB verschillende gegevensmodellen, zoals sleutel/waarde, kolomsleutel, document en grafiek?

Sleutel-/waardegegevensmodellen (tabel), kolomsleutel, document en grafiek worden allemaal native ondersteund vanwege het ARS-ontwerp (atomen, records en reeksen) waarop Azure Cosmos DB is gebouwd. Atomen, records en sequenties kunnen eenvoudig worden toegewezen en geprojecteerd op verschillende gegevensmodellen. De API's voor een subset van modellen zijn momenteel beschikbaar (SQL, MongoDB, Tabel en Gremlin) en andere specifiek voor aanvullende gegevensmodellen zullen in de toekomst beschikbaar zijn.

Azure Cosmos DB heeft een schema agnostische indexeringsengine die in staat is om alle gegevens die het inneemt automatisch te indexeren zonder dat er schema's of secundaire indexen van de ontwikkelaar nodig zijn. De engine is gebaseerd op een set logische indexlay-outs (omgekeerd, zuil, structuur) die de opslaglay-out loskoppelen van de index- en queryverwerkingssubsystemen. Cosmos DB heeft ook de mogelijkheid om een set draadprotocollen en API's op een uitbreidbare manier te ondersteunen en deze efficiënt te vertalen naar het kerngegevensmodel (1) en de logische indexlay-outs (2), waardoor het uniek in staat is om meer dan één gegevensmodel native te ondersteunen.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Kan ik meerdere API's gebruiken om toegang te krijgen tot mijn gegevens?

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. Wanneer multi-model betekent dat Azure Cosmos DB meerdere API's en meerdere gegevensmodellen ondersteunt, gebruiken verschillende API's verschillende gegevensindelingen voor opslag en draadprotocol. SQL maakt bijvoorbeeld gebruik van JSON, MongoDB gebruikt BSON, Tabel gebruikt EDM, Cassandra gebruikt CQL, Gremlin gebruikt GraphSON. Daarom raden we aan om dezelfde API te gebruiken voor alle toegang tot de gegevens in een bepaald account.

Elke API werkt onafhankelijk, behalve de Gremlin en SQL API, die interoperabel zijn.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Is Azure Cosmos DB HIPAA compliant?

Ja, Azure Cosmos DB is HIPAA-compatibel. HIPAA stelt vereisten vast voor het gebruik, de openbaarmaking en de beveiliging van individueel identificeerbare gezondheidsinformatie. Zie het Microsoft [Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)voor meer informatie.

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Wat zijn de opslaglimieten van Azure Cosmos DB?

Er is geen limiet aan de totale hoeveelheid gegevens die een container kan opslaan in Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Wat zijn de doorvoerlimieten van Azure Cosmos DB?

Er is geen limiet aan de totale hoeveelheid doorvoer die een container kan ondersteunen in Azure Cosmos DB. Het belangrijkste idee is om uw werklast ongeveer gelijkmatig te verdelen over een voldoende groot aantal partitiesleutels.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Zijn direct- en gateway-connectiviteitsmodi versleuteld?

Ja, beide modi zijn altijd volledig versleuteld.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Hoeveel kost Azure Cosmos DB?

Zie voor meer informatie de pagina [met prijsdetails van Azure Cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/) De gebruikskosten van Azure Cosmos DB worden bepaald door het aantal ingerichte containers, het aantal uren dat de containers online waren en de ingerichte doorvoer voor elke container.

### <a name="is-a-free-account-available"></a>Is er een gratis account beschikbaar?

Ja, u zich kosteloos aanmelden voor een tijdelijk account, zonder verplichtingen. Als u zich wilt aanmelden, gaat u [gratis naar Proberen Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) of lees meer in de [veelgestelde vragen over Azure Cosmos DB uitproberen.](#try-cosmos-db)

Als u nieuw bent bij Azure, u zich aanmelden voor een [gratis Azure-account](https://azure.microsoft.com/free/), waardoor u 30 dagen en een tegoed hebt om alle Azure-services uit te proberen. Als u een Visual Studio-abonnement hebt, komt u ook in aanmerking voor [gratis Azure-tegoed voor](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) gebruik op een Azure-service.

U de [Azure Cosmos DB Emulator](local-emulator.md) ook gebruiken om uw toepassing lokaal gratis te ontwikkelen en te testen, zonder een Azure-abonnement te maken. Als u tevreden bent over hoe uw toepassing in de Azure Cosmos DB Emulator werkt, kunt u overstappen naar een Azure Cosmos DB-account in de cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hoe kan ik extra hulp krijgen met Azure Cosmos DB?

Om een technische vraag te stellen, u posten op een van deze twee vragen en antwoord forums:

* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow is het beste voor het programmeren van vragen. Zorg ervoor dat uw vraag is [on-topic](https://stackoverflow.com/help/on-topic) en [geef zo veel mogelijk details, waardoor de vraag duidelijk en beantwoordbaar](https://stackoverflow.com/help/how-to-ask).

Als u nieuwe functies wilt aanvragen, maakt u een nieuwe aanvraag op [gebruikersstem](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Azure Cosmos DB-abonnementen uitproberen

U nu gratis genieten van een beperkte Azure Cosmos DB-ervaring zonder abonnement. Als u zich wilt aanmelden voor een Azure Cosmos DB-abonnement proberen, gaat u gratis naar [Azure Cosmos DB proberen](https://azure.microsoft.com/try/cosmosdb/) en gebruikt u een persoonlijk Microsoft-account (MSA). Dit abonnement staat los van de [gratis proefversie](https://azure.microsoft.com/free/)van Azure en kan worden gebruikt, samen met een Gratis Proefversie van Azure of een door Azure betaald abonnement.

Probeer Azure Cosmos DB-abonnementen in de Azure-portal naast andere abonnementen die zijn gekoppeld aan uw gebruikersnaam.

De volgende voorwaarden zijn van toepassing op Azure Cosmos DB-abonnementen uitproberen:

* Toegang tot accounts kan worden verleend aan persoonlijke Microsoft-accounts (MSA). Vermijd het gebruik van Aad-accounts (Active Directory) of accounts van zakelijke AAD-huurders, maar ze kunnen beperkingen hebben die het verlenen van toegang kunnen blokkeren.
* Eén [container met doorvoervoorzieningen](./set-throughput.md#set-throughput-on-a-container) per abonnement voor SQL-, Gremlin-API- en Tabelaccounts.
* Maximaal drie [doorvoer-ingerichte verzamelingen](./set-throughput.md#set-throughput-on-a-container) per abonnement voor MongoDB-accounts.
* Eén [doorvoer-ingerichte database](./set-throughput.md#set-throughput-on-a-database) per abonnement. Doorvoeringerichte databases kunnen een willekeurig aantal containers bevatten.
* 10 GB opslagcapaciteit.
* Wereldwijde replicatie is beschikbaar in de volgende [Azure-regio's:](https://azure.microsoft.com/regions/)Centraal-VS, Noord-Europa en Zuidoost-Azië
* Maximale doorvoer van 5 K RU/s wanneer deze op containerniveau wordt ingericht.
* Maximale doorvoer van 20 K RU/s wanneer deze is ingericht op databaseniveau.
* Abonnementen verlopen na 30 dagen en kunnen worden verlengd tot maximaal 31 dagen in totaal.
* Azure-ondersteuningstickets kunnen niet worden gemaakt voor Try Azure Cosmos DB-accounts. Er wordt echter ondersteuning geboden aan abonnees met bestaande ondersteuningsplannen.

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB instellen

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hoe meld ik me aan voor Azure Cosmos DB?

Azure Cosmos DB is beschikbaar in de Azure-portal. Meld u eerst aan voor een Azure-abonnement. Nadat u zich hebt aangemeld, u een Azure Cosmos DB-account toevoegen aan uw Azure-abonnement.

### <a name="what-is-a-master-key"></a>Wat is een hoofdsleutel?

Een hoofdsleutel is een beveiligingstoken voor toegang tot alle resources voor een account. Personen met de sleutel hebben gelezen en schrijven toegang tot alle bronnen in de database account. Wees voorzichtig wanneer u hoofdtoetsen distribueert. De primaire hoofdsleutel en secundaire hoofdsleutel zijn beschikbaar op **het** sleutelsblad van de [Azure-portal.][azure-portal] Zie [Toegangssleutels weergeven, kopiëren en opnieuw genereren](manage-with-cli.md#list-account-keys) voor meer informatie over sleutels.

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Op welke regio's kunnen voorkeurslocaties worden ingesteld?

De waarde Voorkeurslocaties kan worden ingesteld op een van de Azure-regio's waarin Cosmos DB beschikbaar is. Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor een lijst met beschikbare regio's .

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Is er iets waar ik op moet letten bij het distribueren van gegevens over de hele wereld via de Azure-datacenters?

Azure Cosmos DB is aanwezig in alle Azure-regio's, zoals opgegeven op de pagina [Azure-regio's.](https://azure.microsoft.com/regions/) Omdat het de kernservice is, heeft elk nieuw datacenter een Azure Cosmos DB-aanwezigheid.

Wanneer u een regio instelt, moet u er rekening mee houden dat Azure Cosmos DB soevereine en overheidswolken respecteert. Dat wil zeggen, als je een account aanmaakt in een [soevereine regio,](https://azure.microsoft.com/global-infrastructure/)kun je niet repliceren uit die [soevereine regio.](https://azure.microsoft.com/global-infrastructure/) Op dezelfde manier u replicatie niet inschakelen naar andere soevereine locaties van een extern account.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Is het mogelijk om over te schakelen van containerniveau doorvoerprovisioning naar database niveau doorvoer provisioning? Of andersom

Het inrichten van container- en databaseniveaudoorvoer is afzonderlijke aanbiedingen en het schakelen tussen een van deze gegevens vereist het migreren van gegevens van bron naar bestemming. Dat betekent dat u een nieuwe database of een nieuwe container moet maken en vervolgens gegevens moet migreren met behulp van [bulkexecutorbibliotheek](bulk-executor-overview.md) of [Azure Data Factory.](../data-factory/connector-azure-cosmos-db.md)

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Ondersteunt Azure CosmosDB analyse van tijdreeksen?

Ja Azure CosmosDB ondersteunt tijdreeksanalyse, hier is een voorbeeld voor [tijdreeksenpatroon](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). In dit voorbeeld ziet u hoe u wijzigingsfeed gebruiken om geaggregeerde weergaven te maken over tijdreeksgegevens. U deze aanpak uitbreiden door spark streaming of een andere stream data processor te gebruiken.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Wat zijn de Azure Cosmos DB-servicequota en doorvoerlimieten

Zie de Azure Cosmos [DB-servicequota](concepts-limits.md) en [limieten per container en databaseartikelen](set-throughput.md#comparison-of-models) voor meer informatie.

## <a name="sql-api"></a>SQL-API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hoe begin ik met ontwikkelen tegen de SQL API?

Eerst moet u zich aanmelden voor een Azure-abonnement. Zodra u zich aanmeldt voor een Azure-abonnement, u een SQL API-container toevoegen aan uw Azure-abonnement. Zie [Een Azure Cosmos-databaseaccount maken](create-sql-api-dotnet.md#create-account)voor instructies voor het toevoegen van een Azure Cosmos DB-account .

Er zijn [SDK's](sql-api-sdk-dotnet.md) beschikbaar voor .NET, Python, Node.js, JavaScript en Java. Ontwikkelaars kunnen de [RESTful HTTP API's](/rest/api/cosmos-db/) ook gebruiken om te communiceren met Azure Cosmos DB-bronnen van verschillende platforms en talen.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kan ik toegang krijgen tot een aantal kant-en-klare monsters om een voorsprong te krijgen?

Voorbeelden voor de SQL API [.NET](sql-api-dotnet-samples.md), [Java,](https://github.com/Azure/azure-documentdb-java) [Node.js](sql-api-nodejs-samples.md)en [Python](sql-api-python-samples.md) SDKs zijn beschikbaar op GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Ondersteunt de SQL API-database schemavrije gegevens?

Ja, met de SQL API kunnen toepassingen willekeurige JSON-documenten opslaan zonder schemadefinities of hints. Gegevens zijn onmiddellijk beschikbaar voor query via de Azure Cosmos DB SQL-queryinterface.

### <a name="does-the-sql-api-support-acid-transactions"></a>Ondersteunt de SQL API ACID-transacties?

Ja, de SQL API ondersteunt cross-document transacties uitgedrukt als JavaScript-opgeslagen procedures en triggers. Transacties worden ondergebracht bij één partitie binnen elke container en worden uitgevoerd met ACID-semantiek als 'alles of niets', geïsoleerd van andere gelijktijdig uitvoerende code en gebruikersverzoeken. Als er uitzonderingen worden gegenereerd via de server-side uitvoering van JavaScript-toepassingscode, wordt de hele transactie teruggedraaid. 

### <a name="what-is-a-container"></a>Wat is een container?

Een container is een groep documenten en de bijbehorende JavaScript-toepassingslogica. Een container is een factureerbare entiteit, waarbij de [kosten](performance-levels.md) worden bepaald door de doorvoer en gebruikte opslag. Containers kunnen een of meer partities of servers overspannen en kunnen schalen om vrijwel onbeperkte hoeveelheden opslag of doorvoer te verwerken.

* Voor SQL API wordt een container toegewezen aan een container.
* Voor Cosmos DB's API voor MongoDB-accounts wordt een container toegewezen aan een verzameling.
* Voor Cassandra- en Table API-accounts wordt een container toegewezen aan een tabel.
* Voor Gremlin API-accounts wordt een container toegewezen aan een grafiek.

Containers zijn ook de factureringsentiteiten voor Azure Cosmos DB. Elke container wordt per uur gefactureerd op basis van de ingerichte doorvoer en gebruikte opslagruimte. Zie [Azure Cosmos DB Pricing](https://azure.microsoft.com/pricing/details/cosmos-db/)voor meer informatie.

### <a name="how-do-i-create-a-database"></a>Hoe maak ik een database?

U databases maken met behulp van de [Azure-portal](https://portal.azure.com), zoals beschreven in [Een container toevoegen,](create-sql-api-java.md#add-a-container)een van de [Azure Cosmos DB SDK's](sql-api-sdk-dotnet.md)of de [REST-API's](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Hoe stel ik gebruikers en machtigingen in?

U gebruikers en machtigingen maken met behulp van een van de [Cosmos DB API SDKs](sql-api-sdk-dotnet.md) of de [REST API's.](/rest/api/cosmos-db/)

### <a name="does-the-sql-api-support-sql"></a>Ondersteunt de SQL API SQL?

De SQL-querytaal die wordt ondersteund door SQL API-accounts is een verbeterde subset van de queryfunctionaliteit die wordt ondersteund door SQL Server. De Azure Cosmos DB SQL-querytaal biedt uitgebreide hiërarchische en relationele operatoren en uitbreidbaarheid via JavaScript-gebaseerde, door de gebruiker gedefinieerde functies (UDF's). Json grammatica maakt het mogelijk voor het modelleren van JSON-documenten als bomen met gelabelde knooppunten, die worden gebruikt door zowel de Azure Cosmos DB automatische indexeringstechnieken als het SQL-querydialect van Azure Cosmos DB. Zie het SQL [Query-artikel][query] voor informatie over het gebruik van SQL-grammatica.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Ondersteunt de SQL API SQL-aggregatie functies?

De SQL API ondersteunt low-latency aggregatie `MAX`op `AVG`elke `SUM` schaal via aggregaatfuncties `COUNT`, `MIN`, , en via de SQL-grammatica. Zie [Samengevoegde functies voor](sql-query-aggregates.md)meer informatie .

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hoe biedt de SQL API gelijktijdigheid?

De SQL API ondersteunt optimistische gelijktijdigheidscontrole (OCC) via HTTP-entiteitstags of ETags. Elke SQL API-bron heeft een ETag en de ETag wordt ingesteld op de server telkens wanneer een document wordt bijgewerkt. De ETag-header en de huidige waarde zijn opgenomen in alle antwoordberichten. ETags kunnen worden gebruikt met de kop-Inhoud en-match om de server te laten beslissen of een bron moet worden bijgewerkt. De waarde If-Match is de ETag-waarde die moet worden gecontroleerd. Als de ETag-waarde overeenkomt met de etag-waarde van de server, wordt de bron bijgewerkt. Als de ETag niet meer actueel is, weigert de server de bewerking met een antwoordcode 'HTTP 412 Precondition failure'. De client haalt vervolgens de resource terug om de huidige ETag-waarde voor de resource te verkrijgen. Bovendien kunnen ETags worden gebruikt met de kop -Niet-overeenkomen om te bepalen of een nieuwe bron nodig is.

Als u optimistische gelijktijdigheid wilt gebruiken in .NET, gebruikt u de klasse [AccessCondition.](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) Zie [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) in het voorbeeld DocumentManagement op GitHub voor een .NET-voorbeeld.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hoe voer ik transacties uit in de SQL API?

De SQL API ondersteunt taalgeïntegreerde transacties via javascript-opgeslagen procedures en triggers. Alle databasebewerkingen in scripts worden uitgevoerd onder momentopnameisolatie. Als het een container met één partitie is, wordt de uitvoering naar de container uitgevoerd. Als de container is verdeeld, wordt de uitvoering afgestemd op documenten met dezelfde partitiesleutelwaarde in de container. Er wordt aan het begin van de transactie een schermopname van de documentversies (ETags) gemaakt en de transactie wordt alleen uitgevoerd als het script kan worden uitgevoerd. Als JavaScript een fout genereert, wordt de transactie teruggedraaid. Zie [JavaScript-programmering aan de serverzijde voor Azure Cosmos DB voor](stored-procedures-triggers-udfs.md)meer informatie.

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hoe kan ik documenten invoegen in Cosmos DB?

U documenten op een van de volgende manieren invoegen in Azure Cosmos DB:

* Het gereedschap Bulkexecutor, zoals beschreven in [Het gebruik van bulkexecutor .NET-bibliotheek](bulk-executor-dot-net.md) en [Het gebruik van de Java-bibliotheek voor bulkuitvoer](bulk-executor-java.md)
* Het hulpprogramma voor gegevensmigratie, zoals beschreven in [databasemigratietool voor Azure Cosmos DB](import-data.md).
* Opgeslagen procedures, zoals beschreven in [JavaScript-programmering aan de serverzijde voor Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Is de SQL API-ondersteuning resource link caching?

Ja, omdat Azure Cosmos DB een RESTful-service is, zijn resourcekoppelingen onveranderlijk en kunnen ze in de cache worden opgeslagen. SQL API-clients kunnen een koptekst 'If-None-Match' opgeven voor reads ten opzichte van een resource-achtig document of container en vervolgens hun lokale kopieën bijwerken nadat de serverversie is gewijzigd.

### <a name="is-a-local-instance-of-sql-api-available"></a>Is een lokaal exemplaar van SQL API beschikbaar?

Ja. De [Azure Cosmos DB Emulator](local-emulator.md) biedt een high-fidelity emulatie van de Cosmos DB-service. Het ondersteunt functionaliteit die identiek is aan Azure Cosmos DB, inclusief ondersteuning voor het maken en opvragen van JSON-documenten, het inrichten en schalen van verzamelingen en het uitvoeren van opgeslagen procedures en triggers. U toepassingen ontwikkelen en testen met behulp van de Azure Cosmos DB Emulator en deze op globale schaal implementeren in Azure door één configuratiewijziging aan te brengen in het verbindingseindpunt voor Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Waarom worden lange zwevende puntwaarden in een document afgerond wanneer ze worden bekeken vanuit de gegevensverkenner in de portal.

Dit is de beperking van JavaScript. JavaScript gebruikt nummers met dubbele precisie zwevende punten zoals opgegeven in IEEE 754 en kan alleen nummers tussen -(2<sup>53</sup> - 1) en 2<sup>53</sup>-1 (d.w.z. 9007199254740991) veilig vasthouden.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Waar zijn machtigingen toegestaan in de objecthiërarchie?

Het maken van machtigingen met Behulp van ResourceTokens is toegestaan op containerniveau en de afstammelingen daarvan (zoals documenten, bijlagen). Dit houdt in dat het proberen om een machtiging te maken op de database of een accountniveau momenteel niet is toegestaan.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Wat is de API van Azure Cosmos DB voor MongoDB?

De API van Azure Cosmos DB voor MongoDB is een compatibiliteitslaag voor draadprotocol waarmee toepassingen eenvoudig en transparant kunnen communiceren met de native Azure Cosmos-databaseengine met behulp van bestaande, door de gemeenschap ondersteunde SDK's en stuurprogramma's voor MongoDB. Ontwikkelaars kunnen nu bestaande MongoDB-toolchains en -vaardigheden gebruiken om toepassingen te bouwen die profiteren van Azure Cosmos DB. Ontwikkelaars profiteren van de unieke mogelijkheden van Azure Cosmos DB, waaronder wereldwijde distributie met multi-master replicatie, auto-indexering, back-uponderhoud, financieel ondersteund service level agreements (SLA's) enz.

### <a name="how-do-i-connect-to-my-database"></a>Hoe maak ik verbinding met mijn database?

De snelste manier om verbinding te maken met een Cosmos-database met de API van Azure Cosmos DB voor MongoDB is door naar de [Azure-portal](https://portal.azure.com)te gaan. Ga naar uw account en klik vervolgens in het linkernavigatiemenu op **Snel starten**. Quickstart is de beste manier om codefragmenten te krijgen om verbinding te maken met uw database.

Azure Cosmos DB handhaaft strenge beveiligingseisen en -normen. Azure Cosmos DB-accounts vereisen verificatie en beveiligde communicatie via TLS, dus zorg ervoor dat u TLSv1.2 gebruikt.

Zie [Verbinding maken met uw Cosmos-database met de API van Azure Cosmos DB voor MongoDB voor](connect-mongodb-account.md)meer informatie.

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Zijn er extra foutcodes die ik moet afhandelen tijdens het gebruik van azure cosmos DB's API voor MongoDB?

Samen met de algemene MongoDB-foutcodes heeft de API van Azure Cosmos DB voor MongoDB zijn eigen specifieke foutcodes:

| Fout               | Code  | Beschrijving  | Oplossing  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Het totale aantal verbruikte aanvraageenheden is meer dan het ingerichte aanvraag-eenheidstarief voor de container en is beperkt. | Overweeg de doorvoer die is toegewezen aan een container of een set containers te schalen vanuit de Azure-portal of opnieuw te proberen. |
| ExceededMemoryLimit | 16501 | Als een multi-tenant service, de operatie is gegaan over het geheugen van de client toewijzing. | Verminder het bereik van de bewerking door meer beperkende querycriteria of neem contact op met ondersteuning vanuit de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) <br><br><em> &nbsp; &nbsp;Voorbeeld: &nbsp; &nbsp;db.getCollection('users').aggregaat([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {naam: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {leeftijd: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Wordt de Simba-driver voor MongoDB ondersteund voor gebruik met azure cosmos DB's API voor MongoDB?

Ja, u Simba's Mongo ODBC-stuurprogramma gebruiken met azure cosmos DB's API voor MongoDB

## <a name="table-api"></a><a id="table"></a>Table-API

### <a name="how-can-i-use-the-table-api-offering"></a>Hoe kan ik het Table API-aanbod gebruiken?

De Azure Cosmos DB Table API is beschikbaar in de [Azure-portal.][azure-portal] Eerst moet u zich aanmelden voor een Azure-abonnement. Nadat u zich hebt aangemeld, u een Azure Cosmos DB Table API-account toevoegen aan uw Azure-abonnement en vervolgens tabellen aan uw account toevoegen.

U de ondersteunde talen en bijbehorende quick-starts vinden in de [inleiding tot Azure Cosmos DB Table API.](table-introduction.md)

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Heb ik een nieuwe SDK nodig om de Tabel-API te gebruiken?

Nee, bestaande opslag-SDK's moeten nog steeds werken. Het is echter aan te raden dat men altijd de nieuwste SDK's krijgt voor de beste ondersteuning en in veel gevallen superieure prestaties. Zie de lijst met beschikbare talen in de [inleiding tot Azure Cosmos DB Table API](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Waar is Table-API niet identiek aan het opslaggedrag van Azure Table?

Er zijn enkele gedragsverschillen die gebruikers die afkomstig zijn van Azure Table-opslag die tabellen willen maken met de Azure Cosmos DB Table API, zich bewust moeten zijn van:

* Azure Cosmos DB Table API maakt gebruik van een gereserveerd capaciteitsmodel om gegarandeerde prestaties te garanderen, maar dit betekent dat men betaalt voor de capaciteit zodra de tabel wordt gemaakt, zelfs als de capaciteit niet wordt gebruikt. Met Azure Table-opslag betaalt men alleen voor de gebruikte capaciteit. Dit helpt om uit te leggen waarom Table API een 10 ms read en 15 ms write SLA kan bieden op het 99e percentiel, terwijl Azure Table-opslag een SLA van 10 seconden biedt. Maar als gevolg hiervan, met Tabel API tabellen, zelfs lege tabellen zonder aanvragen, geld kosten om ervoor te zorgen dat de capaciteit beschikbaar is om eventuele aanvragen te behandelen op de SLA aangeboden door Azure Cosmos DB.
* Queryresultaten die door de tabel-API worden geretourneerd, worden niet gesorteerd in partitiesleutel/rijsleutelvolgorde zoals in Azure Table-opslag.
* Rijtoetsen kunnen maximaal 255 bytes bedragen
* Batches kunnen maximaal 2 mb's hebben
* CORS wordt momenteel niet ondersteund
* Tabelnamen in Azure Table-opslag zijn niet hoofdlettergevoelig, maar wel in Azure Cosmos DB Table API
* Sommige interne indelingen van Azure Cosmos DB voor coderingsgegevens, zoals binaire velden, zijn momenteel niet zo efficiënt als men zou willen. Dit kan dus leiden tot onverwachte beperkingen op de grootte van de gegevens. Op dit moment kan men bijvoorbeeld de volledige Meg van een tabelentiteit niet gebruiken om binaire gegevens op te slaan omdat de codering de grootte van de gegevens vergroot.
* Eigenschappennaam 'ID' van entiteit die momenteel niet wordt ondersteund
* TabelQuery TakeCount is niet beperkt tot 1000

Wat de REST API betreft, zijn er een aantal eindpunten/queryopties die niet worden ondersteund door Azure Cosmos DB Table API:

| Restmethode(en) | Resteindpunt/queryoptie | Doc-URL's | Uitleg |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=eigenschappen| [Eigenschappen van tabelservice instellen](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) [en eigenschappen voor tabelservice opdoen](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Dit eindpunt wordt gebruikt om CORS-regels, configuratie van opslaganalyses en logboekinstellingen in te stellen. CORS wordt momenteel niet ondersteund en analyses en logboekregistratieworden anders verwerkt in Azure Cosmos DB dan Azure Storage Tables |
| Opties | /\<tabel-resource-naam> | [Pre-flight CORS-tabelaanvraag](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Dit is onderdeel van CORS dat Azure Cosmos DB momenteel niet ondersteunt. |
| GET | /?restype=service@comp=statistieken | [Tabelservicestatistieken bekijken](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Geeft informatie hoe snel gegevens worden gerepliceerd tussen primaire en secondaries. Dit is niet nodig in Cosmos DB als de replicatie is onderdeel van schrijft. |
| GET, PUT | /mytable?comp=acl | [Tabel ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) en [SetTable ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Hiermee wordt het beleid voor opgeslagen toegang dat wordt gebruikt om Gedeelde toegangshandtekeningen (SAS) te beheren en ingesteld. Hoewel SAS wordt ondersteund, worden ze anders ingesteld en beheerd. |

Daarnaast ondersteunt Azure Cosmos DB Table API alleen de JSON-indeling, niet ATOM.

Hoewel Azure Cosmos DB Shared Access Signatures (SAS) ondersteunt, zijn er bepaalde beleidsregels die het niet ondersteunt, met name beleid dat betrekking heeft op beheerbewerkingen, zoals het recht om nieuwe tabellen te maken.

Met name voor de .NET SDK zijn er enkele klassen en methoden die Azure Cosmos DB momenteel niet ondersteunt.

| Klasse | Niet-ondersteunde methode |
|-------|-------- |
| CloudTableClient | \*ServiceEigenschappen* |
|                  | \*ServiceStatistieken* |
| CloudTable | Machtigingen instellen* |
|            | Machtigingen voor berichten* |
| TableServiceContext | * (deze klasse is afgeschaft) |
| TableServiceEntiteit | " " |
| TableServiceExtensies | " " |
| TableServiceQuery | " " |

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hoe geef ik feedback over de SDK of bugs?

U uw feedback op een van de volgende manieren delen:

* [Gebruikersstem](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow is het beste voor het programmeren van vragen. Zorg ervoor dat uw vraag is [on-topic](https://stackoverflow.com/help/on-topic) en [geef zo veel mogelijk details, waardoor de vraag duidelijk en beantwoordbaar](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Wat is de verbindingstekenreeks die ik moet gebruiken om verbinding te maken met de tabel-API?

De verbindingstekenreeks is:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

U de verbindingstekenreeks ophalen op de pagina Verbindingstekenreeks in de Azure-portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hoe overschrijf ik de config-instellingen voor de aanvraagopties in de .NET SDK voor de Tabel-API?

Sommige instellingen worden verwerkt op de CreateCloudTableClient-methode en andere via de app.config in de sectie appInstellingen in de clienttoepassing. Zie [Azure Cosmos DB-mogelijkheden](tutorial-develop-table-dotnet.md)voor informatie over config-instellingen.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Zijn er wijzigingen voor klanten die de bestaande Azure Table Storage SDKs gebruiken?

Geen. Er zijn geen wijzigingen voor bestaande of nieuwe klanten die de bestaande Azure Table Storage SDKs gebruiken.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hoe kan ik tabelgegevens weergeven die zijn opgeslagen in Azure Cosmos DB voor gebruik met de Tabel-API?

U de Azure-portal gebruiken om door de gegevens te bladeren. U ook de Api-code van tabellen of de hulpprogramma's gebruiken die in het volgende antwoord worden genoemd.

### <a name="which-tools-work-with-the-table-api"></a>Welke hulpprogramma's werken met de Tabel-API?

U de [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)gebruiken.

Hulpprogramma's met de flexibiliteit om een verbindingstekenreeks in de eerder opgegeven indeling te gebruiken, kunnen de nieuwe tabel-API ondersteunen. Een lijst met tabelhulpprogramma's wordt weergegeven op de pagina [Hulpprogramma's voor Azure Storage Client.](../storage/common/storage-explorers.md)

### <a name="is-the-concurrency-on-operations-controlled"></a>Wordt de gelijktijdigheid op operaties gecontroleerd?

Ja, optimistische gelijktijdigheid wordt geleverd via het gebruik van het ETag-mechanisme.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Wordt het OData-querymodel ondersteund voor entiteiten?

Ja, de Tabel-API ondersteunt OData-query en LINQ-query.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kan ik in dezelfde toepassing verbinding maken met Azure Table Storage en Azure Cosmos DB Table API?

Ja, u verbinding maken door twee afzonderlijke exemplaren van de CloudTableClient te maken, die elk via de verbindingstekenreeks naar een eigen URI verwijzen.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hoe migreer ik een bestaande Azure Table-opslagtoepassing naar deze aanbieding?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) en de [Azure Cosmos DB Data Migration Tool](import-data.md) worden beide ondersteund.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hoe wordt de uitbreiding van de opslaggrootte voor deze service gedaan als ik bijvoorbeeld begin met *n* GB aan gegevens en mijn gegevens in de loop van de tijd zullen groeien tot 1 TB?

Azure Cosmos DB is ontworpen om onbeperkte opslag te bieden via het gebruik van horizontale schaling. De service kan uw opslag monitoren en effectief verhogen.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hoe controleer ik het aanbod van de tabel-API?

U het deelvenster **Tabel-API-statistieken** gebruiken om aanvragen en opslaggebruik te controleren.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hoe bereken ik de doorvoer die ik nodig heb?

U de capaciteitsschatter gebruiken om de tabeldoorvoer te berekenen die nodig is voor de bewerkingen. Zie [Eenheden voor aanvragen voor schatting en gegevensopslag](https://www.documentdb.com/capacityplanner)voor meer informatie . In het algemeen u uw entiteit als JSON weergeven en de nummers voor uw activiteiten verstrekken.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kan ik de Table API SDK lokaal gebruiken met de emulator?

Momenteel niet.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Kan mijn bestaande toepassing werken met de Tabel-API?

Ja, dezelfde API wordt ondersteund.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Moet ik mijn bestaande Azure Table-opslagtoepassingen migreren naar de SDK als ik de functies van de Tabel-API niet wil gebruiken?

Nee, u bestaande Azure Table-opslagelementen maken en gebruiken zonder enige onderbreking. Als u de tabel-API echter niet gebruikt, u niet profiteren van de automatische index, de extra consistentieoptie of globale distributie.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hoe voeg ik replicatie van de gegevens toe in de tabel-API in meer dan één regio van Azure?

U de [globale replicatie-instellingen](tutorial-global-distribution-sql-api.md#portal) van de Azure Cosmos DB-portal gebruiken om regio's toe te voegen die geschikt zijn voor uw toepassing. Als u een wereldwijd gedistribueerde toepassing wilt ontwikkelen, moet u uw toepassing ook toevoegen met de informatieset PreferredLocation aan de lokale regio voor het bieden van lage leeslatentie.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hoe wijzig ik het primaire schrijfgebied voor het account in de tabel-API?

U het deelvenster Globale replicatieportal azure cosmos DB gebruiken om een regio toe te voegen en vervolgens niet over te geven aan het vereiste gebied. Zie Ontwikkelen [met Azure Cosmos DB-accounts met meerdere regio's](high-availability.md)voor instructies .

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hoe configureer ik mijn voorkeursleesregio's voor lage latentie wanneer ik mijn gegevens distribueer?

Gebruik de voorkeurslocatiesleutel in het bestand app.config om te helpen lezen vanaf de lokale locatie. Voor bestaande toepassingen wordt met de tabel-API een fout gegenereerd als LocationMode is ingesteld. Verwijder die code, omdat de tabel-API deze informatie opneemt uit het bestand app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hoe moet ik nadenken over consistentieniveaus in de tabel-API?

Azure Cosmos DB biedt goed onderbouwde afwegingen tussen consistentie, beschikbaarheid en latentie. Azure Cosmos DB biedt vijf consistentieniveaus voor tabel-API-ontwikkelaars, zodat u het juiste consistentiemodel op tabelniveau kiezen en individuele aanvragen indienen tijdens het opvragen van de gegevens. Wanneer een client verbinding maakt, kan deze een consistentieniveau opgeven. U het niveau wijzigen via het argument consistencyLevel van CreateCloudTableClient.

De Tabel-API biedt lezen met lage latentie met 'Lees uw eigen schrijft', met consistentie van Bounded-staleness als standaard. Zie [Consistentieniveaus](consistency-levels.md)voor meer informatie .

Azure Table-opslag biedt standaard sterke consistentie binnen een regio en uiteindelijke consistentie op de secundaire locaties.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Biedt Azure Cosmos DB Table API meer consistentieniveaus dan Azure Table-opslag?

Ja, zie [Consistentieniveaus](consistency-levels.md)voor informatie over hoe u profiteren van de gedistribueerde aard van Azure Cosmos DB. Omdat garanties worden gegeven voor de consistentieniveaus, u ze met vertrouwen gebruiken.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Hoe lang duurt het om de gegevens te repliceren wanneer globale distributie is ingeschakeld?

Azure Cosmos DB verbindt de gegevens blijvend in de lokale regio en duwt de gegevens onmiddellijk naar andere regio's in een kwestie van milliseconden. Deze replicatie is alleen afhankelijk van de retourtijd (RTT) van het datacenter. Zie [Azure Cosmos DB: een wereldwijd gedistribueerde databaseservice op Azure](distribute-data-globally.md)voor meer informatie over de wereldwijde distributiecapaciteit van Azure Cosmos DB.

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan het consistentieniveau van de leesaanvraag worden gewijzigd?

Met Azure Cosmos DB u het consistentieniveau instellen op containerniveau (op de tabel). Met de .NET SDK u het niveau wijzigen door de waarde voor tableConsistencyLevel-toets in het bestand app.config op te geven. De mogelijke waarden zijn: Sterk, Begrensdstaleness, Sessie, Consistent Voorvoegsel en Eventual. Zie [Tunable-gegevensconsistentieniveaus in Azure Cosmos DB](consistency-levels.md)voor meer informatie. Het belangrijkste idee is dat u het consistentieniveau van de aanvraag niet instellen op meer dan de instelling voor de tabel. U bijvoorbeeld het consistentieniveau voor de tabel niet instellen op Uiteindelijk en het consistentieniveau van de aanvraag op Sterk.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hoe verwerkt de tabel-API failover als een regio uitvalt?

De Tabel-API maakt gebruik van het wereldwijd gedistribueerde platform van Azure Cosmos DB. Als u ervoor wilt zorgen dat uw toepassing downtime voor datacenters kan tolereren, schakelt u ten minste één regio in voor het account in de Azure Cosmos [DB-portal Ontwikkelen met Azure Cosmos DB-accounts met meerdere regio's.](high-availability.md) U de prioriteit van de regio instellen met behulp van de portal [Ontwikkelen met Azure Cosmos DB-accounts met meerdere regio's.](high-availability.md)

U zoveel regio's toevoegen als u wilt voor het account en bepalen waar het niet kan mislukken door een failoverprioriteit te geven. Om de database te gebruiken, moet u daar ook een toepassing verstrekken. Wanneer u dit doet, zullen uw klanten geen downtime ervaren. De [nieuwste .NET client SDK](table-sdk-dotnet.md) is auto homing, maar de andere SDK's niet. Dat wil zeggen, het kan detecteren de regio die is down en automatisch niet over naar de nieuwe regio.

### <a name="is-the-table-api-enabled-for-backups"></a>Is de Tabel-API ingeschakeld voor back-ups?

Ja, de Tabel-API maakt gebruik van het platform van Azure Cosmos DB voor back-ups. Back-ups worden automatisch gemaakt. Zie [Online back-up en herstel met Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)voor meer informatie.

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Indexeert de Tabel-API standaard alle kenmerken van een entiteit?

Ja, alle kenmerken van een entiteit worden standaard geïndexeerd. Zie [Azure Cosmos DB: Indexeringsbeleid voor](index-policy.md)meer informatie .

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Betekent dit dat ik niet meer dan één index hoef te maken om aan de query's te voldoen?

Ja, Azure Cosmos DB Table API biedt automatische indexering van alle kenmerken zonder schemadefinitie. Deze automatisering bevrijdt ontwikkelaars om zich te concentreren op de toepassing in plaats van op het maken en beheren van indexen. Zie [Azure Cosmos DB: Indexeringsbeleid voor](index-policy.md)meer informatie .

### <a name="can-i-change-the-indexing-policy"></a>Kan ik het indexeringsbeleid wijzigen?

Ja, u het indexeringsbeleid wijzigen door de indexdefinitie op te geven. Je moet goed coderen en ontsnappen aan de instellingen.

Voor de non-.NET SDKs kan het indexeringsbeleid alleen worden ingesteld in de portal van **Data Explorer,** naar de specifieke tabel die u wilt wijzigen en vervolgens naar de **& Instellingen**->indexeringsbeleid gaan, de gewenste wijziging aanbrengen en vervolgens **opslaan**.

Vanaf de .NET SDK kan het worden ingediend in het app.config-bestand:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB als platform lijkt veel mogelijkheden te hebben, zoals sorteren, aggregaten, hiërarchie en andere functionaliteit. Voegt u deze mogelijkheden toe aan de Tabel-API?

De tabel-API biedt dezelfde queryfunctionaliteit als Azure Table-opslag. Azure Cosmos DB biedt ook ondersteuning voor sorteren, statistische functies, georuimtelijke query, hiërarchie en een groot aantal ingebouwde functies. Zie [SQL-query's voor](how-to-sql-query.md)meer informatie .

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Wanneer moet ik Tabeldoorvoer voor de tabel-API wijzigen?

U moet Tabeldoorvoer wijzigen wanneer een van de volgende voorwaarden van toepassing is:

* U voert een uittreksel, transformatie en belasting (ETL) van gegevens uit, of u wilt veel gegevens in korte tijd uploaden.
* U hebt meer doorvoer nodig vanuit de container of van een set containers aan de achterkant. U ziet bijvoorbeeld dat de gebruikte doorvoer meer is dan de ingerichte doorvoer en dat u wordt beperkt. Zie [Doorvoer instellen voor Azure Cosmos-containers voor](set-throughput.md)meer informatie.

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kan ik de doorvoer van mijn tabel-API-tabel opschalen of opschalen?

Ja, u het schaalvenster van de Azure Cosmos DB-portal gebruiken om de doorvoer te schalen. Zie [Doorvoer instellen](set-throughput.md)voor meer informatie .

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Is een standaard tabeldoorvoerset ingesteld voor nieuw ingerichte tabellen?

Ja, als u de Tabeldoorvoer niet overschrijft via app.config en geen vooraf gemaakte container in Azure Cosmos DB gebruikt, maakt de service een tabel met doorvoer van 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Is er een wijziging van de prijzen voor bestaande klanten van de Azure Table-opslagservice?

Geen. Er is geen prijswijziging voor bestaande Azure Table-opslagklanten.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hoe wordt de prijs berekend voor de Tabel-API?

De prijs is afhankelijk van de toegewezen Tabeldoorvoer.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hoe ga ik om met tariefbeperking op de tabellen in Tabel API-aanbieding?

Als het aanvraagpercentage groter is dan de capaciteit van de ingerichte doorvoer voor de onderliggende container of een set containers, krijgt u een foutmelding en probeert de SDK de aanroep opnieuw door het beleid voor nieuwe pogingen toe te passen.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Waarom moet ik een doorvoer kiezen, afgezien van PartitionKey en RowKey om te profiteren van het Tabel API-aanbod van Azure Cosmos DB?

Azure Cosmos DB stelt een standaarddoorvoer voor uw container in als u deze niet opgeeft in het app.config-bestand of via de portal.

Azure Cosmos DB biedt garanties voor prestaties en latentie, met bovengrenzen bij gebruik. Deze garantie is mogelijk wanneer de motor governance kan afdwingen op de activiteiten van de huurder. Het instellen van TableThroughput zorgt ervoor dat u de gegarandeerde doorvoer en latentie krijgt, omdat het platform deze capaciteit reserveert en operationeel succes garandeert.

Door de doorvoerspecificatie te gebruiken, u deze elastisch wijzigen om te profiteren van de seizoensgebondenheid van uw toepassing, aan de doorvoerbehoeften te voldoen en kosten te besparen.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure Table-opslag is voor mij goedkoop geweest, omdat ik alleen betaal om de gegevens op te slaan en ik zelden beveel. De Azure Cosmos DB Table API-aanbieding lijkt me in rekening te brengen, ook al heb ik geen enkele transactie uitgevoerd of iets opgeslagen. Kun je het uitleggen?

Azure Cosmos DB is ontworpen als een wereldwijd gedistribueerd, SLA-gebaseerd systeem met garanties voor beschikbaarheid, latentie en doorvoer. Wanneer u doorvoer reserveert in Azure Cosmos DB, is deze gegarandeerd, in tegenstelling tot de doorvoer van andere systemen. Azure Cosmos DB biedt extra mogelijkheden die klanten hebben aangevraagd, zoals secundaire indexen en wereldwijde distributie.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Ik krijg nooit een quotum volledige"-melding (wat aangeeft dat een partitie vol is) wanneer ik gegevens inneem in Azure Table-opslag. Met de Table API krijg ik dit bericht. Is dit aanbod mij te beperken en dwingt me om mijn bestaande aanvraag te veranderen?

Azure Cosmos DB is een SLA-gebaseerd systeem dat onbeperkte schaal biedt, met garanties voor latentie, doorvoer, beschikbaarheid en consistentie. Zorg ervoor dat uw gegevensgrootte en -index beheersbaar en schaalbaar zijn om gegarandeerde premiumprestaties te garanderen. De limiet van 10 GB voor het aantal entiteiten of items per partitiesleutel is om ervoor te zorgen dat we geweldige lookup- en queryprestaties bieden. Om ervoor te zorgen dat uw toepassing goed schaalt, zelfs voor Azure Storage, raden we u aan *geen* hot partitie te maken door alle informatie in één partitie op te slaan en op te vragen.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Dus PartitionKey en RowKey zijn nog steeds vereist met de Tabel API?

Ja. Omdat het oppervlak van de Tabel-API vergelijkbaar is met dat van de Azure Table Storage SDK, biedt de partitiesleutel een efficiënte manier om de gegevens te distribueren. De rijsleutel is uniek binnen die partitie. De rijsleutel moet aanwezig zijn en kan niet null zijn zoals in de standaard SDK. De lengte van RowKey is 255 bytes en de lengte van PartitionKey is 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Wat zijn de foutberichten voor de Tabel-API?

Azure Table-opslag en Azure Cosmos DB Table API gebruiken dezelfde SDK's, zodat de meeste fouten hetzelfde zijn.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Waarom krijg ik gas wanneer ik veel tabellen na elkaar probeer te maken in de Tabel-API?

Azure Cosmos DB is een SLA-gebaseerd systeem dat latentie- en doorvoer-, beschikbaarheids- en consistentiegaranties biedt. Omdat het een ingericht systeem is, reserveert het middelen om deze vereisten te garanderen. De snelle mate van creatie van tabellen wordt gedetecteerd en beperkt. We raden u aan te kijken naar de snelheid van het maken van tabellen en deze te verlagen tot minder dan 5 per minuut. Houd er rekening mee dat de tabel-API een ingericht systeem is. Op het moment dat je het indient, begin je ervoor te betalen.

## <a name="gremlin-api"></a>Gremlin-API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Moet ik voor C#/.NET-ontwikkeling het pakket Microsoft.Azure.Graphs of Gremlin.NET gebruiken?

Azure Cosmos DB Gremlin API maakt gebruik van de open-source drivers als de belangrijkste connectoren voor de service. Dus de aanbevolen optie is om [drivers die worden ondersteund door Apache Tinkerpop](https://tinkerpop.apache.org/)te gebruiken.

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hoe worden RU/s in rekening gebracht bij het uitvoeren van query's in een grafiekdatabase?

Alle grafiekobjecten, vertices en randen worden weergegeven als JSON-documenten in de backend. Aangezien een Gremlin-query één of meer grafiekobjecten tegelijk kan wijzigen, zijn de kosten die eraan verbonden zijn rechtstreeks gerelateerd aan de objecten, randen die door de query worden verwerkt. Dit is hetzelfde proces dat Azure Cosmos DB gebruikt voor alle andere API's. Zie [Aanvraageenheden in Azure Cosmos DB](request-units.md) voor meer informatie.

De RU-heffing is gebaseerd op de werkgegevensset van de traversal en niet op de resultaatset. Als een query bijvoorbeeld een enkel hoekpunt als resultaat moet verkrijgen, maar onderweg meer dan één ander object moet doorkruisen, worden de kosten gebaseerd op alle grafiekobjecten die nodig zijn om het ene resultaathoek te berekenen.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Wat is de maximale schaal die een grafiekdatabase kan hebben in Azure Cosmos DB Gremlin API?

Azure Cosmos DB maakt gebruik van [horizontale partitionering](partition-data.md) om de toename van opslag- en doorvoervereisten automatisch aan te pakken. De maximale doorvoer- en opslagcapaciteit van een werkbelasting wordt bepaald door het aantal partities dat aan een bepaalde container is gekoppeld. Een Gremlin API-container heeft echter een specifieke set richtlijnen om een goede prestatie-ervaring op schaal te garanderen. Zie [partitioneren in het](partition-data.md) Azure Cosmos DB-artikel voor meer informatie over partitionering en aanbevolen procedures.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hoe kan ik beschermen tegen injectieaanvallen met Gremlin drivers?

De meeste native Apache Tinkerpop Gremlin-stuurprogramma's staan de optie toe om een woordenboek met parameters voor query-uitvoering te bieden. Dit is een voorbeeld van hoe het te doen in [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) en in [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Waarom krijg ik de fout "Gremlin Query Compilation Error: Unable to find any method"?

Azure Cosmos DB Gremlin API implementeert een subset van de functionaliteit die is gedefinieerd in het gremlin-oppervlak. Zie Het [ondersteuningsartikel gremlin voor](gremlin-support.md) ondersteunde stappen en meer informatie.

De beste oplossing is het herschrijven van de vereiste Gremlin-stappen met de ondersteunde functionaliteit, omdat alle essentiële Gremlin-stappen worden ondersteund door Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Waarom krijg ik de fout 'WebSocketException: The server returned statuscode '200' toen statuscode '101' werd verwacht?

Deze fout wordt waarschijnlijk geworpen wanneer het verkeerde eindpunt wordt gebruikt. Het eindpunt dat deze fout genereert, heeft het volgende patroon:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Dit is het eindpunt van documenten voor uw grafiekdatabase.  Het juiste eindpunt om te gebruiken is het Gremlin-eindpunt, dat de volgende indeling heeft:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Waarom krijg ik de fout 'RequestRateIsTooLarge'?

Deze fout betekent dat de toegewezen aanvraageenheden per seconde niet voldoende zijn om de query te serveren. Deze fout wordt meestal gezien wanneer u een query uitvoert die alle voordelen verkrijgt:

```
// Query example:
g.V()
```

Met deze query wordt geprobeerd alle hoekzakken uit de grafiek op te halen. Dus, de kosten van deze query zal gelijk zijn aan ten minste het aantal vertices in termen van RU's. De instelling RU/s moet worden aangepast om deze query aan te pakken.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Waarom worden mijn Gremlin-driververbindingen uiteindelijk verbroken?

Een Gremlin-verbinding wordt gemaakt via een WebSocket-verbinding. Hoewel WebSocket-verbindingen geen specifieke tijd hebben om te leven, beëindigt Azure Cosmos DB Gremlin API inactieve verbindingen na 30 minuten inactiviteit.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Waarom kan ik geen vloeiende API-aanroepen gebruiken in de native Gremlin-stuurprogramma's?

Vloeiende API-aanroepen worden nog niet ondersteund door de Azure Cosmos DB Gremlin API. Vloeiende API-aanroepen vereisen een interne opmaakfunctie die bekend staat als bytecode-ondersteuning en die momenteel niet wordt ondersteund door Azure Cosmos DB Gremlin API. Om dezelfde reden wordt het nieuwste Gremlin-JavaScript-stuurprogramma momenteel ook niet ondersteund.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Hoe kan ik de efficiëntie van mijn Gremlin-query's evalueren?

De previewstap **executionProfile()** kan worden gebruikt om een analyse van het queryuitvoeringsplan te geven. Deze stap moet worden toegevoegd aan het einde van een Gremlin-query, zoals wordt geïllustreerd in het volgende voorbeeld:

**Voorbeeld van query**

```
g.V('mary').out('knows').executionProfile()
```

**Voorbeelduitvoer**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

De uitvoer van het bovenstaande profiel geeft aan hoeveel tijd er wordt besteed aan het verkrijgen van de hoekpuntobjecten, de randobjecten en de grootte van de werkgegevensset. Dit heeft te maken met de standaardkostenmetingen voor Azure Cosmos DB-query's.

## <a name="cassandra-api"></a><a id="cassandra"></a>Cassandra API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Wat is de protocolversie die wordt ondersteund door Azure Cosmso DB Cassandra API? Is er een plan om andere protocollen te ondersteunen?

Apache Cassandra API voor Azure Cosmos DB ondersteunt vandaag CQL versie 4. Als u feedback hebt over het ondersteunen van andere protocollen, laat [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)het ons dan weten via [spraakfeedback van gebruikers](https://feedback.azure.com/forums/263030-azure-cosmos-db) of stuur een e-mail naar .

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Waarom is het kiezen van een doorvoer voor een tabel een vereiste?

Azure Cosmos DB stelt de standaarddoorvoer voor uw container in op basis van waar u de tabel maakt van - portal of CQL.
Azure Cosmos DB biedt garanties voor prestaties en latentie, met bovengrenzen bij gebruik. Deze garantie is mogelijk wanneer de motor governance kan afdwingen op de activiteiten van de huurder. Het instellen van doorvoer zorgt ervoor dat u de gegarandeerde doorvoer en latentie krijgt, omdat het platform deze capaciteit reserveert en het succes van de werking garandeert.
U de doorvoer elastisch wijzigen om te profiteren van de seizoensgebondenheid van uw toepassing en kosten te besparen.

Het doorvoerconcept wordt uitgelegd in het artikel [Aanvraageenheden in Azure Cosmos DB.](request-units.md) De doorvoer voor een tabel wordt gelijkmatig verdeeld over de onderliggende fysieke partities.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Wat is de standaard RU/s van de tabel wanneer deze is gemaakt via CQL? Wat als ik het moet veranderen?

Azure Cosmos DB gebruikt aanvraageenheden per seconde (RU/s) als valuta voor het leveren van doorvoer. Tabellen gemaakt via CQL hebben 400 RU. U de RU van het portaal wijzigen.

CQL (CQL)

```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Wat gebeurt er als de doorvoer is opgebruikt?

Azure Cosmos DB biedt garanties voor prestaties en latentie, met bovengrenzen bij gebruik. Deze garantie is mogelijk wanneer de motor governance kan afdwingen op de activiteiten van de huurder. Dit is mogelijk op basis van het instellen van de doorvoer, wat ervoor zorgt dat u de gegarandeerde doorvoer en latentie krijgt, omdat platform deze capaciteit reserveert en het succes van de werking garandeert.
Wanneer u deze capaciteit overneemt, krijgt u een overbelast foutbericht dat aangeeft dat uw capaciteit is opgebruikt.
0x1001 Overbelast: de aanvraag kan niet worden verwerkt omdat "Aanvraagpercentage groot is". Op dit moment is het essentieel om te zien welke bewerkingen en hun volume dit probleem veroorzaakt. U een idee krijgen over verbruikte capaciteit die over de ingerichte capaciteit gaat met statistieken op de portal. Dan moet u ervoor zorgen dat de capaciteit bijna even veel wordt verbruikt over alle onderliggende partities. Als u ziet dat het grootste deel van de doorvoer wordt verbruikt door één partitie, hebt u scheefgroei van de werkbelasting.

Er zijn statistieken beschikbaar die u laten zien hoe doorvoer wordt gebruikt gedurende uren, dagen en per zeven dagen, over partities of in totaal. Zie [Controleren en debuggen met statistieken in Azure Cosmos DB](use-metrics.md)voor meer informatie.

Diagnostische logboeken worden uitgelegd in het [diagnoslogartikel Azure Cosmos DB.](logging.md)

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Is de primaire sleutelkaart voor het partitiesleutelconcept van Azure Cosmos DB?

Ja, de partitiesleutel wordt gebruikt om de entiteit op de juiste locatie te plaatsen. In Azure Cosmos DB wordt het gebruikt om de juiste logische partitie te vinden die is opgeslagen op een fysieke partitie. Het partitioneringsconcept wordt goed uitgelegd in het [artikel Partitie en schaal in Azure Cosmos DB.](partition-data.md) De essentiële take away hier is dat een logische partitie niet moet gaan over de 10-GB limiet vandaag.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Wat gebeurt er als ik een volledige quotummelding krijg die aangeeft dat een partitie vol is?

Azure Cosmos DB is een SLA-gebaseerd systeem dat onbeperkte schaal biedt, met garanties voor latentie, doorvoer, beschikbaarheid en consistentie. Deze onbeperkte opslag is gebaseerd op horizontale schaal uit gegevens met behulp van partitionering als het belangrijkste concept. Het partitioneringsconcept wordt goed uitgelegd in het [artikel Partitie en schaal in Azure Cosmos DB.](partition-data.md)

De limiet van 10 GB voor het aantal entiteiten of items per logische partitie waaraan u zich moet houden. Om ervoor te zorgen dat uw toepassing goed schaalt, raden we u aan *geen* hot partitie te maken door alle informatie in één partitie op te slaan en op te vragen. Deze fout kan alleen komen als uw gegevens scheef zijn: dat wil zeggen,&nbsp;je hebt veel gegevens voor een partitiesleutel (meer dan 10 GB). U de distributie van gegevens vinden via de opslagportal. Manier om deze fout op te lossen is door de tabel opnieuw te maken en een gedetailleerde primaire (partitiesleutel) te kiezen, waardoor gegevens beter kunnen worden verdeeld.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Is het mogelijk om Cassandra API te gebruiken als key value store met miljoenen of miljarden individuele partitiesleutels?

Azure Cosmos DB kan onbeperkte gegevens opslaan door de opslag uit te schalen. Dit staat los van de doorvoer. Ja, u cassandra API altijd gebruiken om sleutel/waarden op te slaan en op te halen door de juiste primaire/partitiesleutel op te geven. Deze afzonderlijke sleutels krijgen hun eigen logische partitie en zitten bovenop fysieke partitie zonder problemen.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Is het mogelijk om meer dan één tabel te maken met Apache Cassandra API van Azure Cosmos DB?

Ja, het is mogelijk om meer dan één tabel te maken met Apache Cassandra API. Elk van deze tabellen wordt behandeld als eenheid voor doorvoer en opslag.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Is het mogelijk om meer dan één tabel achter elkaar te maken?

Azure Cosmos DB is resource-beheerd systeem voor zowel gegevens- als besturingsvlakactiviteiten. Containers zoals verzamelingen, tabellen zijn runtime-entiteiten die zijn ingericht voor een bepaalde doorvoercapaciteit. Het snel achter elkaar maken van deze containers wordt niet verwacht en wordt beperkt. Als u tests hebt die tabellen onmiddellijk laten vallen/maken, probeert u ze te plaatsen.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Wat is het maximum aantal tabellen dat kan worden gemaakt?

Er is geen fysieke limiet op het aantal [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) tabellen, stuur een e-mail op als je een groot aantal tabellen (waar de totale constante grootte gaat meer dan 10 TB aan gegevens) die moeten worden gemaakt van de gebruikelijke 10s of 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Wat is het maximale aantal keyspace dat we kunnen creëren?

Er is geen fysieke limiet op het aantal keyspaces omdat het [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) metagegevenscontainers zijn, stuur een e-mail op als je om de een of andere reden een groot aantal keyspaces hebt.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Is het mogelijk om veel gegevens in te brengen nadat u van de normale tabel bent begonnen?

De opslagcapaciteit wordt automatisch beheerd en neemt toe naarmate u meer gegevens invoert. U dus met vertrouwen zoveel gegevens importeren als u nodig hebt zonder knooppunten te beheren en in te richten, en meer.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Is het mogelijk om yaml-bestandsinstellingen te leveren om Apache Casssandra API van Azure Cosmos DB-gedrag te configureren?

Apache Cassandra API van Azure Cosmos DB is een platformservice. Het biedt compatibiliteit op protocolniveau voor het uitvoeren van bewerkingen. Het verbergt de complexiteit van beheer, monitoring en configuratie. Als ontwikkelaar/gebruiker hoeft u zich geen zorgen te maken over beschikbaarheid, grafstenen, sleutelcache, rijcache, bloeifilter en tal van andere instellingen. De Apache Cassandra API van Azure Cosmos DB richt zich op het leveren van lees- en schrijfprestaties die u nodig hebt zonder de overhead van configuratie en beheer.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Ondersteunt Apache Cassandra API voor Azure Cosmos DB de statusopdrachten knooppunttoevoeging/clusterstatus/knooppunt?

Apache Cassandra API is een platformservice die capaciteitsplanning maakt en inspeelt op de elasticiteitseisen voor doorvoer & opslag een fluitje van een cent. Met Azure Cosmos DB voorziet u in doorvoer, die u nodig hebt. Vervolgens u het op en neer een aantal keren door de dag zonder zorgen te maken over het toevoegen / verwijderen van knooppunten of het beheer ervan. Dit houdt in dat u het knooppunt, clusterbeheertool ook niet hoeft te gebruiken.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Wat gebeurt er met betrekking tot verschillende config-instellingen voor keyspace-creatie zoals eenvoudig/netwerk?

Azure Cosmos DB biedt wereldwijde distributie out-of-the-box voor beschikbaarheid en lage latentie redenen. U hoeft geen replica's of andere dingen in te stellen. Alle schrijfbewerkingen zijn altijd blijvend vastgelegd in elke regio waar u schrijft en tegelijkertijd prestatiegaranties biedt.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Wat gebeurt er met betrekking tot verschillende instellingen voor tabelmetadata zoals bloomfilter, caching, read repair change, gc_grace, compressie memtable_flush_period en meer?

Azure Cosmos DB biedt prestaties voor lees-/schrijf- en doorvoerzonder dat u een van de configuratie-instellingen hoeft aan te raken en deze per ongeluk hoeft te manipuleren.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Wordt time-to-live (TTL) ondersteund voor Cassandra tafels?

Ja, TTL wordt ondersteund.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Is het mogelijk om de status van het knooppunt, de replicastatus, de gc en de OS-parameters eerder te controleren met verschillende hulpprogramma's? Wat moet er nu in de gaten worden gehouden?

Azure Cosmos DB is een platformservice die u helpt de productiviteit te verhogen en u geen zorgen te maken over het beheren en bewaken van infrastructuur. U hoeft alleen maar te zorgen voor doorvoer die beschikbaar is op portal statistieken om te vinden of je krijgt gewurgd en verhogen of verlagen van die doorvoer.
Monitor [SLA's](monitor-accounts.md).
Gebruik [Metrische gegevens](use-metrics.md) Diagnostische [logboeken gebruiken](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Welke client SDKs kan werken met Apache Cassandra API van Azure Cosmos DB?

Apache Cassandra SDK's client drivers die CQLv3 gebruiken werden gebruikt voor clientprogramma's. Als u andere stuurprogramma's hebt die u gebruikt of [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)als u problemen ondervindt, stuurt u e-mail naar .

### <a name="is-composite-partition-key-supported"></a>Wordt de samengestelde partitiesleutel ondersteund?

Ja, u de normale syntaxis gebruiken om samengestelde partitiesleutel te maken.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Kan ik sstableloader gebruiken voor het laden van gegevens?

Nee, sstableloader wordt niet ondersteund.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Kan een on-premises Apache Cassandra-cluster worden gekoppeld aan de Cassandra API van Azure Cosmos DB?

Op dit moment heeft Azure Cosmos DB een geoptimaliseerde ervaring voor cloudomgevingen zonder overhead van bewerkingen. Als u koppeling nodig [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) hebt, stuurt u e-mail naar met een beschrijving van uw scenario. We werken aan het aanbieden om het on-premises/andere cloud Cassandra-cluster te koppelen aan de Cassandra API van Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>Biedt Cassandra API volledige back-ups?

Azure Cosmos DB biedt twee gratis volledige back-ups die vandaag met een interval van vier uur zijn gemaakt voor alle API's. Dit zorgt ervoor dat u geen back-upschema en andere dingen hoeft in te stellen.
Als u de retentie en frequentie wilt [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) wijzigen, stuurt u een e-mail naar of verhoogt u een ondersteuningsaanvraag. U vindt informatie over back-upmogelijkheden in het artikel [Automatische online back-up en herstel met het](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) azure cosmos DB-artikel.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hoe verwerkt de Cassandra API-account failover als een regio uitvalt?

De Azure Cosmos DB Cassandra API leent van het wereldwijd gedistribueerde platform van Azure Cosmos DB. Als u ervoor wilt zorgen dat uw toepassing downtime voor datacenters kan tolereren, schakelt u ten minste één regio in voor het account in de Azure Cosmos [DB-portal Ontwikkelen met Azure Cosmos DB-accounts met meerdere regio's.](high-availability.md) U de prioriteit van de regio instellen met behulp van de portal [Ontwikkelen met Azure Cosmos DB-accounts met meerdere regio's.](high-availability.md)

U zoveel regio's toevoegen als u wilt voor het account en bepalen waar het niet kan mislukken door een failoverprioriteit te geven. Om de database te gebruiken, moet u daar ook een toepassing verstrekken. Wanneer u dit doet, zullen uw klanten geen downtime ervaren.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Indexeert de Apache Cassandra API standaard alle kenmerken van een entiteit?

Cassandra API is van plan secundaire indexering te ondersteunen om selectieve index op bepaalde kenmerken te maken. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan ik de nieuwe Cassandra API SDK lokaal gebruiken met de emulator?

Ja, dit wordt ondersteund.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB als platform lijkt veel mogelijkheden te hebben, zoals change feed en andere functionaliteit. Worden deze mogelijkheden toegevoegd aan de Cassandra API?

De Apache Cassandra API biedt dezelfde CQL-functionaliteit als Apache Cassandra. We zijn van plan om te kijken naar de haalbaarheid van het ondersteunen van verschillende vermogens in de toekomst.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Feature x van de reguliere Cassandra API werkt niet zoals vandaag, waar kan de feedback worden verstrekt?

Feedback geven via [feedback van gebruikers.](https://feedback.azure.com/forums/263030-azure-cosmos-db)

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
