---
title: 'Snelstart: aangepaste waarschuwingen maken voor Azure Security Center voor IoT'
description: Aangepaste apparaatwaarschuwingen voor de Beveiligingsservice azure Security Center voor IoT begrijpen, maken en toewijzen.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 063e5c9e7d75fd1c07d148c265b1fe64eee3cbc8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78303525"
---
# <a name="quickstart-create-custom-alerts"></a>Snelstart: aangepaste waarschuwingen maken


Met behulp van aangepaste beveiligingsgroepen en waarschuwingen, maakt optimaal gebruik van de end-to-end beveiligingsinformatie en categorische apparaatkennis om een betere beveiliging te garanderen in uw IoT-oplossing. 

## <a name="why-use-custom-alerts"></a>Waarom aangepaste waarschuwingen gebruiken? 

Je kent je IoT-apparaten het beste.

Voor klanten die hun verwachte apparaatgedrag volledig begrijpen, u met Azure Security Center for IoT dit inzicht vertalen naar een beleid voor apparaatgedrag en waarschuwen voor elke afwijking van verwacht, normaal gedrag.

## <a name="security-groups"></a>Beveiligingsgroepen

Met beveiligingsgroepen u logische groepen apparaten definiëren en hun beveiligingsstatus op een gecentraliseerde manier beheren.

Deze groepen kunnen apparaten vertegenwoordigen met specifieke hardware, apparaten die op een bepaalde locatie zijn geïmplementeerd of een andere groep die geschikt is voor uw specifieke behoeften.

Beveiligingsgroepen worden gedefinieerd door een eigenschap van de dubbele tag van het apparaat met de naam **SecurityGroup**. Standaard heeft elke IoT-oplossing op IoT Hub één beveiligingsgroep met de naam **standaard.** Wijzig de waarde van de eigenschap **SecurityGroup** om de beveiligingsgroep van een apparaat te wijzigen.
 
Bijvoorbeeld:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

Gebruik beveiligingsgroepen om uw apparaten te groeperen in logische categorieën. Nadat u de groepen hebt gemaakt, wijst u ze toe aan de aangepaste waarschuwingen van uw keuze, voor de meest effectieve end-to-end IoT-beveiligingsoplossing. 

## <a name="customize-an-alert"></a>Een waarschuwing aanpassen

1. Open je IoT Hub. 
2. Klik **op Aangepaste waarschuwingen** in de sectie **Beveiliging.** 
3. Kies een beveiligingsgroep waarop u de aanpassing wilt toepassen. 
4. Klik **op Een aangepaste waarschuwing toevoegen**.
5. Selecteer een aangepaste waarschuwing in de vervolgkeuzelijst. 
6. Bewerk de vereiste eigenschappen en klik op **OK**.
7. Klik op **OPSLAAN**. Zonder de nieuwe waarschuwing op te slaan, wordt de waarschuwing verwijderd wanneer u de volgende keer IoT Hub sluit.

 
## <a name="alerts-available-for-customization"></a>Waarschuwingen beschikbaar voor aanpassing

Azure Security Center for IoT biedt een groot aantal waarschuwingen die kunnen worden aangepast aan uw specifieke behoeften. Bekijk de [aanpasbare waarschuwingstabel](concept-customizable-security-alerts.md) op de ernst van de waarschuwing, gegevensbron, beschrijving en onze voorgestelde herstelstappen als en wanneer elke waarschuwing wordt ontvangen. 


## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om te leren hoe u een beveiligingsagent implementeert...

> [!div class="nextstepaction"]
> [Een beveiligingsagent implementeren](how-to-deploy-agent.md)
