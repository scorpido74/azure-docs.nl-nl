---
title: Een Azure IoT-hub maken met Azure IoT-hulpprogramma's voor VS-code | Microsoft Documenten
description: Meer informatie over het gebruik van de Azure IoT-hulpprogramma's voor Visual Studio Code om een Azure IoT-hub in een brongroep te maken.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912253"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Een IoT-hub maken met de Azure IoT-hulpprogramma's voor visual studiocode

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In dit artikel ziet u hoe u de [Azure IoT-hulpprogramma's voor Visual Studio-code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) gebruiken om een Azure IoT-hub te maken. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om dit artikel te voltooien, heb je het volgende nodig:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- [Visual Studio-code](https://code.visualstudio.com/)

- [Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor Visual Studio-code.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

1. Open in Visual Studio Code de **explorer-weergave.**

2. Vouw onder aan de Explorer de sectie **Azure IoT Hub-apparaten** uit. 

   ![Azure IoT-hubapparaten uitbreiden](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Klik op de sectiekop **van** **De Azure IoT Hub-apparaten.** Als u de ellips niet ziet, zweeft u over de kop. 

4. Kies **IoT-hub maken**.

5. In de rechterbenedenhoek wordt een pop-up weergegeven waarmee u zich voor de eerste keer aanmelden bij Azure.

6. Selecteer Azure-abonnement. 

7. Selecteer resourcegroep.

8. Selecteer locatie.

9. Selecteer de prijscategorie.

10. Voer een wereldwijd unieke naam in voor uw IoT-hub.

11. Wacht een paar minuten tot de IoT Hub is gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nu hebt u een IoT-hub geïmplementeerd met behulp van de Azure IoT-hulpprogramma's voor Visual Studio-code. Ga als volgt te werk om verder te gaan, bekijk de volgende artikelen:

* [Gebruik de Azure IoT-hulpprogramma's voor Visual Studio-code om berichten tussen uw apparaat en een IoT-hub te verzenden en te ontvangen.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

* [De Azure IoT-hulpprogramma's voor Visual Studio-code gebruiken voor Azure IoT Hub-apparaatbeheer](iot-hub-device-management-iot-toolkit.md)

* [Zie de wikipagina azure IoT Hub for VS Code](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
