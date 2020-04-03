---
title: Ondersteuning voor een groot verkeersvolume van Application Gateway
description: In dit artikel vindt u richtlijnen voor het configureren van Azure Application Gateway ter ondersteuning van scenario's met een hoog netwerkverkeer.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617205"
---
# <a name="application-gateway-high-traffic-support"></a>Ondersteuning voor intensief verkeer in Application Gateway

>[!NOTE]
> In dit artikel worden enkele voorgestelde richtlijnen beschreven om u te helpen bij het instellen van uw Application Gateway om extra verkeer te verwerken vanwege het hoge verkeersvolume dat kan optreden als gevolg van de COVID-19-crisis.

U Application Gateway with Web Application Firewall (WAF) gebruiken voor een schaalbare en veilige manier om verkeer naar uw webtoepassingen te beheren.

Met de volgende suggesties u Application Gateway instellen met WAF om extra verkeer te verwerken.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Gebruik de v2 SKU over v1 voor de mogelijkheden en prestatievoordelen voor automatisch schalen
De v2 SKU biedt automatisch schalen om ervoor te zorgen dat uw Application Gateway kan worden opgeschaald naarmate het verkeer toeneemt. Het biedt ook andere belangrijke prestatievoordelen, zoals 5x betere TLS offload prestaties, snellere implementatie en update tijden, zone redundantie, en meer in vergelijking met v1. Zie voor meer informatie onze [v2-documentatie.](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Maximaal aantal instance's instellen op het maximaal mogelijke (125)
 
Als u een Application Gateway v2 SKU hebt, kan de Toepassingsgateway naar behoefte worden uitgeschaald op de maximaal mogelijke waarde van 125. Dit maakt het mogelijk om de mogelijke toename van het verkeer naar uw toepassingen te behandelen. Er worden alleen kosten in rekening gebracht voor de capaciteitseenheden (CPU's) die u gebruikt.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Stel het minimumaantal gevallen in op basis van uw gemiddelde CU-gebruik

Ervan uitgaande dat u een Application Gateway v2 SKU hebt, duurt het zes tot zeven minuten om automatisch schalen uit te schalen. Met een hoger minimum aantal instance's kan de Application Gateway uw verkeer beter verwerken wanneer de belasting wordt verhoogd, omdat een piek in het verkeer geen automatische schalingbewerking vereist.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Waarschuwing als een bepaalde statistiek 75% van het gemiddelde CU-gebruik overschrijdt 
Zie de [documentatie over application gateway metrics](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) voor een gedetailleerde uitleg van onze statistieken en andere walkthroughs. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Voorbeeld: Een waarschuwing instellen op 75% van het gemiddelde CU-gebruik

In dit voorbeeld ziet u hoe u de Azure-portal gebruiken om een waarschuwing in te stellen wanneer 75% van het gemiddelde CU-gebruik is bereikt. 
1. Navigeer naar de **toepassingsgateway.**
2. Selecteer in het linkerdeelvenster **Statistieken** onder het tabblad **Controle.** 
3. Voeg een statistiek toe voor **gemiddelde huidige rekeneenheden**. 
![WAF-statistiek instellen](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Als u het minimumaantal exemplaren hebt ingesteld als uw gemiddelde CU-gebruik, gaat u verder en stelt u een waarschuwing in wanneer 75% van uw minimale exemplaren in gebruik zijn. Als uw gemiddelde gebruik bijvoorbeeld 10 CPU's is, stelt u een waarschuwing in op 7,5 CPU's. Dit waarschuwt u als het gebruik toeneemt en geeft u de tijd om te reageren. U het minimum verhogen als u denkt dat dit verkeer zal worden volgehouden om u te waarschuwen dat het verkeer kan toenemen. 
![WAF-waarschuwing instellen](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> U instellen dat de waarschuwing optreedt bij een lager of hoger CU-gebruikspercentage, afhankelijk van hoe gevoelig u wilt zijn voor potentiële verkeerspieken.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>WAF instellen met geofiltering en botbescherming om aanvallen te stoppen
Als u een extra beveiligingslaag voor uw toepassing wilt, gebruikt u de Application Gateway WAF_v2 SKU voor WAF-mogelijkheden. U de v2 SKU zo configureren dat deze alleen toegang geeft tot uw toepassingen vanuit een bepaald land of land. U stelt een aangepaste WAF-regel in om verkeer expliciet toe te staan of te blokkeren op basis van de geolocatie. Zie voor meer informatie [aangepaste regels voor geofiltering](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) en het configureren van [aangepaste regels voor Application Gateway WAF_v2 SKU via PowerShell.](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)

Schakel botbescherming in om bekende slechte bots te blokkeren. Dit moet de hoeveelheid verkeer naar uw toepassing te verminderen. Zie [botbeveiliging met instelinstructies](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)voor meer informatie .

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Diagnostische gegevens inschakelen op Application Gateway en WAF

Met diagnostische logboeken u firewalllogboeken, prestatielogboeken en toegangslogboeken weergeven. U deze logboeken in Azure gebruiken om toepassingsgateways te beheren en op te lossen. Zie voor meer informatie onze [diagnostische documentatie.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging) 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Een TLS-beleid instellen voor extra beveiliging
Zorg ervoor dat u de nieuwste TLS-beleidsversie gebruikt[(AppGwSslPolicy20170401S).](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s) Dit dwingt TLS 1.2 en sterkere cijfers. Zie VOOR meer informatie [tls-beleidsversies en ciphersuites configureren via PowerShell.](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)
