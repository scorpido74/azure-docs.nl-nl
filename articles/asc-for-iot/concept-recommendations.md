---
title: Informatie over azure security center voor IoT-beveiligingsaanbevelingen| Microsoft Documenten
description: Meer informatie over het concept van beveiligingsaanbevelingen en hoe deze worden gebruikt in Azure Security Center voor IoT.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303576"
---
# <a name="security-recommendations"></a>Aanbevelingen voor beveiliging

Azure Security Center for IoT scant uw Azure-bronnen en IoT-apparaten en biedt beveiligingsaanbevelingen om uw aanvalsoppervlak te verminderen. Beveiligingsaanbevelingen zijn uitvoerbaar en zijn bedoeld om klanten te helpen bij het naleven van best practices op het gebied van beveiliging.

In dit artikel vindt u een lijst met aanbevelingen die kunnen worden geactiveerd op uw IoT Hub en/of IoT-apparaten.

## <a name="recommendations-for-iot-devices"></a>Aanbevelingen voor IoT-apparaten

Apparaataanbevelingen bieden inzichten en suggesties om de beveiligingshouding van apparaten te verbeteren. 

| Severity | Name                                                      | Gegevensbron | Beschrijving                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Middelgroot   | Poorten openen op apparaat                                      | Agent       | Er is een luistereindpunt gevonden op het apparaat.                                                                                                                                                        |
| Middelgroot   | Tolerant firewall beleid gevonden in een van de ketens. | Agent       | Toegestane firewallbeleid gevonden (INPUT/OUTPUT). Firewallbeleid moet standaard al het verkeer weigeren en regels definiëren om de benodigde communicatie naar/van het apparaat mogelijk te maken.                               |
| Middelgroot   | Tolerante firewallregel in de invoerketen is gevonden     | Agent       | Er is een regel in de firewall gevonden die een tolerant patroon bevat voor een breed scala aan IP-adressen of poorten.                                                                                    |
| Middelgroot   | Tolerante firewallregel in de uitvoerketen is gevonden    | Agent       | Er is een regel in de firewall gevonden die een tolerant patroon bevat voor een breed scala aan IP-adressen of poorten.                                                                                   |
| Middelgroot   | Validatie van de basislijn van het bewerkingssysteem is mislukt           | Agent       | Apparaat voldoet niet aan [CIS Linux benchmarks.](https://www.cisecurity.org/cis-benchmarks/)                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Operationele aanbevelingen voor IoT-apparaten

Operationele aanbevelingen bieden inzichten en suggesties om de configuratie van beveiligingsagenten te verbeteren.

| Severity | Name                                    | Gegevensbron | Beschrijving                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Laag      | Agent verstuurt ongebruikte berichten          | Agent       | 10% of meer van de beveiligingsberichten waren kleiner dan 4 KB tijdens de laatste 24 uur.  |
| Laag      | Beveiligingstwinconfiguratie niet optimaal | Agent       | Beveiliging twin configuratie is niet optimaal.                                        |
| Laag      | Conflict beveiliging twin-configuratie    | Agent       | Conflicten werden geïdentificeerd in de security twin configuratie. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Aanbevelingen voor IoT Hub

Aanbevelingswaarschuwingen bieden inzicht en suggesties voor acties om de beveiligingshouding van uw omgeving te verbeteren.  

| Severity | Name                                                     | Gegevensbron | Beschrijving                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hoog     | Identieke verificatiereferenties die door meerdere apparaten worden gebruikt | IoT Hub     | IoT Hub-verificatiereferenties worden door meerdere apparaten gebruikt. Dit kan duiden op een onwettig apparaat dat zich voordoet als een legitiem apparaat. Het gebruik van dubbele referenties verhoogt het risico op apparaatimitatie door een kwaadwillende actor. |
| Middelgroot   | Standaard-IP-filterbeleid moet worden ontzeggen                  | IoT Hub     | Ip-filterconfiguratie moet regels hebben gedefinieerd voor toegestaan verkeer en moet standaard al het andere verkeer weigeren.                                                                                                     |
| Middelgroot   | IP-filterregel bevat een groot IP-bereik                   | IoT Hub     | Een IP-filterbron-IP-bereik toestaan is te groot. Overdreven tolerante regels kunnen uw IoT-hub blootstellen aan kwaadwillende actoren.                                                                                       |
| Laag      | Diagnostische logboeken inschakelen in IoT Hub                       | IoT Hub     | Schakel logboeken in en bewaar ze maximaal een jaar. Met logboeken u activiteitssporen opnieuw maken voor onderzoeksdoeleinden wanneer zich een beveiligingsincident voordoet of uw netwerk is gecompromitteerd.                                       |
|

## <a name="next-steps"></a>Volgende stappen

- Overzicht van Azure Security Center voor [IoT-service](overview.md)
- Meer informatie over hoe u [toegang krijgt tot uw beveiligingsgegevens](how-to-security-data-access.md)
- Meer informatie over [het onderzoeken van een apparaat](how-to-investigate-device.md)