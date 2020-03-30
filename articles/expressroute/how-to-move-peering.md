---
title: 'Azure ExpressRoute: een openbare peering verplaatsen naar Microsoft-peering'
description: In dit artikel ziet u de stappen om uw openbare peering naar Microsoft-peering op ExpressRoute te verplaatsen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436846"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Overstappen van openbare peering op Microsoft-peering

Met dit artikel u een openbare peeringconfiguratie verplaatsen naar Microsoft-peering zonder downtime. ExpressRoute biedt ondersteuning voor het gebruik van Microsoft-peering met routefilters voor Azure PaaS-services, zoals Azure Storage en Azure SQL Database. U hebt nu maar één routeringsdomein nodig voor toegang tot Microsoft PaaS- en SaaS-services. U kunt routefilters gebruiken om de PaaS-servicevoorvoegsels selectief te adverteren voor Azure-regio's die u wilt verbruiken.

Azure public peering heeft 1 NAT IP-adres dat is gekoppeld aan elke BGP-sessie. Met Microsoft-peering u uw eigen NAT-toewijzingen configureren en routefilters gebruiken voor selectieve voorvoegseladvertenties. Public Peering is een unidirectionele service waarmee connectiviteit altijd wordt gestart van uw WAN naar Microsoft Azure-services. Microsoft Azure-services kunnen geen verbindingen met uw netwerk starten via dit routeringsdomein.

Zodra openbaar peering is ingeschakeld, u verbinding maken met alle Azure-services. We staan u niet toe om selectief diensten te kiezen waarvoor we routes adverteren. Terwijl Microsoft peering een bidirectionele connectiviteit is waar verbinding kan worden gestart vanuit Microsoft Azure-service, samen met uw WAN. Zie [ExpressRoute-circuits en routeringsdomeinen](expressroute-circuit-peerings.md)voor meer informatie over routeringsdomeinen en peering.

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Als u verbinding wilt maken met Microsoft-peering, moet u NAT instellen en beheren. Uw connectiviteitsprovider kan de NAT instellen en beheren als een beheerde service. Als u van plan bent toegang te krijgen tot de Azure PaaS- en Azure SaaS-services op Microsoft-peering, is het belangrijk om de NAT IP-groep correct te vergroten. Zie de [NAT-vereisten voor Microsoft-peering voor](expressroute-nat.md#nat-requirements-for-microsoft-peering)meer informatie over NAT voor ExpressRoute. Wanneer u verbinding maakt met Microsoft via Azure ExpressRoute(Microsoft-peering), hebt u meerdere koppelingen naar Microsoft. De ene koppeling is uw bestaande internetverbinding en de andere gaat via ExpressRoute. Bepaald verkeer naar Microsoft kan via internet gaan maar kan terugkeren via ExpressRoute of andersom.

![Bidirectionele connectiviteit](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> De NAT IP-adresgroep die wordt geadverteerd aan Microsoft mag niet worden geadverteerd op internet. Dit verbreekt de connectiviteit met andere Microsoft-services.

Raadpleeg [Asymmetrische routering met meerdere netwerkpaden](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) voor kanttekeningen van asymmetrische routering voordat u Microsoft-peering configureert.

* Als u openbare peering gebruikt en momenteel IP-netwerkregels hebt voor openbare IP-adressen die worden gebruikt om toegang te krijgen tot [Azure Storage](../storage/common/storage-network-security.md) of Azure [SQL Database,](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)moet u ervoor zorgen dat de NAT IP-groep die is geconfigureerd met Microsoft-peering is opgenomen in de lijst met openbare IP-adressen voor het Azure-opslagaccount of Azure SQL-account.<br>
* Gebruik de stappen in dit artikel in de volgorde waarin ze worden gepresenteerd om naar Microsoft-peering te gaan zonder downtime.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Microsoft-peering maken

Als Microsoft-peering niet is gemaakt, gebruikt u een van de volgende artikelen om Microsoft-peering te maken. Als uw connectiviteitsprovider beheerde layer 3-services aanbiedt, u de connectiviteitsprovider vragen microsoft-peering voor uw circuit in te schakelen.

Als laag 3 door u wordt beheerd, is de volgende informatie vereist voordat u verdergaat:

* Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. Vanuit dit subnet wijst u het eerste bruikbare IP-adres toe aan uw router, omdat Microsoft het tweede bruikbare IP voor zijn router gebruikt.<br>
* Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. Vanuit dit subnet wijst u het eerste bruikbare IP-adres toe aan uw router, omdat Microsoft het tweede bruikbare IP voor zijn router gebruikt.<br>
* Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Voor zowel primaire als secundaire koppelingen moet u dezelfde VLAN-id gebruiken.<br>
* AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.<br>
* Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. Als u van plan bent een set voorvoegsels te verzenden, u een door komma's gescheiden lijst verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.<br>
* Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.

* **Optioneel** - Klant ASN: Als u reclame maakt voorvoegsels die niet zijn geregistreerd op het AS-nummer voor peering, u het AS-nummer opgeven waarop ze zijn geregistreerd.<br>
* **Optioneel** - Een MD5-hash als u ervoor kiest er een te gebruiken.

Gedetailleerde instructies om Microsoft-peering in te schakelen zijn te vinden in de volgende artikelen:

* [Microsoft-peering maken met Azure-portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Microsoft-peering maken met Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Microsoft-peering maken met Azure CLI](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Microsoft-peering valideren is ingeschakeld

Controleer of de Microsoft-peering is ingeschakeld en of de geadverteerde openbare voorvoegsels in de geconfigureerde status staan.

* [Azure-portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure-CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Een routefilter configureren en koppelen aan het circuit

Nieuwe Microsoft-peering adverteert standaard geen voorvoegsels totdat een routefilter aan het circuit is gekoppeld. Wanneer u een routefilterregel maakt, u de lijst met servicecommunity's opgeven voor Azure-regio's die u wilt gebruiken voor Azure PaaS-services. Dit biedt u de flexibiliteit om de routes te filteren volgens uw eis, zoals weergegeven in de volgende schermafbeelding:

![Openbaar peering samenvoegen](./media/how-to-move-peering/routefilter.jpg)

Routefilters configureren met een van de volgende artikelen:

* [Routefilters configureren voor Microsoft-peering met Azure-portal](how-to-routefilter-portal.md)<br>
* [Routefilters configureren voor Microsoft-peering met Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Routefilters configureren voor Microsoft-peering met Azure CLI](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Verwijder de openbare peering

Nadat u hebt gecontroleerd of de Microsoft-peering is geconfigureerd en de voorvoegsels die u wilt consumeren correct zijn geadverteerd op Microsoft-peering, u vervolgens de openbare peering verwijderen. Als u het openbare peering wilt verwijderen, gebruikt u een van de volgende artikelen:

* [Azure public peering verwijderen met Azure PowerShell](about-public-peering.md#powershell)
* [Azure public peering verwijderen met CLI](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Peerings bekijken
  
U een lijst met alle ExpressRoute-circuits en peerings bekijken in de Azure-portal. Zie [Microsoft-peeringdetails bekijken](expressroute-howto-routing-portal-resource-manager.md#getmsft)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over ExpressRoute.
