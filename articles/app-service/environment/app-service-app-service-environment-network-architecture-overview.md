---
title: Netwerkarchitectuur v1
description: Bouwkundig overzicht van netwerktopologie van App Service Environments. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243846"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Overzicht van netwerkarchitectuur van app-serviceomgevingen
App-serviceomgevingen worden altijd gemaakt binnen een subnet van een [virtueel netwerk][virtualnetwork] - apps die worden uitgevoerd in een App-serviceomgeving kunnen communiceren met privéeindpunten binnen dezelfde virtuele netwerktopologie.  Aangezien klanten delen van hun virtuele netwerkinfrastructuur kunnen vergrendelen, is het belangrijk om inzicht te krijgen in de typen netwerkcommunicatiestromen die optreden met een App Service-omgeving.

## <a name="general-network-flow"></a>Algemene netwerkstroom
Wanneer een App Service Environment (ASE) een openbaar virtueel IP-adres (VIP) gebruikt voor apps, komt al het binnenkomende verkeer op die openbare VIP.  Dit omvat HTTP- en HTTPS-verkeer voor apps, evenals ander verkeer voor FTP, functionaliteit voor foutopsporing op afstand en Azure-beheerbewerkingen.  Voor een volledige lijst van de specifieke poorten (zowel vereist als optioneel) die beschikbaar zijn op de openbare VIP zie het artikel over het regelen van [binnenkomend verkeer][controllinginboundtraffic] naar een App Service Omgeving. 

App Service-omgevingen ondersteunen ook het uitvoeren van apps die alleen zijn gekoppeld aan een intern netwerkadres, ook wel ILB-adres (interne load balancer) genoemd.  Op een ILB ingeschakeld ASE, HTTP en HTTPS-verkeer voor apps, evenals externe debugging oproepen, komen op het ILB-adres.  Voor de meest voorkomende ILB-ASE-configuraties komt FTP/FTPS-verkeer ook aan op het ILB-adres.  Azure-beheerbewerkingen worden echter nog steeds naar poorten 454/455 op de openbare VIP van een ASE met ILB ingeschakeld.

Het onderstaande diagram toont een overzicht van de verschillende inkomende en uitgaande netwerkstromen voor een App Service-omgeving waar de apps zijn gebonden aan een openbaar virtueel IP-adres:

![Algemene netwerkstromen][GeneralNetworkFlows]

Een App Service-omgeving kan communiceren met verschillende eindpunten van particuliere klanten.  Apps die in de app-serviceomgeving worden uitgevoerd, kunnen bijvoorbeeld verbinding maken met databaseserver(s) die worden uitgevoerd op virtuele IaaS-machines in dezelfde virtuele netwerktopologie.

> [!IMPORTANT]
> Als u het netwerkdiagram bekijkt, worden de 'Overige rekenbronnen' geïmplementeerd in een ander subnet dan de App-serviceomgeving. Als u resources in hetzelfde subnet met de ASE implementeert, wordt de verbinding van ASE met die resources geblokkeerd (met uitzondering van specifieke intra-ASE-routering). In plaats daarvan implementeren naar een ander subnet (in hetzelfde VNET). De App Service Environment kan dan verbinding maken. Er is geen extra configuratie nodig.
> 
> 

App-serviceomgevingen communiceren ook met Sql DB- en Azure Storage-resources die nodig zijn voor het beheren en beheren van een App-serviceomgeving.  Sommige sql- en opslagbronnen waarmee een App-serviceomgeving communiceert, bevinden zich in dezelfde regio als de App-serviceomgeving, terwijl andere zich in externe Azure-regio's bevinden.  Als gevolg hiervan is uitgaande connectiviteit met het internet altijd vereist om een App Service-omgeving goed te laten functioneren. 

Aangezien een App-serviceomgeving in een subnet wordt geïmplementeerd, kunnen netwerkbeveiligingsgroepen worden gebruikt om binnenkomend verkeer naar het subnet te beheren.  Zie het volgende [artikel][controllinginboundtraffic]voor meer informatie over het beheren van binnenkomend verkeer naar een App-serviceomgeving.

Zie het volgende artikel over het werken met [Express Route][ExpressRoute]voor meer informatie over het toestaan van uitgaande internetverbinding vanuit een App-serviceomgeving.  Dezelfde benadering die in het artikel wordt beschreven, is van toepassing bij het werken met site-to-site-connectiviteit en het gebruik van gedwongen tunneling.

## <a name="outbound-network-addresses"></a>Uitgaande netwerkadressen
Wanneer een App Service-omgeving uitgaande gesprekken voert, wordt een IP-adres altijd gekoppeld aan de uitgaande oproepen.  Het specifieke IP-adres dat wordt gebruikt, is afhankelijk van de vraag of het endpoint dat wordt aangeroepen zich bevindt binnen de virtuele netwerktopologie of buiten de virtuele netwerktopologie.

Als het endpoint dat wordt aangeroepen **buiten** de virtuele netwerktopologie valt, is het uitgaande adres (ook bekend als het uitgaande NAT-adres) dat wordt gebruikt de openbare VIP van de App-serviceomgeving.  Dit adres is te vinden in de gebruikersinterface van de portal voor de App Service-omgeving in het blad Eigenschappen.

![Uitgaand IP-adres][OutboundIPAddress]

Dit adres kan ook worden bepaald voor AS's die alleen een openbare VIP hebben door een app te maken in de App Service-omgeving en vervolgens een *nslookup* uit te voeren op het adres van de app. Het resulterende IP-adres is zowel de openbare VIP als het uitgaande NAT-adres van de App Service Environment.

Als het eindpunt dat wordt aangeroepen **zich binnen** de virtuele netwerktopologie bevindt, is het uitgaande adres van de aanroepende app het interne IP-adres van de afzonderlijke compute resource waarop de app wordt uitgevoerd.  Er is echter geen permanente toewijzing van interne IP-adressen van virtuele netwerken naar apps.  Apps kunnen zich verplaatsen over verschillende rekenresources en de groep beschikbare rekenbronnen in een App-serviceomgeving kan worden gewijzigd als gevolg van schaalbewerkingen.

Aangezien een App Service-omgeving zich echter altijd in een subnet bevindt, bent u er zeker van dat het interne IP-adres van een compute resource waarop een app wordt uitgevoerd, altijd binnen het CIDR-bereik van het subnet zal liggen.  Als gevolg hiervan moet het subnetbereik met de App Service-omgeving toegang krijgen wanneer fijnmazige ACL's of netwerkbeveiligingsgroepen worden gebruikt om toegang te krijgen tot andere eindpunten binnen het virtuele netwerk.

In het volgende diagram worden deze concepten nader weergegeven:

![Uitgaande netwerkadressen][OutboundNetworkAddresses]

In het bovenstaande diagram:

* Aangezien de openbare VIP van de App Service Environment is 192.23.1.2, dat is de uitgaande IP-adres gebruikt bij het maken van gesprekken naar "Internet" eindpunten.
* Het CIDR-bereik van het subnet voor de App Service-omgeving is 10.0.1.0/26.  Andere eindpunten binnen dezelfde virtuele netwerkinfrastructuur zien oproepen van apps als afkomstig van ergens binnen dit adresbereik.

## <a name="calls-between-app-service-environments"></a>Gesprekken tussen app-serviceomgevingen
Er kan een complexer scenario optreden als u meerdere App-serviceomgevingen implementeert in hetzelfde virtuele netwerk en uitgaande gesprekken maakt van de ene App Service-omgeving naar een andere App Service-omgeving.  Dit soort cross App Service Environment calls worden ook behandeld als "Internet" gesprekken.

In het volgende diagram ziet u een voorbeeld van een gelaagde architectuur met apps op één App Service-omgeving (bijvoorbeeld webapps 'Voordeur') die apps aanroepen in een tweede App Service-omgeving (bijvoorbeeld interne back-end API-apps die niet bedoeld zijn om toegankelijk te zijn vanaf internet). 

![Gesprekken tussen app-serviceomgevingen][CallsBetweenAppServiceEnvironments] 

In het bovenstaande voorbeeld heeft de App Service Environment "ASE One" een uitgaand IP-adres van 192.23.1.2.  Als een app die wordt uitgevoerd in deze app-serviceomgeving een uitgaande oproep doet naar een app die wordt uitgevoerd op een tweede App Service-omgeving ('ASE 2') in hetzelfde virtuele netwerk, wordt het uitgaande gesprek behandeld als een 'internetgesprek'.  Als gevolg hiervan zal het netwerkverkeer dat op de tweede App Service Omgeving aankomt, worden weergegeven als afkomstig van 192.23.1.2 (dus niet het subnetadresbereik van de eerste App Service Environment).

Hoewel gesprekken tussen verschillende App-serviceomgevingen worden behandeld als 'internet'-oproepen, blijft het netwerkverkeer op het regionale Azure-netwerk en stroomt het niet fysiek over het regionale Azure-netwerk openbaar internet.  Als gevolg hiervan u een netwerkbeveiligingsgroep op het subnet van de tweede App-serviceomgeving gebruiken om alleen binnenkomende oproepen toe te staan vanuit de eerste App Service-omgeving (waarvan het uitgaande IP-adres 192.23.1.2 is), waardoor een veilige communicatie tussen de App wordt gewaarborgd Serviceomgevingen.

## <a name="additional-links-and-information"></a>Aanvullende koppelingen en -informatie
Details over binnenkomende poorten die worden gebruikt door App-serviceomgevingen en het gebruik van netwerkbeveiligingsgroepen om binnenkomend verkeer te beheren, zijn [hier][controllinginboundtraffic]beschikbaar.

Details over het gebruik van door de gebruiker gedefinieerde routes om uitgaande internettoegang tot App-serviceomgevingen te verlenen, zijn beschikbaar in dit [artikel.][ExpressRoute] 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

