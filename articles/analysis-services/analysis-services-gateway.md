---
title: On-premises datagateway voor Azure Analysis Services | Microsoft Documenten
description: Een on-premises gateway is noodzakelijk als uw Analysis Services-server in Azure verbinding maakt met on-premises gegevensbronnen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310149"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Verbinding maken met on-premises gegevensbronnen met on-premises datagateway

De on-premises datagateway biedt veilige gegevensoverdracht tussen on-premises gegevensbronnen en uw Azure Analysis Services-servers in de cloud. Naast het werken met meerdere Azure Analysis Services-servers in dezelfde regio, werkt de nieuwste versie van de gateway ook met Azure Logic Apps, Power BI, Power Apps en Power Automate. Hoewel de gateway die u installeert hetzelfde is voor al deze services, hebben Azure Analysis Services en Logic Apps enkele extra stappen.

De hier verstrekte informatie is specifiek voor de manier waarop Azure Analysis Services werkt met de on-premises datagateway. Zie [Wat is een on-premises datagateway voor](/data-integration/gateway/service-gateway-onprem)meer informatie over de gateway in het algemeen en hoe deze werkt met andere services.

Voor Azure Analysis Services is het de eerste keer dat u de gateway instelt, een proces met vier delen:

- **Installatie downloaden en uitvoeren** - Met deze stap wordt een gatewayservice geïnstalleerd op een computer in uw organisatie. U meldt zich ook aan bij Azure met een account in het Azure AD van uw [tenant.](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure B2B-accounts (gast)accounts worden niet ondersteund.

- **Uw gateway registreren** - In deze stap geeft u een naam en herstelsleutel voor uw gateway op en selecteert u een regio, waarbij u uw gateway registreert met de Gateway Cloud Service. Uw gatewaybron kan in elke regio worden geregistreerd, maar het wordt aanbevolen in dezelfde regio als uw Analysis Services-servers. 

- **Maak een gatewaybron in Azure** - In deze stap maakt u een gatewaybron in Azure.

- **Verbind uw servers met uw gatewaybron** - Zodra u een gatewaybron hebt, u beginnen met het verbinden van servers. U meerdere servers en andere bronnen verbinden, mits deze zich in dezelfde regio bevinden.



## <a name="how-it-works"></a><a name="how-it-works"> </a>Hoe het werkt
De gateway die u installeert op een computer in uw organisatie wordt uitgevoerd als een **Windows-service, on-premises gegevensgateway.** Deze lokale service is geregistreerd bij de Gateway-cloudservice via Azure Service Bus. Vervolgens maakt u een on-premises datagatewaybron voor uw Azure-abonnement. Uw Azure Analysis Services-servers worden vervolgens verbonden met uw Azure-gatewaybron. Wanneer modellen op uw server verbinding moeten maken met uw on-premises gegevensbronnen voor query's of verwerking, doorkruisen een query- en gegevensstroom de gatewaybron, Azure Service Bus, de lokale on-premises gegevensgatewayservice en uw gegevensbronnen. 

![Hoe werkt het?](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Query's en gegevensstroom:

1. Er wordt een query gemaakt door de cloudservice met de versleutelde referenties voor de on-premises gegevensbron. De query wordt vervolgens ter verwerking naar de gateway verzonden.
2. De gatewaycloudservice analyseert de query en duwt de aanvraag naar de [Azure Service Bus.](https://azure.microsoft.com/documentation/services/service-bus/)
3. De on-premises gegevensgateway peilt Azure Service Bus om te kijken of er aanvragen klaarstaan.
4. De gateway haalt de query op, ontsleutelt de referenties en maakt met behulp hiervan verbinding met de gegevensbronnen.
5. De gateway stuurt de query voor uitvoering naar de gegevensbron.
6. De resultaten worden vanuit de gegevensbron teruggezonden naar de gateway en vervolgens naar de cloudservice en uw server.

## <a name="installing"></a>Installeren

Bij het installeren voor een Azure Analysis Services-omgeving is het belangrijk dat u de stappen volgt die zijn beschreven in [Het installeren en configureren van on-premises gegevensgateway voor Azure Analysis Services.](analysis-services-gateway-install.md) Dit artikel is specifiek voor Azure Analysis Services. Het bevat extra stappen die nodig zijn om een on-premises gegevensgatewaybron in Azure in te stellen en uw Azure Analysis Services-server met de bron te verbinden.

## <a name="ports-and-communication-settings"></a>Poorten en communicatie-instellingen

De gateway maakt een uitgaande verbinding naar Azure Service Bus. De gateway communiceert via uitgaande poorten: TCP 443 (standaard), 5671, 5672, 9350 t/m 9354.  De gateway vereist geen inkomende poorten.

Mogelijk moet u IP-adressen voor uw gegevensregio opnemen in uw firewall. U kunt de lijst met IP-adressen van Microsoft Azure-datacenters [hier](https://www.microsoft.com/download/details.aspx?id=56519) downloaden. Deze lijst wordt wekelijks bijgewerkt. De adressen in de lijst met IP-adressen van Azure-datacenters worden vermeld in de CIDR-notatie. Zie [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)voor meer informatie.

De volgende zijn volledig gekwalificeerde domeinnamen die door de gateway worden gebruikt.

| Domeinnamen | Uitgaande poorten | Beschrijving |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP wordt gebruikt om het installatiebestand te downloaden. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listeners op Service Bus Relay via TCP (vereist 443 voor het ophalen van tokens voor toegangsbeheer) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Gebruikt voor het testen van de internetverbinding als de gateway onbereikbaar is voor de Power BI-service. |
| *.microsoftonline-p.com |443 |Wordt gebruikt voor verificatie, afhankelijk van de configuratie. |
| dc.services.visualstudio.com  |443 |Gebruikt door AppInsights om telemetrie te verzamelen. |

### <a name="forcing-https-communication-with-azure-service-bus"></a><a name="force-https"></a>HTTPS-communicatie met Azure Service Bus afdwingen

U de gateway dwingen om te communiceren met Azure Service Bus door HTTPS te gebruiken in plaats van directe TCP; dit kan echter de prestaties sterk verminderen. U het bestand *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* wijzigen `AutoDetect` `Https`door de waarde te wijzigen van. Dit bestand bevindt zich meestal op *C:\Program Files\On-premises datagateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Volgende stappen 

De volgende artikelen zijn opgenomen in de algemene inhoud van de on-premises gegevensgateway die van toepassing is op alle services die de gateway ondersteunt:

* [FAQ voor on-premises gegevensgateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Gebruik de on-premises gegevensgateway-app](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Beheer op tenantniveau](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Proxy-instellingen configureren](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Communicatie-instellingen aanpassen](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Logboekbestanden configureren](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Oplossen](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Gatewayprestaties bewaken en optimaliseren](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
