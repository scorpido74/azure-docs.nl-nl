---
title: Personalisatie service versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Personaler service versleuteling van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 10eb627a340b45c93b2cfb2973e294d8d5d7c7e5
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307802"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Personalisatie service versleuteling van gegevens in rust

Met de Personaler service worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. De Personaler service Encryption beveiligt uw gegevens en helpt u om te voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het aanvraag formulier voor de door de [klant beheerde sleutel van de persoonlijkere service](https://aka.ms/cogsvc-cmk)in en verzendt u deze. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat u hebt goedgekeurd voor het gebruik van CMK met de Personaler-service, moet u een nieuwe Personaler-resource maken en E0 selecteren als prijs categorie. Zodra uw persoonlijke resource met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* [Persoonlijkere service klant-aanvraag formulier voor beheerde sleutel](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
