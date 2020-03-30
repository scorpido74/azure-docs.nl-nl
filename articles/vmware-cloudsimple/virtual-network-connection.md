---
title: Azure virtueel netwerk verbinden met CloudSimple met ExpressRoute - Azure VMware-oplossing van CloudSimple
description: Beschrijft hoe u peering-informatie verkrijgen voor een verbinding tussen het virtuele Azure-netwerk en uw CloudSimple-omgeving
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563993"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Eenvoudig maken met het virtuele Azure-netwerk met CloudSimple via ExpressRoute

U uw Private Cloud-netwerk uitbreiden naar uw Azure-virtuele netwerk en Azure-bronnen. Met een ExpressRoute-verbinding hebt u toegang tot bronnen die in uw Azure-abonnement worden uitgevoerd vanuit uw Private Cloud.

## <a name="request-authorization-key"></a>Autorisatiesleutel aanvragen

Er is een autorisatiesleutel vereist voor de ExpressRoute-verbinding tussen uw Private Cloud en het virtuele Azure-netwerk. Als u een sleutel wilt verkrijgen, dient u een ticket in bij <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Ondersteuning.</a>  Gebruik de volgende gegevens in de aanvraag:

* Type probleem: **Technisch**
* Abonnement: **Selecteer het abonnement waarbij cloudsimple-service wordt geïmplementeerd**
* Service: **VMware Solution by CloudSimple**
* Probleemtype: **Serviceaanvraag**
* Subtype Probleem: **autorisatiesleutel voor Azure VNET-verbinding**
* Onderwerp: **Autorisatiesleutel aanvragen voor Azure VNET-verbinding**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Informatie over peering ophalen van CloudSimple-portal

Als u de verbinding wilt instellen, moet u een verbinding tot stand brengen tussen het virtuele Azure-netwerk en uw CloudSimple-omgeving.  Als onderdeel van de procedure moet u de uri- en autorisatiesleutel van het peercircuit leveren. Verkrijg de URI- en autorisatiesleutel van [cloudsimple-portal.](access-cloudsimple-portal.md)  Selecteer **Netwerk** in het zijmenu en selecteer **vervolgens Azure Network Connection**. Of selecteer **Account** in het zijmenu en selecteer vervolgens **Azure-netwerkverbinding**.

Kopieer peer circuit URI en voor de autorisatiesleutel voor elk van de regio's met behulp van *kopieerpictogram.* Voor elke CloudSimple-regio wilt u verbinding maken:

1. Klik **op Kopiëren** om de URI te kopiëren. Plak het in een bestand waar het beschikbaar kan zijn om toe te voegen aan de Azure-portal.  
2. Klik **op Kopiëren** om de autorisatiesleutel te kopiëren en deze ook in het bestand te plakken.

Kopieer de autorisatiesleutel en peer circuit URI die **beschikbaar** is.  **De gebruikte** status geeft aan dat de sleutel al is gebruikt om een virtuele netwerkverbinding te maken.

![Pagina Virtuele netwerkverbinding](media/virtual-network-connection.png)

Zie [Uw CloudSimple Private Cloud-omgeving verbinden met het virtuele Azure-netwerk via ExpressRoute voor](azure-expressroute-connection.md)meer informatie over het instellen van het virtuele Azure-netwerk op CloudSimple.

## <a name="next-steps"></a>Volgende stappen

* [Virtuele Azure-netwerkverbinding met Private Cloud](azure-expressroute-connection.md)
* [Verbinding maken met on-premises netwerk via Azure ExpressRoute](on-premises-connection.md)
