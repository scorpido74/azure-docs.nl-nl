---
title: Modules ontwikkelen en debuggen voor Azure IoT Edge | Microsoft Documenten
description: Gebruik Visual Studio Code om een module voor Azure IoT Edge te ontwikkelen, te bouwen en te debuggen met C#, Python, Node.js, Java of C
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 10c8008d73390174c44ec503f708c1e2c0011e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944301"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio Code gebruiken om modules voor Azure IoT Edge te ontwikkelen en te debuggen

U uw bedrijfslogica omzetten in modules voor Azure IoT Edge. In dit artikel ziet u hoe u Visual Studio Code gebruiken als het belangrijkste hulpmiddel om modules te ontwikkelen en te debuggen.

Er zijn twee manieren om modules te debuggen die zijn geschreven in C#, Node.js of Java in Visual Studio Code: u een proces in een modulecontainer koppelen of de modulecode starten in de foutopsporingsmodus. Om modules die in Python of C zijn geschreven te debuggen, u alleen een proces in Linux amd64-containers koppelen.

Als u niet bekend bent met de foutopsporingsmogelijkheden van Visual Studio Code, leest u over [foutopsporing.](https://code.visualstudio.com/Docs/editor/debugging)

Dit artikel bevat instructies voor het ontwikkelen en debuggen van modules in meerdere talen voor meerdere architecturen. Momenteel biedt Visual Studio Code ondersteuning voor modules geschreven in C#, C, Python, Node.js en Java. De ondersteunde apparaatarchitecturen zijn X64 en ARM32. Zie Ondersteuning voor [taal en architectuur](module-development.md#language-and-architecture-support)voor meer informatie over ondersteunde besturingssystemen, talen en architecturen.

>[!NOTE]
>Ontwikkelen en debuggen ondersteuning voor Linux ARM64 apparaten is in [openbare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Zie [ARM64 IoT Edge-modules ontwikkelen en debugen in Visual Studio Code (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

U een computer of een virtuele machine met Windows, macOS of Linux gebruiken als uw ontwikkelingsmachine. Op Windows-computers u Windows- of Linux-modules ontwikkelen. Als u Windows-modules wilt ontwikkelen, gebruikt u een Windows-computer met versie 1809/build 17763 of nieuwer. Als u Linux-modules wilt ontwikkelen, gebruikt u een Windows-computer die voldoet aan de [vereisten voor Docker Desktop.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Installeer [Visual Studio Code](https://code.visualstudio.com/) eerst en voeg vervolgens de volgende extensies toe:

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-extensie](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio extensie(s) specifiek voor de taal die u ontwikkelt in:
  - C#, inclusief Azure-functies: [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java Extension Pack voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++ extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

U moet ook een aantal aanvullende, taalspecifieke tools installeren om uw module te ontwikkelen:

- C#, inclusief Azure-functies: [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) en [Pip](https://pip.pypa.io/en/stable/installing/#installation) voor het installeren van Python-pakketten (meestal inbegrepen bij uw Python-installatie).

- Node.js: [Node.js](https://nodejs.org). U wilt ook [Yeoman](https://www.npmjs.com/package/yo) en de [Azure IoT Edge Node.js-modulegenerator](https://www.npmjs.com/package/generator-azure-iot-edge-module)installeren.

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) en [Maven](https://maven.apache.org/). U moet [de `JAVA_HOME` omgevingsvariabele instellen](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) om naar uw JDK-installatie te wijzen.

Als u de moduleafbeelding wilt maken en implementeren, hebt u Docker nodig om de moduleafbeelding en een containerregister te bouwen om de moduleafbeelding vast te houden:

- [Docker Community Edition](https://docs.docker.com/install/) op uw ontwikkelingsmachine.

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) of [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > U een lokaal Docker-register gebruiken voor prototype- en testdoeleinden in plaats van een cloudregister.

Tenzij u uw module in C ontwikkelt, hebt u ook de Op Python gebaseerde [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) nodig om uw lokale ontwikkelomgeving in te stellen om uw IoT Edge-oplossing te debuggen, uit te voeren en te testen. Als u dit nog niet hebt gedaan, installeert u [Python (2.7/3.6/3.7) en Pip](https://www.python.org/) en installeert u **iotedgehubdev** door deze opdracht in uw terminal uit te voeren.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Momenteel maakt iotedgehubdev gebruik van een docker-py-bibliotheek die niet compatibel is met Python 3.8.
>
> Als je meerdere Python hebt, inclusief vooraf geïnstalleerde python 2.7 (bijvoorbeeld op Ubuntu `pip` of `pip3` macOS), zorg er dan voor dat je de juiste gebruikt of **iotedgehubdev** installeert

Als u de module op een apparaat wilt testen, hebt u een actieve IoT-hub nodig met ten minste één IoT Edge-apparaat. Als u uw computer als IoT Edge-apparaat wilt gebruiken, voert u de stappen in de quickstart voor [Linux](quickstart-linux.md) of [Windows](quickstart.md)uit. Als u IoT Edge-daemon op uw ontwikkelingsmachine gebruikt, moet u mogelijk EdgeHub en EdgeAgent stoppen voordat u naar de volgende stap gaat.

## <a name="create-a-new-solution-template"></a>Een nieuwe oplossingssjabloon maken

In de volgende stappen ziet u hoe u een IoT Edge-module maakt in uw gewenste ontwikkelingstaal (inclusief Azure-functies, geschreven in C#) met behulp van Visual Studio Code en de Azure IoT-hulpprogramma's. U begint met het maken van een oplossing en vervolgens het genereren van de eerste module in die oplossing. Elke oplossing kan meerdere modules bevatten.

1. Selecteer**Opdrachtpalet** **weergeven** > .

1. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge Solution**in en voer deze uit.

   ![Nieuwe IoT Edge-oplossing uitvoeren](./media/how-to-develop-csharp-module/new-solution.png)

1. Blader naar de map waar u de nieuwe oplossing wilt maken en selecteer **Map selecteren**.

1. Voer een naam in voor uw oplossing.

1. Selecteer een modulesjabloon voor uw gewenste ontwikkelingstaal als eerste module in de oplossing.

1. Voer een naam in voor uw module. Kies een naam die uniek is in uw containerregister.

1. Geef de naam op van de afbeeldingsopslagplaats van de module. Visual Studio Code vult de modulenaam automatisch in met **localhost:5000/<de naam\>van uw module**. Vervang het door uw eigen registergegevens. Als u een lokaal Docker-register gebruikt voor het testen, is **localhost** prima. Als u Azure Container Registry gebruikt, gebruikt u de aanmeldingsserver vanuit de instellingen van uw register. De inlogserver ziet eruit als ** _ \<registernaam\>_.azurecr.io**. Vervang alleen het gedeelte **localhost:5000** van de tekenreeks, zodat het uiteindelijke resultaat lijkt ** \<op *registernaam*\>.azurecr.io/_\<uw modulenaam\>_**.

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code neemt de door u verstrekte informatie, maakt een IoT Edge-oplossing en laadt deze vervolgens in een nieuw venster.

Er zijn vier items in de oplossing:

- Een **.vscode-map** bevat foutopsporingsconfiguraties.

- Een **modulesmap** bevat submappen voor elke module.  In de map voor elke module is er een bestand, **module.json**, dat bepaalt hoe modules worden gebouwd en geïmplementeerd.  Dit bestand moet worden gewijzigd om het containerregister voor moduleimplementatie te wijzigen van localhost naar een extern register. Op dit moment heb je maar één module.  Maar u meer toevoegen in het opdrachtpalet met de opdracht **Azure IoT Edge: IoT Edge Module toevoegen.**

- Een **.env-bestand** bevat uw omgevingsvariabelen. Als Azure Container Registry uw register is, hebt u een gebruikersnaam en wachtwoord voor Azure Container Registry.

  > [!NOTE]
  > Het omgevingsbestand wordt alleen gemaakt als u een afbeeldingsopslagplaats voor de module opgeeft. Als u de standaardinstellingen voor localhost hebt geaccepteerd om lokaal te testen en te debuggen, hoeft u geen omgevingsvariabelen aan te geven.

- Een **bestand deployment.template.json** geeft een overzicht van uw nieuwe module, samen met een **voorbeeldmodule simulatedTemperatureSensor** die gegevens simuleert die u gebruiken voor het testen. Zie Meer informatie over hoe implementatiemanifesten werken, zie [Meer informatie over het gebruik van implementatiemanifesten om modules te implementeren en routes vast te stellen.](module-composition.md)

Als u wilt zien hoe de gesimuleerde temperatuurmodule werkt, bekijkt u de [broncode SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Extra modules toevoegen

Als u extra modules aan uw oplossing wilt toevoegen, voert u de opdracht **Azure IoT Edge: IoT Edge Module toe vanuit** het opdrachtpalet. U ook met **modules** de rechtermuisknop `deployment.template.json` op de map modules of het bestand in de visual studiocodeverkenner-weergave klikken en vervolgens **IoT Edge-module toevoegen selecteren.**

## <a name="develop-your-module"></a>Ontwikkel uw module

De standaardmodulecode die bij de oplossing wordt geleverd, bevindt zich op de volgende locatie:

- Azure Function (C#): **modules > * &lt;uw modulenaam&gt;* > *&lt;uw modulenaam&gt;*.cs**
- C#: **modules > * &lt;&gt; de naam van uw module* > Program.cs**
- Python: **modules > * &lt;&gt; uw modulenaam* > main.py**
- Node.js: **modules > * &lt;&gt; uw modulenaam* > app.js**
- Java: **modules > * &lt;&gt; je modulenaam* > src > main > java > com > edgemodulemodules > App.java**
- C: **modules > * &lt;&gt; uw modulenaam* > main.c**

De module en het bestand deployment.template.json zijn zo ingesteld dat u de oplossing bouwen, naar uw containerregister duwen en deze implementeren op een apparaat om te beginnen met testen zonder enige code aan te raken. De module is gebouwd om eenvoudig input van een bron (in dit geval, de module SimulatedTemperatureSensor te nemen die gegevens simuleert) en het aan IoT Hub te leiden.

Wanneer u klaar bent om de sjabloon aan te passen met uw eigen code, gebruikt u de [Azure IoT Hub SDK's](../iot-hub/iot-hub-devguide-sdks.md) om modules te bouwen die voldoen aan de belangrijkste behoeften voor IoT-oplossingen zoals beveiliging, apparaatbeheer en betrouwbaarheid.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Een module zonder container debuggen (C#, Node.js, Java)

Als u zich ontwikkelt in C#, Node.js of Java, vereist uw module het gebruik van een **Object ModuleClient** in de standaardmodulecode, zodat het berichten kan starten, uitvoeren en routeren. U gebruikt ook de **standaardinvoerkanaalinvoer1** om actie te ondernemen wanneer de module berichten ontvangt.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge-simulator instellen voor IoT Edge-oplossing

Op uw ontwikkelmachine u een IoT Edge-simulator starten in plaats van de IoT Edge-beveiligingsdaemon te installeren, zodat u uw IoT Edge-oplossing uitvoeren.

1. Klik in device explorer aan de linkerkant met de rechtermuisknop op de IE-id van IoT Edge en selecteer **Vervolgens IoT Edge Simulator instellen** om de simulator te starten met de tekenreeks voor apparaatverbinding.
1. U zien dat de IoT Edge Simulator is ingesteld door het lezen van de voortgang detail in de geïntegreerde terminal.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>IoT Edge-simulator instellen voor één module-app

Als u de simulator wilt instellen en starten, voert u de opdracht **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module uit** het opdrachtpalet Visual Studio Code. Gebruik de **waarde-invoer1** van de standaardmodulecode (of de equivalente waarde van uw code) als invoernaam voor uw toepassing wanneer u daarom wordt gevraagd. De opdracht activeert de **iotedgehubdev** CLI en start vervolgens de IoT Edge-simulator en een testmodulecontainer. U de uitgangen hieronder in de geïntegreerde terminal zien als de simulator is gestart in de modus met één module. U ook `curl` een opdracht zien om berichten door te sturen. U gebruikt dit later.

   ![IoT Edge-simulator instellen voor één module-app](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   U de docker explorer-weergave in Visual Studio Code gebruiken om de status van de module te bekijken.

   ![Simulatormodulestatus](media/how-to-develop-csharp-module/simulator-status.png)

   De **edgeHubDev-container** is de kern van de lokale IoT Edge-simulator. Het kan worden uitgevoerd op uw ontwikkelingsmachine zonder de IoT Edge security daemon en biedt omgevinginstellingen voor uw native module app of module containers. De **invoercontainer** stelt REST API's bloot om berichten te helpen bij het overbruggen van berichten naar het doelinvoerkanaal op uw module.

### <a name="debug-module-in-launch-mode"></a>Foutopsporingsmodule in de startmodus

1. Bereid uw omgeving voor op foutopsporing volgens de vereisten van uw ontwikkelingstaal, stel een breekpunt in uw module en selecteer de foutopsporingsconfiguratie die u wilt gebruiken:
   - **C #**
     - Wijzig in de geïntegreerde visualstudiocode de map in de *** &lt;map met&gt; *** de naam van uw module en voer de volgende opdracht uit om de .NET Core-toepassing te bouwen.

       ```cmd
       dotnet build
       ```

     - Open het `Program.cs` bestand en voeg een breekpunt toe.

     - Navigeer naar de foutopsporingsweergave Foutopsporings van de Visual Studio-code door **Weergave > foutopsporing te**selecteren. Selecteer de foutopsporingsconfiguratie ** * &lt;&gt; van uw modulenaam* Lokale foutopsporing (.NET Core)** in de vervolgkeuzelijst.

        > [!NOTE]
        > Als uw `TargetFramework` .NET-kern niet in `launch.json`overeenstemming is met uw programmapad in, `launch.json` moet `TargetFramework` u het programmapad handmatig bijwerken om het in uw .csproj-bestand te matchen, zodat Visual Studio Code dit programma met succes kan starten.

   - **Node.js**
     - Wijzig in de geïntegreerde visual studiocode de map in de *** &lt;map met de naam&gt; van uw module*** en voer de volgende opdracht uit om knooppuntpakketten te installeren

       ```cmd
       npm install
       ```

     - Open het `app.js` bestand en voeg een breekpunt toe.

     - Navigeer naar de foutopsporingsweergave Foutopsporings van de Visual Studio-code door **Weergave > foutopsporing te**selecteren. Selecteer de foutopsporingsconfiguratie ** * &lt;&gt; van uw modulenaam* Lokale foutopsporing (Node.js)** in de vervolgkeuzelijst.
   - **Java**
     - Open het `App.java` bestand en voeg een breekpunt toe.

     - Navigeer naar de foutopsporingsweergave Foutopsporings van de Visual Studio-code door **Weergave > foutopsporing te**selecteren. Selecteer de foutopsporingsconfiguratie ** * &lt;&gt; van uw modulenaam* Local Debug (Java)** in de vervolgkeuzelijst.

1. Klik **op Foutopsporing starten** of druk op **F5** om de foutopsporingssessie te starten.

1. Voer in de geïntegreerde visual studiocode-terminal de volgende opdracht uit om een **Hello World-bericht** naar uw module te verzenden. Dit is de opdracht die in eerdere stappen wordt weergegeven wanneer u de IoT Edge-simulator instelt.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Als u Windows gebruikt, zorgt u ervoor dat de shell van uw geïntegreerde Visual Studio Code-terminal **Git Bash** of **WSL Bash**is. U `curl` de opdracht niet uitvoeren vanuit een PowerShell- of opdrachtprompt.
   > [!TIP]
   > U ook [PostMan](https://www.getpostman.com/) of andere API-hulpprogramma's gebruiken om berichten te verzenden via in plaats van `curl`.

1. In de weergave Foutopsporings voor Visuele studiocode ziet u de variabelen in het linkerdeelvenster.

1. Als u uw foutopsporingssessie wilt stoppen, selecteert u de knop Stoppen of drukt u op **Shift + F5**en voert u **Azure IoT Edge: Stop IoT Edge Simulator** uit in het opdrachtpalet om de simulator te stoppen en op te schonen.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Foutopsporing in de invoegmodus met IoT Edge Simulator (C#, Node.js, Java, Azure Functions)

Uw standaardoplossing bevat twee modules, een is een gesimuleerde temperatuursensormodule en de andere is de pijpmodule. De gesimuleerde temperatuursensor stuurt berichten naar de pijpmodule en vervolgens worden de berichten naar de IoT-hub gestuurd. In de modulemap die u hebt gemaakt, zijn er verschillende Docker-bestanden voor verschillende containertypen. Gebruik een van de bestanden die eindigen met de extensie **.debug** om uw module te bouwen voor het testen.

Momenteel wordt foutopsporing in de attach-modus alleen als volgt ondersteund:

- C# modules, inclusief die voor Azure-functies, ondersteunen foutopsporing in Linux amd64-containers
- Node.js modules ondersteunen foutopsporing in Linux amd64 en arm32v7 containers, en Windows amd64 containers
- Java-modules ondersteunen foutopsporing in Linux amd64- en arm32v7-containers

> [!TIP]
> U schakelen tussen opties voor het standaardplatform voor uw IoT Edge-oplossing door op het item in de statusbalk van visual studiocode te klikken.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge-simulator instellen voor IoT Edge-oplossing

In uw ontwikkelmachine u een IoT Edge-simulator starten in plaats van de IoT Edge-beveiligingsdaemon te installeren, zodat u uw IoT Edge-oplossing uitvoeren.

1. Klik in device explorer aan de linkerkant met de rechtermuisknop op de IE-id van IoT Edge en selecteer **Vervolgens IoT Edge Simulator instellen** om de simulator te starten met de tekenreeks voor apparaatverbinding.

1. U zien dat de IoT Edge Simulator is ingesteld door het lezen van de voortgang detail in de geïntegreerde terminal.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Container bouwen en uitvoeren voor foutopsporing en foutopsporing in de invoegmodus

1. Open het modulebestand `app.js` `App.java`(`Program.cs` `<your module name>.cs`, , of ) en voeg een breekpunt toe.

1. Klik in de weergave Visual Studio `deployment.debug.template.json` Code Explorer met de rechtermuisknop op het bestand voor uw oplossing en selecteer vervolgens **de Oplossing IoT Edge bouwen en uitvoeren in Simulator**. U alle modulecontainerlogboeken in hetzelfde venster bekijken. U ook naar de dockerweergave navigeren om de containerstatus te bekijken.

   ![Variabelen bekijken](media/how-to-vs-code-develop-module/view-log.png)

1. Navigeer naar de foutopsporingsweergave Visual Studio Code en selecteer het foutopsporingsconfiguratiebestand voor uw module. De naam van de foutopsporingsoptie moet vergelijkbaar ** * &lt;&gt; * zijn met de naam Externe foutopsporing van uw module**

1. Selecteer **Foutopsporing starten** of druk op **F5**. Selecteer het proces waaraan u wilt koppelen.

1. In de foutopsporingsweergave Van de Visual Studio-code ziet u de variabelen in het linkerdeelvenster.

1. Als u de foutopsporingssessie wilt stoppen, selecteert u eerst de knop Stoppen of drukt u op **Shift + F5**en selecteert u **Azure IoT Edge: Stop IoT Edge Simulator** in het opdrachtpalet.

> [!NOTE]
> In het voorgaande voorbeeld ziet u hoe u IoT Edge-modules op containers debuggen. Het voegde blootgestelde poorten toe `createOptions` aan de containerinstellingen van uw module. Nadat u klaar bent met het debuggen van uw modules, raden we u aan deze blootgestelde poorten te verwijderen voor iot edge-modules die klaar zijn voor productie.
>
> Voor modules die zijn geschreven in C#, inclusief Azure-functies, is dit voorbeeld gebaseerd op de foutopsporingsversie van `Dockerfile.amd64.debug`, die de .NET Core command-line debugger (VSDBG) in uw containerafbeelding bevat tijdens het bouwen. Nadat u uw C#-modules hebt gedebugd, raden we u aan het Dockerfile zonder VSDBG direct te gebruiken voor iot edge-modules die productieklaar zijn.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Een module met de runtime van IoT Edge debuggen

In elke modulemap zijn er verschillende Docker-bestanden voor verschillende containertypen. Gebruik een van de bestanden die eindigen met de extensie **.debug** om uw module te bouwen voor het testen.

Wanneer u modules met deze methode debugadt, worden uw modules uitgevoerd bovenop de runtime van IoT Edge. Het IoT Edge-apparaat en uw Visual Studio Code kunnen op dezelfde machine staan, of meer typisch, Visual Studio Code bevindt zich op de ontwikkelingsmachine en de IoT Edge-runtime en modules draaien op een andere fysieke machine. Als u debuggen vanuit Visual Studio Code, moet u:

- Stel uw IoT Edge-apparaat in, bouw uw IoT Edge-module(s) met het **.debug** Dockerfile en implementeer vervolgens naar het IoT Edge-apparaat.
- Stel het IP- en de poort van de module bloot, zodat de foutopsporing kan worden bevestigd.
- Werk `launch.json` de code zo bij dat Visual Studio Code kan worden gekoppeld aan het proces in de container op de externe machine. Dit bestand bevindt `.vscode` zich in de map in uw werkruimte en wordt bijgewerkt telkens wanneer u een nieuwe module toevoegt die foutopsporing ondersteunt.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Uw module bouwen en implementeren op het IoT Edge-apparaat

1. Open in Visual Studio `deployment.debug.template.json` Code het bestand, dat de foutopsporingsversie `createOptions` van uw moduleafbeeldingen bevat met de juiste waardenset.

1. Als u uw module in Python ontwikkelt, voert u de volgende stappen uit voordat u verdergaat:
   - Open het `main.py` bestand en voeg deze code toe na de importsectie:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Voeg de volgende regel code toe aan de callback die u wilt debuggen:

      ```python
      ptvsd.break_into_debugger()
      ```

     Als u bijvoorbeeld de `receive_message_listener` functie wilt debuggen, voegt u die coderegel in zoals hieronder wordt weergegeven:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. Ga als een opdracht met de opdracht Visual Studio Code:
   1. Voer de oplossing **Azure IoT Edge: Build and Push IoT Edge**uit.

   1. Selecteer `deployment.debug.template.json` het bestand voor uw oplossing.

1. In de sectie **Azure IoT Hub Devices** van de visual studio code explorer-weergave:
   1. Klik met de rechtermuisknop op een IoT Edge-apparaat-id en selecteer **Vervolgens Implementatie maken voor één apparaat**.

      > [!TIP]
      > Als u wilt bevestigen dat het apparaat dat u hebt gekozen een IoT Edge-apparaat is, selecteert u het apparaat om de lijst met modules uit te breiden en de aanwezigheid van **$edgeHub** en **$edgeAgent**te verifiëren. Elk IoT Edge-apparaat bevat deze twee modules.

   1. Navigeer naar de **config-map** van `deployment.debug.amd64.json` uw oplossing, selecteer het bestand en selecteer **Vervolgens Edge Deployment Manifest selecteren.**

U ziet de implementatie die is gemaakt met een implementatie-id in de geïntegreerde terminal.

U de status van `docker ps` uw container controleren door de opdracht in de terminal uit te voeren. Als uw Visual Studio Code en IoT Edge runtime op dezelfde machine worden uitgevoerd, u ook de status controleren in de Visual Studio Code Docker-weergave.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Het IP-adres en de poort van de module voor de foutopsporing blootzetten

U deze sectie overslaan als uw modules op dezelfde machine als Visual Studio Code worden uitgevoerd, omdat u localhost gebruikt om aan de `createOptions` container te `launch.json` koppelen en al de juiste poortinstellingen hebt in het **.debug** Dockerfile, de containerinstellingen en het bestand van de module. Als uw modules en Visual Studio Code op afzonderlijke machines worden uitgevoerd, volgt u de stappen voor uw ontwikkelingstaal.

- **C#, inclusief Azure-functies**

  [Configureer het SSH-kanaal op uw ontwikkelmachine en IoT Edge-apparaat](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) en bewerk `launch.json` het bestand om te koppelen.

- **Node.js**

  - Zorg ervoor dat de module op de te debugged machine is uitgevoerd en klaar voor debuggers te bevestigen, en dat poort 9229 is extern toegankelijk. U dit `http://<target-machine-IP>:9229/json` verifiëren door het openen op de foutopsporingsmachine. Deze URL moet informatie weergeven over de Node.js-module die moet worden gedebugged.
  
  - Open visual studiocode op uw ontwikkelingsmachine `launch.json` en bewerk vervolgens zodat de adreswaarde van het ** * &lt;&gt; * profiel Remote Debug (Node.js)** van uw module (of ** * &lt;uw modulenaam&gt; * Remote Debug (Node.js in Windows Container)** is als de module als Windows-container wordt uitgevoerd, het IP-adres van de machine die wordt gedebugged.

- **Java**

  - Bouw een SSH-tunnel naar de machine `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`te worden ontbugged door te draaien .
  
  - Open op uw ontwikkelmachine Visual Studio Code en bewerk het `launch.json` ** * &lt;&gt; * profiel van uw modulenaam Remote Debug (Java)** zodat u koppelen aan de doelmachine. Zie de sectie over `launch.json` [het configureren van de foutopsporing](https://code.visualstudio.com/docs/java/java-debugging#_configuration)voor meer informatie over het bewerken en debuggen van Java met Visual Studio Code.

- **Python**

  - Zorg ervoor dat poort 5678 op de te ontluchten machine open en toegankelijk is.

  - Wijzig in `ptvsd.enable_attach(('0.0.0.0', 5678))` de code waarin `main.py`u eerder hebt ingevoegd , **0.0.0.0** naar het IP-adres van de te debugged te worden verwijderd. Bouw, duw en implementeer uw IoT Edge-module opnieuw.

  - Open visual studiocode op uw ontwikkelingsmachine `launch.json` en `host` bewerk vervolgens zodat de waarde van het **python-profiel * &lt;(Remote&gt; * Debug)** van uw module het IP-adres van de doelmachine gebruikt in plaats van `localhost`.

### <a name="debug-your-module"></a>Uw module debuggen

1. Selecteer in de weergave Foutopsporingsvan de Visual Studio-code het foutopsporingsconfiguratiebestand voor uw module. De naam van de foutopsporingsoptie moet vergelijkbaar ** * &lt;&gt; * zijn met de naam Externe foutopsporing van uw module**

1. Open het modulebestand voor uw ontwikkelingstaal en voeg een breekpunt toe:

   - **Azure- functie (C#)**: Voeg `<your module name>.cs`uw breekpunt toe aan het bestand .
   - **C#**: Voeg uw `Program.cs`breekpunt toe aan het bestand .
   - **Node.js**: Voeg uw `app.js`breekpunt toe aan het bestand.
   - **Java**: Voeg uw `App.java`breekpunt toe aan het bestand.
   - **Python:** Voeg uw breekpunt toe aan het `main.py`bestand `ptvsd.break_into_debugger()` in de callbackmethode waarbij u de regel hebt toegevoegd.
   - **C**: Voeg uw `main.c`breekpunt toe aan het bestand .

1. Selecteer **Foutopsporing starten** of selecteer **F5**. Selecteer het proces waaraan u wilt koppelen.

1. In de weergave Foutopsporings voor Visuele studiocode ziet u de variabelen in het linkerdeelvenster.

> [!NOTE]
> In het voorgaande voorbeeld ziet u hoe u IoT Edge-modules op containers debuggen. Het voegde blootgestelde poorten toe `createOptions` aan de containerinstellingen van uw module. Nadat u klaar bent met het debuggen van uw modules, raden we u aan deze blootgestelde poorten te verwijderen voor iot edge-modules die klaar zijn voor productie.

## <a name="build-and-debug-a-module-remotely"></a>Een module op afstand bouwen en debuggen

Met recente wijzigingen in zowel de Docker- als moby-engines om SSH-verbindingen te ondersteunen en een nieuwe instelling in Azure IoT-hulpprogramma's die het mogelijk maakt omgevingsinstellingen in het opdrachtpalet Visual Studio Code en Azure IoT Edge-terminals in te voeren, u nu bouwen en debuggen modules op externe apparaten.

Zie dit [IoT Developer-blogbericht](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) voor meer informatie en stapsgewijze instructies.

## <a name="next-steps"></a>Volgende stappen

Nadat u de module hebt gebouwd, leest u hoe [u Azure IoT Edge-modules implementeert vanuit Visual Studio Code.](how-to-deploy-modules-vscode.md)

Als u modules wilt ontwikkelen voor uw IoT Edge-apparaten, [begrijpt en gebruikt u Azure IoT Hub SDKs.](../iot-hub/iot-hub-devguide-sdks.md)
