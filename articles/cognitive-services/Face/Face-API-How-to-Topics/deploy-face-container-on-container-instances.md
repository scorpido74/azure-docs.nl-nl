---
title: Een face-container uitvoeren in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Implementeer de face-container in een Azure-container exemplaar en test deze in een webbrowser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 5fbe316580cfa2ca280a2587536df037146e8d02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86538119"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>De face-container implementeren op Azure Container Instances

> [!IMPORTANT]
> De limiet voor gebruikers van Face-container is bereikt. Er worden momenteel geen nieuwe toepassingen geaccepteerd voor de Face-container.

Meer informatie over het implementeren van de Cognitive Services [Face](../face-how-to-install-containers.md) -container naar Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Met deze procedure wordt het maken van een Azure face-resource gedemonstreerd. Vervolgens bespreken we het verzamelen van de bijbehorende container installatie kopie. Ten slotte markeren we de mogelijkheid om de indeling van de twee uit een browser uit te oefenen. Door gebruik te maken van containers kan de aandacht van de ontwikkel aars van de infra structuur afnemen in plaats van de ontwikkeling van toepassingen te richten.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]