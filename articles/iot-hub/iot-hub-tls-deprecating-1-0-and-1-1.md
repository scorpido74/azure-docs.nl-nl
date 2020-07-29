---
title: TLS 1,0 en 1,1 in IoT Hub afafschaffing | Microsoft Docs
description: Richt lijnen met betrekking tot de afschaffing van TLS 1,0 en 1,1 en ondersteunde code ringen in IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83849515"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Afschaffing van TLS 1,0 en 1,1 in IoT Hub

IoT Hub wordt verplaatst naar Transport Layer Security (TLS) 1,2 als versleutelings methode voor IoT-apparaten en-services om de beste versleuteling te bieden. 

## <a name="timeline"></a>Tijdlijn

IoT Hub wordt TLS 1.0/1.1 blijven ondersteunen tot verdere kennisgeving. We raden echter aan alle klanten zo snel mogelijk naar TLS 1,2 te migreren.

## <a name="deprecating-tls-11-ciphers"></a>TLS 1,1-code ringen afafschaffing

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>TLS 1,0-code ringen afafschaffing

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-ciphers"></a>TLS 1,2-code ringen

Zie [IOT hub TLS 1,2 Aanbevolen code](iot-hub-tls-support.md#recommended-ciphers)ringen.
 
## <a name="customer-feedback"></a>Feedback van klanten

Hoewel de TLS 1,2-afdwinging een in de hele branche geschikte coderings keuze is en wordt ingeschakeld zoals gepland, zullen we graag horen van klanten over hun specifieke implementaties en problemen met het vaststellen van TLS 1,2. U kunt uw opmerkingen voor dit doel verzenden naar [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) .
