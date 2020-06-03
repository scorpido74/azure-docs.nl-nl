---
title: Versleuteling van gegevens in de vorm van een reserverings service
titleSuffix: Azure Cognitive Services
description: Versleuteling van de gegevens in de rest van de formulier herkenning.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 782e03f572f007b8523c3ae797dd626c383a01f4
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310713"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Versleuteling van de gegevens in de rest van de formulier herkenning

Azure Form Recognizer versleutelt uw gegevens automatisch wanneer deze persistent worden gemaakt in de Cloud. Met versleuteling voor formulier herkenning beschermt u uw gegevens en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschik bare resources die zijn gemaakt na 11 mei 2020. Als u CMK wilt gebruiken met de formulier herkenner, moet u een nieuwe resource voor de herkenner van een formulier maken. Zodra de resource is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* [Formulier voor het door de klant beheerde sleutel aanvraag](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


