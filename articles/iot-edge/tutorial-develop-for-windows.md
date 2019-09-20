---
title: Module ontwikkelen voor Windows-apparaten-Azure IoT Edge | Microsoft Docs
description: In deze zelf studie wordt uitgelegd hoe u uw ontwikkel computer-en cloud resources instelt voor het ontwikkelen van IoT Edge modules met behulp van Windows-containers voor Windows-apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/15/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: f62698cc736bb7df51d013199d3054b25598240e
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147403"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Zelfstudie: IoT Edge-modules ontwikkelen voor Windows-apparaten

Gebruik Visual Studio om code te ontwikkelen en te implementeren op Windows-apparaten met IoT Edge.

In de Snelstartgids hebt u een IoT Edge apparaat gemaakt met behulp van een virtuele Windows-machine en een vooraf gemaakte module geïmplementeerd vanuit Azure Marketplace. In deze zelf studie wordt uitgelegd wat u nodig hebt om uw eigen code te ontwikkelen en implementeren op een IoT Edge apparaat. Deze zelf studie is een nuttige vereiste voor de andere zelf studies, die meer details over specifieke programmeer talen of Azure-Services gaan. 

In deze zelf studie wordt het voor beeld van het implementeren van een  **C# module op een Windows-apparaat**gebruikt. Dit voor beeld is gekozen omdat het het meest voorkomende ontwikkel scenario is. Als u in een andere taal wilt ontwikkelen of als u Azure-Services wilt implementeren als modules, is deze zelf studie nog steeds nuttig voor meer informatie over de ontwikkel hulpprogramma's. Wanneer u de ontwikkelings concepten begrijpt, kunt u de gewenste taal of Azure-service kiezen voor meer informatie. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw ontwikkel machine in.
> * Gebruik de IoT Edge-hulpprogram ma's voor Visual Studio om een nieuw project te maken.
> * Bouw uw project als een container en sla het op in een Azure container Registry.
> * Implementeer uw code op een IoT Edge apparaat. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Belangrijkste concepten

In deze zelf studie wordt de ontwikkeling van een IoT Edge module door lopen. Een *IOT Edge module*, of soms alleen *module* voor kort, is een container die uitvoer bare code bevat. U kunt een of meer modules implementeren op een IoT Edge apparaat. Modules voeren specifieke taken uit, zoals het opnemen van gegevens van Sens oren, het uitvoeren van gegevens analyses of het opschonen van gegevens, of het verzenden van berichten naar een IoT-hub. Zie voor meer informatie [Azure IOT Edge modules begrijpen](iot-edge-modules.md).

Wanneer u IoT Edge modules ontwikkelt, is het belang rijk dat u begrijpt wat het verschil is tussen de ontwikkelings machine en het doel IoT Edge apparaat waar de module uiteindelijk zal worden geïmplementeerd. De container die u voor de module code bouwt, moet overeenkomen met het besturings systeem (OS) van het *doel apparaat*. Voor Windows-container ontwikkeling is dit concept eenvoudiger omdat Windows-containers alleen worden uitgevoerd op Windows-besturings systemen. Maar u kunt bijvoorbeeld uw Windows-ontwikkel computer gebruiken om modules voor Linux IoT Edge-apparaten te bouwen. In dat scenario moet u ervoor zorgen dat er Linux-containers op uw ontwikkel machine werden uitgevoerd. Als u deze zelf studie doorloopt, moet u het verschil tussen het *besturings systeem van de ontwikkel machine* en het *container besturingssysteem*onthouden.

Deze zelf studie is gericht op Windows-apparaten met IoT Edge. Windows-IoT Edge apparaten gebruiken Windows-containers. U kunt het beste Visual Studio gebruiken voor het ontwikkelen van Windows-apparaten, zodat deze zelf studie wordt gebruikt. U kunt ook Visual Studio code gebruiken, hoewel er verschillen zijn in ondersteuning tussen de twee hulpprogram ma's.

De volgende tabel geeft een overzicht van de ondersteunde ontwikkelings scenario's voor **Windows-containers** in Visual Studio code en Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-Services** | Azure Functions <br> Azure Stream Analytics |   |
| **Talen** | C#(fout opsporing wordt niet ondersteund) | C <br> C# |
| **Meer informatie** | [Azure IoT Edge voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-Hulpprogram Ma's voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge-Hulpprogram Ma's voor Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Vereisten

Een ontwikkel computer:

* Windows 10 met 1809-update of nieuwer.
* U kunt uw eigen computer of een virtuele machine gebruiken, afhankelijk van uw ontwikkelings voorkeuren.
* Installeer [Git](https://git-scm.com/). 

Een Azure IoT Edge apparaat in Windows:

* Het is raadzaam om IoT Edge niet uit te voeren op uw ontwikkel computer, maar u kunt in plaats daarvan een afzonderlijk apparaat gebruiken. Dit onderscheid tussen de ontwikkel machine en het IoT Edge apparaat is nauw keuriger in een echt implementatie scenario en helpt de verschillende concepten direct te blijven gebruiken.
* Als er nog geen tweede apparaat beschikbaar is, gebruikt u het Quick Start-artikel om een IoT Edge apparaat in azure te maken met een [virtuele Windows-machine](quickstart.md).

Cloudresources:

* Een gratis of een IOT- [hub](../iot-hub/iot-hub-create-through-portal.md) met een standaard laag in Azure. 

## <a name="install-container-engine"></a>Container Engine installeren

IoT Edge-modules worden verpakt als containers, dus u hebt een container engine op uw ontwikkel computer nodig om de containers te bouwen en te beheren. Het is raadzaam docker Desktop te gebruiken voor ontwikkeling vanwege de vele functies en populariteit als een container engine. Met docker Desktop op een Windows-computer kunt u scha kelen tussen Linux-containers en Windows-containers, zodat u eenvoudig modules kunt ontwikkelen voor verschillende soorten IoT Edge apparaten. 

Gebruik de docker-documentatie om te installeren op uw ontwikkel computer: 

* [Docker Desktop voor Windows installeren](https://docs.docker.com/docker-for-windows/install/)

  * Wanneer u docker Desktop voor Windows installeert, wordt u gevraagd of u Linux-of Windows-containers wilt gebruiken. Gebruik voor deze zelf studie **Windows-containers**. Zie [scha kelen tussen Windows-en Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)voor meer informatie.


## <a name="set-up-visual-studio-and-tools"></a>Visual Studio en hulpprogram ma's instellen

De IoT-uitbrei dingen voor Visual Studio helpen u bij het ontwikkelen van IoT Edge-modules. Deze uitbrei dingen bieden project sjablonen, automatiseren het maken van het implementatie manifest en bieden u de mogelijkheid om IoT Edge apparaten te bewaken en te beheren. In deze sectie installeert u Visual Studio en de uitbrei ding IoT Edge en stelt u vervolgens uw Azure-account in voor het beheren van IoT Hub resources vanuit Visual Studio. 

In deze zelf studie leert u de ontwikkelings stappen voor Visual Studio 2019. Als u Visual Studio 2017 (versie 15,7 of hoger) gebruikt, zijn de stappen vergelijkbaar. Als u liever Visual Studio code gebruikt, raadpleegt u de instructies in [Visual Studio code gebruiken voor het ontwikkelen en opsporen van fouten in modules voor Azure IOT Edge](how-to-vs-code-develop-module.md). 

1. Bereid Visual Studio 2019 voor op uw ontwikkel machine. 

   * Als u Visual Studio nog niet op uw ontwikkel machine hebt, [installeert u Visual studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) met de volgende werk belastingen: 

      * Azure-ontwikkeling
      * Desktop ontwikkeling metC++
      * Platformoverschrijdende ontwikkelmogelijkheden van .NET Core

   * Als u Visual Studio 2019 al hebt geïnstalleerd op uw ontwikkel computer, volgt u de stappen in [Visual Studio aanpassen](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) om de vereiste werk belastingen toe te voegen.

2. Down load en installeer de extensie voor [Azure IOT Edge-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) voor Visual Studio 2019. 

   Als u Visual Studio 2017 (versie 15,7 of hoger) gebruikt, downloadt en installeert u de [Azure IOT Edge-Hulpprogram ma's voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Wanneer uw installaties zijn voltooid, opent u Visual Studio 2019 en selecteert u **door gaan zonder code**.

4. Selecteer**Cloud Verkenner** **weer geven** > . 

5. Selecteer het profiel pictogram in de Cloud Verkenner en meld u aan bij uw Azure-account als u zich nog niet hebt aangemeld. 

6. Zodra u zich hebt aangemeld, worden uw Azure-abonnementen weer gegeven. Vouw het abonnement uit met uw IoT-hub. 

7. Vouw onder uw abonnement **IOT hubs** uit en vervolgens uw IOT-hub. U ziet een lijst met uw IoT-apparaten en kunt deze Verkenner gebruiken om ze te beheren. 

   ![Toegang tot IoT Hub resources in Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Een nieuw module project maken

De uitbrei ding van de Azure IoT Edge-Hulpprogram Ma's biedt project sjablonen voor alle ondersteunde IoT Edge module talen in Visual Studio. Deze sjablonen hebben alle bestanden en code die u nodig hebt om een Working-module te implementeren om IoT Edge te testen. u kunt ook een begin punt geven om de sjabloon aan te passen met uw eigen bedrijfs logica. 

1. Selecteer **bestand** > nieuwproject >  **...**

2. Zoek in het venster New project naar **IOT Edge** en kies het **Azure IOT Edge (Windows amd64)-** project. Klik op **Volgende**. 

   ![Een nieuw Azure IoT Edge project maken](./media/tutorial-develop-for-windows/new-project.png)

3. In het venster uw nieuwe project configureren wijzigt u de naam van het project en de oplossing in een beschrijvende soort, zoals **CSharpTutorialApp**. Klik op **maken** om het project te maken.

   ![Een nieuw Azure IoT Edge project configureren](./media/tutorial-develop-for-windows/configure-project.png)

4. Configureer in het venster module toevoegen het project met de volgende waarden: 

   | Veld | Value |
   | ----- | ----- |
   | Visual Studio-sjabloon | Selecteer  **C# de module**. | 
   | Modulenaam | Accepteer de standaard **IotEdgeModule1**. | 
   | URL van opslag plaats | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De container installatie kopie wordt vooraf ingevuld op basis van de waarde van de module Project naam. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke afbeeldings opslagplaats ziet \<eruit als\>register naam. azurecr.io/iotedgemodule1. |

      ![Uw project configureren voor doel apparaat, module type en container register](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Selecteer **toevoegen** om de module te maken. 

Wanneer het nieuwe project in het Visual Studio-venster wordt geladen, moet u even de tijd nemen om de gemaakte bestanden te verkennen: 

* Een IoT Edge-project met de naam **CSharpTutorialApp**.
    * De map **modules** bevat aanwijzers naar de modules die in het project zijn opgenomen. In dit geval moet deze alleen IotEdgeModule1 zijn. 
    * Het bestand **Deployment. Temp late. json** is een sjabloon waarmee u een implementatie manifest maakt. Een *implementatie manifest* is een bestand dat precies aangeeft welke modules u wilt implementeren op een apparaat, hoe ze moeten worden geconfigureerd en hoe ze met elkaar en de Cloud kunnen communiceren. 
* Een IoT Edge module-project met de naam **IotEdgeModule1**.
    * Het **Program.cs** -bestand bevat de C# standaard module code die bij de project sjabloon wordt geleverd. De standaard module maakt invoer van een bron en geeft deze door aan IoT Hub. 
    * Het bestand **module. json** bevat details over de module, met inbegrip van de volledige opslag plaats voor de installatie kopie, de installatie kopie versie en welke Dockerfile moet worden gebruikt voor elk ondersteund platform.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Geef uw register referenties voor de IoT Edge-agent op

De IoT Edge runtime heeft uw register referenties nodig om de container installatie kopieën op het IoT Edge apparaat te halen. Voeg deze referenties toe aan de implementatie sjabloon. 

1. Open het bestand **Deployment. Temp late. json** .

2. Zoek de eigenschap **registryCredentials** in de gewenste eigenschappen van de $edgeAgent. 

3. Werk de eigenschap bij met uw referenties, met de volgende indeling: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Sla het bestand deployment.template.json op. 

### <a name="review-the-sample-code"></a>De voorbeeldcode controleren

De oplossings sjabloon die u hebt gemaakt, bevat voorbeeld code voor een IoT Edge module. In deze voorbeeld module worden berichten gewoon ontvangen en vervolgens door gegeven aan. Met de pijplijn functionaliteit wordt een belang rijk concept in IoT Edge gedemonstreerd, waarmee modules met elkaar communiceren.

Voor elke module kunnen meerdere *invoer* -en *uitvoer* wachtrijen worden gedeclareerd in de code. Met de IoT Edge hub die op het apparaat wordt uitgevoerd, worden berichten gerouteerd van de uitvoer van een module naar de invoer van een of meer modules. De specifieke taal voor het declareren van invoer en uitvoer verschilt per taal, maar het concept is hetzelfde voor alle modules. Zie voor meer informatie over route ring tussen modules [routes declareren](module-composition.md#declare-routes).

De voorbeeld C# code die bij de project sjabloon hoort, maakt gebruik van de [ModuleClient-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) van de IOT hub SDK voor .net. 

1. Zoek in het bestand **Program.cs** de methode **SetInputMessageHandlerAsync** .

2. Met de methode [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) wordt een invoer wachtrij ingesteld voor het ontvangen van inkomende berichten. Bekijk deze methode en Bekijk hoe het een invoer wachtrij initialiseert met de naam **input1**. 

   ![De invoer naam zoeken in de SetInputMessageHandlserAsync-constructor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Zoek vervolgens de methode **SendEventAsync** .

4. De methode [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) verwerkt berichten ontvangen en stelt een uitvoer wachtrij in om deze aan te geven. Bekijk deze methode en controleer of het een uitvoer wachtrij initialiseert met de naam **output1**. 

   ![De naam van de uitvoer in de SendEventAsync-constructor zoeken](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open het bestand **Deployment. Temp late. json** .

6. Zoek de eigenschap **modules** van de gewenste eigenschappen van de $edgeAgent. 

   Er moeten twee modules worden weer gegeven. De eerste is **SimulatedTemperatureSensor**, die is opgenomen in alle sjablonen standaard om gesimuleerde temperatuur gegevens te bieden die u kunt gebruiken om uw modules te testen. De tweede is de **IotEdgeModule1** -module die u hebt gemaakt als onderdeel van dit project.

   Deze eigenschap modules declareert welke modules moeten worden opgenomen in de implementatie op uw apparaat of op apparaten. 

7. Zoek de eigenschap **routes** van de gewenste eigenschappen van de $edgeHub. 

   Een van de functies als de module IoT Edge hub berichten moet routeren tussen alle modules in een implementatie. Controleer de waarden in de eigenschap routes. De eerste route, **IotEdgeModule1ToIoTHub**, gebruikt een Joker teken ( **\*** ) om een bericht op te genomen van een uitvoer wachtrij in de IotEdgeModule1-module. Deze berichten gaan naar *$upstream*. Dit is een gereserveerde naam die IOT hub aangeeft. De tweede route, **sensorToIotEdgeModule1**, haalt berichten uit de SimulatedTemperatureSensor-module en stuurt deze door naar de invoer wachtrij *input1* van de module IotEdgeModule1. 

   ![Routes bekijken in implementatie. sjabloon. json](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>De oplossing bouwen en pushen

U hebt de module code en de implementatie sjabloon gecontroleerd om enkele belang rijke concepten van de implementatie te begrijpen. U bent nu klaar om de IotEdgeModule1-container installatie kopie te bouwen en deze naar uw container register te pushen. Met de extensie IoT-hulpprogram ma's voor Visual Studio genereert deze stap ook het implementatie manifest op basis van de informatie in het sjabloon bestand en de module gegevens van de oplossings bestanden. 

### <a name="sign-in-to-docker"></a>Aanmelden bij docker

Geef uw container register referenties op voor de docker op uw ontwikkel computer zodat u de container installatie kopie kunt pushen zodat deze kan worden opgeslagen in het REGI ster. 

1. Open Power shell of een opdracht prompt.

2. Meld u aan bij docker met de Azure container Registry-referenties die u hebt opgeslagen nadat u het REGI ster hebt gemaakt. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligings waarschuwing weer gegeven waarin wordt aanbevolen het `--password-stdin`gebruik van te gebruiken. Hoewel dat best practice wordt aanbevolen voor productie scenario's, valt het buiten het bereik van deze zelf studie. Zie voor meer informatie de koppeling naar docker- [aanmelding](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

### <a name="build-and-push"></a>Bouwen en pushen

Uw ontwikkel machine heeft nu toegang tot uw container register en uw IoT Edge apparaten. Het is tijd om de project code in te scha kelen in een container installatie kopie. 

1. Klik met de rechter muisknop op de projectmap **CSharpTutorialApp** en selecteer **Build and push IOT Edge modules**. 

   ![IoT Edge modules bouwen en pushen](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Met de opdracht build en push worden drie bewerkingen gestart. Eerst wordt een nieuwe map gemaakt in de oplossing **config** met het volledige implementatie manifest, gebaseerd op de gegevens in de implementatie sjabloon en andere oplossings bestanden. Ten tweede wordt `docker build` de container installatie kopie gemaakt op basis van de juiste dockerfile voor uw doel architectuur. Vervolgens wordt `docker push` de opslag plaats voor de installatie kopie naar het container register pusht. 

   Dit proces kan enkele minuten de eerste keer duren, maar de volgende keer dat u de opdrachten uitvoert, wordt sneller uitgevoerd. 

2. Open het bestand **Deployment. Windows-amd64. json** in de zojuist gemaakte map Config. (De map config wordt mogelijk niet weer gegeven in de Solution Explorer in Visual Studio. Als dat het geval is, selecteert u het pictogram **alle bestanden weer geven** op de taak balk Solution Explorer.)

3. Zoek de para meter **Image** van de sectie IotEdgeModule1. U ziet dat de installatie kopie de volledige opslag plaats voor de installatie kopie met de naam, versie en architectuur label uit het bestand module. json bevat.

4. Open het bestand **module. json** in de map IotEdgeModule1. 

5. Het versie nummer voor de module installatie kopie wijzigen. (De versie, niet de $schema-versie.) Verhoog bijvoorbeeld het versie nummer van de patch naar **0.0.2** , alsof er een kleine oplossing in de module code is gemaakt. 

   >[!TIP]
   >Met module versies kunt u versie beheer inschakelen en kunt u wijzigingen in een kleine set apparaten testen voordat u updates voor de productie implementeert. Als u de module versie niet verhoogt voordat u bouwt en pusht, overschrijft u de opslag plaats in het container register. 

6. Sla de wijzigingen op in het bestand module. json.

7. Klik opnieuw met de rechter muisknop op de **CSharpTutorialApp** -projectmap en selecteer opnieuw **IOT Edge modules bouwen en pushen** . 

8. Open de **implementatie. Windows-amd64. json** -bestand opnieuw. U ziet dat een nieuw bestand niet is gemaakt tijdens het uitvoeren van de opdracht build en push. In plaats daarvan is hetzelfde bestand bijgewerkt om de wijzigingen weer te geven. De IotEdgeModule1-installatie kopie verwijst nu naar de 0.0.2-versie van de container. Deze wijziging in het implementatie manifest is de manier waarop u het IoT Edge apparaat vertelt dat er een nieuwe versie van een module moet worden opgehaald. 

9. Ga naar het [Azure Portal](https://portal.azure.com) en navigeer naar het container register om verder te controleren wat de opbouw en de push opdracht hebben ondervonden. 

10. Selecteer in het container register **opslag** plaatsen en vervolgens **iotedgemodule1**. Controleer of beide versies van de installatie kopie naar het REGI ster zijn gepusht.

    ![Beide afbeeldings versies in container Registry weer geven](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Problemen oplossen

Als er fouten optreden bij het bouwen en pushen van de installatie kopie van de module, moet u vaak met docker-configuratie op uw ontwikkel computer. Gebruik de volgende controles om uw configuratie te controleren: 

* Hebt u de `docker login` opdracht uitgevoerd met behulp van de referenties die u hebt gekopieerd uit het container register? Deze referenties zijn anders dan degene die u gebruikt om u aan te melden bij Azure. 
* Hebt u de juiste containeropslagplaats? Heeft het de juiste naam voor het container register en de juiste module naam? Open het bestand **module. json** in de map IotEdgeModule1 om dit te controleren. De waarde van de opslag plaats moet er als  **\<register\>naam. azurecr.io/iotedgemodule1**uitzien. 
* Als u een andere naam hebt gebruikt dan **IotEdgeModule1** voor uw module, is die naam consistent in de oplossing?
* Voert de computer uit van hetzelfde type containers dat u bouwt? Deze zelf studie is voor Windows IoT Edge-apparaten, dus uw Visual Studio-bestanden moeten de **Windows-amd64-** extensie hebben en docker Desktop moet Windows-containers uitvoeren. 

## <a name="deploy-modules-to-device"></a>Modules implementeren op het apparaat

U hebt gecontroleerd of de gemaakte container installatie kopieën zijn opgeslagen in het container register, dus het is tijd om ze te implementeren op een apparaat. Zorg ervoor dat uw IoT Edge-apparaat actief is. 

1. Open de Cloud Verkenner in Visual Studio en vouw de details uit voor uw IoT-hub. 

2. Selecteer de naam van het apparaat dat u wilt implementeren. Selecteer in de lijst **acties** de optie **implementatie maken**.

   ![Implementatie voor één apparaat maken](./media/tutorial-develop-for-windows/create-deployment.png)


3. Ga in Verkenner naar de map config van het project en selecteer de **implementatie. Windows-amd64. json** -bestand. Dit bestand bevindt zich vaak op`C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Gebruik niet het bestand Deployment. Temp late. json, dat niet beschikt over de volledige module-installatie kopie waarden. 

4. Vouw de details van uw IoT Edge-apparaat uit in de Cloud Verkenner om de modules op het apparaat te bekijken.

5. Gebruik de knop **vernieuwen** om de status van het apparaat bij te werken om te zien of de SimulatedTemperatureSensor-en IotEdgeModule1-modules uw apparaat hebben geïmplementeerd. 


   ![Modules weer geven die worden uitgevoerd op uw IoT Edge apparaat](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Berichten van het apparaat weer geven

De IotEdgeModule1 code ontvangt berichten via de invoer wachtrij en geeft deze door aan de uitvoer wachtrij. Het implementatie manifest heeft gedeclareerde routes die berichten ontvangen van SimulatedTemperatureSensor naar IotEdgeModule1 en vervolgens berichten die vervolgens worden doorgestuurd van IotEdgeModule1 naar IoT Hub. Met de Azure IoT Edge-hulpprogram ma's voor Visual Studio kunt u berichten zien wanneer ze op IoT Hub van uw afzonderlijke apparaten arriveren. 

1. Selecteer in de Visual Studio Cloud Explorer de naam van het IoT Edge-apparaat dat u hebt geïmplementeerd. 

2. Selecteer in het menu **acties** de optie **controle van ingebouwd gebeurtenis eindpunt starten**.

3. Bekijk de sectie **uitvoer** in Visual Studio om berichten te bekijken die binnenkomen op uw IOT-hub. 

   Het kan enkele minuten duren voordat beide modules worden gestart. De IoT Edge runtime moet het nieuwe implementatie manifest ontvangen, de module-installatie kopieën uit de container runtime ophalen en vervolgens elke nieuwe module starten. 

   ![Binnenkomend apparaat naar Cloud berichten weer geven](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wijzigingen op apparaat weer geven

Als u wilt zien wat er gebeurt op uw apparaat zelf, gebruikt u de opdrachten in deze sectie om de IoT Edge runtime en modules op uw apparaat te controleren. 

De opdrachten in deze sectie zijn voor uw IoT Edge apparaat, niet voor uw ontwikkel computer. Als u een virtuele machine voor uw IoT Edge-apparaat gebruikt, kunt u er nu verbinding mee maken. Ga in azure naar de overzichts pagina van de virtuele machine en selecteer **verbinding maken** om toegang te krijgen tot de verbinding met extern bureau blad. Open een opdracht of Power shell-venster op het apparaat om de `iotedge` opdrachten uit te voeren.

* Bekijk alle modules die op het apparaat zijn geïmplementeerd en controleer hun status:

   ```cmd
   iotedge list
   ```

   U ziet vier modules: de twee IoT Edge runtime modules, SimulatedTemperatureSensor en IotEdgeModule1. Alle vier moeten worden weer gegeven als actief.

* Inspecteer de logboeken voor een specifieke module:

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge modules zijn hoofdletter gevoelig. 

   De SimulatedTemperatureSensor-en IotEdgeModule1-logboeken moeten de berichten weer geven die ze verwerken. De edgeAgent-module is verantwoordelijk voor het starten van de andere modules, zodat de logboeken hiervan informatie bevatten over het implementeren van het implementatie manifest. Als een module niet wordt vermeld of niet wordt uitgevoerd, hebben de edgeAgent-logboeken waarschijnlijk de fouten. De edgeHub-module is verantwoordelijk voor de communicatie tussen de modules en IoT Hub. Als de modules actief zijn, maar de berichten niet op uw IoT-hub arriveren, hebben de edgeHub-logboeken waarschijnlijk de fouten. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie stelt u Visual Studio 2019 in op uw ontwikkel computer en implementeert u uw eerste IoT Edge-module. Nu u de basis concepten kent, kunt u de functionaliteit toevoegen aan een module, zodat de gegevens kunnen worden geanalyseerd die worden door gegeven. Kies de taal van uw voor keur: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
