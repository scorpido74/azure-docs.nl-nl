---
title: 'Azure VMware-oplossingen (AVS): on-premises verbinding met ExpressRoute'
description: Hierin wordt beschreven hoe u een on-premises verbinding aanvraagt via ExpressRoute van een AVS-netwerk
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10a21faf2790b4c7a26d80e46bf44c8bffabf27f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019618"
---
# <a name="connect-from-on-premises-to-avs-using-expressroute"></a>Verbinding maken tussen on-premises en AVS met ExpressRoute

Als u al een Azure ExpressRoute-verbinding hebt vanaf een externe locatie (zoals on-premises) naar Azure, kunt u deze verbinding maken met uw AVS-omgeving. U kunt dit doen via een Azure-functie waarmee twee ExpressRoute-circuits met elkaar kunnen worden verbonden. Met deze methode wordt een veilige, persoonlijke, hoge band breedte en lage latentie verbinding tot stand gebracht tussen de twee omgevingen.

[![on-premises ExpressRoute-verbinding-Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Voordat u begint

Een **/29-** netwerk adres blok is vereist voor het tot stand brengen van Global Reach verbinding van on-premises. De/29-adres ruimte wordt gebruikt voor het tussenliggende netwerk tussen ExpressRoute-circuits. Het tussenliggende netwerk mag niet overlappen met een van uw virtuele Azure-netwerken, on-premises netwerken of voor de automatische AVS-Cloud netwerken.

## <a name="prerequisites"></a>Vereisten

* Er is een Azure ExpressRoute-circuit vereist voordat u verbinding kunt maken tussen het circuit en de Cloud netwerken van de AVS.
* Een gebruiker is verplicht met bevoegdheden om autorisatie sleutels te maken op een ExpressRoute-circuit.

## <a name="scenarios"></a>Scenario's

Door uw on-premises netwerk te verbinden met het Privécloud-netwerk van de automatische Cloud kunt u de automatische AVS-Cloud op verschillende manieren gebruiken, met inbegrip van de volgende scenario's:

* Toegang tot uw persoonlijke AVS-Cloud netwerk zonder een site-naar-site-VPN-verbinding te maken.
* Gebruik uw on-premises Active Directory als een id-bron in de Privécloud van uw AVS.
* Migreer virtuele machines die on-premises worden uitgevoerd naar uw AVS-Privécloud.
* Gebruik de Privécloud van uw AVS als onderdeel van een oplossing voor herstel na nood gevallen.
* On-premises resources gebruiken op de virtuele machines van uw AVS-werk belasting in de Privécloud.

## <a name="connecting-expressroute-circuits"></a>ExpressRoute-circuits verbinden

Om de ExpressRoute-verbinding tot stand te brengen, moet u een autorisatie maken op uw ExpressRoute-circuit en de autorisatie gegevens opgeven om te kunnen AVS.


### <a name="create-expressroute-authorization"></a>ExpressRoute-autorisatie maken

1. Meld u aan bij Azure Portal.

2. Zoek in de bovenste zoek balk naar **ExpressRoute-circuit** en klik op **ExpressRoute-circuits** onder **Services**.
    [![ExpressRoute-circuits](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecteer het ExpressRoute-circuit dat u van plan bent om verbinding te maken met uw AVS-netwerk.

4. Op de pagina ExpressRoute klikt u op **autorisaties**, voert u een naam in voor de autorisatie en klikt u op **Opslaan**.
    [ExpressRoute-circuit autorisatie voor ![](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopieer de resource-ID en autorisatie sleutel door te klikken op het pictogram kopiëren. Plak de ID en de sleutel in een tekst bestand.
    [![-autorisatie kopie voor ExpressRoute-circuit](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > De **resource-id** moet worden gekopieerd uit de gebruikers interface en moet de indeling hebben ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` wanneer u deze biedt voor ondersteuning.

6. Een ticket met <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">ondersteuning</a> voor het maken van de verbinding.
    * Probleem type: **technisch**
    * Abonnement: het **abonnement waarin de AVS-service is geïmplementeerd**
    * Service: **VMware-oplossingen (AVS)**
    * Probleem type: **service aanvraag**
    * Subtype van probleem: **ExpressRoute-verbinding maken met on-premises**
    * Geef de bron-ID en de autorisatie sleutel op die u hebt gekopieerd en opgeslagen in het detail venster.
    * Geef een/29-netwerk adres ruimte op voor het tussenliggende netwerk.
    * Verzendt u de standaard route via ExpressRoute?
    * Moet het verkeer van de Privécloud in de Cloud gebruikmaken van de standaard route die via ExpressRoute is verzonden?

    > [!IMPORTANT]
    > Als u een standaard route verzendt, kunt u al het Internet verkeer vanuit de Privécloud van uw persoonlijke Cloud verzenden met behulp van uw on-premises Internet verbinding. Als u de standaard route die is geconfigureerd op de AVS-Privécloud wilt uitschakelen en de standaard route voor on-premises verbindingen wilt gebruiken, geeft u de details op in het ondersteunings ticket.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure-netwerk verbindingen](cloudsimple-azure-network-connection.md)  
