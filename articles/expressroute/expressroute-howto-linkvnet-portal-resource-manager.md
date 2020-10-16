---
title: 'Zelfstudie: Een VNet koppelen aan een ExpressRoute-circuit - Azure Portal'
description: Deze zelfstudie laat zien hoe u een verbinding maakt om een virtueel netwerk te koppelen aan een ExpressRoute-circuit met behulp van Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: f46ad0d45967f94191732f472b44a47de930a3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855350"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Zelfstudie: Een virtueel netwerk verbinden met een ExpressRoute-circuit met behulp van de portal

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
> 

In deze zelfstudie leert u hoe u een verbinding maakt om een virtueel netwerk te koppelen aan een ExpressRoute-circuit met behulp van Azure Portal. De virtuele netwerken die u met uw Azure ExpressRoute-circuit verbindt, kunnen zich in hetzelfde abonnement bevinden of deel uitmaken van een ander abonnement.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een virtueel netwerk verbinden met een circuit in hetzelfde abonnement.
> - Een virtueel netwerk verbinden met een circuit in een ander abonnement.
> - De koppeling tussen het virtuele netwerk en het ExpressRoute-circuit verwijderen.

## <a name="prerequisites"></a>Vereisten

* Bekijk de [vereisten](expressroute-prerequisites.md), de [routeringsvereisten](expressroute-routing.md) en de [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben.
  * Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door de connectiviteitsprovider.
  * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. Raadpleeg het artikel [Peering voor een ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md) voor instructies voor peeren en routeren.
  * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd en dat BGP-peering tussen uw netwerk en Microsoft wordt ingesteld voor end-to-end-connectiviteit.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerkgateway hebt gemaakt en volledig hebt ingericht. Volg de instructies om [een virtuele netwerkgateway voor ExpressRoute te maken](expressroute-howto-add-gateway-resource-manager.md). Een virtuele netwerkgateway voor ExpressRoute maakt gebruik van het GatewayType 'ExpressRoute', niet VPN.

* U kunt tot 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer een standaard ExpressRoute-circuit wordt gebruikt.

* Eén VNet kan aan maximaal vier ExpressRoute-circuits worden gekoppeld. Gebruik de volgende procedure om een nieuw verbindingsobject te maken voor elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, in verschillende abonnementen of in een combinatie van beide bevinden.

* Als u de ExpressRoute Premium-invoegtoepassing inschakelt, kunt u virtuele netwerken koppelen die zich buiten de geopolitieke regio van het ExpressRoute-circuit bevinden. De Premium-invoegtoepassing biedt u ook de mogelijkheid om meer dan 10 virtuele netwerken te verbinden met uw ExpressRoute-circuit, afhankelijk van de gekozen bandbreedte. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoegtoepassing.

* U kunt [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) voordat u begint om de stappen beter te begrijpen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Een VNet verbinden met een circuit - zelfde abonnement

> [!NOTE]
> BGP-configuratiegegevens worden niet weergegeven als de provider van laag 3 uw peerings heeft geconfigureerd. Als uw circuit een ingerichte status heeft, zou u verbindingen moeten kunnen maken.
>

### <a name="to-create-a-connection"></a>Een verbinding maken

1. Zorg ervoor dat uw ExpressRoute-circuit en persoonlijke Azure-peering juist zijn geconfigureerd. Volg de instructies in [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md) en [Peering voor een ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-arm.md). Uw ExpressRoute-circuit zou eruit moeten zien zoals in de volgende afbeelding:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Schermopname van ExpressRoute-circuit":::

2. U kunt nu beginnen met het inrichten van een verbinding om uw virtuele netwerkgateway te koppelen aan uw ExpressRoute-circuit. Selecteer **Verbinding** > **Toevoegen** om de pagina **Verbinding toevoegen** te openen en stel vervolgens de waarden in.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Schermopname van ExpressRoute-circuit":::

3. Nadat de verbinding is geconfigureerd, wordt de informatie voor de verbinding weergegeven in het verbindingsobject.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Schermopname van ExpressRoute-circuit":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Een VNet verbinden met een circuit - ander abonnement

U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. De volgende afbeelding is een schematische weergave van hoe delen tussen meerdere abonnementen werkt voor ExpressRoute-circuits.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Schermopname van ExpressRoute-circuit":::

Elk van de kleinere clouds in de grote cloud staan voor abonnementen die tot verschillende afdelingen binnen een organisatie behoren. Elk van de afdelingen in de organisatie gebruikt een eigen abonnement voor het implementeren van hun services, maar ze kunnen één ExpressRoute-circuit delen om verbinding te maken terug naar uw on-premises netwerk. Eén afdeling (in dit voorbeeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen gebruikmaken van het ExpressRoute-circuit.

  > [!NOTE]
  > Kosten voor connectiviteit- en bandbreedte voor het toegewezen circuit zijn in rekening gebracht bij de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde bandbreedte.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Beheer: informatie over circuiteigenaars en -gebruikers

De 'circuiteigenaar' is een geautoriseerde hoofdgebruiker van de ExpressRoute-circuitresource. De circuiteigenaar kan autorisaties maken die kunnen worden ingewisseld door 'circuitgebruikers'. Circuitgebruikers zijn eigenaren van virtuele netwerkgateways die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuitgebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De circuiteigenaar heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Het intrekken van een autorisatie heeft tot gevolg dat alle koppelingsverbindingen uit het abonnement waarvan de toegangsrechten zijn ingetrokken, worden verwijderd.

### <a name="circuit-owner-operations"></a>Bewerkingen door circuiteigenaars

**Een autorisatie voor een verbinding maken**

De circuiteigenaar maakt een autorisatie, waarmee een autorisatiesleutel wordt gemaakt die door een circuitgebruiker moet worden gebruikt om de virtuele netwerkgateways te verbinden met het ExpressRoute-circuit. Een autorisatie is slechts voor één verbinding geldig.

> [!NOTE]
> Voor elke verbinding is een afzonderlijke autorisatie vereist.
>

1. Selecteer op de pagina ExpressRoute de optie **Autorisaties** en typ vervolgens een **naam** voor de autorisatie en selecteer **Opslaan**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Schermopname van ExpressRoute-circuit":::

2. Zodra de configuratie is opgeslagen, kopieert u de **resource-id** en de **autorisatiesleutel**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Schermopname van ExpressRoute-circuit":::

**Een autorisatie voor een verbinding verwijderen**

U kunt een verbinding verwijderen door het pictogram **Verwijderen** te selecteren op de pagina voor uw verbinding.

### <a name="circuit-user-operations"></a>Bewerkingen door circuitgebruikers

De circuitgebruiker heeft de resource-id en een autorisatiesleutel nodig van de circuiteigenaar.

**Een autorisatie voor een verbinding inwisselen**

1. Selecteer de knop **+Nieuw**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Schermopname van ExpressRoute-circuit":::

2. Zoek in Marketplace naar **Verbinding**, selecteer deze en selecteer **Maken**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/search-connection.png" alt-text="Schermopname van ExpressRoute-circuit":::

3. Zorg ervoor dat het **verbindingstype** is ingesteld op 'ExpressRoute'.
4. Vul de details in en selecteer **OK** op de pagina Basisinformatie.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Schermopname van ExpressRoute-circuit":::

5. Selecteer op de pagina **Instellingen** de **virtuele netwerkgateway** en schakel het selectievakje **Autorisatie inwisselen** in.
6. Voer de **autorisatiesleutel** en de **URI van het peer-circuit** in en type een naam voor de verbinding. Selecteer **OK**. De **URI van het peer-circuit** is de resource-id van het ExpressRoute-circuit (u kunt deze vinden onder het deelvenster Instelling eigenschappen van het ExpressRoute-circuit).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Schermopname van ExpressRoute-circuit":::

7. Lees de informatie op de pagina **Samenvatting** en selecteer **OK**.

**Een autorisatie voor een verbinding vrijgeven**

U kunt een autorisatie vrijgeven door de verbinding waarmee het ExpressRoute-circuit aan het virtuele netwerk wordt gekoppeld, te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt een verbinding verwijderen en uw VNet loskoppelen van een ExpressRoute-circuit door op de pagina voor uw verbinding het pictogram **Verwijderen** te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een virtueel netwerk verbindt met een circuit in hetzelfde abonnement en in een ander abonnement. Zie 

> [!div class="nextstepaction"]
> [Info over virtuele ExpressRoute-netwerkgateways](expressroute-about-virtual-network-gateways.md) voor meer informatie over de virtuele ExpressRoute-netwerkgateway
