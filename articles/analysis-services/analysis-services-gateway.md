---
title: On-premises gegevens gateway voor Azure Analysis Services | Microsoft Docs
description: Een on-premises gateway is nodig als uw Analysis Services-server in azure verbinding maakt met on-premises gegevens bronnen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76310149"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Verbinding maken met on-premises gegevens bronnen met on-premises gegevens gateway

De on-premises gegevens gateway biedt veilige gegevens overdracht tussen on-premises gegevens bronnen en uw Azure Analysis Services servers in de Cloud. Naast het werken met meerdere Azure Analysis Services servers in dezelfde regio, werkt de meest recente versie van de gateway ook met Azure Logic Apps, Power BI, Power apps en automatische energie. Hoewel de gateway die u installeert hetzelfde is voor al deze services, Azure Analysis Services en Logic Apps enkele extra stappen hebben.

De informatie die hier wordt gegeven, is specifiek voor de manier waarop Azure Analysis Services werkt met de on-premises gegevens gateway. Zie [Wat is een on-premises gegevens gateway?](/data-integration/gateway/service-gateway-onprem)voor meer informatie over de gateway in het algemeen en hoe deze werkt met andere services.

Voor Azure Analysis Services is het voorbereiden van de installatie met de gateway de eerste keer een proces met vier delen:

- **Setup downloaden en uitvoeren** : met deze stap installeert u een gateway service op een computer in uw organisatie. U kunt zich ook aanmelden bij Azure met behulp van een account in de Azure AD [van uw Tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) . Accounts van Azure B2B (gast) worden niet ondersteund.

- **Uw gateway registreren** : in deze stap geeft u een naam en herstel sleutel op voor uw gateway en selecteert u een regio, waarbij u uw gateway registreert bij de gateway-Cloud service. Uw gateway resource kan in elke regio worden geregistreerd, maar het wordt aanbevolen dat deze zich in dezelfde regio bevindt als uw Analysis Services-servers. 

- **Een gateway bron maken in azure** : in deze stap maakt u een gateway bron in Azure.

- **Uw servers verbinden met uw gateway resource** : Zodra u een gateway resource hebt, kunt u aan de slag gaan met het verbinden van de servers. U kunt meerdere servers en andere resources verbinden, mits deze zich in dezelfde regio bevinden.



## <a name="how-it-works"></a><a name="how-it-works"> </a>Uitleg
De gateway die u op een computer in uw organisatie installeert, wordt uitgevoerd als een Windows-service, **on-premises gegevens gateway**. Deze lokale service is geregistreerd bij de Gateway-cloudservice via Azure Service Bus. Vervolgens maakt u een on-premises gegevens gateway resource voor uw Azure-abonnement. Uw Azure Analysis Services-servers worden vervolgens verbonden met uw Azure gateway-resource. Wanneer modellen op uw server verbinding moeten maken met uw on-premises gegevens bronnen voor query's of verwerking, passeren een query en gegevens stroom de gateway resource, Azure Service Bus, de lokale on-premises gegevens Gateway Service en uw gegevens bronnen. 

![Uitleg](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Query's en gegevensstroom:

1. Er wordt een query gemaakt door de cloudservice met de versleutelde referenties voor de on-premises gegevensbron. De query wordt vervolgens ter verwerking naar de gateway verzonden.
2. De gateway-Cloud service analyseert de query en duwt de aanvraag naar het [Azure service bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. De on-premises gegevensgateway peilt Azure Service Bus om te kijken of er aanvragen klaarstaan.
4. De gateway haalt de query op, ontsleutelt de referenties en maakt met behulp hiervan verbinding met de gegevensbronnen.
5. De gateway stuurt de query voor uitvoering naar de gegevensbron.
6. De resultaten worden vanuit de gegevensbron teruggezonden naar de gateway en vervolgens naar de cloudservice en uw server.

## <a name="installing"></a>Wordt geïnstalleerd

Wanneer u voor een Azure Analysis Services omgeving installeert, is het belang rijk dat u de stappen volgt die worden beschreven in de [on-premises gegevens gateway installeren en configureren voor Azure Analysis Services](analysis-services-gateway-install.md). Dit artikel is specifiek voor Azure Analysis Services. Het bevat aanvullende stappen die vereist zijn voor het instellen van een on-premises gegevens gateway resource in Azure en het verbinden van uw Azure Analysis Services-server met de resource.

## <a name="ports-and-communication-settings"></a>Poorten en communicatie-instellingen

De gateway maakt een uitgaande verbinding naar Azure Service Bus. De gateway communiceert via uitgaande poorten: TCP 443 (standaard), 5671, 5672, 9350 t/m 9354.  De gateway vereist geen inkomende poorten.

Mogelijk moet u IP-adressen voor uw gegevens regio in uw firewall toevoegen. U kunt de lijst met IP-adressen van Microsoft Azure-datacenters [hier](https://www.microsoft.com/download/details.aspx?id=56519) downloaden. Deze lijst wordt wekelijks bijgewerkt. De adressen in de lijst met IP-adressen van Azure-datacenters worden vermeld in de CIDR-notatie. Zie [Klasseloze route ring tussen domeinen](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)voor meer informatie.

Hieronder vindt u een volledig gekwalificeerde domein naam die wordt gebruikt door de gateway.

| Domeinnamen | Uitgaande poorten | Description |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP wordt gebruikt om het installatiebestand te downloaden. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listeners op Service Bus Relay via TCP (vereist 443 voor het ophalen van tokens voor toegangsbeheer) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Gebruikt voor het testen van de internetverbinding als de gateway onbereikbaar is voor de Power BI-service. |
| *.microsoftonline-p.com |443 |Wordt gebruikt voor verificatie, afhankelijk van de configuratie. |
| dc.services.visualstudio.com  |443 |Wordt door AppInsights gebruikt voor het verzamelen van telemetrie. |

### <a name="forcing-https-communication-with-azure-service-bus"></a><a name="force-https"></a>HTTPS-communicatie met Azure Service Bus afdwingen

U kunt afdwingen dat de gateway communiceert met Azure Service Bus door gebruik te maken van HTTPS in plaats van direct TCP; Dit kan echter de prestaties aanzienlijk verminderen. U kunt het *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* bestand wijzigen door de waarde te wijzigen van `AutoDetect` in `Https` . Dit bestand bevindt zich doorgaans in *C:\Program Files\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Volgende stappen 

De volgende artikelen zijn opgenomen in de on-premises gegevens gateway algemene inhoud die van toepassing is op alle services die door de gateway worden ondersteund:

* [Veelgestelde vragen over on-premises gegevensgateways](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Gebruik de on-premises gegevensgateway-app](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Beheer op tenantniveau](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Proxy-instellingen configureren](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Communicatie-instellingen aanpassen](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Logboek bestanden configureren](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Problemen oplossen](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Gatewayprestaties bewaken en optimaliseren](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
