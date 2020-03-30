---
title: Private cloud VMware-componenten
titleSuffix: Azure VMware Solution by CloudSimple
description: Beschrijft hoe VMware-componenten worden geïnstalleerd in private cloud
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279505"
---
# <a name="private-cloud-vmware-components"></a>Private Cloud VMware-componenten

Een Private Cloud is een geïsoleerde VMware-stack (ESXi hosts, vCenter, vSAN en NSX) die wordt beheerd door een vCenter-server in een beheerdomein.  Met de CloudSimple-service u VMware native implementeren op Azure bare metal-infrastructuur op Azure-locaties.  Private Clouds zijn geïntegreerd met de rest van de Azure Cloud.  Een private cloud wordt geïmplementeerd met de volgende VMware-stackcomponenten:

* **VMware ESXi -** Hypervisor op azure-dedicated knooppunten
* **VMware vCenter -** Toestel voor gecentraliseerd beheer van private cloud vSphere-omgeving
* **VMware vSAN -** Hyper-geconvergeerde infrastructuuroplossing
* **VMware NSX Datacenter -** Netwerkvirtualisatie en beveiligingssoftware  

## <a name="vmware-component-versions"></a>VMware-componentversies

Een Private Cloud VMware stack wordt geïmplementeerd met de volgende softwareversie.

| Onderdeel | Versie | Gelicentieerde versie |
|-----------|---------|------------------|
| Esxi | 6.7U2 | Enterprise Plus |
| vCenter | 6.7U2 | vCenter-standaard |
| vSAN | 6.7 | Enterprise |
| NSX-datacenter | 2.4.1 | Geavanceerd |

## <a name="esxi"></a>Esxi

VMware ESXi is geïnstalleerd op ingerichte CloudSimple-knooppunten wanneer u een private cloud maakt.  ESXi biedt de hypervisor voor het implementeren van workload virtuele machines (VM's).  Knooppunten bieden hypergeconvergeerde infrastructuur (compute en storage) op uw private cloud.  De knooppunten maken deel uit van het vSphere-cluster op de private cloud.  Elk knooppunt heeft vier fysieke netwerkinterfaces die zijn aangesloten op het underlay-netwerk.  Twee fysieke netwerkinterfaces worden gebruikt om een **vSphere Distributed Switch (VDS)** op vCenter te maken en twee worden gebruikt om een **DOOR NSX beheerde virtuele gedistribueerde switch (N-VDS)** te maken.  Netwerkinterfaces zijn geconfigureerd in actieve modus voor hoge beschikbaarheid.

Meer informatie over VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter-servertoestel

vCenter server appliance (VCSA) biedt de verificatie-, beheer- en orkestratiefuncties voor VMware Solution by CloudSimple. VCSA met embedded Platform Services Controller (PSC) wordt geïmplementeerd wanneer u uw private cloud maakt.  VCSA wordt geïmplementeerd op het vSphere-cluster dat wordt gemaakt wanneer u uw private cloud implementeert.  Elke private cloud heeft zijn eigen VCSA.  Uitbreiding van een private cloud voegt de knooppunten toe aan de VCSA op de private cloud.

### <a name="vcenter-single-sign-on"></a>vCenter-aanmelding

Embedded Platform Services Controller op VCSA is gekoppeld aan een **vCenter Single Sign-On domein**.  De domeinnaam is **cloudsimple.local**.  Er wordt **CloudOwner@cloudsimple.com** een standaardgebruiker gemaakt om toegang te krijgen tot vCenter.  U uw on-premises/Azure active directory [identity sources voor vCenter](set-vcenter-identity.md)toevoegen.

## <a name="vsan-storage"></a>vSAN-opslag

Private clouds worden gemaakt met volledig geconfigureerde all-flash vSAN-opslag, lokaal in het cluster.  Minimaal drie knooppunten van dezelfde SKU zijn vereist om een vSphere-cluster met vSAN-gegevensarchief te maken.  Deduplicatie en compressie zijn standaard ingeschakeld in het vSAN-gegevensarchief.  Op elk knooppunt van het vSphere-cluster worden twee schijfgroepen gemaakt. Elke schijfgroep bevat één cacheschijf en drie capaciteitsschijven.

Er wordt een standaard vSAN-opslagbeleid gemaakt op het vSphere-cluster en toegepast op het vSAN-gegevensarchief.  Dit beleid bepaalt hoe de VM-opslagobjecten worden ingericht en toegewezen in het gegevensarchief om het vereiste serviceniveau te garanderen.  In het opslagbeleid wordt de **foutiefouten gedefinieerd (FTT)** en de **tolerantiemethode Fout .**  U nieuwe opslagbeleidsregels maken en deze toepassen op de VM's. Om SLA te behouden, moet 25% reservecapaciteit behouden blijven op het vSAN datastore.  

### <a name="default-vsan-storage-policy"></a>Standaard vSAN-opslagbeleid

Onderstaande tabel toont de standaardparameters voor vSAN-opslagbeleid.

| Aantal knooppunten in vSphere-cluster | FTT (FTT) | Faaltolerantiemethode |
|------------------------------------|-----|--------------------------|
| 3 en 4 knooppunten | 1 | RAID 1 (spiegelen) - maakt 2 kopieën |
| 5 tot 16 knooppunten | 2 | RAID 1 (spiegelen) - maakt 3 kopieën |

## <a name="nsx-data-center"></a>NSX-datacenter

NSX Data Center biedt netwerkvirtualisatie, microsegmentatie en netwerkbeveiligingsmogelijkheden in uw private cloud.  U alle services die worden ondersteund door NSX Data Center configureren op uw private cloud via NSX.  Wanneer u een private cloud maakt, worden de volgende NSX-componenten geïnstalleerd en geconfigureerd.

* NSXT-manager
* Transportzones
* Host- en Edge Uplink-profiel
* Logische switch voor Edge Transport, Ext1 en Ext2
* IP-pool voor ESXi Transport Node
* IP-pool voor edge-transportknooppunt
* Randknooppunten
* DRS-antiaffiniteitregel voor controller- en Edge VM's
* Tier 0-router
* BGP inschakelen op Tier0-router

## <a name="vsphere-cluster"></a>vSphere-cluster

ESXi-hosts zijn geconfigureerd als een cluster om een hoge beschikbaarheid van de private cloud te garanderen.  Wanneer u een private cloud maakt, worden beheercomponenten van vSphere geïmplementeerd op het eerste cluster.  Er wordt een resourcegroep gemaakt voor beheeronderdelen en alle beheer-VM's worden geïmplementeerd in deze resourcegroep. Het eerste cluster kan niet worden verwijderd om de private cloud te verkleinen.  vSphere-cluster biedt een hoge beschikbaarheid voor VM's met **vSphere HA**.  Fouten die worden getolereerd, zijn gebaseerd op het aantal beschikbare knooppunten in het cluster.  U de ```Number of nodes = 2N+1``` ```N``` formule gebruiken waarbij het aantal fouten is dat moet worden getolereerd.

### <a name="vsphere-cluster-limits"></a>vSphere-clusterlimieten

| Resource | Limiet |
|----------|-------|
| Minimumaantal knooppunten om een private cloud te maken (eerste vSphere-cluster) | 3 |
| Maximaal aantal knooppunten in een vSphere-cluster op een private cloud | 16 |
| Maximaal aantal knooppunten in een private cloud | 64 |
| Maximaal aantal vSphere-clusters in een private cloud | 21 |
| Minimumaantal knooppunten op een nieuw vSphere-cluster | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Onderhoud van VMware-infrastructuur

Af en toe is het noodzakelijk om wijzigingen aan te brengen in de configuratie van de VMware-infrastructuur. Momenteel kunnen deze intervallen elke 1-2 maanden optreden, maar de frequentie zal naar verwachting in de loop van de tijd afnemen. Dit type onderhoud kan meestal worden uitgevoerd zonder het normale verbruik van de CloudSimple-services te onderbreken. Tijdens een VMware-onderhoudsinterval blijven de volgende services zonder enige impact functioneren:

* VMware management plane en applicaties
* vCenter-toegang
* Alle netwerken en opslag
* Al het Azure-verkeer

## <a name="updates-and-upgrades"></a>Updates en upgrades

CloudSimple is verantwoordelijk voor lifecycle management van VMware software (ESXi, vCenter, PSC en NSX) in de private cloud.

Software-updates zijn onder andere:

* **Patches**. Beveiligingspatches of bugfixes die door VMware zijn uitgebracht.
* **Updates**. Kleine versiewijziging van een VMware-stackcomponent.
* **Upgrades**. Belangrijke versiewijziging van een VMware-stackcomponent.

CloudSimple test een kritieke beveiligingspatch zodra deze beschikbaar is bij VMware. Per SLA rolt CloudSimple de beveiligingspatch binnen een week uit naar private cloudomgevingen.

CloudSimple biedt driemaandelijkse onderhoudsupdates voor VMware-softwarecomponenten. Wanneer er een nieuwe grote versie van VMware-software beschikbaar is, werkt CloudSimple samen met klanten om een geschikt onderhoudsvenster voor een upgrade te coördineren.  

## <a name="next-steps"></a>Volgende stappen

* [CloudSimple onderhoud en updates](cloudsimple-maintenance-updates.md)
