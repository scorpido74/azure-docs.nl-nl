---
title: Content Moderator encryptie van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Content Moderator encryptie van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372159"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator encryptie van gegevens in rust

Content Moderator versleutelt uw gegevens automatisch wanneer deze in de cloud worden weergegeven, zodat u de beveiligings- en nalevingsdoelstellingen van uw organisatie bereiken.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar op de e0-prijscategorie. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het door de [klant beheerde sleutelaanvraagformulier voor inhoudsmoderator](https://aka.ms/cogsvc-cmk)in en verzendt deze dit in. Het duurt ongeveer 3-5 werkdagen om de status van uw verzoek te horen. Afhankelijk van de vraag u in een wachtrij worden geplaatst en worden goedgekeurd zodra er ruimte beschikbaar komt. Zodra u is goedgekeurd voor het gebruik van CMK met de contentmoderatorservice, moet u een nieuwe bron voor inhoudsmoderator maken en E0 als prijscategorie selecteren. Zodra uw contentmoderatorbron met de e0-prijscategorie is gemaakt, u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Gegevensversleuteling inschakelen voor uw Content Moderator Team

Zie het [Quickstart: Probeer Inhoudsmoderator op internet](quick-start.md#create-a-review-team)om gegevensversleuteling voor uw Content Moderator Review Team in te schakelen.  

> [!NOTE]
> Je moet een _Resource-id_ met de prijscategorie Content Moderator E0 verstrekken.


## <a name="next-steps"></a>Volgende stappen

* [Formulier voor het aanvragen van een door de klant beheerde sleutelformulier voor de inhoudsmoderator](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
