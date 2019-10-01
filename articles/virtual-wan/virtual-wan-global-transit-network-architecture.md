---
title: Azure Virtual WAN Global Transit-netwerk architectuur | Microsoft Docs
description: Meer informatie over wereld wijde doorvoer netwerk architectuur voor virtuele WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 0a5059382c26afd6120dc14a1ab2c7e5d281e7a1
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695258"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Wereld wijde doorvoer netwerk architectuur en virtueel WAN

Wereld wijde doorvoer netwerk architectuur wordt door ondernemingen goedgekeurd voor het consolideren, verbinden en beheren van de Cloud gerichte moderne IT-footprint van de onderneming. In een moderne, in de Cloud gerichte onderneming hoeft het netwerk verkeer niet te worden backhauled aan hoofd kantoor. Wereld wijde doorvoer netwerk architectuur is gebaseerd op zowel bekende netwerk concepten als nieuwe concepten die uniek zijn voor Cloud-en Cloud architecturen.

![architectuur](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Afbeelding 1: Wereld wijd Transit netwerk met virtuele WAN @ no__t-0

Moderne ondernemingen vereisen alomtegenwoordige-connectiviteit tussen Hyper-gedistribueerde toepassingen, gegevens en gebruikers in de Cloud en on-premises. Met Azure Virtual WAN kunt u een wereld wijde doorvoer netwerk architectuur door alomtegenwoordige, een wille keurige verbinding te maken tussen wereld wijd gedistribueerde sets van VNets, sites, toepassingen en gebruikers. Azure Virtual WAN is een door micro soft beheerde service. Alle netwerk onderdelen waarvan deze service bestaat, worden door micro soft gehost en beheerd. Zie het artikel [overzicht van Virtual WAN](virtual-wan-about.md) voor meer informatie over Virtual WAN.

In de virtuele WAN-architectuur van Azure fungeren Azure-regio's als hubs waarmee u uw filialen kunt verbinden. Zodra de vertakkingen zijn verbonden, kunt u gebruikmaken van de Azure-backbone om vertakking-naar-VNet-en, optioneel, vertakking-naar-vertakking-connectiviteit tot stand te brengen.

U kunt een virtueel WAN opzetten door één virtuele WAN-hub te maken in de regio met het grootste aantal spokes (vertakkingen, VNets, gebruikers) en vervolgens de spokes in andere regio's te verbinden met de hub. Als spokes geografisch worden gedistribueerd, kunt u ook regionale hubs instantiëren en de hubs verbinden. De hubs zijn allemaal deel van hetzelfde virtuele WAN, maar ze kunnen worden gekoppeld aan verschillende regionale beleids regels.

## <a name="hub"></a>Door Voer van hub en spoke

De wereld wijde doorvoer netwerk architectuur is gebaseerd op een klassiek hub-en-spoke-verbindings model, waarbij de hub van het gehoste netwerk van de Cloud transitieve connectiviteit mogelijk maakt tussen eind punten die kunnen worden gedistribueerd in verschillende typen spokes.
  
In dit model kan een spoke zijn:

* Virtueel netwerk (VNets)
* Fysieke branche site
* Externe gebruiker
* Internet

![diagram voor wereld wijde doorvoer hub en spoke](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Afbeelding 2: Hub en spoke @ no__t-0

In afbeelding 2 ziet u de logische weer gave van het wereld wijde netwerk waarin geografisch gedistribueerde gebruikers, fysieke sites en VNets zijn verbonden via een netwerkhub die wordt gehost in de Cloud. Met deze architectuur kan een logische Transit connectiviteit met één hop tussen de netwerk eindpunten worden ingeschakeld. De spokes zijn verbonden met de hub door verschillende Azure-netwerk services, zoals ExpressRoute of site-naar-site-VPN voor fysieke filialen, VNet-verbindingen voor VNets en punt-naar-site-VPN voor externe gebruikers.

## <a name="crossregion"></a>Connectiviteit tussen regio's

Voor een onderneming volgt een Cloud footprint doorgaans de fysieke footprint. De meeste ondernemingen hebben toegang tot de Cloud vanuit een regio die het dichtst bij hun fysieke site en gebruikers ligt. Een van de belangrijkste principals van de wereld wijde netwerk architectuur is het inschakelen van connectiviteit tussen regio's tussen netwerk entiteiten en eind punten. Een Cloud footprint kan meerdere regio's omvatten. Dit betekent dat verkeer van een vertakking dat is verbonden met de cloud in één regio, een andere vertakking of een VNet in een andere regio kan bereiken met behulp van hub-naar-hub-connectiviteit, die momenteel op ons plan is.

## <a name="any"></a>Elke verbinding

Wereld wijde doorvoer netwerk architectuur maakt het mogelijk *om elke verbinding* via een centrale netwerkhub te maken. Deze architectuur elimineert of verkleint de nood zaak van een volledige mesh-of gedeeltelijke connectiviteits modellen die ingewik kelder zijn om te bouwen en onderhouden. Daarnaast is routering beheer in hub-en-spoke versus net-netwerken eenvoudiger te configureren en te onderhouden.

Elke verbinding, in de context van een wereld wijde architectuur, biedt een onderneming met wereld wijd gedistribueerde gebruikers, vertakkingen, data centers, VNets en toepassingen om met elkaar verbinding te maken via de transit hub. De transit hub fungeert als het wereld wijde doorvoer systeem.

![verkeers paden](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Afbeelding 3: Virtuele WAN-verkeers paden @ no__t-0

Azure Virtual WAN ondersteunt de volgende globale doorvoer connectiviteits paden. De letters tussen haakjes worden toegewezen aan afbeelding 3.

* Vertakking-naar-VNet (a)  
* Vertakking-naar-vertakking (b)
* Externe gebruiker-naar-VNet (c)
* Externe gebruiker-to-Branch (d)
* VNet-naar-VNet met behulp van VNet-peering (e)
* ExpressRoute Global Reach 

### <a name="branchvnet"></a>Vertakking-naar-VNet

Vertakking-naar-VNet is het primaire pad dat door Azure Virtual WAN wordt ondersteund. Met dit pad kunt u vertakkingen verbinden met Azure IAAS Enter prise-workloads die zijn geïmplementeerd in azure VNets. Vertakkingen kunnen worden verbonden met het virtuele WAN via ExpressRoute of site-naar-site-VPN. Het verkeer transitt naar VNets die zijn verbonden met de virtuele WAN-hubs via VNet-verbindingen. [Gateway-overdracht](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) is niet vereist voor virtuele WAN, omdat door Virtual WAN automatisch gateway doorvoer naar filialen sites wordt ingeschakeld.

### <a name="branchbranch"></a>Vertakking-naar-vertakking

Vertakkingen kunnen worden verbonden met een virtuele WAN-hub van Azure met behulp van ExpressRoute-circuits en/of site-naar-site-VPN-verbindingen. U kunt de vertakkingen verbinden met de virtuele WAN-hub in de regio die het dichtst bij de vertakking ligt.

Met deze optie kunnen bedrijven gebruikmaken van de Azure-backbone om vertakkingen te verbinden. Hoewel deze mogelijkheid echter beschikbaar is, moet u de voor delen van het verbinden van branches via Azure Virtual WAN, vergeleken met een particulier WAN, afwegen.

### <a name="usertovnet"></a>Externe gebruiker-naar-VNet

U kunt directe, veilige externe toegang tot Azure inschakelen met behulp van punt-naar-site-verbindingen van een externe gebruikers client naar een virtueel WAN. Externe gebruikers van ondernemingen hoeven niet langer hairpin te maken met de Cloud met behulp van een bedrijfs netwerk.

### <a name="usertobranch"></a>Externe gebruiker-naar-vertakking

Via het externe User-to-Branch-pad kunnen externe gebruikers die gebruikmaken van een punt-naar-site-verbinding met Azure toegang hebben tot on-premises werk belastingen en toepassingen door middel van door Voer via de Cloud. Dit pad biedt externe gebruikers de flexibiliteit om werk belastingen te openen die zowel in azure als on-premises zijn geïmplementeerd. Ondernemingen kunnen de centrale Cloud service voor externe toegang inschakelen in azure Virtual WAN.

### <a name="vnetvnet"></a>Door Voer van VNet-naar-VNet met behulp van VNet-peering

Gebruik VNet-peering om VNets te verbinden met meerdere lagen die zijn geïmplementeerd in verschillende VNets. Een VNet-naar-VNet-Transit scenario via Azure Virtual WAN wordt momenteel niet ondersteund, maar bevindt zich op het Azure-schema. Het verbinden van VNets via VNet-peering is de aanbevolen oplossing voor VNets die met elkaar moeten worden verbonden. 

### <a name="globalreach"></a>ExpressRoute Global Reach

ExpressRoute is een privé-en robuuste manier om uw on-premises netwerken te verbinden met de Microsoft Cloud. ExpressRoute Global Reach is een invoeg functie voor ExpressRoute. Met Global Reach kunt u ExpressRoute-circuits samen koppelen om een privé netwerk tussen uw on-premises netwerken te maken. Voor vertakkingen die zijn verbonden met Azure Virtual WAN met ExpressRoute moet de ExpressRoute Global Reach met elkaar communiceren.

In dit model kan elke vertakking die is verbonden met de virtuele WAN-hub met behulp van ExpressRoute, verbinding maken met VNets met behulp van het pad van de vertakking-naar-VNet. Vertakkings-naar-vertakking verkeer kan de hub niet door Voer omdat ExpressRoute Global Reach een meer optimaal pad via Azure WAN mogelijk maakt.

## <a name="security"></a>Beveiliging en beleids beheer

De Virtual Network hub verbindt onderling Connects en mogelijk ziet u alle Transit verkeer. Het kan de plaats zijn om centrale netwerk functies en-services te hosten, zoals een Cloud routering, netwerk beleid en-beveiliging, en toegangs beheer voor Internet.

## <a name="next-steps"></a>Volgende stappen

Maak een verbinding met behulp van Virtual WAN.

* [Site-naar-site-verbindingen met behulp van Virtual WAN](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-verbindingen met behulp van Virtual WAN](virtual-wan-expressroute-portal.md)
