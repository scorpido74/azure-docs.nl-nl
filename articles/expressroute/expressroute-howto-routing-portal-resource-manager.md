---
title: 'Azure-ExpressRoute: peering configureren'
description: In dit artikel worden de stappen beschreven voor het maken en inrichten van ExpressRoute persoonlijke en micro soft-peering. In dit artikel wordt ook gedemonstreerd hoe u de status van een circuit controleert, bijwerkt of verwijdert.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/13/2019
ms.author: duau
ms.openlocfilehash: ccbd9645ac7d331c06e528298b3a45a184c6cc49
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652224"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Peering voor een ExpressRoute-circuit maken en wijzigen

Dit artikel helpt u bij het maken en beheren van routerings configuratie voor een ExpressRoute-circuit met Azure Resource Manager (ARM) met behulp van de Azure Portal. U kunt ook de status van peerings voor een ExpressRoute-circuit controleren, bijwerken of verwijderen en de inrichting ongedaan maken. Als u een andere methode wilt gebruiken om met uw circuit te werken, selecteert u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure-CLI](howto-routing-cli.md)
> * [Open bare peering](about-public-peering.md)
> * [Video-persoonlijke peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video-micro soft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-routing-classic.md)
> 

U kunt privé-peering en micro soft-peering configureren voor een ExpressRoute-circuit (open bare Azure-peering is afgeschaft voor nieuwe circuits). Peerings kunnen in elke gewenste volg orde worden geconfigureerd. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. Voor meer informatie over routerings domeinen en peerings, Zie [ExpressRoute-routerings domeinen](expressroute-circuit-peerings.md). Zie [ExpressRoute Public peering](about-public-peering.md)(Engelstalig) voor meer informatie over open bare peering.

## <a name="configuration-prerequisites"></a>Configuratievereisten

* Zorg dat u de pagina met [vereisten](expressroute-prerequisites.md), de pagina over [routeringsvereisten](expressroute-routing.md) en de pagina over [werkstromen](expressroute-workflows.md) hebt gelezen voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Als u peering (s) wilt configureren, moet het ExpressRoute-Circuit de status ingericht en ingeschakeld hebben. 
* Als u een gedeelde sleutel/MD5-hash wilt gebruiken, moet u deze aan beide zijden van de tunnel gebruiken en het aantal alfanumerieke tekens beperken tot een maximum van 25. Speciale tekens worden niet ondersteund. 

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u een service provider gebruikt die Managed Layer 3-services biedt (meestal een IPVPN, zoals MPLS), wordt door uw connectiviteits provider route ring voor u geconfigureerd en beheerd. 

> [!IMPORTANT]
> Op dit moment bieden we nog geen peerings aan die door serviceproviders worden geconfigureerd via de beheerportal van de service. Deze mogelijkheid zal binnenkort worden ingeschakeld. Neem contact op met uw service provider voordat u BGP-peerings configureert.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

In deze sectie leert u hoe u de micro soft-peering-configuratie voor een ExpressRoute-circuit maakt, ontvangt, bijwerkt en verwijdert.

> [!IMPORTANT]
> Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle service voorvoegsels die worden geadverteerd via de micro soft-peering, zelfs als er geen route filters zijn gedefinieerd. Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voor voegsels die worden geadverteerd totdat een route filter aan het circuit is gekoppeld. Zie [Configure a route filter for micro soft peering](how-to-routefilter-powershell.md)(Engelstalig) voor meer informatie.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. Configureer het ExpressRoute-circuit. Controleer de **provider status** om er zeker van te zijn dat het circuit volledig is ingericht door de connectiviteits provider voordat u verder gaat.

   Als uw connectiviteits provider beheerde Layer 3-services biedt, kunt u uw connectiviteits provider vragen om micro soft-peering voor u in te scha kelen. In dat geval hoeft u de instructies in de volgende secties niet uit te voeren. Als uw connectiviteits provider echter geen route ring voor u beheert, voert u de volgende stappen uit nadat u het circuit hebt gemaakt.

   **Status van circuit provider: niet ingericht**

   [![Scherm afbeelding met de overzichts pagina voor het ExpressRoute-demo circuit met een rood vak waarin de provider status is ingesteld op niet-ingericht.](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **Status van circuit provider: ingericht**

   [![Scherm afbeelding met de overzichts pagina voor het ExpressRoute-demo circuit met een rood vak waarin de provider status is ingesteld op ingericht.](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. Configureer Microsoft-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.

   * Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. Vanuit dit subnet wijst u het eerste bebruikbaar bare IP-adres toe aan uw router, aangezien micro soft gebruikmaakt van het tweede bebruikbaarde IP voor de router.
   * Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. Vanuit dit subnet wijst u het eerste bebruikbaar bare IP-adres toe aan uw router, aangezien micro soft gebruikmaakt van het tweede bebruikbaarde IP voor de router.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Voor zowel primaire als secundaire koppelingen moet u dezelfde VLAN-ID gebruiken.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. Als u van plan bent een aantal voor voegsels te verzenden, kunt u een door komma's gescheiden lijst verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.
   * **Optioneel-** Klant-ASN: als u voor voegsels adverteert die niet zijn geregistreerd voor het peering als-nummer, kunt u het AS-nummer opgeven waarop ze zijn geregistreerd.
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * **Optioneel-** Een MD5-hash als u ervoor kiest om er een te gebruiken.
3. U kunt de peering selecteren die u wilt configureren, zoals wordt weer gegeven in het volgende voor beeld. Selecteer de rij voor Microsoft-peering.

   [![Selecteer de rij met micro soft-peering](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Selecteer de rij met micro soft-peering")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configureer Microsoft-peering. **Sla** de configuratie op nadat u alle para meters hebt opgegeven. De volgende afbeelding toont een voorbeeld configuratie:

   ![Microsoft-peering configureren](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Micro soft controleert of de opgegeven ' aangekondigde open bare voor voegsels ' en ' peer-ASN ' (of ' klant-ASN ') aan u zijn toegewezen in het Internet Routing REGI ster. Als u de open bare voor voegsels van een andere entiteit krijgt en de toewijzing niet is vastgelegd met het routerings register, wordt de automatische validatie niet voltooid en moet hand matig worden gevalideerd. Als de automatische validatie mislukt, wordt het bericht validatie vereist weer gegeven. 
>
> Als u het bericht validatie vereist ziet, verzamelt u de documenten die de open bare voor voegsels bevatten, worden toegewezen aan uw organisatie door de entiteit die wordt vermeld als de eigenaar van de voor voegsels in het routerings register en verzendt deze documenten voor hand matige validatie door een ondersteunings ticket te openen, zoals hieronder wordt weer gegeven. 
>

   Als uw circuit een status ' validatie vereist ' krijgt, moet u een ondersteunings ticket openen om het eigendom van de voor voegsels van het ondersteunings team weer te geven. U kunt een ondersteunings ticket rechtstreeks vanuit de portal openen, zoals wordt weer gegeven in het volgende voor beeld:

   ![Validatie vereist: ondersteunings ticket](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Nadat de configuratie is geaccepteerd, ziet u iets zoals in de volgende afbeelding:

   ![Status van peering: geconfigureerd](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Status van peering: geconfigureerd")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>De details van Microsoft-peering weergeven

U kunt de eigenschappen van micro soft-peering weer geven door de rij voor de peering te selecteren.

[![Eigenschappen van micro soft-peering weer geven](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Eigenschappen weergeven")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Configuratie van Microsoft-peering bijwerken

U kunt de rij selecteren voor de peering die u wilt wijzigen, vervolgens de eigenschappen van de peering wijzigen en uw wijzigingen opslaan.

![Rij met peering selecteren](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft-peering verwijderen

U kunt de peering-configuratie verwijderen door te klikken op het pictogram verwijderen, zoals wordt weer gegeven in de volgende afbeelding:

![Peering verwijderen](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Persoonlijke Azure-peering

In deze sectie leert u hoe u de persoonlijke Azure-peering-configuratie voor een ExpressRoute-circuit maakt, ontvangt, bijwerkt en verwijdert.

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. Configureer het ExpressRoute-circuit. Zorg dat het circuit volledig is ingericht door de connectiviteitsprovider voordat u verder gaat. 

   Als uw connectiviteits provider beheerde Layer 3-services biedt, kunt u uw connectiviteits provider vragen om persoonlijke Azure-peering voor u in te scha kelen. In dat geval hoeft u de instructies in de volgende secties niet uit te voeren. Als uw connectiviteits provider echter geen route ring voor u beheert, gaat u na het maken van het circuit verder met de volgende stappen.

   **Status van circuit provider: niet ingericht**

   [![Scherm opname van de pagina overzicht voor het ExpressRoute-demo circuit met een rood vak met de status van de provider die is ingesteld op niet-ingericht.](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Status van circuit provider: ingericht**

   [![Scherm opname van de pagina overzicht voor het ExpressRoute-demo circuit met een rood vak met de status van de provider die is ingesteld op ingericht.](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configureer persoonlijke Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

   * Een /30-subnet voor de primaire koppeling. Het subnet mag geen deel uitmaken van een adres ruimte die is gereserveerd voor virtuele netwerken. Vanuit dit subnet wijst u het eerste bebruikbaar bare IP-adres toe aan uw router, aangezien micro soft gebruikmaakt van het tweede bebruikbaarde IP voor de router.
   * Een /30-subnet voor de secundaire koppeling. Het subnet mag geen deel uitmaken van een adres ruimte die is gereserveerd voor virtuele netwerken. Vanuit dit subnet wijst u het eerste bebruikbaar bare IP-adres toe aan uw router, aangezien micro soft gebruikmaakt van het tweede bebruikbaarde IP voor de router.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Voor zowel primaire als secundaire koppelingen moet u dezelfde VLAN-ID gebruiken.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een privé AS-nummer gebruiken voor deze peering, behalve voor het aantal van 65515 tot 65520, inclusief.
   * U moet de routes van uw on-premises edge-router naar Azure aankondigen via BGP bij het instellen van de persoonlijke peering.
   * **Optioneel-** Een MD5-hash als u ervoor kiest om er een te gebruiken.
3. Selecteer de rij met persoonlijke Azure-peering, zoals wordt weer gegeven in het volgende voor beeld:

   [![Selecteer de rij met persoonlijke peering](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Selecteer de rij met persoonlijke peering")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configureer persoonlijke Azure-peering. **Sla** de configuratie op nadat u alle para meters hebt opgegeven.

   ![privé-peering configureren](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Nadat de configuratie is geaccepteerd, ziet u iets zoals in het volgende voor beeld:

   ![privé-peering opgeslagen](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>De details van persoonlijke Azure-peering weergeven

U kunt de eigenschappen van persoonlijke Azure-peering weergeven door de peering te selecteren.

[![Eigenschappen van privé-peering weer geven](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Eigenschappen van privé-peering weer geven")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>De configuratie van persoonlijke Azure-peering bijwerken

U kunt de rij voor peering selecteren en de eigenschappen van de peering wijzigen. Sla uw wijzigingen na het bijwerken op.

![privé-peering bijwerken](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Persoonlijke Azure-peering verwijderen

U kunt de peering-configuratie verwijderen door het pictogram verwijderen te selecteren, zoals wordt weer gegeven in de volgende afbeelding:

> [!WARNING]
> U moet ervoor zorgen dat alle virtuele netwerken en ExpressRoute Global Reach verbindingen worden verwijderd voordat u dit voor beeld uitvoert. 
> 
> 

![privé-peering verwijderen](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Volgende stappen

Volgende stap [een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-portal-resource-manager.md)
* Voor meer informatie over ExpressRoute-werkstromen raadpleegt u [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-werkstromen).
* Voor meer informatie over circuitpeering raadpleegt u [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).
* Bekijk het [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtual Network-overzicht) voor meer informatie over het gebruik van virtuele netwerken.
