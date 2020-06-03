---
title: Custom Vision versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Custom Vision versleuteling van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310288"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision versleuteling van gegevens in rust

Met Azure Custom Vision worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Custom Vision versleuteling beveiligt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschik bare resources die zijn gemaakt na 11 mei 2020. Als u CMK met Custom Vision wilt gebruiken, moet u een nieuwe Custom Vision resource maken. Zodra de resource is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Door de klant beheerde sleutels zijn momenteel beschikbaar in deze regio's:

* US - zuid-centraal
* VS - west 2
* VS - oost
* VS (overheid) - Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie door de [klant beheerde sleutels voor Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md) voor een volledige lijst met services die CMK ondersteunen
* [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services aanvraag formulier voor door de klant beheerde sleutel](https://aka.ms/cogsvc-cmk)
