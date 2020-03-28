---
title: 'Zelfstudie: Apparaatgegevens verzenden via transparante gateway - Machine Learning op Azure IoT Edge'
description: In deze zelfstudie ziet u hoe u uw ontwikkelmachine gebruiken als een gesimuleerd IoT Edge-apparaat om gegevens naar de IoT-hub te verzenden door een apparaat te doorlopen dat is geconfigureerd als een transparante gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 50f339b257110f0a5dc0ac08b9f40043ee384afb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706912"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Zelfstudie: Gegevens verzenden via transparante gateway

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks tot dit artikel bent gekomen, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de serie voor de beste resultaten.

In dit artikel gebruiken we de ontwikkelmachine opnieuw als een gesimuleerd apparaat, maar in plaats van gegevens rechtstreeks naar de IoT Hub te verzenden, stuurt het apparaat gegevens naar het IoT Edge-apparaat dat is geconfigureerd als een transparante gateway.

We controleren de werking van het IoT Edge-apparaat terwijl het gesimuleerde apparaat gegevens verzendt. Zodra het apparaat klaar is met werken, bekijken we de gegevens in ons opslagaccount om te valideren dat alles werkt zoals verwacht.

Deze stap wordt meestal uitgevoerd door een cloud- of apparaatontwikkelaar.

## <a name="review-device-harness"></a>Apparaatharnas controleren

Gebruik het [DeviceHarness-project](tutorial-machine-learning-edge-03-generate-data.md) om het downstreamapparaat (of het blad) te simuleren. Verbinding maken met de transparante gateway vereist twee extra dingen:

* Registreer het certificaat om het downstream-apparaat (in dit geval onze ontwikkelingsmachine) te laten vertrouwen op de certificaatinstantie die wordt gebruikt door de IoT Edge-runtime.
* Voeg de volledig gekwalificeerde domeinnaam (FQDN) van de edge gateway toe aan de tekenreeks voor apparaatverbinding.

Kijk naar de code om te zien hoe deze twee items worden geïmplementeerd.

1. Open Visual Studio Code op uw ontwikkelmachine.

2. Gebruik **De** > **map Bestand openen...** om C te openen:\\source\\IoTEdgeAndMlSample\\DeviceHarness.

3. Kijk naar de methode InstallCertificate() in Program.cs.

4. Houd er rekening mee dat als de code het certificaatpad vindt, de methode CertificateManager.InstallCACert wordt aangebeld om het certificaat op de machine te installeren.

5. Kijk nu naar de GetIotHubDevice-methode op de TurbofanDevice-klasse.

6. Wanneer de gebruiker de FQDN van de gateway opgeeft met behulp van de optie "-g", wordt die waarde doorgegeven aan deze methode als gatewayFqdn, die wordt toegevoegd aan de tekenreeks voor apparaatverbinding.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Bladapparaat bouwen en uitvoeren

1. Als het DeviceHarness-project nog steeds wordt geopend in Visual Studio Code, bouwt u het project (Ctrl + Shift + B of **Terminal** > **Run Build Task...**) en selecteert u **Bouwen** in het dialoogvenster.

2. Zoek de volledig gekwalificeerde domeinnaam (FQDN) voor uw edge gateway door te navigeren naar uw IoT Edge-apparaat virtuele machine in de portal en het kopiëren van de waarde voor **DNS-naam** uit het overzicht.

3. Open de Visual Studio Code-terminal **(Terminal** > **New terminal)** en voer de volgende opdracht uit, waarbij u de DNS-naam vervangt `<edge_device_fqdn>` die u van de virtuele machine hebt gekopieerd:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. De applicatie probeert het certificaat op uw ontwikkelmachine te installeren. Als dat gebeurt, accepteer dan de beveiligingswaarschuwing.

5. Klik wanneer u wordt gevraagd om de verbindingstekenreeks Van IoT Hub op de ellips (**...**) in het deelvenster Azure IoT Hub-apparaten en selecteer **IoT Hub Connection String kopiëren**. Plak de waarde in de terminal.

6. U ziet uitvoer als:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Let op de toevoeging van de "GatewayHostName" aan de verbindingstekenreeks van het apparaat, waardoor het apparaat via de IoT-hub communiceert via de transparante gateway van IoT Edge.

## <a name="check-output"></a>Uitvoer controleren

### <a name="iot-edge-device-output"></a>IoT Edge-apparaatuitvoer

De uitvoer van de avroFileWriter-module kan gemakkelijk worden waargenomen door naar het IoT Edge-apparaat te kijken.

1. SSH in uw IoT Edge virtuele machine.

2. Zoek naar bestanden die naar de schijf zijn geschreven.

   ```bash
   find /data/avrofiles -type f
   ```

3. De uitvoer van de opdracht ziet eruit als het volgende voorbeeld:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   U hebt mogelijk meer dan één bestand, afhankelijk van de timing van de run.

4. Besteed aandacht aan de tijdstempels. De avroFileWriter module uploadt de bestanden naar de cloud zodra de laatste wijzigingstijd meer dan 10 minuten in het verleden is (zie GEWIJZIGDE\_FILE\_TIMEOUT in uploader.py in de avroFileWriter module).

5. Zodra de 10 minuten zijn verstreken, moet de module de bestanden uploaden. Als de upload succesvol is, worden de bestanden van de schijf verwijderd.

### <a name="azure-storage"></a>Azure Storage

We kunnen de resultaten van ons leaf-apparaat waarnemen door te kijken naar de opslagaccounts waar we verwachten dat gegevens worden doorgestuurd.

1. Open Visual Studio Code op de ontwikkelmachine.

2. Navigeer in het deelvenster Azure STORAGE in het verkennende venster door de structuur om uw opslagaccount te vinden.

3. Het knooppunt **Blobcontainers** uitvouwen.

4. Van het werk dat we deden in het vorige deel van de tutorial, verwachten we dat de **ruldata** container berichten met RUL zou moeten bevatten. Vouw het **ruldata-knooppunt** uit.

5. U ziet een of meer blobbestanden met de naam: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Klik met de rechtermuisknop op een van de bestanden en kies **Blob downloaden** om het bestand op te slaan in uw ontwikkelingsmachine.

7. Vervolgens uitbreiden van de **uploadturbofanfiles** knooppunt. In het vorige artikel stellen we deze locatie in als doel voor bestanden die zijn geüpload door de avroFileWriter-module.

8. Klik met de rechtermuisknop op de bestanden en kies **Blob downloaden** om deze op te slaan op uw ontwikkelingsmachine.

### <a name="read-avro-file-contents"></a>Lees de inhoud van het Avro-bestand

We hebben een eenvoudige command-line utility voor het lezen van een Avro-bestand en het retourneren van een JSON string van de berichten in het bestand. In deze sectie zullen we het installeren en uitvoeren.

1. Open een terminal in Visual Studio Code **(Terminal** > **New terminal).**

2. Hubavroreader installeren:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Gebruik hubavroreader om het Avro-bestand te lezen dat u hebt gedownload van **ruldata.**

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Houd er rekening mee dat de hoofdtekst van het bericht er zo uitziet als we verwacht hadden met apparaat-ID en voorspelde RUL.

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

5. Voer dezelfde opdracht uit die het Avro-bestand passeert dat u hebt gedownload van **uploadturbofanbestanden.**

6. Zoals verwacht bevatten deze berichten alle sensorgegevens en operationele instellingen van het oorspronkelijke bericht. Deze gegevens kunnen worden gebruikt om het RUL-model op ons randapparaat te verbeteren.

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

Als u van plan bent om de resources te verkennen die door deze end-to-end-zelfstudie worden gebruikt, wacht u tot u klaar bent om de resources die u hebt gemaakt op te schonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om deze te verwijderen:

1. Verwijder de resourcegroep(s) die is gemaakt om de DEv VM, IoT Edge VM, IoT Hub, opslagaccount, machine learning workspace-service (en gemaakte resources te behouden: containerregister, toepassingsinzichten, sleutelkluis, opslagaccount).

2. Verwijder het machine learning-project in [Azure-notitieblokken](https://notebooks.azure.com).

3. Als u de repo lokaal hebt gekloond, sluit u alle PowerShell- of VS-codevensters die verwijzen naar de lokale repo en verwijdert u de repo-map.

4. Als u certificaten lokaal hebt gemaakt,\\verwijdert u de map c: edgeCertificates.

## <a name="next-steps"></a>Volgende stappen

In dit artikel gebruikten we onze ontwikkelingsmachine om een leaf-apparaat te simuleren dat sensor en operationele gegevens naar ons edge-apparaat verzendt. We valideerden dat de modules op het apparaat de gegevens routeerden, geclassificeerd, verwerkten en de gegevens eerst uploadden door de real-time werking van het edge-apparaat te onderzoeken en vervolgens door te kijken naar de bestanden die naar het opslagaccount zijn geüpload.

Meer informatie is te vinden op de volgende pagina's:

* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
* [Gegevens aan de rand opslaan met Azure Blob Storage op IoT Edge (voorbeeld)](how-to-store-data-blob.md)
