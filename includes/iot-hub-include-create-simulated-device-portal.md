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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67176657"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Maak vervolgens een apparaat-id en sla de bijbehorende sleutel op voor later gebruik. Deze apparaat-id wordt gebruikt door de simulatietoepassing om berichten naar de IoT Hub te verzenden. Deze mogelijkheid is niet beschikbaar in PowerShell of wanneer u een Azure Resource Manager-sjabloon gebruikt. In de volgende stappen ziet u hoe u het gesimuleerde apparaat maakt met behulp van de [Azure-portal.](https://portal.azure.com)

1. Open [Azure Portal](https://portal.azure.com) en meld u aan bij uw Azure-account.

2. Selecteer **Resourcegroepen** en kies vervolgens uw resourcegroep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

3. Selecteer in de lijst met bronnen uw IoT-hub. In deze zelfstudie wordt gebruikgemaakt van **ContosoTestHub**. Selecteer **IoT-apparaten** in het deelvenster Hub.

4. Selecteer **+ Toevoegen**. Vul in het deelvenster Apparaat toevoegen de apparaat-id in. In deze zelfstudie wordt gebruikgemaakt van **Contoso-testapparaat**. Laat het item voor sleutels leeg en schakel het selectievakje **Automatisch sleutels genereren** in. Zorg ervoor dat **Apparaat verbinden met IoT Hub** is ingeschakeld. Selecteer **Opslaan**.

   ![Het scherm met extra apparaten](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Nu het is gemaakt, selecteert u het apparaat om de gegenereerde sleutels te zien. Selecteer het pictogram Kopiëren op de primaire sleutel en sla het ergens op, zoals Kladblok voor de testfase van deze zelfstudie.

   ![De details van het apparaat, inclusief de toetsen](./media/iot-hub-include-create-simulated-device-portal/device-details.png)