---
title: Container Formulierherkenning uitvoeren in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Implementeer de container Formulierherkenning in een Azure Container Instance en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 9a3456eb4d30aa8d163488f558b571dd97c73bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605114"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>De container Formulierherkenning implementeren in Azure Container Instances

Meer informatie over het implementeren van de container Cognitive Services [Form Recognizer](form-recognizer-container-howto.md) naar Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Deze procedure toont de creatie van een Azure Form Recognizer-bron. Dan bespreken we het trekken van de bijbehorende container beeld. Tot slot benadrukken we de mogelijkheid om de orkestratie van de twee uit te oefenen vanuit een browser. Het gebruik van containers kan de aandacht van de ontwikkelaars verleggen van het beheren van infrastructuur naar het richten op applicatieontwikkeling.

> [!IMPORTANT]
> De form recognizer-containers gebruiken momenteel versie 1.0 van de API voor formulierherkenning. U hebt toegang tot de nieuwste versie van de API door in plaats daarvan de beheerde service te gebruiken.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Toegang tot het containerregister aanvragen

U moet eerst het [toegangsformulier voor het toegangsformulier voor het herkennen van cognitive services-containers](https://aka.ms/FormRecognizerContainerRequestAccess) invullen en indienen om toegang tot de container aan te vragen. Dit meldt u ook voor Computer Vision. U hoeft zich niet apart aan te melden voor het formulier Computer Vision. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
