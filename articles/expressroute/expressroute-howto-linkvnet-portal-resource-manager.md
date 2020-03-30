---
title: 'ExpressRoute: een VNet koppelen aan een circuit: Azure-portal'
description: Verbind een VNet met een Azure ExpressRoute-circuit. How-to stappen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272914"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Een virtueel netwerk verbinden aan een ExpressRoute-circuit met behulp van de portal
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
> 

Met dit artikel u een verbinding maken om een virtueel netwerk te koppelen aan een Azure ExpressRoute-circuit met behulp van de Azure-portal. De virtuele netwerken die u verbinding maakt met uw Azure ExpressRoute-circuit, kunnen in hetzelfde abonnement zitten of deel uitmaken van een ander abonnement.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer de [vereisten,](expressroute-prerequisites.md) [routeringsvereisten](expressroute-routing.md)en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.

* U moet een actief ExpressRoute-circuit hebben.
  * Volg de instructies om [een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) te maken en laat het circuit inschakelen door uw connectiviteitsprovider.
  * Zorg ervoor dat Azure private peering is geconfigureerd voor uw circuit. Zie [het peering maken en wijzigen voor een ExpressRoute-circuitartikel](expressroute-howto-routing-portal-resource-manager.md) voor peering- en routeringsinstructies.
  * Controleer of Azure private peering is geconfigureerd en dat het BGP-peering tussen uw netwerk en Microsoft is ingeschakeld, zodat u end-to-end-connectiviteit inschakelen.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerkgateway hebt gemaakt en volledig is ingericht. Volg de instructies om [een virtuele netwerkgateway voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)te maken. Een virtuele netwerkgateway voor ExpressRoute maakt gebruik van de GatewayType 'ExpressRoute', niet VPN.

* U maximaal 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer ze een standaard ExpressRoute-circuit gebruiken.

* Een enkele VNet kan worden gekoppeld aan maximaal vier ExpressRoute-circuits. Gebruik het onderstaande proces om een nieuw verbindingsobject te maken voor elk ExpressRoute-circuit waaru verbinding mee maakt. De ExpressRoute-circuits kunnen in hetzelfde abonnement, verschillende abonnementen of een mix van beide zijn.

* U een virtueel netwerk buiten de geopolitieke regio van het ExpressRoute-circuit koppelen of een groter aantal virtuele netwerken aansluiten op uw ExpressRoute-circuit als u de ExpressRoute premium-add-on hebt ingeschakeld. Bekijk de [FAQ](expressroute-faqs.md) voor meer informatie over de premium add-on.

* U [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) voordat u begint met het beter begrijpen van de stappen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Een VNet aansluiten op een circuit - hetzelfde abonnement

> [!NOTE]
> BGP-configuratiegegevens worden niet weergegeven als de layer 3-provider uw peerings heeft geconfigureerd. Als uw circuit in een ingerichte status verkeert, moet u verbindingen kunnen maken.
>

### <a name="to-create-a-connection"></a>Een verbinding maken

1. Controleer of uw ExpressRoute-circuit en Azure private peering zijn geconfigureerd. Volg de instructies in [Maak een ExpressRoute circuit](expressroute-howto-circuit-arm.md) en Maak en wijzig [peering voor een ExpressRoute circuit.](expressroute-howto-routing-arm.md) Uw ExpressRoute-circuit moet er uitzien als de volgende afbeelding:

   [![ExpressRoute-circuitschermafbeelding](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Circuit bekijken")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. U nu beginnen met het inrichten van een verbinding om uw virtuele netwerkgateway te koppelen aan uw ExpressRoute-circuit. Klik **Connection** > **op Verbinding toevoegen** om de pagina Verbindings **toevoegen** te openen en configureer de waarden.

   [![Verbindingsschermafbeelding toevoegen](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Schermafbeelding van verbinding toevoegen")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Nadat uw verbinding is geconfigureerd, worden de gegevens voor de verbinding weergegeven nadat uw verbinding is geconfigureerd.

   ![Schermafbeelding van verbindingsobject](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Een VNet aansluiten op een circuit - ander abonnement

U een ExpressRoute-circuit delen via meerdere abonnementen. De onderstaande figuur toont een eenvoudig schema van hoe delen werkt voor ExpressRoute-circuits over meerdere abonnementen.

![Connectiviteit met abonnementen voor abonnementen](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Elk van de kleinere clouds binnen de grote cloud wordt gebruikt om abonnementen weer te geven die behoren tot verschillende afdelingen binnen een organisatie.
- Elk van de afdelingen binnen de organisatie kan zijn eigen abonnement gebruiken voor het implementeren van hun services, maar ze kunnen één ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk.
- Eén afdeling (in dit voorbeeld: IT) kan eigenaar zijn van het ExpressRoute circuit. Andere abonnementen binnen de organisatie kunnen gebruik maken van het ExpressRoute-circuit en autorisaties die aan het circuit zijn gekoppeld, waaronder abonnementen die zijn gekoppeld aan andere Azure Active Directory-tenants en inschrijvingen voor enterprise-overeenkomsten.

  > [!NOTE]
  > Connectiviteit en bandbreedtekosten voor het speciale circuit worden toegepast op de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde bandbreedte.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administratie - Over circuiteigenaren en circuitgebruikers

De 'circuit eigenaar' is een geautoriseerde Power User van de ExpressRoute circuit resource. De eigenaar van het circuit kan autorisaties maken die kunnen worden ingewisseld door 'circuitgebruikers'. Circuitgebruikers zijn eigenaars van virtuele netwerkgateways die niet binnen hetzelfde abonnement vallen als het ExpressRoute-circuit. Circuitgebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De eigenaar van het circuit heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Als u een autorisatie intrekt, worden alle koppelingsverbindingen verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Circuit eigenaar operaties

**Een verbindingsautorisatie maken**

De eigenaar van het circuit maakt een autorisatie. Dit resulteert in het maken van een autorisatiesleutel die door een circuitgebruiker kan worden gebruikt om hun virtuele netwerkgateways aan te sluiten op het ExpressRoute-circuit. Een autorisatie is geldig voor slechts één verbinding.

> [!NOTE]
> Voor elke verbinding is een aparte autorisatie vereist.
>

1. Klik op de pagina ExpressRoute op **Autorisaties** en typ een **naam** voor de autorisatie en klik op **Opslaan**.

   ![Autorisaties](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Zodra de configuratie is opgeslagen, kopieert u de **resource-id** en de **autorisatiesleutel**.

   ![Autorisatiesleutel](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Een verbindingsautorisatie verwijderen**

U een verbinding verwijderen door het pictogram **Verwijderen** op de pagina voor uw verbinding te selecteren.

### <a name="circuit-user-operations"></a>Gebruikersbewerkingen van het circuit

De gebruiker van het circuit heeft de resource-id en een autorisatiesleutel van de eigenaar van het circuit nodig.

**Een verbindingsautorisatie inwisselen**

1. Klik op de knop **+Nieuw.**

   ![Klik op Nieuw](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Zoek naar **'Verbinding'** in de Marketplace, selecteer deze en klik op **Maken**.

   ![Zoeken naar verbinding](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Controleer of het **type verbinding** is ingesteld op 'ExpressRoute'.
4. Vul de details in en klik op **OK** op de pagina Basisbeginselen.

   ![De pagina Basisinformatie](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Schakel **op** de pagina Instellingen de **virtuele netwerkgateway in** en schakel het selectievakje **Autorisatie inwisselen** in.
6. Voer de **autorisatiesleutel** en het **peercircuit URI in** en geef de verbinding een naam. Klik op **OK**. De **Peer Circuit URI** is de Resource ID van het ExpressRoute circuit (die u vinden onder het deelvenster Eigenschappeninstelling van het ExpressRoute Circuit).

   ![De pagina Instellingen](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Bekijk de informatie op de **overzichtspagina** en klik op **OK**.

**Een verbindingsautorisatie vrijgeven**

U een autorisatie vrijgeven door de verbinding te verwijderen die het ExpressRoute-circuit koppelt aan het virtuele netwerk.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Een verbinding verwijderen om een VNet te ontkoppelen

U een verbinding verwijderen en uw VNet loskoppelen aan een ExpressRoute-circuit door het pictogram **Verwijderen** op de pagina voor uw verbinding te selecteren.

## <a name="next-steps"></a>Volgende stappen
Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over ExpressRoute.
