---
title: CLI gebruiken om gereserveerde media-eenheden (MRUs) te schalen-Azure | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u CLI gebruikt om media verwerking met Azure Media Services te schalen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: bcbe5fe71e5a4d4d39a29d4a6828c104f6891c0d
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617142"
---
# <a name="how-to-scale-media-reserved-units"></a>Gereserveerde media-eenheden schalen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dit artikel laat u zien hoe u gereserveerde media-eenheden (MRSs) kunt schalen voor snellere code ring.

## <a name="prerequisites"></a>Vereisten

[Een Azure Media Services-account maken](./create-account-howto.md).

Meer informatie over [gereserveerde media-eenheden](concept-media-reserved-units.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Gereserveerde media-eenheden schalen met CLI

Voer de opdracht `mru` uit.

Met de volgende opdracht [AZ AMS account MRU](/cli/azure/ams/account/mru?view=azure-cli-latest) worden de gereserveerde media-eenheden voor het account ' amsaccount ' ingesteld met behulp van de para meters **Count** en **type** .

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Billing

Er worden kosten in rekening gebracht op basis van het aantal minuten dat de gereserveerde media-eenheden in uw account zijn ingericht. Dit gebeurt onafhankelijk van het feit of er taken worden uitgevoerd in uw account. Zie de sectie Veelgestelde vragen op de pagina met prijzen voor [Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor een gedetailleerde uitleg.   

## <a name="next-step"></a>Volgende stap

[Video's analyseren](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Zie ook

* [Quota en limieten](limits-quotas-constraints.md)
