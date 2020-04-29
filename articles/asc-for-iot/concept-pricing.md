---
title: Prijs en bijbehorende kosten
description: Meer informatie over de kosten van Azure Security Center voor IoT en hoe u deze kunt beheren.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: fe117cf8d05ba3392b71858acf94d1fc88c1a527
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311568"
---
# <a name="pricing-and-associated-costs"></a>Prijs en bijbehorende kosten

In dit artikel wordt uitgelegd Azure Security Center voor IoT-prijs model, worden alle gekoppelde kosten samenvatten en wordt uitgelegd hoe u deze kunt beheren.

## <a name="pricing"></a>Prijzen

Het prijs model Azure Security Center voor IoT bestaat uit twee delen en wordt gefactureerd wanneer een IoT Hub is [ingeschakeld](quickstart-onboard-iot-hub.md) in azure Security Center voor IOT:

- Kosten per apparaat-ingebouwde beveiligings mogelijkheden op basis van de analyse van IoT Hub Logboeken.

- Kosten per bericht: Verbeterde beveiligings mogelijkheden op basis van beveiligings berichten van IoT Edge of blad apparaten.

Zie [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie.

## <a name="associated-costs"></a>Gekoppelde kosten

Er zijn kosten verbonden aan Azure Security Center voor IoT, die geen deel uitmaken van de rechtstreekse prijzen:

- Opslag kosten Log Analytics

U kunt de gekoppelde kosten verlagen door te kiezen voor bepaalde oplossings functies. Afmelden door uw instellingen te wijzigen.

Uw instellingen wijzigen:

1. Open IoT Hub.

1. Klik onder **beveiliging**op **overzicht**.

1. Klik op **Instellingen**.

De volgende tabel bevat een samen vatting van de bijbehorende kosten en implicaties van elke optie.

|     | Gebruik | Opmerking |
| --- | --- | --- |
| **Opslag Log Analytics** |  |
| Aanbeveling en waarschuwingen van het apparaat| Beveiligings aanbeveling en waarschuwingen die worden gegenereerd door de service | Niet optioneel |
| Onbewerkte beveiligings gegevens| Onbewerkte beveiligings gegevens van IoT-apparaten, verzameld door beveiligings agenten | _Beveiligings gebeurtenissen voor het opslaan van onbewerkte apparaten_ uitschakelen |
|

>[!Important]
> Uitbellen heeft ernstige gevolgen voor de Azure Security Center voor de beschik baarheid van IoT-beveiligings functies.

| Opt-out | Gevolgen |
| --- | --- |
| _Verzameling van dubbele meta gegevens_ | [Aangepaste waarschuwingen](quickstart-create-custom-alerts.md) uitschakelen |
| | Aanbevelingen van IoT Edge-manifest uitschakelen |
| | Aanbevelingen en waarschuwingen op basis van apparaat-id's uitschakelen |
| _Beveiligings gebeurtenissen voor onbewerkte apparaten opslaan_ | Details van de algemene aanbevelingen voor het besturings systeem van het apparaat zijn niet beschikbaar |
| | Er zijn geen gegevens over [waarschuwingen](concept-security-alerts.md) en [aanbevelings](concept-recommendations.md) onderzoeken beschikbaar |
|

## <a name="see-also"></a>Zie ook

- Toegang tot uw [onbewerkte beveiligings gegevens](how-to-security-data-access.md)
- [Een apparaat onderzoeken](how-to-investigate-device.md)
- [Beveiligings aanbevelingen](concept-recommendations.md) begrijpen en verkennen
- [Beveiligings waarschuwingen](concept-security-alerts.md) begrijpen en verkennen
