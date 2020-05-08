---
title: TLS-ondersteuning voor Azure IoT Device Provisioning Service (DPS)
description: Aanbevolen procedures voor het gebruik van beveiligde TLS-verbindingen voor apparaten en services die communiceren met de IoT Device Provisioning Service (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984848"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>TLS-ondersteuning in azure IoT Hub Device Provisioning Service (DPS)

DPS gebruikt Transport Layer Security (TLS) voor het beveiligen van verbindingen van IoT-apparaten. Er worden op dit moment drie versies van het TLS-protocol ondersteund, namelijk versie 1,0, 1,1 en 1,2.

TLS 1,0 en 1,1 worden beschouwd als verouderd en zijn gepland voor afschaffing. Zie [TLS 1,0 en 1,1 voor IOT hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)afzien voor meer informatie. Het wordt ten zeerste aangeraden TLS 1,2 te gebruiken als de voor Keurs-TLS-versie wanneer u verbinding maakt met DPS.

## <a name="restrict-connections-to-tls-12"></a>Verbindingen beperken tot TLS 1,2

Voor extra beveiliging wordt u aangeraden uw DPS-exemplaren zo te configureren dat *alleen* client verbindingen van het apparaat met TLS-versie 1,2 worden toegestaan en het gebruik van [Aanbevolen code](#recommended-ciphers)ringen wordt afgedwongen.

U doet dit door een nieuwe DPS-resource in te richten in een van de [ondersteunde regio's](#supported-regions) en `minTlsVersion` de `1.2` eigenschap in te stellen op de resource specificatie DPS van uw Azure Resource Manager-sjabloon. Met de volgende voorbeeld sjabloon JSON geeft `minTlsVersion` u de eigenschap op voor een nieuw DPS-exemplaar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

U kunt de sjabloon implementeren met de volgende Azure CLI-opdracht. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Zie [DPS met een Azure Resource Manager-sjabloon instellen](quick-setup-auto-provision-rm.md)voor meer informatie over het maken van DPS-resources met Resource Manager-sjablonen.

De DPS-resource die is gemaakt met deze configuratie, weigert apparaten die verbinding proberen te maken met behulp van TLS-versie 1,0 en 1,1. Op dezelfde manier wordt de TLS-Handshake geweigerd als het HELLO-bericht van de apparaatclient geen van de [Aanbevolen code](#recommended-ciphers)ringen vermeldt.

> [!NOTE]
> De `minTlsVersion` eigenschap is alleen-lezen en kan niet worden gewijzigd nadat uw DPS-resource is gemaakt. Daarom is het essentieel dat u op de juiste wijze test en controleert of *al* uw IOT-apparaten compatibel zijn met TLS 1,2 en de [Aanbevolen coderingen](#recommended-ciphers) vooraf.

## <a name="supported-regions"></a>Ondersteunde regio’s

IoT DPS-instanties waarvoor het gebruik van TLS 1,2 is vereist, kunnen in de volgende regio's worden gemaakt:

* VS (overheid) - Arizona
* VS (overheid) - Virginia

> [!NOTE]
> Bij failovers blijft het `minTlsVersion` eigendom van uw DPS geldig in de geo-paard Region na failover.

## <a name="recommended-ciphers"></a>Aanbevolen code ringen

DPS-instanties die zijn geconfigureerd om alleen TLS 1,2 te accepteren, afdwingen ook het gebruik van de volgende aanbevolen code ringen:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>TLS 1,2 gebruiken in de IoT Sdk's

Gebruik de onderstaande koppelingen om TLS 1,2 en toegestane code ringen te configureren in de Azure IoT client-Sdk's.

| Taal | Versies die TLS 1,2 ondersteunen | Documentatie |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 of hoger            | [Koppeling](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versie 2.0.0 of nieuwer             | [Koppeling](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versie 1.21.4 of nieuwer            | [Koppeling](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versie 1.19.0 of nieuwer            | [Koppeling](https://aka.ms/Tls_Java_SDK_IoT) |
| Node.js   | Versie 1.12.2 of nieuwer            | [Koppeling](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-with-iot-edge"></a>TLS 1,2 gebruiken met IoT Edge

IoT Edge-apparaten kunnen worden geconfigureerd voor het gebruik van TLS 1,2 bij de communicatie met IoT Hub en DPS. Gebruik voor dit doel de pagina met de [IOT Edge documentatie](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).