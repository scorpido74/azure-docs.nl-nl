---
title: Veelgestelde vragen over verschillende Api's in Azure Cosmos DB
description: Krijg antwoorden op veelgestelde vragen over Azure Cosmos DB, een wereld wijd gedistribueerde, multi-model database service. Meer informatie over capaciteit, prestatie niveaus en schalen.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 25b669493e1ee32df4a8c6fc33bdb1b21b08c70f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683334"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Veelgestelde vragen over verschillende API's in Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Wat zijn de typische gebruiks voorbeelden voor Azure Cosmos DB?

Azure Cosmos DB is een goede keuze voor nieuwe web-, mobiele, gaming-en IoT-toepassingen waarbij automatisch schalen, voorspel bare prestaties, snelle volg orde van milliseconde reactie tijden en de mogelijkheid om gegevens op te vragen over schema's. Dit is belang rijk. Azure Cosmos DB onderleent zichzelf bij het snel ontwikkelen en ondersteunen van de continue herhaling van toepassings gegevens modellen. Toepassingen die door de gebruiker gegenereerde inhoud en gegevens beheren, zijn [veelvoorkomende use cases voor Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hoe biedt Azure Cosmos DB voorspel bare prestaties?

Een [aanvraag eenheid](request-units.md) (ru) is de maat eenheid voor door voer in azure Cosmos db. Een 1RU-door Voer komt overeen met de door Voer van het ophalen van een document van 1 KB. Elke bewerking in Azure Cosmos DB, inclusief Lees bewerkingen, schrijf bewerkingen, SQL-query's en uitvoeringen van opgeslagen procedures, heeft een deterministische RU-waarde die is gebaseerd op de door Voer die is vereist om de bewerking te volt ooien. In plaats van te denken over CPU, i/o en het geheugen en de manier waarop deze elk van invloed zijn op de door Voer van uw toepassing, kunt u overwegen in termen van één RU-meting.

U kunt elke Azure Cosmos-container met ingerichte door Voer configureren in termen van RUs of door Voer per seconde. Voor toepassingen van elke schaal kunt u afzonderlijke aanvragen voor het meten van de RU-waarden bepalen en een container inrichten voor het afhandelen van het totale aantal aanvraag eenheden voor alle aanvragen. U kunt de door Voer van uw container ook omhoog of omlaag schalen als de behoeften van uw toepassing ontwikkelen. Voor meer informatie over aanvraag eenheden en voor hulp bij het bepalen van de vereisten voor containers, probeert u de [doorvoer Calculator](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hoe ondersteunt Azure Cosmos DB verschillende gegevens modellen, zoals sleutel/waarde, in kolom vorm, document en grafiek?

Gegevens modellen voor sleutel/waarde (tabel), in kolom vorm, document en grafiek worden allemaal systeem eigen ondersteund vanwege het ontwerp van de ARS (atomen, records en sequencers) waarop Azure Cosmos DB is gebouwd. Atomen, records en reeksen kunnen eenvoudig worden toegewezen en geprojecteerd aan verschillende gegevens modellen. De Api's voor een subset van modellen zijn nu beschikbaar (SQL, MongoDB, Table en Gremlin) en andere specifiek voor aanvullende gegevens modellen zullen in de toekomst beschikbaar zijn.

Azure Cosmos DB heeft een neutraal indexerings engine waarmee alle gegevens die worden opgenomen, automatisch kunnen worden geïndexeerd zonder schema of secundaire indexen van de ontwikkelaar. De engine is afhankelijk van een set logische index indelingen (omgekeerd, in kolom vorm, structuur) waarmee de opslag indeling wordt losgekoppeld van de subsystemen voor index en query verwerking. Cosmos DB heeft ook de mogelijkheid om een set bedradings protocollen en Api's op een uitbreid bare manier te ondersteunen en ze efficiënt te vertalen naar het kern gegevens model (1) en de logische index indelingen (2), waardoor het uniek is om meer dan één gegevens model te ondersteunen.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Kan ik meerdere Api's gebruiken voor toegang tot mijn gegevens?

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. Wanneer multi-model betekent Azure Cosmos DB meerdere Api's en meerdere gegevens modellen ondersteunt, gebruiken verschillende Api's verschillende gegevens indelingen voor Storage en wire-protocol. SQL maakt bijvoorbeeld gebruik van JSON, MongoDB gebruikt BSON, tabel gebruikt EDM, Cassandra gebruikt CQL, Gremlin maakt gebruik van JSON-indeling. Als gevolg hiervan raden we u aan dezelfde API te gebruiken voor alle toegang tot de gegevens in een bepaald account.

Elke API werkt onafhankelijk, met uitzonde ring van de Gremlin-en SQL-API, die interoperabel zijn.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Is Azure Cosmos DB HIPAA-compatibel?

Ja, Azure Cosmos DB is HIPAA-compatibel. HIPAA stelt vereisten vast voor het gebruik, de openbaarmaking en de beveiliging van individueel identificeerbare gezondheidsinformatie. Zie het [vertrouwens centrum van micro soft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)voor meer informatie.

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Wat zijn de opslag limieten van Azure Cosmos DB?

Er is geen limiet voor de totale hoeveelheid gegevens die een container kan opslaan in Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Wat zijn de doorvoer limieten van Azure Cosmos DB?

Er is geen limiet voor het totale aantal door voer dat een container kan ondersteunen in Azure Cosmos DB. Het is belang rijk om uw workload ongeveer gelijkmatig te verdelen over een voldoende aantal partitie sleutels.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Zijn directe en gateway connectiviteits modi versleuteld?

Ja beide modi zijn altijd volledig versleuteld.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Hoeveel kost het Azure Cosmos DB?

Raadpleeg de pagina met [prijs informatie voor Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie. Azure Cosmos DB gebruiks kosten worden bepaald door het aantal ingerichte containers, het aantal uur dat de containers online waren en de ingerichte door Voer voor elke container.

### <a name="is-a-free-account-available"></a>Is er een gratis account beschikbaar?

Ja, u kunt zich gratis aanmelden voor een gratis account, zonder toezeg ging. Als u zich wilt registreren, gaat u naar [Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) of leest u meer in het [Azure Cosmos DB Veelgestelde vragen over try](#try-cosmos-db)-out.

Als u niet bekend bent met Azure, kunt u zich aanmelden voor een [gratis Azure-account](https://azure.microsoft.com/free/), waardoor u 30 dagen en een tegoed krijgt om alle Azure-Services te proberen. Als u een Visual Studio-abonnement hebt, kunt u ook [gratis Azure-tegoeden](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) gebruiken voor elke Azure-service.

U kunt ook de [Azure Cosmos DB-emulator](local-emulator.md) gebruiken om uw toepassing gratis lokaal te ontwikkelen en te testen zonder een Azure-abonnement te maken. Als u tevreden bent over hoe uw toepassing in de Azure Cosmos DB Emulator werkt, kunt u overstappen naar een Azure Cosmos DB-account in de cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hoe kan ik aanvullende hulp krijgen bij Azure Cosmos DB?

Als u een technische vraag wilt stellen, kunt u een bericht plaatsen op een van deze twee vraag-en antwoord forums:

* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow is het meest geschikt voor het Program meren van vragen. Zorg ervoor dat u de vraag hebt [over het onderwerp](https://stackoverflow.com/help/on-topic) en [Geef zo veel mogelijk details op, zodat u de vraag duidelijk en beantwoord kunt krijgen](https://stackoverflow.com/help/how-to-ask).

Als u nieuwe functies wilt aanvragen, maakt u een nieuwe aanvraag voor [gebruikers spraak](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Probeer Azure Cosmos DB-abonnementen

U kunt nu profiteren van een beperkte Azure Cosmos DB ervaring zonder abonnement, kosteloos en toezeg gingen. Als u zich wilt registreren voor een try Azure Cosmos DB-abonnement, gaat u naar [Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) en gebruikt u persoonlijke Microsoft-account (MSA). Dit abonnement is gescheiden van de [gratis proef versie van Azure](https://azure.microsoft.com/free/)en kan worden gebruikt in combi natie met een gratis proef versie van Azure of een betaald Azure-abonnement.

Probeer Azure Cosmos DB abonnementen worden weer gegeven in de Azure Portal volgende andere abonnementen die zijn gekoppeld aan uw gebruikers-ID.

De volgende voor waarden zijn van toepassing om Azure Cosmos DB-abonnementen te proberen:

* Account toegang kan worden verleend aan persoonlijke micro soft-accounts (MSA). Vermijd het gebruik van Active Directory (AAD)-accounts of accounts die deel uitmaken van bedrijfsaad-tenants van het bedrijf, waardoor er mogelijk beperkingen gelden voor het blok keren van de toegang.
* Eén [door Voer ingerichte container](./set-throughput.md#set-throughput-on-a-container) per abonnement voor SQL, GREMLIN-API en tabel accounts.
* Maxi maal drie [ingerichte door Voer ingerichte verzamelingen](./set-throughput.md#set-throughput-on-a-container) per abonnement voor MongoDb-accounts.
* Eén [door Voer ingerichte data base](./set-throughput.md#set-throughput-on-a-database) per abonnement. Data bases met door Voer ingericht kunnen een wille keurig aantal containers bevatten.
* opslag capaciteit van 10 GB.
* Globale replicatie is beschikbaar in de volgende [Azure-regio's](https://azure.microsoft.com/regions/): Centraal vs, Europa-Noord en Zuidoost-Azië
* Maximale door Voer van 5 K RU/s wanneer ingericht op het niveau van de container.
* Maximale door Voer van 20 K RU/s wanneer ingericht op database niveau.
* Abonnementen verlopen na 30 dagen en kunnen worden verlengd tot een bedrag van Maxi maal 31 dagen.
* Er kunnen geen ondersteunings tickets voor Azure worden gemaakt voor het proberen van Azure Cosmos DB accounts; Er wordt echter ondersteuning geboden voor abonnees met bestaande ondersteunings abonnementen.

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB instellen

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hoe kan ik zich registreren voor Azure Cosmos DB?

Azure Cosmos DB is beschikbaar in de Azure Portal. Meld u eerst aan voor een Azure-abonnement. Nadat u zich hebt geregistreerd, kunt u een Azure Cosmos DB-account toevoegen aan uw Azure-abonnement.

### <a name="what-is-a-master-key"></a>Wat is een hoofdsleutel?

Een hoofdsleutel is een beveiligingstoken voor toegang tot alle resources voor een account. Personen met de sleutel hebben lees-en schrijf toegang tot alle resources in het database account. Wees voorzichtig wanneer u hoofd sleutels distribueert. De primaire hoofd sleutel en secundaire hoofd sleutel zijn beschikbaar op de Blade **sleutels** van de [Azure Portal][azure-portal]. Zie [Toegangssleutels weergeven, kopiëren en opnieuw genereren](manage-with-cli.md#list-account-keys) voor meer informatie over sleutels.

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Wat zijn de regio's waarop PreferredLocations kan worden ingesteld?

De PreferredLocations-waarde kan worden ingesteld op een van de Azure-regio's waarin Cosmos DB beschikbaar is. Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor een lijst met beschik bare regio's.

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Is er iets wat ik moet weten bij het distribueren van gegevens over de hele wereld via de Azure-data centers?

Azure Cosmos DB is aanwezig in alle Azure-regio's, zoals is opgegeven op de pagina [Azure-regio's](https://azure.microsoft.com/regions/) . Omdat het de kern service is, heeft elk nieuw Data Center een Azure Cosmos DB aanwezigheid.

Wanneer u een regio instelt, moet u er rekening mee houden dat Azure Cosmos DB respecteert aan soevereine en overheids Clouds. Dat wil zeggen, als u een account in een [soevereine regio](https://azure.microsoft.com/global-infrastructure/)maakt, u niet uit die [soevereine regio](https://azure.microsoft.com/global-infrastructure/)kunt repliceren. Op dezelfde manier kunt u de replicatie niet inschakelen in andere soevereine locaties vanuit een extern account.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Is het mogelijk om over te scha kelen van de inrichting voor het inrichten van container niveau naar het inrichten van de doorvoer capaciteit op database niveau? Of andersom

Het inrichten van een doorvoer capaciteit op basis van containers en data bases is afzonderlijke aanbiedingen en scha kelen tussen beide vereisen het migreren van gegevens van bron naar bestemming. Dit betekent dat u een nieuwe data base of een nieuwe container moet maken en vervolgens gegevens kunt migreren met behulp van de [bibliotheek voor bulksgewijs](bulk-executor-overview.md) uitvoerder of [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Ondersteunt Azure CosmosDB tijd reeks analyse?

Ja Azure CosmosDB ondersteunt tijd reeks analyse. Hier volgt een voor beeld van een [tijds reeks patroon](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). In dit voor beeld ziet u hoe u een wijzigings feed kunt gebruiken om geaggregeerde weer gaven te bouwen voor tijdreeks gegevens. U kunt deze aanpak uitbreiden door gebruik te maken van Spark streaming of een andere stream-gegevens processor.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Wat zijn de Azure Cosmos DB Service quota's en doorvoer limieten?

Zie de Azure Cosmos DB [service quota's](concepts-limits.md) en de [volledige limieten per container en database](set-throughput.md#comparison-of-models) artikelen voor meer informatie.

## <a name="sql-api"></a>SQL-API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hoe kan ik het ontwikkelen van de SQL-API te starten?

U moet zich eerst aanmelden voor een Azure-abonnement. Zodra u zich hebt aangemeld voor een Azure-abonnement, kunt u een SQL API-container toevoegen aan uw Azure-abonnement. Zie [een Azure Cosmos-database account maken](create-sql-api-dotnet.md#create-account)voor instructies over het toevoegen van een Azure Cosmos DB-account.

Er zijn [SDK's](sql-api-sdk-dotnet.md) beschikbaar voor .NET, Python, Node.js, JavaScript en Java. Ontwikkel aars kunnen ook gebruikmaken van de [rest HTTP-api's](/rest/api/cosmos-db/) om te communiceren met Azure Cosmos DB resources van verschillende platforms en talen.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kan ik een aantal kant-en-klare voor beelden gebruiken om aan de slag te gaan?

Voor beelden voor de SQL API [.net](sql-api-dotnet-samples.md)-, [Java](https://github.com/Azure/azure-documentdb-java)-, [node. js](sql-api-nodejs-samples.md)-en [python](sql-api-python-samples.md) -sdk's zijn beschikbaar op github.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Ondersteunt de SQL API-data base schema-vrije gegevens?

Ja, met de SQL-API kunnen toepassingen wille keurige JSON-documenten opslaan zonder schema definities of hints. Gegevens zijn direct beschikbaar voor query's via de Azure Cosmos DB SQL-query interface.

### <a name="does-the-sql-api-support-acid-transactions"></a>Ondersteunt de SQL API ACID-trans acties?

Ja, de SQL-API ondersteunt cross-document transacties, uitgedrukt in Java script-opgeslagen procedures en triggers. Trans acties bevinden zich in een enkele partitie binnen elke container en worden uitgevoerd met zure semantiek als ' alles of niets ', geïsoleerd van andere gelijktijdig uitvoering van code-en gebruikers aanvragen. Als uitzonde ringen worden veroorzaakt door de uitvoering van Java script-toepassings code aan de server zijde, wordt de hele trans actie teruggedraaid. 

### <a name="what-is-a-container"></a>Wat is een container?

Een container is een groep documenten en de bijbehorende java script-toepassings logica. Een container is een factureer bare entiteit, waarbij de [kosten](performance-levels.md) worden bepaald door de door Voer en de gebruikte opslag. Containers kunnen een of meer partities of servers omvatten en kunnen worden geschaald om vrijwel onbeperkte volumes van opslag of door voer te verwerken.

* Voor de SQL-API wordt een container toegewezen aan een container.
* Voor de API van Cosmos DB voor MongoDB-accounts wordt een container toegewezen aan een verzameling.
* Voor Cassandra-en Table-API-accounts wordt een container toegewezen aan een tabel.
* Voor Gremlin-API-accounts wordt een container toegewezen aan een grafiek.

Containers zijn ook de facturerings entiteiten voor Azure Cosmos DB. Elke container wordt per uur gefactureerd, op basis van de ingerichte door Voer en gebruikte opslag ruimte. Zie [Azure Cosmos DB prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)voor meer informatie.

### <a name="how-do-i-create-a-database"></a>Hoe maak ik een database?

U kunt data bases maken met behulp van de [Azure Portal](https://portal.azure.com), zoals beschreven in [een container toevoegen](create-sql-api-java.md#add-a-container), een van de [Azure Cosmos DB Sdk's](sql-api-sdk-dotnet.md)of de [rest-api's](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Hoe stel ik gebruikers en machtigingen in?

U kunt gebruikers en machtigingen maken met behulp van een van de [Cosmos DB-API-sdk's](sql-api-sdk-dotnet.md) of de rest- [api's](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Wordt SQL door de SQL-API ondersteund?

De SQL-query taal die wordt ondersteund door SQL-API-accounts is een uitgebreide subset van de query functionaliteit die wordt ondersteund door SQL Server. De Azure Cosmos DB SQL-query taal biedt uitgebreide hiërarchische en relationele Opera tors en uitbreid baarheid via op Java script gebaseerde, door de gebruiker gedefinieerde functies (Udf's). Met de JSON-grammatica kunnen JSON-documenten worden gemodelleerd als structuren met gelabelde knoop punten. deze worden gebruikt door de Azure Cosmos DB automatische indexerings technieken en het SQL-query dialect van Azure Cosmos DB. Zie het [SQL-query][query] artikel voor meer informatie over het gebruik van SQL-grammatica.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Ondersteunt de SQL-API SQL-aggregatie functies?

De SQL-API ondersteunt aggregatie met lage latentie op elke schaal via statistische `COUNT`functies `MIN`, `MAX`, `AVG`, en `SUM` via de SQL-grammatica. Zie [statistische functies](sql-query-aggregates.md)voor meer informatie.

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hoe biedt de SQL-API gelijktijdigheid?

De SQL API ondersteunt optimistische gelijktijdigheids beheer (OCC) via HTTP-entiteit Tags of ETags. Elke SQL-API-resource heeft een ETag en de ETag wordt ingesteld op de server telkens wanneer een document wordt bijgewerkt. De ETag-kop en de huidige waarde zijn opgenomen in alle antwoord berichten. ETags kan worden gebruikt met de if-match-header om de server toe te staan om te bepalen of een resource moet worden bijgewerkt. De if-match-waarde is de ETag-waarde die moet worden gecontroleerd. Als de ETag-waarde overeenkomt met de server ETag-waarde, wordt de resource bijgewerkt. Als de ETag niet langer actueel is, wordt de bewerking door de server geweigerd met de respons code ' HTTP 412-voor waarde voor fout '. De client haalt de resource vervolgens opnieuw op om de huidige ETag-waarde voor de resource te verkrijgen. Daarnaast kan ETags worden gebruikt in combi natie met de If-None-Match-header om te bepalen of een resource opnieuw moet worden opgehaald.

Als u optimistische gelijktijdigheid wilt gebruiken in .NET, gebruikt u de [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) -klasse. Zie [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) in het DocumentManagement-voor beeld op github voor een .net-voor beeld.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hoe kan ik trans acties uit te voeren in de SQL-API?

De SQL-API ondersteunt in talen geïntegreerde trans acties via Java script-opgeslagen procedures en triggers. Alle database bewerkingen binnen scripts worden uitgevoerd onder snap shot-isolatie. Als het een container met één partitie is, wordt de uitvoering binnen het bereik van de container. Als de container is gepartitioneerd, wordt de uitvoering beperkt tot documenten met dezelfde partitie sleutel waarde in de container. Er wordt aan het begin van de transactie een schermopname van de documentversies (ETags) gemaakt en de transactie wordt alleen uitgevoerd als het script kan worden uitgevoerd. Als JavaScript een fout genereert, wordt de transactie teruggedraaid. Zie [Java script-programmering aan de server zijde voor Azure Cosmos DB](stored-procedures-triggers-udfs.md)voor meer informatie.

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hoe kan ik documenten bulksgewijs invoegen in Cosmos DB?

U kunt op een van de volgende manieren bulksgewijs documenten in Azure Cosmos DB invoegen:

* Het hulp programma bulk-uitvoerder, zoals beschreven in het gebruik [Using bulk executor Java library](bulk-executor-java.md) van een bulk-uitvoerder [.net-bibliotheek](bulk-executor-dot-net.md)
* Het hulp programma voor gegevens migratie, zoals beschreven in [hulp programma voor database migratie voor Azure Cosmos DB](import-data.md).
* Opgeslagen procedures, zoals beschreven in [Java script-programmering aan server zijde voor Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Ondersteunt de SQL-API bron koppeling in cache?

Ja, omdat Azure Cosmos DB een REST-service is, zijn resource koppelingen onveranderbaar en kunnen ze in de cache worden opgeslagen. SQL API-clients kunnen de header If-None-Match opgeven voor lees bewerkingen voor een resource-achtige document of container en vervolgens de lokale kopieën bijwerken nadat de server versie is gewijzigd.

### <a name="is-a-local-instance-of-sql-api-available"></a>Is er een lokaal exemplaar van SQL API beschikbaar?

Ja. De [Azure Cosmos DB-emulator](local-emulator.md) biedt een zeer betrouw bare emulatie van de Cosmos DB-service. Het ondersteunt functionaliteit die identiek is aan Azure Cosmos DB, waaronder ondersteuning voor het maken en opvragen van JSON-documenten, het inrichten en schalen van verzamelingen en het uitvoeren van opgeslagen procedures en triggers. U kunt toepassingen ontwikkelen en testen met behulp van de Azure Cosmos DB emulator en deze implementeren in azure op een wereld wijde schaal door één configuratie wijziging aan te brengen in het verbindings eindpunt voor Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Waarom worden lange drijvende-komma waarden in een document afgerond wanneer dit wordt weer gegeven vanuit Data Explorer in de portal.

Dit is een beperking van Java script. Java script maakt gebruik van drijvende-komma notatie nummers met dubbele precisie, zoals is opgegeven in IEEE 754. het kan veilig zijn om getallen tussen-(2<sup>53</sup> -1) en 2<sup>53</sup>-1 (bijvoorbeeld 9007199254740991) op te slaan.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Waar zijn de machtigingen toegestaan in de object hiërarchie?

Het maken van machtigingen met behulp van ResourceTokens is toegestaan op container niveau en de onderliggende items (zoals documenten, bijlagen). Dit betekent dat een poging om een machtiging te maken op de data base of een account niveau momenteel niet is toegestaan.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Wat is de Azure Cosmos DB-API voor MongoDB?

De API van het Azure Cosmos DB voor MongoDB is een interprotocol Compatibility Layer waarmee toepassingen eenvoudig en transparant kunnen communiceren met de systeem eigen Azure Cosmos-data base-engine door gebruik te maken van bestaande Sdk's en stuur Programma's die door de community worden ondersteund voor MongoDB. Ontwikkel aars kunnen nu bestaande MongoDB-toolchains en-vaardig heden gebruiken om toepassingen te ontwikkelen die gebruikmaken van Azure Cosmos DB. Ontwikkel aars profiteren van de unieke mogelijkheden van Azure Cosmos DB, waaronder wereld wijde distributie met replicatie met meerdere masters, automatische indexering, onderhoud van back-ups, financieel ondersteunde service level agreements (Sla's), enzovoort.

### <a name="how-do-i-connect-to-my-database"></a>Hoe kan ik verbinding maken met mijn data base?

De snelste manier om verbinding te maken met een Cosmos-data base met de API van Azure Cosmos DB voor MongoDB, is naar de [Azure Portal](https://portal.azure.com). Ga naar uw account en klik vervolgens in het navigatie menu links op **Quick Start**. Quick start is de beste manier om code fragmenten op te halen om verbinding te maken met uw data base.

Azure Cosmos DB dwingt strikte beveiligings vereisten en-standaarden af. Voor Azure Cosmos DB accounts is verificatie en beveiligde communicatie via TLS vereist. Zorg er dus voor dat u TLSv 1.2 gebruikt.

Zie [verbinding maken met uw Cosmos-data base met de API van Azure Cosmos DB voor MongoDb](connect-mongodb-account.md)voor meer informatie.

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Zijn er aanvullende fout codes waarmee ik moet omgaan tijdens het gebruik van de API van Azure Cosmos DB voor MongoDB?

Naast de algemene MongoDB-fout codes heeft de API van de Azure Cosmos DB voor MongoDB een eigen specifieke fout code:

| Fout               | Code  | Beschrijving  | Oplossing  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Het totale aantal verbruikte aanvraag eenheden is hoger dan de ingerichte aanvraag-eenheids snelheid voor de container en is beperkt. | U kunt de door Voer die is toegewezen aan een container of een set containers van de Azure Portal schalen of het opnieuw proberen. |
| ExceededMemoryLimit | 16501 | Als multi tenant service heeft de bewerking de geheugen toewijzing van de client overschreden. | Verklein het bereik van de bewerking via meer beperkende query criteria of neem contact op met de ondersteuning van de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Voor beeld <em> &nbsp; &nbsp; &nbsp;: &nbsp;db. getCollection (' gebruikers '). aggregate ([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {naam: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {leeftijd:-1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Is het Simba-stuur programma voor MongoDB ondersteund voor gebruik met de API van Azure Cosmos DB voor MongoDB?

Ja, u kunt het Simba Mongo ODBC-stuur programma gebruiken met de API van Azure Cosmos DB voor MongoDB

## <a name="table-api"></a><a id="table"></a>Tabel-API

### <a name="how-can-i-use-the-table-api-offering"></a>Hoe kan ik de Table-API-aanbieding gebruiken?

De Azure Cosmos DB Table-API is beschikbaar in de [Azure Portal][azure-portal]. U moet zich eerst aanmelden voor een Azure-abonnement. Nadat u zich hebt geregistreerd, kunt u een Azure Cosmos DB Table-API account toevoegen aan uw Azure-abonnement en vervolgens tabellen toevoegen aan uw account.

U kunt de ondersteunde talen vinden en snel aan de slag met de [Inleiding tot Azure Cosmos DB Table-API](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Heb ik een nieuwe SDK nodig om de Table-API te gebruiken?

Nee, bestaande Sdk's voor opslag moeten nog steeds werken. Het wordt echter aanbevolen dat één de nieuwste Sdk's voor de beste ondersteuning krijgt en in veel gevallen superieure prestaties. Zie de lijst met beschik bare talen in de [Inleiding tot Azure Cosmos DB Table-API](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Waar is Table-API niet identiek aan het opslaggedrag van Azure Table?

Er zijn enkele gedrags verschillen die gebruikers die afkomstig zijn van Azure Table-opslag die tabellen willen maken met de Azure Cosmos DB Table-API op de hoogte moeten zijn:

* Azure Cosmos DB Table-API een gereserveerd capaciteits model gebruikt om gegarandeerde prestaties te garanderen, maar dit betekent dat één betaalt voor de capaciteit zodra de tabel is gemaakt, zelfs als de capaciteit niet wordt gebruikt. Met Azure-tabel opslag betaalt alleen voor capaciteit die wordt gebruikt. Zo kunt u uitleggen waarom Table-API een SLA van 10 MS en 15 MS-schrijf bewerkingen op het 99e-percentiel bieden terwijl Azure Table Storage een SLA met 10 seconden biedt. Maar als gevolg hiervan, met Table-API tabellen, zelfs lege tabellen zonder aanvragen, worden kosten in rekening gebracht om ervoor te zorgen dat de capaciteit beschikbaar is voor het afhandelen van aanvragen op de SLA die door Azure Cosmos DB wordt aangeboden.
* De query resultaten die door de Table-API zijn geretourneerd, worden niet in de volg orde van de partitie sleutel/rij-sleutel gesorteerd zoals ze zich in azure Table Storage bevinden.
* De rij-sleutels mogen Maxi maal 255 bytes zijn
* Batches kunnen Maxi maal 2 MB hebben
* CORS wordt momenteel niet ondersteund
* Tabel namen in azure Table Storage zijn niet hoofdletter gevoelig, maar bevinden zich in Azure Cosmos DB Table-API
* Sommige van de interne indelingen van Azure Cosmos DB voor het coderen van gegevens, zoals binaire velden, zijn momenteel niet zo efficiënt als een van de voor beelden. Daarom kan dit leiden tot onverwachte beperkingen voor de grootte van gegevens. Momenteel kan op dit moment niet de volledige ene MB van een tabel entiteit worden gebruikt voor het opslaan van binaire gegevens, omdat de grootte van de gegevens wordt verhoogd met de code ring.
* De naam van de entiteits eigenschap ' ID ' wordt momenteel niet ondersteund
* TableQuery TakeCount is niet beperkt tot 1000

De REST API bestaan uit een aantal eind punten/query opties die niet worden ondersteund door Azure Cosmos DB Table-API:

| Rest methode (n) | Rest-eind punt/query-optie | Doc-Url's | Uitleg |
| ------------| ------------- | ---------- | ----------- |
| OPHALEN, PLAATSEN | /? restype =service@comp= eigenschappen| [Eigenschappen van de tabel service instellen](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) en de eigenschappen van de [tabel service ophalen](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Dit eind punt wordt gebruikt om CORS-regels, configuratie van opslag analyse en logboek registratie-instellingen in te stellen. CORS wordt momenteel niet ondersteund en analyse en logboek registratie is anders in Azure Cosmos DB dan Azure Storage tabellen |
| Opties | /\<tabel-naam> | [Aanvraag voor CORS-tabel vóór de vlucht](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Dit is een onderdeel van CORS dat Azure Cosmos DB momenteel niet wordt ondersteund. |
| GET | /? restype =service@comp= stats | [Statistieken voor tabel service ophalen](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Geeft informatie over hoe snel gegevens worden gerepliceerd tussen primaire en secundaire zones. Dit is niet nodig in Cosmos DB omdat de replicatie deel uitmaakt van schrijf bewerkingen. |
| OPHALEN, PLAATSEN | /mytable? comp = ACL | [Tabel](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) -ACL en [set Table-ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) ophalen | Hiermee wordt het opgeslagen toegangs beleid opgehaald en ingesteld dat wordt gebruikt voor het beheren van Shared Access signatures (SAS). Hoewel SAS wordt ondersteund, worden ze op een andere manier ingesteld en beheerd. |

Daarnaast biedt Azure Cosmos DB Table-API alleen ondersteuning voor de JSON-indeling, niet ATOM.

Hoewel Azure Cosmos DB ondersteuning biedt voor Shared Access signatures (SAS), wordt bepaalde beleids regels niet ondersteund, met name voor beheer bewerkingen, zoals het recht om nieuwe tabellen te maken.

Voor de .NET SDK zijn er in het bijzonder een aantal klassen en methoden die Azure Cosmos DB momenteel niet wordt ondersteund.

| Klasse | Niet-ondersteunde methode |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | Recht |
|            | GetPermissions* |
| TableServiceContext | * (deze klasse is afgeschaft) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hoe kan ik feedback geven over de SDK of bugs?

U kunt uw feedback op de volgende manieren delen:

* [Mening van gebruikers](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow is het meest geschikt voor het Program meren van vragen. Zorg ervoor dat u de vraag hebt [over het onderwerp](https://stackoverflow.com/help/on-topic) en [Geef zo veel mogelijk details op, zodat u de vraag duidelijk en beantwoord kunt krijgen](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Wat is de connection string die ik moet gebruiken om verbinding te maken met de Table-API?

De connection string is:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

U kunt de connection string ophalen via de pagina verbindings reeks in de Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hoe kan ik de configuratie-instellingen voor de aanvraag opties in de .NET SDK voor de Table-API overschreven?

Sommige instellingen worden verwerkt op de CreateCloudTableClient-methode en andere via de app. config in de sectie appSettings in de client toepassing. Zie [Azure Cosmos DB mogelijkheden](tutorial-develop-table-dotnet.md)voor meer informatie over configuratie-instellingen.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Zijn er wijzigingen voor klanten die de bestaande Azure Table Storage-Sdk's gebruiken?

Geen. Er zijn geen wijzigingen voor bestaande of nieuwe klanten die gebruikmaken van de bestaande Sdk's voor Azure Table Storage.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hoe kan ik tabel gegevens weer geven die zijn opgeslagen in Azure Cosmos DB voor gebruik met de Table-API?

U kunt de Azure Portal gebruiken om door de gegevens te bladeren. U kunt ook de Table-API code of de hulpprogram ma's die worden vermeld in het volgende antwoord gebruiken.

### <a name="which-tools-work-with-the-table-api"></a>Welke hulpprogram ma's werken met de Table-API?

U kunt de [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)gebruiken.

Hulpprogram ma's met de flexibiliteit om een connection string te maken in de indeling die eerder is opgegeven, kunnen de nieuwe Table-API ondersteunen. Op de pagina [Azure Storage client hulpprogramma's](../storage/common/storage-explorers.md) vindt u een lijst met hulp middelen voor tabellen.

### <a name="is-the-concurrency-on-operations-controlled"></a>Wordt de gelijktijdigheid van bewerkingen geregeld?

Ja, optimistische gelijktijdigheid wordt verzorgd via het gebruik van het ETag-mechanisme.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Wordt het OData-query model ondersteund voor entiteiten?

Ja, de Table-API ondersteunt OData-query's en LINQ-query's.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kan ik verbinding maken met Azure Table Storage en Azure Cosmos DB Table-API naast elkaar in dezelfde toepassing?

Ja, u kunt verbinding maken door twee afzonderlijke instanties van de CloudTableClient te maken, die elk verwijzen naar een eigen URI via de connection string.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hoe kan ik een bestaande Azure Table Storage-toepassing migreren naar deze aanbieding?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) en het [hulp programma voor gegevens migratie van Azure Cosmos DB](import-data.md) worden beide ondersteund.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hoe uitbrei ding van de opslag ruimte die voor deze service wordt uitgevoerd als bijvoorbeeld ik begin met *n* GB aan gegevens en zullen mijn gegevens gedurende een bepaalde periode tot 1 TB groeien?

Azure Cosmos DB is ontworpen om onbeperkte opslag te bieden via het gebruik van Horizon taal schalen. De service kan uw opslag bewaken en effectief verg Roten.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hoe kan ik de Table-API aanbieding controleren?

U kunt het deel venster Table-API **metrieken** gebruiken om aanvragen en opslag gebruik te bewaken.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hoe kan ik de door Voer vereist berekenen?

U kunt de Estimator capaciteit gebruiken om de TableThroughput te berekenen die vereist is voor de bewerkingen. Zie voor meer informatie [schatten van aanvraag eenheden en gegevens opslag](https://www.documentdb.com/capacityplanner). Over het algemeen kunt u uw entiteit weer geven als JSON en de cijfers voor uw bewerkingen opgeven.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kan ik de Table-API SDK lokaal gebruiken met de emulator?

Momenteel niet.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Kan mijn bestaande toepassing samen werken met de Table-API?

Ja, dezelfde API wordt ondersteund.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Moet ik mijn bestaande Azure Table Storage-toepassingen migreren naar de SDK als ik de Table-API-functies niet wil gebruiken?

Nee, u kunt bestaande Azure Table Storage-assets zonder onderbreking van welke aard maken en gebruiken. Als u de Table-API echter niet gebruikt, kunt u niet profiteren van de automatische index, de extra consistentie optie of wereld wijde distributie.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hoe kan ik replicatie van de gegevens in de Table-API in meer dan één regio van Azure toevoegen?

U kunt de [globale replicatie-instellingen](tutorial-global-distribution-sql-api.md#portal) van de Azure Cosmos DB-Portal gebruiken om regio's toe te voegen die geschikt zijn voor uw toepassing. Als u een wereld wijd gedistribueerde toepassing wilt ontwikkelen, moet u ook uw toepassing toevoegen met de PreferredLocation-gegevens die zijn ingesteld op de lokale regio voor een lage lees latentie.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hoe kan ik Wijzig de primaire schrijf regio voor het account in de Table-API?

U kunt het deel venster globale replicatie Portal Azure Cosmos DB gebruiken om een regio toe te voegen en vervolgens een failover naar de vereiste regio uit te geven. Zie [ontwikkelen met Azure Cosmos DB accounts met meerdere regio's](high-availability.md)voor instructies.

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hoe kan ik mijn favoriete Lees regio's configureren voor een lage latentie wanneer ik mijn gegevens Distribueer?

Als u hulp nodig hebt bij het lezen van de lokale locatie, gebruikt u de sleutel PreferredLocation in het bestand app. config. Voor bestaande toepassingen genereert de Table-API een fout als LocationMode is ingesteld. Verwijder die code, omdat de Table-API deze gegevens uit het bestand app. config ophaalt. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hoe moet ik nadenken over de consistentie niveaus in de Table-API?

Azure Cosmos DB biedt goed gegronde afwegingen tussen consistentie, Beschik baarheid en latentie. Azure Cosmos DB biedt vijf consistentie niveaus voor het Table-API van ontwikkel aars, zodat u het juiste consistentie model kunt kiezen op tabel niveau en afzonderlijke aanvragen moet maken tijdens het uitvoeren van een query op de gegevens. Wanneer een client verbinding maakt, kan er een consistentie niveau worden opgegeven. U kunt het niveau wijzigen via het argument consistencyLevel van CreateCloudTableClient.

De Table-API biedt Lees bewerkingen met een lage latentie en ' Lees uw eigen schrijf bewerkingen ' met consistentie van afhankelijkheid als de standaard instelling. Zie [consistentie niveaus](consistency-levels.md)voor meer informatie.

Azure-tabel opslag biedt standaard sterke consistentie binnen een regio en uiteindelijk consistentie op de secundaire locaties.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Biedt Azure Cosmos DB Table-API meer consistentie niveaus dan Azure-tabel opslag?

Ja, Zie [consistentie niveaus](consistency-levels.md)voor meer informatie over de gedistribueerde aard van Azure Cosmos db. Omdat er garanties worden geboden voor de consistentie niveaus, kunt u ze met vertrouwen gebruiken.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Hoe lang duurt het om de gegevens te repliceren wanneer globale distributie is ingeschakeld?

Azure Cosmos DB voert de gegevens blijvend in de lokale regio door en duwt de gegevens onmiddellijk naar andere regio's in een paar milliseconden. Deze replicatie is alleen afhankelijk van de round-trip-tijd (RTT) van het Data Center. Zie [Azure Cosmos DB: een wereld wijd gedistribueerde database service op Azure](distribute-data-globally.md)voor meer informatie over de mogelijkheden van globale distributie van Azure Cosmos db.

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan het consistentie niveau van de Lees aanvraag worden gewijzigd?

Met Azure Cosmos DB kunt u het consistentie niveau instellen op het niveau van de container (in de tabel). Met behulp van de .NET SDK kunt u het niveau wijzigen door de waarde voor de sleutel TableConsistencyLevel in het bestand app. config op te geven. De mogelijke waarden zijn: Strong, gebonden veroudering, sessie, consistent voor voegsel en uiteindelijk. Zie [instel bare data Consistency levels in azure Cosmos DB](consistency-levels.md)voor meer informatie. Het is belang rijk dat u het niveau van de aanvraag consistentie op meer dan de instelling voor de tabel niet kunt instellen. U kunt bijvoorbeeld het consistentie niveau voor de tabel op het gewenste niveau niet instellen en de consistentie van de aanvraag is sterk.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hoe verwerkt de Table-API failover als een regio uitvalt?

De Table-API maakt gebruik van het wereld wijd gedistribueerde platform van Azure Cosmos DB. Om ervoor te zorgen dat uw toepassing downtime van data centers kan verdragen, moet u ten minste één regio voor het account inschakelen in de Azure Cosmos DB Portal [met meerdere regio's Azure Cosmos DB accounts](high-availability.md). U kunt de prioriteit van de regio instellen met behulp van de portal [ontwikkeling met Azure Cosmos DB accounts voor meerdere regio's](high-availability.md).

U kunt zoveel regio's toevoegen als u wilt voor het account en het beheer waar de failover kan worden uitgevoerd door een failover-prioriteit op te geven. Als u de Data Base wilt gebruiken, moet u ook een toepassing opgeven. Als u dit doet, zijn uw klanten geen last van uitval. De [nieuwste versie](table-sdk-dotnet.md) van de .net-client-SDK is automatisch multihoming, maar de andere sdk's zijn niet. Dat wil zeggen dat de regio die niet beschikbaar is, wordt gedetecteerd en automatisch een failover naar de nieuwe regio kan worden uitgevoerd.

### <a name="is-the-table-api-enabled-for-backups"></a>Is de Table-API ingeschakeld voor back-ups?

Ja, de Table-API maakt gebruik van het platform van Azure Cosmos DB voor back-ups. Back-ups worden automatisch gemaakt. Zie [online back-ups en herstellen met Azure Cosmos DB](online-backup-and-restore.md)voor meer informatie.

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Indexeert de Table-API standaard alle kenmerken van een entiteit?

Ja, alle kenmerken van een entiteit worden standaard geïndexeerd. Zie [Azure Cosmos DB: Indexing policies](index-policy.md)(Engelstalig) voor meer informatie.

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Betekent dit dat ik niet meer dan één index hoeft te maken om aan de query's te voldoen?

Ja, Azure Cosmos DB Table-API biedt automatische indexering van alle kenmerken zonder schema definitie. Met deze automatisering kunnen ontwikkel aars zich concentreren op de toepassing in plaats van op het maken en beheren van indexen. Zie [Azure Cosmos DB: Indexing policies](index-policy.md)(Engelstalig) voor meer informatie.

### <a name="can-i-change-the-indexing-policy"></a>Kan ik het indexerings beleid wijzigen?

Ja, u kunt het indexerings beleid wijzigen door de index definitie op te geven. U moet de instellingen op de juiste wijze coderen en escapepen.

Voor de non-.NET Sdk's kan het indexerings beleid alleen worden ingesteld in de portal op **Data Explorer**, navigeer naar de specifieke tabel die u wilt wijzigen en ga vervolgens naar de **instellingen voor het schalen &**->indexerings beleid, breng de gewenste wijziging aan en **Sla**het bestand op.

De .NET-SDK kan worden verzonden in het bestand app. config:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB als platform lijkt veel mogelijkheden te hebben, zoals sorteren, aggregaties, hiërarchie en andere functionaliteit. Gaat u deze mogelijkheden toevoegen aan de Table-API?

De Table-API biedt dezelfde query functionaliteit als Azure-tabel opslag. Azure Cosmos DB biedt ook ondersteuning voor sorteren, statistische functies, georuimtelijke query, hiërarchie en een groot aantal ingebouwde functies. Zie [SQL-query's](how-to-sql-query.md)voor meer informatie.

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Wanneer moet ik TableThroughput wijzigen voor de Table-API?

U moet TableThroughput wijzigen wanneer een van de volgende voor waarden van toepassing is:

* U voert een uitpakken, transformeren en laden (ETL) van gegevens uit of u wilt een grote hoeveelheid gegevens in korte tijd uploaden.
* U hebt meer door Voer van de container of van een set containers aan de back-end nodig. U ziet bijvoorbeeld dat de gebruikte door Voer meer is dan de ingerichte door Voer en u wordt beperkt. Zie [instellen van door Voer voor Azure Cosmos-containers](set-throughput.md)voor meer informatie.

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kan ik de door Voer van mijn Table-API tabel omhoog of omlaag schalen?

Ja, u kunt het deel venster schaal van de Azure Cosmos DB Portal gebruiken om de door voer te schalen. Zie voor meer informatie [instellen door Voer](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Is er een standaard TableThroughput ingesteld voor nieuwe ingerichte tabellen?

Ja, als u de TableThroughput via app. config niet overschrijft en geen vooraf gemaakte container gebruikt in Azure Cosmos DB, wordt door de service een tabel gemaakt met de door Voer van 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Zijn er prijs wijzigingen voor bestaande klanten van de service Azure Table Storage?

Geen. Er is geen wijziging in de prijs voor bestaande klanten met Azure Table Storage.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hoe wordt de prijs berekend voor de Table-API?

De prijs is afhankelijk van de toegewezen TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hoe kan ik de frequentie limiet voor de tabellen in Table-API aanbieding afhandelen?

Als de aanvraag snelheid meer is dan de capaciteit van de ingerichte door Voer voor de onderliggende container of een set containers, krijgt u een fout melding en probeert de SDK de oproep opnieuw uit te voeren door het beleid voor opnieuw proberen toe te passen.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Waarom moet ik een door Voer onderscheiden van PartitionKey en RowKey om te kunnen profiteren van de Table-API aanbieding van Azure Cosmos DB?

Azure Cosmos DB stelt een standaard doorvoer voor uw container in als u deze niet in het app. config-bestand of via de portal opgeeft.

Azure Cosmos DB biedt garanties voor prestaties en latentie, met een bovengrens voor de bewerking. Deze garantie is mogelijk wanneer de engine governance kan afdwingen voor de bewerkingen van de Tenant. Door TableThroughput in te stellen, zorgt u ervoor dat u de gegarandeerde door Voer en latentie krijgt, omdat deze capaciteit door het platform wordt gereserveerd en de operationele succes wordt gegarandeerd.

Door gebruik te maken van de doorvoer specificatie, kunt u deze elastisch wijzigen om te profiteren van de seizoensgebondenheid van uw toepassing, aan de vereisten voor door voer te voldoen en kosten te besparen.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure-tabel opslag is voor mij goedkope, omdat ik alleen betaal om de gegevens op te slaan en ik zelden een query uitvoert. De Azure Cosmos DB Table-API aanbieding lijkt te worden opgeladen, zelfs als ik geen enkele trans actie heb uitgevoerd of niets heeft opgeslagen. Kunt u uitleggen?

Azure Cosmos DB is ontworpen als een wereld wijd gedistribueerd systeem op basis van een SLA met garanties voor Beschik baarheid, latentie en door voer. Wanneer u de door Voer in Azure Cosmos DB reserveert, is dit gegarandeerd, in tegens telling tot de door Voer van andere systemen. Azure Cosmos DB biedt aanvullende mogelijkheden die klanten hebben aangevraagd, zoals secundaire indices en wereld wijde distributie.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Ik krijg nooit een melding over een volledig quotum (waarmee wordt aangegeven dat een partitie vol is) wanneer ik gegevens opneemt in azure-tabel opslag. Ik ontvang dit bericht met de Table-API. Is deze aanbieding beperkt en kan ik mijn bestaande toepassing wijzigen?

Azure Cosmos DB is een SLA-systeem dat onbeperkte schaal biedt, met garanties voor latentie, door Voer, Beschik baarheid en consistentie. Zorg ervoor dat uw gegevens grootte en-index beheersbaar en schaalbaar zijn om gegarandeerde Premium-prestaties te garanderen. De limiet van 10 GB voor het aantal entiteiten of items per partitie sleutel is om ervoor te zorgen dat we geweldige Zoek-en query prestaties bieden. Om ervoor te zorgen dat uw toepassing goed wordt geschaald, zelfs voor Azure Storage, wordt u aangeraden *geen* Hot-partitie te maken door alle gegevens in één partitie op te slaan en er query's op uit te voeren.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>PartitionKey en RowKey zijn nog steeds vereist voor de Table-API?

Ja. Omdat de surface area van de Table-API vergelijkbaar is met die van de Azure Table Storage SDK, biedt de partitie sleutel een efficiënte manier om de gegevens te distribueren. De rij is uniek binnen die partitie. De rij moet aanwezig zijn en mag niet null zijn, zoals in de standaard-SDK. De lengte van RowKey is 255 bytes en de lengte van PartitionKey is 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Wat zijn de fout berichten voor de Table-API?

Azure-tabel opslag en Azure Cosmos DB Table-API dezelfde Sdk's gebruiken, zodat de meeste fouten hetzelfde zijn.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Waarom worden er beperkingen toegepast wanneer ik een groot aantal tabellen in de Table-API probeer te maken?

Azure Cosmos DB is een SLA-systeem dat latentie, door Voer, Beschik baarheid en consistentie garanties biedt. Omdat het een ingericht systeem is, reserveert het bronnen om deze vereisten te garanderen. De snelle snelheid voor het maken van tabellen wordt gedetecteerd en beperkt. We raden u aan het aantal tabellen te maken en te verlagen tot minder dan 5 per minuut. Houd er rekening mee dat de Table-API een ingericht systeem is. Op het moment dat u het inricht, moet u hiervoor betalen.

## <a name="gremlin-api"></a>Gremlin-API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Voor C#/.net-ontwikkeling.-ontwikkeling moet ik het pakket micro soft. Azure. graphs of Gremlin.NET gebruiken?

Azure Cosmos DB Gremlin API maakt gebruik van de open source-Stuur Programma's als de belangrijkste connectors voor de service. De aanbevolen optie is daarom het gebruik [van Stuur Programma's die worden ondersteund door Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hoe worden RU/s in rekening gebracht bij het uitvoeren van query's op een grafiek database?

Alle grafiek objecten, hoek punten en randen worden weer gegeven als JSON-documenten in de back-end. Omdat een Gremlin-query een of meer grafiek objecten tegelijk kan wijzigen, zijn de kosten die eraan zijn gekoppeld, direct gerelateerd aan de objecten, de randen die door de query worden verwerkt. Dit is hetzelfde proces dat Azure Cosmos DB voor alle andere Api's gebruikt. Zie [Aanvraageenheden in Azure Cosmos DB](request-units.md) voor meer informatie.

De RU-kosten zijn gebaseerd op de werk gegevensset van het Trans Port en niet de resultatenset. Als een query bijvoorbeeld gericht is op het verkrijgen van één hoek punt, maar er meer dan één ander object op de manier moet worden door lopen, worden de kosten gebaseerd op alle grafiek objecten die nodig zijn voor het berekenen van het ene resultaat hoekpunt.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Wat is de maximale schaal die een grafiek database kan hebben in Azure Cosmos DB Gremlin-API?

Azure Cosmos DB maakt gebruik van [horizontale partitionering](partition-data.md) om de toename van opslag-en doorvoer vereisten automatisch te verhelpen. De maximale door Voer en opslag capaciteit van een werk belasting wordt bepaald door het aantal partities dat is gekoppeld aan een bepaalde container. Een Gremlin API-container heeft echter een specifieke set richt lijnen om een goede prestatie ervaring op schaal te garanderen. Zie [partitioneren in azure Cosmos DB](partition-data.md) artikel voor meer informatie over partitionering en aanbevolen procedures.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hoe kan ik beveiligen tegen ininjectie aanvallen met behulp van Gremlin-Stuur Programma's?

De meeste systeem eigen Apache Tinkerpop Gremlin-Stuur Programma's bieden de mogelijkheid om een woorden lijst met para meters op te geven voor het uitvoeren van query's. Dit is een voor beeld van hoe u dit doet in [Gremlin.net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) en in [Gremlin-java script](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Waarom krijg ik de fout ' Gremlin-query compilatie fout: kan geen van de volgende methoden vinden '?

Azure Cosmos DB Gremlin API implementeert een subset van de functionaliteit die is gedefinieerd in de Gremlin-surface area. Zie [Gremlin-ondersteunings](gremlin-support.md) artikel voor ondersteunde stappen en meer informatie.

De beste oplossing is om de vereiste Gremlin-stappen opnieuw te schrijven met de ondersteunde functionaliteit, omdat alle essentiële Gremlin-stappen door Azure Cosmos DB worden ondersteund.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Waarom krijg ik de fout ' WebSocketException: de server heeft de status code ' 200 ' geretourneerd wanneer de status code ' 101 ' werd verwacht '?

Deze fout wordt waarschijnlijk veroorzaakt wanneer het verkeerde eind punt wordt gebruikt. Het eind punt dat deze fout genereert, heeft het volgende patroon:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Dit is het documenten eindpunt voor uw Graph-data base.  Het juiste eind punt dat moet worden gebruikt, is het Gremlin-eind punt dat de volgende indeling heeft:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Waarom krijg ik de fout ' RequestRateIsTooLarge '?

Deze fout betekent dat de toegewezen aanvraag eenheden per seconde niet voldoende zijn voor het leveren van de query. Deze fout wordt meestal weer gegeven wanneer u een query uitvoert waarmee alle hoek punten worden opgehaald:

```
// Query example:
g.V()
```

Met deze query wordt geprobeerd alle hoek punten op te halen uit de grafiek. Dit betekent dat de kosten van deze query gelijk zijn aan ten minste het aantal hoek punten in de voor waarden van RUs. De RU/s-instelling moet worden aangepast om deze query te verhelpen.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Waarom worden de verbindingen van mijn Gremlin-stuur programma uiteindelijk verwijderd?

Een Gremlin-verbinding wordt gemaakt via een WebSocket-verbinding. Hoewel WebSocket-verbindingen geen specifieke time to Live hebben, zal Azure Cosmos DB Gremlin-API na 30 minuten inactiviteit niet-actieve verbindingen beëindigen.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Waarom kan ik geen fluent API-aanroepen gebruiken in de systeem eigen Gremlin-Stuur Programma's?

Fluent API-aanroepen worden nog niet ondersteund door de Azure Cosmos DB Gremlin API. Voor Fluent-API-aanroepen is een interne indelings functie vereist die ondersteuning biedt voor byte code die momenteel niet wordt ondersteund door Azure Cosmos DB Gremlin-API. Vanwege dezelfde reden wordt het meest recente Gremlin-java script-stuur programma momenteel ook niet ondersteund.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Hoe kan ik de efficiëntie van mijn Gremlin-query's evalueren?

De **executionProfile ()** preview-stap kan worden gebruikt om een analyse van het uitvoerings plan voor query's te bieden. Deze stap moet aan het einde van een Gremlin-query worden toegevoegd, zoals wordt geïllustreerd door het volgende voor beeld:

**Query voorbeeld**

```
g.V('mary').out('knows').executionProfile()
```

**Voorbeeld uitvoer**

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

In de uitvoer van het bovenstaande profiel ziet u hoeveel tijd wordt besteed aan het verkrijgen van de object Vertex objecten, de rand objecten en de grootte van de werk gegevensset. Dit heeft betrekking op de standaardkosten metingen voor Azure Cosmos DB query's.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
