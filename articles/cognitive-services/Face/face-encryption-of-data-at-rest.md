---
title: Face service encryptie van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Face service encryptie van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372214"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Face service encryptie van gegevens in rust

De Face-service versleutelt uw gegevens automatisch wanneer deze in de cloud worden weergegeven. De Face-serviceversleuteling beschermt uw gegevens en helpt u om aan uw organisatorische beveiligings- en nalevingsverplichtingen te voldoen.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar op de e0-prijscategorie. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het door de [klant beheerde sleutelaanvraagformulier](https://aka.ms/cogsvc-cmk)van Face Service in en verzendt deze dit in. Het duurt ongeveer 3-5 werkdagen om de status van uw verzoek te horen. Afhankelijk van de vraag u in een wachtrij worden geplaatst en worden goedgekeurd zodra er ruimte beschikbaar komt. Zodra u is goedgekeurd voor het gebruik van CMK met de Face-service, moet u een nieuwe Face-bron maken en E0 selecteren als de prijslaag. Zodra uw Face-bron met de E0-prijscategorie is gemaakt, u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Volgende stappen

* [Formulier voor het aanvragen van een door de klant beheerde sleutelformulier voor de klant](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


