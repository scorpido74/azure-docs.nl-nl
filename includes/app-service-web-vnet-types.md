---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81312841"
---
* De multi tenant systemen die ondersteuning bieden voor het volledige assortiment prijs abonnementen, behalve geïsoleerd.
* De App Service Environment, die wordt geïmplementeerd in uw VNet en die ondersteuning biedt voor geïsoleerde prijzen plan-apps.

De VNet-integratie functie wordt gebruikt in apps voor meerdere tenants. Als uw app zich in [app service Environment][ASEintro]bevindt, is deze al in een VNet en is het niet nodig om de VNet-integratie functie te gebruiken om resources in hetzelfde VNet te bereiken. Zie [app service-netwerk functies][Networkingfeatures]voor meer informatie over alle netwerk functies.

VNet-integratie biedt uw app toegang tot resources in uw VNet, maar verleent geen inkomende persoonlijke toegang aan uw app vanuit het VNet. Toegang tot de persoonlijke site verwijst naar het maken van een app die alleen toegankelijk is vanuit een particulier netwerk, bijvoorbeeld vanuit een virtueel Azure-netwerk. VNet-integratie wordt alleen gebruikt om uitgaande oproepen van uw app naar uw VNet te maken. De VNet-integratie functie werkt anders als deze wordt gebruikt met VNet in dezelfde regio en met VNet in andere regio's. De VNet-integratie functie heeft twee varianten:

* **Regionale VNet-integratie**: wanneer u verbinding maakt met Azure Resource Manager virtuele netwerken in dezelfde regio, moet u een toegewijd subnet hebben in het VNet waarmee u een integratie uitvoert.
* **Gateway-vereiste VNet-integratie**: wanneer u verbinding maakt met VNet in andere regio's of een klassiek virtueel netwerk in dezelfde regio, hebt u een Azure Virtual Network-gateway nodig die is ingericht in het doel-VNet.

De VNet-integratie functies:

* Vereisen een prijs plan voor Standard, Premium, PremiumV2 of elastisch Premium.
* Ondersteuning voor TCP en UDP.
* Werken met Azure App Service apps en functie-apps.

Er zijn enkele dingen die VNet-integratie niet ondersteunt, zoals:

* Een station koppelen.
* Integratie van Active Directory.
* Naamgeving.

Gateway-vereiste VNet-integratie biedt alleen toegang tot bronnen in het doel-VNet of in netwerken die zijn verbonden met het doel-VNet met peering of Vpn's. Gateway-vereiste VNet-integratie biedt geen toegang tot bronnen die beschikbaar zijn via Azure ExpressRoute-verbindingen of werkt met Service-eind punten.

VNet-integratie geeft uw app altijd toegang tot resources in uw VNet, maar verleent geen inkomende persoonlijke toegang aan uw app vanuit het VNet. Toegang via een persoonlijke site verwijst naar het toegankelijk maken van uw app vanuit een particulier netwerk, bijvoorbeeld vanuit een Azure-VNet. VNet-integratie is alleen voor het maken van uitgaande oproepen vanuit uw app naar uw VNet.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
