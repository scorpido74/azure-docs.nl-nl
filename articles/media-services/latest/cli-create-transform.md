---
title: Azure CLI-voorbeeldscript - Een transformatie maken | Microsoft Docs
description: Gebruik het Azure CLI-script om een transformatie te maken.
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
ms.openlocfilehash: 7fea8d997d25d6075e30620e7fd3527003c6a8bb
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128795"
---
# <a name="cli-example-create-a-transform"></a>CLI-voorbeeld: Een transformatie maken

In het Azure CLI-script in dit artikel ziet u hoe u een transformatie kunt maken. Transformaties geven een beschrijving van de eenvoudige werkstroom van taken voor het verwerken van video- of audiobestanden (vaak 'recipe' genoemd). Kijk altijd eerst of er al een transformatie bestaat met de gewenste naam 'recipe'. Als dit het geval is, moet u deze transformatie opnieuw gebruiken.

## <a name="prerequisites"></a>Vereisten 

[Een Azure Media Services-account maken](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> U kunt alleen een pad opgeven naar een aangepaste standaard Encoder voor een JSON-bestand voor [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Zie het voor beeld voor [coderen met een aangepaste transform](custom-preset-cli-howto.md) .
>
> U kunt geen bestands naam door geven wanneer u [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)gebruikt.

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Volgende stappen

[AZ AMS Transform (CLI)](https://docs.microsoft.com/cli/azure/ams/transform?view=azure-cli-latest)
