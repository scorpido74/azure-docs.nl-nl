---
title: Face-container uitvoeren in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Implementeer de Face-container in een Azure Container Instance en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 4f8a08697443ec6920b3fa73de19a950e54e7f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716261"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>De Face-container implementeren in Azure Container Instances

Meer informatie over het implementeren van de container Cognitive Services [Face](../face-how-to-install-containers.md) naar Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Deze procedure toont de creatie van een Azure Face-bron. Dan bespreken we het trekken van de bijbehorende container beeld. Tot slot benadrukken we de mogelijkheid om de orkestratie van de twee uit te oefenen vanuit een browser. Het gebruik van containers kan de aandacht van de ontwikkelaars verleggen van het beheren van infrastructuur naar het richten op applicatieontwikkeling.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Toegang aanvragen tot het register van privécontainers

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]