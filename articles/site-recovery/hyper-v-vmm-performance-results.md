---
title: Hyper-V VM-replicatie testen op een secundaire site met VMM met Azure Site Recovery
description: In dit artikel vindt u informatie over prestatietests voor replicatie van Hyper V VM's in VMM-clouds naar een secundaire site met Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 3edd182e335bc679d95d7be64f45b617a9f54c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73663176"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Testresultaten voor Hyper-V-replicatie naar een secundaire site


Dit artikel bevat de resultaten van prestatietests bij het repliceren van Hyper-V VM's in VMM-clouds (System Center Virtual Machine Manager) naar een secundair datacenter.

## <a name="test-goals"></a>Testdoelen

Het doel van het testen was om te onderzoeken hoe Site Recovery presteert tijdens steady state replicatie.

- Replicatie met steady state vindt plaats wanneer VM's de eerste replicatie hebben voltooid en deltawijzigingen worden gesynchroniseerd.
- Het is belangrijk om prestaties te meten met behulp van steady state, omdat het de status is waarin de meeste VM's blijven, tenzij er onverwachte onderbrekingen optreden.
- De testimplementatie bestond uit twee on-premises sites, met een VMM-server op elke site. Deze testimplementatie is typerend voor een implementatie van het hoofdkantoor/filiaal, waarbij het hoofdkantoor fungeert als de primaire site en het filiaal als secundaire of herstelsite.

## <a name="what-we-did"></a>Wat we deden

Dit hebben we gedaan in de testpas:

1. Vm's gemaakt met VMM-sjablonen.
2. Gestarte VM's en vastgelegde basislijnprestatiestatistieken gedurende 12 uur.
3. Gemaakt clouds op de primaire en herstel VMM-servers.
4. Geconfigureerde replicatie in Siteherstel, inclusief toewijzing tussen bron- en herstelwolken.
5. Ingeschakelde beveiliging voor VM's en deze in staat gesteld om de eerste replicatie te voltooien.
6. Ik heb een paar uur gewacht op systeemstabilisatie.
7. Vastgelegde prestatiestatistieken gedurende 12 uur, waarbij alle VM's gedurende die 12 uur in een verwachte replicatiestatus bleven.
8. De delta gemeten tussen de prestatiestatistieken basislijn en de replicatieprestatiestatistieken.


## <a name="primary-server-performance"></a>Primaire serverprestaties

* Hyper-V Replica (gebruikt door Site Recovery) houdt wijzigingen in een logboekbestand asynchroon bij, met minimale opslagoverhead op de primaire server.
* Hyper-V Replica maakt gebruik van zelfonderhouden geheugen cache om IOPS overhead te minimaliseren voor het bijhouden. Het slaat schrijft naar de VHDX in het geheugen, en spoelt ze in het logbestand voor de tijd dat het logboek wordt verzonden naar de herstelsite. Een schijf flush gebeurt ook als de schrijfbewerkingen een vooraf bepaalde limiet raken.
* De onderstaande grafiek toont de steady state IOPS overhead voor replicatie. We kunnen zien dat de IOPS overhead als gevolg van replicatie is ongeveer 5%, dat is vrij laag.

  ![Primaire resultaten](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica gebruikt geheugen op de primaire server om de schijfprestaties te optimaliseren. Zoals in de volgende grafiek wordt weergegeven, is de geheugenoverhead op alle servers in het primaire cluster marginaal. De weergegeven geheugenoverhead is het percentage geheugen dat door replicatie wordt gebruikt, in vergelijking met het totale geïnstalleerde geheugen op de Hyper-V-server.

![Primaire resultaten](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica heeft minimale CPU overhead. Zoals weergegeven in de grafiek, replicatie overhead is in het bereik van 2-3%.

![Primaire resultaten](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Secundaire serverprestaties

Hyper-V Replica gebruikt een kleine hoeveelheid geheugen op de herstelserver om het aantal opslagbewerkingen te optimaliseren. De grafiek geeft een overzicht van het geheugengebruik op de herstelserver. De weergegeven geheugenoverhead is het percentage geheugen dat door replicatie wordt gebruikt, in vergelijking met het totale geïnstalleerde geheugen op de Hyper-V-server.

![Secundaire resultaten](./media/hyper-v-vmm-performance-results/IC744916.png)

Het aantal I/O-bewerkingen op de herstelsite is een functie van het aantal schrijfbewerkingen op de primaire site. Laten we eens kijken naar de totale I/O-bewerkingen op de herstelsite in vergelijking met de totale I/O-bewerkingen en schrijfbewerkingen op de primaire site. Uit de grafieken blijkt dat de totale IOPS op de herstelsite

* Ongeveer 1,5 keer de schrijf IOPS op de primaire.
* Ongeveer 37% van de totale IOPS op de primaire site.

![Secundaire resultaten](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secundaire resultaten](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Effect op het gebruik van het netwerk

Er werd gemiddeld 275 Mb per seconde netwerkbandbreedte gebruikt tussen de primaire en herstelknooppunten (met compressie ingeschakeld), tegen een bestaande bandbreedte van 5 Gb per seconde.

![Resultaten netwerkgebruik](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Effect op vm-prestaties

Een belangrijke overweging is de impact van replicatie op productieworkloads die op de virtuele machines worden uitgevoerd. Als de primaire site voldoende is ingericht voor replicatie, mag er geen invloed zijn op de workloads. Het lichtgewicht trackingmechanisme van Hyper-V Replica zorgt ervoor dat workloads die in de virtuele machines worden uitgevoerd, niet worden beïnvloed tijdens replicatie met steady-state. Dit wordt geïllustreerd in de volgende grafieken.

Deze grafiek toont IOPS uitgevoerd door virtuele machines met verschillende workloads, voor en na replicatie is ingeschakeld. U vaststellen dat er geen verschil tussen de twee.

![Resultaten van het replicaeffect](./media/hyper-v-vmm-performance-results/IC744920.png)

In de volgende grafiek ziet u de doorvoer van virtuele machines met verschillende workloads, voor en na replicatie is ingeschakeld. U vaststellen dat replicatie geen significante impact heeft.

![Resultaten replica-effecten](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Conclusie

De resultaten tonen duidelijk aan dat Site Recovery, in combinatie met Hyper-V Replica, schaalt goed met minimale overhead voor een groot cluster. Siteherstel biedt eenvoudige implementatie, replicatie, beheer en monitoring. Hyper-V Replica biedt de benodigde infrastructuur voor succesvolle replicatieschaling. 

## <a name="test-environment-details"></a>Details van de testomgeving

### <a name="primary-site"></a>Primaire site

* De primaire site heeft een cluster met vijf Hyper-V-servers, met 470 virtuele machines.
* De VM's voeren verschillende workloads uit en hebben allemaal siteherstelbeveiliging ingeschakeld.
* Opslag voor het clusterknooppunt wordt geleverd door een iSCSI SAN. Model – Hitachi HUS130.
* Elke clusterserver heeft vier netwerkkaarten (NIC's) van elk één Gbps.
* Twee van de netwerkkaarten zijn verbonden met een iSCSI-privénetwerk en twee zijn verbonden met een extern bedrijfsnetwerk. Een van de externe netwerken is alleen gereserveerd voor clustercommunicatie.

![Primaire hardwarevereisten](./media/hyper-v-vmm-performance-results/IC744922.png)

| server | RAM | Model | Processor | Aantal verwerkers | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servers in cluster: <br />ESTLAB-HOST11 ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 heeft 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Functie Hyper-V |
| VMM-server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Secundaire site

* De secundaire site heeft een failovercluster met zes node.
* Opslag voor het clusterknooppunt wordt geleverd door een iSCSI SAN. Model – Hitachi HUS130.

![Primaire hardwarespecificatie](./media/hyper-v-vmm-performance-results/IC744923.png)

| server | RAM | Model | Processor | Aantal verwerkers | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2,30 GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Functie Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Functie Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Functie Hyper-V |
| VMM-server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Serverworkloads

* Voor testdoeleinden hebben we workloads gekozen die vaak worden gebruikt in bedrijfsscenario's van klanten.
* We gebruiken [IOMeter](http://www.iometer.org) met de werkbelasting kenmerk samengevat in de tabel voor simulatie.
* Alle IOMeter-profielen zijn ingesteld om willekeurige bytes te schrijven om worst-case schrijfpatronen voor workloads te simuleren.

| Workload | I/O-grootte (KB) | % Toegang | %Lezen | Uitstekende I/O's | I/O-patroon |
| --- | --- | --- | --- | --- | --- |
| Bestandsserver |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Alle 100% willekeurig |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% willekeurig<br />100% sequentiële |
| Exchange |32 |100% |67% |8 |100% willekeurig |
| Werkstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Beide 100% willekeurig |
| Webserver |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Alle 75% willekeurig |

### <a name="vm-configuration"></a>VM-configuratie

* 470 VM's op het primaire cluster.
* Alle VM's met VHDX-schijf.
* VM's met workloads die in de tabel zijn samengevat. Alle zijn gemaakt met VMM-sjablonen.

| Workload | # VM's | Minimaal RAM (GB) | Maximaal RAM (GB) | Logische schijfgrootte (GB) per VM | Maximale IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Bestandsserver |50 |1 |2 |552 |22 |
| Vdi |149 |.5 |1 |80 |6 |
| Webserver |149 |.5 |1 |80 |6 |
| Totaal |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Instellingen voor siteherstel

* Site Recovery is geconfigureerd voor on-premises on-premises bescherming
* De VMM-server heeft vier clouds geconfigureerd, met de Hyper-V-clusterservers en hun VM's.

| Primaire VMM-cloud | Beveiligde virtuele machines | Replicatiefrequentie | Extra herstelpunten |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minuten |Geen |
| PrimaryCloudRpo30s |47 |30 seconden |Geen |
| Primair CloudRpo30sArp1 |47 |30 seconden |1 |
| PrimaryCloudRpo5m |235 |5 minuten |Geen |

### <a name="performance-metrics"></a>Metrische gegevens voor prestaties

De tabel geeft een overzicht van de prestatiestatistieken en -tellers die in de implementatie zijn gemeten.

| Gegevens | Prestatiemeteritem |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Beschikbaar geheugen |\Geheugen\Beschikbare mbytes |
| IOPS |\PhysicalDisk(_Total)\Schijfoverdrachten per seconde |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage\<Device( VHD>)\Leesbewerkingen/Sec |
| VM schrijven (IOPS) bewerkingen/sec |\Hyper-V Virtual Storage\<Device( VHD>)\Schrijfbewerkingen/S |
| VM-leesdoorvoer |\Hyper-V Virtual Storage\<Device( VHD>)\Lees Bytes/sec |
| VM-schrijfdoorvoer |\Hyper-V Virtual Storage\<Device( VHD>)\Schrijf bytes/sec |

## <a name="next-steps"></a>Volgende stappen

[Replicatie instellen](hyper-v-vmm-disaster-recovery.md)
