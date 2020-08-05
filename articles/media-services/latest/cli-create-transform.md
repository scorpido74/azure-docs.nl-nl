---
title: Azure CLI-voorbeeldscript - Een transformatie maken | Microsoft Docs
description: Transformaties geven een beschrijving van de eenvoudige werkstroom van taken voor het verwerken van video- of audiobestanden (vaak 'recipe' genoemd). In het Azure CLI-script in dit artikel ziet u hoe u een transformatie kunt maken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 365c6a6a10ee79d96c1054416669e84c5392344c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092164"
---
# <a name="cli-example-create-a-transform"></a>CLI-voorbeeld: Een transformatie maken

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
