---
title: Modules ontwikkelen en debuggen in Visual Studio - Azure IoT Edge
description: Gebruik Visual Studio met Azure IoT-hulpprogramma's om een C- of C#IoT Edge-module te ontwikkelen en deze van uw IoT-hub naar een IoT-apparaat te pushen, zoals geconfigureerd door een implementatiemanifest.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384854"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio 2019 gebruiken om modules voor Azure IoT Edge te ontwikkelen en te debuggen

U uw bedrijfslogica omzetten in modules voor Azure IoT Edge. In dit artikel zie je hoe je Visual Studio 2019 gebruiken als het belangrijkste hulpmiddel om modules te ontwikkelen en te debuggen.

De Azure IoT Edge Tools voor Visual Studio biedt de volgende voordelen:

- Azure IoT Edge-oplossingen en -modules maken, bewerken, bouwen, uitvoeren en debuggen op uw lokale ontwikkelingscomputer.
- Implementeer uw Azure IoT Edge-oplossing op Azure IoT Edge-apparaat via Azure IoT Hub.
- Codeer uw Azure IoT-modules in C of C# terwijl u alle voordelen van de ontwikkeling van Visual Studio hebt.
- Azure IoT Edge-apparaten en -modules beheren met gebruikersinterface.

In dit artikel ziet u hoe u de Azure IoT Edge-hulpprogramma's voor Visual Studio 2019 gebruiken om uw IoT Edge-modules te ontwikkelen. U leert ook hoe u uw project implementeert op uw Azure IoT Edge-apparaat. Momenteel biedt Visual Studio 2019 ondersteuning voor modules geschreven in C en C#. De ondersteunde apparaatarchitecturen zijn Windows X64 en Linux X64 of ARM32. Zie Ondersteuning voor [taal en architectuur](module-development.md#language-and-architecture-support)voor meer informatie over ondersteunde besturingssystemen, talen en architecturen.
  
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een computer of virtuele machine met Windows als ontwikkelingsmachine gebruikt. Op Windows-computers u Windows- of Linux-modules ontwikkelen. Als u Windows-modules wilt ontwikkelen, gebruikt u een Windows-computer met versie 1809/build 17763 of nieuwer. Als u Linux-modules wilt ontwikkelen, gebruikt u een Windows-computer die voldoet aan de [vereisten voor Docker Desktop.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Omdat dit artikel Visual Studio 2019 als belangrijkste ontwikkeltool gebruikt, installeert u Visual Studio. Zorg ervoor dat u de **Azure-ontwikkeling** en **desktopontwikkeling opneemt met C++** workloads in uw Visual Studio 2019-installatie. U [Visual Studio 2019 wijzigen](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) om de vereiste workloads toe te voegen.

Nadat je Visual Studio 2019 klaar is, heb je ook de volgende tools en componenten nodig:

- Download en installeer [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) van de Visual Studio-marktplaats om een IoT Edge-project te maken in Visual Studio 2019.

> [!TIP]
> Als u Visual Studio 2017 gebruikt, download t/m De Markt [voor De IE](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)

- Download en installeer [Docker Community Edition](https://docs.docker.com/install/) op je ontwikkelmachine om je moduleafbeeldingen te bouwen en uit te voeren. U moet Docker CE instellen om in de Linux-containermodus of windows-containermodus te worden uitgevoerd.

- Stel uw lokale ontwikkelomgeving in om uw IoT Edge-oplossing te debuggen, uit te voeren en te testen door het [Azure IoT EdgeHub Dev Tool te](https://pypi.org/project/iotedgehubdev/)installeren. Installeer [Python (2.7/3.6+) en Pip](https://www.python.org/) en installeer vervolgens het **iotedgehubdev-pakket** door de volgende opdracht in uw terminal uit te voeren. Controleer of uw Azure IoT EdgeHub Dev Tool-versie groter is dan 0,3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Kloon de repository en installeer de Vcpkg-bibliotheekbeheerder en installeer vervolgens het **azure-iot-sdk-c-pakket** voor Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) of [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > U een lokaal Docker-register gebruiken voor prototype- en testdoeleinden in plaats van een cloudregister.

- Als u de module op een apparaat wilt testen, hebt u een actieve IoT-hub nodig met ten minste één IoT Edge-apparaat. Als u uw computer als IoT Edge-apparaat wilt gebruiken, voert u de stappen in de quickstart voor [Linux](quickstart-linux.md) of [Windows](quickstart.md)uit. Als u IoT Edge-daemon op uw ontwikkelingsmachine gebruikt, moet u mogelijk EdgeHub en EdgeAgent stoppen voordat u met de ontwikkeling in Visual Studio begint.

### <a name="check-your-tools-version"></a>Uw proefversie van uw hulpprogramma's controleren

1. Selecteer **Extensies beheren**in het menu **Extensies** . Uitvouwen **> Hulpprogramma's** en u **Azure IoT Edge-hulpprogramma's voor Visual Studio** en Cloud Explorer voor Visual **Studio**vinden.

1. Let op de geïnstalleerde versie. U deze versie vergelijken met de nieuwste versie op Visual Studio Marketplace[(Cloud Explorer,](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019) [Azure IoT Edge)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)

1. Als uw versie ouder is dan wat beschikbaar is op Visual Studio Marketplace, werkt u uw hulpprogramma's in Visual Studio bij, zoals in de volgende sectie wordt weergegeven.

### <a name="update-your-tools"></a>Uw hulpprogramma's bijwerken

1. Vouw in het venster **Extensies beheren** updates uit > Visual **Studio Marketplace,** selecteer **Azure IoT Edge Tools** of Cloud Explorer voor Visual **Studio** en selecteer **Bijwerken**.

1. Nadat de hulpprogramma-update is gedownload, sluit u Visual Studio om de hulpprogramma-update te activeren met behulp van het VSIX-installatieprogramma.

1. Selecteer IN het installatieprogramma **OK** om te starten en **wijzig vervolgens** om de hulpprogramma's bij te werken.

1. Nadat de update is voltooid, selecteert u Visual Studio **sluiten** en opnieuw starten.

### <a name="create-an-azure-iot-edge-project"></a>Een Azure IoT Edge-project maken

De Azure IoT Edge-projectsjabloon in Visual Studio maakt een project dat kan worden geïmplementeerd op Azure IoT Edge-apparaten in Azure IoT Hub. Eerst maakt u een Azure IoT Edge-oplossing en vervolgens de eerste module in die oplossing. Elke IoT Edge-oplossing kan meer dan één module bevatten.

> [!TIP]
> De IoT Edge-projectstructuur die door Visual Studio is gemaakt, is niet hetzelfde als in Visual Studio Code.

1. Zoek in het nieuwe projectdialoogvenster van Visual Studio azure **IoT Edge-project** en klik op **Volgende**. Voer in het venster projectconfiguratie een naam voor uw project in en geef de locatie op en selecteer **Vervolgens Maken**. De standaardprojectnaam is **AzureIoTEdgeApp1**.

   ![Nieuw project maken](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Selecteer in het venster **IoT Edge-toepassing en module toevoegen** de optie **C# Module** of **C-module** en geef vervolgens de modulenaam en moduleafbeeldingsopslagplaats op. Visual Studio vult de modulenaam automatisch in met **localhost:5000/<uw modulenaam\>**. Vervang het door uw eigen registergegevens. Als u een lokaal Docker-register gebruikt voor het testen, is **localhost** prima. Als u Azure Container Registry gebruikt, gebruikt u de aanmeldingsserver vanuit de instellingen van uw register. De inlogserver ziet eruit als ** _ \<registernaam\>_.azurecr.io**. Vervang alleen het gedeelte **localhost:5000** van de tekenreeks, zodat het uiteindelijke resultaat lijkt ** \<op *registernaam*\>.azurecr.io/_\<uw modulenaam\>_**. De standaardmodulenaam is **IotEdgeModule1**

   ![Toepassing en module toevoegen](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Selecteer **OK** om de Azure IoT Edge-oplossing te maken met een module die C# of C gebruikt.

Nu hebt u een **AzureIoTEdgeApp1.Linux.Amd64-project** of een **AzureIoTEdgeApp1.Windows.Amd64-project** en ook een **IotEdgeModule1-project** in uw oplossing. Elk **AzureIoTEdgeApp1-project** heeft een `deployment.template.json` bestand, dat de modules definieert die u wilt bouwen en implementeren voor uw IoT Edge-oplossing, en bepaalt ook de routes tussen modules. De standaardoplossing heeft een **SimulatedTemperatureSensor-module** en een **IotEdgeModule1-module.** De **module SimulatedTemperatureSensor** genereert gesimuleerde gegevens naar de **IotEdgeModule1-module,** terwijl de standaardcode in de **IotEdgeModule1-module** rechtstreeks ontvangen berichten naar Azure IoT Hub leidt.

Bekijk de [broncode SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)om te zien hoe de gesimuleerde temperatuursensor werkt.

Het **IotEdgeModule1-project** is een .NET Core 2.1-consoletoepassing als het een C#-module is. Het bevat vereiste Docker-bestanden die u nodig hebt voor uw IoT Edge-apparaat dat wordt uitgevoerd met een Windows-container of Linux-container. Het `module.json` bestand beschrijft de metagegevens van een module. De werkelijke modulecode, die Azure IoT Device SDK als `Program.cs` afhankelijkheid `main.c` neemt, wordt gevonden in het of-bestand.

## <a name="develop-your-module"></a>Ontwikkel uw module

De standaardmodulecode die bij de oplossing wordt geleverd, bevindt zich bij **IotEdgeModule1** > **Program.cs** (voor C#) of **main.c** (C). De module `deployment.template.json` en het bestand zijn zo ingesteld dat u de oplossing bouwen, naar uw containerregister duwen en deze implementeren op een apparaat om te beginnen met testen zonder enige code aan te raken. De module is gebouwd om input te nemen van een bron (in dit geval de **SimulatedTemperatureSensor-module** die gegevens simuleert) en deze door te geven aan Azure IoT Hub.

Wanneer u klaar bent om de modulesjabloon aan te passen met uw eigen code, gebruikt u de [Azure IoT Hub SDK's](../iot-hub/iot-hub-devguide-sdks.md) om modules te bouwen die voldoen aan de belangrijkste behoeften voor IoT-oplossingen zoals beveiliging, apparaatbeheer en betrouwbaarheid.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Iotedgehubdev initialiseren met de verbindingstekenreeks van iot edge-apparaten

1. Kopieer de verbindingstekenreeks van een IoT Edge-apparaat vanuit **primaire verbindingstekenreeks** in de Visual Studio Cloud Explorer. Zorg ervoor dat u de verbindingstekenreeks van een niet-Edge-apparaat niet kopieert, omdat het pictogram van een IoT Edge-apparaat verschilt van het pictogram van een niet-edge-apparaat.

   ![Randapparaatverbindingstekenreeks kopiëren](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Selecteer **in** het menu Extra de optie **Azure IoT Edge Tools** > **Setup IoT Edge Simulator,** plak de verbindingstekenreeks en klik op **OK**.

   ![Venster Tekenreekstekenreeks voor rand instellen openen](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Voer de verbindingstekenreeks in vanaf de eerste stap en selecteer **OK**.

> [!NOTE]
> U hoeft deze stappen slechts één keer te volgen op uw ontwikkelcomputer, omdat de resultaten automatisch worden toegepast op alle volgende Azure IoT Edge-oplossingen. Deze procedure kan opnieuw worden gevolgd als u moet overschakelen naar een andere verbindingstekenreeks.

## <a name="build-and-debug-single-module"></a>Enkele module bouwen en debuggen

Normaal gesproken wilt u elke module testen en debuggen voordat u deze uitvoert binnen een hele oplossing met meerdere modules.

1. Klik in **Solution Explorer**met de rechtermuisknop op **IotEdgeModule1** en selecteer **Instellen als StartUp-project** in het contextmenu.

   ![Startproject instellen](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Druk op **F5** of klik op de onderstaande knop om de module uit te voeren; het kan 10&ndash;20 seconden duren de eerste keer dat je dit doet.

   ![Module uitvoeren](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. U ziet een .NET Core-console-app starten als de module is geïnitialiseerd.

   ![Module actief](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Als u zich ontwikkelt in C#, stelt u een breekpunt in de `PipeMessage()` functie in **Program.cs**; bij gebruik van C stelt `InputQueue1Callback()` u een breekpunt in in de functie in **main.c**. U het vervolgens testen door een bericht te verzenden door de volgende opdracht uit te voeren in een **Git Bash-** of **WSL** Bash-shell. (U `curl` de opdracht niet uitvoeren vanuit een PowerShell- of opdrachtprompt.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Foutopsporingse enkele module](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Het breekpunt moet worden geactiveerd. Je variabelen bekijken in het venster Visual Studio **Locals.**

   > [!TIP]
   > U ook [PostMan](https://www.getpostman.com/) of andere API-hulpprogramma's gebruiken om berichten te verzenden in plaats van `curl`.

1. Druk op **Ctrl + F5** of klik op de stopknop om foutopsporing te stoppen.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>IoT Edge-oplossing bouwen en debuggen met meerdere modules

Nadat u klaar bent met het ontwikkelen van één module, wilt u misschien een hele oplossing met meerdere modules uitvoeren en debuggen.

1. Voeg in **Solution Explorer**een tweede module toe aan de oplossing door met de rechtermuisknop op **AzureIoTEdgeApp1** te klikken en**Nieuwe IoT Edge-module** **toevoegen te** > selecteren. De standaardnaam van de tweede module is **IotEdgeModule2** en fungeert als een andere pijpmodule.

1. Open het `deployment.template.json` bestand en u ziet **iotEdgeModule2** is toegevoegd in de **modules** sectie. Vervang de **sectie routes** door het volgende. Als u de namen van uw module hebt aangepast, moet u deze namen bijwerken zodat deze overeenkomen.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Klik met de rechtermuisknop op **AzureIoTEdgeApp1** en selecteer **Instellen als StartUp-project** in het contextmenu.

1. Maak je breekpunten en druk op **F5** om meerdere modules tegelijk uit te voeren en te debuggen. U ziet meerdere vensters van de .NET Core-console-app, die elk venster een andere module vertegenwoordigen.

   ![Foutopsporing meerdere modules](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Druk op **Ctrl + F5** of selecteer de stopknop om foutopsporing te stoppen.

## <a name="build-and-push-images"></a>Afbeeldingen maken en pushen

1. Zorg ervoor dat **AzureIoTEdgeApp1** het opstartproject is. Selecteer **Foutopsporing** of **Vrijgeven** als de configuratie die u wilt maken voor uw moduleafbeeldingen.

    > [!NOTE]
    > Bij het kiezen van **Foutopsporing**gebruikt `Dockerfile.(amd64|windows-amd64).debug` Visual Studio om Docker-afbeeldingen te maken. Dit omvat de .NET Core-opdrachtregelfouter VSDBG in uw containerafbeelding tijdens het bouwen. Voor iot edge-modules die productieklaar zijn, raden we `Dockerfile.(amd64|windows-amd64)` u aan de **Release-configuratie** te gebruiken, die zonder VSDBG wordt gebruikt.

1. Als u een privéregister zoals Azure Container Registry (ACR) gebruikt, gebruikt u de volgende opdracht Docker om u aan te melden.  U de gebruikersnaam en het wachtwoord ophalen op de pagina **Toegangssleutels** van uw register in de Azure-portal. Als u lokaal register gebruikt, u [een lokaal register uitvoeren.](https://docs.docker.com/registry/deploying/#run-a-local-registry)

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Als u een privéregister zoals Azure Container Registry gebruikt, moet u uw inloggegevens `deployment.template.json`toevoegen aan de runtime-instellingen in het bestand. Vervang de tijdelijke aanduidingen door uw werkelijke gebruikersnaam, wachtwoord en registernaam van de ACR-beheerder.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Klik in **Solution Explorer**met de rechtermuisknop op **AzureIoTEdgeApp1** en selecteer **IoT Edge-modules bouwen en pushen** om de Docker-afbeelding voor elke module te bouwen en te pushen.

## <a name="deploy-the-solution"></a>De oplossing implementeren

In dit snelstartartikel voor het instellen van uw IoT Edge-apparaat hebt u een module geïmplementeerd met behulp van de Azure-portal. U ook modules implementeren met de Cloud Explorer voor Visual Studio. U hebt al een implementatiemanifest voorbereid `deployment.json` op uw scenario, het bestand en alles wat u hoeft te doen is een apparaat selecteren om de implementatie te ontvangen.

1. Open **Cloud Explorer** door op Cloud Explorer **weergeven** > **Cloud Explorer**te klikken . Zorg ervoor dat je bent ingelogd bij Visual Studio 2019.

1. Vouw in **Cloud Explorer**uw abonnement uit, zoek uw Azure IoT Hub en het Azure IoT Edge-apparaat dat u wilt implementeren.

1. Klik met de rechtermuisknop op het IoT Edge-apparaat om er een implementatie voor te maken. Navigeer naar het implementatiemanifest dat is geconfigureerd voor uw platform in de `deployment.arm32v7.json` **config-map** in uw Visual Studio-oplossing, zoals .

1. Klik op de vernieuwingsknop om de nieuwe modules samen met de **module SimulatedTemperatureSensor** te zien en **$edgeAgent** en **$edgeHub.**

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

1. Als u het D2C-bericht voor een specifiek IoT-Edge-apparaat wilt controleren, selecteert u het in uw IoT-hub in **Cloud Explorer** en klikt u vervolgens op **Het ingebouwde gebeurteniseindpunt** starten in het **actievenster.**

1. Als u de controlegegevens wilt stoppen, selecteert u **Het ingebouwde gebeurteniseindpunt stoppen** in het **actievenster.**

## <a name="next-steps"></a>Volgende stappen

Als u aangepaste modules wilt ontwikkelen voor uw IoT Edge-apparaten, [begrijpt en gebruikt u Azure IoT Hub SDKs.](../iot-hub/iot-hub-devguide-sdks.md)
