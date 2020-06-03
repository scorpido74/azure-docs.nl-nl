---
title: Content Moderator versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Content Moderator versleuteling van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 38fc21ee45db25f015a6b8b534b0d922efa636f2
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310543"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator versleuteling van gegevens in rust

Content Moderator versleutelt uw gegevens automatisch wanneer deze in de cloud worden bewaard, zodat u kunt voldoen aan de beveiligings-en nalevings doelen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het aanvraag formulier voor de door de [klant beheerde sleutel](https://aka.ms/cogsvc-cmk)in en verzendt u content moderator deze. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat het is goedgekeurd voor het gebruik van CMK met de Content Moderator-service, moet u een nieuwe Content Moderator resource maken en E0 selecteren als prijs categorie. Zodra uw Content Moderator resource met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Gegevens versleuteling inschakelen voor uw Content Moderator team

Als u gegevens versleuteling wilt inschakelen voor uw Content Moderator beoordelings team, raadpleegt u de [Snelstartgids: probeer content moderator op Internet](quick-start.md#create-a-review-team).  

> [!NOTE]
> U moet een _resource-id_ opgeven met de prijs categorie content moderator E0.

## <a name="next-steps"></a>Volgende stappen

* Zie door de [klant beheerde sleutels voor Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md) voor een volledige lijst met services die CMK ondersteunen
* [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services aanvraag formulier voor door de klant beheerde sleutel](https://aka.ms/cogsvc-cmk)

