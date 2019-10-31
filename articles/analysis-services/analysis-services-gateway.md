---
title: On-premises gegevens gateway voor Azure Analysis Services | Microsoft Docs
description: Een on-premises gateway is nodig als uw Analysis Services-server in azure verbinding maakt met on-premises gegevens bronnen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35ffc7f3c97ca7ab14f94c3607560ffb6ea0b399
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146848"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Verbinding maken met on-premises gegevens bronnen met on-premises gegevens gateway

De on-premises gegevens gateway biedt veilige gegevens overdracht tussen on-premises gegevens bronnen en uw Azure Analysis Services servers in de Cloud. Naast het werken met meerdere Azure Analysis Services servers in dezelfde regio, werkt de meest recente versie van de gateway ook met Azure Logic Apps, Power BI, Power apps en Microsoft Flow. U kunt meerdere services in hetzelfde abonnement en dezelfde regio met één gateway koppelen. Hoewel de gateway die u installeert hetzelfde is voor al deze services, Azure Analysis Services en Logic Apps enkele extra stappen hebben.

Voor Azure Analysis Services is het voorbereiden van de installatie met de gateway de eerste keer een proces met vier delen:

- **Setup downloaden en uitvoeren** : met deze stap installeert u een gateway service op een computer in uw organisatie. U kunt zich ook aanmelden bij Azure met behulp van een account in de Azure AD [van uw Tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) . Accounts van Azure B2B (gast) worden niet ondersteund.

- **Uw gateway registreren** : in deze stap geeft u een naam en herstel sleutel op voor uw gateway en selecteert u een regio, waarbij u uw gateway registreert bij de gateway-Cloud service. Uw gateway bron kan in elke regio worden geregistreerd, maar het wordt aangeraden dat deze zich in dezelfde regio bevindt als uw Analysis Services-servers. 

- **Een gateway bron maken in azure** : in deze stap maakt u een gateway bron in uw Azure-abonnement.

- **Uw servers verbinden met uw gateway resource** : Zodra u een gateway bron in uw abonnement hebt, kunt u aan de slag gaan met het verbinden van uw servers. U kunt meerdere servers en andere bronnen verbinden, mits deze zich in hetzelfde abonnement en dezelfde regio bevinden.

## <a name="how-it-works"> </a>Hoe werkt het?
De gateway die u op een computer in uw organisatie installeert, wordt uitgevoerd als een Windows-service, **on-premises gegevens gateway**. Deze lokale service is geregistreerd bij de gateway-Cloud service via Azure Service Bus. Vervolgens maakt u een on-premises gegevens gateway resource voor uw Azure-abonnement. Uw Azure Analysis Services-servers worden vervolgens verbonden met uw Azure gateway-resource. Wanneer modellen op uw server verbinding moeten maken met uw on-premises gegevens bronnen voor query's of verwerking, passeren een query en gegevens stroom de gateway resource, Azure Service Bus, de lokale on-premises gegevens Gateway Service en uw gegevens bronnen. 

![Het werkt als volgt](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Query's en gegevens stroom:

1. Er wordt een query gemaakt door de Cloud service met de versleutelde referenties voor de on-premises gegevens bron. Het wordt vervolgens verzonden naar een wachtrij die de gateway kan verwerken.
2. De gateway-Cloud service analyseert de query en duwt de aanvraag naar het [Azure service bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. De on-premises gegevens gateway pollt de Azure Service Bus voor in behandeling zijnde aanvragen.
4. De gateway haalt de query op, ontsleutelt de referenties en maakt verbinding met de gegevens bronnen met deze referenties.
5. De Gateway verzendt de query naar de gegevens bron voor uitvoering.
6. De resultaten worden verzonden vanuit de gegevens bron, terug naar de gateway en vervolgens naar de Cloud service en uw server.

## <a name="installing"></a>Installeren

Wanneer u voor een Azure Analysis Services omgeving installeert, is het belang rijk dat u de stappen volgt die worden beschreven in de [on-premises gegevens gateway installeren en configureren voor Azure Analysis Services](analysis-services-gateway-install.md). Dit artikel is specifiek voor Azure Analysis Services. Het bevat aanvullende stappen die vereist zijn voor het instellen van een on-premises gegevens gateway resource in Azure en het verbinden van uw Azure Analysis Services-server met de resource.

## <a name="ports-and-communication-settings"></a>Poorten en communicatie-instellingen

De gateway maakt een uitgaande verbinding met Azure Service Bus. Deze communiceert via uitgaande poorten: TCP 443 (standaard), 5671, 5672, 9350 tot en met 9354.  De gateway vereist geen inkomende poorten.

Mogelijk moet u IP-adressen voor uw gegevens regio in uw firewall toevoegen. U kunt de [IP-lijst van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)downloaden. Deze lijst wordt wekelijks bijgewerkt. De IP-adressen die worden weer gegeven in de IP-lijst van het Azure-Data Center zijn in CIDR-notatie. Zie [Klasseloze route ring tussen domeinen](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)voor meer informatie.

Hieronder vindt u een volledig gekwalificeerde domein naam die wordt gebruikt door de gateway.

| Domein namen | Uitgaande poorten | Beschrijving |
| --- | --- | --- |
| *. powerbi.com |80 |HTTP gebruikt voor het downloaden van het installatie programma. |
| *. powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queueing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listeners op Service Bus Relay via TCP (vereist 443 voor de aanschaf van Access Control-token) |
| *. frontend.clouddatahub.net |443 |HTTPS |
| *. core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |443 |Wordt gebruikt om de Internet verbinding te testen als de gateway onbereikbaar is voor de Power BI-service. |
| *. microsoftonline-p.com |443 |Wordt gebruikt voor verificatie, afhankelijk van de configuratie. |
| dc.services.visualstudio.com  |443 |Wordt door AppInsights gebruikt voor het verzamelen van telemetrie. |

### <a name="force-https"></a>HTTPS-communicatie met Azure Service Bus forceren

U kunt afdwingen dat de gateway communiceert met Azure Service Bus door gebruik te maken van HTTPS in plaats van direct TCP; Dit kan echter de prestaties aanzienlijk verminderen. U kunt het bestand *Microsoft. PowerBI. DataMovement. pipeline. GatewayCore. dll. config* wijzigen door de waarde te wijzigen van `AutoDetect` in `Https`. Dit bestand bevindt zich doorgaans in *C:\Program Files\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Volgende stappen 

De volgende artikelen zijn opgenomen in de on-premises gegevens gateway algemene inhoud die van toepassing is op alle services die door de gateway worden ondersteund:

* [FAQ voor on-premises gegevensgateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [De on-premises gegevens gateway-app gebruiken](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Beheer op Tenant niveau](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Proxy-instellingen configureren](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Communicatie-instellingen aanpassen](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Logboek bestanden configureren](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Problemen oplossen](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Gateway prestaties bewaken en optimaliseren](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
