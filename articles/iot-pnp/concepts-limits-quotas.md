---
title: De preview-versie van een IoT-Plug en Play en quota beperken | Microsoft Docs
description: Meer informatie over de limieten, quota's en beperking die van toepassing zijn wanneer u IoT Plug en Play preview gebruikt.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337395"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Preview-limieten, quota's en gashendel voor IoT Plug en Play

In dit artikel worden de IoT-Plug en Play limieten, quota en beperking beschreven die van toepassing zijn op de open bare preview. Er zijn bestaande [IOT hub quota's en beperking](../iot-hub/iot-hub-devguide-quotas-throttling.md) die ook van toepassing zijn.

## <a name="iot-hub"></a>IoT Hub

Voor de open bare preview zijn de volgende limieten en quota van toepassing op een IoT-hub:

| Limieten, beperkingen en beperkingen | Waarde | Opmerkingen |
|-----|-----|-----|
| Aantal interfaces dat per hub kan worden geregistreerd | 1500 ||
| Maximale grootte van een onderdeel naam | 1-64 tekens | Toegestane tekens: a-z, A-Z, 0-9 (niet als het eerste teken) en het onderstrepings teken (niet als het eerste of laatste teken). |
| Maximale grootte van een eigenschaps naam | 1-64 tekens | Toegestane tekens: a-z, A-Z, 0-9 (niet als het eerste teken) en het onderstrepings teken (niet als het eerste of laatste teken). |
| Maximale grootte van een eigenschaps waarde | Hetzelfde als de Digital Apparaatdubbels Definition Language- [eigenschap](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) | 5 niveaus diep en mogen geen matrix of een complex schema zijn dat een matrix bevat |
| Maximale grootte van een opdracht naam | 1-64 tekens | Toegestane tekens: a-z, A-Z, 0-9 (niet als het eerste teken) en het onderstrepings teken (niet als het eerste of laatste teken).|
| Dubbele grootte van apparaat | Hetzelfde als [IOT hub limieten](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||

## <a name="parser-library"></a>Parser-bibliotheek

De parser-bibliotheek volgt de limieten die van toepassing zijn op de taal van de [Digital apparaatdubbels-definitie](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="next-steps"></a>Volgende stappen

De voorgestelde volgende stap is het controleren van de [IoT Plug en Play-architectuur](concepts-architecture.md).
