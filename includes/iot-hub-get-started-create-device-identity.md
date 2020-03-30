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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78893098"
---
In deze sectie gebruikt u de Azure CLI om een apparaatidentiteit voor dit artikel te maken. Apparaat-id's zijn hoofdlettergevoelig.

1. Azure [Cloud Shell openen](https://shell.azure.com/).

1. Voer in Azure Cloud Shell de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI te installeren:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Maak een nieuwe `myDeviceId` apparaatidentiteit genaamd en haal de tekenreeks voor apparaatverbinding met deze opdrachten op:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Noteer de tekenreeks van de apparaatverbinding vanaf het resultaat. Deze tekenreeks voor apparaatverbinding wordt door de apparaat-app gebruikt om verbinding te maken met uw IoT-hub als apparaat.

<!-- images and links -->
