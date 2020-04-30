---
title: 'Snelstartgids: aangepaste waarschuwingen maken'
description: Meer informatie over het maken en toewijzen van waarschuwingen voor aangepaste apparaten voor de Azure Security Center voor IoT-beveiligings service.
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
ms.openlocfilehash: 8ba81b669c25d91efa8ad1f07b1a7835b42c5cf4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310834"
---
# <a name="quickstart-create-custom-alerts"></a>Snelstartgids: aangepaste waarschuwingen maken

Met aangepaste beveiligings groepen en waarschuwingen kunt u optimaal profiteren van de end-to-end beveiligings informatie en categorische op het apparaat om te zorgen voor een betere beveiliging van uw IoT-oplossing.

## <a name="why-use-custom-alerts"></a>Waarom aangepaste waarschuwingen gebruiken?

U kent het beste uw IoT-apparaten.

Voor klanten die hun verwachte gedrag van het apparaat begrijpen, kunt u met Azure Security Center voor IoT deze kennis vertalen in een gedrags beleid voor apparaten en een waarschuwing over eventuele afwijkingen van verwacht, normaal gedrag.

## <a name="security-groups"></a>Beveiligingsgroepen

Met beveiligings groepen kunt u logische groepen apparaten definiëren en hun beveiligings status op een gecentraliseerde manier beheren.

Deze groepen kunnen apparaten vertegenwoordigen met specifieke hardware, apparaten die zijn geïmplementeerd op een bepaalde locatie of een andere groep die geschikt is voor uw specifieke behoeften.

Beveiligings groepen worden gedefinieerd door een eigenschap voor een dubbele label voor een apparaat met de naam **beveiligings groep**. Elke IoT-oplossing op IoT Hub heeft standaard één beveiligings groep met de naam **standaard**. Wijzig de waarde van de eigenschap **beveiligings groep** om de beveiligings groep van een apparaat te wijzigen.

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

Gebruik beveiligings groepen om uw apparaten in logische categorieën te groeperen. Nadat u de groepen hebt gemaakt, wijst u deze toe aan de aangepaste waarschuwingen van uw keuze voor de meest efficiënte end-to-end IoT-beveiligings oplossing.

## <a name="customize-an-alert"></a>Een waarschuwing aanpassen

1. Open uw IoT Hub.
1. Klik op **aangepaste waarschuwingen** in het gedeelte **beveiliging** .
1. Kies een beveiligings groep waarop u de aanpassing wilt Toep assen.
1. Klik op **een aangepaste waarschuwing toevoegen**.
1. Selecteer een aangepaste waarschuwing in de vervolg keuzelijst.
1. Bewerk de vereiste eigenschappen, klik op **OK**.
1. Zorg ervoor dat u op **Opslaan**klikt. Zonder de nieuwe waarschuwing op te slaan, wordt de waarschuwing verwijderd wanneer u de volgende keer IoT Hub sluit.

## <a name="alerts-available-for-customization"></a>Waarschuwingen die beschikbaar zijn voor aanpassing

Azure Security Center voor IoT biedt een groot aantal waarschuwingen dat kan worden aangepast op basis van uw specifieke behoeften. Bekijk de [aanpas bare waarschuwings tabel](concept-customizable-security-alerts.md) voor de ernst van de waarschuwing, de gegevens bron, de beschrijving en de voorgestelde herstel stappen als en wanneer elke waarschuwing wordt ontvangen.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het implementeren van een beveiligings agent...

> [!div class="nextstepaction"]
> [Een beveiligings agent implementeren](how-to-deploy-agent.md)
