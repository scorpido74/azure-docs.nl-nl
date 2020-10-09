---
title: Azure CLI-voorbeeldscript - Een transformatie maken | Microsoft Docs
description: Transformaties geven een beschrijving van de eenvoudige werkstroom van taken voor het verwerken van video- of audiobestanden (vaak 'recipe' genoemd). In het Azure CLI-script in dit artikel ziet u hoe u een transformatie kunt maken.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e85116aaad1eecbe137ae3e470811b31d1a855f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295968"
---
# <a name="cli-example-create-a-transform"></a>CLI-voorbeeld: Een transformatie maken

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In het Azure CLI-script in dit artikel ziet u hoe u een transformatie kunt maken. Transformaties geven een beschrijving van de eenvoudige werkstroom van taken voor het verwerken van video- of audiobestanden (vaak 'recipe' genoemd). Kijk altijd eerst of er al een transformatie bestaat met de gewenste naam 'recipe'. Als dit het geval is, moet u deze transformatie opnieuw gebruiken.

## <a name="prerequisites"></a>Vereisten 

[Een Azure Media Services-account maken](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> U kunt alleen een pad opgeven naar een aangepast, vooraf ingesteld JSON-bestand voor een Standard Encoder voor [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Zie het voorbeeld [Coderen met een aangepaste transformatie](custom-preset-cli-howto.md).
>
> U kunt geen bestandsnaam doorgeven wanneer u [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) gebruikt.

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Volgende stappen

[az ams transform (CLI)](/cli/azure/ams/transform?view=azure-cli-latest)
