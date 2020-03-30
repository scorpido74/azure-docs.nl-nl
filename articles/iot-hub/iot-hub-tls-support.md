---
title: Azure IoT Hub TLS-ondersteuning
description: Aanbevolen procedures bij het gebruik van beveiligde TLS-verbindingen voor apparaten en services die communiceren met IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409493"
---
# <a name="tls-support-in-iot-hub"></a>TLS-ondersteuning in IoT Hub

IoT Hub maakt gebruik van Transport Layer Security (TLS) om verbindingen van IoT-apparaten en -services te beveiligen. Drie versies van het TLS-protocol worden momenteel ondersteund, namelijk versies 1.0, 1.1 en 1.2.

TLS 1.0 en 1.1 worden beschouwd als legacy en zijn gepland voor afschaffing. Zie [TLS 1.0 en 1.1 voor IoT Hub deprecating .](iot-hub-tls-deprecating-1-0-and-1-1.md) Het wordt ten zeerste aanbevolen om TLS 1.2 als voorkeursTLS-versie te gebruiken wanneer u verbinding maakt met IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Verbindingen beperken tot TLS 1.2 in uw IoT Hub-bron

Voor extra beveiliging wordt geadviseerd om uw IoT-hubs te configureren om *alleen* clientverbindingen toe te staan die TLS-versie 1.2 gebruiken en het gebruik van [aanbevolen cijfers](#recommended-ciphers)af te dwingen.

Informeer hiervoor een nieuwe IoT-hub in een van `minTlsVersion` de `1.2` [ondersteunde regio's](#supported-regions) en stel de eigenschap in op de IoT-hubbronspecificatie van uw Azure Resource Manager-sjabloon:

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

De gemaakte IoT Hub-bron met deze configuratie weigert apparaat- en serviceclients die proberen verbinding te maken met TLS-versies 1.0 en 1.1. Op dezelfde manier wordt de TLS-handshake geweigerd als het HELLO-bericht van de client geen van de [aanbevolen cijfers vermeldt.](#recommended-ciphers)

> [!NOTE]
> De `minTlsVersion` eigenschap is alleen-lezen en kan niet worden gewijzigd zodra uw IoT Hub-bron is gemaakt. Het is daarom essentieel dat u op de juiste manier test en valideert dat *al* uw IoT-apparaten en -services compatibel zijn met TLS 1.2 en de [aanbevolen cijfers](#recommended-ciphers) vooraf.

### <a name="supported-regions"></a>Ondersteunde regio’s

IoT-hubs waarvoor TLS 1.2 moet worden gebruikt, kunnen in de volgende regio's worden gemaakt:

* VS - oost
* VS - zuid-centraal
* VS - west 2
* VS (overheid) - Arizona
* VS (overheid) - Virginia

> [!NOTE]
> Bij failovers `minTlsVersion` blijft de eigenschap van uw IoT Hub effectief in de geo-gekoppelde regio na failover.

### <a name="recommended-ciphers"></a>Aanbevolen cijfers

IoT-hubs die zijn geconfigureerd om alleen TLS 1.2 te accepteren, zullen ook het gebruik van de volgende aanbevolen cijfers afdwingen:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>TLS 1.2 gebruiken in uw IoT Hub SDKs

Gebruik de onderstaande koppelingen om TLS 1.2 te configureren en toegestane cijfers in SDK's van de IoT Hub-client.

| Taal | Versies die TLS 1.2 ondersteunen | Documentatie |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 of nieuwer            | [Koppeling](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versie 2.0.0 of nieuwer             | [Koppeling](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versie 1.21.4 of nieuwer            | [Koppeling](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versie 1.19.0 of nieuwer            | [Koppeling](https://aka.ms/Tls_Java_SDK_IoT) |
| Node.js   | Versie 1.12.2 of nieuwer            | [Koppeling](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>TLS 1.2 gebruiken in uw IoT Edge-installatie

IoT Edge-apparaten kunnen worden geconfigureerd om TLS 1.2 te gebruiken bij het communiceren met IoT Hub. Gebruik hiervoor de [documentatiepagina van IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).