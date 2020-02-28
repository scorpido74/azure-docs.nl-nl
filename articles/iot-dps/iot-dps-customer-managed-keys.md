---
title: Azure Device Provisioning Service gegevens versleuteling op rest via door de klant beheerde sleutels | Microsoft Docs
description: Versleuteling van gegevens in rust met door de klant beheerde sleutels voor Device Provisioning Service
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675141"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Versleuteling van gegevens in rust met door de klant beheerde sleutels voor Device Provisioning Service

## <a name="overview"></a>Overzicht

Device Provisioning Service (DPS) ondersteunt versleuteling van gegevens in rust met door de klant beheerde sleutels (CMK), ook wel bekend als uw eigen sleutel (BYOK). DPS voorziet in het versleutelen van gegevens in rust en onderweg. DPS maakt standaard gebruik van door micro soft beheerde sleutels voor het versleutelen van de gegevens. Met CMK-ondersteuning hebben klanten nu de keuze om de gegevens in rust te versleutelen met een sleutel coderings sleutel, die wordt beheerd door de klanten, met behulp van de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Voor deze functie is het maken van een nieuwe DPS vereist in een van de volgende regio's: VS-Oost, VS-West 2 of Zuid-Centraal vs. Neem contact met ons op via [micro soft ondersteuning](https://azure.microsoft.com/support/create-ticket/)als u deze mogelijkheid wilt proberen. Deel uw bedrijfs naam en abonnements-ID wanneer u contact opneemt met micro soft ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)

* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)