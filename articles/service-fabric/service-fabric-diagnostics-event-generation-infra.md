---
title: Bewaking azure-servicefabricplatformniveau
description: Meer informatie over gebeurtenissen en logboeken op platformniveau die worden gebruikt om Azure Service Fabric-clusters te controleren en te diagnosticeren.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 720cc157111293146b796f8567f94a4f1f4830c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376933"
---
# <a name="monitoring-the-cluster"></a>Het cluster bewaken

Het is belangrijk om te controleren op clusterniveau om te bepalen of uw hardware en cluster zich gedragen zoals verwacht. Hoewel Service Fabric toepassingen kan laten draaien tijdens een hardwarefout, moet u nog steeds vaststellen of er een fout optreedt in een toepassing of in de onderliggende infrastructuur. U moet ook uw clusters controleren om de capaciteit beter te plannen en te helpen bij beslissingen over het toevoegen of verwijderen van hardware.

Service Fabric onthult verschillende gestructureerde platformevenementen, zoals [Service Fabric-evenementen,](service-fabric-diagnostics-events.md)via de EventStore en diverse logkanalen out-of-the-box. 

Op Windows zijn Service Fabric-gebeurtenissen beschikbaar bij één ETW-provider met een set relevante `logLevelKeywordFilters` die wordt gebruikt om te kiezen tussen operationele en gegevens & Messaging-kanalen - dit is de manier waarop we uitgaande Service Fabric-gebeurtenissen scheiden waarop we moeten worden gefilterd als dat nodig is.

* **Operationeel** Bewerkingen op hoog niveau uitgevoerd door Service Fabric en het cluster, inclusief gebeurtenissen voor een knooppunt dat eraan komt, een nieuwe toepassing die wordt geïmplementeerd of een upgrade-rollback, enz. Bekijk [hier](service-fabric-diagnostics-event-generation-operational.md)de volledige lijst met evenementen.  

* **Operationeel - gedetailleerd**  
Gezondheidsrapporten en beslissingen over het balanceren van de belasting.

Het operatiekanaal is toegankelijk via verschillende manieren, waaronder ETW/Windows Event Logs, de [EventStore](service-fabric-diagnostics-eventstore.md) (beschikbaar op Windows in versies 6.2 en hoger voor Windows-clusters). De EventStore geeft u toegang tot de gebeurtenissen van uw cluster per entiteit (entiteiten zoals cluster, knooppunten, toepassingen, services, partities, replica's en containers) en stelt deze bloot via REST API's en de Service Fabric-clientbibliotheek. Gebruik de EventStore om uw dev/testclusters te controleren en om een point-in-time inzicht te krijgen in de status van uw productieclusters.

* **Gegevens & Berichten**  
Kritieke logboeken en gebeurtenissen die in de berichten worden gegenereerd (momenteel alleen de ReverseProxy) en het gegevenspad (betrouwbare servicesmodellen).

* **Data & Messaging - gedetailleerd**  
Verbose-kanaal dat alle niet-kritieke logboeken van gegevens en berichten in het cluster bevat (dit kanaal heeft een zeer hoog aantal gebeurtenissen).

Naast deze, zijn er twee gestructureerde EventSource kanalen voorzien, evenals logs die we verzamelen voor ondersteuningsdoeleinden.

* [Reliable Services-gebeurtenissen](service-fabric-reliable-services-diagnostics.md)  
Programmeermodel specifieke gebeurtenissen.

* [Reliable Actors-gebeurtenissen](service-fabric-reliable-actors-diagnostics.md)  
Programmeermodel specifieke gebeurtenissen en prestatiemeteritems.

* Ondersteuningslogboeken  
Systeemlogboeken die door Service Fabric worden gegenereerd, mogen alleen door ons worden gebruikt bij het verlenen van ondersteuning.

Deze verschillende kanalen hebben betrekking op het grootste deel van het platform niveau logging die wordt aanbevolen. Als u logboekregistratie op platformniveau wilt verbeteren, u overwegen te investeren in een beter begrip van het gezondheidsmodel en het toevoegen van aangepaste statusrapporten en het toevoegen van aangepaste **prestatiemeteritems** om een realtime inzicht te krijgen in de impact van uw services en toepassingen op het cluster.

Om van deze logboeken te kunnen profiteren, is het ten zeerste aan te raden om 'Diagnostics' ingeschakeld te laten tijdens het maken van het cluster in de Azure Portal. Door diagnostische gegevens in te schakelen, kan Windows Azure Diagnostics, wanneer het cluster wordt geïmplementeerd, de kanalen Operationele, Betrouwbare Services en Betrouwbare actoren erkennen en de gegevens opslaan zoals verder uitgelegd in [aggregategebeurtenissen met Azure Diagnostics.](service-fabric-diagnostics-event-aggregation-wad.md)

## <a name="azure-service-fabric-health-and-load-reporting"></a>Status- en belastingrapportage van Azure Service Fabric

Service Fabric heeft een eigen gezondheidsmodel, dat in detail wordt beschreven in deze artikelen:

- [Inleiding tot de statuscontrole Service Fabric](service-fabric-health-introduction.md)
- [Servicestatus rapporteren en controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Aangepaste statusrapporten van servicefabric toevoegen](service-fabric-report-health.md)
- [Gezondheidsrapporten van Service Fabric weergeven](service-fabric-view-entities-aggregated-health.md)

Health monitoring is essentieel voor meerdere aspecten van het bedienen van een service, vooral tijdens een applicatie-upgrade. Nadat elk upgradedomein van de service is bijgewerkt, moet het upgradedomein de statuscontroles doorstaan voordat de implementatie naar het volgende upgradedomein wordt verplaatst. Als de status OK niet kan worden bereikt, wordt de implementatie teruggedraaid, zodat de toepassing in een bekende OK-status blijft staan. Hoewel sommige klanten mogelijk worden beïnvloed voordat de services worden teruggedraaid, ondervinden de meeste klanten geen probleem. Ook een resolutie gebeurt relatief snel zonder te hoeven wachten op actie van een menselijke operator. Hoe meer statuscontroles in uw code worden opgenomen, hoe veerkrachtiger uw service is voor implementatieproblemen.

Een ander aspect van de servicestatus is het rapporteren van statistieken van de service. Statistieken zijn belangrijk in Service Fabric omdat ze worden gebruikt om het gebruik van resources in evenwicht te brengen. Statistieken kunnen ook een indicator zijn van de gezondheid van het systeem. U hebt bijvoorbeeld een toepassing met veel services en elke instantie rapporteert een statistiek (requests per second) (RPS). Als één service meer resources gebruikt dan een andere service, verplaatst Service Fabric service-exemplaren rond het cluster om te proberen zelfs het resourcegebruik te behouden. Zie [Resourceverbruik beheren en laden in Service Fabric beheren met statistieken](service-fabric-cluster-resource-manager-metrics.md)voor een meer gedetailleerde uitleg over hoe het gebruik van resources werkt.

Statistieken kunnen u ook inzicht geven in hoe uw service presteert. Na verloop van tijd u statistieken gebruiken om te controleren of de service binnen de verwachte parameters werkt. Als trends bijvoorbeeld aantonen dat de gemiddelde RPS op maandagochtend om 9 uur 1000 is, u een gezondheidsrapport instellen dat u waarschuwt als de RPS lager is dan 500 of hoger dan 1.500. Alles is misschien prima, maar het is misschien de moeite waard om er zeker van te zijn dat uw klanten een geweldige ervaring hebben. Uw service kan een set statistieken definiëren die kunnen worden gerapporteerd voor statuscontroledoeleinden, maar die geen invloed hebben op de resourcebalancing van het cluster. Stel hiervoor het metrische gewicht in op nul. We raden u aan alle statistieken te starten met een gewicht van nul en het gewicht niet te verhogen totdat u zeker weet dat u begrijpt hoe het wegen van de statistieken van invloed is op het balanceren van resources voor uw cluster.

> [!TIP]
> Gebruik niet te veel gewogen statistieken. Het kan moeilijk zijn om te begrijpen waarom service-exemplaren worden verplaatst voor het balanceren. Een paar metrics kunnen een lange weg te gaan!

Alle informatie die de status en prestaties van uw sollicitatie kan aangeven, is een kandidaat voor statistieken en gezondheidsrapporten. **Een CPU-prestatiemeter kan u vertellen hoe uw knooppunt wordt gebruikt, maar het vertelt u niet of een bepaalde service in orde is, omdat meerdere services mogelijk op één knooppunt worden uitgevoerd.** Statistieken zoals RPS, verwerkte items en de latentie van aanvragen kunnen echter allemaal de status van een specifieke service aangeven.

## <a name="service-fabric-support-logs"></a>Service Fabric-ondersteuningslogboeken

Als u contact moet opnemen met Microsoft-ondersteuning voor hulp bij uw Azure Service Fabric-cluster, zijn ondersteuningslogboeken bijna altijd vereist. Als uw cluster wordt gehost in Azure, worden ondersteuningslogboeken automatisch geconfigureerd en verzameld als onderdeel van het maken van een cluster. De logboeken worden opgeslagen in een speciaal opslagaccount in de brongroep van uw cluster. Het opslagaccount heeft geen vaste naam, maar in het account ziet u blobcontainers en -tabellen met namen die met *stof*beginnen. Zie [Een zelfstandig Azure Service Fabric-cluster en](service-fabric-cluster-creation-for-windows-server.md) [configuratie-instellingen voor een zelfstandig Windows-cluster](service-fabric-cluster-manifest.md)maken en beheren voor informatie over het instellen van logboekverzamelingen voor een zelfstandig cluster. Voor zelfstandige Service Fabric-instanties moeten de logboeken naar een lokaal bestandsshare worden verzonden. U **bent verplicht** om deze logboeken te hebben voor ondersteuning, maar ze zijn niet bedoeld om bruikbaar te zijn voor iemand buiten het Microsoft-klantenserviceteam.

## <a name="measuring-performance"></a>Prestaties meten

Als u de prestaties van uw cluster meet, u begrijpen hoe het in staat is om belasting- en schijfbeslissingen te verwerken rond het schalen van uw cluster (zie meer informatie over het schalen van een cluster [op Azure](service-fabric-cluster-scale-up-down.md)of [on-premises).](service-fabric-cluster-windows-server-add-remove-nodes.md) Prestatiegegevens zijn ook handig in vergelijking met acties die u of uw toepassingen en services hebben uitgevoerd bij het analyseren van logboeken in de toekomst. 

Zie [Prestatiemeteritems in servicestof](service-fabric-diagnostics-event-generation-perf.md) voor een lijst met prestatiemeteritems die u verzamelen bij het gebruik van Service Fabric

Dit zijn twee veelvoorkomende manieren waarop u prestatiegegevens voor uw cluster instellen:

* **Een agent gebruiken**  
Dit is de favoriete manier om prestaties van een machine te verzamelen, omdat agents meestal een lijst met mogelijke prestatiestatistieken hebben die kunnen worden verzameld, en het is een relatief eenvoudig proces om de statistieken te kiezen die u wilt verzamelen of wijzigen. De informatie over de Azure Monitor die Azure Monitor-logboeken aanbiedt in Azure [Monitor-logboeken van](service-fabric-diagnostics-event-analysis-oms.md) Service Fabric, registreert integratie en Het instellen van de [loganalytics-agent](../log-analytics/log-analytics-windows-agent.md) voor meer informatie over de loganalyse-agent, een dergelijke monitoringagent die prestatiegegevens voor clusterVM's en geïmplementeerde containers kan ophalen.

* **Prestatiemeteritems naar Azure Table Storage**  
U prestatiestatistieken ook naar dezelfde tabelopslag sturen als de gebeurtenissen. Dit vereist het wijzigen van de Azure Diagnostics-configuratie om de juiste prestatiemeteritems op te halen uit de VM's in uw cluster en om dockerstatistieken op te halen als u containers implementeert. Lees meer over het configureren van [prestatiemeteritems in WAD](service-fabric-diagnostics-event-aggregation-wad.md) in Service Fabric om de prestatiemeterverzameling in te stellen.

## <a name="next-steps"></a>Volgende stappen

* Lees meer over [Azure Monitor-logboeken integratie](service-fabric-diagnostics-event-analysis-oms.md) van Service Fabric om clusterdiagnoses te verzamelen en aangepaste query's en waarschuwingen te maken
* Meer informatie over de ingebouwde diagnostische ervaring van Service Fabric, de [EventStore](service-fabric-diagnostics-eventstore.md)
* Enkele [veelvoorkomende diagnostische scenario's](service-fabric-diagnostics-common-scenarios.md) in Service Fabric doorlopen
