---
title: Anomaliedetectorcontainer uitvoeren in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Implementeer de container Anomaliedetector in een Azure Container Instance en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716361"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Een anomaliedetectorcontainer implementeren in Azure Container Instances

Meer informatie over het implementeren van de container Cognitive Services [Anomaly Detector](../anomaly-detector-container-howto.md) naar Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Deze procedure toont de creatie van een Anomalie Detector bron. Dan bespreken we het trekken van de bijbehorende container beeld. Tot slot benadrukken we de mogelijkheid om de orkestratie van de twee uit te oefenen vanuit een browser. Het gebruik van containers kan de aandacht van de ontwikkelaars verleggen van het beheren van infrastructuur naar het richten op applicatieontwikkeling.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Toegang aanvragen tot het register van privécontainers

U moet eerst het [aanvraagformulier anomaly detectorcontainer](https://aka.ms/adcontainer) invullen en indienen om toegang tot de container aan te vragen.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Volgende stappen

* [Containers installeren en uitvoeren](../anomaly-detector-container-configuration.md) controleren voor het trekken van de containerafbeelding en de container uitvoeren
* Containers [configureren controleren](../anomaly-detector-container-configuration.md) voor configuratie-instellingen
* [Meer informatie over de API-service voor Anomalie Detector](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
