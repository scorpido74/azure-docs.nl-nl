---
title: Ondersteuning voor Azure IoT Hub TLS
description: Aanbevolen procedures voor het gebruik van beveiligde TLS-verbindingen voor apparaten en services die communiceren met IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: ce8ef987adc0cec4fcd8acef4cc075d50c92d62a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722887"
---
# <a name="tls-support-in-iot-hub"></a>TLS-ondersteuning in IoT Hub

IoT Hub gebruikt Transport Layer Security (TLS) om verbindingen van IoT-apparaten en-services te beveiligen. Er worden op dit moment drie versies van het TLS-protocol ondersteund, namelijk versie 1,0, 1,1 en 1,2.

TLS 1,0 en 1,1 worden beschouwd als verouderd en zijn gepland voor afschaffing. Zie [TLS 1,0 en 1,1 voor IOT hub](iot-hub-tls-deprecating-1-0-and-1-1.md)afzien voor meer informatie. Het wordt ten zeerste aangeraden TLS 1,2 te gebruiken als de voor Keurs-TLS-versie wanneer u verbinding maakt met IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Verbindingen beperken tot TLS 1,2 in uw IoT Hub-resource

Voor extra beveiliging wordt u aangeraden om uw IoT-hubs zo te configureren dat *alleen* client verbindingen met TLS-versie 1,2 worden toegestaan en het gebruik van [Aanbevolen code](#recommended-ciphers)ringen wordt afgedwongen.

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

> [!NOTE]
> De eigenschap `minTlsVersion` is alleen-lezen en kan niet worden gewijzigd nadat de IoT Hub resource is gemaakt. Daarom is het essentieel dat u op de juiste wijze test en controleert of *al* uw IOT-apparaten en-Services compatibel zijn met TLS 1,2 en de [Aanbevolen coderingen](#recommended-ciphers) vooraf.

### <a name="supported-regions"></a>Ondersteunde regio’s

IoT-hubs waarvoor het gebruik van TLS 1,2 vereist is, kunnen in de volgende regio's worden gemaakt:

* VS - oost
* US - zuid-centraal
* US - west 2

> [!NOTE]
> Bij failovers blijft de eigenschap `minTlsVersion` van uw IoT Hub effectief in de geo-paard Region-post-failover.

### <a name="recommended-ciphers"></a>Aanbevolen code ringen

IoT-hubs die zijn geconfigureerd om alleen TLS 1,2 te accepteren, afdwingen ook het gebruik van de volgende aanbevolen code ringen:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>TLS 1,2 gebruiken in uw IoT Hub Sdk's

Gebruik de onderstaande koppelingen voor het configureren van TLS 1,2 en toegestane code ringen in IoT Hub client-Sdk's.

| Taal | TLS 1,2 ondersteund | Documentatie |
|----------|-------------------|---------------|
| C        | Ja               | [Koppeling](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Ja               | [Koppeling](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Ja               | [Koppeling](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Ja               | [Koppeling](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Ja               | [Koppeling](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>TLS 1,2 gebruiken in uw IoT Edge-installatie

IoT Edge-apparaten kunnen worden geconfigureerd voor het gebruik van TLS 1,2 bij de communicatie met IoT Hub. Gebruik voor dit doel de pagina met de [IOT Edge documentatie](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).