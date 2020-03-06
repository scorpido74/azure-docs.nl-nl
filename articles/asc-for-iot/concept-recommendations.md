---
title: Meer informatie over Azure Security Center voor IoT-beveiligings aanbevelingen | Microsoft Docs
description: Meer informatie over het concept van beveiligings aanbevelingen en hoe ze worden gebruikt in Azure Security Center voor IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: a8de821abcedf6bb9a331852a2c0af9b6439667a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303576"
---
# <a name="security-recommendations"></a>Aanbevelingen voor beveiliging

Met Azure Security Center voor IoT worden uw Azure-resources en IoT-apparaten gescand en worden beveiligings aanbevelingen gegeven om uw kwets baarheid te beperken. Beveiligings aanbevelingen zijn maat regelen en zijn bedoeld om klanten te helpen bij het naleven van de best practices voor beveiliging.

In dit artikel vindt u een lijst met aanbevelingen die kunnen worden geactiveerd op uw IoT Hub en/of IoT-apparaten.

## <a name="recommendations-for-iot-devices"></a>Aanbevelingen voor IoT-apparaten

Aanbevelingen voor apparaten bieden inzichten en suggesties voor het verbeteren van de beveiliging van postuur. 

| Severity | Naam                                                      | Gegevensbron | Beschrijving                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Middelgroot   | Poorten op het apparaat openen                                      | Agent       | Er is een luisterend eind punt gevonden op het apparaat.                                                                                                                                                        |
| Middelgroot   | Permissief firewall beleid gevonden in een van de ketens. | Agent       | Toegestaan firewall beleid gevonden (invoer/uitvoer). Het firewall beleid moet standaard al het verkeer weigeren en regels definiëren om de benodigde communicatie met/van het apparaat mogelijk te maken.                               |
| Middelgroot   | Er is een strikte firewall regel in de invoer keten gevonden     | Agent       | Er is een regel in de firewall gevonden die een soepel patroon bevat voor een breed scala aan IP-adressen of poorten.                                                                                    |
| Middelgroot   | Er is een strikte firewall regel in de uitvoer keten gevonden    | Agent       | Er is een regel in de firewall gevonden die een soepel patroon bevat voor een breed scala aan IP-adressen of poorten.                                                                                   |
| Middelgroot   | Validatie van de basislijn planning van het besturings systeem is mislukt           | Agent       | Het apparaat voldoet niet aan de [CIS Linux-Benchmarks](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Operationele aanbevelingen voor IoT-apparaten

Operationele aanbevelingen bieden inzichten en suggesties voor het verbeteren van de configuratie van de beveiligings agent.

| Severity | Naam                                    | Gegevensbron | Beschrijving                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Laag      | Agent verzendt ongebruikte berichten          | Agent       | 10% of meer beveiligings berichten zijn in de afgelopen 24 uur kleiner dan 4 KB.  |
| Laag      | Dubbele configuratie voor beveiliging niet optimaal | Agent       | De dubbele configuratie van beveiliging is niet optimaal.                                        |
| Laag      | Conflict tussen beveiligings configuratie    | Agent       | Er zijn conflicten gevonden in de beveiligings-dubbele configuratie. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Aanbevelingen voor IoT Hub

Aanbevelings waarschuwingen bieden inzicht en suggesties voor acties om de beveiliging postuur van uw omgeving te verbeteren.  

| Severity | Naam                                                     | Gegevensbron | Beschrijving                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hoog     | Identieke verificatie referenties die worden gebruikt door meerdere apparaten | IoT Hub     | IoT Hub authenticatie referenties worden gebruikt door meerdere apparaten. Dit kan duiden op een illegitimate-apparaat dat een legitiem apparaat imiteert. Dubbel referentie gebruik verhoogt het risico van imitatie van apparaten door een schadelijke actor. |
| Middelgroot   | Het standaard IP-filter beleid moet worden geweigerd                  | IoT Hub     | Voor de configuratie van de IP-filter moeten regels worden gedefinieerd voor het toegestane verkeer, en standaard moeten alle andere verkeer standaard worden geweigerd.                                                                                                     |
| Middelgroot   | De IP-filter regel bevat een groot IP-bereik                   | IoT Hub     | Een IP-adres bereik van de bron filter regel toestaan is te groot. Over de regel matig strikte regels kunnen uw IoT-hub beschikbaar maken voor kwaad aardige actors.                                                                                       |
| Laag      | Diagnostische logboeken inschakelen in IoT Hub                       | IoT Hub     | Logboeken inschakelen en ze tot een jaar bewaren. Door Logboeken te behouden, kunt u de activiteiten sporen voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.                                       |
|

## <a name="next-steps"></a>Volgende stappen

- [Overzicht](overview.md) van Azure Security Center voor IOT-service
- Meer informatie over [het verkrijgen van toegang tot uw beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [het onderzoeken van een apparaat](how-to-investigate-device.md)