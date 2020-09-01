---
title: Face service-versleuteling van data-at-rest
titleSuffix: Azure Cognitive Services
description: Micro soft biedt door micro soft beheerde coderings sleutels en kunt u ook uw Cognitive Services-abonnementen beheren met uw eigen sleutels, met de naam door de klant beheerde sleutels (CMK). In dit artikel worden gegevens versleuteling in rust voor gezicht beschreven en wordt uitgelegd hoe u CMK kunt inschakelen en beheren.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: eab90fc2cb30ae8e9f1c19bdbefc6fbc88c32f76
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079265"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Face service-versleuteling van data-at-rest

Met de service Face worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Met de face service-versleuteling beschermt u uw gegevens en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het [aanvraag formulier voor de klant beheerde sleutel](https://aka.ms/cogsvc-cmk)in. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat u hebt goedgekeurd voor het gebruik van CMK met de face-service, moet u een nieuwe gezichts bron maken en E0 selecteren als prijs categorie. Zodra uw gezichts bron met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie door de [klant beheerde sleutels voor Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md) voor een volledige lijst met services die CMK ondersteunen
* [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services aanvraag formulier voor door de klant beheerde sleutel](https://aka.ms/cogsvc-cmk)
