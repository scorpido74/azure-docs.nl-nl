---
title: Azure VMware-oplossingen (AVS)-AVS-onderdelen van de Privécloud-VMware
description: Hierin wordt beschreven hoe VMware-onderdelen worden geïnstalleerd op de AVS-privécloud
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016779"
---
# <a name="avs-private-cloud-vmware-components"></a>VMware-onderdelen van de AVS-privécloud

Een AVS-privécloud is een geïsoleerde VMware-stack (ESXi hosts, vCenter, vSAN en NSX) die worden beheerd door een vCenter-Server in een beheer domein. Met de AVS-service kunt u VMware systeem eigen implementeren op een Azure bare-metal infra structuur in azure-locaties. Persoonlijke Clouds van AVS zijn geïntegreerd met de rest van de Azure-Cloud. Een AVS-Privécloud wordt geïmplementeerd met de volgende VMware stack-onderdelen:

* **VMware ESXi-** Hyper Visor op aan Azure toegewezen knoop punten
* **VMware vCenter-** Apparaat voor gecentraliseerd beheer van de vSphere-omgeving van de AVS-Privécloud
* **VMware-vSAN-** Oplossing voor Hyper-geconvergeerde infra structuur
* **VMware NSX Data Center-** Netwerkvirtualisatie en beveiligings software  

## <a name="vmware-component-versions"></a>Versies van VMware-onderdelen

Een AVS-VMware-stack voor de Privécloud wordt geïmplementeerd met de volgende software versie.

| Component | Versie | Versie met licentie |
|-----------|---------|------------------|
| ESXi | 6,7 U2 | Enter prise plus |
| vCenter | 6,7 U2 | vCenter-standaard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.4.1 | Geavanceerd |

## <a name="esxi"></a>ESXi

VMware ESXi is geïnstalleerd op ingerichte AVS-knoop punten wanneer u een Privécloud maakt. ESXi biedt de Hyper Visor voor het implementeren van virtuele machines van werk belastingen (Vm's). Knoop punten bieden een Hyper-geconvergeerde infra structuur (reken-en opslag) op uw AVS-privécloud. De knoop punten maken deel uit van het vSphere-cluster in de Privécloud van de AVS. Elk knoop punt heeft vier fysieke netwerk interfaces die zijn verbonden met het aan-netwerk. Twee fysieke netwerk interfaces worden gebruikt voor het maken van een **vSphere-gedistribueerde switch (VDS)** in vCenter en twee worden gebruikt voor het maken van een door **NSX beheerde virtuele gedistribueerde switch (N-VDS)** . Netwerk interfaces worden geconfigureerd in de modus actief-actief voor hoge Beschik baarheid.

Meer informatie over VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter Server-apparaat

vCenter Server-apparaat (VCSA) biedt de functies voor verificatie, beheer en Orchestration voor VMware-oplossingen (AVS). VCSA met de embedded platform Services controller (PSC) wordt geïmplementeerd wanneer u de Privécloud van uw AVS maakt. VCSA wordt geïmplementeerd op het vSphere-cluster dat wordt gemaakt wanneer u de Privécloud van uw AVS implementeert. Elke AVS-privécloud heeft zijn eigen VCSA. Uitbrei ding van een AVS-privécloud voegt de knoop punten toe aan de VCSA in de Privécloud van de AVS.

### <a name="vcenter-single-sign-on"></a>eenmalige aanmelding via vCenter

De controller van de embedded platform Services op VCSA is gekoppeld aan een **vCenter-domein met eenmalige aanmelding**. De domein naam is **AVS. local**. Er wordt een standaard gebruikers **CloudOwner@AVS.com** gemaakt voor toegang tot vCenter. U kunt uw on-premises/Azure Active Directory- [identiteits bronnen voor vCenter](set-vcenter-identity.md)toevoegen.

## <a name="vsan-storage"></a>vSAN-opslag

Persoonlijke Clouds van AVS worden gemaakt met volledig geconfigureerde alle Flash vSAN-opslag, lokaal naar het cluster. Er zijn mini maal drie knoop punten van dezelfde SKU vereist voor het maken van een vSphere-cluster met vSAN-gegevens opslag. De-duplicatie en compressie zijn standaard ingeschakeld op de vSAN-gegevens opslag. Op elk knoop punt van het vSphere-cluster worden twee schijf groepen gemaakt. Elke schijf groep bevat één cache schijf en drie capaciteits schijven.

Er wordt een standaard vSAN-opslag beleid gemaakt op het vSphere-cluster en toegepast op het vSAN-gegevens archief. Dit beleid bepaalt hoe de VM-opslag objecten worden ingericht en toegewezen in de gegevens opslag om het vereiste service niveau te garanderen. Het opslag beleid definieert de **fouten die moeten worden toegestaan (FTT)** en de **fout tolerantie methode**. U kunt nieuwe opslag beleidsregels maken en deze Toep assen op de Vm's. Als u SLA wilt onderhouden, moet er een capaciteit van 25% worden behouden op de vSAN-gegevens opslag. 

### <a name="default-vsan-storage-policy"></a>Standaard vSAN-opslag beleid

De volgende tabel bevat de standaard para meters voor vSAN-opslag beleid.

| Aantal knoop punten in het vSphere-cluster | FTT | Fout tolerantie methode |
|------------------------------------|-----|--------------------------|
| 3 en 4 knoop punten | 1 | RAID 1 (spie gelen): maakt 2 kopieën |
| 5 tot 16 knoop punten | 2 | RAID 1 (spie gelen): drie kopieën maken |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center biedt Netwerkvirtualisatie, micro segmentatie en netwerk beveiligings mogelijkheden op uw AVS-Privécloud. U kunt alle services die worden ondersteund door NSX Data Center op uw AVS-Privécloud configureren via NSX. Wanneer u een Privécloud maakt, worden de volgende NSX-onderdelen geïnstalleerd en geconfigureerd.

* NSXT Manager
* Transport zones
* Uplinkpoortprofiel voor host en Edge
* Logische switch voor Edge Trans Port, Ext1 en ext2
* IP-adres groep voor ESXi-transport knooppunt
* IP-adres groep voor het knoop punt Edge-Trans Port
* Edge-knoop punten
* DRS-affiniteits regel voor controller-en Edge-Vm's
* Router op laag 0
* BGP inschakelen op de Tier0-router

## <a name="vsphere-cluster"></a>vSphere-cluster

ESXi-hosts worden geconfigureerd als een cluster om te zorgen voor hoge Beschik baarheid van de AVS-Privécloud. Wanneer u een Privécloud maakt, worden beheer onderdelen van vSphere geïmplementeerd op het eerste cluster. Er wordt een resource groep gemaakt voor beheer onderdelen en alle management-Vm's worden geïmplementeerd in deze resource groep. Het eerste cluster kan niet worden verwijderd om de cloud van de AVS te verkleinen. vSphere-cluster biedt hoge Beschik baarheid voor Vm's met **VSPHERE ha**. Te verdragen fouten zijn gebaseerd op het aantal beschik bare knoop punten in het cluster. U kunt de formule gebruiken ```Number of nodes = 2N+1``` waarbij ```N``` het aantal fouten is dat moet worden toegelaten.

### <a name="vsphere-cluster-limits"></a>limieten voor vSphere-clusters

| Bron | Limiet |
|----------|-------|
| Minimum aantal knoop punten voor het maken van een AVS-Privécloud (eerste vSphere-cluster) | 3 |
| Het maximum aantal knoop punten in een vSphere-cluster in een Privécloud-Cloud | 16 |
| Maximum aantal knoop punten in een Privécloud in de Cloud | 64 |
| Maximum aantal vSphere-clusters in een geavs-Privécloud | 21 |
| Minimum aantal knoop punten op een nieuw vSphere-cluster | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware-infrastructuur onderhoud

Het is nu af en toe nood zakelijk om wijzigingen aan te brengen in de configuratie van de VMware-infra structuur. Op dit moment kunnen deze intervallen elke 1-2 maanden optreden, maar wordt de frequentie naar verwachting in de loop van de tijd geweigerd. Dit type onderhoud kan meestal worden uitgevoerd zonder het normale gebruik van de AVS-services te onderbreken. Tijdens een VMware-onderhouds interval blijven de volgende services functioneren zonder enige impact:

* VMware-beheer vlak en-toepassingen
* toegang tot vCenter
* Alle netwerken en opslag
* Al het Azure-verkeer

## <a name="updates-and-upgrades"></a>Updates en upgrades

AVS is verantwoordelijk voor het levenscyclus beheer van VMware-software (ESXi, vCenter, PSC en NSX) in de AVS-Privécloud.

Software-updates zijn onder andere:

* **Patches**. Beveiligings patches of oplossingen voor oplossingen die worden vrijgegeven door VMware.
* **Updates**. Wijziging van de secundaire versie van een VMware-stack onderdeel.
* **Upgrades**. Wijziging van de hoofd versie van een VMware-stack onderdeel.

Met AVS wordt een kritieke beveiligings patch getest zodra deze beschikbaar is in VMware. Via de SLA implementeert AVS de beveiligings patch om persoonlijke Cloud omgevingen binnen een week te AVS.

AVS biedt updates voor het driemaandelijkse onderhoud aan VMware-software onderdelen. Wanneer er een nieuwe primaire versie van VMware-software beschikbaar is, werkt AVS samen met klanten om een geschikt onderhouds venster voor de upgrade te coördineren. 

## <a name="next-steps"></a>Volgende stappen

* [Onderhoud en updates van AVS](cloudsimple-maintenance-updates.md)
