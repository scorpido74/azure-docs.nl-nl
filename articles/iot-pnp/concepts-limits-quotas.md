---
title: Limieten en quota IoT Plug and Play Preview | Microsoft Documenten
description: Begrijp de limieten, quota en beperking die van toepassing zijn wanneer u IoT Plug and Play Preview gebruikt.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531374"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limieten, quota's en throttles van IoT Plug and Play Preview

In dit artikel worden de IoT Plug and Play-specifieke limieten, quota en beperking uitgelegd die van toepassing zijn in de openbare preview. Er zijn bestaande [IoT Hub-quota en beperking](../iot-hub/iot-hub-devguide-quotas-throttling.md) die ook van toepassing zijn.

## <a name="iot-hub"></a>IoT Hub

Voor de openbare preview gelden de volgende limieten en quota voor een IoT-hub:

| Beperkingen, beperkingen en gashendels | Waarde | Opmerkingen |
|-----|-----|-----|
| Aantal apparaatcapaciteitsmodellen (DCM's) of interfaces die per hub kunnen worden geregistreerd | 1500 ||
| Maximaal aantal interfaces dat per apparaat kan worden geregistreerd | 40 ||
| Maximum aantal DCM's dat per apparaat kan worden geregistreerd | 1 ||
| Maximale grootte van interface/DCM-bestand | 512 chars ||
| Maximale grootte van een interfacenaam | 256 chars ||
| Maximale grootte van een eigenschapsnaam  | 64 bytes, 7 verdiepingen in de diepte `$iotin`(en het eerste niveau is gereserveerd voor ) | Toegestane tekens: a-z, A-Z, 0-9 (niet als het eerste teken) en onderstrepen. |
| Maximale grootte van een eigenschapswaarde | 512 bytes ||
| Maximale grootte van een opdrachtnaam | 100 bytes ||
| Apparaat dubbele grootte | Hetzelfde als [IoT Hub Limits](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| OplossingsAPI-aanroepen in SKU (ongeacht eenheden) | 100 aanvraag per seconde ||

## <a name="model-repository"></a>Modelopslagplaats

Voor het openbare voorbeeld gelden de volgende limieten en quota voor een modelopslagplaats:

| Beperkingen, beperkingen en gashendels| Waarde |
|-----|-----|
| Aantal bedrijfsmodelopslagplaatsen per Azure Active Directory-tenant | 1 |
| Aantal autorisatiesleutels per modelopslagplaats | 10  |
| Aantal modellen (DCM's of interfaces) per bedrijfsmodelrepository| 1500  |
| Aantal modellen (DCM's of interfaces) in de openbare modelopslagplaats per Azure Active Directory-tenant| 1500  |
| Aantal DCM's of interfaces dat wordt verwijderd in een bedrijfsmodelopslagplaats | 10 query's per seconde (QPS)|
| Aantal modelopslagplaatsen dat door een tenant wordt gemaakt/bijgewerkt| 1 QPS |
| Aantal autorisatiesleutels dat wordt gemaakt/bijgewerkt/verwijderd in een modelopslagplaats | 1 QPS|
| Aantal DCM's dat wordt gemaakt in een bedrijfsmodelopslagplaats | 10 QPS |
| Aantal interfaces dat wordt gemaakt in een bedrijfsmodelrepository | 10 QPS|
| Aantal DCM's dat wordt gemaakt in de openbare modelopslagplaats | 10 QPS|
| Aantal interfaces dat wordt gemaakt in de openbare modelopslagplaats | 10 QPS|

## <a name="parser-library"></a>Parser bibliotheek

De parser bibliotheek volgt de grenzen die van toepassing zijn op de [Digital Twin Definition Language](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Volgende stappen

Een voorgestelde volgende stap is om te leren [hoe u verbinding maken met en communiceren met een IoT Plug and Play-apparaat.](./howto-develop-solution.md)
