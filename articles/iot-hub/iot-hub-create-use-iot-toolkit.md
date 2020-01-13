---
title: Een Azure-IoT Hub maken met Azure IoT-Hulpprogram Ma's voor VS code | Microsoft Docs
description: Informatie over het gebruik van de Azure IoT-hulpprogram ma's voor Visual Studio code voor het maken van een Azure IoT-hub in een resource groep.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912253"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Een IoT-hub maken met de Azure IoT-Hulpprogram Ma's voor Visual Studio code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In dit artikel leest u hoe u de [Azure IOT-Hulpprogram ma's voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) gebruikt om een Azure IOT hub te maken. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U hebt het volgende nodig om dit artikel te volt ooien:

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) voor Visual Studio Code.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

1. Open in Visual Studio Code, de **Explorer** weergeven.

2. Vouw aan de onderkant van de Verkenner van de **Azure IoT Hub-apparaten** sectie. 

   ![Azure IoT Hub-apparaten uitvouwen](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Klik op de **...**  in de **Azure IoT Hub-apparaten** sectiekop. Als u het beletselteken niet ziet, Beweeg de muisaanwijzer over de header. 

4. Kies **IOT hub maken**.

5. Er wordt een pop-upvenster weer gegeven in de rechter benedenhoek om u voor de eerste keer aan te melden bij Azure.

6. Selecteer Azure-abonnement. 

7. Selecteer een resource groep.

8. Selecteer een locatie.

9. Selecteer de prijs categorie.

10. Voer een wereld wijd unieke naam in voor uw IoT Hub.

11. Wacht enkele minuten totdat de IoT Hub is gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nu hebt u een IoT-hub geïmplementeerd met de Azure IoT-Hulpprogram Ma's voor Visual Studio code. Bekijk de volgende artikelen om verder te verkennen:

* [Gebruik de Azure IOT-Hulpprogram ma's voor Visual Studio code voor het verzenden en ontvangen van berichten tussen uw apparaat en een IOT hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Gebruik de Azure IoT-Hulpprogram Ma's voor Visual Studio code voor Azure IoT Hub Apparaatbeheer](iot-hub-device-management-iot-toolkit.md)

* [Zie de pagina Azure IOT hub for VS code wiki](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
