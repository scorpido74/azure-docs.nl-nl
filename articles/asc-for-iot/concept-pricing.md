---
title: Prijs en bijbehorende kosten
description: Meer informatie over de kosten die zijn gekoppeld aan Azure Security Center voor IoT en hoe u deze beheren.
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311568"
---
# <a name="pricing-and-associated-costs"></a>Prijs en bijbehorende kosten

In dit artikel wordt uitgelegd hoe azure security center voor IoT-prijsmodel, een overzicht van alle bijbehorende kosten en wordt uitgelegd hoe u deze beheren.

## <a name="pricing"></a>Prijzen

Het Azure Security Center for IoT-prijsmodel bestaat uit twee delen en wordt gefactureerd zodra een IoT-hub is [ingeschakeld](quickstart-onboard-iot-hub.md) in Azure Security Center for IoT:

- Kosten per apparaat - ingebouwde beveiligingsmogelijkheden op basis van analyse van IoT Hub-logboeken.

- Kosten per bericht - verbeterde beveiligingsmogelijkheden op basis van beveiligingsberichten van IoT Edge- of leaf-apparaten.

Zie [De prijzen van het Beveiligingscentrum](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie .

## <a name="associated-costs"></a>Bijbehorende kosten

Azure Security Center for IoT heeft bijbehorende kosten, die geen deel uitmaken van de directe prijzen:

- Opslagkosten log Analytics

U de bijbehorende kosten verlagen door u af te melden voor bepaalde oplossingsfuncties. Opt-out door uw instellingen te wijzigen.

Ga als instellen om uw instellingen te wijzigen:

1. Open IoT Hub.

1. Klik **onder Beveiliging**op **Overzicht**.

1. Klik op **Instellingen**.

In de volgende tabel vindt u een overzicht van de bijbehorende kosten en implicaties van elke optie.

|     | Gebruik | Opmerking |
| --- | --- | --- |
| **Log Analytics-opslag** |  |
| Apparaataanbeveling en waarschuwingen| Beveiligingsaanbeveling en waarschuwingen gegenereerd door de service | Niet optioneel |
| Ruwe beveiligingsgegevens| Ruwe beveiligingsgegevens van IoT-apparaten, verzameld door beveiligingsagenten | Beveiligingsgebeurtenissen _voor apparaten_ voor winkels uitschakelen |
|

>[!Important]
> Opt-out heeft ernstige gevolgen voor de beschikbaarheid van Azure Security Center voor IoT-beveiligingsfuncties.

| Afmelden | Gevolgen |
| --- | --- |
| _Dubbele metagegevensverzameling_ | [Aangepaste waarschuwingen uitschakelen](quickstart-create-custom-alerts.md) |
| | Manifestaanbevelingen van IoT Edge uitschakelen |
| | Aanbevelingen en waarschuwingen op basis van apparaatidentiteit uitschakelen |
| _Beveiligingsgebeurtenissen voor raw-apparaten opslaan_ | Details over basislijnaanbevelingen van apparaatbesturingssysteem zijn niet beschikbaar |
| | Details over [waarschuwings-](concept-security-alerts.md) en [aanbevelingsonderzoeken](concept-recommendations.md) zijn niet beschikbaar |
|

## <a name="see-also"></a>Zie ook

- Toegang tot uw [ruwe beveiligingsgegevens](how-to-security-data-access.md)
- [Een apparaat onderzoeken](how-to-investigate-device.md)
- [Beveiligingsaanbevelingen](concept-recommendations.md) begrijpen en verkennen
- [Beveiligingswaarschuwingen](concept-security-alerts.md) begrijpen en verkennen
