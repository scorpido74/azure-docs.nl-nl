---
title: Een apparaat genereren connection string voor Azure IoT Central | Microsoft Docs
description: Hoe kan ik als ontwikkel aars van een apparaat een connection string genereren voor apparaten die verbinding moeten maken met een IoT Central-toepassing?
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019783"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Een apparaat genereren connection string om verbinding te maken met een Azure IoT Central-toepassing

In dit artikel wordt beschreven hoe als ontwikkel aars van een apparaat een connection string kunnen genereren voor apparaten die verbinding moeten maken met een IoT Central toepassing. De procedure in dit artikel laat u zien hoe u snel één apparaat verbindt met behulp van een Shared Access Signature (SAS). Deze aanpak is nuttig wanneer u experimenteert met IoT Central of apparaten testen. Zie [connectiviteit van apparaten in Azure IOT Central](concepts-connectivity.md)voor alternatieve benaderingen voor gebruik in een productie omgeving.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

- Een Azure IoT Central-toepassing. Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
- Een ontwikkelings machine waarop de [node. js](https://nodejs.org/) -versie 8.0.0 of hoger is geïnstalleerd. U kunt uitvoeren `node --version` op de opdracht regel om uw versie te controleren. Node.js is beschikbaar voor een groot aantal verschillende besturingssystemen.

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

In de volgende stappen wordt beschreven hoe u de informatie krijgt die u nodig hebt om een SAS-connection string te genereren voor een apparaat:

1. Zoek in het **device Explorer**het apparaat dat u wilt verbinden met uw toepassing:

    ![Een echt apparaat selecteren](media/howto-generate-connection-string/real-devices.png)

1. Selecteer op de pagina **apparaat** de optie **verbinding maken**:

    ![Verbinding maken selecteren](media/howto-generate-connection-string/connect.png)

1. Noteer de verbindings gegevens, de **Scope-** id, de **apparaat-id**en de **primaire sleutel**van het apparaat om de volgende stappen uit te voeren:

    ![Verbindingsdetails](media/howto-generate-connection-string/device-connect.png)

    U kunt de waarden van deze pagina kopiëren om op te slaan.

## <a name="generate-the-connection-string"></a>De connection string genereren

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u een connection string hebt gegenereerd voor een echt apparaat om verbinding te maken met uw Azure IoT Central-toepassing, kunt u de volgende stappen volgen:

* [Een DevKit-apparaat voorbereiden en aansluiten (C)](howto-connect-devkit.md)
* [Een Raspberry PI (python) voorbereiden en er verbinding mee maken](howto-connect-raspberry-pi-python.md)
* [Een Raspberry PI (C#) voorbereiden en verbinden](howto-connect-raspberry-pi-csharp.md)
* [Een Windows 10 IoT core-apparaat (C#) voorbereiden en verbinden](howto-connect-windowsiotcore.md)
* [Een generieke Node.js-client verbinden met uw Azure IoT Central-toepassing](howto-connect-nodejs.md)