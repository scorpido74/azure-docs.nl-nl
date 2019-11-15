---
title: 'Azure-ExpressRoute: peering configureren'
description: In dit artikel worden de stappen beschreven voor het maken en inrichten van ExpressRoute persoonlijke en micro soft-peering. In dit artikel wordt ook gedemonstreerd hoe u de status van een circuit controleert, bijwerkt of verwijdert.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.openlocfilehash: 5fb728cccd77d0cefd10c124cb7215dc3b880fe3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083525"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Peering voor een ExpressRoute-circuit maken en wijzigen

Dit artikel helpt u bij het maken en beheren van routerings configuratie voor een ExpressRoute-circuit met Azure Resource Manager (ARM) met behulp van de Azure Portal. U kunt ook controleren op de status, bijwerken of verwijderen en inrichting van peerings voor een ExpressRoute-circuit ongedaan maken. Als u wilt een andere methode gebruiken om te werken met uw circuit, selecteert u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - Privépeering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - openbare peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-routing-classic.md)
> 

U kunt Azure private en micro soft-peering configureren voor een ExpressRoute-circuit (open bare Azure-peering is afgeschaft voor nieuwe circuits). U kunt peerings configureren in elke gewenste volgorde. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. Zie [over circuits en peerings](expressroute-circuit-peerings.md)voor meer informatie over routerings domeinen en peerings.

## <a name="configuration-prerequisites"></a>Configuratievereisten

* Zorg dat u de pagina met [vereisten](expressroute-prerequisites.md), de pagina over [routeringsvereisten](expressroute-routing.md) en de pagina over [werkstromen](expressroute-workflows.md) hebt gelezen voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Als u peering (s) wilt configureren, moet het ExpressRoute-Circuit de status ingericht en ingeschakeld hebben. 
* Als u een gedeelde sleutel/MD5-hash wilt gebruiken, moet u deze aan beide zijden van de tunnel gebruiken en het aantal alfanumerieke tekens beperken tot een maximum van 25. Speciale tekens worden niet ondersteund. 

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u een serviceprovider die beheerde laag-3-services (meestal een IPVPN, zoals MPLS), uw connectiviteitsprovider configureert en beheert routering voor u. 

> [!IMPORTANT]
> Op dit moment bieden we nog geen peerings aan die door serviceproviders worden geconfigureerd via de beheerportal van de service. Deze mogelijkheid zal binnenkort worden ingeschakeld. Neem contact op met uw serviceprovider voordat u BGP-peerings configureert.
> 
> 

## <a name="msft"></a>Microsoft-peering

In deze sectie helpt u bij het maken, ophalen, bijwerken en verwijderen van de configuratie voor de Microsoft-peering voor een ExpressRoute-circuit.

> [!IMPORTANT]
> Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017 hebben alle service-voorvoegsels geadverteerd via de Microsoft-peering, zelfs als routefilters zijn niet gedefinieerd. Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017 hebben geen voorvoegsels geadverteerd totdat een routefilter is gekoppeld aan het circuit. Zie voor meer informatie, [een routefilter voor Microsoft-peering configureren](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. Configureer het ExpressRoute-circuit. Controleer de **provider status** om er zeker van te zijn dat het circuit volledig is ingericht door de connectiviteits provider voordat u verder gaat.

   Als uw connectiviteitsprovider beheerde laag-3-services biedt, kunt u uw connectiviteitsprovider om in te schakelen van Microsoft-peering voor u vragen. In dat geval hoeft u de instructies in de volgende secties niet uit te voeren. Als uw connectiviteits provider echter geen route ring voor u beheert, voert u de volgende stappen uit nadat u het circuit hebt gemaakt.

   **Status van circuit provider: niet ingericht**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Status van circuit provider: ingericht**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Configureer Microsoft-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verder gaat.

   * Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. In dit subnet wordt u het eerste bruikbaar IP-adres toewijzen aan uw router, zoals Microsoft het tweede bruikbaar IP-adres voor de router gebruikt.
   * Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. In dit subnet wordt u het eerste bruikbaar IP-adres toewijzen aan uw router, zoals Microsoft het tweede bruikbaar IP-adres voor de router gebruikt.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Voor zowel de primaire en secundaire koppelingen moet u de dezelfde VLAN-ID.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. Als u van plan bent om een set voorvoegsels te verzenden, kunt u een door komma's gescheiden lijst verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.
   * **Optioneel:** klant-ASN: als u voorvoegsels adverteert die niet zijn geregistreerd op de AS-nummer peering, kunt u het AS-nummer waaraan ze zijn geregistreerd.
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * **Optioneel:** een MD5-hash, als u ervoor kiest een te gebruiken.
3. U kunt selecteren om de peering die u configureren, wilt zoals wordt weergegeven in het volgende voorbeeld. Selecteer de rij voor Microsoft-peering.

   [![Selecteer de rij met micro soft-peering](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Selecteer de rij met micro soft-peering")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configureer Microsoft-peering. **Sla** de configuratie op nadat u alle para meters hebt opgegeven. De volgende afbeelding toont een voorbeeld configuratie:

   ![Microsoft-peering configureren](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Micro soft controleert of de opgegeven ' aangekondigde open bare voor voegsels ' en ' peer-ASN ' (of ' klant-ASN ') aan u zijn toegewezen in het Internet Routing REGI ster. Als u de open bare voor voegsels van een andere entiteit krijgt en de toewijzing niet is vastgelegd met het routerings register, wordt de automatische validatie niet voltooid en moet hand matig worden gevalideerd. Als de automatische validatie mislukt, wordt het bericht validatie vereist weer gegeven. 
>
> Als u het bericht validatie vereist ziet, verzamelt u de documenten die de open bare voor voegsels bevatten, worden toegewezen aan uw organisatie door de entiteit die wordt vermeld als eigenaar van de voor voegsels in het routerings register en deze documenten voor hand matige validatie indienen door een ondersteunings ticket openen zoals hieronder wordt weer gegeven. 
>

   Als uw circuit een status ' validatie vereist ' krijgt, moet u een ondersteunings ticket openen om het eigendom van de voor voegsels van het ondersteunings team weer te geven. U kunt een ondersteuningsticket rechtstreeks vanuit de portal, zoals wordt weergegeven in het volgende voorbeeld:

   ![Validatie vereist: ondersteunings ticket](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Nadat de configuratie is geaccepteerd, ziet u iets zoals in de volgende afbeelding:

   ![Status van peering: geconfigureerd](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Status van peering: geconfigureerd")]

### <a name="getmsft"></a>Details van Microsoft-peering weergeven

U kunt de eigenschappen van micro soft-peering weer geven door de rij voor de peering te selecteren.

[![Eigenschappen van micro soft-peering weer geven](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Eigenschappen weergeven")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Configuratie van Microsoft-peering bijwerken

U kunt de rij selecteren voor de peering die u wilt wijzigen, vervolgens de eigenschappen van de peering wijzigen en uw wijzigingen opslaan.

![Rij met peering selecteren](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Microsoft-peering verwijderen

U kunt de peering-configuratie verwijderen door te klikken op het pictogram verwijderen, zoals wordt weer gegeven in de volgende afbeelding:

![Peering verwijderen](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Persoonlijke Azure-peering

In deze sectie helpt u bij het maken, ophalen, bijwerken en verwijderen van de Azure private peering configuratie voor een ExpressRoute-circuit.

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. Configureer het ExpressRoute-circuit. Zorg dat het circuit volledig is ingericht door de connectiviteitsprovider voordat u verder gaat. 

   Als uw connectiviteitsprovider beheerde laag-3-services biedt, kunt u uw connectiviteitsprovider om in te schakelen persoonlijke Azure-peering voor u vragen. In dat geval hoeft u de instructies in de volgende secties niet uit te voeren. Als uw connectiviteits provider echter geen route ring voor u beheert, gaat u na het maken van het circuit verder met de volgende stappen.

   **Status van circuit provider: niet ingericht**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Status van circuit provider: ingericht**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configureer persoonlijke Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

   * Een /30-subnet voor de primaire koppeling. Het subnet moet deel uitmaken van een adresruimte gereserveerd voor virtuele netwerken niet. In dit subnet wordt u het eerste bruikbaar IP-adres toewijzen aan uw router, zoals Microsoft het tweede bruikbaar IP-adres voor de router gebruikt.
   * Een /30-subnet voor de secundaire koppeling. Het subnet moet deel uitmaken van een adresruimte gereserveerd voor virtuele netwerken niet. In dit subnet wordt u het eerste bruikbaar IP-adres toewijzen aan uw router, zoals Microsoft het tweede bruikbaar IP-adres voor de router gebruikt.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Voor zowel de primaire en secundaire koppelingen moet u de dezelfde VLAN-ID.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een privé AS-nummer gebruiken voor deze peering, behalve voor het aantal van 65515 tot 65520, inclusief.
   * U moet de routes van uw on-premises edge-router naar Azure aankondigen via BGP bij het instellen van de persoonlijke peering.
   * **Optioneel:** een MD5-hash, als u ervoor kiest een te gebruiken.
3. Selecteer de rij met persoonlijke Azure-peering, zoals wordt weer gegeven in het volgende voor beeld:

   [![Selecteer de rij met persoonlijke peering](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Selecteer de rij met persoonlijke peering")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configureer persoonlijke Azure-peering. **Sla** de configuratie op nadat u alle para meters hebt opgegeven.

   ![Configureer persoonlijke Azure-peering](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Nadat de configuratie is geaccepteerd, ziet er ongeveer uitzien als in het volgende voorbeeld:

   ![privé-peering opgeslagen](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Details van een Azure private peering weergeven

U kunt de eigenschappen van persoonlijke Azure-peering weergeven door de peering te selecteren.

[![Eigenschappen van privé-peering weer geven](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Eigenschappen van privé-peering weer geven")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Configuratie van een Azure private peering bijwerken

U kunt de rij voor peering selecteren en de eigenschappen van de peering wijzigen. Sla uw wijzigingen na het bijwerken op.

![privé-peering bijwerken](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Persoonlijke Azure-peering verwijderen

U kunt de peeringconfiguratie verwijderen door het verwijderingspictogram te selecteren zoals wordt weergegeven in de volgende afbeelding:

> [!WARNING]
> U moet ervoor zorgen dat alle virtuele netwerken en globale bereiken ExpressRoute-verbindingen worden verwijderd voordat u het volgende voorbeeld uitvoert. 
> 
> 

![privé-peering verwijderen](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Openbare Azure-peering

In deze sectie helpt u bij het maken, ophalen, bijwerken en verwijderen van de Azure openbare peering configuratie voor een ExpressRoute-circuit.

> [!Note]
> Open bare Azure-peering is afgeschaft voor nieuwe circuits. Zie [ExpressRoute peering](expressroute-circuit-peerings.md)(Engelstalig) voor meer informatie.
>

### <a name="getpublic"></a>Details van een Azure openbare peering weergeven

De eigenschappen van open bare Azure-peering weer geven door de peering te selecteren.

### <a name="updatepublic"></a>Azure-configuratie in openbare peering bijwerken

Selecteer de rij voor peering en wijzig vervolgens de eigenschappen van de peering.

### <a name="deletepublic"></a>Openbare Azure-peering verwijderen

Verwijder de peering-configuratie door het pictogram verwijderen te selecteren.

## <a name="next-steps"></a>Volgende stappen

Volgende stap, [een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-portal-resource-manager.md)
* Voor meer informatie over ExpressRoute-werkstromen raadpleegt u [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-werkstromen).
* Voor meer informatie over circuitpeering raadpleegt u [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).
* Bekijk het [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtual Network-overzicht) voor meer informatie over het gebruik van virtuele netwerken.
