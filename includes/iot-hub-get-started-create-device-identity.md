---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78893098"
---
In deze sectie gebruikt u de Azure CLI om een apparaat-id voor dit artikel te maken. Apparaat-id's zijn hoofdlettergevoelig.

1. Open [Azure Cloud shell](https://shell.azure.com/).

1. Voer in Azure Cloud Shell de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI te installeren:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Maak een nieuwe apparaat-id `myDeviceId` met de naam en haal het apparaat Connection String op met de volgende opdrachten:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Noteer het apparaat connection string van het resultaat. Dit apparaat connection string wordt gebruikt door de apparaat-app om verbinding te maken met uw IoT Hub als een apparaat.

<!-- images and links -->
