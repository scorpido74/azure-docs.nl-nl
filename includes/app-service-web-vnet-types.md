---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312841"
---
* De multitenant-systemen die het volledige scala aan prijsplannen ondersteunen, behalve Geïsoleerd.
* De App Service Environment, die wordt geïmplementeerd in uw VNet en geïsoleerde apps voor prijsplannen ondersteunt.

De VNet-integratiefunctie wordt gebruikt in multitenant-apps. Als uw app zich in [de app-serviceomgeving bevindt,][ASEintro]bevindt deze zich al in een VNet en hoeft u de Functie VNet-integratie niet te gebruiken om bronnen in hetzelfde VNet te bereiken. Zie [App Service-netwerkfuncties][Networkingfeatures]voor meer informatie over alle netwerkfuncties.

VNet-integratie geeft uw app toegang tot bronnen in uw VNet, maar verleent geen inkomende privétoegang tot uw app vanuit het VNet. Toegang tot privé-site verwijst naar het alleen toegankelijk maken van een app vanuit een privénetwerk, bijvoorbeeld vanuit een virtueel Azure-netwerk. VNet-integratie wordt alleen gebruikt om uitgaande gesprekken vanuit uw app naar uw VNet te voeren. De VNet-integratiefunctie gedraagt zich anders wanneer deze wordt gebruikt met VNet in dezelfde regio en met VNet in andere regio's. De VNet Integration-functie heeft twee varianten:

* **Regionale VNet-integratie:** Wanneer u verbinding maakt met virtuele azure-netwerken voor Azure Resource Manager in dezelfde regio, moet u een speciaal subnet in het VNet hebben waarmee u integreert.
* **VNet-integratie vereist voor**gateway's: Wanneer u verbinding maakt met VNet in andere regio's of met een klassiek virtueel netwerk in dezelfde regio, hebt u een Azure Virtual Network-gateway nodig die is ingericht in het doel VNet.

De VNet-integratiefuncties:

* Vereist een standaard-, Premium-, PremiumV2- of Elastic Premium-prijsplan.
* Ondersteuning voor TCP en UDP.
* Werken met Azure App Service-apps en functie-apps.

Er zijn een aantal dingen die VNet Integration niet ondersteunt, zoals:

* Het monteren van een station.
* Active Directory-integratie.
* Netbios.

Door gatewayvereist VNet-integratie biedt alleen toegang tot bronnen in het doel VNet of in netwerken die zijn verbonden met het doel VNet met peering of VPN's. VNet-integratie die vereist is voor poorten biedt geen toegang tot bronnen die beschikbaar zijn voor Azure ExpressRoute-verbindingen of werken met serviceeindpunten.

Ongeacht de gebruikte versie geeft VNet Integration uw app toegang tot bronnen in uw VNet, maar verleent het geen inkomende privétoegang tot uw app vanuit het VNet. Toegang tot privé-site verwijst naar het alleen toegankelijk maken van uw app vanuit een privénetwerk, bijvoorbeeld vanuit een Azure VNet. VNet Integration is alleen voor uitgaande gesprekken vanuit uw app naar uw VNet.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
