---
title: Azure IoT Hub gegevens versleuteling op rest via door de klant beheerde sleutels | Microsoft Docs
description: Versleuteling van gegevens in rust met door de klant beheerde sleutels voor IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976571"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Versleuteling van gegevens in rust met door de klant beheerde sleutels voor IoT Hub

IoT Hub ondersteunt versleuteling van gegevens in rust met door de klant beheerde sleutels (CMK), ook wel bekend als uw eigen sleutel (BYOK). Azure IoT Hub zorgt voor versleuteling van gegevens in rust en in transit, omdat deze in onze data centers wordt geschreven en voor u wordt gedecodeerd wanneer u deze opent. IoT Hub maakt standaard gebruik van door micro soft beheerde sleutels om de gegevens in rust te versleutelen. Met CMK kunt u een andere laag versleuteling krijgen boven standaard versleuteling en kunt u ervoor kiezen om gegevens in rust te versleutelen met een sleutel coderings sleutel, die wordt beheerd via uw [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Dit biedt u de flexibiliteit om toegangs beheer te maken, te draaien, uit te scha kelen en in te trekken. Als BYOK is geconfigureerd voor uw IoT Hub, bieden we ook dubbele code ring, die een tweede beveiligingslaag biedt, en waarmee u de versleutelings sleutel via uw Azure Key Vault kunt beheren.

Voor deze functie is het maken van een nieuwe IoT Hub vereist (Basic-of Standard-laag). Neem contact met ons op via [micro soft ondersteuning](https://azure.microsoft.com/support/create-ticket/)als u deze mogelijkheid wilt proberen. Deel uw bedrijfs naam en abonnements-ID wanneer u contact opneemt met micro soft ondersteuning.


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
