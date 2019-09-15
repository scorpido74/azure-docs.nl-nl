---
title: Bestanden van apparaten uploaden naar Azure IoT Hub met python | Microsoft Docs
description: Het uploaden van bestanden van een apparaat naar de Cloud met behulp van Azure IoT Device SDK voor python. Geüploade bestanden worden opgeslagen in een Azure Storage-BLOB-container.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 6dfbcc7a3e76842546326742d801c913451855f3
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001128"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub (python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

In dit artikel wordt beschreven hoe u de functies voor het [uploaden van bestanden van IOT hub](iot-hub-devguide-file-upload.md) gebruikt om een bestand te uploaden naar [Azure Blob Storage](../storage/index.yml). In deze zelfstudie leert u het volgende:

* Een opslag container veilig bieden voor het uploaden van een bestand.

* Gebruik de python-client om een bestand te uploaden via uw IoT-hub.

Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-python.md) Quick Start toont de basis functionaliteit voor het uitwisselen van apparaten van IOT hub. In sommige gevallen kunt u de gegevens die uw apparaten verzenden echter niet eenvoudig toewijzen aan de relatief kleine apparaat-naar-Cloud-berichten die IoT Hub accepteren. Wanneer u bestanden van een apparaat nodig hebt, kunt u nog steeds gebruikmaken van de beveiliging en betrouw baarheid van IoT Hub.

> [!NOTE]
> IoT Hub python SDK ondersteunt momenteel alleen het uploaden van op tekens gebaseerde bestanden, zoals **txt** -bestanden.

Aan het einde van deze zelf studie voert u de python-console-app uit:

* **FileUpload.py**, dat een bestand uploadt naar Storage met behulp van de python-apparaat-SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> In deze hand leiding wordt gebruikgemaakt van de afgeschafte v1 python SDK, omdat de functie voor het uploaden van bestanden nog niet is geïmplementeerd in de nieuwe v2-SDK.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie maakt u de app apparaat om een bestand te uploaden naar IoT hub.

1. Voer bij de opdracht prompt de volgende opdracht uit om het **Azure-iothub-Device-client-** pakket te installeren:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Maak met behulp van een tekst editor een test bestand dat u naar de Blob-opslag wilt uploaden.

    > [!NOTE]
    > IoT Hub python SDK ondersteunt momenteel alleen het uploaden van op tekens gebaseerde bestanden, zoals **txt** -bestanden.

3. Maak met behulp van een tekst editor een **FileUpload.py** -bestand in de werkmap.

4. Voeg de volgende `import` instructies en variabelen toe aan het begin van het **FileUpload.py** -bestand. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. Vervang `[Device Connection String]` in het bestand door de Connection String van uw IOT hub-apparaat. Vervang `[Full path to file]` door het pad naar het test bestand dat u hebt gemaakt of een bestand op het apparaat dat u wilt uploaden. Vervang `[File name for storage]` door de naam die u wilt toewijzen aan het bestand nadat het is geüpload naar de Blob-opslag. 

6. Een call back maken voor de functie **upload_blob** :

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Voeg de volgende code toe om verbinding te maken met de client en het bestand te uploaden. Neem ook de `main` routine op:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Sla het **UploadFile.py** -bestand op en sluit het.

## <a name="run-the-application"></a>De toepassing uitvoeren

U bent nu klaar om de toepassing uit te voeren.

1. Voer bij een opdracht prompt in de werkmap de volgende opdracht uit:

    ```cmd/sh
    python FileUpload.py
    ```

2. Op de volgende scherm afbeelding ziet u de uitvoer van de app **fileupload** :

    ![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-python-python-file-upload/1.png)

3. U kunt de portal gebruiken om het geüploade bestand weer te geven in de opslag container die u hebt geconfigureerd:

    ![Bestand geüpload](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de functies voor het uploaden van bestanden van IoT Hub kunt gebruiken om Bestands uploads van apparaten te vereenvoudigen. U kunt IoT hub-functies en-scenario's blijven verkennen met de volgende artikelen:

* [Een IoT-hub maken via een programma](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK's voor Azure IoT](iot-hub-devguide-sdks.md)
