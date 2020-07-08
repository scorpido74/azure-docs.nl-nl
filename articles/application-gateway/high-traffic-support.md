---
title: Ondersteuning voor Application Gateway High Traffic volume
description: Dit artikel bevat richt lijnen voor het configureren van Azure-toepassing gateway ter ondersteuning van volume scenario's met een hoog netwerk verkeer.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: b96720ead2c7b7bc942efca32a8510f57c2dbcad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85250245"
---
# <a name="application-gateway-high-traffic-support"></a>Ondersteuning voor intensief verkeer in Application Gateway

>[!NOTE]
> In dit artikel worden enkele aanbevolen richt lijnen beschreven om u te helpen bij het instellen van uw Application Gateway voor het afhandelen van extra verkeer als gevolg van een groot verkeers volume dat kan optreden door de COVID-19-crisis.

U kunt Application Gateway met Web Application firewall (WAF) gebruiken voor een schaal bare en veilige manier om verkeer naar uw webtoepassingen te beheren.

De volgende suggesties helpen u bij het instellen van Application Gateway met WAF voor het verwerken van extra verkeer.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Gebruik v2 SKU over v1 voor de mogelijkheden voor automatisch schalen en prestaties
De v2-SKU biedt automatisch schalen om ervoor te zorgen dat uw Application Gateway kan worden geschaald naarmate het verkeer toeneemt. Het biedt ook andere aanzienlijke prestatie voordelen, zoals 5x betere TLS-offload-prestaties, snellere implementatie-en update tijden, zone redundantie en meer in vergelijking met v1. Zie onze [v2-documentatie](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)voor meer informatie. 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Stel het maximum aantal instanties in op het Maxi maal mogelijk (125)
 
Als u een SKU van Application Gateway v2 hebt en u het maximum aantal exemplaren instelt op de maximale mogelijke waarde van 125, kan de Application Gateway naar behoefte uitschalen. Zo kunt u de mogelijke toename van het verkeer naar uw toepassingen afhandelen. Er worden alleen kosten in rekening gebracht voor de capaciteits eenheden (CUs) die u gebruikt.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Stel het minimum aantal exemplaren in op basis van uw gemiddelde CU-gebruik

Als u een SKU van Application Gateway v2 hebt, neemt automatisch schalen zes tot zeven minuten in beslag. Met een hoger minimum aantal instanties kan de Application Gateway het verkeer beter verwerken wanneer de belasting toeneemt, omdat een piek in verkeer geen automatische schaal bewerking vereist.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Waarschuwen als een bepaalde metriek 75% van het gemiddelde CU-gebruik overschrijdt 
Raadpleeg de [documentatie over Application Gateway metrische gegevens](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) voor een gedetailleerde uitleg van de metrische gegevens en andere scenario's. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Voor beeld: een waarschuwing instellen voor 75% van het gemiddelde CU-gebruik

In dit voor beeld ziet u hoe u de Azure Portal kunt gebruiken om een waarschuwing in te stellen wanneer 75% van het gemiddelde van CU-gebruik is bereikt. 
1. Navigeer naar uw **Application Gateway**.
2. Selecteer in het linkerdeel venster **metrische gegevens** op het tabblad **bewaking** . 
3. Een metriek toevoegen voor **gemiddelde huidige reken eenheden**. 
![WAF metric instellen](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Als u uw minimum aantal instanties hebt ingesteld op uw gemiddelde CU-gebruik, kunt u een waarschuwing instellen wanneer 75% van de minimum instanties in gebruik zijn. Als uw gemiddelde gebruik bijvoorbeeld 10 CUs is, stelt u een waarschuwing in op 7,5. Hiermee wordt u gewaarschuwd als het gebruik toeneemt en u tijd hebt om te reageren. U kunt het minimum verhogen als u denkt dat dit verkeer wordt ondervangen om u te waarschuwen dat verkeer kan toenemen. 
![WAF-waarschuwing instellen](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> U kunt instellen dat de waarschuwing wordt uitgevoerd bij een lager of hoger CU-gebruiks percentage, afhankelijk van hoe gevoelig u wilt zijn voor mogelijke verkeers pieken.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>WAF met geofiltering en bot-beveiliging instellen om aanvallen te stoppen
Als u voor uw toepassing een extra beveiligingslaag wilt, gebruikt u de Application Gateway WAF_v2 SKU voor WAF-mogelijkheden. U kunt de v2-SKU zo configureren dat alleen toegang tot uw toepassingen vanuit een bepaald land/regio of landen/regio's is toegestaan. U stelt een aangepaste regel voor WAF in om verkeer expliciet toe te staan of te blok keren op basis van de geolocatie. Zie [geofilterende aangepaste regels](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) en [aangepaste regels configureren op Application Gateway WAF_v2 SKU via Power shell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)voor meer informatie.

Schakel bot-beveiliging in om bekende beschadigde bots te blok keren. Dit verlaagt de hoeveelheid verkeer die wordt ontvangen naar uw toepassing. Zie [bot Protection with set up instructies](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)(Engelstalig) voor meer informatie.

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Diagnostische gegevens inschakelen op Application Gateway en WAF

Met Diagnostische logboeken kunt u Firewall logboeken, prestatie logboeken en toegangs logboeken weer geven. U kunt deze logboeken in azure gebruiken om toepassings gateways te beheren en op te lossen. Zie de [Diagnostische documentatie](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)voor meer informatie. 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Een TLS-beleid instellen voor extra beveiliging
Zorg ervoor dat u de meest recente TLS-beleids versie ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)) gebruikt. Hiermee worden TLS 1,2 en sterkere cijfers afgedwongen. Zie [TLS-beleids versies en coderings suites configureren via Power shell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)voor meer informatie.
