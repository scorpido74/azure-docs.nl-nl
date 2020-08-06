---
title: Ruimtelijke Azure-ankers installeren voor Unity
description: Een eenheids project configureren voor het gebruik van Azure-ruimtelijke ankers
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812290"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Ruimtelijke Azure-ankers configureren in een Unity-project

## <a name="requirements"></a>Vereisten

De Azure spatiale ankers ondersteunen eenheid 2019,4 (LTS) met de volgende configuraties.

* Unity 2019,4 met AR Foundation 3,1 wordt ondersteund in de Azure spatiale ankers 2.4.0 +.

## <a name="configuring-a-project"></a>Een project configureren

De ruimte van de Azure-ruimtelijke voor eenheid wordt momenteel gedistribueerd met behulp van een Asset package Unit ( `.unitypackage` ), dat u kunt vinden in de [github-releases](https://github.com/Azure/azure-spatial-anchors-samples/releases).

### <a name="import-the-asset-package"></a>Het Asset-pakket importeren

1. Down load het `AzureSpatialAnchors.unitypackage` bestand voor de versie die u wilt richten op basis van de [github-releases](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Volg de instructies [hier](https://docs.unity3d.com/Manual/AssetPackagesImport.html) om het Asset-pakket Unit te importeren in uw project.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: ankers in eenheid maken en vinden](./create-locate-anchors-unity.md)
