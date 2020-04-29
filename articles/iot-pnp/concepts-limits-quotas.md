---
title: De preview-versie van een IoT-Plug en Play en quota beperken | Microsoft Docs
description: Meer informatie over de limieten, quota's en beperking die van toepassing zijn wanneer u IoT Plug en Play preview gebruikt.
author: miagdp
ms.author: miag
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cce99b7d9de09134fd01afb36c41bce3966e8536
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80518176"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Preview-limieten, quota's en gashendel voor IoT Plug en Play

In dit artikel worden de IoT-Plug en Play limieten, quota en beperking beschreven die van toepassing zijn op de open bare preview. Er zijn bestaande [IOT hub quota's en beperking](../iot-hub/iot-hub-devguide-quotas-throttling.md) die ook van toepassing zijn.

## <a name="iot-hub"></a>IoT Hub

Voor de open bare preview zijn de volgende limieten en quota van toepassing op een IoT-hub:

| Limieten, beperkingen en beperkingen | Waarde | Opmerkingen |
|-----|-----|-----|
| Aantal apparaatfuncties (DCMs) of interfaces dat per hub kan worden geregistreerd | 1500 ||
| Maximum aantal interfaces dat per apparaat kan worden geregistreerd | 40 ||
| Maximum aantal DCMs dat per apparaat kan worden geregistreerd | 1 ||
| Maximale grootte van interface/DCM-bestand | 512 kB ||
| Maximale grootte van een interface naam | 256 tekens ||
| Maximale grootte van een eigenschaps naam  | 64 bytes, 7 niveaus diep (en het eerste niveau is gereserveerd voor `$iotin`) | Toegestane tekens: a-z, A-Z, 0-9 (niet als het eerste teken) en onderstrepings teken. |
| Maximale grootte van een eigenschaps waarde | 512 bytes ||
| Maximale grootte van een opdracht naam | 100 bytes ||
| Dubbele grootte van apparaat | Hetzelfde als [IOT hub limieten](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Resolution API-aanroepen voor alle SKU'S (ongeacht eenheden) | 100 aanvragen per seconde ||

## <a name="model-repository"></a>Model opslagplaats

Voor de open bare preview zijn de volgende limieten en quota van toepassing op een model opslagplaats:

| Limieten, beperkingen en beperkingen | Waarde |
|-----|-----|
| Aantal opslag plaatsen van bedrijfs model per Azure Active Directory-Tenant | 1 |
| Aantal autorisatie sleutels per model opslagplaats | 10  |
| Aantal modellen (DCMs of interfaces) per bedrijfs model opslagplaats| 1500  |
| Het aantal modellen (DCMs of interfaces) in de open bare model opslagplaats per Azure Active Directory-Tenant| 1500  |
| Aantal DCMs of interfaces dat wordt verwijderd in een bedrijfs model opslagplaats | 10 query's per seconde (QPS)|
| Aantal model opslagplaatsen dat door een Tenant wordt gemaakt/bijgewerkt| 1 QPS |
| Aantal autorisatie sleutels dat wordt gemaakt/bijgewerkt/verwijderd in een model opslagplaats | 1 QPS|
| Aantal DCMs dat wordt gemaakt in een bedrijfs model opslagplaats | 10 QPS |
| Aantal interfaces dat wordt gemaakt in een bedrijfs model opslagplaats | 10 QPS|
| Aantal DCMs dat wordt gemaakt in de open bare model opslagplaats | 10 QPS|
| Aantal interfaces dat wordt gemaakt in de open bare model opslagplaats | 10 QPS|

## <a name="parser-library"></a>Parser-bibliotheek

De parser-bibliotheek volgt de limieten die van toepassing zijn op de [Digital-taal voor dubbele definitie](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Volgende stappen

Een voorgestelde volgende stap is om te leren hoe u [verbinding kunt maken met een IOT-Plug en Play apparaat en](./howto-develop-solution.md)hoe u deze kunt gebruiken.
