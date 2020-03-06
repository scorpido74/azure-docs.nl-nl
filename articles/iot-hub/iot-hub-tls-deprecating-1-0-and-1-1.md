---
title: TLS 1,0 en 1,1 in IoT Hub en de Device Provisioning Service (DPS) afzien | Microsoft Docs
description: Richt lijnen met betrekking tot de afschaffing van TLS 1,0 en 1,1 en ondersteunde code ringen in IoT Hub en DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402787"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Afschaffing van TLS 1,0 en 1,1 in IoT Hub en Device Provisioning Service

IoT Hub en de DPS (Device Provisioning Service) worden verplaatst naar Transport Layer Security (TLS) 1,2 als versleutelings methode voor IoT-apparaten en-services om de beste versleuteling te bieden. 

## <a name="supported-ciphers"></a>Ondersteunde versleuteling

De tijd lijn voor de beschik baarheid van verschillende code ringen die in TLS-Handshake worden gebruikt, is als volgt:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (momenteel ondersteund)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (wordt ondersteund in de tweede helft van 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (wordt ondersteund in de tweede helft van 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (wordt ondersteund in de tweede helft van 2020)


## <a name="customer-feedback"></a>Feedback van klanten

Hoewel de TLS 1,2-afdwinging een in de hele branche geschikte coderings keuze is en wordt ingeschakeld zoals gepland, zullen we graag horen van klanten over hun specifieke implementaties en problemen met het vaststellen van TLS 1,2. Voor dit doel kunt u uw opmerkingen naar [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)verzenden.
