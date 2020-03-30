---
title: Aanpasbare beveiligingswaarschuwingshandleiding voor Azure Security Center voor IoT| Microsoft Documenten
description: Meer informatie over aanpasbare beveiligingswaarschuwingen en aanbevolen herstel met Azure Security Center voor IoT-functies en -service.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 32d79267559480de8ec91e5e66196bfd57e01cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299570"
---
# <a name="azure-security-center-for-iot-security-alerts"></a>Beveiligingswaarschuwingen in Azure Security Center for IoT

Azure Security Center for IoT analyseert uw IoT-oplossing voortdurend met behulp van geavanceerde analyses en bedreigingsinformatie om u te waarschuwen voor schadelijke activiteiten.

We raden u aan om aangepaste waarschuwingen te maken op basis van uw kennis van het verwachte apparaatgedrag om ervoor te zorgen dat waarschuwingen fungeren als de meest efficiënte indicatoren van potentieel compromis in uw unieke organisatie-implementatie en -landschap. 

De volgende lijst met Azure Security Center voor IoT-waarschuwingen is door u definieerbaar op basis van uw verwachte IoT-hub en/of apparaatgedrag. Zie [Aangepaste waarschuwingen maken voor](quickstart-create-custom-alerts.md)meer informatie over het aanpassen van elke waarschuwing.

## <a name="iot-hub-alerts-available-for-customization"></a>IoT Hub-waarschuwingen beschikbaar voor aanpassing 



| Severity | Naam van waarschuwing | Gegevensbron | Beschrijving | Voorgestelde herstel|
|---|---|---|---|---|
| Laag      | Aangepaste waarschuwing - aantal cloud-naar-apparaatberichten in het AMQP-protocol ligt buiten het toegestane bereik          | IoT Hub     | Het aantal cloud-to-device-berichten (AMQP-protocol) binnen een specifiek tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.||
| Laag      | Aangepaste waarschuwing - aantal geweigerde cloud- naar apparaatberichten in AMQP-protocol ligt buiten het toegestane bereik | IoT Hub     | Aantal cloud-to-device-berichten (AMQP-protocol) dat door het apparaat is geweigerd, binnen een specifiek tijdvenster zich buiten het momenteel geconfigureerde en toegestane bereik bevindt.||
| Laag      | Aangepaste waarschuwing - aantal apparaat-naar-cloudberichten in het AMQP-protocol ligt buiten het toegestane bereik      | IoT Hub     | De hoeveelheid apparaat naar cloudberichten (AMQP-protocol) binnen een specifiek tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.|   |
| Laag      | Aangepaste waarschuwing - aantal directe methodeaanroepen ligt buiten het toegestane bereik | IoT Hub     | De hoeveelheid directe methode die binnen een bepaald tijdvenster wordt aanroepen, bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.||
| Laag      | Aangepaste waarschuwing - aantal bestandsuploads ligt buiten het toegestane bereik | IoT Hub     | De hoeveelheid bestandsuploads binnen een specifiek tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.| |
| Laag      | Aangepaste waarschuwing - aantal cloud-naar-apparaatberichten in HTTP-protocol ligt buiten het toegestane bereik | IoT Hub     | De hoeveelheid cloud-to-device-berichten (HTTP-protocol) in een tijdvenster bevindt zich niet in het geconfigureerde toegestane bereik                                  |
| Laag      | Aangepaste waarschuwing - aantal geweigerde cloud- naar apparaatberichten in HTTP-protocol bevindt zich niet binnen het toegestane bereik | IoT Hub     | De hoeveelheid cloud-to-device-berichten (HTTP-protocol) binnen een specifiek tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik. |
| Laag      | Aangepaste waarschuwing - aantal apparaten voor cloudberichten in HTTP-protocol ligt buiten het toegestane bereik | IoT Hub| De hoeveelheid apparaat naar cloudberichten (HTTP-protocol) binnen een specifiek tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.|    |
| Laag      | Aangepaste waarschuwing - aantal cloud-naar-apparaatberichten in MQTT-protocol ligt buiten het toegestane bereik | IoT Hub     | De hoeveelheid cloud-to-device-berichten (MQTT-protocol) binnen een specifiek tijdvenster valt buiten het momenteel geconfigureerde en toegestane bereik.|   |
| Laag      | Aangepaste waarschuwing - aantal geweigerde cloud- naar apparaatberichten in MQTT-protocol ligt buiten het toegestane bereik | IoT Hub     | De hoeveelheid cloud-to-device-berichten (MQTT-protocol) die door het apparaat binnen een specifiek tijdvenster wordt geweigerd, bevindt zich buiten het momenteel geconfigureerde en toegestane bereik. |
| Laag      | Aangepaste waarschuwing - aantal apparaat-naar-cloudberichten in MQTT-protocol ligt buiten het toegestane bereik          | IoT Hub     | De hoeveelheid apparaat naar cloudberichten (MQTT-protocol) binnen een specifiek tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.|
| Laag      | Aangepaste waarschuwing - aantal zuiveringen van opdrachtwachtrijen ligt buiten het toegestane bereik                               | IoT Hub     | De hoeveelheid functies die wachtrijen binnen een bepaald tijdvenster wordt verwijderd, bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.||
| Laag      | Aangepaste waarschuwing - aantal moduledubbele updates ligt buiten het toegestane bereik                                       | IoT Hub     | De hoeveelheid moduledubbele updates binnen een specifiek tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.|
| Laag      | Aangepaste waarschuwing - aantal ongeautoriseerde bewerkingen ligt buiten het toegestane bereik  | IoT Hub     | De hoeveelheid ongeautoriseerde bewerkingen binnen een specifiek tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.|
|

## <a name="agent-alerts-available-for-customization"></a>Agentwaarschuwingen beschikbaar voor aanpassing 

| Severity | Naam van waarschuwing | Gegevensbron | Beschrijving | Voorgestelde herstel|
|---|---|---|---|---|
| Laag      | Aangepaste waarschuwing - aantal actieve verbindingen ligt buiten het toegestane bereik  | Agent       | Het aantal actieve verbindingen binnen een specifiek tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik.|  Onderzoek de apparaatlogboeken. Ontdek waar de verbinding vandaan komt en bepaal of deze goedaardig of kwaadaardig is. Als kwaadwillig, verwijder mogelijke malware en begrijpen bron. Als u goedaardig bent, voegt u de bron toe aan de toegestane verbindingslijst.  |
| Laag      | Aangepaste waarschuwing - uitgaande verbinding gemaakt met een IP die niet is toegestaan                             | Agent       | Er is een uitgaande verbinding gemaakt met een IP die buiten uw toegestane IP-lijst valt. |Onderzoek de apparaatlogboeken. Ontdek waar de verbinding vandaan komt en bepaal of deze goedaardig of kwaadaardig is. Als kwaadwillig, verwijder mogelijke malware en begrijpen bron. Als u goedaardig bent, voegt u de bron toe aan de toegestane IP-lijst.                        |
| Laag      | Aangepaste waarschuwing - aantal mislukte lokale aanmeldingen ligt buiten het toegestane bereik                               | Agent       | Het aantal mislukte lokale aanmeldingen binnen een bepaald tijdvenster bevindt zich buiten het momenteel geconfigureerde en toegestane bereik. |   |
| Laag      | Aangepaste waarschuwing - aanmelding van een gebruiker die niet op de toegestane gebruikerslijst staat | Agent       | Een lokale gebruiker buiten de toegestane gebruikerslijst die is aangemeld bij het apparaat.|  Als u ruwe gegevens opslaat, navigeert u naar uw logboekanalyseaccount en gebruikt u de gegevens om het apparaat te onderzoeken, identificeert u de bron en bevestigt u vervolgens de lijst toestaan/blokkeren voor deze instellingen. Als u momenteel geen ruwe gegevens opslaat, gaat u naar het apparaat en stelt u de lijst toestaan/blokkeren voor deze instellingen vast.|
| Laag      | Aangepaste waarschuwing - er is een proces uitgevoerd dat niet is toegestaan | Agent       | Een proces dat niet is toegestaan, is uitgevoerd op het apparaat. |Als u ruwe gegevens opslaat, navigeert u naar uw logboekanalyseaccount en gebruikt u de gegevens om het apparaat te onderzoeken, identificeert u de bron en bevestigt u vervolgens de lijst toestaan/blokkeren voor deze instellingen. Als u momenteel geen ruwe gegevens opslaat, gaat u naar het apparaat en stelt u de lijst toestaan/blokkeren voor deze instellingen vast.  |
|

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [aanpassen van een waarschuwing](quickstart-create-custom-alerts.md)
- Overzicht van Azure Security Center voor [IoT-service](overview.md)
- Meer informatie over hoe u [toegang krijgt tot uw beveiligingsgegevens](how-to-security-data-access.md)
- Meer informatie over [het onderzoeken van een apparaat](how-to-investigate-device.md)