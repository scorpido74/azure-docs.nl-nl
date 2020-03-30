---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671524"
---
* De multi-tenant systemen die het volledige scala aan prijsplannen ondersteunen, behalve
* De App Service Environment (ASE), die wordt geïmplementeerd in uw VNet en geïsoleerde apps voor prijsplannen ondersteunt

Dit document gaat door de VNet-integratiefunctie, die is bedoeld voor gebruik in de multi-tenant-apps. Als uw app zich in [de app-serviceomgeving bevindt,][ASEintro]bevindt deze zich al in een VNet en hoeft u de Functie VNet-integratie niet te gebruiken om bronnen in hetzelfde VNet te bereiken. Lees app [Service-netwerkfuncties][Networkingfeatures] voor meer informatie over alle netwerkfuncties

VNet-integratie geeft uw app toegang tot bronnen in uw virtuele netwerk, maar verleent geen inkomende privétoegang tot uw app vanuit het VNet. Toegang tot privé-site verwijst naar het alleen toegankelijk maken van apps vanuit een privénetwerk, bijvoorbeeld vanuit een virtueel Azure-netwerk. VNet Integration is alleen voor uitgaande gesprekken vanuit uw app naar uw VNet. De VNet-integratiefunctie gedraagt zich anders wanneer deze wordt gebruikt met VNets in dezelfde regio en met VNets in andere regio's. De VNet Integration-functie heeft twee varianten.

* Regionale VNet-integratie - Wanneer u verbinding maakt met VNets van Resource Manager in dezelfde regio, moet u een speciaal subnet in het VNet hebben waarmee u integreert. 
* Gateway vereist VNet-integratie - Wanneer u verbinding maakt met VNets in andere regio's of met een Klassieke VNet in dezelfde regio, hebt u een Virtual Network-gateway nodig die is ingericht in het doel VNet.

De VNet-integratiefuncties:

* een standaard-, Premium-, PremiumV2- of Elastic Premium-prijsplan vereisen 
* ondersteuning voor TCP en UDP
* werken met App Service-apps en Functie-apps

Er zijn een aantal dingen die VNet Integration niet ondersteunt, waaronder:

* het monteren van een station
* AD-integratie 
* NetBios

Gateway vereist VNet-integratie biedt alleen toegang tot bronnen in het doel VNet of in netwerken die zijn verbonden met het doel VNet met peering of VPN's. Vereiste Gateway VNet-integratie biedt geen toegang tot bronnen die beschikbaar zijn voor ExpressRoute-verbindingen of werkt met serviceeindpunten. 

Ongeacht de gebruikte versie geeft VNet Integration uw app toegang tot bronnen in uw virtuele netwerk, maar verleent het geen inkomende privétoegang tot uw app vanuit het virtuele netwerk. Toegang tot privé-site verwijst naar het alleen toegankelijk maken van uw app vanuit een privénetwerk, bijvoorbeeld vanuit een virtueel Azure-netwerk. VNet Integration is alleen voor uitgaande gesprekken vanuit uw app naar uw VNet. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features