---
title: LUIS-container implementeren op Azure Container-exemplaren
titleSuffix: Azure Cognitive Services
description: Implementeer de LUIS-container in een Azure Container-exemplaar en test deze in een webbrowser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: 08af17106846a0f5f7a0ccc2b01da1b2e15c1143
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879197"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>De LUIS-container (Language Understanding) implementeren in Azure Container-exemplaren

Meer informatie over het implementeren van de [LUIS-container](luis-container-howto.md) voor cognitive services naar Azure [Container-exemplaren](https://docs.microsoft.com/azure/container-instances/). Deze procedure toont de creatie van een Anomalie Detector bron. Dan bespreken we het trekken van de bijbehorende container beeld. Tot slot benadrukken we de mogelijkheid om de orkestratie van de twee uit te oefenen vanuit een browser. Het gebruik van containers kan de aandacht van de ontwikkelaars verleggen van het beheren van infrastructuur naar het richten op applicatieontwikkeling.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

De LUIS-container `.gz` vereist een modelbestand dat tijdens runtime wordt ingetrokken. De container moet toegang hebben tot dit modelbestand via een volumehouder van de containerinstantie. Zie [Een bestandsshare maken voor](../../storage/files/storage-how-to-create-file-share.md)informatie over het maken van een Azure-bestandsshare . Let op de naam, sleutel en bestandssharenaam van het Azure Storage-account, zoals u ze later nodig hebt.

### <a name="export-and-upload-packaged-luis-app"></a>Verpakte LUIS-app exporteren en uploaden

Als u het LUIS-model (verpakte app) wilt uploaden naar het Azure-bestandsaandeel, moet u <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">het eerst <span class="docon docon-navigate-external x-hidden-focus"> </span>exporteren vanuit de LUIS-portal. </a> Navigeer vanuit de Azure-portal naar de **overzichtspagina** van de bron van het opslagaccount en selecteer **Bestandsshares**. Selecteer de naam van bestandsshare die u onlangs hebt gemaakt en selecteer vervolgens de knop **Uploaden.**

> [!div class="mx-imgBorder"]
> ![Uploaden naar bestandsshare](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Upload het LUIS-modelbestand.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
