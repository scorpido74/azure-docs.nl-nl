---
title: Inzicht in het IP-adres van uw IoT-hub | Microsoft Documenten
description: Meer informatie over het opvragen van uw IP-adres van uw IoT-hub en de eigenschappen ervan. Het IP-adres van uw IoT-hub kan veranderen tijdens bepaalde scenario's, zoals disaster recovery of regionale failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367564"
---
# <a name="iot-hub-ip-addresses"></a>IP-adressen van IoT Hub

De IP-adresvoorvoegsels van openbare eindpunten van IoT Hub worden periodiek gepubliceerd onder de _AzureIoTHub-servicetag_ [service tag](../virtual-network/service-tags-overview.md).

> [!NOTE]
> Voor apparaten die worden geïmplementeerd binnen on-premises netwerken, ondersteunt Azure IoT Hub VNET-connectiviteitsintegratie met privéeindpunten. Zie [IoT Hub-ondersteuning voor VNET's](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints) voor meer informatie.


U deze IP-adresvoorvoegsels gebruiken om de connectiviteit tussen IoT Hub en uw apparaten of netwerkassets te beheren om verschillende netwerkisolatiedoelen te implementeren:

| Doel | Toepasselijke scenario's | Methode |
|------|-----------|----------|
| Zorg ervoor dat uw apparaten en services alleen communiceren met IoT Hub-eindpunten | [Device-to-cloud](./iot-hub-devguide-messaging.md)en [cloud-to-device](./iot-hub-devguide-messages-c2d.md) messaging, [directe methoden,](./iot-hub-devguide-direct-methods.md) [apparaat- en moduletweelingen](./iot-hub-devguide-device-twins.md) en [apparaatstreams](./iot-hub-device-streams-overview.md) | Gebruik _AzureIoTHub-_ en _EventHub-servicetags_ om IoT Hub en IP-adresvoorvoegsels van Event Hub te ontdekken en ALLOW-regels te configureren voor de firewall-instelling van uw apparaten en services voor die ip-adresvoorvoegsels; verkeer naar andere ip-adressen van de bestemming te laten vallen waarmee u niet wilt dat de apparaten of services communiceren. |
| Ervoor zorgen dat het eindpunt van uw IoT Hub-apparaat alleen verbindingen ontvangt van uw apparaten en netwerkassets | [Device-to-cloud](./iot-hub-devguide-messaging.md)en [cloud-to-device](./iot-hub-devguide-messages-c2d.md) messaging, [directe methoden,](./iot-hub-devguide-direct-methods.md) [apparaat- en moduletweelingen](./iot-hub-devguide-device-twins.md) en [apparaatstreams](./iot-hub-device-streams-overview.md) | Gebruik de [IP-filterfunctie](iot-hub-ip-filtering.md) van IoT Hub om verbindingen vanaf uw apparaten en IP-adressen van netwerkactiva toe te staan (zie [sectie beperkingen).](#limitations-and-workarounds) | 
| Zorg ervoor dat de aangepaste eindpuntbronnen van uw routes (opslagaccounts, servicebus en gebeurtenishubs) alleen bereikbaar zijn vanuit uw netwerkassets | [Berichtroutering](./iot-hub-devguide-messages-d2c.md) | Volg de richtlijnen van uw resource voor beperking van connectiviteit (bijvoorbeeld via [firewallregels,](../storage/common/storage-network-security.md) [privékoppelingen](../private-link/private-endpoint-overview.md)of [serviceeindpunten);](../virtual-network/virtual-network-service-endpoints-overview.md) _Gebruik AzureIoTHub-servicetags_ om IP-adresvoorvoegsels van IoT Hub te ontdekken en ALLOW-regels toe te voegen voor die IP-voorvoegsels in de firewallconfiguratie van uw bron (zie [sectie beperkingen).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Aanbevolen procedures

* Wanneer u ALLOW-regels toevoegt in de firewallconfiguratie van uw apparaten, u het beste specifieke poorten leveren [die worden gebruikt door toepasselijke protocollen.](./iot-hub-devguide-protocols.md#port-numbers)

* De IP-adresvoorvoegsels van de IoT-hub kunnen worden gewijzigd. Deze wijzigingen worden periodiek gepubliceerd via servicetags voordat ze van kracht worden. Het is daarom belangrijk dat u processen ontwikkelt om regelmatig de nieuwste servicetags op te halen en te gebruiken. Dit proces kan worden geautomatiseerd via de [service tags discovery API.](../virtual-network/service-tags-overview.md#service-tags-on-premises) Houd er rekening mee dat de API voor het ontdekken van servicetags nog steeds in een voorbeeld is en in sommige gevallen mogelijk niet de volledige lijst met tags en IP-adressen produceert. Totdat de detectie-API algemeen beschikbaar is, u overwegen de [servicetags in downloadbare JSON-indeling te gebruiken.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Gebruik de *AzureIoTHub.[ regionaam]* tag om IP-voorvoegsels te identificeren die worden gebruikt door IoT-hubeindpunten in een specifieke regio. Als u rekening wilt houden met het herstel van datacenterrampen of [regionale failover,](iot-hub-ha-dr.md) is ook de verbinding met IP-voorvoegsels van het geo-pair-gebied van uw IoT Hub ingeschakeld.

* Het instellen van firewallregels in IoT Hub kan de connectiviteit blokkeren die nodig is om Azure CLI- en PowerShell-opdrachten tegen uw IoT-hub uit te voeren. Om dit te voorkomen, u ALLOW-regels toevoegen voor de IP-adresvoorvoegsels van uw clients om CLI- of PowerShell-clients opnieuw in te schakelen om met uw IoT Hub te communiceren.  


## <a name="limitations-and-workarounds"></a>Beperkingen en tijdelijke oplossingen

* IoT Hub IP-filterfunctie heeft een limiet van 10 regels. Deze limiet kan worden verhoogd via aanvragen via Azure Customer Support. 

* Uw geconfigureerde [IP-filterregels](iot-hub-ip-filtering.md) worden alleen toegepast op ip-eindpunten van uw IoT Hub en niet op het ingebouwde Event Hub-eindpunt van uw IoT-hub. Als u ook ip-filtering wilt toepassen op de gebeurtenishub waar uw berichten worden opgeslagen, u dit doen door uw eigen Event Hub-bron mee te nemen, waar u de gewenste IP-filterregels rechtstreeks configureren. Hiervoor moet u uw eigen Event Hub-bron inrichten en [berichtroutering](./iot-hub-devguide-messages-d2c.md) instellen om uw berichten naar die bron te verzenden in plaats van de ingebouwde Event Hub van uw IoT Hub. Ten slotte, zoals besproken in de bovenstaande tabel, moet u ook connectiviteit toestaan van de IP-adresvoorvoegsels van IoT Hub naar uw ingerichte Event Hub-bron om de functionaliteit voor het routeren van berichten mogelijk te maken.

* Wanneer u naar een opslagaccount routert, is het alleen mogelijk om verkeer toe te staan via de IP-adresvoorvoegsels van IoT Hub wanneer het opslagaccount zich in een andere regio bevindt als uw IoT Hub.

## <a name="support-for-ipv6"></a>Ondersteuning voor IPv6 

IPv6 wordt momenteel niet ondersteund op IoT Hub.
