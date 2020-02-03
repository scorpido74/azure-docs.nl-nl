---
title: TLS 1,0 en 1,1 in IoT Hub en de Device Provisioning Service (DPS) afzien | Microsoft Docs
description: Richt lijnen met betrekking tot de afschaffing van TLS 1,0 en 1,1 en ondersteunde code ringen in IoT Hub en DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 95733f579740f2928cda917eec0023bf00d53076
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722783"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Afschaffing van TLS 1,0 en 1,1 in IoT Hub en Device Provisioning Service

IoT Hub en de DPS (Device Provisioning Service) worden verplaatst naar Transport Layer Security (TLS) 1,2 als versleutelings methode voor IoT-apparaten en-services om de beste versleuteling te bieden. Daarom zal oudere ondersteuning voor TLS 1,0 en TLS 1,1 en verschillende niet-aanbevolen oudere code ringen worden afgeschaft in **1 juli 2020**.


## <a name="impact"></a>Impact
Op basis van de specifieke omstandigheden en configuraties van klanten zijn de afschaffing van TLS 1,0 en 1,1 en niet-aanbevolen oudere code ringen een invloed op de wijziging van uw IoT-apparaten en-services die communiceren met IoT Hub of DPS. In sommige gevallen kunnen apparaten en services die niet compatibel zijn met deze wijzigingen, geen verbinding maken met de IoT Hub of DPS na de genoemde afsluitings datum.


## <a name="supported-ciphers"></a>Ondersteunde versleuteling

Alleen de volgende code ringen zijn toegestaan tijdens TLS-handshake:

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Feedback van klanten

Hoewel de TLS 1,2-afdwinging een in de hele branche geschikte coderings keuze is en wordt ingeschakeld zoals gepland, zullen we graag horen van klanten over hun specifieke implementaties en problemen met het vaststellen van TLS 1,2. Voor dit doel kunt u uw opmerkingen naar [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)verzenden.