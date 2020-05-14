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
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202269"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision versleuteling van gegevens in rust

Met Azure Custom Vision worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Custom Vision versleuteling beveiligt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschik bare resources die zijn gemaakt na 11 mei 2020. Als u CMK met Custom Vision wilt gebruiken, moet u een nieuwe Custom Vision resource maken. Zodra de resource is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

### <a name="regional-availability"></a>Regionale beschikbaarheid

Door de klant beheerde sleutels zijn momenteel beschikbaar in deze regio's:

* US - zuid-centraal
* VS - west 2
* VS - oost
* VS (overheid) - Virginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Volgende stappen

* [Custom Vision aanvraag formulier voor door de klant beheerde sleutel](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


