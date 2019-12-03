---
title: 'Zelf studie: apparaatgegevens verzenden via transparante gateway-Machine Learning op Azure IoT Edge'
description: Deze zelf studie laat zien hoe u uw ontwikkel computer als een gesimuleerd IoT Edge apparaat kunt gebruiken om gegevens te verzenden naar de IoT Hub door een apparaat te passeren dat is geconfigureerd als een transparante gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 50f339b257110f0a5dc0ac08b9f40043ee384afb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706912"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Zelf studie: gegevens verzenden via een transparante gateway

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks in dit artikel hebt gearriveerd, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks voor de beste resultaten.

In dit artikel gebruiken we de ontwikkel machine opnieuw als een gesimuleerd apparaat, maar in plaats van gegevens rechtstreeks naar de IoT Hub verzenden het apparaat gegevens verzendt naar het IoT Edge apparaat geconfigureerd als een transparante gateway.

We controleren de werking van het IoT Edge apparaat terwijl het gesimuleerde apparaat gegevens verzendt. Zodra het apparaat is voltooid, bekijken we de gegevens in het opslag account om alles te valideren wat naar verwachting werkt.

Deze stap wordt doorgaans uitgevoerd door een ontwikkelaar van de Cloud of het apparaat.

## <a name="review-device-harness"></a>Apparaat-harnas controleren

Het [DeviceHarness-project](tutorial-machine-learning-edge-03-generate-data.md) opnieuw gebruiken om het downstream-of Leaf-apparaat te simuleren. Voor het maken van verbinding met de transparante gateway zijn twee extra dingen vereist:

* Registreer het certificaat om het downstream-apparaat te maken (in dit geval onze ontwikkel computer) vertrouwt de certificerings instantie die wordt gebruikt door de IoT Edge runtime.
* Voeg de Edge-gateway Fully Qualified Domain Name (FQDN) toe aan het apparaat connection string.

Bekijk de code om te zien hoe deze twee items worden geïmplementeerd.

1. Open Visual Studio code op uw ontwikkel machine.

2. Gebruik **bestand** > **map openen...** om C:\\source\\IoTEdgeAndMlSample\\DeviceHarness te openen.

3. Bekijk de methode InstallCertificate () in Program.cs.

4. Als de code het certificaatpad vindt, wordt de methode CertificateManager. InstallCACert aangeroepen om het certificaat op de computer te installeren.

5. Bekijk nu de methode GetIotHubDevice in de klasse TurbofanDevice.

6. Wanneer de gebruiker de FQDN van de gateway specificeert met behulp van de optie-g, wordt die waarde door gegeven aan deze methode als gatewayFqdn, die wordt toegevoegd aan het apparaat connection string.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Een Leaf-apparaat bouwen en uitvoeren

1. Als het DeviceHarness-project nog is geopend in Visual Studio code, bouwt u het project (CTRL + SHIFT + B of **Terminal** > **bouw taak uitvoeren...** ) en selecteert u op basis van het dialoog venster **Build** .

2. Zoek de Fully Qualified Domain Name (FQDN) voor uw Edge-gateway door te navigeren naar de virtuele machine van uw IoT Edge apparaat in de portal en de waarde voor **DNS-naam** te kopiëren in het overzicht.

3. Open de Visual Studio code-Terminal (**terminal** > **New Terminal**) en voer de volgende opdracht uit. Vervang `<edge_device_fqdn>` door de DNS-naam die u hebt gekopieerd van de virtuele machine:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. De toepassing probeert het certificaat te installeren op uw ontwikkel computer. Als dit het geval is, accepteert u de beveiligings waarschuwing.

5. Wanneer u wordt gevraagd om de IoT Hub connection string klikt u op het beletsel teken ( **...** ) in het deel venster Azure IOT Hub apparaten en selecteert u **IOT hub verbindings reeks kopiëren**. Plak de waarde in de Terminal.

6. De uitvoer ziet er als volgt uit:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Let op de toevoeging van de "GatewayHostName" aan de apparaat connection string, waardoor het apparaat via de IoT Hub via de transparante gateway via de-IoT Edge kan communiceren.

## <a name="check-output"></a>Uitvoer controleren

### <a name="iot-edge-device-output"></a>Uitvoer van IoT Edge apparaat

De uitvoer van de avroFileWriter-module kan gemakkelijk worden waargenomen door te kijken naar het IoT Edge apparaat.

1. SSH in uw IoT Edge virtuele machine.

2. Zoek naar bestanden die naar schijf zijn geschreven.

   ```bash
   find /data/avrofiles -type f
   ```

3. De uitvoer van de opdracht ziet eruit als in het volgende voor beeld:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Het kan zijn dat er meer dan één bestand is afhankelijk van de timing van de uitvoering.

4. Let op de tijds tempels. De avroFileWriter-module uploadt de bestanden naar de Cloud wanneer het laatste wijzigings tijdstip in het verleden meer dan 10 minuten is (Zie gewijzigde\_bestand\_time-out in uploader.py in de avroFileWriter-module).

5. Zodra de 10 minuten zijn verstreken, moeten de bestanden worden geüpload met de module. Als het uploaden is gelukt, worden de bestanden van de schijf verwijderd.

### <a name="azure-storage"></a>Azure Storage

We kunnen de resultaten van het Leaf-apparaat dat gegevens verzenden, bekijken door te kijken naar de opslag accounts waar we verwachten dat gegevens worden gerouteerd.

1. Open Visual Studio code op de ontwikkel machine.

2. In het deel venster AZURE-opslag in het venster verkennen navigeert u naar de structuur om uw opslag account te vinden.

3. Vouw het knoop punt **BLOB containers** uit.

4. Vanuit het werk dat we in het vorige gedeelte van de zelf studie hebben gedaan, verwachten we dat de **ruldata** -container berichten moet bevatten met resterende levens duur. Vouw het knoop punt **ruldata** uit.

5. U ziet een of meer BLOB-bestanden met de naam zoals: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Klik met de rechter muisknop op een van de bestanden en kies **BLOB downloaden** om het bestand op te slaan op uw ontwikkel computer.

7. Vouw vervolgens het knoop punt **uploadturbofanfiles** uit. In het vorige artikel stellen we deze locatie in als het doel voor bestanden die worden geüpload door de avroFileWriter-module.

8. Klik met de rechter muisknop op de bestanden en kies **BLOB downloaden** om deze op te slaan op uw ontwikkel computer.

### <a name="read-avro-file-contents"></a>Inhoud van Avro-bestand lezen

We hebben een eenvoudig opdracht regel hulpprogramma opgenomen voor het lezen van een AVRO-bestand en het retour neren van een JSON-teken reeks van de berichten in het bestand. In deze sectie wordt het geïnstalleerd en uitgevoerd.

1. Open een terminal in Visual Studio code (**terminal** > **New Terminal**).

2. Hubavroreader installeren:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Gebruik hubavroreader om het AVRO-bestand te lezen dat u hebt gedownload van **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Houd er rekening mee dat de hoofd tekst van het bericht eruitziet zoals verwacht met apparaat-ID en voorspelde resterende levens duur.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

5. Voer dezelfde opdracht uit voor het AVRO-bestand dat u hebt gedownload van **uploadturbofanfiles**.

6. Zoals verwacht, bevatten deze berichten alle sensor gegevens en operationele instellingen van het oorspronkelijke bericht. Deze gegevens kunnen worden gebruikt om het resterende levens duur-model op ons edge-apparaat te verbeteren.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om de resources die worden gebruikt door deze end-to-end zelf studie te verkennen, wacht u tot u klaar bent met het opschonen van de resources die u hebt gemaakt. Als u niet wilt door gaan, gebruikt u de volgende stappen om ze te verwijderen:

1. Verwijder de resource groep (en) die zijn gemaakt voor de ontwikkel-VM, IoT Edge VM, IoT Hub, opslag account, machine learning werkruimte service (en gemaakte resources: container register, Application Insights, sleutel kluis, opslag account).

2. Verwijder het machine learning project in [Azure notebooks](https://notebooks.azure.com).

3. Als u de opslag plaats lokaal hebt gekloond, sluit u alle Power shell-of VS-code Vensters die verwijzen naar de lokale opslag plaats en verwijdert u vervolgens de map opslag plaats.

4. Als u lokaal certificaten hebt gemaakt, verwijdert u de map c:\\edgeCertificates.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we onze ontwikkel machine gebruikt voor het simuleren van een Leaf-apparaat dat sensor-en operationele gegevens naar ons edge-apparaat verzendt. We hebben gevalideerd dat de modules op het apparaat worden gerouteerd, geclassificeerd, bewaard en dat de gegevens eerst worden geüpload door de real-time werking van het edge-apparaat te onderzoeken en vervolgens te kijken naar de bestanden die naar het opslag account worden geüpload.

Meer informatie vindt u op de volgende pagina's:

* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
* [Gegevens opslaan aan de rand met Azure Blob Storage op IoT Edge (preview)](how-to-store-data-blob.md)
