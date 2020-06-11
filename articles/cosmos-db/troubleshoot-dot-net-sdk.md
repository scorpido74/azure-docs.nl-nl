---
title: Problemen vaststellen en oplossen bij het gebruik van Azure Cosmos DB .NET SDK
description: Gebruik functies als logboek registratie aan client zijde en andere hulpprogram ma's van derden voor het identificeren, diagnosticeren en Azure Cosmos DB oplossen van problemen met het gebruik van .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/06/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c0f40b3c79c16046ef61e89cad72c714346d2674
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84672626"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Problemen vaststellen en oplossen bij het gebruik van Azure Cosmos DB .NET SDK

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java-SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Dit artikel heeft betrekking op veelvoorkomende problemen, tijdelijke oplossingen, diagnostische stappen en hulpprogram ma's wanneer u de [.NET SDK](sql-api-sdk-dotnet.md) gebruikt met Azure Cosmos DB SQL API-accounts.
De .NET-SDK biedt logische weer gave aan de client zijde om toegang te krijgen tot de Azure Cosmos DB SQL-API. In dit artikel worden tools en benaderingen beschreven om u te helpen bij eventuele problemen.

## <a name="checklist-for-troubleshooting-issues"></a>Controle lijst voor het oplossen van problemen
Bekijk de volgende controle lijst voordat u uw toepassing naar productie gaat verplaatsen. Met de controle lijst voor komt u dat er verschillende veelvoorkomende problemen worden weer geven. U kunt ook snel vaststellen wanneer er een probleem optreedt:

*    De nieuwste [SDK](sql-api-sdk-dotnet-standard.md)gebruiken. Preview-Sdk's mogen niet worden gebruikt voor productie. Dit voor komt dat bekende problemen die al zijn opgelost, worden gerepareerd.
*    Bekijk de [Tips voor prestaties](performance-tips.md)en volg de aanbevolen procedures. Dit helpt voor komen dat schalen, latentie en andere prestatie problemen worden opgelost.
*    Schakel de SDK-logboek registratie in om u te helpen bij het oplossen van een probleem. Het inschakelen van de logboek registratie kan van invloed zijn op de prestaties, zodat het het beste kan worden ingeschakeld wanneer u problemen oplost. U kunt de volgende logboeken inschakelen:
*    [Metrische gegevens vastleggen](monitor-accounts.md) met behulp van de Azure Portal. Met metrische gegevens van de portal wordt de Azure Cosmos DB telemetrie weer gegeven. Dit is handig om te bepalen of het probleem overeenkomt met Azure Cosmos DB of van de client zijde.
*    Registreer de [Diagnostische teken reeks](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) in de v2 SDK of [diagnoses](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) in de V3 SDK van de punt bewerkings reacties.
*    De [metrische gegevens](sql-api-query-metrics.md) van de SQL-query registreren bij alle antwoorden op query's 
*    Volg de installatie van de [SDK-logboek registratie]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Bekijk de sectie [veelvoorkomende problemen en tijdelijke oplossingen](#common-issues-workarounds) in dit artikel.

Raadpleeg de [sectie met github-problemen](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) die actief worden bewaakt. Controleer of er een soortgelijk probleem met een tijdelijke oplossing al is ingediend. Als u geen oplossing hebt gevonden, kunt u een GitHub-probleem oplossen. U kunt een ondersteunings streepje openen voor urgente problemen.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Veelvoorkomende problemen en tijdelijke oplossingen

### <a name="general-suggestions"></a>Algemene suggesties
* Voer, indien mogelijk, uw app uit in dezelfde Azure-regio als uw Azure Cosmos DB-account. 
* U kunt problemen met de connectiviteit/Beschik baarheid ondervinden als gevolg van een gebrek aan resources op de client computer. U wordt aangeraden uw CPU-gebruik te bewaken op knoop punten waarop de Azure Cosmos DB-client wordt uitgevoerd, en omhoog/omlaag te schalen als deze bij hoge belasting worden uitgevoerd.

### <a name="check-the-portal-metrics"></a>Controleer de metrische gegevens van de portal
Door de [metrische gegevens](monitor-accounts.md) van de portal te controleren, kunt u bepalen of het een probleem aan de client zijde is of dat er een probleem is met de service. Als de metrische gegevens bijvoorbeeld een hoog aantal aanvragen met een rente beperking bevatten (HTTP-status code 429), wat betekent dat de aanvraag wordt beperkt, controleert u de sectie [aanvraag snelheid is te groot] . 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Time-outs van aanvragen
RequestTimeout treedt doorgaans op wanneer u direct/TCP gebruikt, maar kan zich in de gateway modus voordoen. Deze fouten zijn de meest voorkomende bekende oorzaken en suggesties voor het oplossen van het probleem.

* CPU-gebruik is hoog, waardoor latentie en/of time-outs voor aanvragen worden veroorzaakt. De klant kan de hostcomputer zo schalen dat er meer resources worden toegewezen, of de belasting kan worden gedistribueerd op meer computers.
* Beschik baarheid van socket/poort is mogelijk laag. Bij het uitvoeren in azure kunnen clients die gebruikmaken van de .NET SDK, de poort uitputting voor Azure SNAT (PAT) aanraken. Gebruik de meest recente versie 2. x of 3. x van de .NET-SDK om te voor komen dat dit probleem optreedt. Dit is een voor beeld van waarom het wordt aanbevolen om altijd de nieuwste SDK-versie uit te voeren.
* Het maken van meerdere DocumentClient-instanties kan leiden tot verbindings conflicten en time-outproblemen. Volg de [Tips voor prestaties](performance-tips.md)en gebruik één DocumentClient-exemplaar in een heel proces.
* Gebruikers zien soms verhoogde latentie of time-outs van aanvragen omdat hun verzamelingen ontoereikend zijn ingericht, de back-end vertragings aanvragen en de client worden intern opnieuw geprobeerd. Controleer de [metrische gegevens](monitor-accounts.md)van de portal.
* Azure Cosmos DB distribueert de totale ingerichte door Voer gelijkmatig over fysieke partities. Controleer de metrische gegevens van de portal om te zien of de werk belasting een Hot- [partitie sleutel](partition-data.md)tegen komt. Dit zorgt ervoor dat de geaggregeerde door Voer (RU/s) onder het ingerichte RUs lijkt, maar een door Voer (RU/s) van één partitie overschrijdt de ingerichte door voer. 
* Daarnaast voegt de 2,0 SDK kanaal semantiek toe aan directe/TCP-verbindingen. Er wordt één TCP-verbinding gebruikt voor meerdere aanvragen tegelijk. Dit kan leiden tot twee problemen onder specifieke gevallen:
    * Een hoge mate van gelijktijdigheid kan leiden tot conflicten op het kanaal.
    * Grote aanvragen of antwoorden kunnen leiden tot hoofd-of-line blok kering van het kanaal en de exacerbate-inhoud, zelfs met een relatief lage mate van gelijktijdigheid.
    * Als de aanvraag in een van deze twee categorieën valt (of als er sprake is van een hoog CPU-gebruik), zijn dit mogelijke oplossingen:
        * Probeer de toepassing omhoog te schalen.
        * Daarnaast kunnen SDK-logboeken worden vastgelegd via de [traceer-listener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) om meer details te krijgen.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Hoge netwerk latentie
Hoge netwerk latentie kan worden geïdentificeerd met behulp van de [Diagnostische teken reeks](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) in de v2 SDK of [diagnoses](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) in de V3 SDK.

Als er geen [time-outs](#request-timeouts) aanwezig zijn en de diagnostische gegevens afzonderlijke aanvragen weer geven waarbij de hoge latentie duidelijk is voor het verschil tussen `ResponseTime` en `RequestStartTime` , bijvoorbeeld (>300 milliseconden in dit voor beeld):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Deze latentie kan meerdere oorzaken hebben:

* Uw toepassing wordt niet uitgevoerd in dezelfde regio als uw Azure Cosmos DB-account.
* De configuratie van de [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) of [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) is onjuist en er wordt geprobeerd verbinding te maken met een andere regio waar uw toepassing momenteel wordt uitgevoerd.
* Er is mogelijk een knel punt op de netwerk interface vanwege veel verkeer. Als de toepassing wordt uitgevoerd op Azure Virtual Machines, zijn er mogelijke oplossingen:
    * Overweeg het gebruik [van een virtuele machine met versneld netwerken ingeschakeld](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * [Versneld netwerken inschakelen op een bestaande virtuele machine](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Overweeg het gebruik van een [hogere virtuele machine](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Uitputting van de poort van Azure SNAT (PAT)

Als uw app is geïmplementeerd op [Azure virtual machines zonder een openbaar IP-adres](../load-balancer/load-balancer-outbound-connections.md#defaultsnat), worden de standaard [Azure SNAT-poorten](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) gebruikt om verbinding te maken met een wille keurig eind punt buiten uw VM. Het aantal verbindingen dat van de virtuele machine naar het Azure Cosmos DB-eind punt is toegestaan, wordt beperkt door de [Azure SNAT-configuratie](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Deze situatie kan leiden tot verbindings beperking, sluiting verbinding of de hierboven vermelde [time-outs van aanvragen](#request-timeouts).

 Azure SNAT-poorten worden alleen gebruikt als uw virtuele machine een privé-IP-adres heeft om verbinding te maken met een openbaar IP-adres. Er zijn twee manieren om de limiet voor Azure SNAT te voor komen (als u al een exemplaar van één client gebruikt voor de hele toepassing):

* Voeg uw Azure Cosmos DB Service-eind punt toe aan het subnet van uw virtuele Azure Virtual Machines-netwerk. Zie [Azure Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie. 

    Wanneer het service-eind punt is ingeschakeld, worden de aanvragen niet langer verzonden vanuit een openbaar IP-adres naar Azure Cosmos DB. In plaats daarvan worden de identiteit van het virtuele netwerk en het subnet verzonden. Deze wijziging kan ertoe leiden dat de firewall wordt neergezet als alleen open bare IP-adressen zijn toegestaan. Als u een firewall gebruikt en u het service-eind punt inschakelt, voegt u een subnet toe aan de firewall door gebruik te maken van [Virtual Network acl's](../virtual-network/virtual-networks-acl.md).
* Wijs een [openbaar IP-adres toe aan uw Azure-VM](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="http-proxy"></a>HTTP-proxy
Als u een HTTP-proxy gebruikt, moet u ervoor zorgen dat het het aantal verbindingen dat in de SDK is geconfigureerd kan ondersteunen `ConnectionPolicy` .
Anders worden er verbindings problemen beschreven.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Aanvraag frequentie te groot
' De aanvraag snelheid is te groot ' of de fout code 429 geeft aan dat uw aanvragen worden beperkt, omdat de verbruikte door Voer (RU/s) de [ingerichte door](set-throughput.md)Voer heeft overschreden. De SDK voert automatisch aanvragen opnieuw uit op basis van het opgegeven [beleid voor opnieuw proberen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Als deze fout vaak optreedt, kunt u overwegen de door Voer van de verzameling te verhogen. Controleer de [metrische gegevens van de portal](use-metrics.md) om te zien of er 429 fouten optreden. Controleer uw [partitie sleutel](partitioning-overview.md#choose-partitionkey) om ervoor te zorgen dat deze een gelijkmatige verdeling van de opslag en het aanvraag volume levert. 

### <a name="slow-query-performance"></a>Trage query prestaties
Met de [metrische gegevens](sql-api-query-metrics.md) van de query kunt u bepalen waar de query de meeste tijd in bebesteding neemt. Vanuit de metrische gegevens van de query kunt u zien hoeveel er wordt uitgegeven aan de back-end van de client.
* Als de back-end-query snel wordt geretourneerd en een grote tijd op de client wordt gespendeerd, controleert u de belasting van de computer. Het is waarschijnlijk dat er onvoldoende bronnen zijn en de SDK wacht totdat de resources beschikbaar zijn voor het verwerken van het antwoord.
* Als de back-end-query langzaam is, [optimaliseert u de query](optimize-cost-queries.md) en bekijkt u het huidige [indexerings beleid](index-overview.md) 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401: de MAC-hand tekening die in de HTTP-aanvraag is gevonden, is niet hetzelfde als de berekende hand tekening
Als u het volgende fout bericht van 401 hebt ontvangen: de MAC-hand tekening in de HTTP-aanvraag is niet hetzelfde als de berekende hand tekening. Dit kan worden veroorzaakt door de volgende scenario's.

1. De sleutel is gedraaid en voldoet niet aan de [Aanbevolen procedures](secure-access-to-data.md#key-rotation). Dit is meestal het geval. Het kan een paar seconden duren voordat de draaiing van de account sleutel van Cosmos DB kan worden uitgevoerd, afhankelijk van de grootte van het Cosmos DB-account.
   1. 401 MAC-hand tekening wordt kort na het draaien van een sleutel weer gegeven en stopt uiteindelijk zonder wijzigingen. 
2. De sleutel is onjuist geconfigureerd op de toepassing, zodat de sleutel niet overeenkomt met het account.
   1. 401 MAC-handtekening probleem is consistent en gebeurt voor alle aanroepen
3. Er is een race voorwaarde bij het maken van een container. Een instantie van de toepassing probeert toegang te krijgen tot de container voordat het maken van de container is voltooid. Het meest voorkomende scenario als de toepassing wordt uitgevoerd, en de container wordt verwijderd en opnieuw gemaakt met dezelfde naam terwijl de toepassing wordt uitgevoerd. De SDK probeert de nieuwe container te gebruiken, maar het maken van de container wordt nog uitgevoerd, zodat deze de sleutels niet bevat.
   1. 401 MAC-handtekening probleem wordt kort weer gegeven na het maken van een container en pas nadat het maken van de container is voltooid.
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>HTTP-fout 400. De grootte van de aanvraag headers is te lang.
 De grootte van de koptekst is groter geworden en overschrijdt de Maxi maal toegestane grootte. Het wordt altijd aanbevolen de nieuwste SDK te gebruiken. Zorg ervoor dat u ten minste versie [3. x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) of [2. x](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)gebruikt, waardoor het traceren van de header grootte wordt toegevoegd aan het uitzonderings bericht.

Mogelijke
 1. Het sessie token is te groot geworden. Het sessie token groeit naarmate het aantal partities toeneemt in de container.
 2. Het vervolg token is groter geworden. Verschillende query's hebben verschillende voortzettings tokens.
 3. Dit wordt veroorzaakt door een combi natie van het sessie token en vervolg token.

Oplossing:
   1. Volg de [Tips voor prestaties](performance-tips.md) en converteer de toepassing naar de modus direct en TCP-verbinding. Direct + TCP heeft geen beperking voor de header grootte, zoals HTTP, waardoor dit probleem wordt voor komen.
   2. Als het sessie token de oorzaak is, dan is een tijdelijke oplossing om de toepassing opnieuw te starten. Wanneer het toepassings exemplaar opnieuw wordt gestart, wordt het sessie token opnieuw ingesteld. Als de uitzonde ringen worden gestopt nadat de computer opnieuw is opgestart, wordt de oorzaak van het sessie token bevestigd. Uiteindelijk wordt de grootte van de uitzonde ring verg root.
   3. Als de toepassing niet kan worden geconverteerd naar direct en TCP en het sessie token de oorzaak is, kan de oplossing worden uitgevoerd door het [consistentie niveau](consistency-levels.md)van de client te wijzigen. Het sessie token wordt alleen gebruikt voor de consistentie van de sessie, wat de standaard waarde is voor Cosmos DB. Elk ander consistentie niveau maakt geen gebruik van het sessie token. 
   4. Als de toepassing niet kan worden geconverteerd naar direct en TCP en het vervolg token de oorzaak is, probeert u de optie ResponseContinuationTokenLimitInKb in te stellen. De optie is te vinden in de FeedOptions voor v2 of QueryRequestOptions in v3.

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Aanvraag frequentie te groot]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
