---
title: Inzicht in het IP-adres van uw DPS-exemplaar (IoT Device Provisioning Service) | Microsoft Documenten
description: Meer informatie over het opvragen van uw DPS-adres (IoT Device Provisioning Service) en de bijbehorende eigenschappen. Het IP-adres van uw DPS-exemplaar kan veranderen tijdens bepaalde scenario's, zoals disaster recovery of regionale failover.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284926"
---
# <a name="iot-hub-dps-ip-addresses"></a>IOT Hub DPS IP-adressen

De IP-adresvoorvoegsels voor de openbare eindpunten van een IoT Hub Device Provisioning Service (DPS) worden periodiek gepubliceerd onder de _AzureIoTHub-servicetag_ [service tag](../virtual-network/service-tags-overview.md). U deze IP-adresvoorvoegsels gebruiken om de connectiviteit tussen een IoT DPS-exemplaar en apparaten of netwerkelementen te beheren om verschillende netwerkisolatiedoelen te implementeren:

| Doel | Methode |
|------|----------|
| Zorg ervoor dat uw apparaten en services alleen communiceren met IoT Hub DPS-eindpunten | Gebruik de _AzureIoTHub-servicetag_ om DpS-exemplaren van IoT Hub te ontdekken. Configureer ALLOW-regels voor de firewallinstelling van uw apparaten en services voor die IP-adresvoorvoegsels dienovereenkomstig. Configureer regels om verkeer naar andere bestemmings-IP-adressen te laten vallen waarmee u niet wilt dat apparaten of services communiceren. |
| Ervoor zorgen dat uw IoT Hub DPS-eindpunt alleen verbindingen ontvangt van uw apparaten en netwerkassets | Gebruik de [IP-filterfunctie](iot-dps-ip-filtering.md) van IoT DPS om filterregels te maken voor de API's van het apparaat en de DPS-service. Deze filterregels kunnen worden gebruikt om alleen verbindingen van uw apparaten en IP-adressen van netwerkactiva toe te staan (zie [sectie beperkingen).](#limitations-and-workarounds) | 




## <a name="best-practices"></a>Aanbevolen procedures

* Wanneer u ALLOW-regels toevoegt in de firewallconfiguratie van uw apparaten, u het beste specifieke poorten leveren [die worden gebruikt door toepasselijke protocollen.](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)

* De IP-adresvoorvoegsels van IoT DPS-exemplaren kunnen worden gewijzigd. Deze wijzigingen worden periodiek gepubliceerd via servicetags voordat ze van kracht worden. Het is daarom belangrijk dat u processen ontwikkelt om regelmatig de nieuwste servicetags op te halen en te gebruiken. Dit proces kan worden geautomatiseerd via de [service tags discovery API.](../virtual-network/service-tags-overview.md#service-tags-on-premises) De API voor het ontdekken van servicetags bevindt zich nog steeds in preview en produceert in sommige gevallen mogelijk niet de volledige lijst met tags en IP-adressen. Totdat de detectie-API algemeen beschikbaar is, u overwegen de [servicetags in downloadbare JSON-indeling te gebruiken.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Gebruik de *AzureIoTHub.[ regionaam]* tag om IP-voorvoegsels te identificeren die worden gebruikt door DPS-eindpunten in een specifieke regio. Als u rekening wilt houden met het herstel van datacenterrampen of [regionale failover,](../iot-hub/iot-hub-ha-dr.md)is ook de verbinding met IP-voorvoegsels van het geo-pair-gebied van uw DPS-instantie ingeschakeld.

* Het instellen van firewallregels voor een DPS-exemplaar kan de connectiviteit blokkeren die nodig is om Azure CLI- en PowerShell-opdrachten tegen te voeren. Om deze verbindingsproblemen te voorkomen, u ALLOW-regels toevoegen voor de IP-adresvoorvoegsels van uw clients om CLI- of PowerShell-clients opnieuw in te schakelen om met uw DPS-exemplaar te communiceren.  


## <a name="limitations-and-workarounds"></a>Beperkingen en tijdelijke oplossingen

* De DPS IP-filterfunctie heeft een limiet van 100 regels. Deze limiet kan worden verhoogd via aanvragen via Azure Customer Support. 

* Uw geconfigureerde [IP-filterregels](iot-dps-ip-filtering.md) worden alleen toegepast op uw DPS-eindpunten en niet op de gekoppelde IoT Hub-eindpunten. IP-filtering voor gekoppelde IoT-hubs moet afzonderlijk worden geconfigureerd. Zie [IE-filterregels voor IoT Hub IP.](../iot-hub/iot-hub-ip-filtering.md)

## <a name="support-for-ipv6"></a>Ondersteuning voor IPv6 

IPv6 wordt momenteel niet ondersteund op IoT Hub of DPS.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over IP-adresconfiguraties met DPS:

* [IP-filtering configureren](iot-dps-ip-filtering.md)
