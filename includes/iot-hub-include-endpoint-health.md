---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84792083"
---
U kunt de REST API status van [eind punt ophalen](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) gebruiken om de status van de eind punten op te halen. U kunt het beste de [IOT hub metrische gegevens](../articles/iot-hub/iot-hub-metrics.md) met betrekking tot de bericht latentie van de route ring gebruiken om fouten op te sporen en op te sporen wanneer de status van het eind punt inactief of beschadigd is, omdat er een latentie wordt verwacht wanneer het eind punt zich in een van deze statussen bevindt.


|Status|Beschrijving|
|---|---|
|blijft|Het eind punt accepteert berichten zoals verwacht.|
|slechte|Het eind punt accepteert geen berichten en IoT Hub probeert berichten naar dit eind punt te verzenden.|
|unknown|IoT Hub heeft niet geprobeerd berichten te leveren aan dit eind punt.|
|gedegradeerd|Het eind punt accepteert berichten die langzamer zijn dan verwacht of worden hersteld vanaf een slechte status.|
|geval|IoT Hub levert geen berichten meer aan dit eind punt. Poging tot het verzenden van berichten naar dit eind punt is mislukt.|
