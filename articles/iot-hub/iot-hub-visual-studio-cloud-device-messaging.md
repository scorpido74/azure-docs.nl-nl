---
title: VS Cloud Explorer gebruiken om berichten over Azure IoT Hub-apparaten te beheren
description: Meer informatie over het gebruik van Cloud Explorer voor Visual Studio om het apparaat te monitoren voor cloudberichten en cloudberichten te verzenden naar apparaatberichten in Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079479"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Cloud Explorer gebruiken voor Visual Studio om berichten tussen uw apparaat en IoT Hub te verzenden en te ontvangen

![End-to-end diagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) is een handige Visual Studio-extensie waarmee u uw Azure-bronnen bekijken, hun eigenschappen inspecteren en belangrijke ontwikkelaarsacties uitvoeren vanuit Visual Studio. In dit artikel wordt gespitst op het verzenden en ontvangen van berichten tussen uw apparaat en uw hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Wat u leert

In dit artikel leert u hoe u Cloud Explorer voor Visual Studio gebruiken om device-to-cloudberichten te controleren en berichten van cloud naar apparaat te verzenden. Device-to-cloud-berichten kunnen sensorgegevens zijn die uw apparaat verzamelt en vervolgens naar uw IoT-hub verzendt. Cloud-to-device-berichten kunnen opdrachten zijn die uw IoT Hub naar uw apparaat verzendt. Knipper bijvoorbeeld met een LED die is aangesloten op uw apparaat.

## <a name="what-you-do"></a>Wat je doet

In dit artikel doet u de volgende taken:

- Gebruik Cloud Explorer voor Visual Studio om berichten van apparaat tot cloud te controleren.

- Gebruik Cloud Explorer voor Visual Studio om berichten van cloud naar apparaat te verzenden.

## <a name="what-you-need"></a>Wat u nodig hebt

U hebt de volgende voorwaarden nodig:

- Een actief Azure-abonnement.

- Een Azure IoT Hub onder uw abonnement.

- Microsoft Visual Studio 2017 Update 9 of hoger. Dit artikel maakt gebruik van [Visual Studio 2019](https://www.visualstudio.com/vs/).

- De component Cloud Explorer van Visual Studio Installer, die standaard is geselecteerd met Azure-werkbelasting.

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer bijwerken naar nieuwste versie

De Component Cloud Explorer van Visual Studio Installer voor Visual Studio 2017 ondersteunt alleen het bewaken van berichten tussen apparaten en cloud-to-device. Als u Visual Studio 2017 wilt gebruiken, downloadt en installeert u de nieuwste [Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Meld u aan om toegang te krijgen tot uw hub

Voer de volgende stappen uit om toegang te krijgen tot uw hub:

1. Selecteer**cloudverkenner** **in** > Visual Studio om Cloud Explorer te openen.

1. Selecteer het pictogram Accountbeheer om uw abonnementen weer te geven.

    ![Pictogram Accountbeheer](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Als u bent aangemeld bij Azure, worden uw accounts weergegeven. Als u zich voor de eerste keer wilt aanmelden bij Azure, kiest **u Een account toevoegen**.

1. Selecteer de Azure-abonnementen die u wilt gebruiken en kies **Toepassen**.

1. Breid uw abonnement uit en vouw **vervolgens IoT-hubs**uit.  Onder elke hub u uw apparaten voor die hub zien.

    ![Apparaatlijst](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Device-to-cloudberichten bewaken

Voer de volgende stappen uit om berichten te controleren die van uw apparaat naar uw IoT-hub worden verzonden:

1. Klik met de rechtermuisknop op uw IoT-hub of -apparaat en selecteer **D2C-bericht met bewaken starten**.

    ![D2C-bericht controleren starten](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. De bewaakte berichten worden weergegeven onder **Uitvoer**.

    ![Resultaat van D2C-berichten controleren](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Als u de bewaking wilt stoppen, klikt u met de rechtermuisknop op een IoT-hub of -apparaat en selecteert u **D2C-bericht stoppen**.

## <a name="send-cloud-to-device-messages"></a>Cloud-naar-apparaat-berichten verzenden

Voer de volgende stappen uit om een bericht van uw IoT-hub naar uw apparaat te verzenden:

1. Klik met de rechtermuisknop op uw apparaat en selecteer **C2D-bericht verzenden**.

1. Voer het bericht in het invoervak in.

    ![C2D-bericht verzenden](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Resultaten worden weergegeven onder **Uitvoer**.

    ![Resultaat van C2D-berichten verzenden](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u device-to-cloud-berichten controleren en cloud-to-device-berichten verzenden tussen uw IoT-apparaat en Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]