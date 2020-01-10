---
title: Ondersteuning voor Azure IoT Hub TLS
description: Aanbevolen procedures voor het gebruik van beveiligde TLS-verbindingen voor apparaten en services die communiceren met IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.custom: Azure IoT Hub TLS
ms.openlocfilehash: 62fdfc4277b44a502206eb37466e6409521186a5
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75839528"
---
# <a name="tls-support-in-iot-hub"></a>TLS-ondersteuning in IoT Hub

IoT Hub gebruikt Transport Layer Security (TLS) om verbindingen van IoT-apparaten en-services te beveiligen. Er worden op dit moment drie versies van het TLS-protocol ondersteund, namelijk versie 1,0, 1,1 en 1,2.

TLS 1,0 en 1,1 worden beschouwd als verouderd en zijn [gepland voor afschaffing](./tls-1.2-everywhere.md). Het wordt daarom ten zeerste aanbevolen TLS 1,2 te gebruiken als de voor Keurs-TLS-versie bij het maken van verbinding met IoT Hub.


## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Verbindingen beperken tot TLS 1,2 in uw IoT Hub-resource

Voor extra beveiliging wordt u aangeraden om uw IoT-hubs zo te configureren dat _alleen_ client verbindingen met TLS-versie 1,2 worden toegestaan en het gebruik van [Aanbevolen code](#recommended-ciphers)ringen wordt afgedwongen.

Hiertoe dient u een nieuwe IoT Hub in te richten in een van de [ondersteunde regio's](#supported-regions) en de `minTlsVersion` eigenschap in te stellen op `1.2` in de resource specificatie van de IOT hub van de Azure Resource Manager-sjabloon:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Met de gemaakte IoT Hub resource die gebruikmaakt van deze configuratie, worden de apparaat-en service clients die verbinding proberen te maken met behulp van TLS-versies 1,0 en 1,1 geweigerd. Op dezelfde manier wordt de TLS-Handshake geweigerd als het bericht client HELLO geen van de [Aanbevolen code](#recommended-ciphers)ringen vermeldt.

Houd er rekening mee dat de eigenschap `minTlsVersion` alleen-lezen is en niet kan worden gewijzigd nadat de IoT Hub resource is gemaakt. Daarom is het essentieel dat u op de juiste wijze test en controleert of _al_ uw IOT-apparaten en-Services compatibel zijn met TLS 1,2 en de [Aanbevolen coderingen](#recommended-ciphers) vooraf.


### <a name="supported-regions"></a>Ondersteunde regio’s

IoT-hubs waarvoor het gebruik van TLS 1,2 vereist is, kunnen in de volgende regio's worden gemaakt:

* VS - oost
* VS - zuid-centraal
* VS - west 2

> [!NOTE]
> Bij failovers blijft de eigenschap `minTlsVersion` van uw IoT Hub effectief in de geo-paard Region-post-failover.



### <a name="recommended-ciphers"></a>Aanbevolen code ringen

IoT-hubs die zijn geconfigureerd om alleen TLS 1,2 te accepteren, afdwingen ook het gebruik van de volgende aanbevolen code ringen:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`


### <a name="use-tls-12-in-your-iot-edge-setup"></a>TLS 1,2 gebruiken in uw IoT Edge-installatie

IoT Edge-apparaten kunnen worden geconfigureerd voor het gebruik van TLS 1,2 bij de communicatie met IoT Hub. Gebruik voor dit doel de pagina met de [IOT Edge documentatie](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).