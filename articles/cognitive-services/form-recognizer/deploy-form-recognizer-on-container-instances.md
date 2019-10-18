---
title: De container voor de formulier herkenning uitvoeren in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Implementeer de formulier Recognizer-container in een Azure-container exemplaar en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: c45d0e316cf67d22095911fadfd3d84ef407e1b0
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515630"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>De formulier Recognizer-container implementeren naar Azure Container Instances

Meer informatie over het implementeren van de Cognitive Services [Form Recognizer](form-recognizer-container-howto.md) -container naar Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Met deze procedure wordt het maken van een Azure Form Recognizer-resource gedemonstreerd. Vervolgens bespreken we het verzamelen van de bijbehorende container installatie kopie. Ten slotte markeren we de mogelijkheid om de indeling van de twee uit een browser uit te oefenen. Door gebruik te maken van containers kan de aandacht van de ontwikkel aars van de infra structuur afnemen in plaats van de ontwikkeling van toepassingen te richten.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Toegang aanvragen tot het container register

U moet eerst het [formulier toegangs aanvraag voor de Cognitive Services Form Recognizer containers](https://aka.ms/FormRecognizerRequestAccess) volt ooien en verzenden om toegang tot de container aan te vragen. Als u dit doet, wordt u ook gemeldd voor Computer Vision. U hoeft zich niet te registreren voor het Computer Vision aanvraag formulier afzonderlijk. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
