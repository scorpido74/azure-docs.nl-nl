---
title: Problemen vaststellen en oplossen bij het gebruik van Azure Cosmos DB .NET SDK
description: Gebruik functies zoals logboekregistratie aan de clientzijde en andere hulpprogramma's van derden om Azure Cosmos DB-problemen te identificeren, diagnosticeren en oplossen bij het gebruik van .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137951"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Problemen vaststellen en oplossen bij het gebruik van Azure Cosmos DB .NET SDK
In dit artikel worden veelvoorkomende problemen, tijdelijke oplossingen, diagnostische stappen en hulpprogramma's behandeld wanneer u de [.NET SDK](sql-api-sdk-dotnet.md) met Azure Cosmos DB SQL API-accounts gebruikt.
De .NET SDK biedt een logische weergave aan clientzijde om toegang te krijgen tot de Azure Cosmos DB SQL API. In dit artikel worden tools en benaderingen beschreven om u te helpen bij eventuele problemen.

## <a name="checklist-for-troubleshooting-issues"></a>Checklist voor het oplossen van problemen:
Houd rekening met de volgende checklist voordat u uw aanvraag in productie neemt. Als u de checklist gebruikt, worden verschillende veelvoorkomende problemen voorkomen die u mogelijk ziet. U ook snel een diagnose stellen wanneer er een probleem optreedt:

*    Gebruik de nieuwste [SDK.](sql-api-sdk-dotnet-standard.md) Preview-SDK's mogen niet worden gebruikt voor productie. Dit voorkomt het raken van bekende problemen die al zijn opgelost.
*    Bekijk de [prestatietips](performance-tips.md)en volg de voorgestelde praktijken. Dit voorkomt schaalvergroting, latentie en andere prestatieproblemen.
*    Schakel de SDK-logboekregistratie in om u te helpen een probleem op te lossen. Het inschakelen van de logboekregistratie kan van invloed zijn op de prestaties, dus het is het beste om deze alleen in te schakelen wanneer problemen worden opgelost. U de volgende logboeken inschakelen:
    *    [Log statistieken](monitor-accounts.md) met behulp van de Azure-portal. Portalmetrics tonen de Azure Cosmos DB-telemetrie, wat handig is om te bepalen of het probleem overeenkomt met Azure Cosmos DB of dat het van de clientkant komt.
    *    Log de [diagnostische tekenreeks](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) in de V2 SDK of [diagnostische gegevens](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) in V3 SDK van de puntbewerkingsreacties.
    *    De [SQL-querystatistieken van](sql-api-query-metrics.md) alle queryreacties registreren 
    *    De installatie voor [SDK-logboekregistratie volgen]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Neem een kijkje op de [algemene problemen en oplossingen](#common-issues-workarounds) sectie in dit artikel.

Controleer de [sectie GitHub-problemen](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) die actief wordt gecontroleerd. Controleer of een soortgelijk probleem met een tijdelijke oplossing al is ingediend. Als u geen oplossing hebt gevonden, dient u een GitHub-probleem in. U een ondersteuningsvink openen voor dringende problemen.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Veelvoorkomende problemen en tijdelijke oplossingen

### <a name="general-suggestions"></a>Algemene suggesties
* Voer uw app waar mogelijk uit in dezelfde Azure-regio als uw Azure Cosmos DB-account. 
* U problemen ondervinden met de connectiviteit/beschikbaarheid als gevolg van een gebrek aan resources op uw clientmachine. We raden u aan uw CPU-gebruik te controleren op knooppunten waarop de Azure Cosmos DB-client wordt uitgevoerd en op/uit te schalen als deze bij hoge belasting worden uitgevoerd.

### <a name="check-the-portal-metrics"></a>De portalstatistieken controleren
Als u de [portalstatistieken](monitor-accounts.md) controleert, u bepalen of het een probleem aan de clientzijde is of dat er een probleem is met de service. Als de statistieken bijvoorbeeld een hoog percentage frequentiebeperkte aanvragen bevatten(HTTP-statuscode 429), wat betekent dat de aanvraag wordt beperkt, controleer dan de [sectie Aanvraagpercentage te groot.] 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Time-outs aanvragen
RequestTimeout gebeurt meestal bij het gebruik van Direct/TCP, maar kan in de gatewaymodus. Deze fouten zijn de veelvoorkomende bekende oorzaken en suggesties over het oplossen van het probleem.

* Het CPU-gebruik is hoog, wat een latentie en/of time-outs voor aanvragen zal veroorzaken. De klant kan de hostmachine opschalen om deze meer resources te geven, of de belasting kan over meer machines worden verdeeld.
* De beschikbaarheid van sockets / poorten is mogelijk laag. Wanneer clients die de .NET SDK gebruiken, worden uitgevoerd in Azure, kunnen ze de uitputting van de Azure SNAT-poort raken. Gebruik de nieuwste versie 2.x of 3.x van de .NET SDK om de kans op het raken van dit probleem te verkleinen. Dit is een voorbeeld van waarom het wordt aanbevolen om altijd de nieuwste SDK-versie uit te voeren.
* Het maken van meerdere DocumentClient-exemplaren kan leiden tot verbindings- en time-outproblemen. Volg de [prestatietips](performance-tips.md)en gebruik één DocumentClient-exemplaar voor een heel proces.
* Gebruikers zien soms verhoogde latentie of vragen time-outs omdat hun verzamelingen onvoldoende zijn ingericht, de back-end-throttles aanvragen en de client opnieuw wordt geprobeerd. Controleer de [portalstatistieken](monitor-accounts.md).
* Azure Cosmos DB verdeelt de totale ingerichte doorvoer gelijkmatig over fysieke partities. Controleer portalstatistieken om te zien of de werkbelasting een hot [partition-sleutel](partition-data.md)tegenkomt. Hierdoor lijkt de geaggregeerde verbruikte doorvoer (RU/s) onder de ingerichte RU's te liggen, maar een enkele partitieverbruikte doorvoer (RU/s) zal de ingerichte doorvoer overschrijden. 
* Daarnaast voegt de 2.0 SDK kanaalsemantiek toe aan direct/TCP-verbindingen. Eén TCP-verbinding wordt gebruikt voor meerdere aanvragen tegelijk. Dit kan leiden tot twee problemen in specifieke gevallen:
    * Een hoge mate van gelijktijdigheid kan leiden tot twist op het kanaal.
    * Grote verzoeken of reacties kunnen leiden tot head-of-line blokkering op het kanaal en verergeren de twist, zelfs met een relatief lage mate van gelijktijdigheid.
    * Als het geval valt in een van deze twee categorieën (of als een hoog CPU-gebruik wordt vermoed), zijn dit mogelijke oplossingen:
        * Probeer de toepassing op te schalen/ uit te schalen.
        * Bovendien kunnen SDK-logboeken worden vastgelegd via [Trace Listener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) om meer details te krijgen.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Hoge netwerklatentie
Hoge netwerklatentie kan worden geïdentificeerd door gebruik te maken van de [diagnostische tekenreeks](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) in de V2 SDK of [diagnostiek](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) in V3 SDK.

Als er geen [time-outs](#request-timeouts) aanwezig zijn en de diagnostiek enkele aanvragen `ResponseTime` `RequestStartTime`weergeeft waarbij de hoge latentie duidelijk is over het verschil tussen en , zoals zo (>300 milliseconden in dit voorbeeld):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Deze latentie kan meerdere oorzaken hebben:

* Uw toepassing wordt niet uitgevoerd in dezelfde regio als uw Azure Cosmos DB-account.
* Uw [voorkeurslocaties](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) of [ApplicationRegion-configuratie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) is onjuist en probeert verbinding te maken met een andere regio waarop uw toepassing momenteel wordt uitgevoerd.
* Er kan een knelpunt zijn op de netwerkinterface vanwege het hoge verkeer. Als de toepassing wordt uitgevoerd op Azure Virtual Machines, zijn er mogelijke tijdelijke oplossingen:
    * Overweeg een virtuele machine te gebruiken [met versnelde netwerken ingeschakeld.](../virtual-network/create-vm-accelerated-networking-powershell.md)
    * [Versnelde netwerken inschakelen op een bestaande virtuele machine.](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)
    * Overweeg het gebruik van een [hogere end Virtual Machine](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) poort uitputting

Als uw app is geïmplementeerd op [Azure Virtual Machines zonder openbaar IP-adres,](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)maken Standaard Azure [SNAT-poorten](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) verbindingen met een eindpunt buiten uw VM. Het aantal verbindingen dat van de VM naar het Azure Cosmos DB-eindpunt is toegestaan, wordt beperkt door de [Azure SNAT-configuratie](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Deze situatie kan leiden tot verbindingsbeperking, verbindingssluiting of de bovengenoemde [time-outs van aanvraag.](#request-timeouts)

 Azure SNAT-poorten worden alleen gebruikt wanneer uw VM een privé-IP-adres heeft dat verbinding maakt met een openbaar IP-adres. Er zijn twee tijdelijke oplossingen om azure SNAT-beperking te voorkomen (op voorwaarde dat u al één clientinstantie in de hele toepassing gebruikt):

* Voeg uw Azure Cosmos DB-serviceeindpunt toe aan het subnet van uw virtuele Azure-machines-netwerk. Zie [Eindpunten voor Azure Virtual Network-service voor](../virtual-network/virtual-network-service-endpoints-overview.md)meer informatie. 

    Wanneer het serviceeindpunt is ingeschakeld, worden de aanvragen niet langer verzonden van een openbaar IP naar Azure Cosmos DB. In plaats daarvan worden het virtuele netwerk en subnetidentiteit verzonden. Deze wijziging kan leiden tot firewall drops als alleen openbare IP's zijn toegestaan. Als u een firewall gebruikt, voegt u bij het inschakelen van het serviceeindpunt een subnet toe aan de firewall met behulp van [Virtuele Netwerk ACL's](../virtual-network/virtual-networks-acl.md).
* Wijs een [openbaar IP toe aan uw Azure VM.](../load-balancer/load-balancer-outbound-connections.md#assignilpip)

### <a name="http-proxy"></a>HTTP-proxy
Als u een HTTP-proxy gebruikt, controleert u of deze `ConnectionPolicy`het aantal verbindingen kan ondersteunen dat is geconfigureerd in de SDK.
Anders heb je te maken met verbindingsproblemen.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Aanvraagtarief te groot
'Verzoeksnelheid te groot' of foutcode 429 geeft aan dat uw aanvragen worden beperkt, omdat de verbruikte doorvoer (RU/s) de [ingerichte doorvoer](set-throughput.md)heeft overschreden. De SDK probeert aanvragen automatisch opnieuw op basis van het opgegeven [beleid voor nieuwe try](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Als u deze fout vaak krijgt, u overwegen de doorvoer op de verzameling te verhogen. Controleer de [statistieken van de portal](use-metrics.md) om te zien of u 429 fouten krijgt. Controleer uw [partitiesleutel](partitioning-overview.md#choose-partitionkey) om ervoor te zorgen dat dit resulteert in een gelijkmatige verdeling van opslag en aanvraagvolume. 

### <a name="slow-query-performance"></a>Trage queryprestaties
De [querystatistieken](sql-api-query-metrics.md) helpen bepalen waar de query het grootste deel van de tijd doorbrengt. Aan de hand van de querystatistieken u zien hoeveel ervan wordt uitgegeven aan de back-end versus de client.
* Als de back-endquery snel terugkeert en een grote tijd aan de client besteedt, controleert u de belasting op de machine. Het is waarschijnlijk dat er niet genoeg resource en de SDK wacht op middelen beschikbaar zijn om de reactie te verwerken.
* Als de back-endquery traag is, u [de query optimaliseren](optimize-cost-queries.md) en naar het huidige [indexeringsbeleid](index-overview.md) kijken 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Aanvraagtarief te groot]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


