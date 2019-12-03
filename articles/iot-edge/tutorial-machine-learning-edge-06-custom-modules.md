---
title: 'Zelf studie: aangepaste modules maken en implementeren-Machine Learning op Azure IoT Edge'
description: Deze zelf studie laat zien hoe u IoT Edge modules maakt en implementeert waarmee gegevens van Blade apparaten worden verwerkt via een machine learning model en vervolgens de inzichten naar IoT Hub verzenden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9890a393d9b2955c1eb0c9894d454a774af68ef
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701833"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Zelf studie: aangepaste IoT Edge-modules maken en implementeren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks in dit artikel hebt gearriveerd, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks voor de beste resultaten.

In dit artikel maken we drie IoT Edge modules die berichten van Leaf-apparaten ontvangen, de gegevens via uw machine learning model uitvoeren en inzichten vervolgens naar IoT Hub door sturen.

IoT Edge hub vereenvoudigt module naar module communicatie. Als u de IoT Edge hub als Message Broker gebruikt, blijven de modules onafhankelijk van elkaar. Modules hoeven alleen de invoer op te geven waarop de berichten worden geaccepteerd en de uitvoer waarnaar ze berichten schrijven.

We willen dat het IoT Edge-apparaat vier dingen doet voor ons:

* Gegevens ontvangen van de Blade-apparaten
* Resterend nuttige levens duur (resterende levens duur) voors pellen voor het apparaat dat de gegevens heeft verzonden
* Een bericht met alleen de resterende levens duur voor het apparaat naar IoT Hub verzenden (deze functie kan zodanig worden gewijzigd dat alleen gegevens worden verzonden als de resterende levens duur onder een bepaald niveau daalt)
* Sla de gegevens van het Leaf-apparaat op in een lokaal bestand op het IoT Edge apparaat. Dit gegevens bestand wordt regel matig geüpload naar IoT Hub via het uploaden van bestanden om de training van het machine learning model te verfijnen. Het gebruik van bestanden uploaden in plaats van constante berichten streaming is rendabeler.

We gebruiken drie aangepaste modules om deze taken uit te voeren:

* **Resterende levens duur-classificatie:** De turboFanRulClassifier-module die we hebben gemaakt in de [trein en implementatie van een Azure machine learning model](tutorial-machine-learning-edge-04-train-model.md) is een standaard machine learning module, waarmee een invoer wordt aangeduid met de naam ' amlInput ' en een uitvoer met de naam ' amlOutput '. De invoer van ' amlInput ' verwacht precies hetzelfde als de invoer die we hebben verzonden naar de ACI-gebaseerde webservice. Op dezelfde manier retourneert ' amlOutput ' dezelfde gegevens als de webservice.

* **Avro schrijver:** Deze module ontvangt berichten over de invoer van ' avroModuleInput ' en bewaart het bericht in de Avro-indeling naar de schijf voor later uploaden naar IoT Hub.

* **Router module:** De router module ontvangt berichten van downstream Leaf-apparaten, vervolgens worden de berichten opgemaakt en verzonden naar de classificatie. De module ontvangt vervolgens de berichten van de classificatie en stuurt het bericht door naar de Avro Writer-module. Ten slotte verzendt de module alleen de resterende levens duur-voor spelling naar de IoT Hub.

  * Invoer
    * **deviceInput**: ontvangt berichten van Leaf-apparaten
    * **rulInput:** ontvangt berichten van de "amlOutput"

  * Uitvoer
    * **classificeren:** berichten verzenden naar ' amlInput '
    * **writeAvro:** stuurt berichten ' avroModuleInput '
    * **toIotHub:** stuurt berichten naar $upstream, waarmee de berichten worden doorgestuurd naar de verbonden IOT hub

In het onderstaande diagram ziet u de modules, invoer, uitvoer en de IoT Edge hub-routes voor de volledige oplossing:

![Diagram van drie modules-architectuur IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

De stappen in dit artikel worden doorgaans uitgevoerd door een Cloud ontwikkelaar.

## <a name="create-a-new-iot-edge-solution"></a>Een nieuwe IoT Edge oplossing maken

Tijdens de uitvoering van de tweede van onze twee Azure Notebooks hebben we een container installatie kopie gemaakt en gepubliceerd met het resterende levens duur-model. Azure Machine Learning, als onderdeel van het proces voor het maken van de installatie kopie, het model verpakt zodat de installatie kopie kan worden geïmplementeerd als een Azure IoT Edge-module. In deze stap gaan we een Azure IoT Edge-oplossing maken met behulp van de module ' Azure Machine Learning ' en de module naar de installatie kopie wijzen die we hebben gepubliceerd met behulp van Azure Notebooks.

1. Open een extern-bureaublad sessie naar uw ontwikkel machine.

2. Open map **C:\\bron\\IoTEdgeAndMlSample** in Visual Studio code.

3. Klik met de rechter muisknop op het deel venster Verkenner (in de lege ruimte) en selecteer **nieuwe IOT EDGE oplossing**.

    ![Nieuwe IoT Edge-oplossing maken](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Accepteer de standaard oplossings naam **EdgeSolution**.

5. Kies **Azure machine learning** als module sjabloon.

6. Noem de module **turbofanRulClassifier**.

7. Kies uw machine learning-werk ruimte.

8. Selecteer de installatie kopie die u hebt gemaakt tijdens het uitvoeren van de Azure-notebook.

9. Bekijk de oplossing en Let op de bestanden die zijn gemaakt:

   * **implementatie. sjabloon. json:** Dit bestand bevat de definitie van elk van de modules in de oplossing. Er zijn drie secties waarmee u rekening moet best Eden aan dit bestand:

     * **Register referenties:** Hiermee definieert u de set aangepaste container registers die u in uw oplossing gebruikt. Op dit moment moet het het REGI ster bevatten uit uw machine learning-werk ruimte, waar uw Azure Machine Learning-installatie kopie is opgeslagen. U kunt een wille keurig aantal container registers hebben, maar voor de eenvoud gaan we dit ene REGI ster gebruiken voor alle modules

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Modules:** Deze sectie bevat de set door de gebruiker gedefinieerde modules die met deze oplossing gaan. U zult zien dat deze sectie momenteel twee modules bevat: SimulatedTemperatureSensor en turbofanRulClassifier. De SimulatedTemperatureSensor is geïnstalleerd door de Visual Studio code-sjabloon, maar dit is niet nodig voor deze oplossing. U kunt de module definitie SimulatedTemperatureSensor verwijderen uit de sectie modules. Houd er rekening mee dat de definitie van de turbofanRulClassifier-module verwijst naar de installatie kopie in het container register. Wanneer we meer modules toevoegen aan de oplossing, worden ze weer gegeven in deze sectie.

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

     * **Routes:** we werken samen met routes die in deze zelf studie heel wat zijn. Routes bepalen hoe modules met elkaar communiceren. De twee routes die zijn gedefinieerd door de sjabloon komen niet overeen met de route ring die we nodig hebben. Met de eerste route worden alle gegevens van elke uitvoer van de classificatie verzonden naar de IoT Hub ($upstream). De andere route is voor SimulatedTemperatureSensor, die we zojuist hebben verwijderd. Verwijder de twee standaard routes.

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

   * **implementatie. debug. Temp late. json:** dit bestand is de foutopsporingsversie van Deployment. Temp late. json. We moeten alle wijzigingen van de implementatie. Temp late. json naar dit bestand spie gelen.

   * **. env:** in dit bestand moet u de gebruikers naam en het wacht woord opgeven voor toegang tot het REGI ster.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Klik met de rechter muisknop op het bestand Deployment. sjabloon. json in Visual Studio code Explorer en selecteer **Build IOT Edge Solution**.

11. U ziet dat met deze opdracht een configuratiemap met het bestand implement. amd64. json wordt gemaakt. Dit bestand is de concrete implementatie sjabloon voor de oplossing.

## <a name="add-router-module"></a>Router module toevoegen

Vervolgens voegen we de router module toe aan onze oplossing. De router module verwerkt verschillende verantwoordelijkheden voor onze oplossing:

* **Berichten ontvangen van Blade apparaten:** wanneer berichten binnenkomen op het IOT edge apparaat vanaf downstream-apparaten, ontvangt de router module het bericht en begint de route ring van het bericht.
* **Berichten verzenden naar de resterende levens duur-classificatie module:** wanneer een nieuw bericht wordt ontvangen van een downstream-apparaat, wordt het bericht door de router module getransformeerd naar de indeling die de resterende levens duur-classificatie verwacht. De router verzendt het bericht naar de resterende levens duur-classificatie voor een resterende levens duur-voor spelling. Zodra de classificatie een voor spelling heeft gemaakt, stuurt deze het bericht terug naar de module router.
* **Resterende levens duur-berichten verzenden naar IOT hub:** wanneer de router berichten van de classificatie ontvangt, wordt het bericht getransformeerd zodat alleen de essentiële informatie, apparaat-id en resterende levens duur worden opgeslagen en wordt het verkorte bericht naar de IOT-hub verzonden. Een verdere verfijning, die hier nog niet is gedaan, stuurt berichten naar het IoT Hub alleen wanneer de voor spelling van de resterende levens duur lager is dan een drempel waarde (bijvoorbeeld wanneer de resterende levens duur minder is dan 100 cycli). Als u op deze manier filtert, vermindert u de hoeveelheid berichten en worden de kosten van de IoT-hub verlaagd.
* **Bericht verzenden naar de Avro Writer-module:** om alle gegevens te bewaren die worden verzonden door het downstream-apparaat, verzendt de router module het volledige bericht dat van de classificatie wordt ontvangen, naar de module Avro Writer, die de gegevens persistent maakt en uploadt met IOT hub bestand uploaden.

> [!NOTE]
> De beschrijving van de verantwoordelijkheden van de module kan ervoor zorgen dat de verwerking sequentieel lijkt, maar de stroom is gebaseerd op bericht/gebeurtenis. Daarom hebben we een Orchestration-module nodig, zoals onze router module.

### <a name="create-module-and-copy-files"></a>Module maken en bestanden kopiëren

1. Klik met de rechter muisknop op de map modules in Visual Studio code en kies **IOT Edge module toevoegen**.

2. Kies  **C# module**.

3. Noem de module **turbofanRouter**.

4. Wanneer u wordt gevraagd om de opslag plaats voor de docker-installatie kopie, gebruikt u het REGI ster vanuit de machine learning-werk ruimte (u kunt het REGI ster vinden in het knoop punt registryCredentials van uw *implementatie. bestand sjabloon. json* ). Deze waarde is het volledig gekwalificeerde adres van het REGI ster, zoals **\<uw REGI ster\>. azurecr.io/turbofanrouter**.

    > [!NOTE]
    > In dit artikel gebruiken we de Azure Container Registry gemaakt door de Azure Machine Learning-werk ruimte, die we hebben gebruikt om onze classificatie te trainen en te implementeren. Dit is uitsluitend voor het gemak. We hebben een nieuw container register gemaakt en onze modules daar gepubliceerd.

5. Open een nieuw terminal venster in Visual Studio code (**bekijk** > **Terminal**) en kopieer bestanden vanuit de map modules.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Wanneer u wordt gevraagd program.cs te overschrijven, drukt u op `y` en vervolgens op `Enter`.

### <a name="build-router-module"></a>Build-router module

1. Selecteer in Visual Studio code de optie **Terminal** > **standaard build-taak configureren**.

2. Klik op **Create tasks. json file from Temp late**.

3. Klik op **.net core**.

4. Wanneer tasks. json wordt geopend, wordt de inhoud vervangen door:

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

5. Sla tasks. json op en sluit het.

6. Voer build uit met `Ctrl + Shift + B` of **Terminal** > een **bouw taak uit te voeren**.

### <a name="set-up-module-routes"></a>Module routes instellen

Zoals hierboven vermeld, gebruikt de IoT Edge-runtime routes die zijn geconfigureerd in het bestand *implementatie. sjabloon. json* om de communicatie tussen de los gekoppelde modules te beheren. In deze sectie wordt uitgelegd hoe u de routes voor de module turbofanRouter kunt instellen. De invoer routes worden eerst beschreven en vervolgens verplaatst naar de uitvoer.

#### <a name="inputs"></a>Invoer

1. In de methode init () van Program.cs registreren we twee retour aanroepen voor de module:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. De eerste call back luistert naar berichten die worden verzonden naar de **deviceInput** -sink. In het bovenstaande diagram zien we dat we berichten van elk Leaf-apparaat naar deze invoer willen routeren. Voeg in het bestand *Deployment. Temp late. json* een route toe die aangeeft dat de Edge hub elk bericht stuurt dat door het IOT edge apparaat is ontvangen en dat niet door een IOT Edge module is verzonden naar de invoer ' deviceInput ' in de turbofanRouter-module:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Voeg vervolgens een route voor berichten uit de module rulClassifier toe aan de turbofanRouter-module:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Uitvoer

Voeg vier extra routes toe aan de para meter $edgeHub route om uitvoer van de router module af te handelen.

1. Program.cs definieert de methode SendMessageToClassifier (), die gebruikmaakt van de module-client om een bericht te verzenden naar de resterende levens duur-classificatie met behulp van de route:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub () gebruikt de module-client voor het verzenden van alleen de resterende levens duur-gegevens voor het apparaat naar de IoT Hub via de route:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter () gebruikt de module-client om het bericht te verzenden met de resterende levens duur-gegevens die zijn toegevoegd aan de avroFileWriter-module.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. Met HandleBadMessage () worden mislukte berichten verzonden naar de IoT Hub waar ze later kunnen worden gerouteerd.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Alle routes die het knoop punt ' $edgeHub ' samen worden genomen, moeten eruitzien als in de volgende JSON:

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
> Bij het toevoegen van de turbofanRouter-module is de volgende extra route gemaakt: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Verwijder deze route, waarbij alleen de routes worden weer gegeven die hierboven in uw implementatie zijn opgenomen. sjabloon. JSON-bestand.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Routes naar implementatie kopiëren. debug. Temp late. json

Als laatste stap is het spie gelen van de wijzigingen die u hebt aangebracht in implementatie. sjabloon. json in implementatie. debug. Temp late. json, om ervoor te zorgen dat onze bestanden synchroon blijven.

## <a name="add-avro-writer-module"></a>Avro Writer-module toevoegen

De Avro Writer-module heeft twee verantwoordelijkheden in onze oplossing om berichten op te slaan en bestanden te uploaden.

* **Berichten opslaan**: wanneer de Avro Writer-module een bericht ontvangt, wordt het bericht naar het lokale bestands systeem geschreven in Avro-indeling. We gebruiken een BIND koppeling, die een directory (in dit geval/data/avrofiles) koppelt aan een pad in de container van de module. Met deze koppeling kan de module schrijven naar een lokaal pad (/avrofiles) en deze bestanden rechtstreeks vanuit het IoT Edge apparaat toegankelijk maken.

* **Bestanden uploaden**: de Avro Writer-module gebruikt de functie voor het uploaden van Azure IOT hub bestand om bestanden te uploaden naar een Azure-opslag account. Zodra een bestand is geüpload, verwijdert de module het bestand van de schijf

### <a name="create-module-and-copy-files"></a>Module maken en bestanden kopiëren

1. Zoek in het opdracht palet naar Select **python: Select interpreter**.

1. Kies het verwerkings programma dat is gevonden in C:\\Python37.

1. Open het opdracht palet opnieuw en zoek naar Selecteer vervolgens **Terminal: Selecteer standaard shell**.

1. Wanneer u hierom wordt gevraagd, kiest u **opdracht prompt**.

1. Open een nieuwe Terminal Shell, **terminal** > **New Terminal**.

1. Klik met de rechter muisknop op de map modules in Visual Studio code en kies **IOT Edge module toevoegen**.

1. Kies **Python-module**.

1. Noem de module "avroFileWriter".

1. Wanneer u wordt gevraagd naar de opslag plaats voor de docker-installatie kopie, gebruikt u hetzelfde REGI ster als voor het toevoegen van de module router.

1. Kopieer bestanden van de voorbeeld module naar de oplossing.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Als u wordt gevraagd om main.py te overschrijven, typt u `y` en klikt u vervolgens op `Enter`.

1. U ziet dat filemanager.py en schema.py zijn toegevoegd aan de oplossing en dat main.py is bijgewerkt.

> [!NOTE]
> Wanneer u een python-bestand opent, wordt u mogelijk gevraagd om pylint te installeren. U hoeft de pluis functie niet te installeren om deze zelf studie te volt ooien.

### <a name="bind-mount-for-data-files"></a>Binding koppelen voor gegevens bestanden

Zoals vermeld in de intro, is de schrijver-module afhankelijk van de aanwezigheid van een BIND koppeling om Avro-bestanden te schrijven naar het bestands systeem van het apparaat.

#### <a name="add-directory-to-device"></a>Directory toevoegen aan apparaat

1. Maak verbinding met de VM van uw IoT Edge apparaat via SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Maak de map waarin de opgeslagen Blade-apparaten worden bewaard.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Update Directory-machtigingen om de container schrijfbaar te maken.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Controleer of de Directory nu de machtiging schrijven (w) heeft voor gebruiker, groep en eigenaar.

   ```bash
   ls -la /data
   ```

   ![Mapmachtigingen voor avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Map toevoegen aan de module

Als u de map wilt toevoegen aan de container van de module, wijzigt u de Dockerfiles die is gekoppeld aan de avroFileWriter-module. Er zijn drie Dockerfiles gekoppeld aan de module: Dockerfile. amd64, Dockerfile. amd64. debug en Dockerfile. arm32v7. Deze bestanden moeten worden gesynchroniseerd in het geval dat u fouten wilt opsporen of implementeren op een arm32-apparaat. Voor dit artikel ligt alleen gericht op Dockerfile. amd64.

1. Open het bestand **Dockerfile. amd64** op uw ontwikkel computer.

2. Wijzig het bestand zodat het er zo uitziet als in het volgende voor beeld:

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

   Met de opdrachten `mkdir` en `chown` wordt het docker-bouw proces geïnstrueerd om een map op het hoogste niveau met de naam/avrofiles in de installatie kopie te maken en vervolgens de moduleuser de eigenaar van die map te maken. Het is belang rijk dat deze opdrachten worden ingevoegd nadat de module gebruiker is toegevoegd aan de installatie kopie met de `useradd` opdracht en voordat de context wordt overgeschakeld naar de moduleuser (gebruiker moduleuser).

3. Breng de bijbehorende wijzigingen aan in Dockerfile. amd64. debug en Dockerfile. arm32v7.

#### <a name="update-the-module-configuration"></a>De module configuratie bijwerken

De laatste stap voor het maken van de binding is het bijwerken van de implementatie. sjabloon. JSON-bestand (en implementatie. debug. sjabloon. json) met de bindings informatie.

1. Open implementatie. Temp late. json.

2. Wijzig de module definitie voor avroFileWriter door de para meter `Binds` toe te voegen die de container Directory/avrofiles naar de lokale map op het edge-apparaat wijst. De module definitie moet overeenkomen met dit voor beeld:

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

3. Breng de bijbehorende wijzigingen in de implementatie. debug. Temp late. json.

### <a name="bind-mount-for-access-to-configyaml"></a>Binding koppelen voor toegang tot config. yaml

Er moet nog één binding worden toegevoegd voor de schrijver-module. Deze binding geeft de module toegang om de connection string te lezen uit het/etc/iotedge/config.yaml-bestand op het IoT Edge-apparaat. We hebben de connection string nodig om een IoTHubClient te maken, zodat we de methode voor het uploaden van\_BLOB\_async kunnen aanroepen om bestanden te uploaden naar de IoT-hub. De stappen voor het toevoegen van deze binding zijn vergelijkbaar met die in de vorige sectie.

#### <a name="update-directory-permission"></a>Directory machtigingen bijwerken

1. Maak via SSH verbinding met uw IoT Edge-apparaat.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Lees machtiging toevoegen aan het bestand config. yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Controleer of de machtigingen juist zijn ingesteld.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Zorg ervoor dat de machtigingen voor config. yaml zijn **: r--r--r--** .

#### <a name="add-directory-to-module"></a>Map toevoegen aan module

1. Open het bestand **Dockerfile. amd64** op uw ontwikkel computer.

2. Voeg een extra set `mkdir` en `chown` opdrachten toe aan het bestand, zodat er ongeveer als volgt uitziet:

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

3. Breng de bijbehorende wijzigingen aan in Dockerfile. amd64. debug en Dockerfile. arm32v7.

#### <a name="update-the-module-configuration"></a>De module configuratie bijwerken

1. Open het bestand **Deployment. Temp late. json** .

2. Wijzig de module definitie voor avroFileWriter door een tweede regel toe te voegen aan de para meter `Binds` die de container Directory (/app/iotconfig) naar de lokale map op het apparaat (/etc/iotedge) wijst.

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

3. Breng de bijbehorende wijzigingen in de implementatie. debug. Temp late. json.

## <a name="install-dependencies"></a>Afhankelijkheden installeren

De schrijf module heeft een afhankelijkheid van twee python-bibliotheken: fastavro en PyYAML. De afhankelijkheden op onze ontwikkel machine moeten worden geïnstalleerd en het docker-buildproces moet worden geïnstalleerd in de installatie kopie van de module.

### <a name="pyyaml"></a>PyYAML

1. Open het bestand **Requirements. txt** op de ontwikkel computer en voeg pyyaml toe.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Open het bestand **Dockerfile. amd64** en voeg een `pip install` opdracht toe voor het bijwerken van het installatie programma.

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

3. Breng de bijbehorende wijzigingen aan in Dockerfile. amd64. debug. <!--may not be necessary. Add 'if needed'?-->

4. Pyyaml lokaal installeren door een Terminal te openen in Visual Studio code en te typen

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. Voeg in requirements. txt fastavro toe na pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Installeer fastavro op uw ontwikkel machine met behulp van de Visual Studio code-Terminal.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>IoT Hub opnieuw configureren

Door de IoT Edge-apparaat en-modules aan het systeem te introduceren, hebben we onze verwachtingen gewijzigd over welke gegevens naar de hub worden verzonden en wat het doel is. De route ring in de hub moet opnieuw worden geconfigureerd om te kunnen omgaan met onze nieuwe realiteit.

> [!NOTE]
> De hub wordt opnieuw geconfigureerd voordat modules worden geïmplementeerd. sommige hub-instellingen, met name bestanden uploaden, moeten correct worden ingesteld om ervoor te zorgen dat de avroFileWriter-module correct wordt uitgevoerd.

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Route instellen voor resterende levens duur-berichten in IoT Hub

Als de router en classificatie zijn geïmplementeerd, wordt verwacht dat er regel matig berichten worden ontvangen met alleen de apparaat-ID en de resterende levens duur-voor spelling voor het apparaat. We willen de resterende levens duur-gegevens naar een eigen opslag locatie routeren, waar we de status van de apparaten kunnen bewaken, rapporten maken en waarschuwingen als dat nodig zijn. Op hetzelfde moment willen we dat alle apparaatgegevens rechtstreeks worden verzonden door een Leaf-apparaat dat nog niet is gekoppeld aan het apparaat van IoT Edge om naar de huidige opslag locatie te gaan.

#### <a name="create-a-rul-message-route"></a>Een resterende levens duur-bericht route maken

1. Ga in het Azure Portal naar uw IoT Hub.

2. Kies **bericht routering**in het navigatie venster aan de linkerkant.

3. Selecteer **Toevoegen**.

4. Noem de route **RulMessageRoute**.

5. Selecteer **toevoegen** naast de **eindpunt** kiezer en kies **Blob Storage**.

6. Geef in het formulier **opslag eindpunt toevoegen een** naam op voor het eind punt **ruldata**.

7. Selecteer **een container kiezen**.

8. Kies het opslag account dat wordt gebruikt in deze zelf studie, met de naam **iotedgeandml\<unieke achtervoegsel\>** .

9. Kies de **ruldata** -container en klik op **selecteren**.

10. Klik op **maken** om het opslag eindpunt te maken.

11. Voer de volgende query in voor de **routerings query**:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Vouw de sectie **testen** en vervolgens de sectie **bericht tekst** uit. Vervang het bericht door dit voor beeld van onze verwachte berichten:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Selecteer **test route**. Als de test is geslaagd, ziet u de melding ' het bericht dat overeenkomt met de query '.

14. Klik op **Opslaan**.

#### <a name="update-turbofandevicetostorage-route"></a>TurbofanDeviceToStorage-route bijwerken

We willen de nieuwe Voorspellings gegevens niet naar onze oude opslag locatie routeren, dus werk de route bij om deze te voor komen.

1. Selecteer op de pagina IoT Hub **bericht routering** het tabblad **routes** .

2. Selecteer **turbofanDeviceDataToStorage**of een wille keurige naam die u hebt gegeven aan uw oorspronkelijke apparaatgegevens route.

3. Werk de routerings query bij naar

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Vouw de sectie **testen** en vervolgens de sectie **bericht tekst** uit. Vervang het bericht door dit voor beeld van onze verwachte berichten:

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

5. Selecteer **test route**. Als de test is geslaagd, ziet u de melding ' het bericht dat overeenkomt met de query '.

6. Selecteer **Opslaan**.

### <a name="configure-file-upload"></a>Uploaden van bestanden configureren

Configureer de functie voor het uploaden van IoT Hub-bestanden om de module file writer in te scha kelen voor het uploaden van bestanden naar opslag.

1. Kies in de linkernavigatiebalk van uw IoT Hub de optie **bestand uploaden**.

2. Selecteer **Azure storage-container**.

3. Selecteer uw opslag account in de lijst.

4. Selecteer de **uploadturbofanfiles** -container en klik op **selecteren**.

5. Selecteer **Opslaan**. De portal waarschuwt u wanneer het opslaan is voltooid.

> [!Note]
> Er wordt geen upload melding voor deze zelf studie ingeschakeld, maar Zie [een melding over het uploaden van een bestand ontvangen](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) voor meer informatie over het verwerken van berichten over het uploaden van bestanden.

## <a name="build-publish-and-deploy-modules"></a>Modules bouwen, publiceren en implementeren

Nu u de configuratie wijzigingen hebt aangebracht, kunt u de installatie kopieën bouwen en publiceren naar ons Azure container Registry. Het bouw proces maakt gebruik van het bestand Deployment. template. json om te bepalen welke modules moeten worden gebouwd. De instellingen voor elke module, inclusief versie, vindt u in het bestand module. json in de map module. Het bouw proces voert eerst een docker-build uit op de Dockerfiles die overeenkomt met de huidige configuratie gevonden in het bestand module. json om een installatie kopie te maken. Vervolgens publiceert de installatie kopie naar het REGI ster vanuit het bestand module. json met een versie label dat overeenkomt met de naam in het bestand module. json. Ten slotte produceert het een configuratie-specifiek implementatie manifest (bijvoorbeeld implementatie. amd64. json), dat we implementeren op het IoT Edge-apparaat. Het IoT Edge apparaat leest de gegevens van het implementatie manifest en op basis van de instructies worden de modules gedownload, de routes geconfigureerd en de gewenste eigenschappen ingesteld. Deze implementatie methode heeft twee neven effecten waarvan u rekening moet houden:

* **Implementatie vertraging:** omdat de IOT Edge runtime de wijziging in de gewenste eigenschappen moet herkennen voordat deze opnieuw wordt geconfigureerd, kan het enige tijd duren nadat u de modules hebt geïmplementeerd, totdat de runtime deze ophaalt en het IOT edge-apparaat begint bij te werken.

* **Module versies:** als u een nieuwe versie van de container van een module naar het container register publiceert met dezelfde versie Tags als de vorige module, wordt de nieuwe versie van de module niet door de runtime gedownload. Het maakt een vergelijking van de versie code van de lokale installatie kopie en de gewenste installatie kopie uit het implementatie manifest. Als deze versies overeenkomen, hoeft de runtime geen actie te ondernemen. Daarom is het belang rijk om de versie van uw module te verhogen telkens wanneer u nieuwe wijzigingen wilt implementeren. Verhoog de versie door de eigenschap **Version** te wijzigen onder de eigenschap **tag** in het bestand module. json voor de module die u wilt wijzigen. Vervolgens bouwt en publiceert u de module.

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

1. Open in Visual Studio code op uw ontwikkel-VM een Visual Studio code-Terminal venster en meld u aan bij uw container register.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Klik in Visual Studio code met de rechter muisknop op Deployment. Temp late. json en kies **Build and Push IOT Edge Solution**.

### <a name="view-modules-in-the-registry"></a>Modules in het REGI ster weer geven

Zodra de build is voltooid, kunnen we de Azure Portal gebruiken om onze gepubliceerde modules te bekijken.

1. Navigeer in het Azure Portal naar uw werk ruimte Azure Machine Learning en klik op de Hyper link voor het **REGI ster**.

    ![Naar het REGI ster navigeren vanuit de machine learning service werkruimte](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Selecteer in de navigatie in het REGI ster **opslag**plaatsen.

3. Houd er rekening mee dat beide modules die u hebt gemaakt, **avrofilewriter** en **turbofanrouter**, worden weer gegeven als opslag plaatsen.

4. Selecteer **turbofanrouter** en houd er rekening mee dat u één afbeelding hebt gepubliceerd als 0.0.1-amd64.

   ![Eerste gecodeerde versie van turbofanrouter weer geven](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Modules implementeren op IoT Edge apparaat

We hebben de modules in onze oplossing gemaakt en geconfigureerd, nu gaan we de modules implementeren op het IoT Edge-apparaat.

1. Klik in Visual Studio code met de rechter muisknop op het bestand **Deployment. amd64. json** in de map Config.

2. Kies **implementatie maken voor één apparaat**.

3. Kies uw IoT Edge-apparaat, **aaTurboFanEdgeDevice**.

4. Vernieuw het paneel Azure IoT Hub-apparaten in Visual Studio code Explorer. U ziet dat de drie nieuwe modules zijn geïmplementeerd, maar nog niet worden uitgevoerd.

5. Vernieuw de gegevens na een paar minuten opnieuw en u ziet de modules die worden uitgevoerd.

   ![Actieve modules weer geven in Visual Studio code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Het kan enkele minuten duren voordat de modules worden gestart en in een constante status worden uitgevoerd. Gedurende die tijd worden modules gestart en gestopt wanneer ze proberen verbinding te maken met de IoT Edge hub-module.

## <a name="diagnosing-failures"></a>Fouten vaststellen

In deze sectie delen we enkele technieken om te weten wat er mis is met een module of modules. Vaak kan een fout eerst worden Spotted uit de status van de Visual Studio code.

### <a name="identify-failed-modules"></a>Mislukte modules identificeren

* **Visual Studio code:** Bekijk het deel venster apparaten van Azure IoT Hub. Als de meeste modules een actieve status hebben, maar één is gestopt, moet u de module gestopt verder onderzoeken. Als alle modules gedurende een lange periode een gestopte status hebben, kan dit ook duiden op storingen.

* **Azure portal:** Door te navigeren naar uw IoT-hub in de portal en vervolgens de pagina met details van het apparaat te zoeken (onder IoT Edge, zoomt u in op uw apparaat), ziet u mogelijk dat een module een fout heeft gerapporteerd of niets heeft genoteerd voor de IoT-hub.

### <a name="diagnosing-from-the-device"></a>Diagnoses van het apparaat

Als u zich aanmeldt bij het IoT Edge apparaat, kunt u toegang krijgen tot een goede informatie over de status van uw modules. Het belangrijkste mechanisme dat wordt gebruikt, zijn de docker-opdrachten waarmee we de containers en installatie kopieën op het apparaat kunnen onderzoeken.

1. Alle actieve containers weer geven. Verwacht wordt dat er voor elke module een container wordt weer geven met een naam die overeenkomt met de module. Met deze opdracht wordt ook de exacte installatie kopie voor de container met de versie weer gegeven, zodat u met uw verwachting kunt overeenkomen. U kunt ook afbeeldingen weer geven door ' afbeelding ' te vervangen door ' container ' in de opdracht.

   ```bash
   sudo docker container ls
   ```

2. De logboeken voor een container ophalen. Met deze opdracht worden alle bewerkingen uitgevoerd, ongeacht of deze zijn geschreven naar StdErr en StdOut in de container. Deze opdracht werkt voor containers die zijn gestart en vervolgens om een of andere reden vastlopen. Het is ook handig om te weten wat er is gebeurd met de containers edgeAgent of edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Een container inspecteren. Met deze opdracht geeft u een ton informatie over de installatie kopie. De gegevens kunnen worden gefilterd, afhankelijk van wat u zoekt. Als u bijvoorbeeld wilt zien of de bindingen op de avroFileWriter juist zijn, kunt u de opdracht gebruiken:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Verbinding maken met een actieve container. Deze opdracht kan handig zijn als u de container wilt controleren terwijl deze wordt uitgevoerd:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een IoT Edge oplossing gemaakt in Visual Studio code met drie modules, een classificatie, een router en een bestands schrijver/Uploader. We hebben de routes zo ingesteld dat de modules met elkaar kunnen communiceren op het apparaat van de Edge, de configuratie van het edge-apparaat hebben aangepast en de Dockerfiles hebben bijgewerkt om afhankelijkheden te installeren en bindings koppelingen aan de containers van de modules toe te voegen. Daarna hebben we de configuratie van de IoT Hub bijgewerkt om onze berichten te routeren op basis van het type en om de uploads van bestanden te verwerken. Nu alles aanwezig is, hebben we de modules geïmplementeerd op het IoT Edge apparaat en hebben ze gezorgd voor de juiste uitvoering van de modules.

Meer informatie vindt u op de volgende pagina's:

* [Meer informatie over het implementeren van modules en het vaststellen van routes naar IoT Edge](module-composition.md)
* [Query syntaxis voor het routeren van berichten IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub bericht routering: nu met route ring op bericht tekst](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Bestanden uploaden met IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Ga door naar het volgende artikel om te beginnen met het verzenden van gegevens en uw oplossing in actie te zien.

> [!div class="nextstepaction"]
> [Gegevens verzenden via een transparante gateway](tutorial-machine-learning-edge-07-send-data-to-hub.md)
