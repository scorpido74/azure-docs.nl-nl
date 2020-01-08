---
title: 'Azure ExpressRoute: een open bare peering verplaatsen naar micro soft-peering'
description: Dit artikel laat u de stappen voor het verplaatsen van uw openbare peering aan Microsoft-peering voor ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436846"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Verplaatsen van een openbare peering naar het Microsoft-peering

Dit artikel helpt u de configuratie van een openbare peering naar Microsoft-peering zonder uitvaltijd. ExpressRoute biedt ondersteuning voor het gebruik van Microsoft-peering met routefilters voor Azure PaaS-services, zoals Azure Storage en Azure SQL Database. U hebt nu maar één routeringsdomein nodig voor toegang tot Microsoft PaaS- en SaaS-services. U kunt routefilters gebruiken om de PaaS-servicevoorvoegsels selectief te adverteren voor Azure-regio's die u wilt verbruiken.

Open bare Azure-peering heeft één NAT IP-adres dat is gekoppeld aan elke BGP-sessie. Met micro soft-peering kunt u uw eigen NAT-toewijzingen configureren, maar ook route filters gebruiken voor selectieve voor voegsels van advertenties. Open bare peering is een service in één richting waarmee de connectiviteit altijd vanuit uw WAN naar Microsoft Azure Services wordt gestart. Microsoft Azure-services worden niet verbindingen in uw netwerk via de deze routeringsdomein kunnen initiëren.

Zodra de openbare peering is ingeschakeld, kunt u verbinding met alle Azure-services. We kunt niet u selectief kiezen services waarvoor we routes te adverteren. Micro soft-peering is een bidirectionele connectiviteit waarbij verbinding kan worden gestart vanuit Microsoft Azure service samen met uw WAN. Zie voor meer informatie over routerings domeinen en peering [ExpressRoute-circuits en routerings domeinen](expressroute-circuit-peerings.md).

## <a name="before"></a>Voordat u begint

Voor verbinding met Microsoft-peering, moet u instellen en beheren van NAT bevinden. Uw connectiviteitsprovider kan instellen en beheren van de NAT als een beheerde service. Als u van plan bent voor toegang tot het Azure PaaS en SaaS van de Azure-services op Microsoft-peering, is het belangrijk om de grootte van de NAT IP-adresgroep correct. Zie voor meer informatie over NAT voor ExpressRoute, de [NAT-vereisten voor Microsoft-peering](expressroute-nat.md#nat-requirements-for-microsoft-peering). Wanneer u verbinding maakt met micro soft via Azure ExpressRoute (micro soft-peering), hebt u meerdere koppelingen naar micro soft. De ene koppeling is uw bestaande internetverbinding en de andere gaat via ExpressRoute. Bepaald verkeer naar Microsoft kan via internet gaan maar kan terugkeren via ExpressRoute of andersom.

![Bidirectionele connectiviteit](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> De NAT IP-adresgroep die wordt geadverteerd aan Microsoft mag niet worden geadverteerd op internet. Dit verbreekt de connectiviteit met andere Microsoft-services.

Raadpleeg [asymmetrische route ring met meerdere netwerk paden](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) voor voor behoud van asymmetrische route ring voordat u micro soft-peering configureert.

* Als u openbare peering en momenteel zijn IP-netwerkregels voor openbare IP-adressen die worden gebruikt voor toegang tot [Azure Storage](../storage/common/storage-network-security.md) of [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), moet u ervoor zorgen dat de NAT IP-adresgroep geconfigureerd met Microsoft is-peering opgenomen in de lijst met openbare IP-adressen voor de Azure-opslagaccount of Azure SQL-account.<br>
* Als u wilt verplaatsen naar Microsoft-peering zonder uitvaltijd, gebruik de stappen in dit artikel in de volgorde waarin ze worden weergegeven.

## <a name="create"></a>1. micro soft-peering maken

Als Microsoft-peering niet is gemaakt, kunt u een van de volgende artikelen te maken met het Microsoft-peering gebruiken. Als uw connectivity provider biedt beheerde layer 3-services, kunt u de connectiviteitsprovider om in te schakelen van Microsoft-peering voor uw circuit vragen.

Als de laag 3 door u wordt beheerd, is de volgende informatie vereist voordat u doorgaat:

* Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. In dit subnet wordt u het eerste bruikbaar IP-adres toewijzen aan uw router, zoals Microsoft het tweede bruikbaar IP-adres voor de router gebruikt.<br>
* Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. In dit subnet wordt u het eerste bruikbaar IP-adres toewijzen aan uw router, zoals Microsoft het tweede bruikbaar IP-adres voor de router gebruikt.<br>
* Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Voor zowel de primaire en secundaire koppelingen moet u de dezelfde VLAN-ID.<br>
* AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.<br>
* Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. Als u van plan bent om een set voorvoegsels te verzenden, kunt u een door komma's gescheiden lijst verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.<br>
* Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.

* **Optioneel** : klant-ASN: als u voor voegsels adverteert die niet zijn geregistreerd voor het peering als-nummer, kunt u het as-nummer opgeven waarop ze zijn geregistreerd.<br>
* **Optioneel** : een MD5-hash als u ervoor kiest om er een te gebruiken.

Gedetailleerde instructies voor het inschakelen van micro soft-peering vindt u in de volgende artikelen:

* [Microsoft-peering maken met Azure portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Microsoft-peering maken met Azure Powershell](expressroute-howto-routing-arm.md#msft)<br>
* [Microsoft-peering maken met Azure CLI](howto-routing-cli.md#msft)

## <a name="validate"></a>2. valideren of micro soft-peering is ingeschakeld

Controleer of de Microsoft-peering is ingeschakeld en het aangekondigde openbare voorvoegsels zijn in de geconfigureerde status.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure-CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. een route filter configureren en koppelen aan het circuit

Nieuwe micro soft-peering adverteert standaard geen voor voegsels totdat een route filter aan het circuit is gekoppeld. Wanneer u een route filter regel maakt, kunt u de lijst met Service community's opgeven voor Azure-regio's die u wilt gebruiken voor Azure PaaS Services. Dit biedt u de flexibiliteit om de routes te filteren op basis van uw vereiste, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Openbare peering samenvoegen](./media/how-to-move-peering/routefilter.jpg)

Configureer routefilters met behulp van een van de volgende artikelen:

* [Routefilters voor Microsoft-peering met behulp van Azure portal configureren](how-to-routefilter-portal.md)<br>
* [Routefilters voor Microsoft-peering met behulp van Azure PowerShell configureren](how-to-routefilter-powershell.md)<br>
* [Routefilters voor Microsoft-peering met behulp van Azure CLI configureren](how-to-routefilter-cli.md)

## <a name="delete"></a>4. de open bare peering verwijderen

Nadat u hebt gecontroleerd dat de Microsoft-peering is geconfigureerd en de voorvoegsels die u wilt gebruiken voor het correct op de Microsoft-peering worden geadverteerd, kunt u vervolgens de openbare peering verwijderen. Als u wilt de openbare peering verwijderen, gebruikt u een van de volgende artikelen:

* [Verwijderen van de openbare Azure-peering met behulp van Azure PowerShell](about-public-peering.md#powershell)
* [Verwijderen van de openbare Azure-peering met behulp van CLI](about-public-peering.md#cli)
  
## <a name="view"></a>5. peerings weer geven
  
U ziet een lijst met alle ExpressRoute-circuits en -peerings in Azure portal. Zie voor meer informatie, [details van de weergave-Microsoft-peering](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).
