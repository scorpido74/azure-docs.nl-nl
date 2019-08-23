---
title: Azure VMware-oplossing op CloudSimple-on-premises-verbinding met behulp van ExpressRoute
description: Hierin wordt beschreven hoe u een on-premises verbinding aanvraagt via ExpressRoute vanuit CloudSimple Region Network
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee359b76072da3caee9ae1f5fab3d0fc28d25c0e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972693"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Verbinding maken tussen on-premises en CloudSimple met behulp van ExpressRoute

Als u al een Azure ExpressRoute-verbinding hebt vanaf een externe locatie (zoals on-premises) naar Azure, kunt u deze verbinding maken met uw CloudSimple-omgeving. U kunt dit doen via een Azure-functie waarmee twee ExpressRoute-circuits met elkaar kunnen worden verbonden. Met deze methode wordt een veilige, persoonlijke, hoge band breedte en lage latentie verbinding tot stand gebracht tussen de twee omgevingen.

[![On-premises ExpressRoute-verbinding-Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="prerequisites"></a>Vereisten

* Er is een Azure ExpressRoute-circuit vereist voordat u verbinding kunt maken tussen het circuit en de particuliere Cloud netwerken van CloudSimple.
* Een gebruiker is verplicht met bevoegdheden om autorisatie sleutels te maken op een ExpressRoute-circuit.

## <a name="scenarios"></a>Scenario's

Door uw on-premises netwerk te verbinden met uw particuliere cloud netwerk, kunt u de Privécloud op verschillende manieren gebruiken, waaronder de volgende:

* Toegang tot uw particuliere cloud netwerk zonder een site-naar-site-VPN-verbinding te maken.
* Gebruik uw on-premises Active Directory als een id-bron in uw Privécloud.
* Migreer virtuele machines die on-premises worden uitgevoerd naar uw Privécloud.
* Gebruik uw Privécloud als onderdeel van een oplossing voor herstel na nood gevallen.
* Gebruik on-premises resources op uw virtuele machines uit de werk belasting van uw privécloud.

## <a name="connecting-expressroute-circuits"></a>ExpressRoute-circuits verbinden

Om de ExpressRoute-verbinding tot stand te brengen, moet u een autorisatie maken op uw ExpressRoute-circuit en de autorisatie gegevens opgeven voor CloudSimple.

### <a name="create-expressroute-authorization"></a>ExpressRoute-autorisatie maken

1. Meld u aan bij Azure Portal.

2. Zoek in de bovenste zoek balk naar **ExpressRoute-circuit** en klik op **ExpressRoute** -circuits onder **Services**.
    [![ExpressRoute-circuits](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecteer het ExpressRoute-circuit dat u van plan bent om verbinding te maken met uw CloudSimple-netwerk.

4. Op de pagina ExpressRoute klikt uop autorisaties, voert u een naam in voor de autorisatie en klikt u op **Opslaan**.
    [![ExpressRoute-circuit autorisatie](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopieer de resource-ID en autorisatie sleutel door te klikken op het pictogram kopiëren. Plak de ID en de sleutel in een tekst bestand.
    [![Kopie van ExpressRoute-circuit autorisatie](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > De **resource-id** moet worden gekopieerd uit de gebruikers interface en moet de ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` indeling hebben als u deze wilt ondersteunen.

6. Een ticket met <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">ondersteuning</a> voor het maken van de verbinding.
    * Type probleem: **Documentatie**
    * Abonnement: **Het abonnement waar de CloudSimple-service is geïmplementeerd**
    * Service: **VMware-oplossing per CloudSimple**
    * Probleem type: **Service aanvraag**
    * Subtype van probleem: **ExpressRoute-verbinding maken met on-premises**
    * Geef de bron-ID en de autorisatie sleutel op die u hebt gekopieerd en opgeslagen in het detail venster.
