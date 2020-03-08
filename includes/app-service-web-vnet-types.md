---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671524"
---
* De multi tenant systemen die ondersteuning bieden voor het volledige aanbod aan prijs plannen, behalve geïsoleerd
* De App Service Environment (ASE), die in uw VNet wordt geïmplementeerd en die ondersteuning biedt voor geïsoleerd prijs plan-apps

Dit document gaat over de VNet-integratie functie, die wordt gebruikt in de multi tenant-apps. Als uw app zich in [app service Environment][ASEintro]bevindt, is deze al in een VNet en is het niet nodig om de VNet-integratie functie te gebruiken om resources in hetzelfde VNet te bereiken. Lees [app service-netwerk functies][Networkingfeatures] voor meer informatie over alle netwerk functies

VNet-integratie biedt uw app toegang tot resources in uw virtuele netwerk, maar verleent geen inkomende persoonlijke toegang aan uw app vanuit het VNet. Toegang tot de persoonlijke site verwijst naar het maken van app alleen toegankelijk vanuit een particulier netwerk, zoals vanuit een virtueel Azure-netwerk. VNet-integratie is alleen voor het maken van uitgaande oproepen vanuit uw app naar uw VNet. De VNet-integratie functie gedraagt zich anders als deze wordt gebruikt in combi natie met VNets in dezelfde regio en met VNets in andere regio's. De VNet-integratie functie heeft twee variaties.

* Regionale VNet-integratie: wanneer u verbinding maakt met Resource Manager-VNets in dezelfde regio, moet u een toegewijd subnet hebben in het VNet waarmee u een integratie wilt maken. 
* Voor gateway vereiste VNet-integratie: wanneer u verbinding maakt met VNets in andere regio's of een klassiek VNet in dezelfde regio, hebt u een Virtual Network gateway nodig die in het doel-VNet is ingericht.

De VNet-integratie functies:

* een prijs plan voor Standard, Premium, PremiumV2 of elastisch Premium vereisen 
* ondersteuning voor TCP en UDP
* werken met App Service apps en functie-apps

Er zijn enkele zaken die VNet-integratie niet ondersteunt, waaronder:

* een station koppelen
* AD-integratie 
* NetBios

Gateway vereiste VNet-integratie biedt alleen toegang tot resources in het doel-VNet of in netwerken die zijn verbonden met het doel-VNet met peering of Vpn's. Gateway vereiste VNet-integratie biedt geen toegang tot bronnen die beschikbaar zijn via ExpressRoute-verbindingen of die werken met Service-eind punten. 

VNet-integratie geeft uw app toegang tot resources in uw virtuele netwerk, maar verleent geen inkomende persoonlijke toegang aan uw app vanuit het virtuele netwerk, ongeacht de gebruikte versie. Toegang via een persoonlijke site verwijst naar het toegankelijk maken van uw app vanaf een particulier netwerk, zoals vanuit een virtueel Azure-netwerk. VNet-integratie is alleen voor het maken van uitgaande oproepen vanuit uw app naar uw VNet. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features