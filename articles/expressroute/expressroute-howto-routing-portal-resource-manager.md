---
title: 'Azure ExpressRoute: peering configureren'
description: In dit artikel worden de stappen voor het maken en inrichten van Privé- en Microsoft-peering voor ExpressRoute documenteren. In dit artikel wordt ook uitgelegd hoe u de status controleren, bijwerken of peeringen voor een circuit verwijderen.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: mialdrid
ms.openlocfilehash: 18d2db18e9880028c60b4b545c3628f4a9cb4703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264789"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Peering voor een ExpressRoute-circuit maken en wijzigen

Met dit artikel u routeringsconfiguratie maken en beheren voor een Azure Resource Manager (ARM) ExpressRoute-circuit met behulp van de Azure-portal. U ook de status controleren, bijwerken of peerings verwijderen en deprovisionen voor een ExpressRoute-circuit. Als u een andere methode wilt gebruiken om met uw circuit te werken, selecteert u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Azure-CLI](howto-routing-cli.md)
> * [Openbare peering](about-public-peering.md)
> * [Video - Privé-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-routing-classic.md)
> 

U private peering en Microsoft-peering configureren voor een ExpressRoute-circuit (Azure public peering is afgeschaft voor nieuwe circuits). Peerings kunnen worden geconfigureerd in elke volgorde die u kiest. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. Zie [Routeringsdomeinen expressroute](expressroute-circuit-peerings.md)voor meer informatie over routeringsdomeinen en peerings. Zie [ExpressRoute public peering](about-public-peering.md)voor informatie over openbare peering.

## <a name="configuration-prerequisites"></a>Configuratievereisten

* Zorg dat u de pagina met [vereisten](expressroute-prerequisites.md), de pagina over [routeringsvereisten](expressroute-routing.md) en de pagina over [werkstromen](expressroute-workflows.md) hebt gelezen voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Om peering(s) te configureren, moet het ExpressRoute-circuit in een ingerichte en ingeschakelde status staan. 
* Als u van plan bent een gedeelde sleutel/MD5-hash te gebruiken, moet u deze aan beide zijden van de tunnel gebruiken en het aantal alfanumerieke tekens beperken tot maximaal 25. Speciale tekens worden niet ondersteund. 

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u een serviceprovider gebruikt die beheerde Layer 3-services aanbiedt (meestal een IPVPN, zoals MPLS), configureert en beheert uw connectiviteitsprovider routering voor u. 

> [!IMPORTANT]
> Op dit moment bieden we nog geen peerings aan die door serviceproviders worden geconfigureerd via de beheerportal van de service. Deze mogelijkheid zal binnenkort worden ingeschakeld. Neem contact op met uw serviceprovider voordat u BGP-peerings configureert.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

Met deze sectie u de Microsoft-peeringconfiguratie voor een ExpressRoute-circuit maken, ontvangen, bijwerken en verwijderen.

> [!IMPORTANT]
> Microsoft-peering van ExpressRoute-circuits die vóór 1 augustus 2017 zijn geconfigureerd, hebben alle servicevoorvoegsels die worden geadverteerd via de Microsoft-peering, zelfs als routefilters niet zijn gedefinieerd. Microsoft-peering van ExpressRoute-circuits die op of na 1 augustus 2017 zijn geconfigureerd, worden pas geadverteerd als er een routefilter aan het circuit is gekoppeld. Zie [Een routefilter configureren voor Microsoft-peering voor](how-to-routefilter-powershell.md)meer informatie.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. Configureer het ExpressRoute-circuit. Controleer de **status van provider** om ervoor te zorgen dat het circuit volledig is ingericht door de connectiviteitsprovider voordat u verder gaat.

   Als uw connectiviteitsprovider beheerde Layer 3-services aanbiedt, u uw connectiviteitsprovider vragen microsoft-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider echter geen routering voor u beheert, gaat u na het maken van uw circuit verder met deze stappen.

   **Circuit - Provider status: Niet ingericht**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Circuit - Provider status: Ingericht**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Configureer Microsoft-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.

   * Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. Vanuit dit subnet wijst u het eerste bruikbare IP-adres toe aan uw router, omdat Microsoft het tweede bruikbare IP voor zijn router gebruikt.
   * Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. Vanuit dit subnet wijst u het eerste bruikbare IP-adres toe aan uw router, omdat Microsoft het tweede bruikbare IP voor zijn router gebruikt.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Voor zowel primaire als secundaire koppelingen moet u dezelfde VLAN-id gebruiken.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. Als u van plan bent een set voorvoegsels te verzenden, u een door komma's gescheiden lijst verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.
   * **Optioneel -** Klant ASN: Als u reclame maakt voorvoegsels die niet zijn geregistreerd op het as-nummer peering, u het AS-nummer opgeven waarop ze zijn geregistreerd.
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * **Optioneel -** Een MD5 hash als u ervoor kiest om er een te gebruiken.
3. U het peering selecteren dat u wilt configureren, zoals in het volgende voorbeeld wordt weergegeven. Selecteer de rij voor Microsoft-peering.

   [![De rij Microsoft-peering selecteren](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "De rij Microsoft-peering selecteren")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configureer Microsoft-peering. **Sla** de configuratie op zodra u alle parameters hebt opgegeven. In de volgende afbeelding ziet u een voorbeeldconfiguratie:

   ![Microsoft-peering configureren](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft controleert of de opgegeven 'Geadverteerde openbare voorvoegsels' en 'Peer ASN' (of 'Klant ASN') aan u zijn toegewezen in het internetrouteringsregister. Als u de openbare voorvoegsels van een andere entiteit ontvangt en als de toewijzing niet wordt geregistreerd met het routeringsregister, wordt de automatische validatie niet voltooid en vereist de handmatige validatie. Als de automatische validatie mislukt, ziet u het bericht 'Validatie nodig'. 
>
> Als u het bericht 'Validatie nodig' ziet, verzamelt u het document(en) waarop de openbare voorvoegsels worden weergegeven, worden toegewezen aan uw organisatie door de entiteit die wordt vermeld als de eigenaar van de voorvoegsels in het routeringsregister en dient u deze documenten in voor handmatige validatie door het openen van een support ticket zoals hieronder weergegeven. 
>

   Als uw circuit een status 'Validatie nodig' krijgt, moet u een ondersteuningsticket openen om het bewijs van eigendom van de voorvoegsels aan ons ondersteuningsteam te tonen. U een ondersteuningsticket rechtstreeks openen vanaf de portal, zoals in het volgende voorbeeld wordt weergegeven:

   ![Validatie nodig - ondersteuningsticket](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Nadat de configuratie is geaccepteerd, ziet u iets dat lijkt op de volgende afbeelding:

   ![Peering-status: geconfigureerd](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Peering-status: geconfigureerd")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>De details van Microsoft-peering weergeven

U de eigenschappen van Microsoft-peering bekijken door de rij voor peering te selecteren.

[![Microsoft-peering-eigenschappen weergeven](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Eigenschappen weergeven")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Configuratie van Microsoft-peering bijwerken

U de rij selecteren voor het peering dat u wilt wijzigen, vervolgens de peering-eigenschappen wijzigen en uw wijzigingen opslaan.

![Peering-rij selecteren](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft-peering verwijderen

U uw peeringconfiguratie verwijderen door op het pictogram verwijderen te klikken, zoals in de volgende afbeelding wordt weergegeven:

![Peering verwijderen](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Persoonlijke Azure-peering

Met deze sectie u de Azure private peering-configuratie voor een ExpressRoute-circuit maken, ontvangen, bijwerken en verwijderen.

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. Configureer het ExpressRoute-circuit. Zorg dat het circuit volledig is ingericht door de connectiviteitsprovider voordat u verder gaat. 

   Als uw connectiviteitsprovider beheerde Layer 3-services aanbiedt, u uw connectiviteitsprovider vragen azure private peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider echter geen routering voor u beheert, gaat u na het maken van uw circuit verder met de volgende stappen.

   **Circuit - Provider status: Niet ingericht**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Circuit - Provider status: Ingericht**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configureer persoonlijke Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

   * Een /30-subnet voor de primaire koppeling. Het subnet mag geen deel uitmaken van een adresruimte die is gereserveerd voor virtuele netwerken. Vanuit dit subnet wijst u het eerste bruikbare IP-adres toe aan uw router, omdat Microsoft het tweede bruikbare IP voor zijn router gebruikt.
   * Een /30-subnet voor de secundaire koppeling. Het subnet mag geen deel uitmaken van een adresruimte die is gereserveerd voor virtuele netwerken. Vanuit dit subnet wijst u het eerste bruikbare IP-adres toe aan uw router, omdat Microsoft het tweede bruikbare IP voor zijn router gebruikt.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Voor zowel primaire als secundaire koppelingen moet u dezelfde VLAN-id gebruiken.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U een privé AS-nummer gebruiken voor deze peering, behalve het nummer van 65515 tot 65520, inclusief.
   * U moet de routes van uw on-premises Edge-router naar Azure adverteren via BGP wanneer u de privé-peering instelt.
   * **Optioneel -** Een MD5 hash als u ervoor kiest om er een te gebruiken.
3. Selecteer de rij voor privépeeren van Azure, zoals in het volgende voorbeeld wordt weergegeven:

   [![De rij privé-peering selecteren](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "De rij privé-peering selecteren")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configureer persoonlijke Azure-peering. **Sla** de configuratie op zodra u alle parameters hebt opgegeven.

   ![privé-peering configureren](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Nadat de configuratie is geaccepteerd, ziet u iets dat lijkt op het volgende voorbeeld:

   ![opgeslagen private peering](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>De details van persoonlijke Azure-peering weergeven

U kunt de eigenschappen van persoonlijke Azure-peering weergeven door de peering te selecteren.

[![Privé-peering-eigenschappen weergeven](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Privé-peering-eigenschappen weergeven")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>De configuratie van persoonlijke Azure-peering bijwerken

U kunt de rij voor peering selecteren en de eigenschappen van de peering wijzigen. Sla de wijzigingen op nadat u bent bijgewerkt.

![privé-peering bijwerken](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Persoonlijke Azure-peering verwijderen

U uw peeringconfiguratie verwijderen door het pictogram verwijderen te selecteren, zoals in de volgende afbeelding wordt weergegeven:

> [!WARNING]
> U moet ervoor zorgen dat alle virtuele netwerken en ExpressRoute Global Reach-verbindingen worden verwijderd voordat u dit voorbeeld uitvoert. 
> 
> 

![privépeering verwijderen](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Volgende stappen

Volgende stap, [Koppel een VNet aan een ExpressRoute circuit](expressroute-howto-linkvnet-portal-resource-manager.md)
* Voor meer informatie over ExpressRoute-werkstromen raadpleegt u [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-werkstromen).
* Voor meer informatie over circuitpeering raadpleegt u [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).
* Bekijk het [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtual Network-overzicht) voor meer informatie over het gebruik van virtuele netwerken.
