---
title: TLS 1.0 en 1.1 in De Voorzieningsservice voor apparaten (DPS) uitdeprecating | Microsoft Documenten
description: Richtlijnen met betrekking tot deafschaffing van TLS 1.0 en 1.1 en ondersteunde cijfers in IoT Hub en DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402787"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Afschrijving van TLS 1.0 en 1.1 in IoT Hub en Device Provisioning Service

Om de beste versleuteling te bieden, stappen IoT Hub en Device Provisioning Service (DPS) over naar Transport Layer Security (TLS) 1.2 als het versleutelingsmechanisme bij uitstek voor IoT-apparaten en -services. 

## <a name="supported-ciphers"></a>Ondersteunde cijfers

De tijdlijn voor de beschikbaarheid van verschillende cijfers die worden gebruikt in TLS handshake is als volgt:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (momenteel ondersteund)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (zal worden ondersteund in de tweede helft van 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (zal worden ondersteund in de tweede helft van 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (zal worden ondersteund in de tweede helft van 2020)


## <a name="customer-feedback"></a>Feedback

Hoewel de TLS 1.2-handhaving een branchebrede best-in-class encryptiekeuze is en zoals gepland wordt ingeschakeld, horen we nog steeds graag van klanten over hun specifieke implementaties en problemen met de vaststelling van TLS 1.2. Hiervoor u uw opmerkingen [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)sturen naar.
