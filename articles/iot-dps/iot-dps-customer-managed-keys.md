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
ms.openlocfilehash: e9b5a549d550f117e080fc64193fbe29658f401d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85298222"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Versleuteling van gegevens in rust met door de klant beheerde sleutels voor Device Provisioning Service

## <a name="overview"></a>Overzicht

Device Provisioning Service (DPS) ondersteunt versleuteling van gegevens in rust met door de klant beheerde sleutels (CMK), ook wel bekend als uw eigen sleutel (BYOK). DPS voorziet in het versleutelen van gegevens in rust en onderweg, omdat deze worden geschreven in onze data centers en worden ontsleuteld voor u wanneer u er toegang toe hebt. DPS maakt standaard gebruik van door micro soft beheerde sleutels om de gegevens in rust te versleutelen. Met CMK kunt u een extra laag versleuteling krijgen boven standaard platform versleuteling door ervoor te kiezen om gegevens in rust te versleutelen met een sleutel versleutelings sleutel die wordt beheerd via uw [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Dit biedt u de flexibiliteit om sleutels te maken, te roteren, uit te scha kelen en in te trekken. Als CMK is geconfigureerd voor uw DPS, betekent dit dat dubbele versleuteling is ingeschakeld met twee beveiligings lagen die uw gegevens actief beveiligen. 

Voor deze functie is het maken van een nieuwe DPS vereist. Neem contact met ons op via [micro soft ondersteuning](https://azure.microsoft.com/support/create-ticket/)als u deze mogelijkheid wilt proberen. Deel uw bedrijfs naam en abonnements-ID wanneer u contact opneemt met micro soft ondersteuning.


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)

* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
