---
title: De Azure-portal gebruiken om het uploaden van bestanden te configureren | Microsoft Documenten
description: De Azure-portal gebruiken om uw IoT-hub te configureren om bestandsuploads vanaf verbonden apparaten in te schakelen. Bevat informatie over het configureren van het Azure-opslagaccount voor bestemming.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60735012"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Uploaden van IoT Hub-bestanden configureren met behulp van de Azure-portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Bestand uploaden

Als u de functionaliteit voor het uploaden van bestanden wilt gebruiken [in IoT Hub,](iot-hub-devguide-file-upload.md)moet u eerst een Azure Storage-account koppelen aan uw hub. Selecteer **Het uploaden** van bestanden om een lijst met eigenschappen voor het uploaden van bestanden weer te geven voor de IoT-hub die wordt gewijzigd.

![Instellingen voor het uploaden van IoT-hub-bestanden weergeven in de portal](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Opslagcontainer:** gebruik de Azure-portal om een blobcontainer in een Azure Storage-account in uw huidige Azure-abonnement te selecteren om te koppelen aan uw IoT Hub. Indien nodig u een Azure Storage-account maken op het blade **van opslagaccounts** en de blobcontainer op het **containerblad Containers.** IoT Hub genereert automatisch SAS-URI's met schrijfmachtigingen voor deze blobcontainer voor apparaten die kunnen worden gebruikt wanneer ze bestanden uploaden.

   ![Opslagcontainers weergeven voor het uploaden van bestanden in de portal](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Meldingen ontvangen voor geüploade bestanden:** Meldingen voor het uploaden van bestanden inschakelen of uitschakelen via de toggle.

* **SAS TTL**: Deze instelling is de time-to-live van de SAS URI's die door IoT Hub naar het apparaat worden teruggestuurd. Stel standaard in op een uur, maar kan met de schuifregelaar worden aangepast aan andere waarden.

* **Standaardttl-instellingen voor bestandsmeldingen:** de time-to-live van een melding voor het uploaden van bestanden voordat deze is verlopen. Stel standaard in op één dag, maar kan met de schuifregelaar worden aangepast aan andere waarden.

* **Maximale leveringshoeveelheid bestandsmeldingen:** het aantal keren dat de IoT Hub een melding voor het uploaden van bestanden probeert te leveren. Stel standaard in op 10, maar kan met de schuifregelaar worden aangepast aan andere waarden.

   ![Upload van IoT-hubbestanden configureren in de portal](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Bestanden uploaden vanaf een apparaat](iot-hub-devguide-file-upload.md) in de ontwikkelaarshandleiding voor IoT Hub voor meer informatie over de mogelijkheden voor het uploaden van bestanden van IoT Hub.

Volg deze koppelingen voor meer informatie over het beheer van Azure IoT Hub:

* [IoT-apparaten bulksgewijs beheren](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-statistieken](iot-hub-metrics.md)
* [Controle van bewerkingen](iot-hub-operations-monitoring.md)

Zie:

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Beveilig uw IoT-oplossing van de grond af aan](../iot-fundamentals/iot-security-ground-up.md)
