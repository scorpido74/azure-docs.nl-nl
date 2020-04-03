---
title: Azure Cosmos DB .NET change feed Processor API, SDK release notes
description: Lees alles over de Change Feed Processor API en SDK, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de .NET Change Feed Processor SDK.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 5820778d46f5701b82bb289192350a9e13739d37
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619446"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor SDK: Notities downloaden en vrijgeven

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-wijzigingsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulkexecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK downloaden**|[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-documentatie**|[Api-naslagdocumentatie voor feedprocessorbibliotheek wijzigen](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Aan de slag**|[Aan de slag met de Change Feed Processor .NET SDK](change-feed.md)|
|**Huidig ondersteund kader**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Als u de change feed processor gebruikt, raadpleegt u de nieuwste versie 3.x van de [.NET SDK](change-feed-processor.md), die de feed in de SDK heeft ingebouwd. 

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="v2-builds"></a>v2 bouwt

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Een nieuwe `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` methode en `ICheckpointPartitionProcessorFactory`bijbehorende openbare interface toegevoegd. Dit maakt een `IPartitionProcessor` implementatie van de interface mogelijk om ingebouwde controlepuntenmechanisme te gebruiken. De nieuwe fabriek is `IPartitionProcessorFactory`vergelijkbaar met `Create` de bestaande `ILeaseCheckpointer` , behalve dat de methode neemt ook de parameter.
* Slechts één van de twee `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`methoden, of , `ChangeFeedProcessorBuilder` kan worden gebruikt voor dezelfde instantie.

### <a name="228"></a><a name="2.2.8"/>2.2.8
* Verbeteringen in stabiliteit en diagnose:
  * Ondersteuning toegevoegd om te detecteren dat het lang duurt voordat de voerinvoer van leeswijzigingen in beslag neemt. Wanneer het langer duurt dan `ChangeFeedProcessorOptions.ChangeFeedTimeout` de waarde die door de eigenschap is opgegeven, worden de volgende stappen uitgevoerd:
    * De bewerking om de wijzigingsfeed op de problematische partitie te lezen, wordt afgebroken.
    * De instantie change feed processor laat het eigendom van de problematische lease vallen. De ingevallen lease zal worden opgehaald tijdens de volgende lease-stap die zal worden gedaan door dezelfde of verschillende wijziging feed processor instantie. Op deze manier begint het lezen van de feed opnieuw.
    * Er wordt een probleem gemeld aan de gezondheidsmonitor. De standaardheidsmonitor stuurt alle gerapporteerde problemen om logboek te traceren.
  * Een nieuw openbaar `ChangeFeedProcessorOptions.ChangeFeedTimeout`eigendom toegevoegd: . De standaardwaarde van deze eigenschap is 10 minuten.
  * Toegevoegd een nieuwe publieke `Monitoring.MonitoredOperation.ReadChangeFeed`enum waarde: . Wanneer de `HealthMonitoringRecord.Operation` waarde van `Monitoring.MonitoredOperation.ReadChangeFeed`is ingesteld op , geeft dit aan dat het gezondheidsprobleem gerelateerd is aan het lezen van de feed.

### <a name="227"></a><a name="2.2.7"/>2.2.7
* Verbeterde load balancing-strategie voor scenario's bij het verkrijgen van alle leases duurt langer dan het leaseverloopinterval, bijvoorbeeld als gevolg van netwerkproblemen:
  * In dit scenario load balancing algoritme gebruikt om ten onrechte leases beschouwen als verlopen, waardoor het stelen van leases van actieve eigenaren. Dit kan leiden tot onnodige re-balancing veel leases.
  * Dit probleem is opgelost in deze release door het vermijden van opnieuw proberen op conflict tijdens het verwerven van verlopen lease die eigenaar niet is veranderd en posponing het verwerven van verlopen lease naar de volgende load balancing iteratie.

### <a name="226"></a><a name="2.2.6"/>2.2.6
* Verbeterde behandeling van observer uitzonderingen.
* Rijkere informatie over observer fouten:
  * Wanneer een waarnemer is gesloten vanwege een uitzondering die is gegenereerd door ProcessChangesAsync van Observer, ontvangt de CloseAsync nu de redenparameter die is ingesteld op ChangeFeedObserverCloseReason.ObserverError.
  * Traces toegevoegd om fouten in de gebruikerscode in een waarnemer te identificeren.

### <a name="225"></a><a name="2.2.5"/>2.2.5
* Added support for handling split in collections that use shared database throughput.
  * Met deze release wordt een probleem opgelost dat kan optreden tijdens splitsing in verzamelingen met behulp van gedeelde databasedoorvoer wanneer het resultaat wordt gesplitst in een herbalancering van partities met slechts één onderliggend e-mapsleutelbereik dat is gemaakt, in plaats van twee. Wanneer dit gebeurt, kan Change Feed Processor vast komen te zitten bij het verwijderen van de lease voor oude partitiesleutelbereik en niet het maken van nieuwe leases. Het probleem is opgelost in deze release.

### <a name="224"></a><a name="2.2.4"/>2.2.4
* Nieuwe eigenschap ChangeFeedProcessorOptions.StartToegevoegd om de beginwijzigingsfeed te ondersteunen vanaf het vervolgtoken voor aanvragen. Dit wordt alleen gebruikt wanneer de lease-collectie leeg is of een lease-lease niet over ContinuationToken-set beschikt. Voor leases in leaseverzameling en vervolgtoken set wordt de ContinuationToken gebruikt en ChangeFeedProcessorOptions.StartContinuation wordt genegeerd.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Ondersteuning toegevoegd voor het gebruik van aangepaste winkel om vervolgtokens per partitie voort te zetten.
  * Een aangepaste leasewinkel kan bijvoorbeeld Azure Cosmos DB-leaseverzameling zijn die op elke aangepaste manier is verdeeld.
  * Custom lease stores kunnen gebruik maken van nieuwe extensibility point ChangeFeedProcessorBuilder.WithLeaseStoreManager (ILeaseStoreManager) en ILeaseStoreManager public interface.
  * Refactored de ILeaseManager interface in meerdere role interfaces.
* Kleine wijziging: verwijderd extensibility point ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), gebruik in plaats daarvan ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager).

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Deze release lost een probleem op dat optreedt tijdens het verwerken van een gesplitste verzameling en het gebruik van een partitieleaseverzameling. Bij het verwerken van een lease voor gesplitste partitie kan de lease die overeenkomt met die partitie niet worden verwijderd. Het probleem is opgelost in deze release.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Fixed Estimator calculation for Multi Master accounts and new Session Token format.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Ondersteuning toegevoegd voor partitieleaseverzamelingen. De partitiesleutel moet worden gedefinieerd als /id.
* Kleine wijziging: de methoden van de iChangeFeedDocumentClient-interface en de klasse ChangeFeedDocumentClient zijn gewijzigd in de parameters RequestOptions en CancellationToken. IChangeFeedDocumentClient is een geavanceerd extensibiliteitspunt waarmee u de aangepaste implementatie van de documentclient bieden om te gebruiken met Change Feed Processor, bijvoorbeeld DocumentClient versieren en alle oproepen naar het te onderscheppen om extra tracering, foutafhandeling, enz. Met deze update moet de code die IChangeFeedDocumentClient implementeert, worden gewijzigd om nieuwe parameters in de implementatie op te nemen.
* Kleine diagnostische verbeteringen.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Nieuwe API&lt;toegevoegd, Taak&lt;IReadOnlyList RemainingPartitionWork&gt; &gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Dit kan worden gebruikt om geschat werk voor elke partitie te krijgen.
* Ondersteunt Microsoft.Azure.DocumentDB SDK 2.0. Vereist Microsoft.Azure.DocumentDB 2.0 of hoger.

### <a name="206"></a><a name="2.0.6"/>2.0.6
* Added ChangeFeedEventHost.HostName public property for compatibility with v1.

### <a name="205"></a><a name="2.0.5"/>2.0.5
* Fixed a race condition that occurs during partition split. De race voorwaarde kan leiden tot het verwerven van lease en onmiddellijk verliezen tijdens partitie split en het veroorzaken van twist. Het probleem met de raceconditie is opgelost met deze release.

### <a name="204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* De volgende problemen zijn verholpen:
  * Wanneer partitiesplitsing plaatsvindt, kan er dubbele verwerking van documenten worden gewijzigd voordat de splitsing wordt gesplitst.
  * De GetEstimatedRemainingWork API heeft 0 geretourneerd toen er geen leases aanwezig waren in de leaseverzameling.

* De volgende uitzonderingen worden openbaar gemaakt. Extensies die IPartitionProcessor implementeren, kunnen deze uitzonderingen weggooien.
  * Microsoft.azure.documents.changefeedprocessor.exceptions.leaselostexception. 
  * Microsoft.azure.documents.changeFeedprocessor.exceptions.partitionexception. 
  * Microsoft.azure.documents.changeFeedprocessor.exceptions.partitionnotfoundexception.
  * Microsoft.azure.documents.changeFeedprocessor.exceptions.partitionsplitexception. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Kleine API-wijzigingen:
  * ChangeFeedProcessorOptions.IsAutoCheckpointEnabled verwijderd die als verouderd is gemarkeerd.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Stabiliteitsverbeteringen:
  * Betere afhandeling van de initialisatie van de leasewinkel. Wanneer de leasewinkel leeg is, kan slechts één exemplaar van de processor deze initialiseren, de anderen zullen wachten.
  * Stabieler/efficiënterleaseverlenging/release. Het vernieuwen en vrijgeven van een lease partitie is onafhankelijk van het vernieuwen van anderen. In v1 werd dat achtereenvolgens gedaan voor alle partities.
* Nieuwe v2-API:
  * Bouwerpatroon voor flexibele constructie van de processor: de klasse ChangeFeedProcessorBuilder.
    * Kan elke combinatie van parameters.
    * Kan bijvoorbeeld DocumentClient gebruiken voor het bewaken en/of leaseen (niet beschikbaar in v1).
  * IChangeFeedObserver.ProcessChangesAsync neemt nu CancellationToken.
  * IRemainingWorkEstimator - de resterende werkschatter kan apart van de processor worden gebruikt.
  * Nieuwe uitbreidbaarheidspunten:
    * IPartitionLoadBalancingStrategy - voor het op maat maken van partities tussen instanties van de processor.
    * ILease, ILeaseManager - voor custom lease management.
    * IPartitionProcessor - voor aangepaste verwerkingwijzigingen op een partitie.
* Logging - maakt gebruik van [LibLog](https://github.com/damianh/LibLog) bibliotheek.
* 100% achterwaarts compatibel met v1 API.
* Nieuwe codebasis.
* Compatibel met [SQL .NET SDK-versies](sql-api-sdk-dotnet.md) 1.21.1 en hoger.

### <a name="v1-builds"></a>v1 bouwt

### <a name="133"></a><a name="1.3.3"/>1.3.3
* Meer logging toegevoegd.
* Fixed a DocumentClient leak when calling the pending work estimation multiple times.

### <a name="132"></a><a name="1.3.2"/>1.3.2
* Oplossingen in de in behandeling zijnde schatting van het werk.

### <a name="131"></a><a name="1.3.1"/>1.3.1
* Stabiliteitsverbeteringen.
  * Oplossing voor het afhandelen van het probleem met geannuleerde taken dat kan leiden tot gestopte waarnemers op sommige partities.
* Ondersteuning voor handmatige controlepunten.
* Compatibel met [SQL .NET SDK-versies](sql-api-sdk-dotnet.md) 1.21 en hoger.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Hiermee voegt u ondersteuning toe voor .NET Standard 2.0. Het pakket `netstandard2.0` ondersteunt `net451` en kader monikers.
* Compatibel met [SQL .NET SDK-versies](sql-api-sdk-dotnet.md) 1.17.0 en hoger.
* Compatibel met [SQL .NET Core SDK-versies](sql-api-sdk-dotnet-core.md) 1.5.1 en hoger.

### <a name="111"></a><a name="1.1.1"/>1.1.1
* Hiermee wordt een probleem opgelost met de berekening van de schatting van het resterende werk toen de wijzigingsfeed leeg was of er geen werk in behandeling was.
* Compatibel met [SQL .NET SDK-versies](sql-api-sdk-dotnet.md) 1.13.2 en hoger.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Een methode toegevoegd om een schatting te krijgen van het resterende werk dat moet worden verwerkt in de wijzigingsfeed.
* Compatibel met [SQL .NET SDK-versies](sql-api-sdk-dotnet.md) 1.13.2 en hoger.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Compatibel met [SQL .NET SDK-versies](sql-api-sdk-dotnet.md) 1.14.1 en lager.

## <a name="release--retirement-dates"></a>Release & Pensioendata

Microsoft zal ten minste **12 maanden** van tevoren een SDK melden om de overgang naar een nieuwere/ondersteunde versie soepel te laten verlopen.

Nieuwe functies en functionaliteit en optimalisaties worden alleen toegevoegd aan de huidige SDK, als zodanig wordt aanbevolen dat u altijd zo vroeg mogelijk een upgrade uitvoert naar de nieuwste SDK-versie. 

Elk verzoek aan Cosmos DB met behulp van een gepensioneerde SDK wordt door de service afgewezen.

<br/>

| Versie | Releasedatum | Pensioendatum |
| --- | --- | --- |
| [2.3.0](#2.3.0) |2 april 2020 |--- |
| [2.2.8](#2.2.8) |28 oktober 2019 |--- |
| [2.2.7](#2.2.7) |14 mei 2019 |--- |
| [2.2.6](#2.2.6) |29 januari 2019 |--- |
| [2.2.5](#2.2.5) |13 december 2018 |--- |
| [2.2.4](#2.2.4) |29 november 2018 |--- |
| [2.2.3](#2.2.3) |19 november 2018 |--- |
| [2.2.2](#2.2.2) |31 oktober 2018 |--- |
| [2.2.1](#2.2.1) |24 oktober 2018 |--- |
| [1.3.3](#1.3.3) |08 mei 2018 |--- |
| [1.3.2](#1.3.2) |18 april 2018 |--- |
| [1.3.1](#1.3.1) |13 maart 2018 |--- |
| [1.2.0](#1.2.0) |31 oktober 2017 |--- |
| [1.1.1](#1.1.1) |29 augustus 2017 |--- |
| [1.1.0](#1.1.0) |13 augustus 2017 |--- |
| [1.0.0](#1.0.0) |07 juli 2017 |--- |

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook

Zie Microsoft Azure Cosmos [DB-servicepagina](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.
