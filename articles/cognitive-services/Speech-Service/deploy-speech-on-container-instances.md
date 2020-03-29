---
title: Azure Container Instances uitvoeren - Spraakservice
titleSuffix: Azure Cognitive Services
description: Implementeer de spraakservicecontainer in een Azure Container Instance en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: e5d9143b0c84b579945b283ccae1cce36a7a4291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717406"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>De spraakservicecontainer implementeren in Azure Container Instances

Meer informatie over het implementeren van de [servicecontainer](speech-container-howto.md) Cognitive Services Speech naar Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Deze procedure toont de creatie van een Azure Speech-servicebron. Dan bespreken we het trekken van de bijbehorende container beeld. Tot slot benadrukken we de mogelijkheid om de orkestratie van de twee uit te oefenen vanuit een browser. Het gebruik van containers kan de aandacht van de ontwikkelaars verleggen van het beheren van infrastructuur naar het richten op applicatieontwikkeling.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Toegang tot het containerregister aanvragen

U moet eerst het [formulier Voor het aanvragen van opmerkingen](https://aka.ms/speechcontainerspreview/) van Cognitive Services Speech Containers invullen en indienen om toegang tot de container aan te vragen. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
