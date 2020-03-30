---
title: Ondersteunde scenario's voor SAP HANA op Azure (Grote exemplaren)| Microsoft Documenten
description: Ondersteunde scenario's en hun architectuurdetails voor SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617168"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Ondersteunde scenario's voor HANA Large Instances
In dit artikel worden de ondersteunde scenario's en architectuurdetails voor HANA Large Instances (HLI) beschreven.

>[!NOTE]
>Als het vereiste scenario niet in dit artikel wordt vermeld, neemt u contact op met het Microsoft Service Management-team om uw vereisten te beoordelen.
Voordat u de HLI-eenheid instelt, valideert u het ontwerp met SAP of uw serviceimplementatiepartner.

## <a name="terms-and-definitions"></a>Voorwaarden en definities
Laten we eens de termen en definities begrijpen die in dit artikel worden gebruikt:

- **SID**: Een systeem-id voor het HANA-systeem
- **HLI**: Hana Large Instances
- **DR**: Herstel na noodgevallen
- **Normale DR:** Een systeeminstelling met alleen een speciale resource voor DR-doeleinden
- **Multifunctionele DR:** een DR-sitesysteem dat is geconfigureerd om een niet-productieomgeving te gebruiken naast een productie-instantie die is geconfigureerd voor een DR-gebeurtenis 
- **Single-SID**: Een systeem met één exemplaar geïnstalleerd
- **Multi-SID**: Een systeem met meerdere exemplaren geconfigureerd; ook wel een MCOS-omgeving genoemd
- **HSR**: SAP HANA System Replication

## <a name="overview"></a>Overzicht
HANA Large Instances ondersteunt verschillende architecturen om u te helpen uw zakelijke vereisten te vervullen. De volgende secties behandelen de architecturale scenario's en hun configuratiedetails. 

Het afgeleide architectuurontwerp is puur vanuit een infrastructuurperspectief en u moet SAP of uw implementatiepartners raadplegen voor de HANA-implementatie. Als uw scenario's niet in dit artikel worden vermeld, neemt u contact op met het Microsoft-accountteam om de architectuur te bekijken en een oplossing voor u af te leiden.

> [!NOTE]
> Deze architecturen zijn volledig in overeenstemming met het ONTWERP (Tailored Data Integration) (TDI) en worden ondersteund door SAP.

In dit artikel worden de details van de twee componenten in elke ondersteunde architectuur beschreven:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Elke ingerichte server wordt vooraf geconfigureerd met sets Ethernet-interfaces. De Ethernet-interfaces die op elke HLI-eenheid zijn geconfigureerd, zijn onderverdeeld in vier typen:

- **A**: Gebruikt voor of door toegang tot de klant.
- **B**: Gebruikt voor knooppuntcommunicatie. Deze interface is geconfigureerd op alle servers (ongeacht de gevraagde topologie) maar wordt alleen gebruikt voor scale-outscenario's.
- **C**: Gebruikt voor node-to-storage-connectiviteit.
- **D**: Gebruikt voor node-to-iSCSI-apparaatverbinding voor de STONITH-installatie. Deze interface is alleen geconfigureerd wanneer een HSR-installatie wordt aangevraagd.  

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Knooppunt-naar-knooppunt|
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Stonith |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Knooppunt-naar-knooppunt|
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Stonith |

U kiest de interface op basis van de topologie die is geconfigureerd op de HLI-eenheid. Interface 'B' is bijvoorbeeld ingesteld voor node-to-nodecommunicatie, wat handig is wanneer u een scale-out topologie hebt geconfigureerd. Deze interface wordt niet gebruikt voor configuraties met één knooppunt, opschan. Bekijk uw vereiste scenario's (later in dit artikel) voor meer informatie over interfacegebruik. 

Indien nodig u zelf extra NIC-kaarten definiëren. De configuraties van bestaande NIC's kunnen echter *niet* worden gewijzigd.

>[!NOTE]
>Mogelijk vindt u extra interfaces die fysieke interfaces of binding zijn. U dient alleen de eerder genoemde interfaces voor uw use case in overweging te nemen. Alle anderen kunnen worden genegeerd.

De verdeling voor eenheden met twee toegewezen IP-adressen moet eruit zien als:

- Ethernet "A" moet een toegewezen IP-adres hebben dat binnen het IP-pooladresbereik van de server valt dat u bij Microsoft hebt ingediend. Dit IP-adres moet worden bewaard in de */etc/hosts directory* van het besturingssysteem.

- Ethernet "C" moet een toegewezen IP-adres hebben dat wordt gebruikt voor communicatie met NFS. Dit adres hoeft *niet* te worden onderhouden in de *map etc/hosts* om verkeer van instance tot instantie binnen de tenant toe te staan.

Voor HANA System Replication of HANA scale-out implementatie is een bladeconfiguratie met twee toegewezen IP-adressen niet geschikt. Als u slechts twee toegewezen IP-adressen hebt en u een dergelijke configuratie wilt implementeren, neemt u contact op met SAP HANA op Azure Service Management. Ze kunnen u een derde IP-adres toewijzen in een derde VLAN. Voor HANA Large Instances-eenheden met drie toegewezen IP-adressen op drie NIC-poorten zijn de volgende gebruiksregels van toepassing:

- Ethernet "A" moet een toegewezen IP-adres hebben dat buiten het IP-pooladresbereik van de server valt dat u bij Microsoft hebt ingediend. Dit IP-adres mag niet worden bewaard in de *map etc/hosts* van het besturingssysteem.

- Ethernet "B" moet uitsluitend worden onderhouden in de *etc / hosts* directory voor communicatie tussen de verschillende instanties. Dit zijn de IP-adressen die in scale-out HANA-configuraties moeten worden onderhouden als de IP-adressen die HANA gebruikt voor de internodeconfiguratie.

- Ethernet "C" moet een toegewezen IP-adres hebben dat wordt gebruikt voor communicatie naar NFS-opslag. Dit type adres mag niet worden onderhouden in de *map etc/hosts.*

- Ethernet "D" moet uitsluitend worden gebruikt voor toegang tot STONITH-apparaten voor pacemaker. Deze interface is vereist wanneer u HANA-systeemreplicatie configureert en een automatische failover van het besturingssysteem wilt bereiken met behulp van een SBD-apparaat.


### <a name="storage"></a>Storage
Opslag is vooraf geconfigureerd op basis van de gevraagde topologie. De volumegroottes en bevestigingspunten variëren afhankelijk van het aantal servers, het aantal SKU's en de geconfigureerde topologie. Bekijk voor meer informatie uw vereiste scenario's (later in dit artikel). Als u meer opslagruimte nodig hebt, u deze in stappen van 1 TB aanschaffen.

>[!NOTE]
>Het mount point /usr/sap/\<SID> is een symbolische link naar het /hana/shared mount point.


## <a name="supported-scenarios"></a>Ondersteunde scenario's

De architectuurdiagrammen in de volgende secties gebruiken de volgende aantekeningen:

[![Tabel met architectuurdiagrammen](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Hier zijn de ondersteunde scenario's:

* Eén knooppunt met één SID
* McOS met één knooppunt
* Eén knooppunt met DR (normaal)
* Eén knooppunt met DR (multifunctioneel)
* HSR met STONITH
* HSR met DR (normaal/multifunctioneel) 
* Host automatische failover (1+1) 
* Scale-out met stand-by
* Scale-out zonder stand-by
* Scale-out met DR

## <a name="single-node-with-one-sid"></a>Eén knooppunt met één SID

Deze topologie ondersteunt één knooppunt in een scale-upconfiguratie met één SID.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Eén knooppunt met één SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|/hana/shared/SID | HANA-installatie | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden | 
|/hana/logbackups/SID | Logboeken opnieuw maken |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.

## <a name="single-node-mcos"></a>McOS met één knooppunt

Deze topologie ondersteunt één knooppunt in een scale-upconfiguratie met meerdere ID's.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![McOS met één knooppunt](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|/hana/shared/SID1 | HANA installatie voor SID1 | 
|/hana/data/SID1/mnt00001 | Installatie van gegevensbestanden voor SID1 | 
|/hana/log/SID1/mnt00001 | Installatie van logbestanden voor SID1 | 
|/hana/logbackups/SID1 | Logboeken opnieuw maken voor SID1 |
|/hana/shared/SID2 | HANA installatie voor SID2 | 
|/hana/data/SID2/mnt00001 | Installatie van gegevensbestanden voor SID2 | 
|/hana/log/SID2/mnt00001 | Installatie van logbestanden voor SID2 | 
|/hana/logbackups/SID2 | Logboeken opnieuw maken voor SID2 |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- De verdeling van de volumegrootte is gebaseerd op de databasegrootte in het geheugen. Zie [Overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)voor meer informatie over welke databaseformaten in het geheugen worden ondersteund in een multi-SID-omgeving.

## <a name="single-node-with-dr-using-storage-replication"></a>Eén knooppunt met DR met opslagreplicatie
 
Deze topologie ondersteunt één knooppunt in een scale-upconfiguratie met één of meerdere SID's, met op opslag gebaseerde replicatie naar de DR-site voor een primaire SID. In het diagram wordt slechts een single-SID-systeem weergegeven op de primaire site, maar MCOS-systemen worden ook ondersteund.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Eén knooppunt met DR met opslagreplicatie](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|/hana/shared/SID | HANA installatie voor SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor SID | 
|/hana/logbackups/SID | Logboeken opnieuw maken voor SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Voor MCOS: De verdeling van de volumegrootte is gebaseerd op de databasegrootte in het geheugen. Zie [Overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)voor meer informatie over welke databaseformaten in het geheugen worden ondersteund in een multi-SID-omgeving.
- Op de DR-site: De volumes en bevestigingspunten zijn geconfigureerd (gemarkeerd als "Vereist voor HANA-installatie") voor de productie-HANA-instantie-installatie op de DR HLI-eenheid. 
- Op de DR-site: de gegevens, logboekback-ups en gedeelde volumes (gemarkeerd als 'Opslagreplicatie') worden gerepliceerd via momentopname van de productiesite. Deze volumes worden alleen tijdens failover gemonteerd. Zie [Failoverprocedure voor noodgevallen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)voor meer informatie .
- Het opstartvolume voor *de klasse SKU Type I* wordt gerepliceerd naar het DR-knooppunt.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Eén knooppunt met DR (multifunctioneel) met opslagreplicatie
 
Deze topologie ondersteunt één knooppunt in een scale-upconfiguratie met één of meerdere SID's, met op opslag gebaseerde replicatie naar de DR-site voor een primaire SID. In het diagram wordt slechts een single-SID-systeem weergegeven op de primaire site, maar multi-SID (MCOS) systemen worden ook ondersteund. Op de DR-site wordt de HLI-eenheid gebruikt voor de QA-instantie terwijl de productiebewerkingen vanaf de primaire site worden uitgevoerd. Tijdens DR failover (of failover test) wordt de QA-instantie op de DR-site naar beneden gehaald.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Eén knooppunt met DR (multifunctioneel) met opslagreplicatie](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de primaire locatie**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/shared/QA-SID | HANA installatie voor QA SID | 
|/hana/data/QA-SID/mnt00001 | Installatie van gegevensbestanden voor QA SID | 
|/hana/log/QA-SID/mnt00001 | Installatie van logbestanden voor QA SID |
|/hana/logbackups/QA-SID | Opnieuw logboeken voor QA SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Voor MCOS: De verdeling van de volumegrootte is gebaseerd op de databasegrootte in het geheugen. Zie [Overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)voor meer informatie over welke databaseformaten in het geheugen worden ondersteund in een multi-SID-omgeving.
- Op de DR-site: De volumes en bevestigingspunten zijn geconfigureerd (gemarkeerd als "Vereist voor HANA-installatie") voor de productie-HANA-instantie-installatie op de DR HLI-eenheid. 
- Op de DR-site: de gegevens, logboekback-ups en gedeelde volumes (gemarkeerd als 'Opslagreplicatie') worden gerepliceerd via momentopname van de productiesite. Deze volumes worden alleen tijdens failover gemonteerd. Zie [Failoverprocedure voor noodgevallen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)voor meer informatie . 
- Op de DR-site: de gegevens, logboekback-ups, logboeken en gedeelde volumes voor QA (gemarkeerd als 'QA-instantie-installatie') zijn geconfigureerd voor de QA-instantie-installatie.
- Het opstartvolume voor *de klasse SKU Type I* wordt gerepliceerd naar het DR-knooppunt.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR met STONITH voor hoge beschikbaarheid
 
Deze topologie ondersteunt twee knooppunten voor de HANA-systeemreplicatieconfiguratie. Deze configuratie wordt alleen ondersteund voor afzonderlijke HANA-exemplaren op een knooppunt. Dit betekent dat MCOS-scenario's *niet* worden ondersteund.

> [!NOTE]
> Vanaf december 2019 wordt deze architectuur alleen ondersteund voor het SUSE-besturingssysteem.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![HSR met STONITH voor hoge beschikbaarheid](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Gebruikt voor STONITH |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Gebruikt voor STONITH |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op het primaire knooppunt**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|**Op het secundaire knooppunt**|
|/hana/shared/SID | HANA installatie voor secundaire SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor secundaire SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor secundaire SID | 
|/hana/logbackups/SID | Logboeken opnieuw maken voor secundaire SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Voor MCOS: De verdeling van de volumegrootte is gebaseerd op de databasegrootte in het geheugen. Zie [Overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)voor meer informatie over welke databaseformaten in het geheugen worden ondersteund in een multi-SID-omgeving.
- STONITH: Een SBD is geconfigureerd voor de STONITH setup. Het gebruik van STONITH is echter optioneel.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Hoge beschikbaarheid met HSR en DR met opslagreplicatie
 
Deze topologie ondersteunt twee knooppunten voor de HANA-systeemreplicatieconfiguratie. Zowel normale als multifunctionele DRs worden ondersteund. Deze configuraties worden alleen ondersteund voor afzonderlijke HANA-exemplaren op een knooppunt. Dit betekent dat MCOS-scenario's *niet* worden ondersteund met deze configuraties.

In het diagram wordt een multifunctioneel scenario weergegeven op de DR-site, waar de HLI-eenheid wordt gebruikt voor de QA-instantie terwijl de productiebewerkingen vanaf de primaire site worden uitgevoerd. Tijdens DR failover (of failover test) wordt de QA-instantie op de DR-site naar beneden gehaald. 

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Hoge beschikbaarheid met HSR en DR met opslagreplicatie](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Gebruikt voor STONITH |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Gebruikt voor STONITH |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op het primaire knooppunt op de primaire locatie**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|**Op het secundaire knooppunt op de primaire locatie**|
|/hana/shared/SID | HANA installatie voor secundaire SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor secundaire SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor secundaire SID | 
|/hana/logbackups/SID | Logboeken opnieuw maken voor secundaire SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/shared/QA-SID | HANA installatie voor QA SID | 
|/hana/data/QA-SID/mnt00001 | Installatie van gegevensbestanden voor QA SID | 
|/hana/log/QA-SID/mnt00001 | Installatie van logbestanden voor QA SID |
|/hana/logbackups/QA-SID | Opnieuw logboeken voor QA SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Voor MCOS: De verdeling van de volumegrootte is gebaseerd op de databasegrootte in het geheugen. Zie [Overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)voor meer informatie over welke databaseformaten in het geheugen worden ondersteund in een multi-SID-omgeving.
- STONITH: Een SBD is geconfigureerd voor de STONITH setup. Het gebruik van STONITH is echter optioneel.
- Op de DR-site: *twee sets opslagvolumes zijn vereist* voor primaire en secundaire nodereplicatie.
- Op de DR-site: De volumes en bevestigingspunten zijn geconfigureerd (gemarkeerd als "Vereist voor HANA-installatie") voor de productie-HANA-instantie-installatie op de DR HLI-eenheid. 
- Op de DR-site: de gegevens, logboekback-ups en gedeelde volumes (gemarkeerd als 'Opslagreplicatie') worden gerepliceerd via momentopname van de productiesite. Deze volumes worden alleen tijdens failover gemonteerd. Zie [Failoverprocedure voor noodgevallen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)voor meer informatie . 
- Op de DR-site: de gegevens, logboekback-ups, logboeken en gedeelde volumes voor QA (gemarkeerd als 'QA-instantie-installatie') zijn geconfigureerd voor de QA-instantie-installatie.
- Het opstartvolume voor *de klasse SKU Type I* wordt gerepliceerd naar het DR-knooppunt.


## <a name="host-auto-failover-11"></a>Host automatische failover (1+1)
 
Deze topologie ondersteunt twee knooppunten in een hostautomatische failoverconfiguratie. Er is een knooppunt met een master / werknemer rol en een ander als stand-by. *SAP ondersteunt dit scenario alleen voor S/4 HANA.* Zie [OSS note 2408419 - SAP S/4HANA - Multi-Node Support](https://launchpad.support.sap.com/#/notes/2408419)voor meer informatie.



### <a name="architecture-diagram"></a>Architectuurdiagram  

![Host automatische failover (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communicatie tussen knooppunt en knooppunt |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Communicatie tussen knooppunt en knooppunt |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de master- en stand-byknooppunten**|
|/hana/gedeeld | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |



### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Op stand-by: De volumes en bevestigingspunten zijn geconfigureerd (gemarkeerd als "Vereist voor HANA-installatie") voor de HANA-instantieinstallatie op de stand-by-eenheid.
 

## <a name="scale-out-with-standby"></a>Scale-out met stand-by
 
Deze topologie ondersteunt meerdere knooppunten in een scale-outconfiguratie. Er is één knooppunt met een hoofdrol, een of meer knooppunten met een werkrol en een of meer knooppunten als stand-by. Er kan echter slechts één hoofdknooppunt op een enkel moment in de tijd zijn.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![Scale-out met stand-by](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communicatie tussen knooppunt en knooppunt |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Communicatie tussen knooppunt en knooppunt |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de hoofd-, werk- en stand-byknooppunten**|
|/hana/gedeeld | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |


## <a name="scale-out-without-standby"></a>Scale-out zonder stand-by
 
Deze topologie ondersteunt meerdere knooppunten in een scale-outconfiguratie. Er is één knooppunt met een hoofdrol en een of meer knooppunten met een werkrol. Er kan echter slechts één hoofdknooppunt op een enkel moment in de tijd zijn.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![Scale-out zonder stand-by](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communicatie tussen knooppunt en knooppunt |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Communicatie tussen knooppunt en knooppunt |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de hoofd- en werkknooppunten**|
|/hana/gedeeld | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Uitschalen met DR met opslagreplicatie
 
Deze topologie ondersteunt meerdere knooppunten in een scale-out met een DR. Zowel normale als multifunctionele DRs worden ondersteund. In het diagram wordt alleen de DR voor één doel weergegeven. U deze topologie aanvragen met of zonder het stand-by node.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![Uitschalen met DR met opslagreplicatie](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communicatie tussen knooppunt en knooppunt |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Communicatie tussen knooppunt en knooppunt |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op het primaire knooppunt**|
|/hana/gedeeld | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|**Op het DR-knooppunt**|
|/hana/gedeeld | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
-  Op de DR-site: De volumes en bevestigingspunten zijn geconfigureerd (gemarkeerd als "Vereist voor HANA-installatie") voor de productie-HANA-instantie-installatie op de DR HLI-eenheid. 
- Op de DR-site: de gegevens, logboekback-ups en gedeelde volumes (gemarkeerd als 'Opslagreplicatie') worden gerepliceerd via momentopname van de productiesite. Deze volumes worden alleen tijdens failover gemonteerd. Zie [Failoverprocedure voor noodgevallen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)voor meer informatie . 
- Het opstartvolume voor *de klasse SKU Type I* wordt gerepliceerd naar het DR-knooppunt.


## <a name="single-node-with-dr-using-hsr"></a>Eén knooppunt met DR met HSR
 
Deze topologie ondersteunt één knooppunt in een scale-upconfiguratie met één SID, met HANA-systeemreplicatie naar de DR-site voor een primaire SID. In het diagram wordt slechts een single-SID-systeem weergegeven op de primaire site, maar multi-SID (MCOS) systemen worden ook ondersteund.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Eén knooppunt met DR met HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI/HSR |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd op zowel de HLI-eenheden (Primair als DR):

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|/hana/shared/SID | HANA installatie voor SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor SID | 
|/hana/logbackups/SID | Logboeken opnieuw maken voor SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Voor MCOS: De verdeling van de volumegrootte is gebaseerd op de databasegrootte in het geheugen. Zie [Overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)voor meer informatie over welke databaseformaten in het geheugen worden ondersteund in een multi-SID-omgeving.
- Het primaire knooppunt synchroniseert met het DR-knooppunt met HANA-systeemreplicatie. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) wordt gebruikt om de ExpressRoute-circuits met elkaar te verbinden om een privénetwerk te maken tussen uw regionale netwerken.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Single node HSR naar DR (kosten geoptimaliseerd) 
 
 Deze topologie ondersteunt één knooppunt in een scale-upconfiguratie met één SID, met HANA-systeemreplicatie naar de DR-site voor een primaire SID. In het diagram wordt slechts een single-SID-systeem weergegeven op de primaire site, maar multi-SID (MCOS) systemen worden ook ondersteund. Op de DR-site wordt een HLI-eenheid gebruikt voor de QA-instantie terwijl de productiebewerkingen vanaf de primaire site worden uitgevoerd. Tijdens DR failover (of failover test) wordt de QA-instantie op de DR-site naar beneden gehaald.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Single node HSR naar DR (kosten geoptimaliseerd)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI/HSR |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de primaire locatie**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|/hana/shared/QA-SID | HANA installatie voor QA SID | 
|/hana/data/QA-SID/mnt00001 | Installatie van gegevensbestanden voor QA SID | 
|/hana/log/QA-SID/mnt00001 | Installatie van logbestanden voor QA SID |
|/hana/logbackups/QA-SID | Opnieuw logboeken voor QA SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Voor MCOS: De verdeling van de volumegrootte is gebaseerd op de databasegrootte in het geheugen. Zie [Overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)voor meer informatie over welke databaseformaten in het geheugen worden ondersteund in een multi-SID-omgeving.
- Op de DR-site: De volumes en bevestigingspunten zijn geconfigureerd (gemarkeerd als "PROD Instance at DR-site") voor de productie-HANA-instantie-installatie op de DR HLI-eenheid. 
- Op de DR-site: de gegevens, logboekback-ups, logboeken en gedeelde volumes voor QA (gemarkeerd als 'QA-instantie-installatie') zijn geconfigureerd voor de QA-instantie-installatie.
- Het primaire knooppunt synchroniseert met het DR-knooppunt met HANA-systeemreplicatie. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) wordt gebruikt om de ExpressRoute-circuits met elkaar te verbinden om een privénetwerk te maken tussen uw regionale netwerken.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Hoge beschikbaarheid en disaster recovery met HSR 
 
 Deze topologie ondersteunt twee knooppunten voor de HANA-systeemreplicatieconfiguratie voor de hoge beschikbaarheid van de lokale regio's. Voor de DR synchroniseert het derde knooppunt op het DR-gebied met de primaire site met behulp van HSR (async-modus). 

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Hoge beschikbaarheid en disaster recovery met HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI/HSR |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de primaire locatie**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Op de DR-site: De volumes en bevestigingspunten zijn geconfigureerd (gemarkeerd als "PROD DR-instantie") voor de productie-HANA-instantie-installatie op de DR HLI-eenheid. 
- Het primaire siteknooppunt synchroniseert met het DR-knooppunt met HANA-systeemreplicatie. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) wordt gebruikt om de ExpressRoute-circuits met elkaar te verbinden om een privénetwerk te maken tussen uw regionale netwerken.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Hoge beschikbaarheid en disaster recovery met HSR (kosten geoptimaliseerd)
 
 Deze topologie ondersteunt twee knooppunten voor de HANA-systeemreplicatieconfiguratie voor de hoge beschikbaarheid van de lokale regio's. Voor de DR synchroniseert het derde knooppunt op het DR-gebied met de primaire site met behulp van HSR (async-modus), terwijl een andere instantie (bijvoorbeeld QA) al opraakt van het DR-knooppunt. 

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Hoge beschikbaarheid en disaster recovery met HSR (kosten geoptimaliseerd)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd, maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI/HSR |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Geconfigureerd, maar niet in gebruik |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op de primaire locatie**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|/hana/shared/QA-SID | HANA installatie voor QA SID | 
|/hana/data/QA-SID/mnt00001 | Installatie van gegevensbestanden voor QA SID | 
|/hana/log/QA-SID/mnt00001 | Installatie van logbestanden voor QA SID |
|/hana/logbackups/QA-SID | Opnieuw logboeken voor QA SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Op de DR-site: De volumes en bevestigingspunten zijn geconfigureerd (gemarkeerd als "PROD DR-instantie") voor de productie-HANA-instantie-installatie op de DR HLI-eenheid. 
- Op de DR-site: de gegevens, logboekback-ups, logboeken en gedeelde volumes voor QA (gemarkeerd als 'QA-instantie-installatie') zijn geconfigureerd voor de QA-instantie-installatie.
- Het primaire siteknooppunt synchroniseert met het DR-knooppunt met HANA-systeemreplicatie. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) wordt gebruikt om de ExpressRoute-circuits met elkaar te verbinden om een privénetwerk te maken tussen uw regionale netwerken.

## <a name="scale-out-with-dr-using-hsr"></a>Scale-out met DR met HSR
 
Deze topologie ondersteunt meerdere knooppunten in een scale-out met een DR. U deze topologie aanvragen met of zonder het stand-by node. Het primaire siteknooppunt synchroniseert met het DR-siteknooppunt met HANA-systeemreplicatie (async-modus).


### <a name="architecture-diagram"></a>Architectuurdiagram  

[![Scale-out met DR met HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
De volgende netwerkinterfaces zijn vooraf geconfigureerd:

| NIC logische interface | SKU-type | Naam met SUSE OS | Naam met RHEL OS | Gebruiksvoorbeeld|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | Client-to-HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Communicatie tussen knooppunt en knooppunt |
| C | TYPE I | eth1.tenant | eno2.tenant | Knooppunt-naar-opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd, maar niet in gebruik |
| A | TYPE II | vlan\<huurderGeen> | team0.tenant | Client-to-HLI/HSR |
| B | TYPE II | vlan\<huurderGeen+2> | team0.tenant+2 | Communicatie tussen knooppunt en knooppunt |
| C | TYPE II | vlan\<huurderGeen+1> | team0.tenant+1 | Knooppunt-naar-opslag |
| D | TYPE II | vlan\<huurderGeen+3> | team0.tenant+3 | Geconfigureerd, maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende bevestigingspunten zijn vooraf geconfigureerd:

| Bevestigingspunt | Gebruiksvoorbeeld | 
| --- | --- |
|**Op het primaire knooppunt**|
|/hana/gedeeld | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |
|**Op het DR-knooppunt**|
|/hana/gedeeld | HANA installatie voor productie SID | 
|/hana/data/SID/mnt00001 | Installatie van gegevensbestanden voor productie SID | 
|/hana/log/SID/mnt00001 | Installatie van logbestanden voor productie SID | 
|/hana/logbackups/SID | Redo-logboeken voor productie SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische link naar /hana/shared/SID.
- Op de DR-site: De volumes en bevestigingspunten zijn geconfigureerd voor de productie HANA instance installatie op de DR HLI unit. 
- Het primaire siteknooppunt synchroniseert met het DR-knooppunt met HANA-systeemreplicatie. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) wordt gebruikt om de ExpressRoute-circuits met elkaar te verbinden om een privénetwerk te maken tussen uw regionale netwerken.


## <a name="next-steps"></a>Volgende stappen
- [Infrastructuur en connectiviteit](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) voor HANA Large Instances
- [Hoge beschikbaarheid en noodherstel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) voor HANA Large Instances
