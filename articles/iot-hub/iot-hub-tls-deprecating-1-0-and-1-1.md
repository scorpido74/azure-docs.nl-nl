---
title: TLS 1,0 en 1,1 in IoT Hub afafschaffing | Microsoft Docs
description: Richt lijnen met betrekking tot de afschaffing van TLS 1,0 en 1,1 en ondersteunde code ringen in IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381299"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Afschaffing van TLS 1,0 en 1,1 in IoT Hub

IoT Hub wordt verplaatst naar Transport Layer Security (TLS) 1,2 als versleutelings methode voor IoT-apparaten en-services om de beste versleuteling te bieden. 

## <a name="timeline"></a>Tijdlijn

IoT Hub wordt TLS 1.0/1.1 blijven ondersteunen tot verdere kennisgeving. We raden echter aan alle klanten zo snel mogelijk naar TLS 1,2 te migreren.

## <a name="supported-ciphers"></a>Ondersteunde versleuteling

De tijd lijn voor de beschik baarheid van verschillende code ringen die in TLS-Handshake worden gebruikt, is als volgt:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (momenteel ondersteund)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (wordt ondersteund in de tweede helft van 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (wordt ondersteund in de tweede helft van 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (wordt ondersteund in de tweede helft van 2020)

## <a name="customer-feedback"></a>Feedback van klanten

Hoewel de TLS 1,2-afdwinging een in de hele branche geschikte coderings keuze is en wordt ingeschakeld zoals gepland, zullen we graag horen van klanten over hun specifieke implementaties en problemen met het vaststellen van TLS 1,2. U kunt uw opmerkingen voor dit doel verzenden naar [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
