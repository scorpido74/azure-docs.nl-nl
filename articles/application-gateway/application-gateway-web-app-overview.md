---
title: Multi-tenant back-ends
titleSuffix: Azure Application Gateway
description: Op deze pagina wordt de ondersteuning voor back-ends met meerdere tenants in Application Gateway beschreven.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: efa2885ce0534c5d78bb08bbf24da59850f6ea22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075180"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Application Gateway-ondersteuning voor back-ends met meerdere tenant's, zoals app-service

In architecturale ontwerpen met meerdere tenantn in webservers worden meerdere websites uitgevoerd op dezelfde webserverinstantie. Hostnames worden gebruikt om onderscheid te maken tussen de verschillende toepassingen die worden gehost. Application Gateway wijzigt standaard niet de binnenkomende HTTP-host-header van de client en stuurt de header ongewijzigd terug naar de back-end. Dit werkt goed voor backend pool leden zoals NIC's, virtuele machine schaal sets, openbare IP-adressen, interne IP-adressen en FQDN als deze niet vertrouwen op een specifieke host header of SNI extensie op te lossen om het juiste eindpunt. Er zijn echter veel services, zoals Azure App-serviceweb-apps en Azure API-beheer die multi-tenant van aard zijn en vertrouwen op een specifieke hostheader of SNI-extensie om het juiste eindpunt op te lossen. Meestal is de DNS-naam van de toepassing, die op zijn beurt de DNS-naam is die is gekoppeld aan de toepassingsgateway, anders dan de domeinnaam van de backendservice. Daarom is de hostheader in de oorspronkelijke aanvraag die door de toepassingsgateway is ontvangen, niet dezelfde als de hostnaam van de backendservice. Hierdoor kunnen de back-ends van de multitenant back-ends het verzoek naar het juiste eindpunt niet oplossen, tenzij de hostheader in de aanvraag van de toepassingsgateway naar de backend wordt gewijzigd in de hostnaam van de backendservice. 

Application Gateway biedt een functie waarmee gebruikers de HTTP host-header in de aanvraag kunnen overschrijven op basis van de hostnaam van de back-end. Dankzij deze functie is er ondersteuning voor back-ends met meerdere tenants, zoals web-apps van Azure App Service en API-beheer. Deze functie is beschikbaar voor zowel de v1- en v2-standaard- als de WAF-SKU. 

![host overschrijven](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Dit is niet van toepassing op de Azure App-serviceomgeving (ASE), omdat ASE een speciale bron is in tegenstelling tot Azure App-service, een multi-tenantbron.

## <a name="override-host-header-in-the-request"></a>Hostheader in de aanvraag overschrijven

De mogelijkheid om een hostoverschrijving op te geven, wordt gedefinieerd in de [HTTP-instellingen](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) en kan worden toegepast op elke back-endpool tijdens het maken van de regel. De volgende twee manieren om hostheader en SNI-extensie voor back-ends met meerdere tenant's te overschrijven, worden ondersteund:

- De mogelijkheid om de hostnaam in te stellen op een vaste waarde die expliciet is ingevoerd in de HTTP-instellingen. Deze mogelijkheid zorgt ervoor dat de hostheader wordt overschreven op deze waarde voor al het verkeer naar de back-endpool waar de specifieke HTTP-instellingen worden toegepast. Als u end-to-end SSL gebruikt, wordt de overschreven hostnaam gebruikt in de SNI-extensie. Met deze mogelijkheid kunnen scenario's worden mogelijk waarin een back-endpoolfarm een hostheader verwacht die verschilt van de inkomende header voor klantenhost.

- De mogelijkheid om de hostnaam af te leiden van het IP of FQDN van de back-end poolleden. HTTP-instellingen bieden ook een optie om dynamisch de hostnaam te kiezen uit de FQDN van een back-endpoollid als deze is geconfigureerd met de optie om de hostnaam af te leiden van een afzonderlijk back-endpoollid. Als u end-to-end SSL gebruikt, wordt de hostnaam afgeleid van de FQDN en gebruikt in de SNI-extensie. Met deze mogelijkheid kunnen scenario's worden gemaakt waarin een back-endpool twee of meer multi-tenant PaaS-services zoals Azure-webapps kan hebben en de hostheader van het verzoek voor elk lid de hostnaam bevat die is afgeleid van de FQDN. Voor het implementeren van dit scenario gebruiken we een schakelaar in de HTTP-instellingen met de naam [Pick hostname van backend-adres,](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) waarmee de hostheader in het oorspronkelijke verzoek dynamisch wordt overschreven aan de kop tekst die in de backendpool wordt vermeld.  Als uw backendpool FQDN bijvoorbeeld 'contoso11.azurewebsites.net' en 'contoso22.azurewebsites.net' bevat, wordt de hostheader van het oorspronkelijke verzoek, die wordt contoso.com, overschreven naar contoso11.azurewebsites.net of contoso22.azurewebsites.net wanneer het verzoek naar de juiste backendserver wordt verzonden. 

  ![web-app-scenario](./media/application-gateway-web-app-overview/scenario.png)

Met deze functie kunnen klanten opties opgeven in de HTTP-instellingen en kunnen ze aangepaste tests uitvoeren voor de gewenste configuratie. Deze instelling wordt vervolgens gekoppeld aan een listener en een back-endpool met behulp van een regel.

## <a name="special-considerations"></a>Bijzondere overwegingen

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>SSL-beëindiging en end-to-end SSL met multi-tenantservices

Zowel SSL-beëindiging als end-to-end SSL-versleuteling worden ondersteund met multi-tenantservices. Voor SSL-beëindiging bij de toepassingsgateway moet ssl-certificaat nog steeds worden toegevoegd aan de serverlistener van de toepassingsgateway. In het geval van ssl van end-to-end hoeven vertrouwde Azure-services, zoals Web-apps voor Azure App-services, de backends in de toepassingsgateway echter niet op de witte lijst te zetten. Daarom is het niet nodig om verificatiecertificaten toe te voegen. 

![end-to-end SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

In de bovenstaande afbeelding is het niet nodig om verificatiecertificaten toe te voegen wanneer de app-service is geselecteerd als backend.

### <a name="health-probe"></a>Statustest

Het overschrijven van de hostheader in de **HTTP-instellingen** heeft alleen invloed op de aanvraag en de routering. het heeft geen invloed op het gedrag van de gezondheidssonde. Als u wilt dat de end-to-end configuratie werkt, moeten zowel de test als de HTTP-instellingen worden bewerkt op basis van de juiste configuratie. Aangepaste sondes bieden niet alleen de mogelijkheid om een hostheader in de sondeconfiguratie op te geven, maar ondersteunen ook de mogelijkheid om de hostheader af te leiden van de momenteel geconfigureerde HTTP-instellingen. Deze configuratie kan worden opgegeven met behulp van de parameter `PickHostNameFromBackendHttpSettings` in de testconfiguratie.

### <a name="redirection-to-app-services-url-scenario"></a>Omleiding naar het URL-scenario van App-service

Er kunnen scenario's zijn waarin de hostnaam in het antwoord van de App-service de browser van de eindgebruiker kan doorverwijzen naar de hostnaam *.azurewebsites.net in plaats van het domein dat is gekoppeld aan de Toepassingsgateway. Dit probleem kan zich voordoen wanneer:

- U hebt omleiding geconfigureerd op uw App Service. Omleiding kan net zo eenvoudig zijn als het toevoegen van een trailing slash aan de aanvraag.
- U hebt Azure AD-verificatie die de omleiding veroorzaakt.

Zie Omleiding naar [het URL-probleem van de app-service oplossen](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)als u dergelijke gevallen wilt oplossen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een toepassingsgateway met een multi-tenant-app, zoals azure app-serviceweb-app als back-endpoollid, door te gaan naar [App Service-webapps configureren met Application Gateway](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
