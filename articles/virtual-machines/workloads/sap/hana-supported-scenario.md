---
title: Ondersteunde scenario's SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Scenario's die worden ondersteund en de architectuur Details voor SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f17e447f26ae4f7573941fc0c578a918ff45a145
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101221"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Ondersteunde scenario's voor HANA grote instanties
In dit document worden de ondersteunde scenario's en de architectuur Details van de HANA-grote instanties (HLI) beschreven.

>[!NOTE]
>Als uw vereiste scenario hier niet wordt vermeld, neemt u contact op met het micro soft service management-team om uw vereisten te beoordelen.
Voordat u verdergaat met het inrichten van de HLI-eenheid, valideert u het ontwerp met SAP of uw service-implementatie partner.

## <a name="terms-and-definitions"></a>Voor waarden en definities
Laten we de termen en definities begrijpen die in het document worden gebruikt.

- SID: Systeem-id voor HANA-systeem.
- HLI Hana grote instanties.
- SRS Een nood herstel site.
- Normaal DR: Een systeem installatie met een speciale resource voor alleen DR-doel einden.
- Multifunctionele DR: Een systeem op een DR-site dat is geconfigureerd voor het gebruik van een niet-productie omgeving en een productie-exemplaar dat is geconfigureerd om te worden gebruikt voor DR gebeurtenis. 
- Enkele SID:  Er is een systeem met één exemplaar geïnstalleerd.
- Meervoudige SID: Een systeem waarop meerdere instanties zijn geconfigureerd. Ook wel een MCOS omgeving genoemd.


## <a name="overview"></a>Overzicht
De HANA grote instanties ondersteunen de verschillende architecturen om uw bedrijfs vereisten te bereiken. In de volgende lijst worden de scenario's en hun configuratie details beschreven. 

Het afgeleide architectuur ontwerp is uitsluitend afkomstig uit het oogpunt van de infra structuur en u moet SAP of uw implementatie partners raadplegen voor de HANA-implementatie. Als uw scenario's niet worden vermeld, neemt u contact op met het Microsoft-account team om de architectuur te beoordelen en een oplossing voor u af te leiden.

**Deze architecturen zijn volledig compatibel met het TDI-ontwerp (aangepaste gegevens integratie) en ondersteund door SAP.**

In dit document worden de details van de twee onderdelen in elke ondersteunde architectuur beschreven:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Elke server die wordt ingericht, is vooraf geconfigureerd met de sets van Ethernet-interfaces. Hier volgen de details van de Ethernet-interfaces die op elke HLI-eenheid zijn geconfigureerd.

- **A**: Deze interface wordt gebruikt voor/door de client toegang.
- **B**: Deze interface wordt gebruikt voor de communicatie tussen knoop punten. Deze interface is geconfigureerd op alle servers (onafhankelijk van de aangevraagde topologie), maar wordt alleen gebruikt voor de 
- scale-out scenario's.
- **C**: Deze interface wordt gebruikt voor het knoop punt voor de connectiviteit van de opslag.
- **D**: Deze interface wordt gebruikt voor het knoop punt voor de verbinding met een ISCSI-apparaat voor STONITH-installatie. Deze interface wordt alleen geconfigureerd wanneer de HSR-installatie wordt aangevraagd.  

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Knoop punt naar knoop punt |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | STONITH |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Knoop punt naar knoop punt |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

U kunt de interfaces gebruiken op basis van de topologie die is geconfigureerd op de HLI-eenheid. Bijvoorbeeld: de interface "B" is ingesteld voor het knoop punt communicatie van knoop punten. Dit is handig wanneer u een scale-out-topologie hebt geconfigureerd. In het geval van een configuratie met één knoop punt, wordt deze interface niet gebruikt. Bekijk de vereiste scenario's (verderop in dit document) voor meer informatie over het gebruik van de interface. 

Als dat nodig is, kunt u extra NIC-kaarten opgeven. De configuratie van de bestaande Nic's kan echter niet worden gewijzigd.

>[!NOTE]
>U kunt nog steeds aanvullende interfaces vinden die fysieke interfaces of bindingen zijn. U moet rekening houden met de hierboven vermelde interfaces voor uw gebruikte case, rest kan worden genegeerd/of niet worden getemperd met.

De distributie voor eenheden met twee IP-adressen moet er als volgt uitzien:

- Voor Ethernet ' A ' moet een IP-adres worden toegewezen dat buiten het adres bereik van de server-IP-groep valt die u hebt verzonden naar micro soft. Dit IP-adres wordt gebruikt voor het onderhouden van de bestand/etc/hosts van het besturings systeem.

- Voor Ethernet ' C ' moet een IP-adres worden toegewezen dat wordt gebruikt voor communicatie met NFS. Deze adressen hoeven daarom **niet** in etc/hosts te worden bewaard om instantie verkeer binnen de Tenant toe te staan.

Voor implementatie cases van HANA-systeem replicatie of HANA-uitschalen is een Blade configuratie met twee toegewezen IP-adressen niet geschikt. Als er slechts twee IP-adressen zijn toegewezen en u een dergelijke configuratie wilt implementeren, neemt u contact op met SAP HANA op Azure service management om een derde IP-adres in een derde VLAN aan te vragen. Voor HANA grote instantie-eenheden met drie IP-adressen die zijn toegewezen op drie NIC-poorten, gelden de volgende gebruiks regels:

- Voor Ethernet ' A ' moet een IP-adres worden toegewezen dat buiten het adres bereik van de server-IP-groep valt die u hebt verzonden naar micro soft. Dit IP-adres mag daarom niet worden gebruikt voor het onderhouden van de bestand/etc/hosts van het besturings systeem.

- Ethernet "B" mag alleen worden gebruikt om te worden onderhouden in etc/hosts voor communicatie tussen de verschillende exemplaren. Deze adressen zijn ook de IP-adressen die moeten worden onderhouden in scale-out HANA-configuraties, omdat IP-adressen HANA worden gebruikt voor de configuratie tussen knoop punten.

- Voor Ethernet ' C ' moet een IP-adres worden toegewezen dat wordt gebruikt voor communicatie met NFS-opslag. Dit type adressen mag daarom niet worden onderhouden in etc/hosts.

- Ethernet "D" mag alleen worden gebruikt voor toegang tot STONITH-apparaat voor pacemaker. Deze interface is vereist wanneer u HANA System Replication (HSR) configureert en automatische failover wilt uitvoeren op het besturings systeem met behulp van een op SBD gebaseerd apparaat.


### <a name="storage"></a>Storage
Opslag is vooraf geconfigureerd op basis van de aangevraagde topologie. De grootte en koppel punt van het volume kunnen variëren op basis van het aantal servers, Sku's en topologie dat is geconfigureerd. Bekijk de vereiste scenario's (verderop in dit document) voor meer informatie. Als er meer opslag ruimte nodig is, kunt u deze kopen in één slag van 1 TB.

>[!NOTE]
>De koppel punt/usr/sap/\<sid > is een symbolische koppeling naar de/Hana/Shared koppel punt.


## <a name="supported-scenarios"></a>Ondersteunde Scenario's

In de architectuur diagrammen worden de volgende notaties gebruikt voor de afbeeldingen:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

De volgende lijst bevat de ondersteunde scenario's:

1. Eén knoop punt met één SID
2. MCOS met één knoop punt
3. Eén knoop punt met DR (normaal)
4. Eén knoop punt met DR (meerdere doel einden)
5. HSR met STONITH
6. HSR met DR (normaal/Multipurpose) 
7. Automatische failover van host (1 + 1) 
8. Uitschalen met stand-by
9. Uitschalen zonder stand-by
10. Uitschalen met DR



## <a name="1-single-node-with-one-sid"></a>1. Eén knoop punt met één SID

Deze topologie ondersteunt één knoop punt in een configuratie met één SID.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Single-node-with-one-SID. png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd maar niet in gebruik |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|/hana/shared/SID | HANA-installatie | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren | 
|/hana/log/SID/mnt00001 | Installatie van logboek bestanden | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. MCOS met één knoop punt

Deze topologie ondersteunt één knoop punt in een configuratie met meerdere Sid's voor omhoog schalen.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd maar niet in gebruik |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|/hana/shared/SID1 | HANA-installatie voor SID1 | 
|/hana/data/SID1/mnt00001 | Gegevens bestanden installeren voor SID1 | 
|/hana/log/SID1/mnt00001 | Logboek bestanden installeren voor SID1 | 
|/hana/logbackups/SID1 | Logboeken voor SID1 opnieuw uitvoeren |
|/hana/shared/SID2 | HANA-installatie voor SID2 | 
|/hana/data/SID2/mnt00001 | Gegevens bestanden installeren voor SID2 | 
|/hana/log/SID2/mnt00001 | Logboek bestanden installeren voor SID2 | 
|/hana/logbackups/SID2 | Logboeken voor SID2 opnieuw uitvoeren |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Distributie van volume grootte is gebaseerd op de grootte van de data base in het geheugen. Raadpleeg de sectie [overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund met multisid-omgeving.

## <a name="3-single-node-with-dr-normal"></a>3. Eén knoop punt met DR (normaal)
 
Deze topologie ondersteunt één knoop punt in een schaal bare configuratie met een of meer Sid's met de opslag replicatie op de DR-site voor een primaire SID. In het diagram wordt slechts één SID op de primaire site weer gegeven, maar multisid (MCOS) wordt ook ondersteund.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Single-Node-with-Dr. png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd maar niet in gebruik |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|/hana/shared/SID | HANA-installatie voor SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren op SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: Distributie van volume grootte is gebaseerd op de grootte van de data base in het geheugen. Raadpleeg de sectie [overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund met multisid-omgeving.
- Op DR: De volumes en mountpoints worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het productie HANA-exemplaar op de DR/HLI-eenheid. 
- Op DR: De gegevens, logbackups en gedeelde volumes (gemarkeerd als ' opslag replicatie ') worden gerepliceerd via de moment opname van de productie site. Deze volumes worden alleen tijdens de failover-tijd gekoppeld. Lees voor meer informatie de procedure voor het herstellen van de [nood herstel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) document voor meer informatie.
- Het opstart volume voor de **SKU type I-klasse** wordt gerepliceerd naar een Dr-knoop punt.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Eén knoop punt met DR (meerdere doel einden)
 
Deze topologie ondersteunt één knoop punt in een schaal bare configuratie met een of meer Sid's met de opslag replicatie op de DR-site voor een primaire SID. In het diagram wordt slechts één SID op de primaire site weer gegeven, maar multisid (MCOS) wordt ook ondersteund. Op de DR-site wordt HLI-eenheid gebruikt voor QA-instantie terwijl de productie bewerkingen vanaf de primaire site worden uitgevoerd. Op het moment van de DR-failover (of failover-test) wordt het exemplaar van QA op de DR-site offline gezet.

### <a name="architecture-diagram"></a>Architectuurdiagram  

![Single-Node-with-Dr-Multipurpose. png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd maar niet in gebruik |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|**Op de primaire site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 
|/hana/shared/QA-SID | HANA-installatie voor QA-SID | 
|/hana/data/QA-SID/mnt00001 | Gegevens bestanden installeren voor QA-SID | 
|/hana/log/QA-SID/mnt00001 | Logboek bestanden installeren voor QA-SID |
|/hana/logbackups/QA-SID | Logboeken opnieuw voor QA-SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: Distributie van volume grootte is gebaseerd op de grootte van de data base in het geheugen. Raadpleeg de sectie [overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund met multisid-omgeving.
- Op DR: De volumes en mountpoints worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het productie HANA-exemplaar op de DR/HLI-eenheid. 
- Op DR: De gegevens, logbackups en gedeelde volumes (gemarkeerd als ' opslag replicatie ') worden gerepliceerd via de moment opname van de productie site. Deze volumes worden alleen tijdens de failover-tijd gekoppeld. Lees voor meer informatie de procedure voor het herstellen van de [nood herstel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) document voor meer informatie. 
- Op DR: De gegevens, logbackups, logboek, gedeelde volumes voor QA (gemarkeerd als ' installatie van QA-instantie ') zijn geconfigureerd voor de installatie van het QA-exemplaar.
- Het opstart volume voor de **SKU type I-klasse** wordt gerepliceerd naar een Dr-knoop punt.

## <a name="5-hsr-with-stonith"></a>5. HSR met STONITH
 
Deze topologie ondersteunt twee knoop punten voor de configuratie van HANA-systeem replicatie (HSR). Deze configuratie wordt alleen ondersteund voor enkelvoudige HANA-instanties op een knoop punt. Betekent dat MCOS-scenario's niet worden ondersteund.

**Vanaf nu wordt deze architectuur alleen ondersteund voor het SUSE-besturings systeem.**


### <a name="architecture-diagram"></a>Architectuurdiagram  

![HSR-with-STONITH. png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Gebruikt voor STONITH |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Gebruikt voor STONITH |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|**Op het primaire knoop punt**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op het secundaire knoop punt**|
|/hana/shared/SID | HANA-installatie voor secundaire SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor secundaire SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor secundaire SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor secundaire SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: Distributie van volume grootte is gebaseerd op de grootte van de data base in het geheugen. Raadpleeg de sectie [overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund met multisid-omgeving.
- STONITH: Er is een SBD geconfigureerd voor de STONITH-installatie. Het gebruik van STONITH is echter optioneel.


## <a name="6-hsr-with-dr"></a>6. HSR met DR
 
Deze topologie ondersteunt twee knoop punten voor de configuratie van HANA-systeem replicatie (HSR). Zowel normaal als multifunctionele DR wordt ondersteund. Deze configuraties worden alleen ondersteund voor enkelvoudige HANA-instanties in een knoop punt. Betekent dat MCOS-scenario's niet worden ondersteund met deze configuraties.

In het diagram wordt het scenario voor meerdere doel einden weer gegeven waar op de DR-site HLI-eenheid wordt gebruikt voor QA-instantie terwijl de productie bewerkingen vanaf de primaire site worden uitgevoerd. Op het moment van de DR-failover (of failover-test) wordt het exemplaar van QA op de DR-site offline gezet. 



### <a name="architecture-diagram"></a>Architectuurdiagram  

![HSR-with-DR. png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Geconfigureerd maar niet in gebruik |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Gebruikt voor STONITH |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Geconfigureerd maar niet in gebruik |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Gebruikt voor STONITH |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|**Op het primaire knoop punt op de primaire site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op het secundaire knoop punt op de primaire site**|
|/hana/shared/SID | HANA-installatie voor secundaire SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor secundaire SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor secundaire SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor secundaire SID |
|**Op de DR-site**|
|/hana/shared/SID | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 
|/hana/shared/QA-SID | HANA-installatie voor QA-SID | 
|/hana/data/QA-SID/mnt00001 | Gegevens bestanden installeren voor QA-SID | 
|/hana/log/QA-SID/mnt00001 | Logboek bestanden installeren voor QA-SID |
|/hana/logbackups/QA-SID | Logboeken opnieuw voor QA-SID |

### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Voor MCOS: Distributie van volume grootte is gebaseerd op de grootte van de data base in het geheugen. Raadpleeg de sectie [overzicht en architectuur](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) voor meer informatie over de grootte van de data base in het geheugen die wordt ondersteund met multisid-omgeving.
- STONITH: Er is een SBD geconfigureerd voor de STONITH-installatie. Het gebruik van STONITH is echter optioneel.
- Op DR: **Er zijn twee sets opslag volumes vereist** voor de replicatie van het primaire en het secundaire knoop punt.
- Op DR: De volumes en mountpoints worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het productie HANA-exemplaar op de DR/HLI-eenheid. 
- Op DR: De gegevens, logbackups en gedeelde volumes (gemarkeerd als ' opslag replicatie ') worden gerepliceerd via de moment opname van de productie site. Deze volumes worden alleen tijdens de failover-tijd gekoppeld. Lees voor meer informatie de procedure voor het herstellen van de [nood herstel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) document voor meer informatie. 
- Op DR: De gegevens, logbackups, logboek, gedeelde volumes voor QA (gemarkeerd als ' installatie van QA-instantie ') zijn geconfigureerd voor de installatie van het QA-exemplaar.
- Het opstart volume voor de **SKU type I-klasse** wordt gerepliceerd naar een Dr-knoop punt.


## <a name="7-host-auto-failover-11"></a>7. Automatische failover van host (1 + 1)
 
Deze topologie ondersteunt twee knoop punten in een automatische failover-configuratie van een host. Er bevindt zich één knoop punt met de rol Master/Worker en als stand-by. **SAP ondersteunt dit scenario alleen voor S/4 HANA.** Raadpleeg de OSS-opmerking "[2408419-SAP S/4HANA-ondersteuning voor meerdere knoop punten](https://launchpad.support.sap.com/#/notes/2408419)" voor meer informatie.



### <a name="architecture-diagram"></a>Architectuurdiagram  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communicatie tussen knoop punten en knoop punten |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd maar niet in gebruik |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communicatie tussen knoop punten en knoop punten |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|**Op de Master-en standby-knoop punten**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |



### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
- Bij stand-by: De volumes en mountpoints worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het HANA-exemplaar op de stand-by-eenheid.
 

## <a name="8-scale-out-with-standby"></a>8. Uitschalen met stand-by
 
Deze topologie ondersteunt meerdere knoop punten in een scale-out configuratie. Er is één knoop punt met een hoofd functie, een of meer knoop punten met de rol Worker en een of meer knoop punten als stand-by. Er kan echter slechts één hoofd knooppunt op een bepaald moment zijn.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![scaleout-nm-standby. png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communicatie tussen knoop punten en knoop punten |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd maar niet in gebruik |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communicatie tussen knoop punten en knoop punten |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|**Op de knoop punten Master, Worker en stand-by**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |


## <a name="9-scale-out-without-standby"></a>9. Uitschalen zonder stand-by
 
Deze topologie ondersteunt meerdere knoop punten in een scale-out configuratie. Er is één knoop punt met een master-functie en één-of modus knooppunten met een werk rollen. Er kan echter slechts één hoofd knooppunt op een bepaald moment zijn.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communicatie tussen knoop punten en knoop punten |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd maar niet in gebruik |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communicatie tussen knoop punten en knoop punten |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|**Op de Master-en worker-knoop punten**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Uitschalen met DR
 
Deze topologie ondersteunt meerdere knoop punten in een scale-out met een DR. Zowel normaal als multifunctionele DR wordt ondersteund. In het diagram wordt alleen DR. 1. U kunt deze topologie aanvragen met of zonder het stand-by-knoop punt.


### <a name="architecture-diagram"></a>Architectuurdiagram  

![scaleout-with-Dr. png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
De volgende netwerk interfaces zijn vooraf geconfigureerd:

| LOGISCHE NETWERK INTERFACE | SKU-TYPE | Naam met SUSE-besturings systeem | Naam met RHEL-besturings systeem | Use-case|
| --- | --- | --- | --- | --- |
| G | TYPE I | eth0.tenant | eno1. Tenant | Client naar HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communicatie tussen knoop punten en knoop punten |
| C | TYPE I | eth1.tenant | eno2. Tenant | Knoop punt naar opslag |
| D | TYPE I | eth4.tenant | eno4.tenant | Geconfigureerd maar niet in gebruik |
| G | TYPE II | vlan\<tenantNo> | team0. Tenant | Client naar HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communicatie tussen knoop punten en knoop punten |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Knoop punt naar opslag |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Geconfigureerd maar niet in gebruik |

### <a name="storage"></a>Storage
De volgende mountpoints zijn vooraf geconfigureerd:

| Koppel punt | Use-case | 
| --- | --- |
|**Op het primaire knoop punt**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 
|/hana/logbackups/SID | Logboeken opnieuw uitvoeren voor productie-SID |
|**Op het DR-knoop punt**|
|/hana/shared | HANA-installatie voor productie-SID | 
|/hana/data/SID/mnt00001 | Gegevens bestanden installeren voor productie-SID | 
|/hana/log/SID/mnt00001 | Logboek bestanden installeren voor productie-SID | 


### <a name="key-considerations"></a>Belangrijkste overwegingen
- /usr/sap/SID is een symbolische koppeling naar/hana/shared/SID.
-  Op DR: De volumes en mountpoints worden geconfigureerd (gemarkeerd als vereist voor HANA-installatie) voor de installatie van het productie HANA-exemplaar op de DR/HLI-eenheid. 
- Op DR: De gegevens, logbackups en gedeelde volumes (gemarkeerd als ' opslag replicatie ') worden gerepliceerd via de moment opname van de productie site. Deze volumes worden alleen tijdens de failover-tijd gekoppeld. Lees voor meer informatie de procedure voor het herstellen van de [nood herstel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) document voor meer informatie. 
- Het opstart volume voor de **SKU type I-klasse** wordt gerepliceerd naar een Dr-knoop punt.


## <a name="next-steps"></a>Volgende stappen
- Bekijk de [infra structuur en connectiviteit](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) voor HLI
- Raadpleeg [hoge Beschik baarheid en herstel na nood](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) geval voor HLI
