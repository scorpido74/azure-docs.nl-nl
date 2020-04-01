---
title: 'Zelfstudie: Aangepaste modules maken en implementeren - Machine Learning op Azure IoT Edge'
description: In deze zelfstudie ziet u hoe u IoT Edge-modules maken en implementeren die gegevens van bladapparaten verwerken via een machine learning-model en de inzichten vervolgens naar IoT Hub verzenden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3cba7781ac80ae567b2bfd54c4131429ed94b90f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75772360"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Zelfstudie: Aangepaste IoT Edge-modules maken en implementeren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks tot dit artikel bent gekomen, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de serie voor de beste resultaten.

In dit artikel maken we drie IoT Edge-modules die berichten ontvangen van bladapparaten, de gegevens door uw machine learning-model uitvoeren en vervolgens inzichten doorsturen naar IoT Hub.

IoT Edge hub faciliteert module-communicatie met module. Door de IoT Edge-hub te gebruiken als berichtenmakelaar blijven modules onafhankelijk van elkaar. Modules hoeven alleen de ingangen op te geven waarop ze berichten accepteren en de uitgangen waarop ze berichten schrijven.

We willen dat het IoT Edge-apparaat vier dingen voor ons bereikt:

* Gegevens ontvangen van de bladapparaten
* De resterende gebruiksduur (RUL) voorspellen voor het apparaat dat de gegevens heeft verzonden
* Stuur een bericht met alleen de RUL voor het apparaat naar IoT Hub (deze functie kan worden gewijzigd om alleen gegevens te verzenden als de RUL onder een bepaald niveau zakt)
* Sla de gegevens van het Leaf-apparaat op in een lokaal bestand op het IoT Edge-apparaat. Dit gegevensbestand wordt periodiek geüpload naar IoT Hub via het uploaden van bestanden om de training van het machine learning-model te verfijnen. Het gebruik van het uploaden van bestanden in plaats van constante berichtstreaming is kosteneffectiever.

Om deze taken uit te voeren, gebruiken we drie aangepaste modules:

* **RUL-classificatie:** De turboFanRulClassifier-module die we in Train hebben gemaakt [en een Azure Machine Learning-model implementeren,](tutorial-machine-learning-edge-04-train-model.md) is een standaard machine learning-module, die een ingang blootlegt die "amlInput" wordt genoemd en een uitvoer genaamd "amlOutput". De "amlInput" verwacht dat de invoer er precies zo uitziet als de input die we naar de ACI-gebaseerde webservice hebben gestuurd. Op dezelfde manier retourneert "amlOutput" dezelfde gegevens als de webservice.

* **Avro schrijver:** Deze module ontvangt berichten op de "avroModuleInput" ingang en blijft het bericht in Avro-formaat naar schijf voor later uploaden naar IoT Hub.

* **Routermodule:** De routermodule ontvangt berichten van downstream leaf-apparaten, maakt vervolgens formaten en stuurt de berichten naar de classificatie. De module ontvangt vervolgens de berichten van de classificatie en stuurt het bericht door naar de Avro-schrijvermodule. Ten slotte stuurt de module alleen de RUL-voorspelling naar de IoT Hub.

  * Ingangen:
    * **deviceInput**: ontvangt berichten van leaf-apparaten
    * **rulInput:** ontvangt berichten van de "amlOutput"

  * Uitgangen:
    * **classificeren:** stuurt berichten naar "amlInput"
    * **writeAvro:** stuurt berichten naar "avroModuleInput"
    * **toIotHub:** stuurt berichten naar $upstream, die de berichten doorgeeft aan de verbonden IoT Hub

Het onderstaande diagram toont de modules, ingangen, uitgangen en de IoT Edge Hub-routes voor de volledige oplossing:

![IoT Edge drie modules architectuurdiagram](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

De stappen in dit artikel worden meestal uitgevoerd door een cloudontwikkelaar.

## <a name="create-a-new-iot-edge-solution"></a>Een nieuwe IoT Edge-oplossing maken

Tijdens de uitvoering van de tweede van onze twee Azure Notebooks hebben we een containerafbeelding gemaakt en gepubliceerd die ons RUL-model bevat. Azure Machine Learning heeft dat model verpakt als onderdeel van het proces voor het maken van afbeeldingen, zodat de afbeelding kan worden geïmplementeerd als een Azure IoT Edge-module. In deze stap gaan we een Azure IoT Edge-oplossing maken met behulp van de module 'Azure Machine Learning' en de module aanwijzen op de afbeelding die we hebben gepubliceerd met Azure-notitieblokken.

1. Open een externe desktopsessie op uw ontwikkelmachine.

2. Map **C\\openen:\\bron IoTEdgeAndMlSample** in Visual Studio Code.

3. Klik met de rechtermuisknop op het deelvenster Explorer (in de lege ruimte) en selecteer **Nieuwe IoT Edge-oplossing**.

    ![Nieuwe IoT Edge-oplossing maken](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Accepteer de standaardoplossingsnaam **EdgeSolution**.

5. Kies **Azure Machine Learning** als modulesjabloon.

6. Noem de module **turbofanRulClassifier**.

7. Kies uw machine learning-werkruimte.

8. Selecteer de afbeelding die u hebt gemaakt tijdens het uitvoeren van het Azure-notitieblok.

9. Kijk naar de oplossing en let op de bestanden die zijn gemaakt:

   * **deployment.template.json:** Dit bestand bevat de definitie van elk van de modules in de oplossing. Er zijn drie secties om aandacht aan te besteden in dit bestand:

     * **Registerreferenties:** Hiermee definieert u de set aangepaste containerregisters die u in uw oplossing gebruikt. Op dit moment moet het register van uw machine learning-werkruimte bevatten, waar uw Azure Machine Learning-afbeelding is opgeslagen. U een willekeurig aantal containerregisters hebben, maar voor de eenvoud zullen we dit ene register gebruiken voor alle modules

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Modules:** Deze sectie bevat de set door de gebruiker gedefinieerde modules die bij deze oplossing past. U zult merken dat deze sectie momenteel twee modules bevat: SimulatedTemperatureSensor en turbofanRulClassifier. De SimulatedTemperatureSensor is geïnstalleerd door de Visual Studio Code template, maar we hebben het niet nodig voor deze oplossing. U de moduledefinitie SimulatedTemperatureSensor verwijderen uit de modulessectie. Merk op dat de turbofanRulClassifier module definitie wijst op de afbeelding in uw container register. Als we meer modules toevoegen aan de oplossing, zullen ze in deze sectie verschijnen.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Routes:** we zullen werken met routes nogal wat in deze tutorial. Routes bepalen hoe modules met elkaar communiceren. De twee routes die door de sjabloon worden gedefinieerd, komen niet overeen met de route die we nodig hebben. De eerste route stuurt alle gegevens van elke uitvoer van de classificatie naar de IoT-hub ($upstream). De andere route is voor SimulatedTemperatureSensor, die we net hebben verwijderd. Verwijder de twee standaardroutes.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **deployment.debug.template.json:** dit bestand is de foutopsporingsversie van deployment.template.json. We moeten alle wijzigingen van de deployment.template.json spiegelen in dit bestand.

   * **.env:** in dit bestand moet u de gebruikersnaam en het wachtwoord opgeven om toegang te krijgen tot uw register.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Klik met de rechtermuisknop op het bestand deployment.template.json in Visual Studio Code explorer en selecteer **IoT Edge Solution bouwen**.

11. Merk op dat deze opdracht een config-map maakt met een deployment.amd64.json-bestand. Dit bestand is de concrete implementatiesjabloon voor de oplossing.

## <a name="add-router-module"></a>Routermodule toevoegen

Vervolgens voegen we de Router-module toe aan onze oplossing. De Router module behandelt verschillende verantwoordelijkheden voor onze oplossing:

* **Ontvang berichten van leaf-apparaten:** wanneer berichten vanaf downstream-apparaten naar het IoT Edge-apparaat komen, ontvangt de routermodule het bericht en begint de routering van het bericht te orkestreren.
* **Berichten verzenden naar de RUL Classifier-module:** wanneer een nieuw bericht wordt ontvangen van een downstream-apparaat, wordt het bericht door de routermodule omgezet in de indeling die de RUL-classificatie verwacht. De router stuurt het bericht naar de RUL Classifier voor een RUL voorspelling. Zodra de classificatie een voorspelling heeft gedaan, stuurt het bericht terug naar de routermodule.
* **Rul-berichten verzenden naar IoT Hub:** wanneer de router berichten van de classificatie ontvangt, wordt het bericht getransformeerd om alleen de essentiële informatie, apparaat-ID en RUL te bevatten en wordt het verkorte bericht naar de IoT-hub verzonden. Een verdere verfijning, die we hier niet hebben gedaan, zou berichten sturen naar de IoT Hub alleen wanneer de RUL voorspelling valt onder een drempel (bijvoorbeeld wanneer de RUL is minder dan 100 cycli). Filteren op deze manier zou het volume van berichten verminderen en de kosten van de IoT-hub verlagen.
* **Stuur een bericht naar de Avro Writer-module:** om alle gegevens te bewaren die door het downstream-apparaat worden verzonden, stuurt de routermodule het volledige bericht dat van de classificatie is ontvangen naar de Avro Writer-module, die zal blijven bestaan en de gegevens uploadt met iot-hub-bestandsupload.

> [!NOTE]
> De beschrijving van de verantwoordelijkheden van de module kan de verwerking sequentiële lijken, maar de stroom is bericht/gebeurtenis gebaseerd. Daarom hebben we een orchestration module nodig zoals onze Router module.

### <a name="create-module-and-copy-files"></a>Module maken en bestanden kopiëren

1. Klik met de rechtermuisknop op de map modules in Visual Studio Code en kies **IoT Edge Module toevoegen**.

2. Kies **C#-module**.

3. Noem de module **turbofanRouter**.

4. Wanneer u wordt gevraagd om uw Docker Image Repository, gebruikt u het register vanuit de machine learning-werkruimte (u het register vinden in het knooppunt registerCredentials van uw *deployment.template.json-bestand).* Deze waarde is het volledig gekwalificeerde adres van het register, zoals ** \<uw register\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > In dit artikel gebruiken we het Azure Container Registry dat is gemaakt door de Azure Machine Learning-werkruimte, waarmee we onze classificatie trainden en implementeren. Dit is puur voor het gemak. We hadden een nieuw containerregister kunnen maken en daar onze modules kunnen publiceren.

5. Open een nieuw terminalvenster in Visual Studio Code **(View** > **Terminal)** en kopieer bestanden uit de modulesmap.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Wanneer gevraagd om program.cs te `y` overschrijven, druk op en druk dan op `Enter`.

### <a name="build-router-module"></a>Routermodule bouwen

1. Selecteer in Visual Studio-code **de** > **optie Standaardbuildtaak terminal configureren**.

2. Klik op **Het bestand taken maken.json van sjabloon**.

3. Klik op **.NET Core**.

4. Wanneer tasks.json wordt geopend, vervangt u de inhoud door:

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Taken opslaan en sluiten.json.

6. Build uitvoeren `Ctrl + Shift + B` met of **Terminal** > **Run Build-taak**.

### <a name="set-up-module-routes"></a>Moduleroutes instellen

Zoals hierboven vermeld, gebruikt de IoT Edge runtime routes die zijn geconfigureerd in het bestand *deployment.template.json* om de communicatie tussen losjes gekoppelde modules te beheren. In deze sectie gaan we in op het instellen van de routes voor de turbofanRouter module. We zullen eerst de invoerroutes afleggen en dan verder gaan met de uitgangen.

#### <a name="inputs"></a>Invoer

1. In de Methode Init() van Program.cs registreren we twee callbacks voor de module:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. De eerste callback luistert naar berichten die naar de **deviceInput-sink** worden verzonden. In het bovenstaande diagram zien we dat we berichten van elk bladapparaat naar deze invoer willen routeren. Voeg in het bestand *deployment.template.json* een route toe die de randhub vertelt om berichten die zijn ontvangen door het IoT Edge-apparaat dat niet door een IoT Edge-module is verzonden, te routeren naar de invoer die "deviceInput" op de turbofanRouter-module wordt genoemd:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Voeg vervolgens een route toe voor berichten van de rulClassifier module in de turbofanRouter module:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Uitvoer

Voeg vier extra routes toe aan de parameter $edgeHub route om uitvoer vanuit de routermodule te verwerken.

1. Program.cs definieert de methode SendMessageToClassifier(), die de moduleclient gebruikt om een bericht naar de RUL-classificatie te verzenden met behulp van de route:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() gebruikt de moduleclient om alleen de RUL-gegevens voor het apparaat via de route naar de IoT-hub te verzenden:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() gebruikt de moduleclient om het bericht te verzenden met de RUL-gegevens die zijn toegevoegd aan de avroFileWriter-module.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() verzendt mislukte berichten stroomopwaarts de IoT Hub waar ze voor later kunnen worden doorgestuurd.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Met alle routes samen uw "$edgeHub" knooppunt moet eruit zien als de volgende JSON:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> Het toevoegen van de turbofanRouter `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`module creëerde de volgende extra route: . Verwijder deze route, waarbij alleen de bovenstaande routes in uw bestand deployment.template.json worden overgenomen.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Routes kopiëren naar deployment.debug.template.json

Als laatste stap, om onze bestanden gesynchroniseerd te houden, spiegelde u de wijzigingen die u hebt aangebracht in deployment.template.json in deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Module Avro Writer toevoegen

De Avro Writer module heeft twee verantwoordelijkheden in onze oplossing, om berichten op te slaan en bestanden te uploaden.

* **Berichten opslaan:** wanneer de Module Avro Writer een bericht ontvangt, wordt het bericht geschreven naar het lokale bestandssysteem in Avro-formaat. We gebruiken een bindmount, die een map (in dit geval /data/avrofiles) monteert in een pad in de container van de module. Met deze bevestiging kan de module naar een lokaal pad (/avrofiles) schrijven en deze bestanden rechtstreeks toegankelijk hebben vanaf het IoT Edge-apparaat.

* **Bestanden uploaden:** de Avro Writer-module gebruikt de functie voor het uploaden van Azure IoT Hub-bestanden om bestanden te uploaden naar een Azure-opslagaccount. Zodra een bestand is geüpload, verwijdert de module het bestand van de schijf

### <a name="create-module-and-copy-files"></a>Module maken en bestanden kopiëren

1. Zoek in het opdrachtpalet naar **Vervolgens Python: Selecteer Tolk**.

1. Kies de tolk in\\C: Python37.

1. Open het opdrachtpalet opnieuw en zoek naar vervolgens **Terminal: Selecteer Standaardshell**.

1. Kies **Opdrachtprompt wanneer**u daarom wordt gevraagd.

1. Open een nieuwe terminal shell, **Terminal** > **New Terminal**.

1. Klik met de rechtermuisknop op de map modules in Visual Studio Code en kies **IoT Edge Module toevoegen**.

1. Kies **Python-module**.

1. Noem de module "avroFileWriter".

1. Gebruik hetzelfde register als u hebt gebruikt bij het toevoegen van de Router-module wanneer u wordt gevraagd om uw Docker Image Repository.

1. Kopieer bestanden uit de voorbeeldmodule naar de oplossing.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Als u wordt gevraagd om `y` main.py `Enter`te overschrijven, typt u en drukt u vervolgens op .

1. Merk op dat filemanager.py en schema.py aan de oplossing zijn toegevoegd en main.py is bijgewerkt.

> [!NOTE]
> Wanneer u een Python-bestand opent, wordt u mogelijk gevraagd om pylint te installeren. U hoeft de linter niet te installeren om deze zelfstudie te voltooien.

### <a name="bind-mount-for-data-files"></a>Bindingsbevestiging voor gegevensbestanden

Zoals vermeld in de intro, de schrijver module is gebaseerd op de aanwezigheid van bind mount te schrijven Avro bestanden naar het bestandssysteem van het apparaat.

#### <a name="add-directory-to-device"></a>Map toevoegen aan apparaat

1. Maak verbinding met de VM van uw IoT Edge-apparaat met SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Maak de map met de opgeslagen berichten van het Leaf-apparaat.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Update directorymachtigingen om het te schrijven door de container.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Met het valideren van de map is nu schrijftoestemming (w) voor gebruiker, groep en eigenaar.

   ```bash
   ls -la /data
   ```

   ![Directorymachtigingen voor avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Map toevoegen aan de module

Om de map toe te voegen aan de container van de module, wijzigen we de Dockerfiles die zijn gekoppeld aan de avroFileWriter-module. Er zijn drie Dockerfiles gekoppeld aan de module: Dockerfile.amd64, Dockerfile.amd64.debug en Dockerfile.arm32v7. Deze bestanden moeten gesynchroniseerd worden gehouden voor het geval we willen debuggen of implementeren op een arm32-apparaat. Richt u voor dit artikel alleen op Dockerfile.amd64.

1. Open het **Dockerfile.amd64-bestand** op uw ontwikkelingsmachine.

2. Wijzig het bestand zodat het er in het volgende voorbeeld uitziet:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   De `mkdir` `chown` opdrachten en opdrachten instrueren het Docker-bouwproces om een directory op het hoogste niveau genaamd /avrofiles in de afbeelding te maken en vervolgens de modulegebruiker de eigenaar van die map te maken. Het is belangrijk dat deze opdrachten worden ingevoegd nadat de `useradd` modulegebruiker met de opdracht aan de afbeelding is toegevoegd en voordat de context overschakelt naar de modulegebruiker (USER moduleuser).

3. Breng de bijbehorende wijzigingen aan in Dockerfile.amd64.debug en Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>De moduleconfiguratie bijwerken

De laatste stap van het maken van de binding is het bijwerken van de bestanden deployment.template.json (en deployment.debug.template.json) met de bindingsgegevens.

1. Deployment.template.json openen.

2. Wijzig de moduledefinitie voor avroFileWriter door de `Binds` parameter toe te voegen die de containermap /avrofiles naar de lokale map op het randapparaat wijst. De definitie van uw module moet overeenkomen met dit voorbeeld:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Breng de bijbehorende wijzigingen aan in deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Bind mount voor toegang tot config.yaml

We moeten nog een binding toevoegen voor de schrijversmodule. Deze binding geeft de module toegang tot het lezen van de verbindingstekenreeks van het bestand /etc/iotedge/config.yaml op het IoT Edge-apparaat. We hebben de verbindingstekenreeks nodig om een IoTHubClient\_\_te maken, zodat we de uploadblob-async-methode kunnen aanroepen om bestanden naar de IoT-hub te uploaden. De stappen voor het toevoegen van deze binding zijn vergelijkbaar met die in de vorige sectie.

#### <a name="update-directory-permission"></a>Mapmachtigingen bijwerken

1. Maak verbinding met uw IoT Edge-apparaat met SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Leestoestemming toevoegen aan het config.yaml-bestand.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. De machtigingen valideren zijn correct ingesteld.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Zorg ervoor dat de machtigingen voor config.yaml **-r--r--r zijn.**

#### <a name="add-directory-to-module"></a>Map toevoegen aan module

1. Open het **Dockerfile.amd64-bestand** op uw ontwikkelingsmachine.

2. Voeg een extra `mkdir` `chown` set en opdrachten toe aan het bestand, zodat het er zo uitziet:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Breng de bijbehorende wijzigingen aan in Dockerfile.amd64.debug en Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>De moduleconfiguratie bijwerken

1. Open het bestand **deployment.template.json.**

2. Wijzig de moduledefinitie voor avroFileWriter door `Binds` een tweede regel toe te voegen aan de parameter die de containermap (/app/iotconfig) naar de lokale directory op het apparaat wijst (/etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Breng de bijbehorende wijzigingen aan in deployment.debug.template.json.

## <a name="install-dependencies"></a>Afhankelijkheden installeren

De writer module neemt een afhankelijkheid van twee Python bibliotheken, fastavro en PyYAML. We moeten de afhankelijkheden van onze ontwikkelingsmachine installeren en het Docker-bouwproces instrueren om ze in het beeld van onze module te installeren.

### <a name="pyyaml"></a>PyYAML (PyYAML)

1. Open op uw ontwikkelmachine het **requirements.txt-bestand** en voeg pyyaml toe.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Open het **Dockerfile.amd64-bestand** en voeg een `pip install` opdracht toe om de instellingen te upgraden.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Breng de bijbehorende wijzigingen aan in Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Installeer pyyaml lokaal door een terminal te openen in Visual Studio Code en te typen

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro Fastavro

1. In requirements.txt, voeg fastavro na pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Installeer fastavro op uw ontwikkelingsmachine met behulp van de Visual Studio Code terminal.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>IoT-hub opnieuw configureren

Door de introductie van het IoT Edge-apparaat en de modules in het systeem, hebben we onze verwachtingen veranderd over welke gegevens naar de hub worden verzonden en voor welk doel. We moeten de routing in de hub opnieuw configureren om met onze nieuwe realiteit om te gaan.

> [!NOTE]
> We configureren de hub opnieuw voordat we modules implementeren omdat sommige hub-instellingen, met name het uploaden van bestanden, correct moeten zijn ingesteld om de avroFileWriter-module correct uit te voeren

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Route instellen voor RUL-berichten in IoT-hub

Met de router en classificatie op zijn plaats, verwachten we regelmatig berichten te ontvangen die alleen de apparaat-ID en de RUL-voorspelling voor het apparaat bevatten. We willen de RUL-gegevens doorsturen naar een eigen opslaglocatie waar we de status van de apparaten kunnen controleren, rapporten en brandmeldingen kunnen maken als dat nodig is. Tegelijkertijd willen we dat apparaatgegevens die nog steeds rechtstreeks worden verzonden door een leaf-apparaat dat nog niet is gekoppeld aan ons IoT Edge-apparaat, doorgaan met de route naar de huidige opslaglocatie.

#### <a name="create-a-rul-message-route"></a>Een RUL-berichtroute maken

1. Navigeer in de Azure-portal naar uw IoT-hub.

2. Kies **berichtroutering**aan de linkerkant van de linkernavigatie .

3. Selecteer **Toevoegen**.

4. Noem de route **RulMessageRoute**.

5. Selecteer **Toevoegen** naast de **eindpuntkiezer** en kies **Blob-opslag**.

6. Geef in het **eindpuntformulier Een opslageindpunt toevoegen** de naam van het eindpunt **ruldata**.

7. Selecteer **Een container kiezen**.

8. Kies het opslagaccount dat tijdens deze zelfstudie wordt gebruikt, die wordt genoemd als **een uniek\<achtervoegsel\>van iotedgeandml.**

9. Kies de **ruldata-container** en klik op **Selecteren**.

10. Klik **op Maken** om het opslageindpunt te maken.

11. Voer voor de **routeringsquery**de volgende query in:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Vouw de sectie **Test** uit en vervolgens de sectie **Berichttekst.** Vervang het bericht door dit voorbeeld van onze verwachte berichten:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Selecteer **Testroute**. Als de test is geslaagd, ziet u 'Het bericht komt overeen met de query'.

14. Klik op **Opslaan**.

#### <a name="update-turbofandevicetostorage-route"></a>TurbofanDeviceToStorage-route bijwerken

We willen de nieuwe voorspellingsgegevens niet doorsturen naar onze oude opslaglocatie, dus de route bijwerken om deze te voorkomen.

1. Selecteer op de **routeringspagina van** het IoT-hubbericht het tabblad **Routes.**

2. Selecteer **turbofanDeviceDataToStorage**of welke naam u ook hebt opgegeven aan de route van uw oorspronkelijke apparaatgegevens.

3. De routeringsquery bijwerken naar

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Vouw de sectie **Test** uit en vervolgens de sectie **Berichttekst.** Vervang het bericht door dit voorbeeld van onze verwachte berichten:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Selecteer **Testroute**. Als de test is geslaagd, ziet u 'Het bericht komt overeen met de query'.

6. Selecteer **Opslaan**.

### <a name="configure-file-upload"></a>Uploaden van bestanden configureren

Configureer de functie voor het uploaden van IoT Hub-bestanden zodat de module bestandsschrijver bestanden naar opslag kan uploaden.

1. Kies **Bestand uploaden**vanaf de linkernavigator in uw IoT-hub .

2. Selecteer **Azure Storage Container**.

3. Selecteer uw opslagaccount in de lijst.

4. Selecteer de **container uploadturbofanbestanden** en klik op **Selecteren**.

5. Selecteer **Opslaan**. De portal waarschuwt u wanneer de opslag is voltooid.

> [!Note]
> We schakelen geen uploadmelding in voor deze zelfstudie, maar zie [Een melding voor het uploaden van bestanden ontvangen](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) voor meer informatie over het verwerken van meldingen voor het uploaden van bestanden.

## <a name="build-publish-and-deploy-modules"></a>Modules bouwen, publiceren en implementeren

Nu we de configuratiewijzigingen hebben doorgevoerd, zijn we klaar om de afbeeldingen te bouwen en te publiceren naar ons Azure-containerregister. Het buildproces maakt gebruik van het bestand deployment.template.json om te bepalen welke modules moeten worden gebouwd. De instellingen voor elke module, inclusief versie, zijn te vinden in het module.json-bestand in de modulemap. In het buildproces wordt eerst een Docker-build uitgevoerd op de Dockerbestanden die overeenkomt met de huidige configuratie in het module.json-bestand om een afbeelding te maken. Vervolgens publiceert de afbeelding in het register van het module.json-bestand met een versietag die overeenkomt met de afbeelding in het module.json-bestand. Ten slotte produceert het een configuratiespecifiek implementatiemanifest (bijvoorbeeld deployment.amd64.json), dat we zullen implementeren op het IoT Edge-apparaat. Het IoT Edge-apparaat leest de informatie uit het implementatiemanifest en downloadt op basis van de instructies de modules, configureert de routes en stelt de gewenste eigenschappen in. Deze implementatiemethode heeft twee bijwerkingen waarvan u zich bewust moet zijn:

* **Implementatievertraging:** aangezien de Runtime van IoT Edge de wijziging in de gewenste eigenschappen moet herkennen voordat deze opnieuw wordt geconfigureerd, kan het enige tijd duren voordat u uw modules implementeert totdat de runtime ze oppikt en het IoT Edge-apparaat begint bij te werken.

* **Moduleversies zijn belangrijk:** als u een nieuwe versie van de container van een module publiceert naar uw containerregister met dezelfde versietags als de vorige module, wordt de runtime niet gedownload van de nieuwe versie van de module. Het doet een vergelijking van de versie tag van de lokale afbeelding en de gewenste afbeelding uit de implementatie manifest. Als deze versies overeenkomen, wordt er geen actie ondernomen. Daarom is het belangrijk om de versie van uw module te verhogen elke keer dat u nieuwe wijzigingen wilt implementeren. De versie wijzigen door de eigenschap **versie** te wijzigen onder de eigenschap **tag** in het module.json-bestand voor de module die u wijzigt. Bouw en publiceer de module.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Bouwen en publiceren

1. Open in Visual Studio Code op uw ontwikkelVM een Visual Studio Code-terminalvenster en log in op uw containerregister.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Klik in Visual Studio Code met de rechtermuisknop op deployment.template.json en kies **IoT Edge-oplossing bouwen en pushen.**

### <a name="view-modules-in-the-registry"></a>Modules in het register weergeven

Zodra de build is voltooid, kunnen we de Azure-portal gebruiken om onze gepubliceerde modules te bekijken.

1. Navigeer in de Azure-portal naar uw Azure Machine Learning-werkruimte en klik op de hyperlink voor **Register.**

    ![Navigeren naar het register vanuit de machine learning-servicewerkruimte](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Selecteer **repositories**in de navigator aan de registerzijde.

3. Houd er rekening mee dat beide modules die u hebt gemaakt, **avrofilewriter** en **turbofanrouter**, worden weergegeven als opslagplaatsen.

4. Selecteer **turbofanrouter** en houd er rekening mee dat u een afbeelding hebt gepubliceerd met de tag 0.0.1-amd64.

   ![Bekijk de eerste gelabelde versie van turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Modules implementeren op IoT Edge-apparaat

We hebben de modules in onze oplossing gebouwd en geconfigureerd, nu zullen we de modules implementeren op het IoT Edge-apparaat.

1. Klik in Visual Studio Code met de rechtermuisknop op het bestand **deployment.amd64.json** in de map config.

2. Kies **Implementatie maken voor één apparaat**.

3. Kies uw IoT Edge-apparaat, **aaTurboFanEdgeDevice.**

4. Vernieuw het deelvenster Azure IoT Hub-apparaten in Visual Studio Code explorer. U moet zien dat de drie nieuwe modules zijn geïmplementeerd, maar nog niet worden uitgevoerd.

5. Vernieuw je na een paar minuten opnieuw en je ziet de modules draaien.

   ![Hardloopmodules weergeven in Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Het kan enkele minuten duren voordat de modules beginnen en zich in een stabiele looptoestand vestigen. Gedurende die tijd u modules starten en stoppen wanneer ze proberen een verbinding met de IoT Edge-hubmodule tot stand te brengen.

## <a name="diagnosing-failures"></a>Fouten diagnosticeren

In deze sectie delen we een aantal technieken om te begrijpen wat er mis is gegaan met een module of modules. Vaak kan een storing eerst worden gezien vanuit de status in de Visual Studio Code.

### <a name="identify-failed-modules"></a>Mislukte modules identificeren

* **Visual Studio-code:** Kijk naar het azure IoT Hub-apparatenpaneel. Als de meeste modules in een lopende toestand zijn, maar er een is gestopt, moet u die gestopte module verder onderzoeken. Als alle modules gedurende een lange periode in een gestopte toestand verkeren, kan dit ook duiden op een storing.

* **Azure-portal:** Door naar uw IoT-hub in de portal te navigeren en vervolgens de pagina met apparaatdetails te vinden (onder IoT Edge, boor in uw apparaat) u merken dat een module een fout heeft gemeld of nooit iets heeft gemeld aan de IoT-hub.

### <a name="diagnosing-from-the-device"></a>Diagnose vanaf het apparaat

Door in te loggen op het IoT Edge-apparaat, u toegang krijgen tot een groot deel van de informatie over de status van uw modules. Het belangrijkste mechanisme dat we gebruiken zijn de Docker commando's waarmee we de containers en afbeeldingen op het apparaat kunnen onderzoeken.

1. Vermeld alle lopende containers. We verwachten voor elke module een container te zien met een naam die overeenkomt met de module. Deze opdracht geeft ook de exacte afbeelding voor de container inclusief versie weer, zodat u overeenkomen met uw verwachting. U ook afbeeldingen vermelden door 'afbeelding' te vervangen door 'container' in de opdracht.

   ```bash
   sudo docker container ls
   ```

2. Haal de logboeken voor een container. Deze opdracht voert uit wat er is geschreven naar StdErr en StdOut in de container. Deze opdracht werkt voor containers die zijn begonnen en vervolgens om de een of andere reden zijn overleden. Het is ook handig om te begrijpen wat er is gebeurd met de edgeAgent- of edgeHub-containers.

   ```bash
   sudo docker container logs <container name>
   ```

3. Inspecteer een container. Deze opdracht geeft een ton van informatie over de afbeelding. De gegevens kunnen worden gefilterd, afhankelijk van wat u zoekt. Als u bijvoorbeeld wilt zien of de bindingen op de avroFileWriter correct zijn, u de opdracht gebruiken:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Maak verbinding met een lopende container. Deze opdracht kan handig zijn als u de container wilt onderzoeken terwijl deze wordt uitgevoerd:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een IoT Edge-oplossing in Visual Studio Code gemaakt met drie modules, een classificatie, een router en een bestandsschrijver/uploader. We hebben de routes zo ingesteld dat de modules met elkaar kunnen communiceren op het randapparaat, de configuratie van het randapparaat hebben gewijzigd en de Dockerfiles hebben bijgewerkt om afhankelijkheden te installeren en bindbevestigingen toe te voegen aan de containers van de modules. Vervolgens hebben we de configuratie van de IoT-hub bijgewerkt om onze berichten te routeren op basis van het type en om bestandsuploads te verwerken. Met alles op zijn plaats, hebben we de modules geïmplementeerd op het IoT Edge-apparaat en ervoor gezorgd dat de modules correct werkten.

Meer informatie is te vinden op de volgende pagina's:

* [Meer informatie over het implementeren van modules en het vaststellen van routes naar IoT Edge](module-composition.md)
* [Querysyntaxis voor IoT Hub-berichtroutering](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub-berichtroutering: nu met routering op berichtbody](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Bestanden uploaden met IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Bestanden uploaden van uw apparaat naar de cloud met IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Ga verder naar het volgende artikel om gegevens te verzenden en uw oplossing in actie te zien.

> [!div class="nextstepaction"]
> [Gegevens verzenden via transparante gateway](tutorial-machine-learning-edge-07-send-data-to-hub.md)
