---
title: Omleiding naar URL van appservice oplossen
titleSuffix: Azure Application Gateway
description: In dit artikel vindt u informatie over het oplossen van het omleidingsprobleem wanneer Azure Application Gateway wordt gebruikt met Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293544"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Problemen met app-service oplossen in Application Gateway

Meer informatie over het diagnosticeren en oplossen van problemen die u tegenkomen wanneer Azure App Service wordt gebruikt als back-enddoel met Azure Application Gateway.

## <a name="overview"></a>Overzicht

In dit artikel leert u hoe u de volgende problemen oplossen:

> [!div class="checklist"]
> * De URL van de app-service wordt in de browser weergegeven wanneer er een omleiding is.
> * Het arraffinity-cookiedomein van de app-service is ingesteld op de naam van de app-servicehost, example.azurewebsites.net in plaats van de oorspronkelijke host.

Wanneer een back-endtoepassing een omleidingsreactie verzendt, u de client omleiden naar een andere URL dan die welke is opgegeven door de back-endtoepassing. U dit doen wanneer een app-service achter een toepassingsgateway wordt gehost en de client een omleiding naar het relatieve pad moet doen. Een voorbeeld is een omleiding van contoso.azurewebsites.net/path1 naar contoso.azurewebsites.net/path2. 

Wanneer de app-service een omleidingsreactie verzendt, gebruikt deze dezelfde hostnaam in de locatiekop van het antwoord als die in het verzoek dat wordt ontvangen van de toepassingsgateway. De client doet bijvoorbeeld het verzoek rechtstreeks om contoso.azurewebsites.net/path2 in plaats van via de toepassingsgateway contoso.com/path2. U wilt de toepassingsgateway niet omzeilen.

Dit probleem kan zich voordoen om de volgende belangrijkste redenen:

- U hebt omleiding geconfigureerd op uw app-service. Omleiding kan net zo eenvoudig zijn als het toevoegen van een trailing slash aan de aanvraag.
- U hebt Azure Active Directory-verificatie, waardoor de omleiding wordt veroorzaakt.

Wanneer u app-services achter een toepassingsgateway gebruikt, verschilt de domeinnaam die is gekoppeld aan de toepassingsgateway (example.com) van de domeinnaam van de app-service (bijvoorbeeld example.azurewebsites.net). De domeinwaarde voor de ARRAffinity-cookie die is ingesteld door de app-service draagt de example.azurewebsites.net-domeinnaam, wat niet wenselijk is. De oorspronkelijke hostnaam, example.com, moet de domeinnaamwaarde in de cookie zijn.

## <a name="sample-configuration"></a>Voorbeeldconfiguratie

- HTTP-listener: basis- of multisite
- Back-end-adresgroep: app-service
- HTTP-instellingen: **Hostname kiezen uit backendadres** ingeschakeld
- Probe: **Hostname kiezen uit HTTP-instellingen** ingeschakeld

## <a name="cause"></a>Oorzaak

App Service is een multitenant-service, dus het gebruikt de hostheader in het verzoek om het verzoek naar het juiste eindpunt te leiden. De standaarddomeinnaam van App Services, *.azurewebsites.net (bijvoorbeeld contoso.azurewebsites.net), verschilt van de domeinnaam van de toepassingsgateway (bijvoorbeeld contoso.com). 

Het oorspronkelijke verzoek van de client heeft de domeinnaam van de toepassingsgateway, contoso.com als hostnaam. U moet de toepassingsgateway configureren om de hostnaam in het oorspronkelijke verzoek te wijzigen in de hostnaam van de app-service wanneer het verzoek wordt doorgestot naar de back-end van de app-service. Gebruik de switch **Pick Hostname from Backend Address** in de HTTP-instellingsconfiguratie van de toepassingsgateway. Gebruik de schakelaar **Hostname kiezen uit HTTP-instellingen backend** in de configuratie van de statussonde.



![Toepassingsgateway wijzigt de hostnaam](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Wanneer de app-service een omleiding doet, wordt de overschreven hostnaam contoso.azurewebsites.net in de locatiekop in plaats van de oorspronkelijke hostnaam contoso.com, tenzij anders geconfigureerd. Controleer het volgende voorbeeldvan aanvraag- en antwoordkoppen.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
In het vorige voorbeeld wordt opgemerkt dat de antwoordkop een statuscode van 301 heeft voor omleiding. De locatiekop tekst heeft de hostnaam van de `www.contoso.com`app-service in plaats van de oorspronkelijke hostnaam.

## <a name="solution-rewrite-the-location-header"></a>Oplossing: de locatiekoptekst opnieuw schrijven

Stel de hostnaam in de locatiekop in op de domeinnaam van de toepassingsgateway. Maak hiervoor een [herschrijfregel](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) met een voorwaarde die evalueert of de locatiekoptekst in het antwoord azurewebsites.net bevat. Het moet ook een actie uitvoeren om de locatiekoptekst te herschrijven om de hostnaam van de toepassingsgateway te hebben. Zie instructies voor [het herschrijven van de locatiekoptekst](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)voor meer informatie.

> [!NOTE]
> De http-header herschrijven ondersteuning is alleen beschikbaar voor de [Standard_v2 en WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) van Application Gateway. Als u v1 SKU gebruikt, raden we u [aan te migreren van v1 naar v2.](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) U wilt herschrijven en andere [geavanceerde mogelijkheden](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) gebruiken die beschikbaar zijn met v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternatieve oplossing: een aangepaste domeinnaam gebruiken

Als u v1 SKU gebruikt, u de locatiekoptekst niet herschrijven. Deze mogelijkheid is alleen beschikbaar voor v2 SKU. Als u het omleidingsprobleem wilt oplossen, geeft u dezelfde hostnaam door die de toepassingsgateway ook aan de app-service ontvangt, in plaats van een hostoverschrijving uit te brengen.

De app-service doet nu de omleiding (indien van toepassing) op dezelfde oorspronkelijke hostheader die naar de toepassingsgateway verwijst en niet naar de eigen gateway.

U moet eigenaar zijn van een aangepast domein en dit proces volgen:

- Registreer het domein in de aangepaste domeinlijst van de app-service. U moet een CNAME in uw aangepaste domein hebben die verwijst naar de FQDN van de app-service. Zie [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)voor meer informatie.

    ![Aangepaste domeinlijst voor app-service](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Uw app-service is klaar `www.contoso.com`om de hostnaam te accepteren. Wijzig uw CNAME-vermelding in DNS om deze bijvoorbeeld terug te wijzen `appgw.eastus.cloudapp.azure.com`naar de FQDN van de toepassingsgateway.

- Zorg ervoor dat `www.contoso.com` uw domein wordt opgelost naar de FQDN van de toepassingsgateway wanneer u een DNS-query doet.

- Stel de aangepaste sonde in om **Hostname kiezen uit backend HTTP-instellingen**uit te schakelen. Schakel in de Azure-portal het selectievakje in de sonde-instellingen uit. Gebruik in PowerShell de schakelaar **-PickHostNameFromBackendHttpSettings** niet in de opdracht **Set-AzApplicationGatewayProbeConfig.** Voer in het veld hostnaam van de sonde de FQDN van uw app-service in, example.azurewebsites.net. De sondeaanvragen die vanuit de toepassingsgateway worden verzonden, dragen deze FQDN in de hostheader.

  > [!NOTE]
  > Controleer bij de volgende stap of uw aangepaste sonde niet is gekoppeld aan uw back-end HTTP-instellingen. Op dit moment zijn de **pickhostnaam van backendadres** ingeschakeld.

- Stel de HTTP-instellingen van de toepassingsgateway in om Hostname kiezen uit te schakelen **vanaf Backend-adres**. Schakel in de Azure-portal het selectievakje uit. Gebruik in PowerShell de schakelaar **-PickHostNameFromBackendAddress** niet in de opdracht **Set-AzApplicationGatewayBackendHttpSettings.**

- Koppel de aangepaste sonde weer aan de back-end HTTP-instellingen en controleer of de back-end in orde is.

- De toepassingsgateway moet nu dezelfde `www.contoso.com`hostnaam, , doorsturen naar de app-service. De omleiding gebeurt op dezelfde hostnaam. Controleer het volgende voorbeeldvan aanvraag- en antwoordkoppen.

Als u de vorige stappen met PowerShell wilt implementeren voor een bestaande installatie, gebruikt u het voorbeeld powershell-script dat volgt. Houd er rekening mee dat we de **-PickHostname-switches** niet hebben gebruikt in de configuratie van de sonde en http-instellingen.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Volgende stappen

Als het probleem in de voorgaande stappen niet is opgelost, opent u een [ondersteuningsticket](https://azure.microsoft.com/support/options/).
