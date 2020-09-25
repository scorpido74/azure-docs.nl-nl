---
title: 'Quickstart: Aangepaste waarschuwingen maken'
description: Aangepaste apparaatwaarschuwingen voor de Azure Defender for IoT-beveiligingsservice begrijpen, maken en toewijzen.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 48682e465374c1a0e1fb74fc6627016696ff6d2c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945079"
---
# <a name="quickstart-create-custom-alerts"></a>Quickstart: Aangepaste waarschuwingen maken

Met aangepaste beveiligingsgroepen en waarschuwingen kunt u optimaal profiteren van de end-to-end beveiligingsinformatie en categorische apparaatkennis om te zorgen voor een betere beveiliging van uw IoT-oplossing.

## <a name="why-use-custom-alerts"></a>Waarom moet u aangepaste waarschuwingen maken?

U kent uw IoT-apparaten het beste.

Voor klanten die het verwachte gedrag van hun apparaten volledig kennen, kan Azure Defender for IoT deze kennis vertalen in een gedragsbeleid voor apparaten en waarschuwingen genereren wanneer van dit verwachte, normale gedrag wordt afgeweken.

## <a name="security-groups"></a>Beveiligingsgroepen

Met beveiligingsgroepen kunt u logische groepen van apparaten definiëren en hun beveiligingsstatus op een gecentraliseerde wijze beheren.

Deze groepen staan voor apparaten met specifieke hardware, apparaten die zijn geïmplementeerd op een bepaalde locatie of een andere groep die past bij uw specifieke behoeften.

Beveiligingsgroepen worden gedefinieerd door een eigenschap voor apparaatdubbeltags met de naam **SecurityGroup**. Elke IoT-oplossing in IoT Hub bevat standaard één beveiligingsgroep met de naam **Standaard**. Wijzig de waarde van de eigenschap **SecurityGroup** om de beveiligingsgroep van een apparaat te wijzigen.

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

Gebruik beveiligingsgroepen om uw apparaten in logische categorieën te groeperen. Nadat u de groepen hebt gemaakt, wijst u deze toe aan de aangepaste waarschuwingen van uw keuze voor de meest efficiënte end-to-end IoT-beveiligingsoplossing.

## <a name="customize-an-alert"></a>Een waarschuwing aanpassen

1. Open uw Azure IoT-hub en selecteer **Instellingen** in het menu **Beveiliging**. 
1. Klik op **Aangepaste waarschuwingen**.
1. Kies een beveiligingsgroep waarop u de aanpassing wilt toepassen.
1. Klik op **Een aangepaste waarschuwing toevoegen**.
1. Selecteer een aangepaste waarschuwing in de vervolgkeuzelijst.
1. Bewerk de gewenste eigenschappen, klik op **OK**.
1. Zorg ervoor dat u op **Opslaan** klikt. Wanneer u de nieuwe waarschuwing niet opslaat, wordt de waarschuwing verwijderd wanneer u de volgende keer IoT Hub sluit.

## <a name="alerts-available-for-customization"></a>Waarschuwingen die beschikbaar zijn voor aanpassing

Defender for IoT biedt een groot aantal waarschuwingen dat kan worden aangepast op basis van uw specifieke behoeften. Bekijk de [tabel met aanpasbare waarschuwingen](concept-customizable-security-alerts.md) voor de ernst van de waarschuwing, de gegevensbron, de beschrijving en onze voorgestelde herstelstappen als en wanneer elke waarschuwing wordt ontvangen.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om te lezen hoe u een beveiligingsagent implementeert...

> [!div class="nextstepaction"]
> [Een beveiligingsagent implementeren](how-to-deploy-agent.md)
