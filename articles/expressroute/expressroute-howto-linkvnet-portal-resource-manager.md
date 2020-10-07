---
title: 'Zelf studie: een VNet koppelen aan een ExpressRoute-circuit-Azure Portal'
description: Deze zelf studie laat zien hoe u een verbinding kunt maken om een virtueel netwerk te koppelen aan een Azure ExpressRoute-circuit met behulp van de Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: bfb358694cbdd214490fb41052e508b94d10baf4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91773021"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Zelf studie: een virtueel netwerk verbinden met een ExpressRoute-circuit met behulp van de portal

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
> 

Deze zelf studie helpt u bij het maken van een verbinding om een virtueel netwerk te koppelen aan een Azure ExpressRoute-circuit met behulp van de Azure Portal. De virtuele netwerken die u met uw Azure ExpressRoute-circuit verbindt, kunnen zich in hetzelfde abonnement benemen of deel uitmaken van een ander abonnement.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een virtueel netwerk verbinden met een circuit in hetzelfde abonnement.
> - Een virtueel netwerk verbinden met een circuit in een ander abonnement.
> - Verwijder de koppeling tussen het virtuele netwerk en het ExpressRoute-circuit.

## <a name="prerequisites"></a>Vereisten

* Bekijk de [prerequisites](expressroute-prerequisites.md) [vereisten, routerings behoeften](expressroute-routing.md)en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben.
  * Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit ingeschakeld door uw connectiviteits provider.
  * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. Zie het artikel [peering maken en wijzigen voor een ExpressRoute-circuit](expressroute-howto-routing-portal-resource-manager.md) voor informatie over peering en route ring.
  * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd en dat BGP-peering tussen uw netwerk en micro soft wordt ingesteld voor end-to-end-connectiviteit.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerk gateway hebt gemaakt en volledig hebt ingericht. Volg de instructies voor het [maken van een virtuele netwerk gateway voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Een virtuele netwerk gateway voor ExpressRoute maakt gebruik van de gateway type ' ExpressRoute ', niet VPN.

* U kunt Maxi maal 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer een standaard ExpressRoute-circuit wordt gebruikt.

* Eén VNet kan worden gekoppeld aan Maxi maal vier ExpressRoute-circuits. Gebruik de volgende procedure om een nieuw verbindings object te maken voor elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, verschillende abonnementen of een combi natie van beide bevindt.

* Als u de Premium-invoeg toepassing ExpressRoute inschakelt, kunt u virtuele netwerken koppelen buiten de geopolitieke regio van het ExpressRoute-circuit. De Premium-invoeg toepassing biedt u ook de mogelijkheid om meer dan 10 virtuele netwerken te verbinden met uw ExpressRoute-circuit, afhankelijk van de gekozen band breedte. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoeg toepassing.

* U kunt [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) voordat u begint met een beter begrip van de stappen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Een VNet verbinden met een circuit-hetzelfde abonnement

> [!NOTE]
> BGP-configuratie gegevens worden niet weer gegeven als de laag 3-provider uw peerings heeft geconfigureerd. Als uw circuit een ingerichte status heeft, moet u verbindingen kunnen maken.
>

### <a name="to-create-a-connection"></a>Een verbinding maken

1. Zorg ervoor dat uw ExpressRoute-circuit en persoonlijke Azure-peering correct zijn geconfigureerd. Volg de instructies in [een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md) en [peering maken en wijzigen voor een ExpressRoute-circuit](expressroute-howto-routing-arm.md). Uw ExpressRoute-circuit moet eruitzien zoals in de volgende afbeelding:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Scherm opname van ExpressRoute-circuit":::

2. U kunt nu beginnen met het inrichten van een verbinding om uw virtuele netwerk gateway te koppelen aan uw ExpressRoute-circuit. Selecteer **verbinding**  >  **toevoegen** om de pagina **verbinding toevoegen** te openen en configureer de waarden.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Scherm opname van ExpressRoute-circuit":::

3. Nadat de verbinding is geconfigureerd, wordt de informatie voor de verbinding weer gegeven in het verbindings object.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Scherm opname van ExpressRoute-circuit":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Een VNet verbinden met een circuit-ander abonnement

U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. In de volgende afbeelding ziet u hoe delen werkt voor ExpressRoute-circuits voor meerdere abonnementen.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Scherm opname van ExpressRoute-circuit":::

Elk van de kleinere Clouds in de grote Cloud wordt gebruikt om abonnementen weer te geven die deel uitmaken van verschillende afdelingen binnen een organisatie. Elk van de afdelingen in de organisatie gebruikt een eigen abonnement voor het implementeren van hun services, maar ze kunnen een enkel ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk. Eén afdeling (in dit voor beeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen gebruikmaken van het ExpressRoute-circuit.

  > [!NOTE]
  > Connectiviteits-en bandbreedte kosten voor het specifieke circuit worden toegepast op de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde band breedte.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Beheer-informatie over circuit eigen aren en circuit gebruikers

De ' circuit eigenaar ' is een geautoriseerde hoofd gebruiker van de ExpressRoute-circuit resource. De eigenaar van het circuit kan autorisaties maken die kunnen worden ingewisseld door ' circuit gebruikers '. Circuit gebruikers zijn eigen aren van virtuele netwerk gateways die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuit gebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De eigenaar van het circuit heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Het intrekken van een autorisatie resulteert in alle koppelings verbindingen die worden verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Bewerkingen voor circuit eigenaars

**Een verbindings autorisatie maken**

De eigenaar van het circuit maakt een autorisatie, waarmee een autorisatie sleutel wordt gemaakt die door een circuit gebruiker moet worden gebruikt om de virtuele netwerk gateways te verbinden met het ExpressRoute-circuit. Een autorisatie is slechts voor één verbinding geldig.

> [!NOTE]
> Voor elke verbinding is een afzonderlijke autorisatie vereist.
>

1. Selecteer op de pagina ExpressRoute de optie **autorisaties** en typ vervolgens een **naam** voor de autorisatie en selecteer **Opslaan**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Scherm opname van ExpressRoute-circuit":::

2. Nadat de configuratie is opgeslagen, kopieert u de **resource-id** en de **autorisatie sleutel**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Scherm opname van ExpressRoute-circuit":::

**Een verbindings autorisatie verwijderen**

U kunt een verbinding verwijderen door het pictogram **verwijderen** te selecteren op de pagina voor uw verbinding.

### <a name="circuit-user-operations"></a>Gebruikers bewerkingen circuit

De circuit gebruiker heeft de resource-ID en een autorisatie sleutel nodig van de eigenaar van het circuit.

**Een verbindings autorisatie inwisselen**

1. Selecteer de knop **+ Nieuw** .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Scherm opname van ExpressRoute-circuit":::

2. Zoek naar **' verbinding '** in de Marketplace, Selecteer deze en selecteer **maken**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/search-connection.png" alt-text="Scherm opname van ExpressRoute-circuit":::

3. Zorg ervoor dat het **verbindings type** is ingesteld op ' ExpressRoute '.
4. Vul de details in en selecteer **OK** op de pagina basis beginselen.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Scherm opname van ExpressRoute-circuit":::

5. Selecteer op de pagina **instellingen** de **virtuele netwerk gateway** en schakel het selectie vakje **autorisatie inwisselen** in.
6. Voer de **autorisatie sleutel** en de **URI van het peer circuit** in en geef de verbinding een naam. Selecteer **OK**. De **URI van het peer circuit** is de resource-id van het ExpressRoute-circuit (dat u kunt vinden onder het instellingen venster Eigenschappen van het ExpressRoute-circuit).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Scherm opname van ExpressRoute-circuit":::

7. Controleer de informatie op de pagina **samen vatting** en selecteer **OK**.

**Een verbindings autorisatie vrijgeven**

U kunt een autorisatie vrijgeven door de verbinding die het ExpressRoute-circuit koppelt, te verwijderen in het virtuele netwerk.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt een verbinding verwijderen en uw VNet ontkoppelen aan een ExpressRoute-circuit door het pictogram **verwijderen** te selecteren op de pagina voor uw verbinding.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een virtueel netwerk verbindt met een circuit in hetzelfde abonnement en een ander abonnement. Zie voor meer informatie over de ExpressRoute-gateway: 

> [!div class="nextstepaction"]
> [Over ExpressRoute van virtuele netwerk gateways](expressroute-about-virtual-network-gateways.md)
