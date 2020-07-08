---
title: 'ExpressRoute: een VNet koppelen aan een circuit: Azure Portal'
description: Verbind een VNet met een Azure ExpressRoute-circuit. Instructies.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: a4ed99d69d1c2389e73c215bccfd6112895e791d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737067"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Een virtueel netwerk verbinden aan een ExpressRoute-circuit met behulp van de portal
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video-Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [Power shell (klassiek)](expressroute-howto-linkvnet-classic.md)
> 

Dit artikel helpt u bij het maken van een verbinding om een virtueel netwerk te koppelen aan een Azure ExpressRoute-circuit met behulp van de Azure Portal. De virtuele netwerken die u met uw Azure ExpressRoute-circuit verbindt, kunnen zich in hetzelfde abonnement bevinden of ze kunnen deel uitmaken van een ander abonnement.

## <a name="before-you-begin"></a>Voordat u begint

* Bekijk de [prerequisites](expressroute-prerequisites.md) [vereisten, routerings behoeften](expressroute-routing.md)en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben.
  * Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit ingeschakeld door uw connectiviteits provider.
  * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. Zie het artikel [peering maken en wijzigen voor een ExpressRoute-circuit](expressroute-howto-routing-portal-resource-manager.md) voor informatie over peering en route ring.
  * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd en dat de BGP-peering tussen uw netwerk en micro soft actief is zodat u end-to-end connectiviteit kunt inschakelen.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerk gateway hebt gemaakt en volledig hebt ingericht. Volg de instructies voor het [maken van een virtuele netwerk gateway voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Een virtuele netwerk gateway voor ExpressRoute maakt gebruik van de gateway type ' ExpressRoute ', niet VPN.

* U kunt Maxi maal 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer een standaard ExpressRoute-circuit wordt gebruikt.

* Eén VNet kan worden gekoppeld aan Maxi maal vier ExpressRoute-circuits. Gebruik de onderstaande procedure om een nieuw verbindings object te maken voor elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, verschillende abonnementen of een combi natie van beide bevindt.

* U kunt een virtueel netwerk koppelen aan een buiten de geopolitieke regio van het ExpressRoute-circuit of een groter aantal virtuele netwerken verbinden met uw ExpressRoute-circuit als u de Premium-invoeg toepassing van ExpressRoute hebt ingeschakeld. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoeg toepassing.

* U kunt [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) voordat u begint met een beter begrip van de stappen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Een VNet verbinden met een circuit-hetzelfde abonnement

> [!NOTE]
> BGP-configuratie gegevens worden niet weer gegeven als de laag 3-provider uw peerings heeft geconfigureerd. Als uw circuit een ingerichte status heeft, moet u verbindingen kunnen maken.
>

### <a name="to-create-a-connection"></a>Een verbinding maken

1. Zorg ervoor dat uw ExpressRoute-circuit en persoonlijke Azure-peering correct zijn geconfigureerd. Volg de instructies in [een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md) en [peering maken en wijzigen voor een ExpressRoute-circuit](expressroute-howto-routing-arm.md). Uw ExpressRoute-circuit moet eruitzien zoals in de volgende afbeelding:

   [![Scherm opname van ExpressRoute-circuit](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Circuit weer geven")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. U kunt nu beginnen met het inrichten van een verbinding om uw virtuele netwerk gateway te koppelen aan uw ExpressRoute-circuit. Klik op **verbinding**  >  **toevoegen** om de pagina **verbinding toevoegen** te openen en configureer de waarden.

   [![Scherm opname van verbinding toevoegen](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Scherm opname van verbinding toevoegen")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Nadat de verbinding is geconfigureerd, wordt de informatie voor de verbinding weer gegeven in het verbindings object.

   ![Scherm opname van verbindings object](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Een VNet verbinden met een circuit-ander abonnement

U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. In de afbeelding hieronder ziet u hoe delen werkt voor ExpressRoute-circuits voor meerdere abonnementen.

![Connectiviteit tussen abonnementen](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Elk van de kleinere Clouds in de grote Cloud wordt gebruikt om abonnementen weer te geven die deel uitmaken van verschillende afdelingen binnen een organisatie.
- Elk van de afdelingen in de organisatie kan hun eigen abonnement gebruiken voor het implementeren van hun services, maar ze kunnen een enkel ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk.
- Eén afdeling (in dit voor beeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen het ExpressRoute-circuit en de autorisaties gebruiken die zijn gekoppeld aan het circuit, inclusief abonnementen die zijn gekoppeld aan andere Azure Active Directory-tenants en Enterprise Agreement inschrijvingen.

  > [!NOTE]
  > Connectiviteits-en bandbreedte kosten voor het specifieke circuit worden toegepast op de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde band breedte.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Beheer-informatie over circuit eigen aren en circuit gebruikers

De ' circuit eigenaar ' is een geautoriseerde hoofd gebruiker van de ExpressRoute-circuit resource. De eigenaar van het circuit kan autorisaties maken die kunnen worden ingewisseld door ' circuit gebruikers '. Circuit gebruikers zijn eigen aren van virtuele netwerk gateways die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuit gebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De eigenaar van het circuit heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Het intrekken van een autorisatie resulteert in alle koppelings verbindingen die worden verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Bewerkingen voor circuit eigenaars

**Een verbindings autorisatie maken**

De eigenaar van het circuit maakt een autorisatie. Dit resulteert in het maken van een autorisatie sleutel die door een circuit gebruiker kan worden gebruikt om de virtuele netwerk gateways te verbinden met het ExpressRoute-circuit. Een autorisatie is slechts voor één verbinding geldig.

> [!NOTE]
> Voor elke verbinding is een afzonderlijke autorisatie vereist.
>

1. Klik op de pagina ExpressRoute op **autorisaties** en typ een **naam** voor de autorisatie en klik op **Opslaan**.

   ![Autorisaties](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Nadat de configuratie is opgeslagen, kopieert u de **resource-id** en de **autorisatie sleutel**.

   ![Autorisatiesleutel](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Een verbindings autorisatie verwijderen**

U kunt een verbinding verwijderen door het pictogram **verwijderen** te selecteren op de pagina voor uw verbinding.

### <a name="circuit-user-operations"></a>Gebruikers bewerkingen circuit

De circuit gebruiker heeft de resource-ID en een autorisatie sleutel nodig van de eigenaar van het circuit.

**Een verbindings autorisatie inwisselen**

1. Klik op de knop **+ Nieuw** .

   ![Klik op nieuw](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Zoek naar **' verbinding '** in de Marketplace, Selecteer deze en klik op **maken**.

   ![Verbinding zoeken](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Zorg ervoor dat het **verbindings type** is ingesteld op ' ExpressRoute '.
4. Vul de details in en klik op **OK** op de pagina basis beginselen.

   ![Pagina Basisinformatie](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Selecteer op de pagina **instellingen** de **virtuele netwerk gateway** en schakel het selectie vakje **autorisatie inwisselen** in.
6. Voer de **autorisatie sleutel** en de **URI van het peer circuit** in en geef de verbinding een naam. Klik op **OK**. De **URI van het peer circuit** is de resource-id van het ExpressRoute-circuit (dat u kunt vinden onder het instellingen venster Eigenschappen van het ExpressRoute-circuit).

   ![Pagina Instellingen](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Controleer de informatie op de pagina **samen vatting** en klik op **OK**.

**Een verbindings autorisatie vrijgeven**

U kunt een autorisatie vrijgeven door de verbinding die het ExpressRoute-circuit koppelt, te verwijderen in het virtuele netwerk.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Een verbinding verwijderen om een VNet te ontkoppelen

U kunt een verbinding verwijderen en uw VNet ontkoppelen aan een ExpressRoute-circuit door het pictogram **verwijderen** te selecteren op de pagina voor uw verbinding.

## <a name="next-steps"></a>Volgende stappen
Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.
