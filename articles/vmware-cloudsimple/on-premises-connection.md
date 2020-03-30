---
title: Azure VMware-oplossing door CloudSimple - On-premises verbinding met ExpressRoute
description: Beschrijft hoe u een on-premises verbinding aanvragen via ExpressRoute van het regionetwerk CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019618"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Maak verbinding van on-premises naar CloudSimple via ExpressRoute

Als u al een Azure ExpressRoute-verbinding hebt vanaf een externe locatie (zoals on-premises) met Azure, u deze verbinding maken met uw CloudSimple-omgeving. U dit doen via een Azure-functie waarmee twee ExpressRoute-circuits met elkaar verbinding kunnen maken. Deze methode zorgt voor een veilige, privé- en hoge bandbreedte- en lage latentieverbinding tussen de twee omgevingen.

[![On-premises ExpressRoute Connection - Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Voordat u begint

Een **/29-netwerkadresblok** is vereist voor het opzetten van een Global Reach-verbinding vanuit on-premises.  De /29-adresruimte wordt gebruikt voor het transitnetwerk tussen expressroutecircuits.  Het transitnetwerk mag niet overlappen met uw Azure virtuele netwerken, on-premises netwerken of CloudSimple Private Cloud-netwerken.

## <a name="prerequisites"></a>Vereisten

* Er is een Azure ExpressRoute-circuit vereist voordat u de verbinding tussen het circuit en de CloudSimple Private Cloud-netwerken tot stand brengen.
* Een gebruiker heeft bevoegdheden om autorisatiesleutels te maken op een ExpressRoute-circuit.

## <a name="scenarios"></a>Scenario's

Als u uw on-premises netwerk verbindt met uw Private Cloud-netwerk, u de Private Cloud op verschillende manieren gebruiken, inclusief de volgende scenario's:

* Krijg toegang tot uw Private Cloud-netwerk zonder een SITE-to-Site VPN-verbinding te maken.
* Gebruik uw on-premises Active Directory als identiteitsbron op uw Private Cloud.
* Migreer virtuele machines die on-premises worden uitgevoerd naar uw Private Cloud.
* Gebruik uw Private Cloud als onderdeel van een oplossing voor noodherstel.
* Gebruik on-premises resources op uw Private Cloud-workloadVM's.

## <a name="connecting-expressroute-circuits"></a>ExpressRoute-circuits verbinden

Als u de ExpressRoute-verbinding wilt maken, moet u een autorisatie op uw ExpressRoute-circuit maken en de autorisatiegegevens aan CloudSimple verstrekken.


### <a name="create-expressroute-authorization"></a>Uitdrukkelijke route-autorisatie maken

1. Meld u aan bij Azure Portal.

2. Zoek vanaf de bovenste zoekbalk naar **het expressroutecircuit** en klik op **ExpressRoute-circuits** onder **Services**.
    [![ExpressRoute-circuits](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecteer het ExpressRoute-circuit dat u wilt verbinden met uw CloudSimple-netwerk.

4. Klik op de pagina ExpressRoute op **Autorisaties,** voer een naam in voor de autorisatie en klik op **Opslaan**.
    [![ExpressRoute-circuitautorisatie](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopieer de bron-id en autorisatiesleutel door op het kopieerpictogram te klikken. Plak de id en de sleutel in een tekstbestand.
    [![Formulier formulier ExpressRoute-circuitautorisatie](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Resource-id** moet worden gekopieerd van de gebruikersinterface ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` en moet in de indeling zijn wanneer u deze opgeeft om deze te ondersteunen.

6. Dien een ticket in met <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Ondersteuning</a> voor de verbinding die moet worden gemaakt.
    * Type probleem: **Technisch**
    * Abonnement: **Abonnement waarbij CloudSimple-service wordt geïmplementeerd**
    * Service: **VMware Solution by CloudSimple**
    * Probleemtype: **Serviceaanvraag**
    * Probleemsubtype: **ExpressRoute-verbinding maken met on-premises**
    * Geef de bron-id en autorisatiesleutel op die u hebt gekopieerd en opgeslagen in het detailvenster.
    * Geef een /29-netwerkadresruimte voor het transitnetwerk.
    * Stuurt u de standaardroute via ExpressRoute?
    * Moet het Private Cloud-verkeer gebruikmaken van de standaardroute die via ExpressRoute wordt verzonden?

    > [!IMPORTANT]
    > Als u een standaardroute verzendt, u al het internetverkeer vanuit Private Cloud verzenden via uw on-premises internetverbinding.  Als u de standaardroute wilt uitschakelen die is geconfigureerd in de private cloud en de standaardroute voor de on-premises verbinding wilt gebruiken, geeft u de details op in het ondersteuningsticket.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure-netwerkverbindingen](cloudsimple-azure-network-connection.md)  
