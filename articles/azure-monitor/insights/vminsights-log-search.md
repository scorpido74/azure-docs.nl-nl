---
title: Logboeken opvragen vanuit Azure-monitor voor VM's
description: Azure Monitor voor VM's-oplossing verzamelt metrische gegevens en logboekgegevens aan en in dit artikel worden de records beschreven en bevat u voorbeeldquery's.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 61a71539dc034a216689eafd8991df60db96d2a4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396930"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Logboeken opvragen vanuit Azure Monitor voor VM's

Azure Monitor voor VM's verzamelt prestatie- en verbindingsstatistieken, computer- en procesvoorraadgegevens en statusgegevens en stuurt deze door naar de werkruimte Log Analytics in Azure Monitor.  Deze gegevens zijn beschikbaar voor [query's](../../azure-monitor/log-query/log-query-overview.md) in Azure Monitor. U deze gegevens toepassen op scenario's zoals migratieplanning, capaciteitsanalyse, detectie en probleemoplossing voor prestaties op aanvraag.

## <a name="map-records"></a>Records toewijzen

Voor elke unieke computer en elk proces wordt één record per uur gegenereerd, naast de records die worden gegenereerd wanneer een proces of computer wordt gestart of is aangesloten op de functie Azure Monitor for Vms Map. Deze records hebben de eigenschappen in de volgende tabellen. De velden en waarden in de ServiceMapComputer_CL gebeurtenissen worden toegewezen aan velden van de machinebron in de API van ServiceMap Azure Resource Manager. De velden en waarden in de ServiceMapProcess_CL gebeurtenissen worden toegewezen aan de velden van de procesbron in de API van ServiceMap Azure Resource Manager. Het veld ResourceName_s komt overeen met het naamveld in de bijbehorende Resource Manager-bron. 

Er zijn intern gegenereerde eigenschappen die u gebruiken om unieke processen en computers te identificeren:

- Computer: gebruik *ResourceId* of *ResourceName_s* om een computer in een Log Analytics-werkruimte op unieke wijze te identificeren.
- Proces: Gebruik *ResourceId* om een proces binnen een Log Analytics-werkruimte op unieke wijze te identificeren. *ResourceName_s* is uniek in de context van de machine waarop het proces draait (MachineResourceName_s) 

Omdat er meerdere records kunnen bestaan voor een opgegeven proces en computer in een opgegeven tijdsbereik, kunnen query's meer dan één record retourneren voor dezelfde computer of proces. Als u alleen de meest `| summarize arg_max(TimeGenerated, *) by ResourceId` recente record wilt opnemen, voegt u de query toe.

### <a name="connections-and-ports"></a>Verbindingen en poorten

De functie Verbindingsstatistieken introduceert twee nieuwe tabellen in Azure Monitor-logboeken: VMConnection en VMBoundPort. Deze tabellen geven informatie over de verbindingen voor een machine (inkomende en uitgaande) en de serverpoorten die op deze servers zijn geopend/actief. ConnectionMetrics worden ook blootgesteld via API's die de middelen bieden om een specifieke statistiek te verkrijgen tijdens een tijdvenster. TCP-verbindingen die het gevolg zijn van *het accepteren* op een luistersocket zijn binnenkomend, terwijl verbindingen die worden gemaakt door verbinding te *maken met* een bepaald IP en een bepaalde poort uitgaand zijn. De richting van een verbinding wordt weergegeven door de eigenschap Richting, die kan worden ingesteld op **binnenkomend** of **uitgaand**. 

Records in deze tabellen worden gegenereerd uit gegevens die zijn gerapporteerd door de afhankelijkheidsagent. Elke record vertegenwoordigt een observatie over een tijdsinterval van 1 minuut. De eigenschap TimeGenerated geeft het begin van het tijdsinterval aan. Elke record bevat informatie om de betreffende entiteit te identificeren, dat wil zeggen, verbinding of poort, evenals statistieken die aan die entiteit zijn gekoppeld. Momenteel wordt alleen netwerkactiviteit gerapporteerd die optreedt met TCP via IPv4. 

#### <a name="common-fields-and-conventions"></a>Gemeenschappelijke velden en conventies 

De volgende velden en conventies zijn van toepassing op zowel VMConnection als VMBoundPort: 

- Computer: Volledig gekwalificeerde domeinnaam van de rapportagemachine 
- AgentId: de unieke id voor een machine met de Log Analytics-agent  
- Machine: naam van de Azure Resource Manager-bron voor de machine die wordt blootgesteld door ServiceMap. Het is van de vorm *m-{GUID}*, waar *GUID* is dezelfde GUID als AgentId  
- Proces: naam van de Azure Resource Manager-bron voor het proces dat door ServiceMap wordt weergegeven. Het is van de vorm *p-{hex string}*. Proces is uniek binnen een machinescope en het genereren van een unieke proces-ID voor alle machines, het combineren van machine- en procesvelden. 
- ProcessName: Uitvoerbare naam van het rapportageproces.
- Alle IP-adressen zijn tekenreeksen in IPv4 canonieke indeling, bijvoorbeeld *13.107.3.160* 

Om kosten en complexiteit te beheren, vertegenwoordigen verbindingsrecords geen afzonderlijke fysieke netwerkverbindingen. Meerdere fysieke netwerkverbindingen worden gegroepeerd in een logische verbinding, die vervolgens wordt weerspiegeld in de desbetreffende tabel.  Records in *de tabel VMConnection* vertegenwoordigen dus een logische groepering en niet de afzonderlijke fysieke verbindingen die worden waargenomen. Fysieke netwerkverbinding die dezelfde waarde deelt voor de volgende kenmerken tijdens een bepaald interval van één minuut, wordt samengevoegd tot één logische record in *VMConnection*. 

| Eigenschap | Beschrijving |
|:--|:--|
|Richting |Richting van de verbinding, waarde is *binnenkomend* of *uitgaand* |
|Machine |De computer FQDN |
|Proces |Identiteit van proces of groepen processen, initiëren/accepteren van de verbinding |
|SourceIp (SourceIp) |IP-adres van de bron |
|DestinationIp |IP-adres van de bestemming |
|DestinationPort |Havennummer van de bestemming |
|Protocol |Protocol gebruikt voor de verbinding.  Waarden is *tcp*. |

Om rekening te houden met de impact van de groepering, wordt informatie over het aantal gegroepeerde fysieke verbindingen verstrekt in de volgende eigenschappen van de record:

| Eigenschap | Beschrijving |
|:--|:--|
|LinksGevestigd |Het aantal fysieke netwerkverbindingen dat is gemaakt tijdens het rapportagetijdvenster |
|LinksBeëindigd |Het aantal fysieke netwerkverbindingen dat is beëindigd tijdens het venster voor rapportagetijd |
|Links zijn mislukt |Het aantal fysieke netwerkverbindingen dat is mislukt tijdens het venster rapportagetijd. Deze informatie is momenteel alleen beschikbaar voor uitgaande verbindingen. |
|LinksLive |Het aantal fysieke netwerkverbindingen dat aan het einde van het rapportagetijdvenster open was|

#### <a name="metrics"></a>Metrische gegevens

Naast de statistieken voor het aantal verbindingen wordt informatie over de hoeveelheid verzonden en ontvangen gegevens op een bepaalde logische verbinding of netwerkpoort ook opgenomen in de volgende eigenschappen van de record:

| Eigenschap | Beschrijving |
|:--|:--|
|BytesSent |Totaal aantal bytes dat is verzonden tijdens het venster voor rapportagetijd |
|Ontvangen bytes |Totaal aantal bytes dat is ontvangen tijdens het rapportagetijdvenster |
|Antwoorden |Het aantal reacties dat tijdens het rapportagetijdvenster is waargenomen. 
|ResponseTimeMax |De grootste responstijd (milliseconden) die tijdens het rapportagetijdvenster wordt waargenomen. Als er geen waarde is, is de eigenschap leeg.|
|ResponseTimeMin |De kleinste responstijd (milliseconden) die tijdens het rapportagetijdvenster wordt waargenomen. Als er geen waarde is, is de eigenschap leeg.|
|ResponseTimeSum (ResponseTimeSum) |De som van alle responstijden (milliseconden) waargenomen tijdens het rapportagetijdvenster. Als er geen waarde is, is de eigenschap leeg.|

Het derde type gegevens dat wordt gerapporteerd, is de reactietijd - hoe lang besteedt een beller wachten d.m.v. een verzoek dat via een verbinding wordt verzonden en waarop het externe eindpunt moet reageren. De gerapporteerde reactietijd is een schatting van de werkelijke responstijd van het onderliggende toepassingsprotocol. Het wordt berekend met behulp van heuristiek op basis van de observatie van de stroom van gegevens tussen de bron en het doel einde van een fysieke netwerkverbinding. Conceptueel is het het verschil tussen het moment dat de laatste byte van een aanvraag de afzender verlaat en het tijdstip waarop de laatste byte van het antwoord naar binnen komt. Deze twee tijdstempels worden gebruikt om aanvraag- en antwoordgebeurtenissen op een bepaalde fysieke verbinding af te definiëren. Het verschil tussen hen vertegenwoordigt de reactietijd van één aanvraag. 

In deze eerste versie van deze functie is ons algoritme een benadering die met wisselend succes kan werken, afhankelijk van het werkelijke toepassingsprotocol dat wordt gebruikt voor een bepaalde netwerkverbinding. De huidige aanpak werkt bijvoorbeeld goed voor op aanvragen gebaseerde protocollen zoals HTTP(S), maar werkt niet met protocollen op basis van een enkele reis of berichtenwachtrij.

Hier zijn een aantal belangrijke punten te overwegen:

1. Als een proces verbindingen op hetzelfde IP-adres accepteert, maar via meerdere netwerkinterfaces, wordt een afzonderlijke record voor elke interface gerapporteerd. 
2. Records met wildcard IP bevatten geen activiteit. Ze zijn opgenomen om het feit te vertegenwoordigen dat een poort op de machine open staat voor binnenkomend verkeer.
3. Om de verbositeit en het gegevensvolume te verminderen, worden records met wildcard-IP weggelaten wanneer er een overeenkomende record (voor hetzelfde proces, poort en protocol) met een specifiek IP-adres is. Wanneer een IP-record met jokertekens wordt weggelaten, wordt de eigenschap IsWildcardBind-record met het specifieke IP-adres ingesteld op 'True' om aan te geven dat de poort wordt blootgesteld via elke interface van de rapportagemachine.
4. Poorten die alleen op een specifieke interface zijn gebonden, hebben IsWildcardBind ingesteld op *False*.

#### <a name="naming-and-classification"></a>Naamgeving en classificatie

Voor het gemak is het IP-adres van het externe uiteinde van een verbinding opgenomen in de eigenschap RemoteIp. Voor binnenkomende verbindingen is RemoteIp hetzelfde als SourceIp, terwijl het voor uitgaande verbindingen hetzelfde is als DestinationIp. De eigenschap RemoteDnsCanonicalNames vertegenwoordigt de DNS-canonieke namen die door de machine voor RemoteIp zijn gerapporteerd. De eigenschappen RemoteDnsQuestions en RemoteClassification zijn gereserveerd voor toekomstig gebruik. 

#### <a name="geolocation"></a>Geolocatie

*VMConnection* bevat ook geolocatiegegevens voor het externe uiteinde van elke verbindingsrecord in de volgende eigenschappen van de record: 

| Eigenschap | Beschrijving |
|:--|:--|
|RemoteCountry RemoteCountry RemoteCountry RemoteCountry |De naam van het land/regio dat RemoteIp host.  Verenigde *Staten* bijvoorbeeld |
|RemoteLatitude (RemoteLatitude) |De geolocatie breedtegraad. Bijvoorbeeld, *47.68* |
|RemoteLongitude |De geolocatie longitude. *Bijvoorbeeld -122,12* |

#### <a name="malicious-ip"></a>Kwaadaardig IP

Elke remoteip-eigenschap in *de VMConnection-tabel* wordt gecontroleerd op basis van een set IP's met bekende schadelijke activiteiten. Als de RemoteIp als kwaadaardig wordt geïdentificeerd, worden de volgende eigenschappen ingevuld (ze zijn leeg, wanneer het IP niet als kwaadaardig wordt beschouwd) in de volgende eigenschappen van de record:

| Eigenschap | Beschrijving |
|:--|:--|
|MaliciousIp |Het RemoteIp-adres |
|IndicatorThreadType |Bedreiging indicator gedetecteerd is een van de volgende waarden, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Beschrijving |Beschrijving van de waargenomen bedreiging. |
|TLPLevel TLPLevel |Traffic Light Protocol (TLP) Level is een van de gedefinieerde waarden, *Wit*, *Groen*, *Amber*, *Red*. |
|Betrouwbaarheid |Waarden zijn *0 – 100*. |
|Severity |Waarden zijn *0 – 5*, waar *5* is de meest ernstige en *0* is helemaal niet ernstig. Standaardwaarde is *3*.  |
|FirstreportedDateTime |De eerste keer dat de provider de indicator rapporteerde. |
|Laatstegemeldedatumtijd |De laatste keer dat de indicator werd gezien door Interflow. |
|Isactive |Geeft aan dat indicatoren worden gedeactiveerd met de waarde *Waar* of *False.* |
|ReportReferenceLink |Links naar rapporten met betrekking tot een bepaalde waarneembare. |
|Aanvullende informatie |Biedt aanvullende informatie, indien van toepassing, over de waargenomen dreiging. |

### <a name="ports"></a>Poorten 

Poorten op een machine die actief binnenkomend verkeer accepteert of mogelijk verkeer kan accepteren, maar die niet actief zijn tijdens het venster voor de rapportagetijd, worden naar de VMBoundPort-tabel geschreven.  

Elke record in VMBoundPort wordt geïdentificeerd aan de volgende velden: 

| Eigenschap | Beschrijving |
|:--|:--|
|Proces | Identiteit van het proces (of groepen processen) waaraan de poort is gekoppeld.|
|Ip | IP-adres van de poort (kan ip-joker, *0.0.0.0)* zijn |
|Poort |Het poortnummer |
|Protocol | Het protocol.  Bijvoorbeeld *tcp* of *udp* (alleen *tcp* wordt momenteel ondersteund).|
 
De identiteit van een poort is afgeleid van de bovenstaande vijf velden en wordt opgeslagen in de eigenschap PortId. Deze eigenschap kan worden gebruikt om snel records voor een specifieke poort in de tijd te vinden. 

#### <a name="metrics"></a>Metrische gegevens 

Poortrecords bevatten statistieken die de verbindingen vertegenwoordigen die eraan zijn gekoppeld. Momenteel worden de volgende statistieken gerapporteerd (de details voor elke statistiek worden beschreven in de vorige sectie): 

- BytesSent en Ontvangen bytes 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Hier zijn een aantal belangrijke punten te overwegen:

- Als een proces verbindingen op hetzelfde IP-adres accepteert, maar via meerdere netwerkinterfaces, wordt een afzonderlijke record voor elke interface gerapporteerd.  
- Records met wildcard IP bevatten geen activiteit. Ze zijn opgenomen om het feit te vertegenwoordigen dat een poort op de machine open staat voor binnenkomend verkeer. 
- Om de verbositeit en het gegevensvolume te verminderen, worden records met wildcard-IP weggelaten wanneer er een overeenkomende record (voor hetzelfde proces, poort en protocol) met een specifiek IP-adres is. Wanneer een IP-record van een wildcard wordt weggelaten, wordt de eigenschap *IsWildcardBind* voor de record met het specifieke IP-adres ingesteld op *True*.  Dit geeft aan dat de poort wordt blootgesteld over elke interface van de rapportagemachine. 
- Poorten die alleen op een specifieke interface zijn gebonden, hebben IsWildcardBind ingesteld op *False*. 

### <a name="vmcomputer-records"></a>VMComputer-records

Records met een type *VMComputer* bevatten voorraadgegevens voor servers met de afhankelijkheidsagent. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--|:--|
|TenantId | De unieke id voor de werkruimte |
|SourceSystem | *Inzichten* | 
|TimeGenerated | Tijdstempel van de record (UTC) |
|Computer | De computer FQDN | 
|AgentId (AgentId) | De unieke ID van de Log Analytics-agent |
|Machine | Naam van de Azure Resource Manager-bron voor de machine die wordt blootgesteld door ServiceMap. Het is van het formulier *m-{GUID}*, waarbij *GUID* dezelfde GUID is als AgentId. | 
|DisplayName | Weergavenaam | 
|FullDisplayName | Volledige weergavenaam | 
|HostName | De naam van de machine zonder domeinnaam |
|Opstarttijd | De opstarttijd van de machine (UTC) |
|Tijdzone | De genormaliseerde tijdzone |
|VirtualisatieStaat | *virtuele*, *hypervisor*, *fysieke* |
|Ipv4Adressen | Array met IPv4-adressen | 
|Ipv4SubnetMaskers | Array met IPv4-subnetmaskers (in dezelfde volgorde als Ipv4Addresses). |
|Ipv4DefaultGateways | Array met IPv4-gateways | 
|Ipv6Adressen | Array met IPv6-adressen | 
|MacAdressen | Array met MAC-adressen | 
|DnsNames | Array met DNS-namen die aan de machine zijn gekoppeld. |
|AfhankelijkheidAgentVersion | De versie van de afhankelijkheidsagent die op de machine wordt uitgevoerd. | 
|OperatingSystemFamily | *Linux*, *Windows* |
|OperatingSystemFullName | De volledige naam van het besturingssysteem | 
|PhysicalMemoryMB | Het fysieke geheugen in megabytes | 
|Processoren | Het aantal processors | 
|CpuSpeed CpuSpeed | De CPU-snelheid in MHz | 
|VirtualMachineType VirtualMachineType | *hyperv*, *vmware*, *xen* |
|VirtualMachineNativeId VirtualMachineNativeId | De VM-id zoals toegewezen door de hypervisor | 
|VirtualMachineNativeName | De naam van de VM |
|VirtualMachineHypervisorId | De unieke id van de hypervisor die de VM host |
|HypervisorType | *hyperv hyperv* |
|HypervisorId | De unieke ID van de hypervisor | 
|HostingProvider | *Azure* |
|_ResourceId | De unieke id voor een Azure-bron |
|AzureSubscriptionId | Een wereldwijd unieke id die uw abonnement identificeert | 
|AzureResourceGroup | De naam van de Azure-brongroep waarvan de machine lid is. |
|AzureResourceName | De naam van de Azure-bron |
|AzureLocatie | De locatie van de Azure-bron |
|AzureUpdateDomein | De naam van het Azure-updatedomein |
|AzureFaultDomain | De naam van het Azure-foutdomein |
|AzureVmId | De unieke id van de virtuele Azure-machine |
|AzureSize | De grootte van de Azure VM |
|AzureImagePublisher | De naam van de Azure VM-uitgever |
|AzureImageOffering | De naam van het azure VM-aanbiedingstype | 
|AzureImageSku | De SKU van de Azure VM-afbeelding | 
|AzureImageVersion | De versie van de Azure VM-afbeelding | 
|AzureCloudServiceName | De naam van de Azure-cloudservice |
|AzureCloudServiceImplementatie | Implementatie-id voor de CloudService |
|AzureCloudServiceRoleName | Naam van de rol naam van cloudservice |
|AzureCloudServiceRoleType | Roltype CloudService: *werknemer* of *web* |
|AzureCloudServiceInstanceId | Functie-id voor cloudservice-rol |
|AzureVmScaleSetName | De naam van de virtuele machineschaalset |
|AzureVmScaleSetDeployment | Implementatie-id voor virtuele machineschaalset |
|AzurevmScaleSetResourceId | De unieke id van de bron van de virtuele machineschaalset.|
|AzureVmScaleSetInstanceId | De unieke id van de virtuele machineschaalset |
|AzureServiceFabricClusterId | De unieke identifer van het Azure Service Fabric-cluster | 
|AzureServiceFabricClusterName | De naam van het Azure Service Fabric-cluster |

### <a name="vmprocess-records"></a>VMProcess-records

Records met een type *VMProcess* bevatten voorraadgegevens voor met TCP verbonden processen op servers met de afhankelijkheidsagent. Deze records hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--|:--|
|TenantId | De unieke id voor de werkruimte |
|SourceSystem | *Inzichten* | 
|TimeGenerated | Tijdstempel van de record (UTC) |
|Computer | De computer FQDN | 
|AgentId (AgentId) | De unieke ID van de Log Analytics-agent |
|Machine | Naam van de Azure Resource Manager-bron voor de machine die wordt blootgesteld door ServiceMap. Het is van het formulier *m-{GUID}*, waarbij *GUID* dezelfde GUID is als AgentId. | 
|Proces | De unieke id van het servicemapproces. Het is in de vorm van *p-{GUID}*. 
|ExecutableName | De naam van het uitvoerbare proces | 
|DisplayName | Weergavenaam proces |
|Rol | Procesrol *webserver:*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Groep | Groepsnaam verwerken. Processen in dezelfde groep zijn logisch met elkaar verbonden, bijvoorbeeld een deel van hetzelfde product of systeemonderdeel. |
|StartTime | De begintijd van de procespool |
|FirstPid FirstPid | De eerste PID in de procespool |
|Beschrijving | De procesbeschrijving |
|CompanyName | De naam van het bedrijf |
|InternalName | De interne naam |
|ProductName | De naam van het product |
|ProductVersion | De versie van het product |
|Bestandsversie | De versie van het bestand |
|Uitvoerbaar Pad |Het pad van de uitvoerbare |
|Commandline | De opdrachtregel |
|WorkingDirectory | De werkmap |
|Services | Een scala aan services waarbij het proces wordt uitgevoerd |
|UserName | De rekening waaronder het proces wordt uitgevoerd |
|UserDomain | Het domein waaronder het proces wordt uitgevoerd |
|_ResourceId | De unieke id voor een proces binnen de werkruimte |


## <a name="sample-map-queries"></a>Voorbeeldkaartquery's

### <a name="list-all-known-machines"></a>Alle bekende machines weergeven

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Wanneer is de VM voor het laatst opnieuw opgestart

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Overzicht van Azure VM's op afbeelding, locatie en SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>De fysieke geheugencapaciteit van alle beheerde computers weergeven

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Computernaam, DNS, IP en BE weergeven

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Alle processen zoeken met 'sql' in de opdrachtregel

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Een machine zoeken (meest recente record) op resourcenaam

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Een machine zoeken (meest recente record) op IP-adres

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Alle bekende processen op een bepaalde machine weergeven

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Alle computers met SQL Server weergeven

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Alle unieke productversies van curl in mijn datacenter weergeven

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Een computergroep maken van alle computers waarop CentOS wordt uitgevoerd

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Verzonden bytes en ontvangen trends

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Welke Azure VM's de meeste bytes verzenden

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Statustrends koppelen

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Trend verbindingsfouten

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

### <a name="number-of-open-ports-across-machines"></a>Aantal open poorten tussen machines

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Scoor processen in uw werkruimte op het aantal poorten dat ze hebben geopend

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Geaggregeerd gedrag voor elke poort

Deze query kan vervolgens worden gebruikt om poorten te scoren op activiteit, bijvoorbeeld poorten met het meeste inkomende/uitgaande verkeer, poorten met de meeste verbindingen
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
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
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

## <a name="performance-records"></a>Prestatierecords
Records met een type *InsightsMetrics* bevatten prestatiegegevens van het gastbesturingssysteem van de virtuele machine. Deze records hebben de eigenschappen in de volgende tabel:


| Eigenschap | Beschrijving |
|:--|:--|
|TenantId | Unieke id voor de werkruimte |
|SourceSystem | *Inzichten* | 
|TimeGenerated | Tijd dat de waarde is verzameld (UTC) |
|Computer | De computer FQDN | 
|Oorsprong | *vm.azm.ms* |
|Naamruimte | Categorie van het prestatiemeterrecht | 
|Name | Naam van het prestatiemeterrecht |
|Val | Verzamelde waarde | 
|Tags | Gerelateerde details over de plaat. Zie de onderstaande tabel voor tags die worden gebruikt met verschillende recordtypen.  |
|AgentId (AgentId) | Unieke id voor de agent van elke computer |
|Type | *InsightsMetrics* |
|_ResourceId_ | Resource-id van de virtuele machine |

De prestatiemeteritems die momenteel zijn verzameld in de tabel *InsightsMetrics* worden weergegeven in de volgende tabel:

| Naamruimte | Name | Beschrijving | Eenheid | Tags |
|:---|:---|:---|:---|:---|
| Computer    | Hartslag             | Computerheartbeat                        | | |
| Geheugen      | BeschikbaarMB           | Beschikbare bytes voor geheugen                    | Bytes          | memorySizeMB - Totale geheugengrootte|
| Netwerk     | SchrijfbytesPerSeconde   | Netwerkschrijfbytes per seconde            | BytesPerSeconde | NetworkDeviceId - Id van het apparaat<br>bytes - Totaal verzonden bytes |
| Netwerk     | ReadBytesPerSeconde    | Netwerkleesbytes per seconde             | BytesPerSeconde | networkDeviceId - Id van het apparaat<br>bytes - Totaal aantal ontvangen bytes |
| Processor   | GebruikPercentage | Percentage processorgebruik          | Percentage        | totalCpu's - Totaal CPU's |
| Logische schijf | WritesperSeconde       | Logische schijfschrijft per seconde            | CountPerSeconde | mountId - Mount ID van het apparaat |
| Logische schijf | Schrijflatencym's        | Latentie van logische schijfschrijflatentie milliseconden    | Milliseconden   | mountId - Mount ID van het apparaat |
| Logische schijf | SchrijfbytesPerSeconde   | Schrijfbytes voor logische schijf per seconde       | BytesPerSeconde | mountId - Mount ID van het apparaat |
| Logische schijf | TransfersPerSeconde    | Logische schijfoverdrachten per seconde         | CountPerSeconde | mountId - Mount ID van het apparaat |
| Logische schijf | TransferLatencyM's     | Latentie van logische schijfoverdracht milliseconde | Milliseconden   | mountId - Mount ID van het apparaat |
| Logische schijf | ReadsPerSeconde        | Logische schijf leest per seconde             | CountPerSeconde | mountId - Mount ID van het apparaat |
| Logische schijf | LeeslatencyM's         | Latentie van logische schijflezen milliseconde     | Milliseconden   | mountId - Mount ID van het apparaat |
| Logische schijf | ReadBytesPerSeconde    | Gelezen bytes voor logische schijf per seconde        | BytesPerSeconde | mountId - Mount ID van het apparaat |
| Logische schijf | FreeSpacePercentage   | Percentage logische schijfvrije ruimte        | Percentage        | mountId - Mount ID van het apparaat |
| Logische schijf | FreeSpaceMB           | Bytes logische schijfvrije ruimte             | Bytes          | mountId - Mount ID van het apparaat<br>diskSizeMB - Totale schijfgrootte |
| Logische schijf | BytesPerSeconde        | Logische schijfbytes per seconde             | BytesPerSeconde | mountId - Mount ID van het apparaat |


## <a name="next-steps"></a>Volgende stappen

* Als u nieuw bent in het schrijven van logboekquery's in Azure Monitor, controleert u [hoe u Log Analytics](../../azure-monitor/log-query/get-started-portal.md) in de Azure-portal gebruiken om logboekquery's te schrijven.

* Meer informatie over [het schrijven van zoekopdrachten](../../azure-monitor/log-query/search-queries.md).
