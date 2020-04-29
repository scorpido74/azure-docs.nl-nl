---
title: Gebruik Azure IoT-Hulpprogram Ma's voor VSCode om IT hub Messa ging te beheren
description: Informatie over het gebruik van Azure IoT-Hulpprogram Ma's voor Visual Studio code voor het controleren van apparaten op Cloud berichten en het verzenden van Cloud naar apparaat-berichten in azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 31a5d55d1067b9dd946c1667118d0bde5ee3d59e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682502"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Gebruik Azure IoT-Hulpprogram Ma's voor Visual Studio code voor het verzenden en ontvangen van berichten tussen uw apparaat en IoT Hub

![End-to-end-diagram](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) is een handige Visual Studio code-extensie die het IOT hub beheer en IOT-toepassings ontwikkeling eenvoudiger maakt. In dit artikel wordt uitgelegd hoe u Azure IoT-Hulpprogram Ma's voor Visual Studio code gebruikt voor het verzenden en ontvangen van berichten tussen uw apparaat en uw IoT-hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Wat u leert

U leert hoe u Azure IoT-Hulpprogram Ma's voor Visual Studio code kunt gebruiken om apparaat-naar-Cloud-berichten te bewaken en Cloud-naar-apparaat-berichten te verzenden. Apparaat-naar-Cloud-berichten kunnen sensor gegevens zijn die uw apparaat verzamelt en vervolgens verzendt naar uw IoT-hub. Cloud-naar-apparaat-berichten kunnen opdrachten zijn die uw IoT-hub naar uw apparaat verzendt om een LED te laten knipen die is verbonden met uw apparaat.

## <a name="what-you-will-do"></a>Wat u moet doen

* Gebruik Azure IoT-Hulpprogram Ma's voor Visual Studio code om apparaat-naar-Cloud-berichten te bewaken.

* Gebruik Azure IoT-Hulpprogram Ma's voor Visual Studio code om Cloud-naar-apparaat-berichten te verzenden.

## <a name="what-you-need"></a>Wat u nodig hebt

* Een actief Azure-abonnement.

* Een Azure IoT hub onder uw abonnement.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IOT-Hulpprogram ma's voor VS code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) of kopieer en plak deze URL in een browser venster:`vscode:extension/vsciot-vscode.azure-iot-tools`

## <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan voor toegang tot uw IoT-hub

1. Vouw in de **Verkenner** -weer gave van VS code het gedeelte **Azure IOT Hub-apparaten** uit in de linkerbenedenhoek.

2. Klik op **IOT hub selecteren** in het context menu.

3. Er wordt een pop-upvenster weer gegeven in de rechter benedenhoek om u voor de eerste keer aan te melden bij Azure.

4. Nadat u zich hebt aangemeld, wordt de lijst met Azure-abonnementen weer gegeven en selecteert u vervolgens Azure-abonnement en IoT Hub.

5. De lijst met apparaten wordt weer gegeven in een paar seconden op het tabblad **apparaten van Azure IOT hub** .

   > [!Note]
   > U kunt de installatie ook voltooien door **IoT Hub-verbindingsreeks instellen** te kiezen. Voer de **iothubowner** -beleids Connection String in voor de IOT-hub waarmee uw IOT-apparaat verbinding maakt in het pop-upvenster.

## <a name="monitor-device-to-cloud-messages"></a>Apparaat-naar-Cloud-berichten bewaken

Voer de volgende stappen uit om berichten te bewaken die worden verzonden vanaf uw apparaat naar uw IoT hub:

1. Klik met de rechter muisknop op het apparaat en selecteer **controle van het ingebouwde gebeurtenis-eind punt starten**.

2. De bewaakte berichten worden weer gegeven in de weer gave **uitvoer** > van**Azure IOT hub** .

3. Als u de bewaking wilt stoppen, klikt u met de rechter muisknop op de **uitvoer** weergave en selecteert u **controle stoppen ingebouwd gebeurtenis eindpunt**.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Voer de volgende stappen uit om een bericht van uw IoT-hub naar uw apparaat te verzenden:

1. Klik met de rechter muisknop op het apparaat en selecteer **C2D-bericht naar apparaat verzenden**.

2. Voer het bericht in het invoervak in.

3. De resultaten worden weer gegeven in de weer gave **uitvoer** > van**Azure IOT hub** .

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u apparaat-naar-Cloud-berichten kunt bewaken en Cloud-naar-apparaat-berichten kunt verzenden tussen uw IoT-apparaat en Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]