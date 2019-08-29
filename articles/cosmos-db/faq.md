---
title: Veelgestelde vragen over de verschillende API's in Azure Cosmos DB
description: Vind antwoorden op veelgestelde vragen over Azure Cosmos DB, een wereldwijd gedistribueerde, multi-model databaseservice. Meer informatie over de capaciteit, prestatieniveaus en te schalen.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: cb2b3246264d04ce97c45dff58979079a731998e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066084"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Veelgestelde vragen over de verschillende API's in Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Wat zijn typische gebruiksvoorbeelden voor Azure Cosmos DB?

Azure Cosmos DB is een goede keuze voor nieuwe web, mobiel, gaming en IoT-toepassingen waarbij automatisch schalen, voorspelbare prestaties, volgorde van orderresponstijden en de mogelijkheid om query te snel via schemavrije gegevens is belangrijk. Azure Cosmos DB toepassingsgegevensmodellen wilt herhalen voor snelle ontwikkeling en het ondersteunen van de continue iteratie van de toepassing gegevensmodellen over te brengen. Toepassingen die worden beheerd door gebruikers gegenereerde inhoud en gegevens zijn [algemene scenario's voor Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hoe biedt Azure Cosmos DB voorspelbare prestaties?

Een [aanvraageenheid](request-units.md) (ru/s) is de maateenheid voor doorvoer in Azure Cosmos DB. Een 1RU-door Voer komt overeen met de door Voer van het ophalen van een document van 1 KB. Elke bewerking in Azure Cosmos DB, met inbegrip van leesbewerkingen, schrijfbewerkingen, SQL-query's en uitvoeringen van opgeslagen procedures, heeft een deterministische RU-waarde die gebaseerd op de doorvoer die is vereist om de bewerking te voltooien. In plaats van nadenken over de CPU, IO en geheugen en hoe ze elke invloed op de doorvoer van uw toepassing, kunt u denken in termen van één RU maateenheid.

U kunt elke Azure Cosmos-container met ingerichte door Voer configureren in termen van RUs of door Voer per seconde. U kunt voor toepassingen van elke omvang, benchmarken van afzonderlijke aanvragen voor het meten van de RU-waarden en inrichten van een container voor het afhandelen van het totaal aantal aanvraageenheden voor alle aanvragen. U kunt ook omhoog of omlaag doorvoer van uw container schalen naarmate de behoeften van uw toepassing veranderen. Voor meer informatie over aanvraag eenheden en voor hulp bij het bepalen van de vereisten voor containers, probeert u de [doorvoer Calculator](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hoe biedt Azure Cosmos DB ondersteuning voor verschillende gegevensmodellen zoals sleutel/waarde, in kolomvorm, document en een graaf?

Sleutel/waarde (tabel), wat in kolomvorm, documenten en de grafiekgegevens zijn modellen alle systeemeigen worden ondersteund vanwege het ARS (atomen, records en -reeksen) ontwerp dat Azure Cosmos DB is gebouwd op. Atomen, records en -reeksen kunnen eenvoudig worden toegewezen en toegewezen aan verschillende gegevensmodellen. De Api's voor een subset van modellen zijn nu beschikbaar (SQL, MongoDB, Table en Gremlin) en andere specifiek voor aanvullende gegevens modellen zullen in de toekomst beschikbaar zijn.

Azure Cosmos DB heeft een schema agnostisch indexeren engine voor automatisch indexeren van alle gegevens die deze neemt zonder schema's of secundaire indexen van de ontwikkelaar. De engine is afhankelijk van een set van logische index-indelingen (omgekeerde, in kolomvorm, structuur) die de indeling van de index en de subsystemen voor queryverwerking loskoppelen. Cosmos DB biedt ook de mogelijkheid ter ondersteuning van een set wire-protocollen en API's op een uitbreidbare manier en efficiënt te vertalen naar het gegevensmodel core (1) en de logische index-indelingen (2) waardoor het een unieke kan meer dan één gegevensmodel systeemeigen ondersteunen.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Kan ik meerdere Api's gebruiken voor toegang tot mijn gegevens?

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. Wanneer multi-model betekent Azure Cosmos DB meerdere Api's en meerdere gegevens modellen ondersteunt, gebruiken verschillende Api's verschillende gegevens indelingen voor Storage en wire-protocol. SQL maakt bijvoorbeeld gebruik van JSON, MongoDB gebruikt BSON, tabel gebruikt EDM, Cassandra gebruikt CQL, Gremlin maakt gebruik van GraphSON. Als gevolg hiervan raden we u aan dezelfde API te gebruiken voor alle toegang tot de gegevens in een bepaald account.

Elke API werkt onafhankelijk, met uitzonde ring van de Gremlin-en SQL-API, die interoperabel zijn.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Compatibel is met Azure Cosmos DB HIPAA?

Ja, Azure Cosmos DB is compatibel met HIPAA. HIPAA stelt vereisten vast voor het gebruik, de openbaarmaking en de beveiliging van individueel identificeerbare gezondheidsinformatie. Zie [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) voor meer informatie.

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Wat zijn de opslaglimieten van Azure Cosmos DB?

Er is geen limiet voor de totale hoeveelheid gegevens die een container kunt opslaan in Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Wat zijn de doorvoerlimieten van Azure Cosmos DB?

Er is geen limiet voor de totale hoeveelheid doorvoer die een container in Azure Cosmos DB kan ondersteunen. Het belangrijkste idee is dat uw workload ongeveer gelijkmatig verdelen tussen een voldoende aantal partitiesleutels.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Direct en Gateway modi voor connectiviteit versleuteld?

Ja, beide modi worden altijd volledig versleuteld.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Wat kost Azure Cosmos DB?

Raadpleeg voor meer informatie, de [prijsinformatie van Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) pagina. Azure Cosmos DB-gebruikskosten worden bepaald door het aantal ingerichte containers, het aantal uren dat de containers online zijn en de ingerichte doorvoer voor elke container.

### <a name="is-a-free-account-available"></a>Is een gratis account beschikbaar?

Ja, kunt u aanmelden voor een account gedurende beperkte tijd gratis, zonder toezegging. Om u te registreren, gaat u naar [Azure Cosmosdb gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/) of lees meer in de [probeer Azure Cosmos DB Veelgestelde vragen over](#try-cosmos-db).

Als u bekend bent met Azure bent, kunt u zich aanmelden voor een [gratis Azure-account](https://azure.microsoft.com/free/), waardoor u 30 dagen en een tegoed om te proberen alle Azure-services. Als u een Visual Studio-abonnement hebt, kunt u ook in aanmerking komen voor [gratis Azure-tegoed](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) kunt gebruiken voor elke Azure-service.

U kunt ook de [Azure Cosmos DB-Emulator](local-emulator.md) ontwikkelen en testen van uw toepassing lokaal voor gratis, zonder dat het maken van een Azure-abonnement. Als u tevreden bent over hoe uw toepassing in de Azure Cosmos DB Emulator werkt, kunt u overstappen naar een Azure Cosmos DB-account in de cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hoe vind ik extra hulp met Azure Cosmos DB?

Als u wilt een technische vraag, kunt u posten naar een van deze twee vraag en antwoord forums:

* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow wordt aanbevolen voor vragen over programmeren. Zorg ervoor dat uw vraag is [op onderwerp](https://stackoverflow.com/help/on-topic) en [zo veel details bevatten mogelijk, waardoor de vraag duidelijke en kan worden beantwoord](https://stackoverflow.com/help/how-to-ask).

Om aan te vragen van nieuwe functies, maakt u een nieuwe aanvraag op [uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.

Andere vragen kunnen worden verstuurd naar het team via [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com), maar dit is een alias voor technische ondersteuning niet.

## <a id="try-cosmos-db"></a>Probeer Azure Cosmos DB-abonnementen

U kunt nu genieten van een beperkte tijd Azure Cosmos DB-ervaring zonder abonnement, zonder kosten en zonder verplichtingen. Als u zich voor een Try Azure Cosmos DB-abonnement, gaat u naar [Azure Cosmosdb gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/). Dit abonnement is gescheiden van de [gratis proefversie van Azure](https://azure.microsoft.com/free/), en kan worden gebruikt samen met een gratis proefversie van Azure of een Azure betaald abonnement.

Probeer Azure Cosmos DB-abonnementen worden weergegeven in de Azure-portal naast andere abonnementen die zijn gekoppeld aan uw gebruikers-ID.

De volgende voorwaarden van toepassing op abonnementen Try Azure Cosmos DB:

* Eén [door Voer ingerichte container](./set-throughput.md#set-throughput-on-a-container) per abonnement voor SQL, GREMLIN-API en tabel accounts.
* Maxi maal drie [ingerichte door Voer ingerichte verzamelingen](./set-throughput.md#set-throughput-on-a-container) per abonnement voor MongoDb-accounts.
* Eén [door Voer ingerichte data base](./set-throughput.md#set-throughput-on-a-database) per abonnement. Data bases met door Voer ingericht kunnen een wille keurig aantal containers bevatten.
* Opslagcapaciteit van 10 GB.
* Globale replicatie is beschikbaar in de volgende [Azure-regio's](https://azure.microsoft.com/regions/): VS-centraal, Europa-noord en Zuidoost-Azië
* Maximale door Voer van 5 K RU/s wanneer ingericht op het niveau van de container.
* Maximale door Voer van 20 K RU/s wanneer ingericht op database niveau.
* Abonnementen verlopen na 30 dagen en kunnen worden verlengd tot een bedrag van Maxi maal 31 dagen.
* Azure ondersteuningstickets kunnen niet worden gemaakt voor Try Azure Cosmos DB-accounts; echter, wordt ondersteuning geboden voor abonnees met bestaande ondersteuningsabonnementen.

## <a name="set-up-azure-cosmos-db"></a>Instellen van Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hoe registreer ik me voor Azure Cosmos DB?

Azure Cosmos DB is beschikbaar in de Azure-portal. Eerst zich registreren voor een Azure-abonnement. Nadat u zich hebt geregistreerd, kunt u een Azure Cosmos DB-account toevoegen aan uw Azure-abonnement.

### <a name="what-is-a-master-key"></a>Wat is een hoofdsleutel?

Een hoofdsleutel is een beveiligingstoken voor toegang tot alle resources voor een account. Personen met de sleutel hebt gelezen en schrijftoegang tot alle resources in de databaseaccount. Wees voorzichtig met het distribueren van hoofdsleutels. De primaire hoofd sleutel en secundaire hoofd sleutel zijn beschikbaar op de Blade **sleutels** van de [Azure Portal][azure-portal]. Zie [Toegangssleutels weergeven, kopiëren en opnieuw genereren](manage-with-cli.md#list-account-keys) voor meer informatie over sleutels.

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Wat zijn de regio's die u opgeeft als PreferredLocations kan worden ingesteld op?

De PreferredLocations-waarde kan worden ingesteld op een van de Azure-regio's waarin Cosmos DB beschikbaar is. Zie voor een lijst van beschikbare regio's, [Azure-regio's](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Is er iets die ik letten moet tijdens het distribueren van gegevens over de hele wereld via de datacenters van Azure?

Azure Cosmos DB aanwezig is in alle Azure regio's, zoals opgegeven op de [Azure-regio's](https://azure.microsoft.com/regions/) pagina. Omdat het de core-service is, heeft elk nieuw datacenter de aanwezigheid van een Azure Cosmos DB.

Wanneer u een regio, houd er rekening mee dat Azure Cosmos DB onafhankelijke en government-clouds respecteert. Dat wil zeggen, als u een account in een [soevereine regio](https://azure.microsoft.com/global-infrastructure/), kan niet worden gerepliceerd buiten die [soevereine regio](https://azure.microsoft.com/global-infrastructure/). U kunt replicatie in een andere onafhankelijke locaties van een buiten-account op deze manier niet inschakelen.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Is het mogelijk om over te schakelen van niveau containerdoorvoer wordt ingericht voor het inrichten van de database-level doorvoer? Of omgekeerd

Container en de inrichting van de database-level doorvoer zijn aparte aanbiedingen en schakelen tussen een van deze vereisen migreren van gegevens van bron naar bestemming. Dit betekent dat u wilt maken van een nieuwe database of een nieuwe verzameling en vervolgens migreren van gegevens met behulp van [bulksgewijs executor bibliotheek](bulk-executor-overview.md) of [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure cosmos DB biedt ondersteuning voor tijdseries analyseren?

Ja Azure cosmos DB biedt ondersteuning voor tijdseries analyseren, ziet u hier een voorbeeld van [reeks tijdpatroon](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Dit voorbeeld laat zien hoe u kunt gebruiken voor het bouwen van geaggregeerde weergaven via time series-gegevens-wijzigingenfeed. U kunt deze benadering uitbreiden met behulp van spark streaming of een andere processor voor streaming-gegevens.

## <a name="sql-api"></a>SQL-API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hoe start ik ontwikkelen op basis van de SQL-API?

Eerst moet u aanmelden voor een Azure-abonnement. Zodra u zich hebt aangemeld voor een Azure-abonnement, kunt u een SQL-API-container toevoegen aan uw Azure-abonnement. Zie [een Azure Cosmos-database account maken](create-sql-api-dotnet.md#create-account)voor instructies over het toevoegen van een Azure Cosmos DB-account.

Er zijn [SDK's](sql-api-sdk-dotnet.md) beschikbaar voor .NET, Python, Node.js, JavaScript en Java. Ontwikkelaars kunnen ook gebruiken de [RESTful HTTP-API's](/rest/api/cosmos-db/) om te communiceren met Azure Cosmos DB-resources uit verschillende platforms en talen.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Ik heb toegang tot de en-klare voorbeelden om aan de slag?

Voorbeelden voor de SQL API [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md), en [Python](sql-api-python-samples.md) SDK's zijn beschikbaar op GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>De database van de SQL-API biedt ondersteuning voor gegevens zonder schema?

Ja, de SQL-API kunt toepassingen om op te slaan zonder schemadefinities of hints willekeurige JSON-documenten. Gegevens zijn onmiddellijk beschikbaar voor query's via de Azure Cosmos DB SQL-QueryInterface.

### <a name="does-the-sql-api-support-acid-transactions"></a>De SQL-API biedt ondersteuning voor ACID-transactions?

Ja, de SQL-API biedt ondersteuning voor transacties van tussen meerder documenten uitgedrukt in JavaScript opgeslagen procedures en triggers. Transacties worden binnen het bereik van één partitie binnen elke container en uitgevoerd met ACID-semantiek als "Alles of niets," geïsoleerd van andere code en gebruikersaanvragen die gelijktijdig worden uitgevoerd. Als er uitzonderingen worden veroorzaakt door de server-side-uitvoering van JavaScript-toepassingscode, de hele transactie teruggedraaid. 

### <a name="what-is-a-container"></a>Wat is een container?

Een container is een groep van documenten en de bijbehorende JavaScript-toepassingslogica. Een container is een factureerbare entiteit, waar de [kosten](performance-levels.md) wordt bepaald door de doorvoer en opslag gebruikt. Containers kunnen omvatten een of meer partities of servers en voor het afhandelen van vrijwel onbeperkte hoeveelheid opslag of doorvoer kunnen schalen.

* Voor de SQL-API en de API van Cosmos DB voor MongoDB-accounts, wordt een container toegewezen aan een verzameling.
* Voor Cassandra en Table-API-accounts, wordt een container toegewezen aan een tabel.
* Voor accounts van de Gremlin-API, is een container worden toegewezen aan een grafiek.

Containers zijn ook de factureringsentiteiten voor Azure Cosmos DB. Elke container wordt gefactureerd per uur, op basis van de ingerichte doorvoer en opslagruimte gebruikt. Zie voor meer informatie, [prijzen voor Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Hoe maak ik een database?

U kunt databases maken met behulp van de [Azure-portal](https://portal.azure.com), zoals beschreven in [toevoegen van een verzameling](create-sql-api-java.md#add-a-container), een van de [Azure Cosmos DB SDK's](sql-api-sdk-dotnet.md), of de [REST-API's](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Hoe stel ik gebruikers en machtigingen in?

U kunt gebruikers en machtigingen maken met behulp van een van de [Cosmos DB API SDK's](sql-api-sdk-dotnet.md) of de [REST-API's](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>De SQL-API biedt ondersteuning voor SQL?

De SQL-query-taal ondersteund door SQL API-accounts is een uitgebreide subset van de queryfunctionaliteit die wordt ondersteund door SQL Server. De Azure Cosmos DB SQL-querytaal biedt geavanceerde hiërarchische en relationele operators en uitbreidingsmogelijkheden via JavaScript-gebaseerde, door gebruiker gedefinieerde functies (UDF's). JSON-grammatica biedt de mogelijkheid voor het JSON-documenten te modelleren als structuren met gelabelde knooppunten die worden gebruikt door zowel de automatische indexeringstechnieken van Azure Cosmos DB en de SQL-querydialect van Azure Cosmos DB. Zie het [SQL-query][query] artikel voor meer informatie over het gebruik van SQL-grammatica.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>De SQL-API biedt ondersteuning voor SQL-aggregatiefuncties?

De SQL-API biedt ondersteuning voor aggregatie van de lage latentie op elke schaal via statistische functies `COUNT`, `MIN`, `MAX`, `AVG`, en `SUM` via de SQL-grammatica. Zie voor meer informatie, [statistische functies](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hoe biedt de SQL-API gelijktijdigheid?

De SQL-API biedt ondersteuning voor Optimistisch gelijktijdigheidbeheer (OCC) via HTTP-entity-tags of ETags. Elke SQL-API-resource heeft een ETag en de ETag is ingesteld op de server telkens wanneer een document wordt bijgewerkt. De ETag-header en de huidige waarde zijn opgenomen in alle berichten met reacties. ETags kan worden gebruikt met de header If-Match om toe te staan van de server om te bepalen of een resource moet worden bijgewerkt. De If-Match-waarde is de ETag-waarde moet worden gecontroleerd tegen. Als de ETag-waarde overeenkomt met de ETag-waarde van de server, worden de resource wordt bijgewerkt. Als de ETag niet meer actueel is, de server worden geweigerd door de bewerking opnieuw uit met een ' HTTP 412 Precondition failure ' responscode. De client refetches vervolgens de bron uit voor het verkrijgen van de huidige ETag-waarde voor de resource. ETags kan bovendien met de If-None-Match-header worden gebruikt om te bepalen of een opnieuw ophalen van een resource nodig is.

Voor het gebruik van optimistische gelijktijdigheid in .NET, gebruiken de [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) klasse. Zie voor een voorbeeld van .NET [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) in het voorbeeld DocumentManagement op GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hoe voer ik transacties in de SQL-API?

De SQL-API biedt ondersteuning voor taalgeïntegreerde transacties via in JavaScript opgeslagen procedures en triggers. Alle databasebewerkingen in scripts worden uitgevoerd onder snapshot-isolatie. Als het is een verzameling met één partitie, wordt de uitvoering is afgestemd op de verzameling. Als de verzameling is gepartitioneerd, wordt de uitvoering is afgestemd op documenten met dezelfde partitiesleutel waarde binnen de verzameling. Er wordt aan het begin van de transactie een schermopname van de documentversies (ETags) gemaakt en de transactie wordt alleen uitgevoerd als het script kan worden uitgevoerd. Als JavaScript een fout genereert, wordt de transactie teruggedraaid. Zie voor meer informatie, [serverzijde JavaScript programmeren voor Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hoe kan ik bulksgewijs invoegen documenten in Cosmos DB?

U kunt bulksgewijs invoegen documenten in Azure Cosmos DB in een van de volgende manieren:

* Het bulksgewijs executor hulpprogramma, zoals beschreven in [met bulksgewijs executor .NET-bibliotheek](bulk-executor-dot-net.md) en [met bulksgewijs executor Java-bibliotheek](bulk-executor-java.md)
* Het hulpprogramma voor migratie, zoals beschreven in [Database-hulpprogramma voor migratie voor Azure Cosmos DB](import-data.md).
* Opgeslagen procedures, zoals beschreven in [serverzijde JavaScript programmeren voor Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Voert de SQL-API ondersteuning resourcekoppelingen?

Ja, omdat Azure Cosmos DB een RESTful-service is, resourcekoppelingen onveranderbaar zijn en kunnen in de cache worden opgeslagen. SQL-API-clients kunnen een header 'If-None-Match' voor leesbewerkingen voor een resource-achtige document of een verzameling opgeven en vervolgens hun lokale kopieën werken nadat de serverversie is gewijzigd.

### <a name="is-a-local-instance-of-sql-api-available"></a>Is een lokaal exemplaar van SQL-API beschikbaar?

Ja. De [Azure Cosmos DB-Emulator](local-emulator.md) biedt een uiterst betrouwbare en emulatie van de Cosmos DB-service. Het ondersteunt functionaliteit die identiek is aan Azure Cosmos DB, inclusief ondersteuning voor het maken en uitvoeren van query's JSON-documenten, inrichten en schalen van verzamelingen en het uitvoeren van opgeslagen procedures en triggers. U kunt ontwikkelen en testen van toepassingen met behulp van de Azure Cosmos DB-Emulator en implementeer deze in Azure op wereldwijde schaal door het maken van een configuratie voor één wijzigen in het verbindingseindpunt voor Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Waarom zijn lang drijvende-waarden in een document afgerond wanneer deze wordt bekeken in data explorer in de portal.

Dit is beperking van JavaScript. Java script maakt gebruik van drijvende-komma notatie nummers met dubbele precisie, zoals is opgegeven in IEEE 754. het kan veilig zijn om getallen tussen-(2<sup>53</sup> -1) en 2<sup>53</sup>-1 (bijvoorbeeld 9007199254740991) op te slaan.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Waar zijn de machtigingen in de objecthiërarchie toegestaan?

Het maken van machtigingen met ResourceTokens is toegestaan op het niveau van de container en de onderliggende objecten (zoals documenten, bijlagen). Dit houdt in dat bij het maken van een machtiging op de database of het accountniveau van een op dat moment is niet toegestaan.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Wat is de Azure Cosmos DB-API voor MongoDB?

De API van het Azure Cosmos DB voor MongoDB is een interprotocol Compatibility Layer waarmee toepassingen eenvoudig en transparant kunnen communiceren met de systeem eigen Azure Cosmos-data base-engine door gebruik te maken van bestaande Sdk's en stuur Programma's die door de community worden ondersteund voor MongoDB. Ontwikkel aars kunnen nu bestaande MongoDB-toolchains en-vaardig heden gebruiken om toepassingen te ontwikkelen die gebruikmaken van Azure Cosmos DB. Ontwikkel aars profiteren van de unieke mogelijkheden van Azure Cosmos DB, waaronder wereld wijde distributie met replicatie met meerdere masters, automatische indexering, onderhoud van back-ups, financieel ondersteunde service level agreements (Sla's), enzovoort.

### <a name="how-do-i-connect-to-my-database"></a>Hoe kan ik verbinding maken met mijn data base?

De snelste manier om verbinding te maken met een Cosmos-data base met de API van Azure Cosmos DB voor MongoDB, is naar de [Azure Portal](https://portal.azure.com). Ga naar uw account en klik vervolgens in het navigatiemenu links op **Quick Start**. Quick start is de beste manier om code fragmenten op te halen om verbinding te maken met uw data base.

Azure Cosmos DB wordt afgedwongen strikte beveiligingseisen- en normen. Azure Cosmos DB-accounts is verificatie en beveiligde communicatie via SSL vereist, dus zorg ervoor dat u ondersteuning voor TLSv1.2.

Zie [verbinding maken met uw Cosmos-data base met de API van Azure Cosmos DB voor MongoDb](connect-mongodb-account.md)voor meer informatie.

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Zijn er aanvullende fout codes waarmee ik moet omgaan tijdens het gebruik van de API van Azure Cosmos DB voor MongoDB?

Naast de algemene MongoDB-fout codes heeft de API van de Azure Cosmos DB voor MongoDB een eigen specifieke fout code:

| Fout               | Code  | Description  | Oplossing  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Het totale aantal aanvraageenheden verbruikt meer is dan de frequentie van de ingerichte aanvraageenheid voor de verzameling en is beperkt. | Houd rekening met schaal de doorvoer toegewezen opnieuw aan een container of een set van containers met de Azure-portal of opnieuw proberen. |
| ExceededMemoryLimit | 16501 | Als een service met meerdere tenants, heeft de bewerking van de client geheugen toegewezen overschreden. | Verklein het bereik van de bewerking door meer beperkende querycriteria of neem contact op met ondersteuning van de [Azure-portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Voor beeld <em>: &nbsp; db.&nbsp;getCollection (' gebruikers '). aggregate ([ &nbsp; &nbsp;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {naam: Andy}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {leeftijd:-1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Is het Simba-stuur programma voor MongoDB ondersteund voor gebruik met de API van Azure Cosmos DB voor MongoDB?

Ja, u kunt het Simba Mongo ODBC-stuur programma gebruiken met de API van Azure Cosmos DB voor MongoDB

## <a id="table"></a>Table-API

### <a name="how-can-i-use-the-table-api-offering"></a>Hoe kan ik de Table-API-aanbieding gebruiken?

De Azure Cosmos DB Table-API is beschikbaar in de [Azure Portal][azure-portal]. Eerst moet u aanmelden voor een Azure-abonnement. Nadat u zich hebt aangemeld, kunt u een Azure Cosmos DB Table-API-account toevoegen aan uw Azure-abonnement en klikt u vervolgens tabellen toevoegen aan uw account.

U vindt de ondersteunde talen en de bijbehorende snel starten in de [Inleiding tot Azure Cosmos DB Table-API](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Moet ik een nieuwe SDK gebruikmaakt van de Table-API?

Nee, bestaande opslag-SDK's moeten nog steeds werken. Het wordt echter aanbevolen dat een altijd Hiermee haalt u de nieuwste SDK's voor de beste ondersteuning en in veel gevallen hogere prestaties. Zie de lijst met beschikbare talen in de [Inleiding tot Azure Cosmos DB Table-API](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Waar is Table-API niet identiek zijn met Azure Table storage gedrag?

Er zijn enkele verschillen in werking die gebruikers die afkomstig zijn van Azure Table-opslag wilt maken van tabellen met de Azure Cosmos DB Table-API moeten rekening houden met:

* Azure Cosmos DB Table-API maakt gebruik van een model met gereserveerde capaciteit om ervoor te zorgen gegarandeerde prestaties, maar dit betekent dat een voor de capaciteit betaalt, zodra de tabel is gemaakt, zelfs als de capaciteit niet wordt gebruikt. Met Azure Table betaalt storage een alleen voor de opslagcapaciteit die wordt gebruikt. Dit helpt om uit te leggen waarom Table-API kunnen bieden een 10 ms lezen en 15 ms schrijven SLA in het 99e percentiel, terwijl Azure Table storage een SLA van 10 seconden biedt. Maar als gevolg hiervan met Table-API-tabellen, zelfs lege tabellen zonder alle aanvragen, geld kosten om ervoor te zorgen de capaciteit voor het afhandelen van de aanvragen die naar deze op de SLA die worden aangeboden door Azure Cosmos DB.
* De query resultaten die door de Table-API zijn geretourneerd, worden niet in de volg orde van de partitie sleutel/rij-sleutel gesorteerd zoals ze zich in azure Table Storage bevinden.
* Recordsleutels mag alleen bestaan uit maximaal 255 bytes
* Batches kunnen maximaal 2 MB/s alleen hebben
* CORS wordt momenteel niet ondersteund
* Tabel namen in azure Table Storage zijn niet hoofdletter gevoelig, maar bevinden zich in Azure Cosmos DB Table-API
* Aantal interne Azure Cosmos DB-indelingen voor codering informatie, zoals binaire velden zijn momenteel niet zo efficiënt als een mogelijk interessant vindt. Daarom kan dit leiden tot onverwachte beperkingen op van elke gegevensomvang. Bijvoorbeeld, kan niet momenteel een gebruiken de volledige één Meg van een Tabelentiteit voor het opslaan van binaire gegevens omdat de codering van de gegevens wordt groter.
* De naam van de entiteits eigenschap ' ID ' wordt momenteel niet ondersteund
* TableQuery TakeCount is niet beperkt tot 1000

Er zijn een aantal opties voor eindpunten/query's die niet worden ondersteund door Azure Cosmos DB Table-API in termen van de REST-API:

| Rest methode(n) | Rest-eindpunt/Query-optie | Document-URL 's | Uitleg |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /? restype =service@comp= eigenschappen| [Instellen van tabeleigenschappen Service](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) en [tabel-Service-eigenschappen ophalen](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Dit eindpunt wordt gebruikt om in te stellen CORS-regels, storage analytics-configuratie en instellingen voor logboekregistratie. CORS is momenteel niet ondersteund en analyses en logboekregistratie zijn verwerkt in Azure Cosmos DB dan Azure Storage-tabellen |
| OPTIES | /\<table-resource-name> | [Vooraf vlucht CORS-aanvraag voor tabel](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Dit maakt deel uit van CORS die op dit moment biedt geen ondersteuning voor Azure Cosmos DB. |
| GET | /? restype =service@comp= statistieken | [Ophalen van Table Service Stats](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Bevat informatie hoe snel van gegevens tussen primaire en secundaire replica's repliceren. Dit is niet nodig in Cosmos DB als de replicatie deel uit van schrijfbewerkingen maakt. |
| GET, PUT | /MyTable? comp acl = | [Tabel ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) en [tabel ACL instellen](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Dit opgehaald en ingesteld van de opgeslagen toegangsbeleid wordt gebruikt voor het beheren van Shared Access Signatures (SAS). Hoewel SAS wordt ondersteund, worden ze op een andere manier ingesteld en beheerd. |

Tabel-API van Azure Cosmos DB ondersteunt bovendien alleen de JSON-indeling, niet ATOM.

Azure Cosmos DB biedt ondersteuning voor zijn Shared Access Signatures (SAS) er bepaalde beleidsregels wordt niet ondersteund, specifiek die zijn gerelateerd aan management-bewerkingen zoals het recht om nieuwe tabellen te maken.

Voor de .NET SDK in het bijzonder, zijn er bepaalde klassen en methoden die momenteel geen ondersteuning voor Azure Cosmos DB bieden.

| Klasse | Niet-ondersteunde methode |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | SetPermissions * |
|            | GetPermissions * |
| TableServiceContext | * (wordt deze klasse afgeschaft) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Als een van deze verschillen een probleem voor uw project, neem dan contact op met [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) en laat ons weten.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hoe kan ik feedback geven over de SDK of fouten?

U kunt uw feedback delen in een van de volgende manieren:

* [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow wordt aanbevolen voor vragen over programmeren. Zorg ervoor dat uw vraag is [op onderwerp](https://stackoverflow.com/help/on-topic) en [zo veel details bevatten mogelijk, waardoor de vraag duidelijke en kan worden beantwoord](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Wat is de verbindingsreeks die ik wil gebruiken voor verbinding met de Table-API?

De verbindingsreeks is:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

U kunt de verbindingsreeks ophalen uit de pagina verbindingsreeks in Azure portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hoe overschrijf ik de configuratie-instellingen voor de Aanvraagopties in de .NET SDK voor de Table-API?

Sommige instellingen worden verwerkt op de CreateCloudTableClient methode en andere via de app.config in de sectie appSettings in de clienttoepassing. Zie voor meer informatie over configuratie-instellingen [mogelijkheden van Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Zijn er wijzigingen voor klanten die gebruik van de bestaande Azure Table storage-SDK's maken?

Geen. Er zijn geen wijzigingen voor bestaande of nieuwe klanten die gebruik van de bestaande Azure Table storage-SDK's maken.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hoe kan ik gegevens in de tabel die opgeslagen in Azure Cosmos DB voor gebruik met de Table-API bekijken?

De Azure-portal kunt u de gegevens bladeren. U kunt ook de Table-API-code of de hulpprogramma's die worden vermeld in het volgende antwoord gebruiken.

### <a name="which-tools-work-with-the-table-api"></a>Welke hulpprogramma's werken met de Table-API?

U kunt de [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Hulpprogramma's met de flexibiliteit om op te nemen van een verbindingsreeks in de opgegeven indeling kunnen eerder ondersteunen de nieuwe tabel-API. Een lijst hulpmiddelen voor tabellen vindt u op de [Azure Storage-clienthulpprogramma's](../storage/common/storage-explorers.md) pagina.

### <a name="is-the-concurrency-on-operations-controlled"></a>Is de waarde voor concurrency voor bewerkingen die worden beheerd?

Ja, optimistische gelijktijdigheid wordt geboden via het gebruik van het mechanisme voor ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Wordt het OData-query-model voor entiteiten ondersteund?

Ja, ondersteunt de Table-API OData-query en LINQ-query.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kan ik verbinding maken met Azure Table Storage en Azure Cosmos DB Table-API naast elkaar in dezelfde toepassing?

Ja, u kunt verbinding maken met het maken van twee afzonderlijke exemplaren van de CloudTableClient, elke die verwijst naar een eigen URI via de verbindingsreeks.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hoe Migreer ik een bestaande Azure Table storage-toepassing op deze aanbieding?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) en de [hulpprogramma voor migratie van Azure Cosmos DB Data](import-data.md) worden beide ondersteund.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hoe is uitbreiding van de grootte van de voor deze service wordt uitgevoerd als een voorbeeld: ik met beginnen *n* GB aan gegevens en de gegevens na verloop van tijd tot 1 TB zal toenemen?

Azure Cosmos DB is ontworpen voor onbeperkte opslag via het gebruik van horizontaal schalen. De service kunt bewaken en effectief verhogen uw opslag.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hoe controleer ik de Table-API-aanbieding?

U kunt de Table-API **metrische gegevens** deelvenster voor het bewaken van aanvragen en opslaggebruik.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hoe ik de doorvoer die ik nodig berekend?

U kunt de capaciteit estimator gebruiken voor het berekenen van de TableThroughput die zijn voor de bewerkingen vereist. Zie voor meer informatie, [schatting Aanvraageenheden en gegevensopslag](https://www.documentdb.com/capacityplanner). In het algemeen kunt u laten zien uw entiteit als JSON en bieden van de getallen voor uw activiteiten.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kan ik de SDK voor Table-API lokaal gebruikt met de emulator?

Momenteel niet.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Kan mijn bestaande toepassingen werken met de Table-API?

Ja, dezelfde API wordt ondersteund.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Moet ik mijn bestaande Azure Table storage-toepassingen naar de SDK migreren als ik niet wilt dat de Table-API-functies te gebruiken?

Nee, kunt u maken en gebruiken van bestaande Azure Table storage activa zonder onderbreking van welke aard dan ook. Als u niet de Table-API gebruikt, kunt u niet kan echter voor profiteren van de automatische index, de optie als u meer consistentie of wereldwijde distributie.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hoe voeg ik replicatie van de gegevens in de Table-API in meer dan één regio van Azure?

U kunt de Azure Cosmos DB-portal [globale replicatie-instellingen](tutorial-global-distribution-sql-api.md#portal) om toe te voegen regio's die geschikt voor uw toepassing zijn. Voor het ontwikkelen van een wereldwijd gedistribueerde toepassing, moet u ook uw toepassing toevoegen met de informatie PreferredLocation is ingesteld op de lokale regio voor het ontwikkelen van wachttijden lezen.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hoe wijzig ik de primaire regio voor schrijven voor het account in de Table-API?

Het Azure Cosmos DB globale replicatie portal deelvenster kunt u een regio toevoegt en vervolgens een failover uitvoeren naar de vereiste regio. Zie voor instructies [ontwikkelen met Azure Cosmos DB-accounts voor meerdere regio's](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hoe configureer ik mijn voorkeur leesregio's voor lage latentie als ik mijn gegevens distribueren?

Om te lezen uit de lokale locatie, gebruikt u de PreferredLocation-sleutel in het bestand app.config. Voor bestaande toepassingen genereert de Table-API een fout als LocationMode is ingesteld. Deze code wordt niet verwijderen omdat de tabel-API deze gegevens uit het bestand app.config haalt. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Wat moet ik doen over de consistentieniveaus in de Table-API?

Azure Cosmos DB biedt goed gemotiveerd wisselwerking tussen consistentie, beschikbaarheid en latentie. Azure Cosmos DB biedt vijf consistentieniveaus voor ontwikkelaars van de Table-API, zodat u kunt het juiste consistentie-model op het tabelniveau van de en maakt afzonderlijke aanvragen tijdens het opvragen van de gegevens. Wanneer een client verbinding maakt, kan deze een consistentieniveau opgeven. U kunt het niveau van de via het argument consistencyLevel van CreateCloudTableClient wijzigen.

De Table-API biedt met lage latentie leest met "lezen uw eigen schrijfbewerkingen,' met consistentie voor gebonden veroudering als standaardwaarde. Zie voor meer informatie, [consistentieniveaus](consistency-levels.md).

Azure Table storage biedt standaard sterke consistentie binnen een regio en uiteindelijk consistentie in de secundaire locatie.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Biedt Azure Cosmos DB Table-API meer consistentieniveaus dan Azure Table storage?

Ja, voor informatie over hoe u kunt profiteren van de gedistribueerde aard van Azure Cosmos DB, Zie [consistentieniveaus](consistency-levels.md). Omdat garanties zijn opgegeven voor de consistentieniveaus, kunt u ze kunt gebruiken met vertrouwen.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Wanneer de wereldwijde distributie is ingeschakeld, hoe lang duurt om de gegevens te repliceren?

Azure Cosmos DB de gegevens in de lokale regio blijvend wordt doorgevoerd en de gegevens naar andere regio's onmiddellijk in een kwestie van milliseconden gepusht. Deze replicatie is alleen afhankelijk van de retourtijd (RTT) van het datacenter. Zie [Azure Cosmos DB voor meer informatie over de mogelijkheden van de globale distributie van Azure Cosmos DB: Een wereld wijd gedistribueerde database](distribute-data-globally.md)service in Azure.

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan het niveau van de leesaanvraag consistentie worden gewijzigd?

U kunt met Azure Cosmos DB, het consistentieniveau instellen op het niveau van de container (op de tabel). Met behulp van de .NET SDK, kunt u het niveau wijzigen door op te geven van de waarde voor TableConsistencyLevel sleutel in het bestand app.config. De mogelijke waarden zijn: Sterke, gebonden veroudering, sessie, consistent voor voegsel en uiteindelijk. Zie voor meer informatie, [gegevens instelbare consistentieniveaus in Azure Cosmos DB](consistency-levels.md). Het belangrijkste idee is dat u niet de consistentie van de aanvraag niveau op meer dan de instelling voor de tabel instellen. U kunt het consistentieniveau van de van de tabel bijvoorbeeld niet instellen op uiteindelijk en het niveau van de aanvraag voor consistentie op sterke.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hoe de Table-API failover af te handelen als een regio uitvalt?

De Table-API maakt gebruik van de wereldwijd gedistribueerde platform van Azure Cosmos DB. Inschakelen om ervoor te zorgen dat uw toepassing datacenter downtime kan tolereren, ten minste één meer regio voor het account in de Azure Cosmos DB-portal [ontwikkelen met Azure Cosmos DB-accounts voor meerdere regio's](high-availability.md). U kunt de prioriteit van de regio kunt instellen met behulp van de portal [ontwikkelen met Azure Cosmos DB-accounts voor meerdere regio's](high-availability.md).

U kunt zoveel regio's als u wilt gebruiken voor het account en waar het kan failover bepalen uitvoeren naar door te geven van een failover-prioriteit toevoegen. Voor het gebruik van de database, moet u er een toepassing te bieden. Als u dit doet, wordt niet uw klanten uitvaltijd moeten ervaren. De [nieuwste .NET client SDK](table-sdk-dotnet.md) homing-automatisch is, maar de andere SDK's niet. Dat wil zeggen, kan de regio die niet actief is en automatisch een failover uitvoeren naar de nieuwe regio detecteren.

### <a name="is-the-table-api-enabled-for-backups"></a>Is de Table-API voor back-ups ingeschakeld?

Ja, de Table-API maakt gebruik van het platform van Azure Cosmos DB voor back-ups. Back-ups worden automatisch gemaakt. Zie voor meer informatie, [Online back-up en herstel met Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>De Table-API alle kenmerken van een entiteit standaard indexeren?

Ja, alle kenmerken van een entiteit worden geïndexeerd. Zie [Azure Cosmos DB voor meer informatie: Indexerings](index-policy.md)beleid.

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Dit betekent dat ik hoef niet te maken van meer dan één index om te voldoen aan de query's gebruikt?

Ja, Azure Cosmos DB Table-API biedt automatische indexering van alle kenmerken zonder eventuele schemadefinitie. Deze automatisering kan ontwikkelaars zich concentreren op de toepassing in plaats van op index maken en beheren. Zie [Azure Cosmos DB voor meer informatie: Indexerings](index-policy.md)beleid.

### <a name="can-i-change-the-indexing-policy"></a>Kan ik het indexeringsbeleid wijzigen?

Ja, u kunt het indexeringsbeleid wijzigen door op te geven van de indexdefinitie. U moet om correct te coderen en als u de instellingen.

Voor de niet - .NET-SDK's, het indexeringsbeleid kan alleen worden ingesteld in de portal op **Data Explorer**, gaat u naar de specifieke tabel die u wilt wijzigen en ga vervolgens naar de **schaal en instellingen**Indexeringsbeleid -> Breng de gewenste wijziging en vervolgens **opslaan**.

Van de .NET SDK kan deze worden verstuurd in het bestand app.config:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB als platform lijkt te veel mogelijkheden, zoals sorteren, statistische functies, hiërarchie en andere functionaliteit hebben. Wordt u deze mogelijkheden toevoegen aan de tabel-API?

De Table-API biedt dezelfde queryfunctionaliteit als Azure Table storage. Azure Cosmos DB biedt ook ondersteuning voor sorteren, statistische functies, georuimtelijke query, hiërarchie en een groot aantal ingebouwde functies. Zie voor meer informatie, [SQL-query's](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Wanneer moet ik TableThroughput wijzigen voor de Table-API?

Wanneer een van de volgende voorwaarden van toepassing is, moet u TableThroughput wijzigen:

* Het uitvoeren van een extraheren, transformeren en laden (ETL) van gegevens, of u wilt uploaden van grote hoeveelheden gegevens in korte tijd.
* U moet meer doorvoer van de container of van een set van containers op de back-end. Bijvoorbeeld, ziet u dat de gebruikte doorvoer hoger dan de ingerichte doorvoer is en u bent ophalen beperkt. Zie [instellen van door Voer voor Azure Cosmos-containers](set-throughput.md)voor meer informatie.

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kan ik omhoog of omlaag schalen de doorvoer van mijn tabel Table-API?

Ja, kunt u de Azure Cosmos DB-portal schaal deelvenster aan de doorvoer te schalen. Zie voor meer informatie, [doorvoer instellen](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Is een standaard die tablethroughput voor nieuw ingerichte tabellen ingesteld?

Ja, als u niet de TableThroughput via app.config overschrijft en geen gebruik van een vooraf gemaakte container in Azure Cosmos DB, de service maakt een tabel met de doorvoer van 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Is er een wijziging van de prijzen voor bestaande klanten van de Azure Table storage-service?

Geen. Er is geen wijziging in de prijs voor bestaande klanten van Azure Table storage.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hoe wordt de prijs berekend voor de Table-API?

De prijs hangt af van de toegewezen TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hoe ga ik om een snelheidsbeperking op de tabellen in de tabel-API-aanbieding?

Als het aantal verwerkte aanvragen hoger dan de capaciteit van de ingerichte doorvoer voor de onderliggende container of een set van containers is, er een fout optreedt en de SDK probeert de aanroep opnieuw door toe te passen van het beleid voor opnieuw proberen.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Waarom moet ik Kies een doorvoer naast PartitionKey en RowKey om te profiteren van de Table-API-aanbieding van Azure Cosmos DB?

Als u niet in het bestand app.config of via de portal opgeeft, Azure Cosmos DB de doorvoer van een standaard ingesteld voor de container.

Azure Cosmos DB biedt gegarandeerd voor de prestaties en de latentie, met een bovengrens voor bewerking. Deze garantie is mogelijk wanneer de engine governance op bewerkingen van de tenant kunt afdwingen. Instellen van TableThroughput zorgt ervoor dat u de gegarandeerde doorvoer en latentie, omdat het platform deze capaciteit gereserveerd en operationele succes gegarandeerd.

Met behulp van de doorvoer-specificatie, kunt u flexibel wijzigen om te profiteren van de periodieke variatie van uw toepassing, voldoen aan de doorvoerbehoeften en kosten besparen.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure Table storage is goedkoop voor mij, omdat ik betaal alleen voor het opslaan van de gegevens, en ik zelden query. De Azure Cosmos DB Table-API-aanbieding lijkt te worden kosten in rekening gebracht me zelfs als ik nog niet hebt uitgevoerd van een enkele transactie of iets opgeslagen. Kunt u dat uitleggen?

Azure Cosmos DB is ontworpen om te worden van een wereldwijd gedistribueerde, op basis van een SLA-systeem met garanties met betrekking tot beschikbaarheid, latentie en doorvoer. Wanneer u de doorvoer in Azure Cosmos DB reserveren, deze kan worden gegarandeerd, in tegenstelling tot de doorvoer van andere systemen. Azure Cosmos DB biedt extra mogelijkheden, die klanten hebben aangevraagd, zoals secundaire indexen en wereldwijde distributie.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Ik krijg nooit een volledige quotum' melding (waarmee wordt aangegeven dat een partitie volledig is) wanneer ik opnemen van gegevens in Azure Table storage. Met de Table-API krijg ik dit bericht. Dit biedt me te beperken en dat ik opnieuw hoef te wijzigen van mijn bestaande toepassing?

Azure Cosmos DB is een SLA-systeem waarmee onbeperkte schaal, garanties met betrekking tot latentie, doorvoer, beschikbaarheid en consistentie. Gegarandeerde premium-prestaties, zorg ervoor dat uw gegevensgrootte en de index beheerbare en schaalbare zijn. De limiet van 10 GB voor het aantal entiteiten of items per partitiesleutel is om ervoor te zorgen dat we uitstekende prestaties voor zoeken en query's uitvoeren bieden. Om ervoor te zorgen dat uw toepassing, zelfs voor Azure Storage schaalt, raden wij aan dat u *niet* een hot partitie maken met alle informatie in één partitie op te slaan en het uitvoeren van query's.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Dus PartitionKey en RowKey zijn nog steeds vereist met de Table-API?

Ja. Omdat de surface area van de Table-API vergelijkbaar met die van de Azure Table storage-SDK is, biedt de partitiesleutel een efficiënte manier voor het distribueren van de gegevens. De rijsleutel is uniek zijn binnen deze partitie. De rijsleutel moet aanwezig zijn en mag niet null zijn als in de standard-SDK. De lengte van RowKey is 255 bytes en de lengte van PartitionKey is 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Wat zijn de foutberichten voor de Table-API?

Azure Table storage en Azure Cosmos DB Table-API gebruikt de dezelfde SDK's, zodat de meeste van de fouten hetzelfde zijn.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Waarom ik te maken met beperkingen wanneer ik probeer te veel tabellen na elkaar maken in de Table-API?

Azure Cosmos DB is een SLA-systeem waarmee latentie, doorvoer, beschikbaarheid en garanties voor consistentie. Omdat het een ingerichte systeem is, behoudt deze resources om te garanderen van deze vereisten. De snelle snelheid van het maken van tabellen is gedetecteerd en beperkt. U wordt aangeraden dat u kijken naar de snelheid van het maken van tabellen en het verlagen naar minder dan 5 per minuut. Houd er rekening mee dat de Table-API een ingerichte systeem is. Het moment dat u het inrichten wordt gestart om te betalen voor deze.

## <a name="gremlin-api"></a>Gremlin-API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Voor C# / .NET-ontwikkeling, moet ik gebruiken het Microsoft.Azure.Graphs pakket of Gremlin.NET?

Azure Cosmos DB Gremlin-API maakt gebruik van de open-source-stuurprogramma's als de belangrijkste connectors voor de service. Zodat de aanbevolen optie is het gebruik van [stuurprogramma's die worden ondersteund door Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hoe worden RU/s in rekening gebracht bij het uitvoeren van query's op een grafiekdatabase?

Alle graph-objecten, hoekpunten en randen, worden weergegeven als JSON-documenten in de back-end. Omdat een Gremlin-query op een wijzigen kunt of veel graph-objecten op een tijdstip, wordt de kosten die gepaard gaan met het rechtstreeks met betrekking tot de objecten, randen die worden verwerkt door de query. Dit is hetzelfde proces dat Azure Cosmos DB voor alle andere API's gebruikt. Zie voor meer informatie, [Aanvraageenheden in Azure Cosmos DB](request-units.md).

De RU-kosten zijn gebaseerd op de werkset van de gegevens van het transport en niet het resultaat ingesteld. Bijvoorbeeld, als een query is erop gericht om op te halen van een hoekpunt als gevolg hiervan, maar moet meer dan één ander object van de manier waarop passeren, klikt u vervolgens de kosten wordt worden gebaseerd op alle graph-objecten die nodig is voor het berekenen van het hoekpunt één resultaat.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Wat is de maximale schaal die een grafiekdatabase in Gremlin-API van Azure Cosmos DB hebben kunt?

Azure Cosmos DB maakt gebruik van [horizontale partitionering](partition-data.md) automatisch adres toename in de vereisten voor opslag en doorvoer. De maximale doorvoer en opslag van de capaciteit van een werkbelasting wordt bepaald door het aantal partities die gekoppeld aan een bepaalde verzameling zijn. Een verzameling Gremlin-API heeft echter een specifieke set richtlijnen om te controleren of een goede prestaties op schaal. Zie voor meer informatie over partitionering en aanbevolen procedures, [partitionering in Azure Cosmos DB](partition-data.md) artikel.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hoe kan ik beveiligen tegen aanvallen via injectie met behulp van Gremlin-stuurprogramma's?

De meeste systeem eigen Apache Tinkerpop Gremlin-Stuur Programma's bieden de mogelijkheid om een woorden lijst met para meters op te geven voor het uitvoeren van query's. Dit is een voorbeeld van hoe u dit doen [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) en [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Waarom krijg ik de fout ' Gremlin-query compilatie: Kan geen van de volgende methoden vinden: ' fout?

Azure Cosmos DB Gremlin-API implementeert een subset van de functionaliteit die is gedefinieerd in het gebied van Gremlin. Zie voor ondersteunde stappen en meer informatie, [Gremlin-ondersteuning](gremlin-support.md) artikel.

De beste oplossing is het herschrijven van de vereiste Gremlin-stappen met de ondersteunde functionaliteit, omdat alle essentiële Gremlin-stappen worden ondersteund door Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Waarom krijg ik de ' WebSocketException: De server heeft de status code ' 200 ' geretourneerd wanneer de status code ' 101 ' werd verwacht '?

Deze fout wordt waarschijnlijk gegenereerd wanneer het verkeerde eindpunt wordt gebruikt. Het eindpunt dat deze fout wordt gegenereerd, is het volgende patroon:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Dit is het eindpunt van de documenten voor de graph-database.  Het juiste eindpunt te gebruiken is de Gremlin-eindpunt met de volgende indeling:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Waarom krijg ik de fout 'RequestRateIsTooLarge'?

Deze fout betekent dat de toegewezen Aanvraageenheden per seconde alleen niet voldoende om de query. Deze fout wordt meestal gezien tijdens het uitvoeren van een query waarmee alle hoekpunten verkrijgt:

```
// Query example:
g.V()
```

Deze query zal proberen om op te halen van alle hoekpunten in de grafiek. De kosten van deze query is dus gelijk is aan ten minste het aantal hoekpunten in termen van ru's. De instelling van de RU/s moet worden aangepast om deze query op te lossen.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Waarom mijn Gremlin-stuurprogramma verbindingen verwijderd uiteindelijk?

Een Gremlin-verbinding wordt gemaakt via een WebSocket-verbinding. Hoewel WebSocket-verbindingen niet beschikken over een bepaald tijdstip met live, kunnen Azure Cosmos DB Gremlin API niet-actieve verbindingen wordt beëindigd na 30 minuten van inactiviteit.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Waarom gebruik ik fluent API-aanroepen kan niet in de systeemeigen Gremlin-stuurprogramma's?

Fluent API-aanroepen worden niet nog ondersteund door de Gremlin-API van Azure Cosmos DB. Fluent API-aanroepen moeten een interne opmaak functie wel bytecode ondersteuning die momenteel wordt niet ondersteund door Azure Cosmos DB Gremlin API. Vanwege om dezelfde reden is wordt de meest recente stuurprogramma voor Gremlin-JavaScript ook op dit moment niet ondersteund.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Hoe kan ik de efficiëntie van mijn Gremlin query's evalueren?

De **executionProfile()** preview stap kan worden gebruikt om zo een analyse van het queryplan kan worden uitgevoerd. Deze stap moet worden toegevoegd aan het einde van elke Gremlin-query zoals wordt geïllustreerd door het volgende voorbeeld:

**Voorbeeld van de query**

```
g.V('mary').out('knows').executionProfile()
```

**Voorbeeld van uitvoer**

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

In de uitvoer van het bovenstaande profiel ziet u hoeveel tijd wordt besteed aan het verkrijgen van de object Vertex objecten, de rand objecten en de grootte van de werk gegevensset. Dit heeft betrekking op de metingen standaardkosten voor Azure Cosmos DB-query's.

## <a id="cassandra"></a> Cassandra-API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Wat is de Protocol versie die wordt ondersteund door Azure Cosmso DB Cassandra-API? Is er een abonnement voor de ondersteuning van andere protocollen?

Apache Cassandra-API voor Azure Cosmos DB ondersteunt vandaag CQL-versie 4. Als u feedback hebt over andere protocollen ondersteunen, laat het ons weten [feedback van gebruikers](https://feedback.azure.com/forums/263030-azure-cosmos-db) of stuur een e-mail naar [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Waarom kiezen is een doorvoer voor een tabel een vereiste?

Azure Cosmos DB wordt standaard doorvoer ingesteld voor de container op basis van waar u de tabel - maken-portal of CQL.
Azure Cosmos DB biedt gegarandeerd voor de prestaties en de latentie, met een bovengrens voor bewerking. Deze garantie is mogelijk wanneer de engine governance op bewerkingen van de tenant kunt afdwingen. Doorvoer van de instelling zorgt ervoor dat u de gegarandeerde doorvoer en latentie, omdat het platform deze capaciteit gereserveerd en bewerking geslaagd garandeert.
U kunt flexibel wijzigen doorvoer om te profiteren van de periodieke variatie van uw toepassing en kosten te besparen.

Het concept van de doorvoer wordt uitgelegd in de [Aanvraageenheden in Azure Cosmos DB](request-units.md) artikel. De doorvoer voor een tabel wordt evenredig verdeeld over de onderliggende fysieke partities.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Wat is de standaardinstelling RU/s van tabel wanneer die zijn gemaakt via CQL? Wat gebeurt er als ik nodig om deze te wijzigen?

Azure Cosmos DB maakt gebruik van aanvraageenheden per seconde (RU/s) als een valuta om doorvoer te bieden. Tabellen die zijn gemaakt via CQL hebben 400 ru's. U kunt de ME uit vanuit de portal wijzigen.

CQL

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

### <a name="what-happens-when-throughput-is-used-up"></a>Wat gebeurt er wanneer de doorvoer is verbruikt?

Azure Cosmos DB biedt gegarandeerd voor de prestaties en de latentie, met een bovengrens voor bewerking. Deze garantie is mogelijk wanneer de engine governance op bewerkingen van de tenant kunt afdwingen. Dit is mogelijk op basis van het instellen van de doorvoer, wat zorgt ervoor dat u de gegarandeerde doorvoer en latentie, omdat het platform deze capaciteit gereserveerd en garandeert bewerking geslaagd.
Wanneer u via deze capaciteit gaat, krijgt u overbelaste fout bericht dat die uw capaciteit is verbruikt.
0x1001 overbelast: de aanvraag kan niet worden verwerkt omdat 'Aanvraagsnelheid hoog is'. In dit stadium is het essentieel om te zien welke bewerkingen en het volume zorgt ervoor dat dit probleem. U krijgt een idee van verbruikte capaciteit gebeurt via de ingerichte capaciteit met metrische gegevens in de portal. Vervolgens moet u ervoor zorgen capaciteit bijna wordt verbruikt gelijkmatig over alle onderliggende partities. Als u de meeste van de doorvoer wordt gebruikt door één partitie, hebt u scheeftrekken van werkbelasting.

Metrische gegevens zijn beschikbaar die laten zien u hoe doorvoer wordt gebruikt in uren, dagen, en per zeven dagen over meerdere partities of in een statistische functie. Zie voor meer informatie, [bewaking en foutopsporing met metrische gegevens in Azure Cosmos DB](use-metrics.md).

Logboeken met diagnostische gegevens worden beschreven de [Diagnostische logboekregistratie van Azure Cosmos DB](logging.md) artikel.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>De primaire sleutel toewijzen aan het belangrijkste concept van de partitie van Azure Cosmos DB biedt?

Ja, de partitiesleutel wordt gebruikt om de entiteit in de juiste locatie. In Azure Cosmos DB, wordt deze gebruikt om te zoeken rechts logische partitie die opgeslagen op een fysieke partitie. Het partitioneren concept wordt ook uitgelegd in de [partitioneren en schalen in Azure Cosmos DB](partition-data.md) artikel. De essentiële toets maken opslaan als volgt dat een logische partitie mag niet via de limiet van 10 GB vandaag nog lopen.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Wat gebeurt er wanneer ik een quotum volledig krijg'-melding die aangeeft dat een partitie vol is?

Azure Cosmos DB is een SLA-systeem waarmee onbeperkte schaal, garanties met betrekking tot latentie, doorvoer, beschikbaarheid en consistentie. Deze onbeperkte opslag is gebaseerd op de horizontale opwaartse gegevens met behulp van de belangrijkste concept partitioneren. Het partitioneren concept wordt ook uitgelegd in de [partitioneren en schalen in Azure Cosmos DB](partition-data.md) artikel.

De limiet van 10 GB voor het aantal entiteiten of items per logische partitie moet u voldoen aan. Om ervoor te zorgen dat uw toepassing goed schaalbaar, raden wij aan dat u *niet* een hot partitie maken met alle informatie in één partitie op te slaan en het uitvoeren van query's. Deze fout kan alleen worden geleverd als uw gegevens is vervormd: dat wil zeggen, hebt u grote hoeveelheden gegevens voor één partitiesleutel (meer dan 10&nbsp;GB). Hier vindt u de verdeling van gegevens met behulp van de opslag-portal. Er is een manier om deze fout te herstellen opnieuw maken in de tabel en kiest u een gedetailleerde primaire (partitiesleutel), waardoor betere verdeling van gegevens.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Is het mogelijk het gebruik van Cassandra-API als sleutel-waardearchief met miljoenen of miljarden afzonderlijke partitiesleutels?

Azure Cosmos DB kunt onbeperkte gegevens opslaan door het uitbreiden van de opslag. Dit is onafhankelijk van de doorvoer. U kunt de Cassandra-API Ja altijd alleen gebruiken voor het opslaan en ophalen van sleutel/waarden door de juiste primaire/partitiesleutel op te geven. Deze afzonderlijke sleutels ophalen van hun eigen logische partitie en zich op een fysieke partitie zonder problemen.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Is het mogelijk te maken van meer dan één tabel met Apache Cassandra-API van Azure Cosmos DB?

Ja, is het mogelijk te maken van meer dan één tabel met Apache Cassandra-API. Elk van deze tabellen wordt behandeld als eenheid voor doorvoer en opslag.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Is het mogelijk te maken van meer dan één tabel achter elkaar?

Azure Cosmos DB is resourcegestuurd systeem voor zowel gegevens als het besturingselement vlak-activiteiten. Containers, zoals verzamelingen, tabellen zijn runtime-entiteiten die zijn ingericht voor de opgegeven doorvoercapaciteit. Het maken van deze containers snel achter elkaar wordt niet verwacht activiteit en beperkt. Als u tests die drop/tabellen onmiddellijk maken hebt, proberen ze uitspreiden.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Wat is een maximum aantal tabellen dat kan worden gemaakt?

Er is geen fysieke limiet voor het aantal tabellen, stuurt u een e-mail op [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) als er een groot aantal tabellen (waarin de totale grootte van de constante gaat via 10 TB aan gegevens) die moeten worden gemaakt op basis van gebruikelijke 10s of 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Wat is het maximale aantal keyspace die we kunnen maken?

Er is geen fysieke limiet voor het aantal keyspaces wanneer ze de metagegevens van containers, stuurt u een e-mail op [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) als u een groot aantal keyspaces voor een of andere reden hebt.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Is het mogelijk om grote hoeveelheden gegevens na het starten van de normale tabel te betrekken?

De opslagcapaciteit wordt automatisch beheerd en neemt toe naarmate u meer gegevens pushen. Zo kunt u gegevens die u nodig hebt zonder beheer en inrichting van knooppunten, en meer vertrouwen importeren.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Is het mogelijk om op te geven van de instellingen voor yaml-bestand voor het configureren van Apache Casssandra API van Azure Cosmos DB-gedrag?

Apache Cassandra-API van Azure Cosmos DB is een platformservice. Het biedt protocol op compatibiliteit voor het uitvoeren van bewerkingen. Het wordt direct de complexiteit van het beheer, bewaking en configuratie verborgen. Als een ontwikkelaar/gebruiker moet u geen zorgen te maken over de beschikbaarheid, tombstones, sleutel cache, rij-cache, waas filter en vele andere instellingen. Azure Cosmos-DB Apache Cassandra-API richt zich op het bieden van lezen en schrijven van prestaties die u nodig zonder de overhead van de configuratie en beheer hebt.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Apache Cassandra-API voor Azure Cosmos DB biedt ondersteuning voor knooppunt toevoeging/cluster status/node status opdrachten?

Apache Cassandra-API is een platformservice waardoor capaciteitsplanning, reageren op de flexibiliteit van de vereisten voor doorvoer en opslag van een koud kunstje. Met Azure Cosmos DB u ingericht met doorvoer, moet u. Vervolgens kunt u deze omhoog en omlaag een willekeurig aantal keren via de dag schalen zonder u zorgen te maken over het toevoegen/verwijderen van knooppunten of ze beheren. Dit betekent dat u hoeft niet naar het knooppunt, voor Clusterbeheer te gebruiken.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Wat gebeurt er met betrekking tot verschillende configuratie-instellingen voor het maken van de keyspace zoals eenvoudige/netwerk?

Azure Cosmos DB biedt wereldwijde distributie buiten het vak voor beschikbaarheid en lage latentie redenen. U hoeft niet te setup replica's of andere dingen. Alle schrijf bewerkingen zijn altijd blijvend quorum die in elke regio waar u schrijft en die prestatie garanties bieden.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Wat gebeurt er met betrekking tot verschillende instellingen voor de metagegevens van de tabel, zoals waas worden gefilterd, caching, lezen herstellen wijzigen, gc_grace en compressie memtable_flush_period?

Azure Cosmos DB biedt prestaties voor lees-/ schrijfbewerkingen en doorvoer zonder dat nodig is voor een van de configuratie-instellingen aan te raken en ze per ongeluk te bewerken.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Wordt de time-to-live (TTL) voor Cassandra-tabellen ondersteund?

Ja, de TTL-waarde wordt ondersteund.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Is het mogelijk om de status van de monitor-knooppunt, status van replica, gc en OS-parameters eerder met verschillende hulpprogramma's? Wat er moet nu worden bewaakt?

Azure Cosmos DB is een platformservice waarmee u de productiviteit kunt verhogen en u geen zorgen kunt over het beheren en controleren van de infrastructuur. U hoeft voor de doorvoer die beschikbaar is op de portal metrische gegevens te vinden als u beperkt bent ophalen en vergroten of dat de doorvoer verkleinen.
Monitor [Sla's](monitor-accounts.md).
Gebruik [metrische gegevens](use-metrics.md) gebruik [diagnostische logboeken](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Welke client-SDK's kunnen werken met Apache Cassandra-API van Azure Cosmos DB?

De client Stuur Programma's van Apache Cassandra SDK die gebruikmaken van CQLv3, zijn gebruikt voor client Programma's. Als u andere stuurprogramma's die u gebruikt of als u problemen ondervindt verzend een e-mail naar [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Wordt samengesteld partitiesleutel ondersteund?

Ja, kunt u de syntaxis van de reguliere samengestelde partitiesleutel maken.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Kan ik sstableloader gebruiken voor het laden van gegevens?

Nee, sstableloader wordt niet ondersteund.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Kan een on-premises Apache Cassandra-cluster worden gekoppeld met de Cassandra-API van Azure Cosmos DB?

Op dit Azure Cosmos DB heeft een optimale ervaring voor cloudomgeving zonder de overhead van bewerkingen. Als u nodig hebt met het koppelen, verzend een e-mail naar [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) met een beschrijving van uw scenario. We werken aan het aanbieden om het on-premises/verschillende Cloud Cassandra-cluster te koppelen aan de Cassandra-API van Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>Biedt Cassandra-API van volledige back-ups?

Azure Cosmos DB biedt twee gratis volledige back-ups die op basis van vier uur interval vandaag nog voor alle API's. Dit zorgt ervoor dat u niet nodig hebt voor het instellen van een back-upschema en andere dingen.
Als u wijzigen bewaring en frequentie wilt, stuur een e-mail naar [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) of een ondersteuningsverzoek in te trekken. Informatie over back-upfunctionaliteit vindt u in de [automatische online back-up en herstel met Azure Cosmos DB](online-backup-and-restore.md) artikel.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hoe de Cassandra-API-account failover af te handelen als een regio uitvalt?

De Cassandra-API van Azure Cosmos DB leent van de wereldwijd gedistribueerde platform van Azure Cosmos DB. Inschakelen om ervoor te zorgen dat uw toepassing datacenter downtime kan tolereren, ten minste één meer regio voor het account in de Azure Cosmos DB-portal [ontwikkelen met Azure Cosmos DB-accounts voor meerdere regio's](high-availability.md). U kunt de prioriteit van de regio kunt instellen met behulp van de portal [ontwikkelen met Azure Cosmos DB-accounts voor meerdere regio's](high-availability.md).

U kunt zoveel regio's als u wilt gebruiken voor het account en waar het kan failover bepalen uitvoeren naar door te geven van een failover-prioriteit toevoegen. Voor het gebruik van de database, moet u er een toepassing te bieden. Als u dit doet, wordt niet uw klanten uitvaltijd moeten ervaren.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra-API index alle kenmerken van een entiteit standaard?

Cassandra-API is van plan om secundaire indexering te ondersteunen om selectieve index voor bepaalde kenmerken te maken. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan ik gebruiken de nieuwe SDK van de Cassandra-API lokaal met de emulator?

Ja, dit wordt ondersteund.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB als platform lijkt veel mogelijkheden te hebben, zoals het wijzigen van de feed en andere functionaliteit. Wordt deze mogelijkheden worden toegevoegd aan de Cassandra-API?

Apache Cassandra-API biedt dezelfde CQL-functionaliteit als Apache Cassandra. We bent van plan om uit te zoeken haalbaarheid van verschillende mogelijkheden in de toekomst ondersteunen.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Functie x van reguliere Cassandra-API werkt niet als het vandaag, waarbij kan feedback worden opgegeven?

Feedback geven via [feedback van gebruikers](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
