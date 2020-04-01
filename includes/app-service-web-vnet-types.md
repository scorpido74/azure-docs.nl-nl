---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419568"
---
* De multitenant-systemen die het volledige scala aan prijsplannen ondersteunen, behalve Geïsoleerd.
* De App Service-omgeving, die wordt geïmplementeerd in uw virtuele netwerk en apps voor geïsoleerde prijzenplannen ondersteunt.

De VNet-integratiefunctie wordt gebruikt in multitenant-apps. Als uw app zich in [de app-serviceomgeving bevindt,][ASEintro]bevindt deze zich al in een virtueel netwerk en hoeft u de functie VNet-integratie niet te gebruiken om bronnen in hetzelfde virtuele netwerk te bereiken. Zie [App Service-netwerkfuncties][Networkingfeatures]voor meer informatie over alle netwerkfuncties.

VNet-integratie geeft uw app toegang tot bronnen in uw virtuele netwerk, maar verleent geen inkomende privétoegang tot uw app vanuit het virtuele netwerk. Toegang tot privé-site verwijst naar het alleen toegankelijk maken van een app vanuit een privénetwerk, bijvoorbeeld vanuit een virtueel Azure-netwerk. VNet-integratie wordt alleen gebruikt om uitgaande gesprekken vanuit uw app naar uw virtuele netwerk te voeren. De VNet-integratiefunctie gedraagt zich anders wanneer deze wordt gebruikt met virtuele netwerken in dezelfde regio en met virtuele netwerken in andere regio's. De VNet Integration-functie heeft twee varianten:

* **Regionale VNet-integratie:** Wanneer u verbinding maakt met virtuele azure-netwerken voor Azure Resource Manager in dezelfde regio, moet u een speciaal subnet hebben in het virtuele netwerk waarmee u integreert.
* **VNet-integratie vereist voor**gateway's: Wanneer u verbinding maakt met virtuele netwerken in andere regio's of met een klassiek virtueel netwerk in dezelfde regio, hebt u een Azure Virtual Network-gateway nodig die is ingericht in het virtuele doelnetwerk.

De VNet-integratiefuncties:

* Vereist een standaard-, Premium-, PremiumV2- of Elastic Premium-prijsplan.
* Ondersteuning voor TCP en UDP.
* Werken met Azure App Service-apps en functie-apps.

Er zijn een aantal dingen die VNet Integration niet ondersteunt, zoals:

* Het monteren van een station.
* Active Directory-integratie.
* Netbios.

Door gatewayvereist VNet-integratie biedt alleen toegang tot bronnen in het virtuele doelnetwerk of in netwerken die zijn verbonden met het beoogde virtuele netwerk met peering of VPN's. VNet-integratie die vereist is voor poorten biedt geen toegang tot bronnen die beschikbaar zijn voor Azure ExpressRoute-verbindingen of werken met serviceeindpunten.

Ongeacht de gebruikte versie geeft VNet Integration uw app toegang tot bronnen in uw virtuele netwerk, maar verleent het geen inkomende privétoegang tot uw app vanuit het virtuele netwerk. Toegang tot privé-site verwijst naar het alleen toegankelijk maken van uw app vanuit een privénetwerk, bijvoorbeeld vanuit een virtueel Azure-netwerk. VNet-integratie is alleen voor uitgaande gesprekken vanuit uw app naar uw virtuele netwerk.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
