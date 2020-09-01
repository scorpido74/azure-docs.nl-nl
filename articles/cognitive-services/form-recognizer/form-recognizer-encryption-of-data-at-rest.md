---
title: Versleuteling van gegevens in de vorm van een reserverings service
titleSuffix: Azure Cognitive Services
description: Micro soft biedt door micro soft beheerde coderings sleutels en kunt u ook uw Cognitive Services-abonnementen beheren met uw eigen sleutels, met de naam door de klant beheerde sleutels (CMK). In dit artikel wordt Inge gaan op de gegevens versleuteling van de formulier herkenner en hoe u CMK inschakelt en beheert.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 86b15b0059c2e3466ef65daeb53780798b3882d0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079248"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Versleuteling van de gegevens in de rest van de formulier herkenning

Azure Form Recognizer versleutelt uw gegevens automatisch wanneer deze persistent worden gemaakt in de Cloud. Met versleuteling voor formulier herkenning beschermt u uw gegevens en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschik bare resources die zijn gemaakt na 11 mei 2020. Als u CMK wilt gebruiken met de formulier herkenner, moet u een nieuwe resource voor de herkenner van een formulier maken. Zodra de resource is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* [Formulier voor het door de klant beheerde sleutel aanvraag](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)