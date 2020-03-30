---
title: Bestanden uploaden van apparaten naar Azure IoT Hub met Python | Microsoft Documenten
description: Bestanden uploaden van een apparaat naar de cloud met Azure IoT-apparaat SDK voor Python. Geüploade bestanden worden opgeslagen in een Azure-opslagblobcontainer.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110745"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Bestanden uploaden van uw apparaat naar de cloud met IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

In dit artikel ziet u hoe u de [mogelijkheden voor het uploaden van bestanden van IoT Hub](iot-hub-devguide-file-upload.md) gebruiken om een bestand te uploaden naar Azure [blob-opslag.](../storage/index.yml) In deze zelfstudie leert u het volgende:

* Zorg veilig voor een opslagcontainer voor het uploaden van een bestand.

* Gebruik de Python-client om een bestand te uploaden via uw IoT-hub.

De [quickstart van telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-python.md) toont de basisfunctionaliteit van IoT-hub voor apparaat naar cloud. In sommige scenario's u de gegevens die uw apparaten verzenden echter niet eenvoudig in kaart brengen in de relatief kleine device-to-cloudberichten die IoT Hub accepteert. Wanneer u bestanden vanaf een apparaat moet up-landen, u nog steeds de beveiliging en betrouwbaarheid van IoT Hub gebruiken.

> [!NOTE]
> IoT Hub Python SDK ondersteunt momenteel alleen het uploaden van op tekens gebaseerde bestanden zoals **.txt-bestanden.**

Aan het einde van deze zelfstudie voer je de Python-console-app uit:

* **FileUpload.py**, die een bestand uploadt naar opslag met behulp van de Python Device SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Deze handleiding maakt gebruik van de afgeschafte V1 Python SDK, omdat de functie Bestandsupload nog niet is geïmplementeerd in de nieuwe V2 SDK.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie maakt u de apparaat-app om een bestand te uploaden naar de IoT-hub.

1. Voer bij uw opdrachtprompt de volgende opdracht uit om het **azure-iothub-device-client-clientpakket** te installeren:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Maak met behulp van een teksteditor een testbestand dat u uploadt naar blobopslag.

    > [!NOTE]
    > IoT Hub Python SDK ondersteunt momenteel alleen het uploaden van op tekens gebaseerde bestanden zoals **.txt-bestanden.**

3. Maak met een teksteditor een **FileUpload.py** bestand in uw werkmap.

4. Voeg de `import` volgende instructies en variabelen toe aan het begin van het **FileUpload.py** bestand. 

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

5. Vervang `[Device Connection String]` in uw bestand de verbindingstekenreeks van uw IoT-hubapparaat. Vervang `[Full path to file]` het pad naar het testbestand dat u hebt gemaakt of een bestand op uw apparaat dat u wilt uploaden. Vervang `[File name for storage]` de naam die u aan uw bestand wilt geven nadat het is geüpload naar blobopslag. 

6. Een callback maken voor de **functie upload_blob:**

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Voeg de volgende code toe om de client te verbinden en het bestand te uploaden. Ook de `main` routine:

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

8. Sla het **UploadFile.py-bestand** op en sluit deze.

## <a name="run-the-application"></a>De toepassing uitvoeren

Nu bent u klaar om de toepassing uit te voeren.

1. Voer bij een opdrachtprompt in uw werkmap de volgende opdracht uit:

    ```cmd/sh
    python FileUpload.py
    ```

2. In de volgende schermafbeelding wordt de uitvoer van de **FileUpload-app** weergegeven:

    ![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-python-python-file-upload/1.png)

3. U de portal gebruiken om het geüploade bestand weer te geven in de opslagcontainer die u hebt geconfigureerd:

    ![Geüpload bestand](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de mogelijkheden voor het uploaden van bestanden van IoT Hub gebruiken om het uploaden van bestanden vanaf apparaten te vereenvoudigen. Met de volgende artikelen u de functies en scenario's van de IoT-hub blijven verkennen:

* [Een IoT-hub programmatisch maken](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK’s voor Azure IoT](iot-hub-devguide-sdks.md)
