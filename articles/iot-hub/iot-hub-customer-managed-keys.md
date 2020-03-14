---
title: Azure IoT Hub gegevens versleuteling op rest via door de klant beheerde sleutels | Microsoft Docs
description: Versleuteling van gegevens in rust met door de klant beheerde sleutels voor IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370573"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Versleuteling van gegevens in rust met door de klant beheerde sleutels voor IoT Hub

IoT Hub ondersteunt versleuteling van gegevens in rust met door de klant beheerde sleutels (CMK), ook wel bekend als uw eigen sleutel (BYOK), ondersteuning voor Azure IoT Hub. Azure IoT Hub voorziet in het versleutelen van gegevens in rust en onderweg. IoT Hub maakt standaard gebruik van door micro soft beheerde sleutels om de gegevens te versleutelen. Met CMK-ondersteuning hebben klanten nu de keuze om de gegevens in rust te versleutelen met een sleutel coderings sleutel, die wordt beheerd door de klanten, met behulp van de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Voor deze functie is het maken van een nieuwe IoT Hub (Basic-of Standard-laag) vereist in een van de volgende regio's: VS-Oost, VS-West 2, Zuid-Centraal VS of US Gov. Neem contact met ons op via [micro soft ondersteuning](https://azure.microsoft.com/support/create-ticket/)als u deze mogelijkheid wilt proberen. Deel uw bedrijfs naam en abonnements-ID wanneer u contact opneemt met micro soft ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
