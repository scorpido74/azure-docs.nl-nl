---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67176657"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Maak vervolgens een apparaat-id en sla de bijbehorende sleutel op voor later gebruik. Deze apparaat-id wordt gebruikt door de simulatietoepassing om berichten naar de IoT Hub te verzenden. Deze mogelijkheid is niet beschikbaar in Power shell of bij het gebruik van een Azure Resource Manager sjabloon. De volgende stappen laten zien hoe u het gesimuleerde apparaat maakt met behulp van de [Azure Portal](https://portal.azure.com).

1. Open [Azure Portal](https://portal.azure.com) en meld u aan bij uw Azure-account.

2. Selecteer **resource groepen** en kies vervolgens de resource groep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

3. Selecteer uw IoT-hub in de lijst met resources. In deze zelfstudie wordt gebruikgemaakt van **ContosoTestHub**. Selecteer **IoT-apparaten** in het deelvenster Hub.

4. Selecteer **+ Toevoegen**. Vul in het deelvenster Apparaat toevoegen de apparaat-id in. In deze zelfstudie wordt gebruikgemaakt van **Contoso-testapparaat**. Laat het item voor sleutels leeg en schakel het selectievakje **Automatisch sleutels genereren** in. Zorg ervoor dat **Apparaat verbinden met IoT Hub** is ingeschakeld. Selecteer **Opslaan**.

   ![Het scherm voor het toevoegen van apparaten](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Nu het is gemaakt, selecteert u het apparaat om de gegenereerde sleutels weer te geven. Selecteer het Kopieer pictogram op de primaire sleutel en sla het ergens op als klad blok voor de test fase van deze zelf studie.

   ![De details van het apparaat, inclusief de sleutels](./media/iot-hub-include-create-simulated-device-portal/device-details.png)