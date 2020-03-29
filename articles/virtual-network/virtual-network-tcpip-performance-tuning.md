---
title: TCP/IP-prestatieafstemming voor Azure VM's | Microsoft Documenten
description: Leer verschillende veelvoorkomende TCP/IP-prestatieafstemmingstechnieken en hun relatie met Azure VM's.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68297772"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP-prestatieafstemming voor Azure VM's

In dit artikel worden veelvoorkomende TCP/IP-prestatieafstemmingstechnieken en een aantal dingen besproken waarmee u rekening moet houden wanneer u ze gebruikt voor virtuele machines die op Azure worden uitgevoerd. Het zal een basisoverzicht van de technieken bieden en onderzoeken hoe ze kunnen worden afgestemd.

## <a name="common-tcpip-tuning-techniques"></a>Algemene TCP/IP-tuningtechnieken

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentatie en grote verzenduitlading

#### <a name="mtu"></a>Mtu

De maximale transmissie-eenheid (MTU) is het grootste formaat frame (pakket), opgegeven in bytes, die kan worden verzonden via een netwerkinterface. De MTU is een configureerbare instelling. De standaard MTU die wordt gebruikt op Azure VM's en de standaardinstelling op de meeste netwerkapparaten wereldwijd, is 1.500 bytes.

#### <a name="fragmentation"></a>Fragmentatie

Fragmentatie treedt op wanneer een pakket wordt verzonden dat de MTU van een netwerkinterface overschrijdt. De TCP/IP-stack breekt het pakket in kleinere stukken (fragmenten) die voldoen aan de MTU van de interface. Fragmentatie vindt plaats op de IP-laag en is onafhankelijk van het onderliggende protocol (zoals TCP). Wanneer een pakket van 2.000 byte wordt verzonden via een netwerkinterface met een MTU van 1.500, wordt het pakket opgesplitst in één pakket van 1.500 byte en één pakket van 500 byte.

Netwerkapparaten in het pad tussen bron en bestemming kunnen pakketten die de MTU overschrijden of het pakket in kleinere stukken fragmenteren.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>De niet-fragmentbit in een IP-pakket

De Don't Fragment (DF) bit is een vlag in de IP-protocol header. De DF-bit geeft aan dat netwerkapparaten op het pad tussen de afzender en ontvanger het pakket niet mogen fragmenteren. Dit beetje kan worden ingesteld om vele redenen. (Zie de sectie 'Path MTU Discovery' van dit artikel bijvoorbeeld.) Wanneer een netwerkapparaat een pakket ontvangt met de bitset Don't Fragment en dat pakket groter is dan de interface MTU van het apparaat, is het standaardgedrag dat het apparaat het pakket laat vallen. Het apparaat stuurt een ICMP Fragmentatie Nodig bericht terug naar de oorspronkelijke bron van het pakket.

#### <a name="performance-implications-of-fragmentation"></a>Prestatieimplicaties van fragmentatie

Fragmentatie kan negatieve gevolgen hebben voor de prestaties. Een van de belangrijkste redenen voor het effect op de prestaties is de CPU / geheugen impact van de fragmentatie en reassembly van pakketten. Wanneer een netwerkapparaat een pakket moet fragmenteren, moet het CPU/geheugenbronnen toewijzen om fragmentatie uit te voeren.

Hetzelfde gebeurt wanneer het pakket opnieuw wordt samengesteld. Het netwerkapparaat moet alle fragmenten opslaan totdat ze zijn ontvangen, zodat het ze weer in het oorspronkelijke pakket kan monteren. Dit proces van fragmentatie en hermontage kan ook latentie veroorzaken.

De andere mogelijke negatieve prestatie-implicatie van fragmentatie is dat gefragmenteerde pakketten mogelijk niet in orde komen. Wanneer pakketten niet in orde zijn, kunnen sommige typen netwerkapparaten ze laten vallen. Wanneer dat gebeurt, moet het hele pakket opnieuw worden verzonden.

Fragmenten worden meestal gedropt door beveiligingsapparaten zoals netwerkfirewalls of wanneer de buffers voor ontvangst van een netwerkapparaat zijn uitgeput. Wanneer de ontvangstbuffers van een netwerkapparaat zijn uitgeput, probeert een netwerkapparaat een gefragmenteerd pakket opnieuw in elkaar te zetten, maar heeft het niet de middelen om het pakket op te slaan en opnieuw aan te nemen.

Fragmentatie kan worden gezien als een negatieve bewerking, maar ondersteuning voor fragmentatie is noodzakelijk wanneer u diverse netwerken via internet met elkaar verbindt.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Voordelen en gevolgen van wijziging van de MTU

Over het algemeen u een efficiënter netwerk creëren door de MTU te verhogen. Elk pakket dat wordt verzonden heeft header-informatie die is toegevoegd aan het oorspronkelijke pakket. Wanneer fragmentatie meer pakketten creëert, is er meer header overhead, en dat maakt het netwerk minder efficiënt.

Hier volgt een voorbeeld. De ethernetkopgrootte is 14 bytes plus een vier-byte framecontrolereeks om de consistentie van het frame te garanderen. Als er één pakket van 2.000 bytes wordt verzonden, wordt 18 bytes Ethernet-overhead toegevoegd aan het netwerk. Als het pakket is gefragmenteerd in een pakket van 1.500 byte en een pakket van 500 byte, heeft elk pakket 18 bytes ethernetheader, een totaal van 36 bytes.

Houd er rekening mee dat het verhogen van de MTU niet noodzakelijkerwijs een efficiënter netwerk zal creëren. Als een toepassing slechts 500-byte-pakketten verzendt, bestaat dezelfde headeroverhead of de MTU 1.500 bytes of 9.000 bytes is. Het netwerk wordt alleen efficiënter als het gebruik maakt van grotere pakketformaten die worden beïnvloed door de MTU.

#### <a name="azure-and-vm-mtu"></a>Azure en VM MTU

De standaard MTU voor Azure VM's is 1.500 bytes. De Azure Virtual Network-stack probeert een pakket te fragmenteren op 1.400 bytes.

Houd er rekening mee dat de stack van het virtuele netwerk niet inherent inefficiënt is omdat deze pakketten met 1.400 bytes fragmenteert, hoewel VM's een MTU van 1.500 hebben. Een groot percentage netwerkpakketten is veel kleiner dan 1.400 of 1.500 bytes.

#### <a name="azure-and-fragmentation"></a>Azure en fragmentatie

Virtual Network stack is ingesteld om te laten vallen "out of order fragmenten," dat wil zeggen, gefragmenteerde pakketten die niet aankomen in hun oorspronkelijke gefragmenteerde volgorde. Deze pakketten worden voornamelijk verwijderd vanwege een netwerkbeveiligingskwetsbaarheid die in november 2018 werd aangekondigd, genaamd FragmentSmack.

FragmentSmack is een defect in de manier waarop de Linux kernel behandeld hermontage van gefragmenteerde IPv4 en IPv6 pakketten. Een externe aanvaller kan deze fout gebruiken om dure verwerkingen voor fragmentatie van fragmenten, wat kan leiden tot meer CPU en een denial of service op het doelsysteem.

#### <a name="tune-the-mtu"></a>Stem de MTU af

U een Azure VM MTU configureren, zoals in elk ander besturingssysteem. Maar u moet rekening houden met de fragmentatie die optreedt in Azure, hierboven beschreven, wanneer u een MTU configureert.

We moedigen klanten niet aan om VM MTU's te verhogen. Deze discussie is bedoeld om de details uit te leggen van hoe Azure MTU implementeert en fragmentatie uitvoert.

> [!IMPORTANT]
>Het verhogen van MTU is niet bekend om de prestaties te verbeteren en kan een negatief effect hebben op de prestaties van toepassingen.
>
>

#### <a name="large-send-offload"></a>Grote verzendlading

Large send offload (LSO) kan de netwerkprestaties verbeteren door de segmentatie van pakketten naar de Ethernet-adapter te ontladen. Wanneer LSO is ingeschakeld, maakt de TCP/IP-stack een groot TCP-pakket en stuurt het naar de Ethernet-adapter voor segmentatie voordat deze wordt doorgestuurd. Het voordeel van LSO is dat het de CPU kan bevrijden van het segmenteren van pakketten in formaten die voldoen aan de MTU en die verwerking kunnen ontladen naar de Ethernet-interface waar deze wordt uitgevoerd in hardware. Zie Ondersteuning van [grote verzenduitlading](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)voor meer informatie over de voordelen van LSO.

Wanneer LSO is ingeschakeld, kunnen Azure-klanten grote frameformaten zien wanneer ze pakketopnamen uitvoeren. Deze grote frameformaten kunnen ertoe leiden dat sommige klanten denken dat er fragmentatie plaatsvindt of dat een grote MTU wordt gebruikt wanneer dat niet het is. Met LSO kan de Ethernet-adapter een grotere maximale segmentgrootte (MSS) adverteren op de TCP/IP-stack om een groter TCP-pakket te maken. Dit hele niet-gesegmenteerde frame wordt vervolgens doorgestuurd naar de Ethernet-adapter en zou zichtbaar zijn in een packet capture uitgevoerd op de VM. Maar het pakket zal worden opgesplitst in veel kleinere frames door de Ethernet-adapter, volgens de Ethernet-adapter MTU.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS-vensterschaling en PMTUD

#### <a name="tcp-maximum-segment-size"></a>MAXIMALe segmentgrootte van TCP

TCP maximale segmentgrootte (MSS) is een instelling die de grootte van TCP-segmenten beperkt, waardoor fragmentatie van TCP-pakketten wordt voorkomen. Besturingssystemen gebruiken deze formule doorgaans om MSS in te stellen:

`MSS = MTU - (IP header size + TCP header size)`

De IP-header en de TCP-header zijn elk 20 bytes of 40 bytes in totaal. Dus een interface met een MTU van 1.500 zal een MSS van 1.460 hebben. Maar de MSS is configureerbaar.

Deze instelling is overeengekomen in de TCP-handdruk in drie richtingen wanneer een TCP-sessie is ingesteld tussen een bron en een bestemming. Beide zijden sturen een MSS-waarde en de onderste van de twee wordt gebruikt voor de TCP-verbinding.

Houd er rekening mee dat de MTU's van de bron en bestemming niet de enige factoren zijn die de MSS-waarde bepalen. Intermediaire netwerkapparaten, zoals VPN-gateways, waaronder Azure VPN Gateway, kunnen de MTU onafhankelijk van de bron en bestemming aanpassen om optimale netwerkprestaties te garanderen.

#### <a name="path-mtu-discovery"></a>Path MTU Discovery

MSS wordt onderhandeld, maar het kan niet wijzen op de werkelijke MSS die kan worden gebruikt. Dit komt omdat andere netwerkapparaten in het pad tussen de bron en de bestemming mogelijk een lagere MTU-waarde hebben dan de bron en bestemming. In dit geval zal het apparaat waarvan de MTU kleiner is dan het pakket het pakket laten vallen. Het apparaat stuurt een ICMP Fragmentatie nodig (Type 3, Code 4) bericht dat de MTU bevat. Met dit ICMP-bericht kan de bronhost zijn Path MTU op de juiste manier verminderen. Het proces heet Path MTU Discovery (PMTUD).

Het PMTUD-proces is inefficiënt en beïnvloedt de netwerkprestaties. Wanneer pakketten worden verzonden die de MTU van een netwerkpad overschrijden, moeten de pakketten opnieuw worden verzonden met een lagere MSS. Als de afzender het ICMP Fragmentatie nodig bericht niet ontvangt, misschien vanwege een netwerkfirewall in het pad (meestal aangeduid als een *PMTUD-zwartgat),* weet de afzender niet dat het de MSS moet verlagen en het pakket voortdurend opnieuw moet verzenden. Daarom raden we niet aan om de Azure VM MTU te verhogen.

#### <a name="vpn-and-mtu"></a>VPN en MTU

Als u VM's gebruikt die inkapseling uitvoeren (zoals IPsec VPN's), zijn er enkele aanvullende overwegingen met betrekking tot de grootte van het pakket en MTU. VPN's voegen meer headers toe aan pakketten, waardoor de pakketgrootte toeneemt en een kleinere MSS nodig is.

Voor Azure raden we u aan de TCP MSS-klem in te stellen op 1.350 bytes en de Tunnel interface MTU op 1.400. Zie voor meer informatie de [pagina VPN-apparaten en IPSec/IKE-parameters.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latentie, retourtijd en TCP-vensterschaling

#### <a name="latency-and-round-trip-time"></a>Latentie en retourtijd

Netwerklatentie wordt beheerst door de snelheid van het licht over een glasvezelnetwerk. Netwerkdoorvoer van TCP wordt ook effectief bepaald door de retourtijd (RTT) tussen twee netwerkapparaten.

| | | | |
|-|-|-|-|
|**Route**|**Afstand**|**Eenrichtingstijd**|**RTT**|
|New York naar San Francisco|4.148 km|21 ms|42 ms|
|New York naar Londen|5.585 km|28 ms|56 ms|
|New York naar Sydney|15.993 km|80 ms|160 ms|

Deze tabel toont de rechte afstand tussen twee locaties. In netwerken is de afstand meestal langer dan de rechte lijn afstand. Hier is een eenvoudige formule om minimum RTT te berekenen zoals bepaald door de snelheid van het licht:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

U 200 gebruiken voor de snelheid van voortplanting. Dit is de afstand, in meters, dat licht reist in 1 milliseconde.

Laten we New York naar San Francisco nemen als voorbeeld. De rechte lijn afstand is 4.148 km. Als we die waarde in de vergelijking stoppen, krijgen we het volgende:

`Minimum RTT = 2 * (4,148 / 200)`

De uitvoer van de vergelijking is in milliseconden.

Als u de beste netwerkprestaties wilt krijgen, is de logische optie om bestemmingen te selecteren met de kortste afstand tussen deze opties. U moet ook uw virtuele netwerk ontwerpen om het pad van het verkeer te optimaliseren en de latentie te verminderen. Zie het gedeelte 'Overwegingen netwerkontwerp' van dit artikel voor meer informatie.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Latentie- en retourtijdeffecten op TCP

Retourtijd heeft een direct effect op de maximale TCP-doorvoer. In het TCP-protocol is *venstergrootte* de maximale hoeveelheid verkeer die via een TCP-verbinding kan worden verzonden voordat de afzender bevestiging van de ontvanger moet ontvangen. Als de TCP MSS is ingesteld op 1.460 en de tcp-venstergrootte is ingesteld op 65.535, kan de afzender 45 pakketten verzenden voordat deze bevestiging van de ontvanger moet ontvangen. Als de afzender geen bevestiging krijgt, worden de gegevens opnieuw verzonden. Dit is de formule:

`TCP window size / TCP MSS = packets sent`

In dit voorbeeld wordt 65.535 / 1.460 naar boven afgerond op 45.

Deze "wachten op bevestiging" staat, een mechanisme om betrouwbare levering van gegevens te garanderen, is wat ervoor zorgt rtt te beïnvloeden TCP doorvoer. Hoe langer de afzender wacht op bevestiging, hoe langer het moet wachten voordat u meer gegevens verzendt.

Hier volgt de formule voor het berekenen van de maximale doorvoer van één TCP-verbinding:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Deze tabel toont de maximale megabyte/per seconde doorvoer van een enkele TCP-verbinding. (Voor leesbaarheid wordt megabytes gebruikt voor de maateenheid.)

| | | | |
|-|-|-|-|
|**TCP-venstergrootte (bytes)**|**RTT-latentie (ms)**|**Maximale megabyte/seconde doorvoer**|**Maximale megabit/seconde doorvoer**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

Als pakketten verloren gaan, wordt de maximale doorvoer van een TCP-verbinding verminderd terwijl de afzender gegevens verzendt die al zijn verzonden.

#### <a name="tcp-window-scaling"></a>TCP-vensterschalen

TCP-vensterschaling is een techniek die de grootte van het TCP-venster dynamisch verhoogt, zodat er meer gegevens kunnen worden verzonden voordat een bevestiging vereist is. In het vorige voorbeeld zouden 45 pakketten worden verzonden voordat een bevestiging vereist was. Als u het aantal pakketten verhoogt dat kan worden verzonden voordat een bevestiging nodig is, vermindert u het aantal keren dat een afzender wacht op bevestiging, waardoor de maximale doorvoer van TCP wordt verhoogd.

In deze tabel worden deze relaties geïllustreerd:

| | | | |
|-|-|-|-|
|**TCP-venstergrootte (bytes)**|**RTT-latentie (ms)**|**Maximale megabyte/seconde doorvoer**|**Maximale megabit/seconde doorvoer**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Maar de TCP-headerwaarde voor TCP-venstergrootte is slechts 2 bytes lang, wat betekent dat de maximale waarde voor een ontvangen venster 65.535 is. Om de maximale venstergrootte te vergroten, werd een TCP-vensterschaalfactor geïntroduceerd.

De schaalfactor is ook een instelling die u configureren in een besturingssysteem. Hier volgt de formule voor het berekenen van de grootte van het TCP-venster met behulp van schaalfactoren:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Hier is de berekening voor een vensterschaalfactor van 3 en een venstergrootte van 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Een schaalfactor van 14 resulteert in een TCP-venstergrootte van 14 (de maximaal toegestane verschuiving). De grootte van het TCP-venster is 1.073.725.440 bytes (8,5 gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Ondersteuning voor TCP-vensterschalen

Windows kan verschillende schalingsfactoren instellen voor verschillende verbindingstypen. (Klassen van verbindingen omvatten datacenter, internet, enzovoort.) U gebruikt `Get-NetTCPConnection` de opdracht PowerShell om het type koppelingvan het vensterschalen weer te geven:

```powershell
Get-NetTCPConnection
```

U `Get-NetTCPSetting` de opdracht PowerShell gebruiken om de waarden van elke klasse weer te geven:

```powershell
Get-NetTCPSetting
```

U de oorspronkelijke TCP-venstergrootte en TCP-schalingfactor in Windows instellen met de `Set-NetTCPSetting` opdracht PowerShell. Zie [Set-NetTCPSetting voor](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)meer informatie.

```powershell
Set-NetTCPSetting
```

Dit zijn de effectieve `AutoTuningLevel`TCP-instellingen voor:

| | | | |
|-|-|-|-|
|**Autotuninglevel**|**Schaalfactor**|**Schaalvermenigvuldiger**|**Formule<br/>om de maximale venstergrootte te berekenen**|
|Uitgeschakeld|Geen|Geen|Venstergrootte|
|Beperkt|4|2^4|Venstergrootte * (2^4)|
|Zeer beperkt|2|2^2|Venstergrootte * (2^2)|
|Normaal|8|2^8|Venstergrootte * (2^8)|
|Experimenteel|14|2^14|Venstergrootte * (2^14)|

Deze instellingen hebben de meeste kans om de TCP-prestaties te beïnvloeden, maar houd er rekening mee dat veel andere factoren op het internet, buiten de controle van Azure, ook de prestaties van TCP kunnen beïnvloeden.

#### <a name="increase-mtu-size"></a>MTU-grootte vergroten

Omdat een grotere MTU een grotere MSS betekent, u zich afvragen of het verhogen van de MTU de TCP-prestaties kan verhogen. Waarschijnlijk niet. Er zijn voor- en nadelen aan pakketgrootte dan alleen TCP-verkeer. Zoals eerder besproken, zijn de belangrijkste factoren die de TCP-doorvoerprestaties beïnvloeden TCP-venstergrootte, pakketverlies en RTT.

> [!IMPORTANT]
> We raden Azure-klanten niet aan de standaard MTU-waarde op virtuele machines te wijzigen.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Versneld netwerken en zijschalen ontvangen

#### <a name="accelerated-networking"></a>Versneld netwerken

Virtuele machine netwerk functies zijn van oudsher CPU-intensief op zowel de gast VM en de hypervisor / host. Elk pakket dat door de host wordt geleid, wordt verwerkt in software door de host-CPU, inclusief alle virtuele netwerkinkapseling en decapulatie. Dus hoe meer verkeer gaat door de host, hoe hoger de CPU belasting. En als de host-CPU bezig is met andere bewerkingen, heeft dat ook invloed op de netwerkdoorvoer en latentie. Azure verhelpt dit probleem met versnelde netwerken.

Versnelde netwerken bieden consistente ultralow netwerklatentie via de interne programmeerbare hardware van Azure en technologieën zoals SR-IOV. Versnelde netwerken verplaatst een groot deel van de Azure-softwaregedefinieerde netwerkstack van de CPU's en naar FPGA-gebaseerde SmartNICs. Met deze wijziging kunnen toepassingen voor eindgebruikers rekencycli terugvorderen, waardoor de VM minder belasting krijgt, waardoor de jitter en de inconsistentie in latentie afnemen. Met andere woorden, prestaties kunnen meer deterministisch zijn.

Versnelde netwerken verbeteren de prestaties doordat de gast-VM de host kan omzeilen en direct een datapad kan opzetten met de SmartNIC van een host. Hier zijn enkele voordelen van versnelde netwerken:

- **Lagere latentie / hogere pakketten per seconde (pps)**: Het verwijderen van de virtuele switch van het datapad elimineert de tijd die pakketten in de host doorbrengen voor beleidsverwerking en verhoogt het aantal pakketten dat in de VM kan worden verwerkt.

- **Verminderde jitter:** Virtuele switch verwerking is afhankelijk van de hoeveelheid beleid dat moet worden toegepast en de werkbelasting van de CPU die de verwerking doet. Als u de beleidshandhaving naar de hardware haalt, wordt die variabiliteit verwijderd door pakketten rechtstreeks aan de VM te leveren, waardoor de host-to-VM-communicatie en alle software-interrupts en contextswitches worden geëlimineerd.

- **Verminderd CPU-gebruik:** het omzeilen van de virtuele switch in de host leidt tot minder CPU-gebruik voor het verwerken van netwerkverkeer.

Als u versnelde netwerken wilt gebruiken, moet u deze expliciet inschakelen op elke toepasselijke VM. Zie [Een virtuele Linux-machine maken met versnelde netwerken](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) voor instructies.

#### <a name="receive-side-scaling"></a>Zijschaling ontvangen

Receive side scaling (RSS) is een netwerkstuurprogrammatechnologie die het ontvangen van netwerkverkeer efficiënter verdeelt door ontvangstverwerking over meerdere CPU's te distribueren in een multiprocessorsysteem. In eenvoudige termen, RSS kan een systeem om meer ontvangen verkeer te verwerken, omdat het gebruik maakt van alle beschikbare CPU's in plaats van slechts een. Zie [Inleiding om zijschaling te ontvangen voor](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling)een meer technische discussie over RSS.

Om de beste prestaties te krijgen wanneer versnelde netwerken is ingeschakeld op een VM, moet u RSS inschakelen. RSS kan ook voordelen bieden voor VM's die geen gebruik maken van versnelde netwerken. Zie [Netwerkdoorvoer optimaliseren voor virtuele Azure-machines voor](https://aka.ms/FastVM)een overzicht van hoe u bepalen of RSS is ingeschakeld en hoe u deze inschakelen.

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP TIME_WAIT en TIME_WAIT moord

TCP-TIME_WAIT is een andere veelvoorkomende instelling die van invloed is op de prestaties van het netwerk en de toepassing. Op drukke VM's die veel sockets openen en sluiten, als clients of als servers (Source IP:Source Port + Destination IP:Destination Port), kan een bepaalde socket tijdens de normale werking van TCP lange tijd in een TIME_WAIT-status terechtkomen. De TIME_WAIT status is bedoeld om eventuele aanvullende gegevens op een socket te kunnen leveren voordat deze wordt gesloten. Tcp/IP-stacks voorkomen dus over het algemeen het hergebruik van een socket door het TCP SYN-pakket van de client in stilte te laten vallen.

De hoeveelheid tijd die een socket in TIME_WAIT is configureerbaar. Het kan variëren van 30 seconden tot 240 seconden. Sockets zijn een eindige bron en het aantal sockets dat op een bepaald moment kan worden gebruikt, is configureerbaar. (Het aantal beschikbare sockets is meestal ongeveer 30.000.) Als de beschikbare sockets worden verbruikt of als clients en servers niet op elkaar zijn afgestemd TIME_WAIT instellingen en een VM een socket in een TIME_WAIT status probeert te hergebruiken, mislukken nieuwe verbindingen omdat TCP SYN-pakketten stilletjes worden verwijderd.

De waarde voor poortbereik voor uitgaande sockets is meestal configureerbaar binnen de TCP/IP-stack van een besturingssysteem. Hetzelfde geldt voor TCP TIME_WAIT instellingen en socket hergebruik. Het wijzigen van deze nummers kan de schaalbaarheid mogelijk verbeteren. Maar afhankelijk van de situatie kunnen deze veranderingen interoperabiliteitsproblemen veroorzaken. Je moet voorzichtig zijn als je deze waarden te veranderen.

U TIME_WAIT moord gebruiken om deze schaalbeperking aan te pakken. TIME_WAIT moord maakt het mogelijk een socket te hergebruiken in bepaalde situaties, zoals wanneer het volgnummer in het IP-pakket van de nieuwe verbinding het volgnummer van het laatste pakket van de vorige verbinding overschrijdt. In dit geval zal het besturingssysteem het mogelijk maken de nieuwe verbinding tot stand te brengen (het accepteert de nieuwe SYN/ACK) en de vorige verbinding die in een TIME_WAIT staat was, te sluiten. Deze mogelijkheid wordt ondersteund op Windows VM's in Azure. Neem contact op met de leverancier van het besturingssysteem voor meer informatie over ondersteuning in andere VM's.

Zie Instellingen die kunnen worden gewijzigd om de [netwerkprestaties te verbeteren voor](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)meer informatie over het configureren van TCP-TIME_WAIT-instellingen en het bereik van de bronpoort.

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuele netwerkfactoren die de prestaties kunnen beïnvloeden

### <a name="vm-maximum-outbound-throughput"></a>VM maximale uitgaande doorvoer

Azure biedt verschillende VM-formaten en -typen, elk met een andere mix van prestatiemogelijkheden. Een van deze mogelijkheden is netwerkdoorvoer (of bandbreedte), die wordt gemeten in megabits per seconde (Mbps). Omdat virtuele machines worden gehost op gedeelde hardware, moet de netwerkcapaciteit eerlijk worden gedeeld tussen de virtuele machines met dezelfde hardware. Grotere virtuele machines krijgen meer bandbreedte dan kleinere virtuele machines.

De netwerkbandbreedte die aan elke virtuele machine wordt toegewezen, wordt gemeten op uitgaande (uitgaande) verkeer van de virtuele machine. Al het netwerkverkeer dat de virtuele machine verlaat, wordt meegeteld voor de toegewezen limiet, ongeacht de bestemming. Als een virtuele machine bijvoorbeeld een limiet van 1000 Mbps heeft, geldt die limiet of het uitgaande verkeer is bestemd voor een andere virtuele machine in hetzelfde virtuele netwerk of een externe azure.

Ingress is niet direct gemeten of beperkt. Maar er zijn andere factoren, zoals CPU- en opslaglimieten, die van invloed kunnen zijn op het vermogen van een virtuele machine om binnenkomende gegevens te verwerken.

Versnelde netwerken zijn ontworpen om de netwerkprestaties te verbeteren, inclusief latentie, doorvoer en CPU-gebruik. Versnelde netwerken kunnen de doorvoer van een virtuele machine verbeteren, maar dat kan alleen tot aan de toegewezen bandbreedte van de virtuele machine.

Azure virtuele machines hebben ten minste een netwerkinterface aan hen verbonden. Misschien hebben ze er meerdere. De bandbreedte die aan een virtuele machine wordt toegewezen, is de som van al het uitgaande verkeer over alle netwerkinterfaces die aan de machine zijn gekoppeld. Met andere woorden, de bandbreedte wordt toegewezen op basis van een virtuele machine, ongeacht het aantal netwerkinterfaces dat aan de machine is gekoppeld.

Verwachte uitgaande doorvoer en het aantal netwerkinterfaces dat wordt ondersteund door elke VM-grootte worden beschreven in [Grootte voor virtuele Windows-machines in Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Als u de maximale doorvoer wilt zien, selecteert u een type, zoals **Algemeen doel,** en zoekt u de sectie over de groottereeks op de resulterende pagina (bijvoorbeeld 'Dv2-reeksen'). Voor elke serie is er een tabel met netwerkspecificaties in de laatste kolom, getiteld "Max NIC's / Verwachte netwerkbandbreedte (Mbps)."

De doorvoerlimiet is van toepassing op de virtuele machine. Doorvoer wordt niet beïnvloed door deze factoren:

- **Aantal netwerkinterfaces**: De bandbreedtelimiet is van toepassing op de som van al het uitgaande verkeer van de virtuele machine.

- **Versnelde netwerken:** Hoewel deze functie nuttig kan zijn bij het bereiken van de gepubliceerde limiet, verandert de limiet niet.

- **Verkeersbestemming**: Alle bestemmingen tellen mee voor de uitgaande limiet.

- **Protocol:** Al het uitgaande verkeer over alle protocollen telt mee voor de limiet.

Zie [Bandbreedte van het virtuele machinenetwerk](https://aka.ms/AzureBandwidth)voor meer informatie.

### <a name="internet-performance-considerations"></a>Overwegingen voor internetprestaties

Zoals besproken in dit artikel, kunnen factoren op het internet en buiten de controle van Azure de netwerkprestaties beïnvloeden. Hier zijn enkele van die factoren:

- **Latentie**: De retourtijd tussen twee bestemmingen kan worden beïnvloed door problemen op tussenliggende netwerken, door verkeer dat niet het "kortste" afstandspad neemt en door suboptimale peeringpaden.

- **Pakketverlies**: Pakketverlies kan worden veroorzaakt door netwerkcongestie, fysieke padproblemen en slecht presterende netwerkapparaten.

- **MTU-grootte/fragmentatie**: Fragmentatie langs het pad kan leiden tot vertragingen bij het binnenkomen van gegevens of in pakketten die niet in orde komen, wat van invloed kan zijn op de levering van pakketten.

Traceroute is een goed hulpmiddel voor het meten van netwerkprestatiekenmerken (zoals pakketverlies en latentie) langs elk netwerkpad tussen een bronapparaat en een doelapparaat.

### <a name="network-design-considerations"></a>Overwegingen voor netwerkontwerp

Samen met de overwegingen die eerder in dit artikel zijn besproken, kan de topologie van een virtueel netwerk de prestaties van het netwerk beïnvloeden. Een hub-and-spoke-ontwerp dat verkeer wereldwijd terugbrengt naar een virtueel netwerk met één hub, introduceert bijvoorbeeld netwerklatentie, wat van invloed is op de algehele netwerkprestaties.

Het aantal netwerkapparaten dat door het netwerkverkeer wordt doorgegeven, kan ook van invloed zijn op de algehele latentie. Bijvoorbeeld, in een hub-and-spoke ontwerp, als het verkeer passeert een spaak netwerk virtueel toestel en een hub virtueel toestel voordat u doorgaat naar het internet, het netwerk virtuele apparaten kunnen introduceren latentie.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure-regio's, virtuele netwerken en latentie

Azure-regio's bestaan uit meerdere datacenters die binnen een algemeen geografisch gebied bestaan. Deze datacenters staan mogelijk niet fysiek naast elkaar. In sommige gevallen worden ze gescheiden door maar liefst 10 kilometer. Het virtuele netwerk is een logische overlay bovenop het fysieke datacenternetwerk van Azure. Een virtueel netwerk impliceert geen specifieke netwerktopologie binnen het datacenter.

Twee VM's die zich in hetzelfde virtuele netwerk en subnet bevinden, bevinden zich bijvoorbeeld in verschillende racks, rijen of zelfs datacenters. Ze kunnen worden gescheiden door voeten van glasvezelkabel of door kilometers glasvezelkabel. Deze variatie kan variabele latentie (een paar milliseconden verschil) tussen verschillende VM's introduceren.

De geografische plaatsing van VM's en de mogelijke resulterende latentie tussen twee VM's kunnen worden beïnvloed door de configuratie van beschikbaarheidssets en beschikbaarheidszones. Maar de afstand tussen datacenters in een regio is regiospecifiek en voornamelijk beïnvloed door datacentertopologie in de regio.

### <a name="source-nat-port-exhaustion"></a>Bron NAT-poortuitputting

Een implementatie in Azure kan communiceren met eindpunten buiten Azure op het openbare internet en/of in de openbare IP-ruimte. Wanneer een instantie een uitgaande verbinding initieert, brengt Azure het privé-IP-adres dynamisch in kaart aan een openbaar IP-adres. Nadat Azure deze toewijzing heeft gemaakt, kan retourverkeer voor de uitgaande stroom ook het privé-IP-adres bereiken waar de stroom is ontstaan.

Voor elke uitgaande verbinding moet de Azure Load Balancer deze toewijzing gedurende enige tijd onderhouden. Met het multitenant-karakter van Azure kan het onderhouden van deze toewijzing voor elke uitgaande stroom voor elke VM resource-intensief zijn. Er zijn dus limieten die zijn ingesteld en gebaseerd zijn op de configuratie van het Azure Virtual Network. Of, om precies te zeggen, een Azure VM kan alleen een bepaald aantal uitgaande verbindingen op een bepaald moment maken. Wanneer deze limieten zijn bereikt, kan de virtuele machine niet meer uitgaande verbindingen maken.

Maar dit gedrag is configureerbaar. Zie [dit artikel](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)voor meer informatie over SNAT- en SNAT-poortuitputting.

## <a name="measure-network-performance-on-azure"></a>Netwerkprestaties meten op Azure

Een aantal van de prestatiemaxima in dit artikel zijn gerelateerd aan de netwerklatentie / retourtijd (RTT) tussen twee VM's. In dit gedeelte vindt u enkele suggesties voor het testen van latentie/RTT en het testen van TCP-prestaties en vm-netwerkprestaties. U de eerder besproken TCP/IP- en netwerkwaarden afstemmen en prestaties testen met behulp van de technieken die in deze sectie worden beschreven. U latentie, MTU-, MSS- en venstergroottewaarden aansluiten op de eerder verstrekte berekeningen en theoretische maxima vergelijken met werkelijke waarden die u tijdens het testen waarneemt.

### <a name="measure-round-trip-time-and-packet-loss"></a>Meet retourtijd en pakketverlies

TCP-prestaties zijn sterk afhankelijk van RTT en packet Loss. Het PING-hulpprogramma dat beschikbaar is in Windows en Linux biedt de eenvoudigste manier om RTT- en pakketverlies te meten. De uitvoer van PING toont de minimale/maximale/gemiddelde latentie tussen een bron en bestemming. Het zal ook pakketverlies tonen. PING gebruikt standaard het ICMP-protocol. U PsPing gebruiken om TCP RTT te testen. Zie [PsPing voor](https://docs.microsoft.com/sysinternals/downloads/psping)meer informatie.

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Werkelijke doorvoer van een TCP-verbinding meten

NTttcp is een hulpmiddel voor het testen van de TCP-prestaties van een Linux- of Windows-vm. U verschillende TCP-instellingen wijzigen en vervolgens de voordelen testen met NTttcp. Zie voor meer informatie de volgende bronnen:

- [Bandbreedte/doorvoertesten (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp-hulpprogramma](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Werkelijke bandbreedte van een virtuele machine meten

U de prestaties van verschillende VM-typen, versnelde netwerken, enzovoort, testen met behulp van een tool genaamd iPerf. iPerf is ook beschikbaar op Linux en Windows. iPerf kan TCP of UDP gebruiken om de totale netwerkdoorvoer te testen. iPerf TCP-doorvoertests worden beïnvloed door de factoren die in dit artikel worden besproken (zoals latentie en RTT). UDP kan dus betere resultaten opleveren als u alleen de maximale doorvoer wilt testen.

Raadpleeg voor meer informatie de volgende artikelen:

- [Problemen met de netwerkprestaties van Expressroute oplossen](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [VPN-doorvoer naar een virtueel netwerk valideren](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Inefficiënt TCP-gedrag detecteren

In pakketopnames kunnen Azure-klanten TCP-pakketten met TCP-vlaggen (SACK, DUP ACK, RETRANSMIT en FAST RETRANSMIT) zien die kunnen wijzen op problemen met de netwerkprestaties. Deze pakketten geven specifiek netwerkinefficiënties aan die het gevolg zijn van pakketverlies. Maar pakketverlies wordt niet noodzakelijkerwijs veroorzaakt door azure-prestatieproblemen. Prestatieproblemen kunnen het gevolg zijn van toepassingsproblemen, problemen met het besturingssysteem of andere problemen die mogelijk niet direct verband houden met het Azure-platform.

Houd er ook rekening mee dat sommige doorgifte en dubbele ACKs normaal zijn op een netwerk. TCP protocollen zijn gebouwd om betrouwbaar te zijn. Bewijs van deze TCP-pakketten in een packet capture duidt niet noodzakelijkerwijs op een systemisch netwerkprobleem, tenzij ze buitensporig zijn.

Toch zijn deze pakkettypen aanwijzingen dat de TCP-doorvoer niet de maximale prestaties bereikt, om redenen die in andere secties van dit artikel worden besproken.

## <a name="next-steps"></a>Volgende stappen

Nu u meer te weten bent gekomen over TCP/IP-prestatieafstemming voor Azure VM's, wilt u misschien andere overwegingen lezen voor [het plannen van virtuele netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) of meer informatie over het verbinden en configureren van virtuele [netwerken.](https://docs.microsoft.com/azure/virtual-network/)
