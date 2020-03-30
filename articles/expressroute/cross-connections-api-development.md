---
title: Azure ExpressRoute CrossConnnections API-ontwikkeling en -integratie
description: In dit artikel vindt u een gedetailleerd overzicht voor ExpressRoute-partners over het brontype expressRouteCrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187011"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API ontwikkeling en integratie

Met de ExpressRoute Partner Resource Manager API kunnen ExpressRoute-partners de layer-2- en layer-3-configuratie van expressroute-circuits van de klant beheren. De ExpressRoute Partner Resource Manager API introduceert een nieuw resourcetype, **expressRouteCrossConnections.** Partners gebruiken deze bron om de expressroutecircuits van klanten te beheren.

## <a name="workflow"></a>Werkstroom

De expressRouteCrossConnections-bron is een schaduwbron voor het ExpressRoute-circuit. Wanneer een Azure-klant een ExpressRoute-circuit maakt en een specifieke ExpressRoute-partner selecteert, maakt Microsoft een expressRouteCrossConnections-bron in het Azure ExpressRoute-beheerabonnement van de partner. Microsoft definieert hiermee een brongroep om de expressRouteCrossConnections-bron in te maken. De naamgevingsstandaard voor de resourcegroep is **CrossConnection-* PeeringLocation***; waar PeeringLocation = de ExpressRoute Locatie. Als een klant bijvoorbeeld een ExpressRoute-circuit maakt in Denver, wordt de CrossConnection gemaakt in het Azure-abonnement van de partner in de volgende resourcegroep: **CrossConnnection-Denver**.

ExpressRoute-partners beheren de laag-2- en laag-3-configuratie door REST-bewerkingen uit te geven tegen de expressRouteCrossConnections-bron.

## <a name="benefits"></a>Voordelen

Voordelen van de overstap naar de expressRouteCrossConnections-bron:

* Eventuele toekomstige verbeteringen voor ExpressRoute-partners worden beschikbaar gesteld op de ExpressRouteCrossConnection-bron.

* Partners kunnen [role-based access control](https://docs.microsoft.com/azure/role-based-access-control/overview) toepassen op de expressRouteCrossConnection-bron. Deze besturingselementen kunnen machtigingen definiëren waarvoor gebruikersaccounts de expressRouteCrossConnection-bron kunnen wijzigen en peering-configuraties kunnen toevoegen/bijwerken/verwijderen.

* De expressRouteCrossConnection-bron legt API's bloot die nuttig kunnen zijn bij het oplossen van ExpressRoute-verbindingen. Dit omvat ARP-tabel, Overzicht van de BGP-routetabel en details van de BGP-routetabel. Deze mogelijkheid wordt niet ondersteund door klassieke implementatie-API's.

* Partners kunnen ook de geadverteerde community's op Microsoft-peering opzoeken met behulp van de *RouteFilter-bron.*

## <a name="api-development-and-integration-steps"></a>Api-ontwikkelings- en integratiestappen

Om zich te ontwikkelen tegen de Partner API maken ExpressRoute-partners gebruik van een testklant en testpartnersetup. De installatie van de testklant wordt gebruikt om ExpressRoute-circuits te maken in testpeeringlocaties die toewijzen aan dummy-apparaten en -poorten. De installatie van de testpartner wordt gebruikt voor het beheren van de ExpressRoute-circuits die zijn gemaakt op de locatie voor het peering van de test.

### <a name="1-enlist-subscriptions"></a>1. Abonnementen indienstdoen

Als u de testpartner wilt aanvragen en de installatie van de klant wilt testen, u twee Pay-As-You-Go Azure-abonnementen opnemen bij uw ExpressRoute-contactpersoon voor engineering:
* **ExpressRoute_API_Dev_Provider_Sub:** Dit abonnement wordt gebruikt voor het beheren van ExpressRoute-circuits die zijn gemaakt op testpeeringlocaties op dummy-apparaten en -poorten.

* **ExpressRoute_API_Dev_Customer_Sub:** Dit abonnement wordt gebruikt om ExpressRoute-circuits te maken in testpeeringlocaties die toewijzen aan dummy-apparaten en -poorten.

De testpeeringlocaties: dummy-apparaten en -poorten worden standaard niet blootgesteld aan productieklanten. Om ExpressRoute-circuits te maken die in kaart kunnen worden gesteld aan de testopstelling, moet een functievlag voor abonnementen worden ingeschakeld.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registreer het Dev_Provider abonnement om toegang te krijgen tot de expressRouteCrossConnections API

Om toegang te krijgen tot de expressRouteCrossConnections API, moet het partnerabonnement worden ingeschreven bij de **Microsoft.Network Resource Provider.** Volg de stappen in het artikel [azure-bronproviders en -typen](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) om het registratieproces te voltooien.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Verificatie instellen voor REST API-aanroepen van Azure Resource Manager

Voor de meeste Azure-services is clientcode vereist om te verifiëren met Resource Manager, met behulp van geldige referenties, voordat service-API's worden aangeroepen. Verificatie wordt gecoördineerd tussen de verschillende actoren door Azure AD en biedt de client een toegangstoken als bewijs van verificatie.

Het verificatieproces omvat twee belangrijke stappen:

1. [Registreer de klant](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Maak het toegangsverzoek](https://docs.microsoft.com/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Geef netwerkinzendertoestemming aan de clienttoepassing

Zodra de verificatie is geconfigureerd, moet u netwerkinzendertoegang verlenen tot uw clienttoepassing, onder de Dev_Provider_Sub. Als u toestemming wilt verlenen, meldt u zich aan bij de [Azure-portal](https://ms.portal.azure.com/#home) en voert u de volgende stappen uit:

1. Navigeer naar abonnementen en selecteer de Dev_Provider_Sub
2. Navigeren naar Toegangsbeheer (IAM)
3. Roltoewijzing toevoegen
4. De rol netwerkbijdrage raaninzender selecteren
5. Toegang toewijzen aan Azure AD-gebruiker, groep of serviceprincipal
6. Selecteer uw clienttoepassing
7. Wijzigingen opslaan

### <a name="5-develop"></a>5.

Ontwikkelen tegen de [expressRouteCrossConnections API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>REST API

Zie [ExpressRoute CrossConnections REST API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) voor REST API-documentatie.

## <a name="next-steps"></a>Volgende stappen

Zie [ExpressRoute REST API's](https://docs.microsoft.com/rest/api/expressroute/)voor meer informatie over alle ExpressRoute REST API's.