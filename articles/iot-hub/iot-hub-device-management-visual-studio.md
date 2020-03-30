---
title: Azure IoT-apparaatbeheer w/ Visual Studio Cloud Explorer
description: Gebruik de Cloud Explorer voor Visual Studio voor Azure IoT Hub-apparaatbeheer, met de Direct-methoden en de gewenste opties voor het beheer van eigenschappen van de Tweeling.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953186"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Cloud Explorer gebruiken voor Visual Studio voor Azure IoT Hub-apparaatbeheer

![End-to-end diagram](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) is een handige Visual Studio-extensie waarmee u uw Azure-bronnen bekijken, hun eigenschappen inspecteren en belangrijke ontwikkelaarsacties uitvoeren vanuit Visual Studio. Het wordt geleverd met beheeropties die u gebruiken om verschillende taken uit te voeren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Beheeroptie          | Taak                    |
|----------------------------|--------------------------------|
| Directe methoden             | Laat een apparaat handelen, zoals het starten of stoppen van het verzenden van berichten of het opnieuw opstarten van het apparaat.                                        |
| Apparaattwee lezen           | De gerapporteerde status van een apparaat opteweeren. Het apparaat meldt bijvoorbeeld dat de LED nu knippert.                                    |
| Apparaattweeling bijwerken         | Plaats een apparaat in bepaalde toestanden, zoals het instellen van een LED op groen of het instellen van het telemetrie-verzendinterval op 30 minuten.         |
| Cloud-to-device berichten   | Meldingen verzenden naar een apparaat. Bijvoorbeeld: "De kans is groot dat het vandaag gaat regenen. Vergeet niet een paraplu mee te nemen."              |

Zie [Device-to-cloud communicatiebegeleiding](iot-hub-devguide-d2c-guidance.md) en [communicatiebegeleiding](iot-hub-devguide-c2d-guidance.md)tussen apparaten naar de cloud voor meer gedetailleerde uitleg over de verschillen en richtlijnen voor het gebruik van deze opties.

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat zijn opgeslagen, zoals metagegevens, configuraties en voorwaarden. IoT Hub blijft bestaan voor elk apparaat dat er verbinding mee maakt. Zie Aan de slag [met apparaattweelingen](iot-hub-node-node-twin-getstarted.md)voor meer informatie over apparaattweelingen.

## <a name="what-you-learn"></a>Wat u leert

In dit artikel leert u hoe u de Cloud Explorer voor Visual Studio gebruiken met verschillende beheeropties op uw ontwikkelcomputer.

## <a name="what-you-do"></a>Wat je doet

Voer in dit artikel Cloud Explorer voor Visual Studio uit met verschillende beheeropties.

## <a name="what-you-need"></a>Wat u nodig hebt

U hebt de volgende voorwaarden nodig:

- Een actief Azure-abonnement.

- Een Azure IoT Hub onder uw abonnement.

- Microsoft Visual Studio 2017 Update 9 of hoger. Dit artikel maakt gebruik van [Visual Studio 2017 of Visual Studio 2019](https://www.visualstudio.com/vs/).

- Cloud Explorer-component van Visual Studio Installer, die standaard is geselecteerd met Azure Workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer bijwerken naar nieuwste versie

De Component Cloud Explorer van Visual Studio Installer voor Visual Studio 2017 ondersteunt alleen het bewaken van berichten tussen apparaten en cloud-to-device. Als u Visual Studio 2017 wilt gebruiken, downloadt en installeert u de nieuwste [Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Meld u aan om toegang te krijgen tot uw hub

1. Selecteer**cloudverkenner** **in** > Visual Studio om Cloud Explorer te openen.

1. Selecteer het pictogram Accountbeheer om uw abonnementen weer te geven.

    ![Pictogram Accountbeheer](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Als u bent aangemeld bij Azure, worden uw accounts weergegeven. Als u zich voor de eerste keer wilt aanmelden bij Azure, kiest **u Een account toevoegen**.

1. Selecteer de Azure-abonnementen die u wilt gebruiken en kies **Toepassen**.

1. Breid uw abonnement uit en vouw **vervolgens IoT-hubs**uit.  Onder elke hub u uw apparaten voor die hub zien. Klik met de rechtermuisknop op één apparaat om toegang te krijgen tot de beheeropties.

    ![Beheeropties](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Directe methoden

Ga als volgt te werk om directe methoden te gebruiken:

1. Klik met de rechtermuisknop op uw apparaat en selecteer **Apparaatdirect gebruiken .**

1. Voer de naam en het laadvermogen van de methode in **De directe methode aanroepen**in en selecteer **OK**.

    Resultaten worden weergegeven in **Uitvoer**.

## <a name="update-device-twin"></a>Apparaattweeling bijwerken

Ga als volgt te werk om een apparaattweeling te bewerken:

1. Klik met de rechtermuisknop op uw apparaat en selecteer **Apparaattweeling bewerken**.

   Een **azure-iot-device-twin.json-bestand** wordt geopend met de inhoud van apparaattweeling.

1. Maak enkele bewerkingen van **tags** of **eigenschappen.gewenste** velden aan het **azure-iot-device-twin.json-bestand.**

1. Druk op **Ctrl+S** om de apparaattweeling bij te werken.

   Resultaten worden weergegeven in **Uitvoer**.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Voer de volgende stappen uit om een bericht van uw IoT-hub naar uw apparaat te verzenden:

1. Klik met de rechtermuisknop op uw apparaat en selecteer **C2D-bericht verzenden**.

1. Voer het bericht in **C2D-bericht verzenden** in en selecteer **OK**.

   Resultaten worden weergegeven in **Uitvoer**.

## <a name="next-steps"></a>Volgende stappen

Je hebt geleerd hoe je Cloud Explorer gebruiken voor Visual Studio met verschillende beheeropties.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
