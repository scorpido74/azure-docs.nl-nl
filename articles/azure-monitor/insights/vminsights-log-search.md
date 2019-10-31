---
title: Een query uitvoeren op Logboeken vanuit Azure Monitor voor VM's (preview) | Microsoft Docs
description: Met Azure Monitor voor VM's oplossing worden metrische gegevens verzameld en wordt er informatie vastgelegd in en in dit artikel worden de records beschreven en worden voorbeeld query's opgenomen.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/29/2019
ms.openlocfilehash: 69ed49c0e1b90b4086a40bd15f5d276c6cfe137f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162214"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Logboeken van Azure Monitor voor VM's opvragen (preview-versie)
Azure Monitor voor VM's verzamelt metrische gegevens over prestaties en verbindingen, computer-en proces inventarisgegevens en status gegevens, en stuurt deze door naar de werk ruimte Log Analytics in Azure Monitor.  Deze gegevens zijn beschikbaar voor [query's](../../azure-monitor/log-query/log-query-overview.md) in azure monitor. U kunt deze gegevens Toep assen op scenario's met inbegrip van migratie planning, capaciteits analyse, detectie en prestatie problemen oplossen op aanvraag.

## <a name="map-records"></a>Records toewijzen
Er wordt één record per uur gegenereerd voor elke unieke computer en elk proces, naast de records die worden gegenereerd wanneer een proces of computer wordt gestart of op Azure Monitor voor VM's kaart-functie wordt uitgevoerd. Deze records hebben de eigenschappen in de volgende tabellen. De velden en waarden in de ServiceMapComputer_CL-gebeurtenissen worden toegewezen aan velden van de machine bron in de ServiceMap-Azure Resource Manager-API. De velden en waarden in de ServiceMapProcess_CL-gebeurtenissen worden toegewezen aan de velden van de proces resource in de ServiceMap-Azure Resource Manager-API. Het veld ResourceName_s komt overeen met het veld naam in de bijbehorende resource manager-resource. 

Er zijn intern gegenereerde eigenschappen die u kunt gebruiken om unieke processen en computers te identificeren:

- Computer: gebruik *ResourceID* of *ResourceName_s* om een computer binnen een log Analytics-werk ruimte uniek te identificeren.
- Proces: gebruik *ResourceID* als unieke id voor een proces binnen een log Analytics-werk ruimte. *ResourceName_s* is uniek binnen de context van de computer waarop het proces wordt uitgevoerd (MachineResourceName_s) 

Omdat er meerdere records kunnen bestaan voor een opgegeven proces en computer in een opgegeven tijds bereik, kunnen query's meer dan één record retour neren voor dezelfde computer of hetzelfde proces. Voeg `| summarize arg_max(TimeGenerated, *) by ResourceId` toe aan de query om alleen de meest recente record op te nemen.

### <a name="connections-and-ports"></a>Verbindingen en poorten
De functie metrische verbindings gegevens introduceert twee nieuwe tabellen in Azure Monitor logs-VMConnection en VMBoundPort. Deze tabellen bevatten informatie over de verbindingen voor een machine (binnenkomend en uitgaand), evenals de server poorten die op de computer zijn geopend/actief. ConnectionMetrics worden ook weer gegeven via Api's die de mogelijkheid bieden om een specifieke metriek te verkrijgen tijdens een tijd venster. TCP-verbindingen die het resultaat zijn van het *accepteren* van een luisterende socket zijn inkomend, terwijl deze worden gemaakt door *verbinding te maken* met een bepaald IP-adres en een andere poort. De richting van een verbinding wordt vertegenwoordigd door de eigenschap direction, die kan worden ingesteld op **Inkomend** of **uitgaand**. 

Records in deze tabellen worden gegenereerd op basis van gegevens die zijn gerapporteerd door de Dependency Agent. Elke record vertegenwoordigt een observatie over een tijds interval van 1 minuut. De eigenschap TimeGenerated geeft het begin van het tijds interval aan. Elke record bevat informatie voor het identificeren van de respectieve entiteit, dat wil zeggen, verbinding of poort, en de metrische gegevens die aan die entiteit zijn gekoppeld. Op dit moment worden alleen netwerk activiteiten gerapporteerd die worden uitgevoerd via TCP via IPv4. 

#### <a name="common-fields-and-conventions"></a>Algemene velden en conventies 
De volgende velden en conventies zijn van toepassing op zowel VMConnection als VMBoundPort: 

- Computer: een volledig gekwalificeerde domein naam van de rapport computer 
- AgentID: de unieke id voor een machine met de Log Analytics-agent  
- Machine: naam van de Azure Resource Manager resource voor de machine die door ServiceMap wordt weer gegeven. Het is van het formulier *m-{GUID}* , waarbij *GUID* dezelfde GUID is als AgentID  
- Proces: de naam van de Azure Resource Manager resource voor het proces dat door ServiceMap wordt weer gegeven. Het is van het formulier *p-{hex string}* . Het proces is uniek binnen een computer bereik en voor het genereren van een unieke proces-ID op verschillende computers, combi neer de velden machine en proces. 
- Verwerker: naam van het uitvoer bare bestand van het rapportage proces.
- Alle IP-adressen zijn teken reeksen in de standaard notatie van IPv4, bijvoorbeeld *13.107.3.160* 

Voor het beheren van de kosten en complexiteit vertegenwoordigen verbindings records geen afzonderlijke fysieke netwerk verbindingen. Meerdere fysieke netwerk verbindingen worden gegroepeerd in een logische verbinding, die vervolgens wordt weer gegeven in de respectievelijke tabel.  Dit betekent dat records in de tabel *VMConnection* een logische groepering vertegenwoordigen en niet de afzonderlijke fysieke verbindingen die worden waargenomen. Fysieke netwerk verbinding delen dezelfde waarde voor de volgende kenmerken gedurende een opgegeven interval van één minuut, worden geaggregeerd naar één logische record in *VMConnection*. 

| Eigenschap | Beschrijving |
|:--|:--|
|Richting |Richting van de verbinding, waarde is *Inkomend* of *uitgaand* |
|Machine |De FQDN van de computer |
|Proces |Identiteit van proces of groepen processen, initiëren/accepteren van de verbinding |
|SourceIp |IP-adres van de bron |
|DestinationIp |IP-adres van de bestemming |
|DestinationPort |Poort nummer van de doel |
|Protocol |Het protocol dat wordt gebruikt voor de verbinding.  Waarden zijn *TCP*. |

Om rekening te houden met de impact van groepering, wordt informatie over het aantal gegroepeerde fysieke verbindingen in de volgende eigenschappen van de record gegeven:

| Eigenschap | Beschrijving |
|:--|:--|
|LinksEstablished |Het aantal fysieke netwerk verbindingen dat tot stand is gebracht tijdens het rapportage tijd venster |
|LinksTerminated |Het aantal fysieke netwerk verbindingen dat is beëindigd tijdens het rapportage tijd venster |
|LinksFailed |Het aantal fysieke netwerk verbindingen dat is mislukt tijdens het rapportage tijd venster. Deze informatie is momenteel alleen beschikbaar voor uitgaande verbindingen. |
|LinksLive |Het aantal fysieke netwerk verbindingen dat aan het einde van het rapportage tijd venster is geopend|

#### <a name="metrics"></a>Metrische gegevens

Naast de metrieken voor het aantal verbindingen, worden gegevens over het volume van de gegevens die zijn verzonden en ontvangen op een bepaalde logische verbinding of netwerk poort ook opgenomen in de volgende eigenschappen van de record:

| Eigenschap | Beschrijving |
|:--|:--|
|Bytes sent |Totaal aantal bytes dat is verzonden tijdens het rapportage tijd venster |
|BytesReceived |Totaal aantal bytes dat is ontvangen tijdens het rapportage tijd venster |
|Antwoorden |Het aantal antwoorden dat is waargenomen tijdens het rapportage tijd venster. 
|ResponseTimeMax |De grootste reactie tijd (milliseconden) die is waargenomen tijdens het rapportage tijd venster. Als er geen waarde is, is de eigenschap leeg.|
|ResponseTimeMin |De kleinste reactie tijd (milliseconden) die is waargenomen tijdens het rapportage tijd venster. Als er geen waarde is, is de eigenschap leeg.|
|ResponseTimeSum |De som van alle reactie tijden (milliseconden) die zijn waargenomen tijdens het rapportage tijd venster. Als er geen waarde is, is de eigenschap leeg.|

Het derde type gegevens dat wordt gerapporteerd, is de reactie tijd: hoe lang een beller wacht op een aanvraag die wordt verzonden via een verbinding om te worden verwerkt en beantwoord door het externe eind punt. De gemelde reactie tijd is een schatting van de werkelijke reactie tijd van het onderliggende toepassings protocol. Het wordt berekend aan de hand van heuristiek, gebaseerd op de gegevens stroom tussen het bron-en doel einde van een fysieke netwerk verbinding. In het algemeen is het het verschil tussen het tijdstip waarop de laatste byte van een aanvraag de afzender verlaat en het tijdstip waarop de laatste byte van het antwoord ernaar terugkomt. Deze twee tijds tempels worden gebruikt om aanvraag-en reactie gebeurtenissen op een bepaalde fysieke verbinding af te bakenen. Het verschil tussen deze geeft de reactie tijd van een enkele aanvraag aan. 

In deze eerste release van deze functie is ons algoritme een benadering die kan samen werken met een verschillende mate van succes, afhankelijk van het daad werkelijke toepassings protocol dat voor een bepaalde netwerk verbinding wordt gebruikt. De huidige aanpak werkt bijvoorbeeld goed voor op aanvragen gebaseerde protocollen zoals HTTP (S), maar werkt niet met protocollen die zijn gebaseerd op een wachtrij of berichten wachtrijen.

Hier volgen enkele belang rijke punten om rekening mee te houden:

1. Als een proces verbindingen accepteert op hetzelfde IP-adres maar via meerdere netwerk interfaces, wordt een afzonderlijke record voor elke interface gerapporteerd. 
2. Records met het Joker teken IP bevatten geen activiteit. Ze zijn opgenomen om het feit te vertegenwoordigen dat een poort op de machine is geopend voor inkomend verkeer.
3. Records met Joker teken-IP worden wegge laten als er een overeenkomende record is (voor hetzelfde proces, dezelfde poort en hetzelfde protocol) als voor een specifiek IP-adres. Wanneer een IP-Joker record wordt wegge laten, wordt de IsWildcardBind-record eigenschap met het specifieke IP-adres ingesteld op ' True ' om aan te geven dat de poort wordt weer gegeven via elke interface van de rapportage computer.
4. Voor poorten die alleen zijn gebonden voor een specifieke interface, is IsWildcardBind ingesteld op *False*.

#### <a name="naming-and-classification"></a>Naamgeving en classificatie
Voor het gemak wordt het IP-adres van de externe kant van een verbinding opgenomen in de eigenschap RemoteIp. Voor binnenkomende verbindingen is RemoteIp hetzelfde als SourceIp, terwijl voor uitgaande verbindingen hetzelfde is als DestinationIp. De eigenschap RemoteDnsCanonicalNames vertegenwoordigt de canonieke DNS-namen die door de computer worden gerapporteerd voor RemoteIp. De eigenschappen RemoteDnsQuestions en RemoteClassification zijn gereserveerd voor toekomstig gebruik. 

#### <a name="geolocation"></a>Geolocatie
*VMConnection* bevat ook geolocatie gegevens voor het externe einde van elke verbindings record in de volgende eigenschappen van de record: 

| Eigenschap | Beschrijving |
|:--|:--|
|RemoteCountry |De naam van het land of de regio die als host fungeert voor RemoteIp.  Bijvoorbeeld *Verenigde Staten* |
|RemoteLatitude |De geolocatie breedte. Bijvoorbeeld *47,68* |
|RemoteLongitude |De geografische locatie lengte. Bijvoorbeeld *-122,12* |

#### <a name="malicious-ip"></a>Schadelijk IP-adres
Elke eigenschap RemoteIp in de tabel *VMConnection* wordt gecontroleerd op basis van een reeks IP-adressen met bekende schadelijke activiteiten. Als de RemoteIp als schadelijk is geïdentificeerd, worden de volgende eigenschappen ingevuld (ze zijn leeg, wanneer het IP-adres niet als schadelijk wordt beschouwd) in de volgende eigenschappen van de record:

| Eigenschap | Beschrijving |
|:--|:--|
|MaliciousIp |Het RemoteIp-adres |
|IndicatorThreadType |Gedetecteerde bedreigings indicator is een van de volgende waarden: *botnet*, *C2*, *CryptoMining*, *Darknet*, *DDoS*, *MaliciousUrl*, *malware*, *phishing*, *proxy*, *pua's geblokkeerd*,  *Watch list*.   |
|Beschrijving |Beschrijving van de waargenomen bedreiging. |
|TLPLevel |TLP-niveau (Traffic Light Protocol) is een van de gedefinieerde waarden, *wit*, *groen*, *geel*, *rood*. |
|vallen |De waarden zijn *0 – 100*. |
|Ernst |De waarden zijn *0 – 5*, waarbij *5* het ernstigste en *0* is. De standaard waarde is *3*.  |
|FirstReportedDateTime |De eerste keer dat de provider de indicator heeft gerapporteerd. |
|LastReportedDateTime |De laatste keer dat de indicator door de stroom is gezien. |
|IsActive |Hiermee wordt aangegeven dat indica toren worden gedeactiveerd met de waarde *True* of *False* . |
|ReportReferenceLink |Koppelingen naar rapporten die zijn gerelateerd aan een gegeven waarneembaar. |
|additionalInformation |Biedt aanvullende informatie, indien van toepassing, over de waargenomen bedreiging. |

### <a name="ports"></a>Poorten 
Poorten op een computer waarop actief binnenkomend verkeer wordt geaccepteerd of waarvoor mogelijk verkeer kan worden geaccepteerd, maar die niet actief zijn tijdens het rapportage tijd venster, worden naar de tabel VMBoundPort geschreven.  

Elke record in VMBoundPort wordt geïdentificeerd aan de hand van de volgende velden: 

| Eigenschap | Beschrijving |
|:--|:--|
|Proces | De identiteit van het proces (of groep processen) waarmee de poort is gekoppeld.|
|Onderzoek | IP-adres van poort (kan *IP-adressen*van joker tekens zijn) |
|Port |Het poort nummer |
|Protocol | Het protocol.  Voor beeld: *TCP* of *UDP* (alleen *TCP* wordt momenteel ondersteund).|
 
De identiteit van een poort wordt afgeleid van de bovenstaande vijf velden en wordt opgeslagen in de eigenschap PortId. Deze eigenschap kan worden gebruikt om snel records te vinden voor een specifieke poort in de loop van de tijd. 

#### <a name="metrics"></a>Metrische gegevens 
Poort records bevatten metrische gegevens voor de verbindingen die eraan zijn gekoppeld. Op dit moment worden de volgende metrische gegevens gerapporteerd (de details van elke metriek worden in de vorige sectie beschreven): 

- Bytes sent en BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Hier volgen enkele belang rijke punten om rekening mee te houden:

- Als een proces verbindingen accepteert op hetzelfde IP-adres maar via meerdere netwerk interfaces, wordt een afzonderlijke record voor elke interface gerapporteerd.  
- Records met het Joker teken IP bevatten geen activiteit. Ze zijn opgenomen om het feit te vertegenwoordigen dat een poort op de machine is geopend voor inkomend verkeer. 
- Records met Joker teken-IP worden wegge laten als er een overeenkomende record is (voor hetzelfde proces, dezelfde poort en hetzelfde protocol) als voor een specifiek IP-adres. Wanneer een IP-adres record met Joker tekens wordt wegge laten, wordt de eigenschap *IsWildcardBind* voor de record met het specifieke IP-adressen ingesteld op *True*.  Dit geeft aan dat de poort wordt weer gegeven via elke interface van de rapport computer. 
- Voor poorten die alleen zijn gebonden voor een specifieke interface, is IsWildcardBind ingesteld op *False*. 

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL records
Records met een type *ServiceMapComputer_CL* hebben inventaris gegevens voor servers met de afhankelijkheids agent. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--|:--|
| Type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | De unieke id voor een machine in de werk ruimte |
| ResourceName_s | De unieke id voor een machine in de werk ruimte |
| ComputerName_s | De FQDN van de computer |
| Ipv4Addresses_s | Een lijst met IPv4-adressen van de server |
| Ipv6Addresses_s | Een lijst met IPv6-adressen van de server |
| DnsNames_s | Een matrix met DNS-namen |
| OperatingSystemFamily_s | Windows of Linux |
| OperatingSystemFullName_s | De volledige naam van het besturings systeem  |
| Bitness_s | De Bitness van de machine (32-bits of 64-bits)  |
| PhysicalMemory_d | Het fysieke geheugen in MB |
| Cpus_d | Het aantal Cpu's |
| CpuSpeed_d | De CPU-snelheid in MHz|
| VirtualizationState_s | *onbekend*, *fysiek*, *virtueel*, *Hyper Visor* |
| VirtualMachineType_s | *hyper-v*, *VMware*, enzovoort |
| VirtualMachineNativeMachineId_g | De VM-ID die is toegewezen door de Hyper Visor |
| VirtualMachineName_s | De naam van de virtuele machine |
| BootTime_t | De opstart tijd |

### <a name="servicemapprocess_cl-type-records"></a>Records van het type ServiceMapProcess_CL
Records met een type *ServiceMapProcess_CL* hebben inventaris gegevens voor met TCP verbonden processen op servers met de afhankelijkheids agent. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--|:--|
| Type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | De unieke id voor een proces in de werk ruimte |
| ResourceName_s | De unieke id voor een proces in de machine waarop het wordt uitgevoerd|
| MachineResourceName_s | De resource naam van de machine |
| ExecutableName_s | De naam van het uitvoer bare proces |
| StartTime_t | De begin tijd van de proces groep |
| FirstPid_d | De eerste pincode in de proces groep |
| Description_s | De proces beschrijving |
| CompanyName_s | De naam van het bedrijf |
| InternalName_s | De interne naam |
| ProductName_s | De naam van het product |
| ProductVersion_s | De product versie |
| FileVersion_s | De bestands versie |
| CommandLine_s | De opdracht regel |
| ExecutablePath_s | Het pad naar het uitvoer bare bestand |
| WorkingDirectory_s | De werkmap |
| Gebruikers | Het account waarmee het proces wordt uitgevoerd |
| User Domain | Het domein waaronder het proces wordt uitgevoerd |

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

### <a name="list-all-known-machines"></a>Alle bekende computers weer geven
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Wanneer is de VM voor het laatst opnieuw opgestart
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Samen vatting van virtuele Azure-machines op installatie kopie, locatie en SKU
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>De capaciteit van het fysieke geheugen van alle beheerde computers weer geven.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s
```

### <a name="list-computer-name-dns-ip-and-os"></a>Computer naam, DNS, IP en besturings systeem weer geven.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Alle processen zoeken met ' SQL ' in de opdracht regel
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Zoek een machine (de meest recente record) op resource naam
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Een machine (meest recente record) zoeken op IP-adres
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Alle bekende processen op een opgegeven computer weer geven
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Alle computers met SQL Server weer geven
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Alle unieke product versies van krul in mijn Data Center weer geven
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Een computer groep maken van alle computers waarop CentOS wordt uitgevoerd
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s
```

### <a name="bytes-sent-and-received-trends"></a>Verzonden en trends ontvangen bytes
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Welke Azure-Vm's de meeste bytes verzenden
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Trends van koppelings status
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Trend van verbindings fouten
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Gebonden poorten
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Aantal open poorten op computers
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Score processen in uw werk ruimte op het aantal poorten dat ze hebben geopend
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Aggregatie gedrag voor elke poort
Deze query kan vervolgens worden gebruikt om poorten te beoordelen op basis van activiteit, bijvoorbeeld poorten met het meeste binnenkomend/uitgaand verkeer, poorten met de meeste verbindingen
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>De uitgaande verbindingen van een groep machines samenvatten
```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>Volgende stappen
* Als u geen logboek query's in Azure Monitor kunt schrijven, raadpleegt u [hoe u log Analytics](../../azure-monitor/log-query/get-started-portal.md) in de Azure Portal gebruikt om logboek query's te schrijven.

* Meer informatie over het [schrijven van zoek query's](../../azure-monitor/log-query/search-queries.md).
