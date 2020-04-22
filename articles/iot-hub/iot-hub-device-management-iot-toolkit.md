---
title: Azure IoT-apparaatbeheer met Azure IoT-hulpprogramma's voor VSCode
description: Gebruik de Azure IoT-hulpprogramma's voor Visual Studio Code voor Azure IoT Hub-apparaatbeheer, met de Direct-methoden en de gewenste opties voor het beheer van eigenschappen van de Tweeling.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: d85e0e967dd802a77ccbc11b884d7a9f2891524d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688103"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Azure IoT-hulpprogramma's gebruiken voor Visual Studio Code voor Azure IoT Hub-apparaatbeheer

![End-to-end diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) is een handige Visual Studio Code-extensie die iot-hubbeheer en iot-toepassingsontwikkeling eenvoudiger maakt. Het wordt geleverd met beheeropties die u gebruiken om verschillende taken uit te voeren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Beheeroptie          | Taak                    |
|----------------------------|--------------------------------|
| Directe methoden             | Laat een apparaat handelen, zoals het starten of stoppen van het verzenden van berichten of het opnieuw opstarten van het apparaat.                                        |
| Apparaattwee lezen           | De gerapporteerde status van een apparaat opteweeren. Het apparaat meldt bijvoorbeeld dat de LED nu knippert.                                    |
| Apparaattweeling bijwerken         | Plaats een apparaat in bepaalde toestanden, zoals het instellen van een LED op groen of het instellen van het telemetrie-verzendinterval op 30 minuten.         |
| Cloud-to-device berichten   | Meldingen verzenden naar een apparaat. Bijvoorbeeld: "De kans is groot dat het vandaag gaat regenen. Vergeet niet een paraplu mee te nemen."              |

Zie [Device-to-cloud communicatiebegeleiding](iot-hub-devguide-d2c-guidance.md) en [communicatiebegeleiding](iot-hub-devguide-c2d-guidance.md)tussen apparaten naar de cloud voor meer gedetailleerde uitleg over de verschillen en richtlijnen voor het gebruik van deze opties.

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat (metagegevens, configuraties en voorwaarden) zijn opgeslagen. IoT Hub blijft bestaan voor elk apparaat dat er verbinding mee maakt. Zie Aan de slag [met apparaattweelingen](iot-hub-node-node-twin-getstarted.md)voor meer informatie over apparaattweelingen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Wat u leert

U leert azure IoT-hulpprogramma's voor visuele studiocode gebruiken met verschillende beheeropties op uw ontwikkelingsmachine.

## <a name="what-you-do"></a>Wat je doet

Voer Azure IoT-hulpprogramma's voor Visual Studio-code uit met verschillende beheeropties.

## <a name="what-you-need"></a>Wat u nodig hebt

* Een actief Azure-abonnement.
* Een Azure IoT-hub onder uw abonnement.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-hulpprogramma's voor VS-code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) of kopieer`vscode:extension/vsciot-vscode.azure-iot-tools`deze URL en plak deze in een browservenster: .

## <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan om toegang te krijgen tot uw IoT-hub

1. Vouw in **de Explorer-weergave** van VS-code de sectie **Azure IoT Hub-apparaten** linksonder uit.

2. Klik **op IoT-hub selecteren** in het contextmenu.

3. In de rechterbenedenhoek wordt een pop-up weergegeven waarmee u zich voor de eerste keer aanmelden bij Azure.

4. Nadat u zich hebt aangemeld, wordt uw Azure-abonnementslijst weergegeven en selecteert u Azure-abonnement en IoT-hub.

5. De lijst met apparaten wordt in een paar seconden weergegeven op het tabblad **Azure IoT Hub-apparaten.**

   > [!Note]
   > U kunt de installatie ook voltooien door **IoT Hub-verbindingsreeks instellen** te kiezen. Voer de beleidsverbindingstekenreeks **iothub-verbinding** in voor de IoT-hub waarmee uw IoT-apparaat verbinding maakt in het pop-upvenster.

## <a name="direct-methods"></a>Directe methoden

1. Klik met de rechtermuisknop op uw apparaat en selecteer **Directe methode aanroepen**. 

2. Voer de naam en het laadvermogen van de methode in het invoervak in.

3. De resultaten worden weergegeven in de**weergave Azure IoT Hub voor** **uitvoer.** > 

## <a name="read-device-twin"></a>Apparaattwee lezen

1. Klik met de rechtermuisknop op uw apparaat en selecteer **Apparaattweeling bewerken**. 

2. Een **azure-iot-device-twin.json-bestand** wordt geopend met de inhoud van apparaattweeling.

## <a name="update-device-twin"></a>Apparaattweeling bijwerken

1. Maak een aantal bewerkingen van **tags** of **eigenschappen.gewenst** veld.

2. Klik met de rechtermuisknop op het **azure-iot-device-twin.json-bestand.**

3. Selecteer **Apparaat twee bijwerken** om de apparaattweeling bij te werken.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Voer de volgende stappen uit om een bericht van uw IoT-hub naar uw apparaat te verzenden:
 
1. Klik met de rechtermuisknop op uw apparaat en selecteer **C2D-bericht verzenden naar apparaat**. 

2. Voer het bericht in het invoervak in.

3. De resultaten worden weergegeven in de**weergave Azure IoT Hub voor** **uitvoer.** > 

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u de extensie Azure IoT Tools voor Visual Studio Code gebruiken met verschillende beheeropties.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
