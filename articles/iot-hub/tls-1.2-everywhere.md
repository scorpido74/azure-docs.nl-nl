---
title: Afschaffing TLS 1,0 en 1,1 in IoT Hub en Device Provisioning Service (DPS) | Microsoft Docs
description: Richt lijnen met betrekking tot de afschaffing van TLS 1,0 en 1,1 en ondersteunde code ringen in IoT Hub en DPS.
author: rezas
ms.author: reza
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f1ee3156e5639ae47d5bb323cf992586580668f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485908"
---
# <a name="tls-10-and-11-deprecation-in-iot-hub-and-device-provisioning-service"></a>De afschrijving van TLS 1,0 en 1,1 in IoT Hub en de Device Provisioning Service

IoT Hub en de DPS (Device Provisioning Service) worden verplaatst naar Transport Layer Security (TLS) 1,2 als versleutelings methode voor IoT-apparaten en-services om de beste versleuteling te bieden. Daarom zal oudere ondersteuning voor TLS 1,0 en TLS 1,1 en verschillende niet-aanbevolen oudere code ringen worden afgeschaft in **1 juli 2020**.


## <a name="impact"></a>Impact
Op basis van de specifieke omstandigheden en configuraties van klanten zijn de afschaffing van TLS 1,0 en 1,1 en niet-aanbevolen oudere code ringen een invloed op de wijziging van uw IoT-apparaten en-services die communiceren met IoT Hub of DPS. In sommige gevallen kunnen apparaten en services die niet compatibel zijn met deze wijzigingen, geen verbinding maken met de IoT Hub of DPS na de genoemde afsluitings datum.


## <a name="supported-ciphers"></a>Ondersteunde versleuteling

Alleen de volgende versleuteling is toegestaan tijdens TLS-handshake:

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Feedback van klanten

Hoewel het afdwingen van TLS 1.2 een keuze is voor de beste coderingsmethode in de branche en deze wordt ingevoerd zoals gepland, willen we graag de mening van klanten horen over hun specifieke implementaties en problemen bij het in gebruik nemen van TLS 1.2. Voor dit doel kunt u uw opmerkingen naar [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)verzenden.