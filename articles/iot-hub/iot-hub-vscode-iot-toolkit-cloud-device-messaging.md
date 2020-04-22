---
title: Azure IoT-hulpprogramma's voor VSCode gebruiken om IT Hub-berichten te beheren
description: Meer informatie over het gebruik van Azure IoT Tools voor Visual Studio Code om device naar cloudberichten te monitoren en cloudberichten naar apparaatberichten in Azure IoT Hub te verzenden.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 31a5d55d1067b9dd946c1667118d0bde5ee3d59e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682502"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Azure IoT-hulpprogramma's voor Visual Studio-code gebruiken om berichten tussen uw apparaat en IoT Hub te verzenden en te ontvangen

![End-to-end diagram](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) is een handige Visual Studio Code-extensie die iot-hubbeheer en iot-toepassingsontwikkeling eenvoudiger maakt. In dit artikel wordt aandacht gegeven aan het verzenden en ontvangen van berichten tussen uw apparaat en uw IoT-hub met Azure IoT-hulpprogramma's voor Visual Studio-code.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Wat je leert

U leert hoe u Azure IoT Tools voor Visual Studio Code gebruiken om device-to-cloudberichten te controleren en berichten van cloud naar apparaat te verzenden. Device-to-cloud-berichten kunnen sensorgegevens zijn die uw apparaat verzamelt en vervolgens naar uw IoT-hub verzendt. Cloud-to-device-berichten kunnen opdrachten zijn die uw IoT-hub naar uw apparaat stuurt om een LED te knipperen die is verbonden met uw apparaat.

## <a name="what-you-will-do"></a>Wat u gaat doen

* Gebruik Azure IoT-hulpprogramma's voor Visual Studio-code om device-to-cloudberichten te controleren.

* Gebruik Azure IoT-hulpprogramma's voor Visual Studio-code om berichten van cloud naar apparaat te verzenden.

## <a name="what-you-need"></a>Wat u nodig hebt

* Een actief Azure-abonnement.

* Een Azure IoT-hub onder uw abonnement.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT-hulpprogramma's voor VS-code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) of kopiëren en plakken deze in een browservenster:`vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan om toegang te krijgen tot uw IoT-hub

1. Vouw in **de Explorer-weergave** van VS-code de sectie **Azure IoT Hub-apparaten** linksonder uit.

2. Klik **op IoT-hub selecteren** in het contextmenu.

3. In de rechterbenedenhoek wordt een pop-up weergegeven waarmee u zich voor de eerste keer aanmelden bij Azure.

4. Nadat u zich hebt aangemeld, wordt uw Azure-abonnementslijst weergegeven en selecteert u Azure-abonnement en IoT-hub.

5. De lijst met apparaten wordt in een paar seconden weergegeven op het tabblad **Azure IoT Hub-apparaten.**

   > [!Note]
   > U kunt de installatie ook voltooien door **IoT Hub-verbindingsreeks instellen** te kiezen. Voer de beleidsverbindingstekenreeks **iothub-verbinding** in voor de IoT-hub waarmee uw IoT-apparaat verbinding maakt in het pop-upvenster.

## <a name="monitor-device-to-cloud-messages"></a>Device-to-cloudberichten bewaken

Voer de volgende stappen uit om berichten te controleren die van uw apparaat naar uw IoT-hub worden verzonden:

1. Klik met de rechtermuisknop op uw apparaat en selecteer **Het ingebouwde gebeurteniseindpunt starten**.

2. De bewaakte berichten worden weergegeven in de**weergave AZURE IoT Hub** **uitvoer.** > 

3. Als u de bewaking wilt stoppen, klikt u met de rechtermuisknop op de **uitvoerweergave** en selecteert **u Ingebouwde gebeurteniseindpunt stoppen**.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Voer de volgende stappen uit om een bericht van uw IoT-hub naar uw apparaat te verzenden:

1. Klik met de rechtermuisknop op uw apparaat en selecteer **C2D-bericht verzenden naar apparaat**.

2. Voer het bericht in het invoervak in.

3. De resultaten worden weergegeven in de**weergave Azure IoT Hub voor** **uitvoer.** > 

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u device-to-cloud-berichten controleren en cloud-to-device-berichten verzenden tussen uw IoT-apparaat en Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]