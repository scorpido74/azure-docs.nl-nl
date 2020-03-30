---
title: Azure Device Provisioning Service-gegevensversleuteling in rust via door de klant beheerde sleutels| Microsoft Documenten
description: Versleuteling van gegevens in rust met door de klant beheerde sleutels voor device provisioning service
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675141"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Versleuteling van gegevens in rust met door de klant beheerde sleutels voor device provisioning service

## <a name="overview"></a>Overzicht

Device Provisioning Service (DPS) ondersteunt versleuteling van gegevens in rust met door de klant beheerde sleutels (CMK), ook wel bekend als Bring your own key (BYOK). DPS biedt versleuteling van gegevens in rust en onderweg. Standaard gebruikt DPS door Microsoft beheerde sleutels om de gegevens te versleutelen. Met CMK-ondersteuning hebben klanten nu de keuze om de gegevens in rust te versleutelen met een sleutelversleutelingssleutel, beheerd door de klanten, met behulp van de [Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)

Deze mogelijkheid vereist de oprichting van een nieuwe DPS, in een van de volgende regio's: Oost-VS, West US 2, of South Central US. Neem contact met ons op via [Microsoft-ondersteuning](https://azure.microsoft.com/support/create-ticket/)om deze mogelijkheid uit te proberen. Deel uw bedrijfsnaam en abonnements-ID wanneer u contact opneemt met Microsoft-ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de service voor apparaatinrichting](https://docs.microsoft.com/azure/iot-dps/)

* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)