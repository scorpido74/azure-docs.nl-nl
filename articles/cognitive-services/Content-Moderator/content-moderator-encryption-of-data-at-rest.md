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
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372159"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator versleuteling van gegevens in rust

Content Moderator versleutelt uw gegevens automatisch wanneer deze in de cloud worden bewaard, zodat u kunt voldoen aan de beveiligings-en nalevings doelen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het aanvraag formulier voor de door de [klant beheerde sleutel](https://aka.ms/cogsvc-cmk)in en verzendt u content moderator deze. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat het is goedgekeurd voor het gebruik van CMK met de Content Moderator-service, moet u een nieuwe Content Moderator resource maken en E0 selecteren als prijs categorie. Zodra uw Content Moderator resource met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Gegevens versleuteling inschakelen voor uw Content Moderator team

Als u gegevens versleuteling wilt inschakelen voor uw Content Moderator beoordelings team, raadpleegt u de [Snelstartgids: probeer content moderator op Internet](quick-start.md#create-a-review-team).  

> [!NOTE]
> U moet een _resource-id_ opgeven met de prijs categorie content moderator E0.


## <a name="next-steps"></a>Volgende stappen

* [Content Moderator aanvraag formulier voor door de klant beheerde sleutel](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
