---
title: CloudSimple onderhoud en updates
titleSuffix: Azure VMware Solution by CloudSimple
description: Beschrijft het CloudSimple-serviceproces voor gepland onderhoud en updates
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025024"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple onderhoud en updates

De Private Cloud-omgeving is ontworpen om geen enkel storingspunt te hebben.

* ESXi-clusters zijn geconfigureerd met vSphere High Availability (HA). De clusters hebben ten minste één reserveknooppunt voor tolerantie.
* Redundante primaire opslag wordt geleverd door vSAN, waarvoor ten minste drie knooppunten nodig zijn om bescherming te bieden tegen één enkele fout. vSAN kan worden geconfigureerd om een hogere tolerantie te bieden voor grotere clusters.
* vCenter-, PSC- en NSX Manager VM's zijn geconfigureerd met RAID-10-opslag om te beschermen tegen opslagfouten. De VM's zijn beschermd tegen node/netwerkstoringen door vSphere HA.
* ESXi-hosts hebben redundante ventilatoren en NIC's.
* TOR- en wervelkolomschakelaars zijn geconfigureerd in HA-paren om veerkracht te bieden.

CloudSimple bewaakt continu de volgende VM's op uptime en beschikbaarheid en biedt beschikbaarheidsSLA's:

* ESXi-hosts
* vCenter
* Psc
* NSX Manager

CloudSimple controleert ook continu het volgende op storingen:

* Harde schijven
* Fysieke NIC-poorten
* Servers
* Ventilatoren
* Power
* Switches
* Van poort wisselen

Als een schijf of knooppunt uitvalt, wordt er automatisch een nieuw knooppunt toegevoegd aan het getroffen VMware-cluster om het onmiddellijk weer in de status te brengen.

CloudSimple maakt een back-up van deze VMware-elementen in de Private Clouds, onderhoudt en werkt deze bij:

* Esxi
* vCenter Platform Services
* Controller
* vSAN
* NSX (NSX)

## <a name="back-up-and-restore"></a>Back-up en herstel

CloudSimple back-up omvat:

* Nachtelijke incrementele back-ups van vCenter-, PSC- en DVS-regels.
* vCenter native API's om een back-up te maken van onderdelen op de toepassingslaag.
* Automatische back-up voorafgaand aan het bijwerken of upgraden van de VMware-beheersoftware.
* vCenter-gegevensversleuteling bij de bron voordat gegevens via een TLS1.2-versleuteld kanaal naar Azure worden overgebracht. De gegevens worden opgeslagen in een Azure-blob waar deze worden gerepliceerd in verschillende regio's.

U een herstel aanvragen door een [ondersteuningsverzoek te](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)openen.

## <a name="maintenance"></a>Onderhoud

CloudSimple doet verschillende soorten gepland onderhoud.

### <a name="backendinternal-maintenance"></a>Backend/intern onderhoud

Dit onderhoud omvat meestal het opnieuw configureren van fysieke assets of het installeren van softwarepatches. Het heeft geen invloed op het normale verbruik van de activa die worden onderhouden. Als er redundante NIC's naar elk fysiek rack gaan, worden normaal netwerkverkeer en Private Cloud-bewerkingen niet beïnvloed. Mogelijk merkt u alleen een prestatie-impact als uw organisatie verwacht de volledige redundante bandbreedte te gebruiken tijdens het onderhoudsinterval.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple-portalonderhoud

Er is enige beperkte uitvaltijd van de service vereist wanneer het cloudsimple-besturingsvlak of -infrastructuur wordt bijgewerkt. Momenteel kunnen onderhoudsintervallen zo frequent zijn als één keer per maand. De frequentie zal naar verwachting na verloop van tijd afnemen. CloudSimple biedt meldingen voor portalonderhoud en houdt het interval zo kort mogelijk. Tijdens een onderhoudsinterval van de portal blijven de volgende services zonder enige impact functioneren:

* VMware management plane en applicaties
* vCenter-toegang
* Alle netwerken en opslag
* Al het Azure-verkeer

### <a name="vmware-infrastructure-maintenance"></a>Onderhoud van VMware-infrastructuur

Af en toe is het noodzakelijk om wijzigingen aan te brengen in de configuratie van de VMware-infrastructuur.  Momenteel kunnen deze intervallen elke 1-2 maanden optreden, maar de frequentie zal naar verwachting in de loop van de tijd afnemen. Dit type onderhoud kan meestal worden uitgevoerd zonder het normale verbruik van de CloudSimple-services te onderbreken. Tijdens een VMware-onderhoudsinterval blijven de volgende services zonder enige impact functioneren:

* VMware management plane en applicaties
* vCenter-toegang
* Alle netwerken en opslag
* Al het Azure-verkeer

## <a name="updates-and-upgrades"></a>Updates en upgrades

CloudSimple is verantwoordelijk voor lifecycle management van VMware software (ESXi, vCenter, PSC en NSX) in de Private Cloud.

Software-updates zijn onder andere:

* **Patches**. Beveiligingspatches of bugfixes die door VMware zijn uitgebracht.
* **Updates**. Kleine versiewijziging van een VMware-stackcomponent.
* **Upgrades**. Belangrijke versiewijziging van een VMware-stackcomponent.

CloudSimple test een kritieke beveiligingspatch zodra deze beschikbaar is bij VMware. Per SLA rolt CloudSimple de beveiligingspatch binnen een week uit naar Private Cloud-omgevingen.

CloudSimple biedt driemaandelijkse onderhoudsupdates voor VMware-softwarecomponenten. Wanneer er een nieuwe grote versie van VMware-software beschikbaar is, werkt CloudSimple samen met klanten om een geschikt onderhoudsvenster voor een upgrade te coördineren.

## <a name="next-steps"></a>Volgende stappen

[Back-up workload VM's met Veeam](backup-workloads-veeam.md)
