---
title: Azure verbinden met openbare clouds | Microsoft Documenten
description: Verschillende manieren beschrijven om Azure te verbinden met andere openbare clouds
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889633"
---
# <a name="connecting-azure-with-public-clouds"></a>Azure verbinden met openbare clouds

Veel ondernemingen volgen een multi-cloudstrategie vanwege zakelijke en technische doelstellingen. Deze omvatten kosten, flexibiliteit, beschikbaarheid van functies, redundantie, gegevenssoevereiniteit enz. Deze strategie helpt hen het beste van beide clouds te benutten. 

Deze aanpak brengt ook uitdagingen met zich mee voor de onderneming op het gebied van netwerk- en toepassingsarchitectuur. Sommige van deze uitdagingen zijn latentie en gegevensdoorvoer. Om deze uitdagingen het hoofd te bieden, willen klanten direct verbinding maken met meerdere clouds. Sommige serviceproviders bieden een oplossing om meerdere cloudproviders voor de klanten met elkaar te verbinden. In andere gevallen kan de klant zijn eigen router implementeren om meerdere openbare clouds met elkaar te verbinden.
## <a name="connectivity-via-expressroute"></a>Connectiviteit via ExpressRoute
Met ExpressRoute kunnen klanten hun on-premises netwerken uitbreiden naar de Microsoft-cloud via een privéverbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunnen klanten verbindingen leggen met Microsoft-cloudservices.

Er zijn drie manieren om verbinding te maken via ExpressRoute.

1. Layer3-provider
2. Layer2-provider
3. Directe verbinding

### <a name="layer3-provider"></a>Layer3-provider

Layer3 providers zijn algemeen bekend als IP VPN of MPLS VPN providers. Klanten maken gebruik van deze providers voor multipoint-connectiviteit tussen hun datacenters, vestigingen en de cloud. Klanten maken verbinding met de L3-provider via BGP of via een statische standaardroute. De serviceprovider adverteert routes tussen de klantsites, datacenters en de public cloud. 
 
Wanneer u verbinding maakt via Layer3-provider, adverteert Microsoft vnet-routes van klanten naar de serviceprovider via BGP. De provider kan twee verschillende implementaties hebben.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Provider kan worden landing elke cloud provider in een aparte VRF, als het verkeer van alle cloud providers zal bereiken op de router van de klant. Als de klant BGP met een serviceprovider uitvoert, worden deze routes standaard opnieuw geadverteerd aan andere cloudproviders. 

Als de serviceprovider alle cloudproviders in dezelfde VRF binnenbrengt, worden routes rechtstreeks geadverteerd naar andere cloudproviders van de serviceprovider. Dit is uitgaande van standaard BGP operatie waar eBGP routes worden geadverteerd aan andere eBGP buren standaard.

Elke openbare cloud heeft verschillende voorvoegsellimiet, dus tijdens het distribueren van de routes moet serviceprovider voorzichtig zijn bij het distribueren van de routes.

### <a name="layer2-provider-and-direct-connection"></a>Layer2-provider en directe verbinding

Hoewel fysieke connectiviteit in beide modellen anders is, maar bij layer3 wordt BGP direct tussen MSEE en de klantrouter tot stand gebracht. Voor ExpressRoute Direct maakt de klant rechtstreeks verbinding met MSEE. In het geval van Layer2 breidt de serviceprovider VLAN uit van klantentot de cloud. Klanten draaien BGP bovenop het layer2-netwerk om hun DC's met de cloud te verbinden.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
In beide gevallen heeft de klant point-to-point verbindingen met elk van de openbare clouds. De klant zal een aparte BGP-verbinding met elke openbare cloud tot stand brengen. Routes die door één cloudprovider worden ontvangen, worden standaard geadverteerd aan andere cloudprovider. Elke cloudprovider heeft een andere voorvoegsellimiet, dus tijdens het adverteren van de routes moet de klant deze limieten regelen. De klant kan gebruikelijke BGP-knoppen gebruiken met Microsoft, terwijl u advertenties adverteert vanuit andere openbare clouds.

## <a name="direct-connection-with-expressroute"></a>Directe verbinding met ExpressRoute

Klanten kunnen ervoor kiezen om ExpressRoute rechtstreeks te verbinden met het directe connectiviteitsaanbod van de cloudprovider. Twee cloudproviders worden terug naar achteren verbonden en BGP zal direct tussen hun routers worden gevestigd. Dit type verbinding is vandaag beschikbaar met Oracle.

## <a name="site-to-site-vpn"></a>Site-naar-site-VPN

Klanten kunnen internet gebruiken om hun exemplaren in Azure te verbinden met andere openbare clouds. Bijna alle cloudproviders bieden vpn-mogelijkheden van site-to-site. Er kunnen echter onverenigbaarheden zijn vanwege het ontbreken van bepaalde varianten. Sommige cloudproviders ondersteunen bijvoorbeeld alleen IKEv1, dus er is een VPN-eindpunt vereist in die cloud. Voor die cloudproviders die IKEv2 ondersteunen, kan een directe tunnel worden gelegd tussen VPN-gateways bij beide cloudproviders.

Site-to-site VPN wordt niet beschouwd als een oplossing met een hoge doorvoer en lage latentie. Het kan echter worden gebruikt als een back-up van fysieke connectiviteit.

## <a name="next-steps"></a>Volgende stappen
Zie [Veelgestelde vragen][ER-FAQ] over ExpressRoute voor verdere vragen over ExpressRoute en virtuele netwerkconnectiviteit.

Zie [Directe verbinding tussen Azure en Oracle Cloud instellen][ER-OCI] voor connectiviteit tussen Azure en Oracle

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



