---
title: Registreren van een nieuw apparaat vanuit Azure portal - Azure IoT Edge | Microsoft Docs
description: De Azure portal gebruiken voor een nieuwe IoT Edge-apparaat registreren en de verbindingsreeks op te halen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7f3d0037bcf0fd33ae23c298679e3157046247cb
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983526"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registreer een nieuwe Azure IoT Edge-apparaat vanuit de Azure-portal

Voordat u uw IoT-apparaten met Azure IoT Edge gebruiken kunt, moet u hen registreert bij uw IoT-hub. Zodra u een apparaat registreert, ontvangt u een connection string dat kan worden gebruikt om uw apparaat in te stellen voor IoT Edge werk belastingen.

In dit artikel bevat informatie over het registreren van een nieuwe IoT Edge-apparaat met behulp van de Azure portal.

## <a name="prerequisites"></a>Vereisten

Een gratis of standaard [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.

## <a name="create-a-device"></a>Een apparaat maken

IoT Edge-apparaten zijn gemaakt en afzonderlijk beheerd vanaf apparaten die verbinding maken met uw IoT-hub, maar niet met het edge-functionaliteit in de Azure-portal.

1. Aanmelden bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.
2. Selecteer **IoT Edge** in het menu.
3. Selecteer **een IOT edge apparaat toevoegen**.
4. Geef een beschrijvende apparaat-ID. Gebruik de standaard instellingen voor het automatisch genereren van verificatie sleutels en het verbinden van het nieuwe apparaat met uw hub.
5. Selecteer **Opslaan**.

## <a name="view-all-devices"></a>Alle apparaten weergeven

Alle de edge-apparaten die verbinding met uw IoT-hub maken worden weergegeven op de **IoT Edge** pagina.

## <a name="retrieve-the-connection-string"></a>De verbindingsreeks ophalen

Wanneer u klaar bent om uw apparaat instellen, moet u de verbindingsreeks die is gekoppeld aan uw fysieke apparaat met de identiteit van de IoT-hub.

1. Klik op de pagina **IOT Edge** in de portal op de apparaat-id in de lijst met IOT edge apparaten.
2. Kopieer de waarde van een van beide **verbindingsreeks (primaire sleutel)** of **verbindingsreeks (secundaire sleutel)** .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules op een apparaat met de Azure Portal](how-to-deploy-modules-portal.md).
