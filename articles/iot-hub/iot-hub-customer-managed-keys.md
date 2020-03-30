---
title: Azure IoT Hub-gegevensversleuteling in rust via door de klant beheerde sleutels| Microsoft Documenten
description: Versleuteling van gegevens in rust met door de klant beheerde sleutels voor IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370573"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Versleuteling van gegevens in rust met door de klant beheerde sleutels voor IoT Hub

IoT Hub ondersteunt versleuteling van gegevens in rust met door de klant beheerde sleutels (CMK), ook wel bekend als Bring your own key (BYOK), ondersteuning voor Azure IoT Hub. Azure IoT Hub biedt versleuteling van gegevens in rust en onderweg. Standaard gebruikt IoT Hub door Microsoft beheerde sleutels om de gegevens te versleutelen. Met CMK-ondersteuning hebben klanten nu de keuze om de gegevens in rust te versleutelen met een sleutelversleutelingssleutel, beheerd door de klanten, met behulp van de [Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)

Deze mogelijkheid vereist de creatie van een nieuwe IoT Hub (basis- of standaardlaag), in een van de volgende regio's: Oost-VS, West US 2, South Central US of US Gov. Neem contact met ons op via [Microsoft-ondersteuning](https://azure.microsoft.com/support/create-ticket/)om deze mogelijkheid uit te proberen. Deel uw bedrijfsnaam en abonnements-ID wanneer u contact opneemt met Microsoft-ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
