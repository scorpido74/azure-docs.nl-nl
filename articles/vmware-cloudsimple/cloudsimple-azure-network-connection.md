---
title: VMware Solution by CloudSimple - Azure-netwerkverbindingen
description: Meer informatie over het verbinden van uw Azure-virtuele netwerk met uw CloudSimple-regionetwerk
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025075"
---
# <a name="azure-network-connections-overview"></a>Overzicht van Azure-netwerkverbindingen

Wanneer u een CloudSimple-service in een regio maakt en knooppunten maakt, u het als:

* Vraag een Azure ExpressRoute-circuit aan en koppel het aan het CloudSimple-netwerk in die regio.
* Verbind uw CloudSimple-regionetwerk met uw virtuele Azure-netwerk of uw on-premises netwerk via Azure ExpressRoute.
* Geef toegang tot services die worden uitgevoerd in uw Azure-abonnement of uw on-premises netwerk vanuit uw Private Cloud-omgeving.

De ExpressRoute-verbinding heeft een hoge bandbreedte met een lage latentie.

## <a name="benefits"></a>Voordelen

Met azure-netwerkverbinding u:

* Gebruik Azure als back-updoel voor virtuele machines in uw Private Cloud.
* Implementeer KMS-servers in uw Azure-abonnement om uw Private Cloud vSAN-gegevensarchief te versleutelen.
* Gebruik hybride toepassingen waarbij de weblaag van de toepassing wordt uitgevoerd in de openbare cloud terwijl de toepassings- en databaselagen worden uitgevoerd in uw Private Cloud.

## <a name="azure-virtual-network-connection"></a>Virtuele netwerkverbinding van Azure

Private Clouds kan met ExpressRoute worden verbonden met uw Azure-bronnen.  Met de ExpressRoute-verbinding hebt u toegang tot bronnen die in uw Azure-abonnement worden uitgevoerd vanuit uw Private Cloud.  Met deze verbinding u uw Private Cloud-netwerk uitbreiden naar uw virtuele Azure-netwerk.  Routes van het CloudSimple-netwerk worden via BGP uitgewisseld met uw virtuele Azure-netwerk.  Als u virtuele netwerkpeering hebt geconfigureerd, zijn alle virtuele netwerken met peered toegankelijk vanuit uw CloudSimple-netwerk.

![Azure ExpressRoute-verbinding met virtueel netwerk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-verbinding met on-premises netwerk

U uw bestaande Azure ExpressRoute-circuit verbinden met uw CloudSimple-regio. ExpressRoute Global Reach-functie wordt gebruikt om de twee circuits met elkaar te verbinden.  Er wordt een verbinding tot stand gebracht tussen de on-premises en CloudSimple ExpressRoute-circuits.  Met deze verbinding u uw on-premises netwerken uitbreiden naar het Private Cloud-netwerk. Routes van uw CloudSimple-netwerk worden via BGP uitgewisseld met uw on-premises netwerk.

![On-premises ExpressRoute Connection - Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Verbinding met on-premises netwerk en virtueel Azure-netwerk

Verbindingen met on-premises netwerk en Azure virtueel netwerk kunnen naast elkaar bestaan vanuit uw CloudSimple-netwerk.  De verbinding maakt gebruik van BGP om routes uit te wisselen tussen on-premises netwerk, Azure virtueel netwerk en CloudSimple-netwerk.  Wanneer u uw CloudSimple-netwerk aansluit op uw Virtuele Azure-netwerk in aanwezigheid van een Global Reach-verbinding, zijn azure virtuele netwerkroutes zichtbaar op uw on-premises netwerk.  Route-uitwisseling vindt plaats in Azure tussen de edge-routers.

![On-premises ExpressRoute-verbinding met virtuele netwerkverbinding met Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Belangrijke overwegingen

Verbinding maken met cloudSimple-netwerk vanaf on-premises netwerk en vanuit het virtuele Azure-netwerk maakt route-uitwisseling tussen alle netwerken mogelijk.

* Het virtuele Azure-netwerk van Azure is zichtbaar vanuit zowel het on-premises netwerk als het CloudSimple-netwerk.
* Als u vanaf een on-premises netwerk verbinding hebt gemaakt met uw Azure-netwerk, biedt verbinding met het CloudSimple-netwerk met Behulp van Global Reach toegang tot virtuele netwerken vanuit het CloudSimple-netwerk.
* Subnetadressen **mogen elkaar niet** overlappen tussen een van de verbonden netwerken.
* CloudSimple adverteert **niet** met standaardroute naar de ExpressRoute-verbindingen
* Als uw on-premises router de standaardroute adverteert, gebruikt het verkeer van het CloudSimple-netwerk en het virtuele Azure-netwerk de geadverteerde standaardroute.  Als gevolg hiervan kunnen virtuele machines op Azure niet worden geopend met behulp van openbare IP-adressen.

## <a name="next-steps"></a>Volgende stappen

* [Eenvoudig maken met het virtuele Azure-netwerk met CloudSimple via ExpressRoute](virtual-network-connection.md)
* [Maak verbinding van on-premises naar CloudSimple via ExpressRoute](on-premises-connection.md)
