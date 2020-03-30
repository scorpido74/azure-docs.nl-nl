---
title: Problemen oplossen bij het gebruik van Azure Functions trigger voor Cosmos DB
description: Veelvoorkomende problemen, tijdelijke oplossingen en diagnostische stappen bij het gebruik van de trigger voor Azure-functies voor Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365505"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Problemen met problemen diagnosticeren en oplossen bij het gebruik van Azure Functions trigger voor Cosmos DB

In dit artikel worden veelvoorkomende problemen, tijdelijke oplossingen en diagnostische stappen gebruikt wanneer u de [trigger voor Azure-functies voor Cosmos DB gebruikt.](change-feed-functions.md)

## <a name="dependencies"></a>Afhankelijkheden

De trigger en bindingen voor Azure Functions voor Cosmos DB zijn afhankelijk van de extensiepakketten via de runtime van Azure Functions. Houd deze pakketten altijd bijgewerkt, omdat ze oplossingen en nieuwe functies kunnen bevatten die eventuele problemen kunnen oplossen:

* Zie [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)voor Azure-functies V2 .
* Zie [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)voor Azure-functies V1 .

In dit artikel wordt altijd verwezen naar Azure Functions V2 wanneer de runtime wordt vermeld, tenzij expliciet is opgegeven.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>De Azure Cosmos DB SDK onafhankelijk van elkaar gebruiken

De belangrijkste functionaliteit van het uitbreidingspakket is om ondersteuning te bieden voor de Trigger en bindingen van Azure-functies voor Cosmos DB. Het bevat ook de [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), wat handig is als u programmatisch wilt communiceren met Azure Cosmos DB zonder de trigger en bindingen te gebruiken.

Als u de Azure Cosmos DB SDK wilt gebruiken, moet u ervoor zorgen dat u geen andere NuGet-pakketverwijzing aan uw project toevoegt. Laat **de SDK-verwijzing in**plaats daarvan oplossen via het uitbreidingspakket van Azure Functions. Gebruik de Azure Cosmos DB SDK los van de trigger en bindingen

Als u bovendien handmatig uw eigen exemplaar van de [Azure Cosmos DB SDK-client](./sql-api-sdk-dotnet-core.md)maakt, moet u het patroon volgen van het hebben van slechts één exemplaar van de client met behulp van [een Singleton-patroonbenadering.](../azure-functions/manage-connections.md#documentclient-code-example-c) Dit proces voorkomt de mogelijke socketproblemen in uw activiteiten.

## <a name="common-scenarios-and-workarounds"></a>Veelvoorkomende scenario's en tijdelijke oplossingen

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure-functie mislukt met foutberichtverzameling bestaat niet

Azure Function mislukt met foutbericht "De bronverzameling 'verzamelnaam' (in database 'databasenaam') of de leaseverzameling 'collection2-name' (in database 'database2-name') bestaat niet. Beide verzamelingen moeten bestaan voordat de luisteraar begint. Als u de leasecollectie automatisch wilt maken, stelt u 'CreateLeaseCollectionIfNotExists' in op 'true'"

Dit betekent dat een van de Azure Cosmos-containers die nodig zijn om de trigger te laten werken, niet bestaat of niet bereikbaar is voor de Azure-functie. **De fout zelf zal u vertellen welke Azure Cosmos database en container is de trigger op zoek naar** op basis van uw configuratie.

1. Controleer `ConnectionStringSetting` het kenmerk en dat het **verwijst naar een instelling die bestaat in uw Azure Function App**. De waarde van dit kenmerk moet niet de verbindingstekenreeks zelf zijn, maar de naam van de configuratie-instelling.
2. Controleer of `databaseName` `collectionName` de en bestaan in uw Azure Cosmos-account. Als u automatische waardevervanging `%settingName%` gebruikt (met behulp van patronen), controleert u of de naam van de instelling bestaat in uw Azure Function App.
3. Als u geen a- `LeaseCollectionName/leaseCollectionName`opgeeft, is de standaardwaarde "leasen". Controleer of een dergelijke container bestaat. Optioneel u `CreateLeaseCollectionIfNotExists` het kenmerk in `true` de Trigger zo instellen dat het automatisch wordt gemaakt.
4. Controleer de firewallconfiguratie van uw [Azure Cosmos-account](how-to-configure-firewall.md) om te zien of de Azure-functie niet wordt geblokkeerd.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure-functie kan niet beginnen met 'Gedeelde doorvoerverzameling moet een partitiesleutel hebben'

De vorige versies van de Azure Cosmos DB Extension ondersteunden geen leasecontainer die is gemaakt in een [gedeelde doorvoerdatabase.](./set-throughput.md#set-throughput-on-a-database) Als u dit probleem wilt oplossen, werkt u de [microsoft.azure.webjobs.extensions.CosmosDB-extensie](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) bij om de nieuwste versie te krijgen.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure Function kan niet beginnen met 'PartitionKey moet worden geleverd voor deze bewerking'.

Deze fout betekent dat u momenteel een partitieleaseverzameling gebruikt met een oude [extensieafhankelijkheid.](#dependencies) Upgrade naar de laatst beschikbare versie. Als u momenteel azure-functies V1 uitvoert, moet u upgraden naar Azure Functions V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure-functie kan niet beginnen met 'De leaseverzameling moet, indien verdeeld, een partitiesleutel hebben die gelijk is aan id.'

Deze fout betekent dat uw huidige leasecontainer is verdeeld, `/id`maar dat het pad met de partitiesleutel niet is . Als u dit probleem wilt oplossen, moet `/id` u de leasecontainer opnieuw maken met als partitiesleutel.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>U ziet een waarde "Waarde kan niet null zijn. Parameternaam: o" in uw Azure Functions-logboeken wanneer u de trigger probeert uit te voeren

Dit probleem wordt weergegeven als u de Azure-portal gebruikt en u probeert de knop **Uitvoeren** op het scherm te selecteren wanneer u een Azure-functie inspecteert die de trigger gebruikt. De trigger vereist niet dat u Uitvoeren wilt selecteren om te starten, deze wordt automatisch gestart wanneer de Azure-functie wordt geïmplementeerd. Als u de logboekstream van de Azure-functie op de Azure-portal wilt controleren, gaat u naar uw bewaakte container en voegt u een aantal nieuwe items in, ziet u automatisch de triggeruitvoeren.

### <a name="my-changes-take-too-long-to-be-received"></a>Mijn wijzigingen duren te lang om te worden ontvangen

Dit scenario kan meerdere oorzaken hebben en ze moeten allemaal worden gecontroleerd:

1. Wordt de Azure-functie geïmplementeerd in dezelfde regio als het Azure Cosmos-account? Voor een optimale netwerklatentie moeten de Azure-functie en het Azure Cosmos-account zich in dezelfde Azure-regio bevinden.
2. Vinden de wijzigingen in uw Azure Cosmos-container continu of sporadisch plaats?
In het laatste geval kan er enige vertraging optreden tussen het moment dat de wijzigingen worden opgeslagen en het moment dat de Azure-functie deze ophaalt. Dit komt doordat wanneer tijdens het intern controleren op wijzigingen in de Azure Cosmos-container geen wijzigingen worden aangetroffen die in behandeling zijn om te worden gelezen, de Azure-container gedurende een configureerbare periode (standaard vijf seconden) in de slaapstand geraakt voordat er op nieuwe wijzigingen wordt gecontroleerd (om een hoog RU-verbruik te voorkomen). U kunt deze periode in de slaapstand configureren via de `FeedPollDelay/feedPollDelay`-instelling in de [configuratie](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) van de trigger (de waarde bedraagt naar verwachting enkele milliseconden).
3. Uw Azure [Cosmos-container](./request-units.md)is mogelijk beperkt in de snelheid.
4. U `PreferredLocations` het kenmerk in uw trigger gebruiken om een door komma's gescheiden lijst met Azure-regio's op te geven om een aangepaste voorkeursverbindingsvolgorde te definiëren.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Sommige wijzigingen worden herhaald in mijn Trigger

Het concept van een "verandering" is een bewerking op een document. De meest voorkomende scenario's waarin gebeurtenissen voor hetzelfde document worden ontvangen, zijn:
* Het account gebruikt uiteindelijke consistentie. Tijdens het consumeren van de wijzigingsfeed in een uiteindelijk consistentieniveau, kunnen er dubbele gebeurtenissen tussen de volgende feedleesbewerkingen voor wijzigingen plaatsvinden (de laatste gebeurtenis van een leesbewerking wordt weergegeven als de eerste van de volgende bewerking).
* Het document wordt bijgewerkt. De feed Wijzigen kan meerdere bewerkingen bevatten voor dezelfde documenten, als dat document updates ontvangt, kan het meerdere gebeurtenissen ophalen (één voor elke update). Een eenvoudige manier om onderscheid te maken tussen `_lsn` verschillende bewerkingen voor hetzelfde document is het bijhouden van de [eigenschap voor elke wijziging.](change-feed.md#change-feed-and-_etag-_lsn-or-_ts) Als ze niet overeenkomen, zijn dit verschillende wijzigingen ten opzichte van hetzelfde document.
* Als u documenten alleen `id`door identificeert, moet u `id` er rekening mee houden dat de unieke `id` id voor een document de en de partitiesleutel is (er kunnen twee documenten zijn met dezelfde, maar verschillende partitiesleutel).

### <a name="some-changes-are-missing-in-my-trigger"></a>Sommige wijzigingen ontbreken in mijn Trigger

Als u merkt dat sommige wijzigingen die zijn doorgevoerd in uw Azure Cosmos-container niet worden opgepikt door de Azure-functie, is er een eerste onderzoeksstap die moet plaatsvinden.

Wanneer uw Azure-functie de wijzigingen ontvangt, verwerkt deze vaak en kan het resultaat optioneel naar een andere bestemming worden verzonden. Wanneer u ontbrekende wijzigingen onderzoekt, controleert u welke **wijzigingen worden ontvangen op het opnamepunt** (wanneer de Azure-functie wordt gestart), niet op de bestemming.

Als er enkele wijzigingen ontbreken op de bestemming, kan dit betekenen dat er een fout optreedt tijdens de uitvoering van de Azure-functie nadat de wijzigingen zijn ontvangen.

In dit scenario is de beste `try/catch` manier van handelen om blokken toe te voegen in uw code en in de lussen die de wijzigingen kunnen verwerken, om een fout voor een bepaalde subset van items op te sporen en deze dienovereenkomstig te behandelen (stuur ze naar een andere opslag voor verdere analyse of opnieuw proberen). 

> [!NOTE]
> Standaard wordt er niet opnieuw een batch met wijzigingen geprobeerd als er een niet-afgehandelde uitzondering tijdens het uitvoeren van de code is opgetreden. Dit betekent dat de reden dat de wijzigingen niet op de bestemming zijn aangekomen, is omdat u ze niet verwerkt.

Als u merkt dat sommige wijzigingen helemaal niet zijn ontvangen door uw trigger, is het meest voorkomende scenario dat er **een andere Azure-functie wordt uitgevoerd.** Het kan een andere Azure-functie zijn die is geïmplementeerd in Azure of een Azure-functie die lokaal wordt uitgevoerd op de machine van een ontwikkelaar die **precies dezelfde configuratie** heeft (dezelfde bewaakte en lease-containers), en deze Azure-functie steelt een subset van de wijzigingen waarvan u zou verwachten dat uw Azure-functie wordt verwerkt.

Bovendien kan het scenario worden gevalideerd als u weet hoeveel Azure Function App-exemplaren u hebt uitgevoerd. Als u uw leasecontainer inspecteert en het aantal leaseartikelen `Owner` telt, moeten de afzonderlijke waarden van de eigenschap in deze container gelijk zijn aan het aantal exemplaren van uw functie-app. Als er meer eigenaren zijn dan de bekende Azure Function App-exemplaren, betekent dit dat deze extra eigenaren degenen zijn die de wijzigingen "stelen".

Een eenvoudige manier om te werken rond `LeaseCollectionPrefix/leaseCollectionPrefix` deze situatie, is het toepassen van een op uw functie met een nieuwe / andere waarde of, als alternatief, testen met een nieuwe leasecontainer.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Noodzaak om alle items in mijn container vanaf het begin opnieuw te starten en opnieuw te verwerken 
Ga als lid van het begin naar een nieuwe verwerking:
1. Stop uw Azure-functie als deze momenteel wordt uitgevoerd. 
1. Verwijder de documenten in de leaseverzameling (of verwijder en maak de leaseverzameling opnieuw zodat deze leeg is)
1. Stel het kenmerk [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger in uw functie in op true. 
1. Start de Azure-functie opnieuw. Het zal nu lezen en verwerken van alle veranderingen vanaf het begin. 

Als u [Startvan begin](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) op true instelt, wordt de Azure-functie weergegeven om wijzigingen te lezen vanaf het begin van de geschiedenis van de verzameling in plaats van de huidige tijd. Dit werkt alleen als er geen reeds gemaakte huurcontracten zijn (dat wil zeggen documenten in de leasecollectie). Het instellen van deze eigenschap op true wanneer er reeds gemaakte huurovereenkomsten zijn gemaakt, heeft geen effect; in dit scenario, wanneer een functie wordt gestopt en opnieuw wordt gestart, begint deze te lezen vanaf het laatste controlepunt, zoals gedefinieerd in de leaseverzameling. Volg de bovenstaande stappen 1-4 om vanaf het begin opnieuw te verwerken.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Binding kan alleen met IReadOnlyList\<Document> of JArray

Deze fout treedt op als uw Azure Functions-project (of een project waarnaar wordt verwezen) een handmatige NuGet-verwijzing bevat naar de Azure Cosmos DB SDK met een andere versie dan die van de [Azure Functions Cosmos DB Extension.](./troubleshoot-changefeed-functions.md#dependencies)

Als u deze situatie wilt oplossen, verwijdert u de handmatige NuGet-verwijzing die is toegevoegd en laat u de Azure Cosmos DB SDK-verwijzing oplossen via het DB-uitbreidingspakket azure functions cosmos.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Het polling-interval van azure-functie wijzigen voor de detectiewijzigingen

Zoals eerder uitgelegd voor [Mijn wijzigingen te lang duren om te worden ontvangen,](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)Azure-functie zal slapen voor een configureerbare hoeveelheid tijd (5 seconden, standaard) voordat u controleert op nieuwe wijzigingen (om een hoog RU-verbruik te voorkomen). U kunt deze periode in de slaapstand configureren via de `FeedPollDelay/feedPollDelay`-instelling in de [configuratie](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) van de trigger (de waarde bedraagt naar verwachting enkele milliseconden).

## <a name="next-steps"></a>Volgende stappen

* [Bewaking inschakelen voor uw Azure-functies](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK-probleemoplossing](./troubleshoot-dot-net-sdk.md)
