---
title: 'Snelstartgids: aangepaste waarschuwingen voor Azure Security Center voor IoT maken'
description: In deze Quick Start kunt u aangepaste waarschuwingen voor Azure Security Center voor IoT maken en toewijzen.
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
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: eca5d69efb04cf8210b0b2aa502bcee5cd4f5264
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904125"
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
2. Klik op **aangepaste waarschuwingen** in het gedeelte **beveiliging** . 
3. Kies een beveiligings groep waarop u de aanpassing wilt Toep assen. 
4. Klik op **een aangepaste waarschuwing toevoegen**.
5. Selecteer een aangepaste waarschuwing in de vervolg keuzelijst. 
6. Bewerk de vereiste eigenschappen, klik op **OK**.
7. Zorg ervoor dat u op **Opslaan**klikt. Zonder de nieuwe waarschuwing op te slaan, wordt de waarschuwing verwijderd wanneer u de volgende keer IoT Hub sluit.

 
## <a name="alerts-available-for-customization"></a>Waarschuwingen die beschikbaar zijn voor aanpassing

De volgende tabel bevat een overzicht van de waarschuwingen die beschikbaar zijn voor aanpassing.


| Severity | Naam | Gegevens bron | Beschrijving | Voorgestelde herstel|
|---|---|---|---|---|
| Laag      | Aangepaste waarschuwing: aantal berichten van de Cloud naar het apparaat in het AMQP-protocol valt buiten het toegestane bereik          | IoT Hub     | Het aantal Cloud-naar-apparaat-berichten (AMQP-Protocol) binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik.||
| Laag      | Aangepaste waarschuwing: aantal afgewezen Cloud-naar-apparaat-berichten in het AMQP-protocol valt buiten het toegestane bereik | IoT Hub     | Het aantal Cloud-naar-apparaat-berichten (AMQP-Protocol) dat door het apparaat is afgewezen, binnen een bepaald tijd venster valt buiten het huidige geconfigureerde en toegestane bereik.||
| Laag      | Aangepaste waarschuwing: aantal Cloud berichten in AMQP-protocol valt buiten het toegestane bereik      | IoT Hub     | De hoeveelheid apparaat naar Cloud berichten (AMQP-Protocol) binnen een bepaald tijd venster valt buiten het huidige geconfigureerde en toegestane bereik.|   |
| Laag      | Aangepaste waarschuwing: aantal aanroepen van directe methode valt buiten het toegestane bereik | IoT Hub     | De hoeveelheid directe methode aanroepen binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik.||
| Laag      | Aangepaste waarschuwing: aantal uploads van bestanden valt buiten het toegestane bereik | IoT Hub     | De hoeveelheid uploads van bestanden binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik.| |
| Laag      | Aangepaste waarschuwing: aantal berichten van de Cloud naar het apparaat in het HTTP-protocol ligt buiten het toegestane bereik | IoT Hub     | De hoeveelheid Cloud-naar-apparaat-berichten (HTTP-protocol) in een tijd venster bevindt zich niet in het geconfigureerde toegestane bereik                                  |
| Laag      | Aangepaste waarschuwing: aantal geweigerde Cloud-naar-apparaat-berichten in HTTP-protocol bevindt zich niet in het toegestane bereik | IoT Hub     | De hoeveelheid Cloud-naar-apparaat-berichten (HTTP-protocol) binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |
| Laag      | Aangepaste waarschuwing: aantal van het apparaat naar Cloud berichten in het HTTP-protocol valt buiten het toegestane bereik | IoT Hub| De hoeveelheid apparaat naar Cloud berichten (HTTP-protocol) binnen een bepaald tijd venster valt buiten het huidige geconfigureerde en toegestane bereik.|    |
| Laag      | Aangepaste waarschuwing: aantal berichten van de Cloud naar het apparaat in het MQTT-protocol valt buiten het toegestane bereik | IoT Hub     | De hoeveelheid Cloud-naar-apparaat-berichten (MQTT-Protocol) binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik.|   |
| Laag      | Aangepaste waarschuwing: aantal afgewezen Cloud-naar-apparaat-berichten in het MQTT-protocol valt buiten het toegestane bereik | IoT Hub     | De hoeveelheid Cloud-naar-apparaat-berichten (MQTT-Protocol) die binnen een bepaald tijd venster van het apparaat worden afgewezen, ligt buiten het huidige geconfigureerde en toegestane bereik. |
| Laag      | Aangepaste waarschuwing: aantal Cloud berichten in MQTT-protocol valt buiten het toegestane bereik          | IoT Hub     | De hoeveelheid apparaat naar Cloud berichten (MQTT-Protocol) binnen een bepaald tijd venster valt buiten het huidige geconfigureerde en toegestane bereik.|
| Laag      | Aangepaste waarschuwing: het aantal opschoningen van de opdracht wachtrij valt buiten het toegestane bereik                               | IoT Hub     | De hoeveelheid leegmaak opdrachten van de opdracht wachtrij binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik.||
| Laag      | Aangepaste waarschuwing: aantal dubbele updates van module valt buiten het toegestane bereik                                       | IoT Hub     | De hoeveelheid module dubbele updates binnen een specifiek tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik.|
| Laag      | Aangepaste waarschuwing: aantal niet-geautoriseerde bewerkingen valt buiten het toegestane bereik  | IoT Hub     | De hoeveelheid niet-geautoriseerde bewerkingen binnen een bepaald tijd venster bevindt zich buiten het huidige geconfigureerde en toegestane bereik.|
| Laag      | Aangepaste waarschuwing: aantal actieve verbindingen valt buiten het toegestane bereik  | Agent       | Het aantal actieve verbindingen binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik.|  De logboeken van het apparaat onderzoeken. Ontdek waar de verbinding vandaan komt en bepaal of het goed is of kwaad aardig is. Als dat schadelijk is, verwijdert u mogelijke malware en begrijpt u de bron. Voeg, indien goed aardig, de bron toe aan de lijst met toegestane verbindingen.  |
| Laag      | Aangepaste waarschuwing: uitgaande verbinding die is gemaakt met een IP-adres dat niet is toegestaan                             | Agent       | Er is een uitgaande verbinding gemaakt met een IP-adres buiten de lijst met toegestane IP-adressen. |De logboeken van het apparaat onderzoeken. Ontdek waar de verbinding vandaan komt en bepaal of het goed is of kwaad aardig is. Als dat schadelijk is, verwijdert u mogelijke malware en begrijpt u de bron. Voeg, indien goed aardig, de bron toe aan de lijst met toegestane IP-adressen.                        |
| Laag      | Aangepaste waarschuwing: aantal mislukte lokale aanmeldingen valt buiten het toegestane bereik                               | Agent       | De hoeveelheid mislukte lokale aanmeldingen binnen een bepaald tijd venster ligt buiten het huidige geconfigureerde en toegestane bereik. |   |
| Laag      | Aangepaste waarschuwing: aanmelding van een gebruiker die niet voor komt in de lijst met toegestane gebruikers | Agent       | Een lokale gebruiker buiten de lijst met toegestane gebruikers die is aangemeld bij het apparaat.|  Als u onbewerkte gegevens opslaat, navigeert u naar uw log Analytics-account en gebruikt u de gegevens om het apparaat te onderzoeken, identificeert u de bron en verhelpt u de lijst voor toestaan/blok keren voor deze instellingen. Als u momenteel geen onbewerkte gegevens opslaat, gaat u naar het apparaat en herstelt u de lijst met toegestane en geblokkeerde websites.|
| Laag      | Aangepaste waarschuwing: er is een proces uitgevoerd dat niet is toegestaan | Agent       | Een proces dat niet is toegestaan, is uitgevoerd op het apparaat. |Als u onbewerkte gegevens opslaat, navigeert u naar uw log Analytics-account en gebruikt u de gegevens om het apparaat te onderzoeken, identificeert u de bron en verhelpt u de lijst voor toestaan/blok keren voor deze instellingen. Als u momenteel geen onbewerkte gegevens opslaat, gaat u naar het apparaat en herstelt u de lijst met toegestane en geblokkeerde websites.  |
|


## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het implementeren van een beveiligings agent...

> [!div class="nextstepaction"]
> [Een beveiligings agent implementeren](how-to-deploy-agent.md)
