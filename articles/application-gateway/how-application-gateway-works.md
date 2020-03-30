---
title: Hoe een toepassingsgateway werkt
description: In dit artikel vindt u informatie over hoe een toepassingsgateway binnenkomende aanvragen accepteert en deze naar de backend leidt.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 84a7bdfb9f8f7c741140cbe2086149dff90db211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132972"
---
# <a name="how-an-application-gateway-works"></a>Hoe een toepassingsgateway werkt

In dit artikel wordt uitgelegd hoe een toepassingsgateway binnenkomende aanvragen accepteert en deze naar de backend leidt.

![Hoe een toepassingsgateway een aanvraag accepteert](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Hoe een toepassingsgateway een aanvraag accepteert

1. Voordat een client een aanvraag naar een toepassingsgateway verzendt, wordt de domeinnaam van de toepassingsgateway opgelost met behulp van een DNS-server (Domain Name System). Azure beheert de DNS-invoer omdat alle toepassingsgateways zich in het azure.com-domein bevinden.

2. De Azure DNS retourneert het IP-adres naar de client, het ip-adres van de clientgateway.

3. De toepassingsgateway accepteert binnenkomend verkeer op een of meer listeners. Een listener is een logische entiteit die controleert op verbindingsaanvragen. Het is geconfigureerd met een frontend IP-adres, protocol en poortnummer voor verbindingen van clients naar de toepassingsgateway.

4. Als er een webtoepassingsfirewall (WAF) in gebruik is, controleert de toepassingsgateway de aanvraagkoppen en de instantie, indien aanwezig, aan de WAF-regels. Deze actie bepaalt of de aanvraag een geldig verzoek of een beveiligingsbedreiging is. Als het verzoek geldig is, wordt het doorgestuurd naar de backend. Als het verzoek niet geldig is en WAF in de preventiemodus staat, wordt het geblokkeerd als een beveiligingsbedreiging. Als het in de detectiemodus staat, wordt het verzoek geëvalueerd en geregistreerd, maar nog steeds doorgestuurd naar de backendserver.

Azure Application Gateway kan worden gebruikt als interne load balancer voor toepassingen of als een op internet gerichte toepassingslastenbalans. Een toepassinggateway met internetgerichte toepassing maakt gebruik van openbare IP-adressen. De DNS-naam van een internet-facing application gateway is openbaar oplosbaar naar zijn openbare IP-adres. Als gevolg hiervan kunnen internetgerichte toepassingsgateways clientverzoeken naar het internet leiden.

Interne toepassingsgateways gebruiken alleen privé-IP-adressen. Als u een aangepaste of [privé DNS-zone gebruikt,](https://docs.microsoft.com/azure/dns/private-dns-overview)moet de domeinnaam intern oplosbaar zijn naar het privé-IP-adres van de Application Gateway. Daarom kunnen interne load-balancers alleen aanvragen van clients met toegang tot een virtueel netwerk voor de toepassingsgateway routeren.

## <a name="how-an-application-gateway-routes-a-request"></a>Hoe een toepassingsgateway een aanvraag leidt

Als een aanvraag geldig is en niet wordt geblokkeerd door WAF, evalueert de toepassingsgateway de regel voor het routeren van aanvragen die aan de listener is gekoppeld. Met deze actie bepaalt u naar welke backendpool de aanvraag moet worden doorgestuurd.

Op basis van de regel voor het routeren van aanvragen bepaalt de toepassingsgateway of alle aanvragen voor de listener moeten worden doorgestuurd naar een specifieke backendpool, aanvragen naar verschillende backendpools op basis van het URL-pad of aanvragen moeten doorverwijzen naar een andere poort of externe site.
>[!NOTE]
>Regels worden verwerkt in de volgorde waarin ze worden vermeld in de portal voor v1 SKU. 

Wanneer de toepassingsgateway de backendpool selecteert, wordt het verzoek naar een van de gezonde backendservers in de groep (y.y.y.y) geleid. De status van de server wordt bepaald door een statussonde. Als de backendpool meerdere servers bevat, gebruikt de toepassingsgateway een round-robin-algoritme om de aanvragen tussen gezonde servers te routeren. Deze belasting balanceert de aanvragen op de servers.

Nadat de toepassingsgateway de backendserver heeft bepaald, wordt een nieuwe TCP-sessie geopend met de backendserver op basis van HTTP-instellingen. HTTP-instellingen geven het protocol, de poort en andere routeringsgerelateerde instellingen op die nodig zijn om een nieuwe sessie met de backendserver vast te stellen.

De poort en het protocol die in HTTP-instellingen worden gebruikt, bepalen of het verkeer tussen de toepassingsgateway en backendservers is versleuteld (waardoor end-to-end TLS wordt uitgevoerd) of niet is versleuteld.

Wanneer een toepassingsgateway het oorspronkelijke verzoek naar de backendserver verzendt, wordt elke aangepaste configuratie in de HTTP-instellingen die zijn gemaakt met betrekking tot het overschrijven van de hostnaam, het pad en het protocol eer. Deze actie behoudt sessieaffiniteit op basis van cookies, het aftappen van verbindingen, selectie van hostnamen vanaf de backend, enzovoort.

 >[!NOTE]
>Als de backend pool:
> - **Is een openbaar eindpunt**, de toepassingsgateway gebruikt zijn frontend openbare IP om de server te bereiken. Als er geen openbaar IP-adres aan de voorzijde is, wordt er een toegewezen voor de uitgaande externe connectiviteit.
> - **Bevat een intern oplosbare FQDN of een privé-IP-adres,** de toepassingsgateway leidt het verzoek naar de backendserver met behulp van de instantie privé IP-adressen.
> - **Bevat een extern eindpunt of een extern oplosbare FQDN,** de toepassingsgateway leidt het verzoek naar de backendserver met behulp van het openbare IP-adres aan de voorkant. De DNS-resolutie is gebaseerd op een privé-DNS-zone of aangepaste DNS-server, indien geconfigureerd, of gebruikt de standaard dns-infrastructuur die is voorzien in Azure. Als er geen openbaar IP-adres aan de voorzijde is, wordt er een toegewezen voor de uitgaande externe connectiviteit.

### <a name="modifications-to-the-request"></a>Wijzigingen in het verzoek

Een toepassingsgateway voegt vier extra kopteksten toe aan alle aanvragen voordat de aanvragen naar de backend worden doorgestuurd. Deze headers zijn x-forwarded-for, x-forwarded-proto, x-forwarded-port en x-original-host. De indeling voor x-forwarded-for header is een door komma's gescheiden lijst met IP:port.

De geldige waarden voor x-forwarded-proto zijn HTTP of HTTPS. X-forwarded-port geeft de poort aan waar de aanvraag de toepassingsgateway heeft bereikt. X-original-host header bevat de originele host header waarmee de aanvraag is aangekomen. Deze header is handig in azure-website-integratie, waarbij de inkomende hostheader wordt gewijzigd voordat het verkeer naar de backend wordt doorgestuurd. Als sessieaffiniteit als optie is ingeschakeld, wordt een door de gateway beheerde affiniteitscookie toegevoegd.

U de toepassingsgateway configureren om kopteksten te wijzigen met [HTTP-headers opnieuw schrijven](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) of het URI-pad te wijzigen met behulp van een instelling voor padoverschrijven. Tenzij geconfigureerd, worden alle binnenkomende aanvragen echter naar de backend geproxieerd.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over toepassingsgatewaycomponenten](application-gateway-components.md)
