---
title: Azure IoT Central video Analytics-object en bewegings detectie | Microsoft Docs
description: Meer informatie over het bouwen van een IoT Central-toepassing met behulp van de toepassings sjabloon video Analytics-object-en bewegings detectie in IoT Central. Deze sjabloon maakt gebruik van live video analyses en verbonden camera's.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 087c5e5716a4c8c640faebd7470905fde85d0136
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038270"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Video analyse-toepassings architectuur voor object-en bewegings detectie

Met de sjabloon **video Analytics-toepassing voor detectie van objecten en bewegingen** kunt u IOT-oplossingen bouwen, zoals live video analyse mogelijkheden.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Architectuur":::

De belangrijkste onderdelen van de video analytics-oplossing zijn:

## <a name="live-video-analytics-lva"></a>Live video Analytics (LVA)

LVA biedt een platform waarmee u intelligente video toepassingen kunt bouwen die de rand en de Cloud omvatten. Met het platform kunt u intelligente video toepassingen bouwen die de rand en de Cloud omvatten. Het platform biedt de mogelijkheid om live video vast te leggen, te registreren, te analyseren en de resultaten te publiceren. Dit kan video-of video analyses zijn naar Azure-Services. De Azure-Services kunnen worden uitgevoerd in de Cloud of de rand. Het platform kan worden gebruikt om IoT-oplossingen te verbeteren met videoanalyse.

Zie [Live video Analytics](https://github.com/Azure/live-video-analytics) op github voor meer informatie.

## <a name="iot-edge-lva-gateway-module"></a>LVA-gateway module IoT Edge

In de module IoT Edge LVA-gateway worden camera's als nieuwe apparaten geïnstantieerd en rechtstreeks verbonden met IoT Central met behulp van de client-SDK voor IoT-apparaten.

Bij deze referentie-implementatie maken apparaten verbinding met de oplossing met behulp van symmetrische sleutels van de rand. Zie [Get connected to Azure IOT Central](../core/concepts-get-connected.md) voor meer informatie over de connectiviteit van apparaten

## <a name="media-graph"></a>Mediagrafiek

Met media Graph kunt u bepalen waar de media moeten worden vastgelegd, hoe deze moeten worden verwerkt en waar de resultaten moeten worden geleverd. U configureert media Graph door onderdelen of knoop punten op de gewenste manier te verbinden. Zie [Media Graph](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) op github voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De voorgestelde volgende stap is informatie over het [maken van een video Analytics-toepassing in Azure IOT Central](tutorial-video-analytics-create-app.md).
