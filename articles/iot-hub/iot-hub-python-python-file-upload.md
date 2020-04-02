---
title: Bestanden uploaden van apparaten naar Azure IoT Hub met Python | Microsoft Documenten
description: Bestanden uploaden van een apparaat naar de cloud met Azure IoT-apparaat SDK voor Python. Geüploade bestanden worden opgeslagen in een Azure-opslagblobcontainer.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.openlocfilehash: 706e1920c6c4fe39e885fd3f5a631070545509ee
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529292"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Bestanden uploaden van uw apparaat naar de cloud met IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

In dit artikel ziet u hoe u de [mogelijkheden voor het uploaden van bestanden van IoT Hub](iot-hub-devguide-file-upload.md) gebruiken om een bestand te uploaden naar Azure [blob-opslag.](../storage/index.yml) In deze zelfstudie leert u het volgende:

* Zorg veilig voor een opslagcontainer voor het uploaden van een bestand.

* Gebruik de Python-client om een bestand te uploaden via uw IoT-hub.

De [quickstart van telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-python.md) toont de basisfunctionaliteit van IoT-hub voor apparaat naar cloud. In sommige scenario's u de gegevens die uw apparaten verzenden echter niet eenvoudig in kaart brengen in de relatief kleine device-to-cloudberichten die IoT Hub accepteert. Wanneer u bestanden vanaf een apparaat moet up-landen, u nog steeds de beveiliging en betrouwbaarheid van IoT Hub gebruiken.

Aan het einde van deze zelfstudie voert u de Python-console-app uit:

* **FileUpload.py**, die een bestand uploadt naar opslag met behulp van de Python Device SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie maakt u de apparaat-app om een bestand te uploaden naar de IoT-hub.

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **azure-iot-apparaatpakket** te installeren. U gebruikt dit pakket om het uploaden van bestanden te coördineren met uw IoT-hub.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Voer bij de opdrachtprompt de volgende opdracht uit om het [**azure.storage.blob-pakket**](https://pypi.org/project/azure-storage-blob/) te installeren. U gebruikt dit pakket om het uploaden van bestanden uit te voeren.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Maak een testbestand dat u uploadt naar blob-opslag.

1. Maak met een teksteditor een **FileUpload.py** bestand in uw werkmap.

1. Voeg de `import` volgende instructies en variabelen toe aan het begin van het **FileUpload.py** bestand.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. Vervang `[Device Connection String]` in uw bestand de verbindingstekenreeks van uw IoT-hubapparaat. Vervang `[Full path to local file]` het pad naar het testbestand dat u hebt gemaakt of een bestand op uw apparaat dat u wilt uploaden.

1. Maak een functie om het bestand te uploaden naar blob-opslag:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Met deze functie wordt de *blob_info* structuur die erin wordt doorgegeven om een URL te maken die wordt gebruikt om een [azure.storage.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python)te initialiseren. Vervolgens wordt uw bestand met deze client geüpload naar Azure blob-opslag.

1. Voeg de volgende code toe om de client te verbinden en het bestand te uploaden:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Deze code maakt een asynchrone **IoTHubDeviceClient** en gebruikt de volgende API's om het uploaden van bestanden met uw IoT-hub te beheren:

    * **get_storage_info_for_blob** informatie krijgt van uw IoT-hub over het gekoppelde opslagaccount dat u eerder hebt gemaakt. Deze informatie bevat de hostnaam, containernaam, blobnaam en een SAS-token. De opslaggegevens worden doorgegeven aan de **functie store_blob** (gemaakt in de vorige stap), zodat de **BlobClient** in die functie kan verifiëren met Azure-opslag. De **get_storage_info_for_blob-methode** retourneert ook een correlation_id, die wordt gebruikt in de **notify_blob_upload_status-methode.** De correlation_id is de manier waarop IoT Hub markeert aan welke blob u werkt.

    * **notify_blob_upload_status** waarschuwt IoT Hub over de status van uw blob-opslagbewerking. U passeert het de correlation_id verkregen door de **get_storage_info_for_blob** methode. Het wordt gebruikt door IoT Hub om elke service die mogelijk luistert naar een melding over de status van de bestandsuploadtaak te melden.

1. Sla het **UploadFile.py-bestand** op en sluit deze.

## <a name="run-the-application"></a>De toepassing uitvoeren

Nu bent u klaar om de toepassing uit te voeren.

1. Voer bij een opdrachtprompt in uw werkmap de volgende opdracht uit:

    ```cmd/sh
    python FileUpload.py
    ```

2. In de volgende schermafbeelding wordt de uitvoer van de **FileUpload-app** weergegeven:

    ![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. U de portal gebruiken om het geüploade bestand weer te geven in de opslagcontainer die u hebt geconfigureerd:

    ![Geüpload bestand](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de mogelijkheden voor het uploaden van bestanden van IoT Hub gebruiken om het uploaden van bestanden vanaf apparaten te vereenvoudigen. Met de volgende artikelen u de functies en scenario's van de IoT-hub blijven verkennen:

* [Een IoT-hub programmatisch maken](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK’s voor Azure IoT](iot-hub-devguide-sdks.md)

Meer informatie over Azure Blob Storage met de volgende koppelingen:

* [Azure Blob-opslagdocumentatie](https://docs.microsoft.com/azure/storage/blobs/)

* [Azure Blob Storage voor Python API-documentatie](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
