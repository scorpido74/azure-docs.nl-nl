---
title: Computer vision-container uitvoeren in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Implementeer de container Computer Vision in een Azure Container Instance en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 1c858432a3382e6dbc8e479aab11b18dc5eebfe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499098"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>De container Computer Vision implementeren in Azure Container Instances

Meer informatie over het implementeren van de container Cognitive Services [Computer Vision](computer-vision-how-to-install-containers.md) naar Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Deze procedure toont de creatie van de Computer Vision-bron aan. Dan bespreken we het trekken van de bijbehorende container beeld. Tot slot benadrukken we de mogelijkheid om de orkestratie van de twee uit te oefenen vanuit een browser. Het gebruik van containers kan de aandacht van de ontwikkelaars verleggen van het beheren van infrastructuur naar het richten op applicatieontwikkeling.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Toegang aanvragen tot het register van privécontainers

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]