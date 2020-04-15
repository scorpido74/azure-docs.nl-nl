---
title: TLS 1.0 en 1.1 in IoT-hub uitdeprecating | Microsoft Documenten
description: Richtlijnen met betrekking tot deafschaffing van TLS 1.0 en 1.1 en ondersteunde cijfers in IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381299"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Afschrijving van TLS 1.0 en 1.1 in IoT Hub

Om de beste versleuteling te bieden, gaat IoT Hub over op Tls(Transport Layer Security) 1.2 als het versleutelingsmechanisme naar keuze voor IoT-apparaten en -services. 

## <a name="timeline"></a>Tijdlijn

IoT Hub blijft TLS 1.0/1.1 tot nader order ondersteunen. We raden echter aan dat alle klanten zo snel mogelijk migreren naar TLS 1.2.

## <a name="supported-ciphers"></a>Ondersteunde cijfers

De tijdlijn voor de beschikbaarheid van verschillende cijfers die worden gebruikt in TLS handshake is als volgt:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (momenteel ondersteund)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (zal worden ondersteund in de tweede helft van 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (zal worden ondersteund in de tweede helft van 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (zal worden ondersteund in de tweede helft van 2020)

## <a name="customer-feedback"></a>Feedback

Hoewel de TLS 1.2-handhaving een branchebrede best-in-class encryptiekeuze is en zoals gepland wordt ingeschakeld, horen we nog steeds graag van klanten over hun specifieke implementaties en problemen met de vaststelling van TLS 1.2. Hiervoor u uw opmerkingen [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)sturen naar.
