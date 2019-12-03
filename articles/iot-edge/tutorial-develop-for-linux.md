---
title: 'Zelf studie: een module ontwikkelen voor Linux-apparaten met behulp van Azure IoT Edge'
description: In deze zelf studie wordt uitgelegd hoe u uw ontwikkel computer en cloud resources instelt om IoT Edge modules te ontwikkelen met Linux-containers voor Linux-apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: e4291c5dcea27699de72b72c52a832a7dc86b97b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701889"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Zelf studie: IoT Edge-modules voor Linux-apparaten ontwikkelen

Gebruik Visual Studio code voor het ontwikkelen en implementeren van code voor Linux-apparaten met IoT Edge. 

In de Quick Start-artikelen hebt u een IoT Edge apparaat gemaakt met behulp van een virtuele Linux-machine en een vooraf gemaakte module geïmplementeerd vanuit Azure Marketplace. In deze zelf studie wordt uitgelegd wat u nodig hebt om uw eigen code te ontwikkelen en implementeren op een IoT Edge apparaat. Deze zelf studie is een nuttige vereiste voor alle andere zelf studies, die meer details over specifieke programmeer talen of Azure-Services zullen gaan. 

In deze zelf studie wordt het voor beeld van het implementeren van een  **C# module naar een Linux-apparaat**gebruikt. Dit voor beeld is gekozen omdat het het meest voorkomende ontwikkelaars scenario is voor IoT Edge oplossingen. Ook als u van plan bent een andere taal te gebruiken of een Azure-service te implementeren, is deze zelf studie nog steeds nuttig voor meer informatie over de ontwikkel hulpprogramma's en-concepten. Nadat u deze inleiding in het ontwikkelings proces hebt voltooid, kunt u de gewenste taal of Azure-service kiezen voor meer informatie. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel uw ontwikkel machine in.
> * Gebruik de IoT Edge-hulpprogram ma's voor Visual Studio code om een nieuw project te maken.
> * Bouw uw project als een container en sla het op in een Azure container Registry.
> * Implementeer uw code op een IoT Edge apparaat. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Belangrijke concepten

In deze zelf studie wordt de ontwikkeling van een IoT Edge module door lopen. Een *IOT Edge module*, of soms alleen *module* voor kort, is een container die uitvoer bare code bevat. U kunt een of meer modules implementeren op een IoT Edge apparaat. Modules voeren specifieke taken uit, zoals het opnemen van gegevens van Sens oren, het uitvoeren van gegevens analyses of het opschonen van gegevens, of het verzenden van berichten naar een IoT-hub. Zie voor meer informatie [Azure IOT Edge modules begrijpen](iot-edge-modules.md).

Wanneer u IoT Edge modules ontwikkelt, is het belang rijk dat u begrijpt wat het verschil is tussen de ontwikkelings machine en het doel IoT Edge apparaat waar de module uiteindelijk zal worden geïmplementeerd. De container die u voor de module code bouwt, moet overeenkomen met het besturings systeem (OS) van het *doel apparaat*. Zo is het meest voorkomende scenario dat iemand die een module ontwikkelt op een Windows-computer voornemens is een Linux-apparaat met IoT Edge te richten. In dat geval zou het besturings systeem van de container Linux zijn. Wanneer u deze zelf studie doorloopt, moet u het verschil tussen het *besturings systeem van de ontwikkel machine* en het *container besturingssysteem*onthouden.

In deze zelf studie worden Linux-apparaten met IoT Edge als doel gebruikt. U kunt uw favoriete besturings systeem gebruiken, zolang uw ontwikkel computer Linux-containers kan uitvoeren. We raden u aan om Visual Studio code te gebruiken voor het ontwikkelen van Linux-apparaten, zodat deze zelf studie wordt gebruikt. U kunt ook Visual Studio gebruiken, hoewel er verschillen zijn in ondersteuning tussen de twee hulpprogram ma's.

De volgende tabel geeft een overzicht van de ondersteunde ontwikkelings scenario's voor **Linux-containers** in Visual Studio code en Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architectuur van Linux-apparaat** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-Services** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Talen** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Meer informatie** | [Azure IoT Edge voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-Hulpprogram Ma's voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge-Hulpprogram Ma's voor Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Ondersteuning voor Linux ARM64-apparaten is beschikbaar in de [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Zie voor meer informatie [ARM64-modules ontwikkelen en fouten opsporen IOT Edge in Visual Studio code (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

In deze zelf studie leert u de ontwikkelings stappen voor Visual Studio code. Als u liever Visual Studio gebruikt, raadpleegt u de instructies in [use Visual studio 2019 voor het ontwikkelen en opsporen van fouten in modules voor Azure IOT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Vereisten

Een ontwikkel computer:

* U kunt uw eigen computer of een virtuele machine gebruiken, afhankelijk van uw ontwikkelings voorkeuren.
  * Zorg ervoor dat uw ontwikkel computer geneste virtualisatie ondersteunt. Deze mogelijkheid is nodig voor het uitvoeren van een container engine, die u in de volgende sectie installeert.
* De meeste besturings systemen waarop een container engine kan worden uitgevoerd, kunnen worden gebruikt voor het ontwikkelen van IoT Edge-modules voor Linux-apparaten. In deze zelf studie wordt gebruikgemaakt van een Windows-computer, maar punten bekende verschillen in MacOS of Linux. 
* Installeer [Git](https://git-scm.com/)voor het ophalen van module sjabloon pakketten verderop in deze zelf studie.  
* [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Een Azure IoT Edge apparaat in Linux:

* Het is raadzaam om IoT Edge niet uit te voeren op uw ontwikkel computer, maar u kunt in plaats daarvan een afzonderlijk apparaat gebruiken. Dit onderscheid tussen de ontwikkel machine en het IoT Edge apparaat is nauw keuriger in een echt implementatie scenario en helpt de verschillende concepten direct te blijven gebruiken.
* Als er nog geen tweede apparaat beschikbaar is, gebruikt u het Quick Start-artikel om een IoT Edge apparaat in azure te maken met een [virtuele Linux-machine](quickstart-linux.md).

Cloudresources:

* Een gratis of een IOT- [hub](../iot-hub/iot-hub-create-through-portal.md) met een standaard laag in Azure. 

## <a name="install-container-engine"></a>Container Engine installeren

IoT Edge-modules worden verpakt als containers, dus u hebt een container engine op uw ontwikkel computer nodig om de containers te bouwen en te beheren. Het is raadzaam docker Desktop te gebruiken voor ontwikkeling vanwege de vele functies en populariteit als een container engine. Met docker Desktop op een Windows-apparaat kunt u scha kelen tussen Linux-containers en Windows-containers, zodat u eenvoudig modules kunt ontwikkelen voor verschillende soorten IoT Edge apparaten. 

Gebruik de docker-documentatie om te installeren op uw ontwikkel computer: 

* [Docker Desktop voor Windows installeren](https://docs.docker.com/docker-for-windows/install/)

  * Wanneer u docker Desktop voor Windows installeert, wordt u gevraagd of u Linux-of Windows-containers wilt gebruiken. Deze beslissing kan op elk gewenst moment worden gewijzigd met een eenvoudige switch. Voor deze zelf studie gebruiken we Linux-containers omdat onze modules zijn gericht op Linux-apparaten. Zie [scha kelen tussen Windows-en Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)voor meer informatie.

* [Docker Desktop voor Mac installeren](https://docs.docker.com/docker-for-mac/install/)

* Meer informatie [over docker CE](https://docs.docker.com/install/) voor installatie gegevens op verschillende Linux-platforms.

## <a name="set-up-vs-code-and-tools"></a>VS code en hulpprogram ma's instellen

Gebruik de IoT-extensies voor Visual Studio code voor het ontwikkelen van IoT Edge-modules. Deze uitbrei dingen bieden project sjablonen, automatiseren het maken van het implementatie manifest en bieden u de mogelijkheid om IoT Edge apparaten te bewaken en te beheren. In deze sectie installeert u Visual Studio code en de IoT-uitbrei ding. vervolgens stelt u uw Azure-account in voor het beheren van IoT Hub resources vanuit Visual Studio code. 

1. Installeer [Visual Studio code](https://code.visualstudio.com/) op uw ontwikkel computer. 

2. Als de installatie is voltooid, selecteert u > **extensies** **weer geven** . 

3. Zoek naar **Azure IOT-Hulpprogram ma's**. Dit is eigenlijk een verzameling extensies waarmee u kunt communiceren met IOT hub-en IOT-apparaten, en voor het ontwikkelen van IOT Edge-modules. 

4. Selecteer **Installeren**. Elke opgenomen extensie wordt afzonderlijk geïnstalleerd. 

5. Wanneer de uitbrei dingen zijn geïnstalleerd, opent u het opdracht palet door > **opdracht** **weer geven** te selecteren. 

6. In het opdracht palet zoekt en selecteert u **Azure: Meld**u aan. Volg de aanwijzingen om u aan te melden bij uw Azure-account. 

7. In het opdracht palet zoekt en selecteert u **Azure IOT hub: selecteer IOT hub**. Volg de aanwijzingen om uw Azure-abonnement en IoT-hub te selecteren. 

7. Open de sectie Explorer van Visual Studio code door het pictogram te selecteren in de activiteiten balk aan de linkerkant of door **weer gave** > **Explorer**te selecteren. 

8. Onder aan de sectie Explorer vouwt u het menu samengevouwen **Azure IOT Hub-apparaten** uit. Als het goed is, ziet u de apparaten en IoT Edge apparaten die zijn gekoppeld aan de IoT-hub die u hebt geselecteerd in het opdracht palet. 

   ![Apparaten weer geven in uw IoT-hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Een nieuw module project maken

De uitbrei ding Azure IoT tools biedt project sjablonen voor alle ondersteunde IoT Edge module talen in Visual Studio code. Deze sjablonen hebben alle bestanden en code die u nodig hebt om een Working-module te implementeren om IoT Edge te testen. u kunt ook een begin punt geven om de sjabloon aan te passen met uw eigen bedrijfs logica. 

Voor deze zelf studie gebruiken we de C# module sjabloon omdat het de meestgebruikte sjabloon is. 

### <a name="create-a-project-template"></a>Een project sjabloon maken

In het Visual Studio code-opdracht palet zoekt en selecteert u **Azure IOT Edge: nieuwe IOT EDGE oplossing**. Volg de aanwijzingen en gebruik de volgende waarden om uw oplossing te maken: 

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **C# Module**. |
   | Een modulenaam opgeven | Accepteer de standaard **SampleModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslag plaats voor de installatie kopie ziet eruit als \<register naam\>. azurecr.io/samplemodule. |
 
   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-develop-for-linux/image-repository.png)

Zodra de nieuwe oplossing in het Visual Studio-code venster is geladen, neemt u even de tijd om vertrouwd te raken met de bestanden die zijn gemaakt: 

* De map **. vscode** bevat een bestand met de naam **Launch. json**, dat wordt gebruikt voor het opsporen van fouten in modules.
* De map **modules** bevat een map voor elke module in uw oplossing. Dit is op dit moment alleen **SampleModule**of de naam die u aan de module hebt gegeven. De map SampleModule bevat de belangrijkste programma code, de meta gegevens van de module en verschillende docker-bestanden. 
* Het **. env** -bestand bevat de referenties voor het container register. Deze referenties worden gedeeld met uw IoT Edge-apparaat, zodat het toegang heeft tot het ophalen van de container installatie kopieën. 
* De **implementatie. debug. sjabloon. json** bestand en **implementatie. bestand sjabloon. json** zijn sjablonen die u helpen bij het maken van een implementatie manifest. Een *implementatie manifest* is een bestand dat precies aangeeft welke modules u wilt implementeren op een apparaat, hoe ze moeten worden geconfigureerd en hoe ze met elkaar en de Cloud kunnen communiceren. De sjabloon bestanden gebruiken verwijzingen voor bepaalde waarden. Wanneer u de sjabloon transformeert in een waar implementatie manifest, worden de aanwijzers vervangen door waarden die zijn overgenomen uit andere oplossings bestanden. Zoek de twee gebruikelijke tijdelijke aanduidingen in uw implementatie sjabloon: 

  * In de sectie register referenties wordt het adres aangevuld met de informatie die u hebt ingevoerd tijdens het maken van de oplossing. De gebruikers naam en het wacht woord verwijzen echter naar de variabelen die zijn opgeslagen in het. env-bestand. Dit is voor beveiliging, omdat het. env-bestand Git wordt genegeerd, maar de implementatie sjabloon niet. 
  * In de sectie SampleModule is de container installatie kopie niet ingevuld, ook al hebt u de opslag plaats voor de installatie kopie opgegeven tijdens het maken van de oplossing. Deze tijdelijke aanduiding verwijst naar het bestand **module. json** in de map SampleModule. Als u naar dat bestand gaat, ziet u dat het veld image de opslag plaats bevat, maar ook een label waarde die bestaat uit de versie en het platform van de container. U kunt de versie hand matig herhalen als onderdeel van de ontwikkelings cyclus en u selecteert het container platform met een schakelaar die verderop in deze sectie wordt geïntroduceerd. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Geef uw register referenties voor de IoT Edge-agent op

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om de container installatie kopieën op het IoT Edge-apparaat te halen. 

De uitbrei ding van de IoT Edge probeert uw container register referenties van Azure op te halen en deze in het omgevings bestand in te vullen. Controleer of uw referenties al zijn opgenomen. Als dat niet het geval is, voegt u ze nu toe:

1. Open het **. env** -bestand in de module oplossing. 
2. Voeg de waarden van de **gebruikers naam** en het **wacht woord** toe die u hebt gekopieerd uit uw Azure container Registry.
3. Sla de wijzigingen in het. env-bestand op. 

### <a name="select-your-target-architecture"></a>Selecteer uw doel architectuur

Visual Studio code kan momenteel modules ontwikkelen C# voor linux amd64-en ARM32v7-apparaten. U moet selecteren welke architectuur u wilt richten op elke oplossing, omdat dit van invloed is op hoe de container wordt gebouwd en wordt uitgevoerd. De standaard waarde is Linux AMD64. 

1. Open het opdracht palet en zoek naar **Azure IOT Edge: Stel het standaard doel platform voor de rand oplossing**in of selecteer het snelkoppelings pictogram in de zijbalk aan de onderkant van het venster. 

   ![Architectuur pictogram selecteren in de zijkants balk](./media/tutorial-develop-for-linux/select-architecture.png)

2. Selecteer in het opdracht palet de doel architectuur in de lijst met opties. Voor deze zelf studie gebruiken we een virtuele machine van Ubuntu als het IoT Edge-apparaat, zodat de standaard **amd64**wordt bewaard. 

### <a name="review-the-sample-code"></a>De voorbeeld code bekijken

De oplossings sjabloon die u hebt gemaakt, bevat voorbeeld code voor een IoT Edge module. In deze voorbeeld module worden berichten gewoon ontvangen en vervolgens door gegeven aan. Met de pijplijn functionaliteit wordt een belang rijk concept in IoT Edge gedemonstreerd, waarmee modules met elkaar communiceren.

Voor elke module kunnen meerdere *invoer* -en *uitvoer* wachtrijen worden gedeclareerd in de code. Met de IoT Edge hub die op het apparaat wordt uitgevoerd, worden berichten gerouteerd van de uitvoer van een module naar de invoer van een of meer modules. De specifieke taal voor het declareren van invoer en uitvoer verschilt per taal, maar het concept is hetzelfde voor alle modules. Zie voor meer informatie over route ring tussen modules [routes declareren](module-composition.md#declare-routes).

De voorbeeld C# code die bij de project sjabloon hoort, maakt gebruik van de [ModuleClient-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) van de IOT hub SDK voor .net. 

1. Open het **Program.cs** -bestand, dat zich in de **modules/SampleModule/** map bevindt. 

2. Zoek in program.cs de methode **SetInputMessageHandlerAsync** .

2. Met de methode [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) wordt een invoer wachtrij ingesteld voor het ontvangen van inkomende berichten. Bekijk deze methode en Bekijk hoe het een invoer wachtrij initialiseert met de naam **input1**. 

   ![De invoer naam zoeken in de SetInputMessageCallback-constructor](./media/tutorial-develop-for-linux/declare-input-queue.png)

3. Zoek vervolgens de methode **SendEventAsync** .

4. De methode [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) verwerkt berichten ontvangen en stelt een uitvoer wachtrij in om deze aan te geven. Bekijk deze methode en controleer of het een uitvoer wachtrij initialiseert met de naam **output1**. 

   ![De naam van de uitvoer in SendEventToOutputAsync zoeken](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Open het bestand **Deployment. Temp late. json** .

7. Zoek de eigenschap **modules** van de gewenste eigenschappen van de $edgeAgent. 

   Er moeten twee modules worden weer gegeven. De eerste is **SimulatedTemperatureSensor**, die is opgenomen in alle sjablonen standaard om gesimuleerde temperatuur gegevens te bieden die u kunt gebruiken om uw modules te testen. De tweede is de **SampleModule** -module die u hebt gemaakt als onderdeel van deze oplossing.

7. Zoek de gewenste eigenschappen voor de module **$edgeHub** aan de onderkant van het bestand. 

   Een van de functies van de IoT Edge hub-module is het routeren van berichten tussen alle modules in een implementatie. Controleer de waarden in de eigenschap **routes** . De eerste route, **SampleModuleToIoTHub**, gebruikt een Joker teken ( **\*** ) om berichten te geven die afkomstig zijn van uitvoer wachtrijen in de SampleModule-module. Deze berichten gaan naar *$upstream*. Dit is een gereserveerde naam die IOT hub aangeeft. De tweede route, sensorToSampleModule, haalt berichten uit de SimulatedTemperatureSensor-module op en stuurt deze door naar de *input1* -invoer wachtrij die u in de SampleModule-code hebt geïnitialiseerd. 

   ![Routes bekijken in implementatie. sjabloon. json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>De oplossing bouwen en pushen

U hebt de module code en de implementatie sjabloon gecontroleerd om enkele belang rijke concepten van de implementatie te begrijpen. U bent nu klaar om de SampleModule-container installatie kopie te bouwen en deze naar uw container register te pushen. Met de extensie voor IoT-hulpprogram ma's voor Visual Studio code genereert deze stap ook het implementatie manifest op basis van de informatie in het sjabloon bestand en de module gegevens van de oplossings bestanden. 

### <a name="sign-in-to-docker"></a>Aanmelden bij docker

Geef de container register referenties op voor docker, zodat de container installatie kopie kan worden pusht voor opslag in het REGI ster. 

1. Open de Visual Studio code-geïntegreerde Terminal door > **Terminal** **weer geven** te selecteren.

2. Meld u aan bij docker met de Azure container Registry-referenties die u hebt opgeslagen nadat u het REGI ster hebt gemaakt. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligings waarschuwing weer gegeven waarin wordt aanbevolen het gebruik van `--password-stdin`. Hoewel dat best practice wordt aanbevolen voor productie scenario's, valt het buiten het bereik van deze zelf studie. Zie voor meer informatie de [koppeling naar docker-aanmelding](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

### <a name="build-and-push"></a>Bouwen en pushen 

Visual Studio code heeft nu toegang tot uw container register, dus is het tijd om de oplossings code in te scha kelen in een container installatie kopie. 

1. Klik in Visual Studio code Explorer met de rechter muisknop op het bestand **Deployment. sjabloon. json** en selecteer **Build and push IOT Edge Solution**. 

   ![IoT Edge modules bouwen en pushen](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Met de opdracht build en push worden drie bewerkingen gestart. Eerst wordt een nieuwe map gemaakt in de oplossing **config** met het volledige implementatie manifest, gebaseerd op de gegevens in de implementatie sjabloon en andere oplossings bestanden. Ten tweede wordt `docker build` uitgevoerd om de container installatie kopie te bouwen op basis van de juiste dockerfile voor uw doel architectuur. Vervolgens wordt `docker push` uitgevoerd om de opslag plaats van de installatie kopie naar het container register te pushen. 

   Dit proces kan enkele minuten de eerste keer duren, maar de volgende keer dat u de opdrachten uitvoert, wordt sneller uitgevoerd. 

2. Open het bestand **Deployment. amd64. json** in de zojuist gemaakte map Config. De bestands naam weerspiegelt de doel architectuur, zodat deze anders is als u een andere architectuur hebt gekozen.

3. U ziet dat de twee para meters met een tijdelijke aanduiding nu zijn ingevuld met de juiste waarden. In de sectie **registryCredentials** zijn de gebruikers naam en het wacht woord van het REGI ster opgehaald uit het. env-bestand. De **SampleModule** heeft de volledige opslag plaats van de installatie kopie met de naam, versie en architectuur label van het bestand module. json. 

4. Open het bestand **module. json** in de map SampleModule. 

5. Het versie nummer voor de module installatie kopie wijzigen. (De versie, niet de $schema-versie.) Verhoog bijvoorbeeld het versie nummer van de patch naar **0.0.2** , alsof er een kleine oplossing in de module code is gemaakt. 

   >[!TIP]
   >Met module versies kunt u versie beheer inschakelen en kunt u wijzigingen in een kleine set apparaten testen voordat u updates voor de productie implementeert. Als u de module versie niet verhoogt voordat u bouwt en pusht, overschrijft u de opslag plaats in het container register. 

6. Sla de wijzigingen op in het bestand module. json.

7. Klik opnieuw met de rechter muisknop op het bestand **Deployment. template. json** en selecteer vervolgens **Build and push IOT Edge Solution**.

8. Open het bestand **implement. amd64. json** opnieuw. U ziet dat een nieuw bestand niet is gemaakt tijdens het uitvoeren van de opdracht build en push. In plaats daarvan is hetzelfde bestand bijgewerkt om de wijzigingen weer te geven. De SampleModule-installatie kopie verwijst nu naar de 0.0.2-versie van de container. 

9. Ga naar het [Azure Portal](https://portal.azure.com) en navigeer naar het container register om verder te controleren wat de opbouw en de push opdracht hebben ondervonden. 

10. Selecteer in het container register **opslag** plaatsen en vervolgens **samplemodule**. Controleer of beide versies van de installatie kopie naar het REGI ster zijn gepusht.

    ![Beide afbeeldings versies in container Registry weer geven](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Problemen oplossen

Als er fouten optreden bij het bouwen en pushen van de installatie kopie van de module, moet u vaak met docker-configuratie op uw ontwikkel computer. Gebruik de volgende controles om uw configuratie te controleren: 

* Hebt u de `docker login` opdracht uitgevoerd met behulp van de referenties die u hebt gekopieerd uit het container register? Deze referenties zijn anders dan degene die u gebruikt om u aan te melden bij Azure. 
* Hebt u de juiste containeropslagplaats? Heeft het de juiste naam voor het container register en de juiste module naam? Open het bestand **module. json** in de map SampleModule om dit te controleren. De waarde van de opslag plaats moet er uitzien als **\<register naam\>. azurecr.io/samplemodule**. 
* Als u een andere naam hebt gebruikt dan **SampleModule** voor uw module, is die naam consistent in de oplossing?
* Voert de computer uit van hetzelfde type containers dat u bouwt? Deze zelf studie is voor Linux IoT Edge-apparaten, zodat Visual Studio code moet zeggen **amd64** of **arm32v7** in de zijbalk, en op docker Desktop moet Linux-containers worden uitgevoerd.  

## <a name="deploy-modules-to-device"></a>Modules implementeren op het apparaat

U hebt gecontroleerd of de gemaakte container installatie kopieën zijn opgeslagen in het container register, dus het is tijd om ze te implementeren op een apparaat. Zorg ervoor dat uw IoT Edge-apparaat actief is. 

1. Vouw in Visual Studio code Explorer het gedeelte Azure IoT Hub-apparaten uit. 

2. Klik met de rechter muisknop op het IoT Edge apparaat waarop u wilt implementeren en selecteer vervolgens **implementatie maken voor één apparaat**. 

   ![Implementatie voor één apparaat maken](./media/tutorial-develop-for-linux/create-deployment.png)

3. Ga in Verkenner naar de map **config** en selecteer vervolgens het bestand **Deployment. amd64. json** . 

   Gebruik niet het bestand Deployment. Temp late. json, dat geen container register referenties of module-installatie kopie waarden bevat. Als u bent gericht op een Linux ARM32-apparaat, wordt het implementatie manifest Deployment. arm32v7. json genoemd. 

4. Vouw de details van uw IoT Edge-apparaat uit en vouw vervolgens de lijst met **modules** voor uw apparaat uit. 

5. Gebruik de knop Vernieuwen om de weer gave van het apparaat bij te werken totdat u de SimulatedTemperatureSensor-en SampleModule-modules ziet die op het apparaat worden uitgevoerd. 

   Het kan enkele minuten duren voordat beide modules worden gestart. De IoT Edge runtime moet het nieuwe implementatie manifest ontvangen, de module-installatie kopieën uit de container runtime ophalen en vervolgens elke nieuwe module starten. 

   ![Modules weer geven die worden uitgevoerd op uw IoT Edge apparaat](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Berichten van het apparaat weer geven

De SampleModule code ontvangt berichten via de invoer wachtrij en geeft deze door aan de uitvoer wachtrij. Het implementatie manifest heeft gedeclareerde routes waarmee berichten worden door gegeven aan SampleModule van SimulatedTemperatureSensor en vervolgens doorgestuurde berichten van SampleModule naar IoT Hub. Met de Azure IoT-hulpprogram ma's voor Visual Studio code kunt u berichten zien wanneer ze op IoT Hub van uw afzonderlijke apparaten arriveren. 

1. Klik in Visual Studio code Explorer met de rechter muisknop op het IoT Edge apparaat dat u wilt bewaken en selecteer vervolgens controle van het **ingebouwde gebeurtenis starten**. 

2. Bekijk het uitvoer venster in Visual Studio code om berichten te zien die binnenkomen op uw IoT-hub. 

   ![Binnenkomend apparaat naar Cloud berichten weer geven](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wijzigingen op apparaat weer geven

Als u wilt zien wat er gebeurt op uw apparaat zelf, gebruikt u de opdrachten in deze sectie om de IoT Edge runtime en modules op uw apparaat te controleren. 

De opdrachten in deze sectie zijn voor uw IoT Edge apparaat, niet voor uw ontwikkel computer. Als u een virtuele machine voor uw IoT Edge-apparaat gebruikt, kunt u er nu verbinding mee maken. Ga in azure naar de overzichts pagina van de virtuele machine en selecteer **verbinding maken** voor toegang tot de beveiligde shell-verbinding. 

* Bekijk alle modules die op het apparaat zijn geïmplementeerd en controleer hun status:

   ```bash
   iotedge list
   ```

   U ziet vier modules: de twee IoT Edge runtime modules, SimulatedTemperatureSensor en SampleModule. Alle vier moeten worden weer gegeven als actief.

* Inspecteer de logboeken voor een specifieke module:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge modules zijn hoofdletter gevoelig. 

   De SimulatedTemperatureSensor-en SampleModule-logboeken moeten de berichten weer geven die ze verwerken. De edgeAgent-module is verantwoordelijk voor het starten van de andere modules, zodat de logboeken hiervan informatie bevatten over het implementeren van het implementatie manifest. Als een module niet wordt vermeld of niet wordt uitgevoerd, hebben de edgeAgent-logboeken waarschijnlijk de fouten. De edgeHub-module is verantwoordelijk voor de communicatie tussen de modules en IoT Hub. Als de modules actief zijn, maar de berichten niet op uw IoT-hub arriveren, hebben de edgeHub-logboeken waarschijnlijk de fouten. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie stelt u Visual Studio code in op uw ontwikkel computer en implementeert u uw eerste IoT Edge-module. Nu u de basis concepten kent, kunt u de functionaliteit toevoegen aan een module, zodat de gegevens kunnen worden geanalyseerd die worden door gegeven. Kies de taal van uw voor keur: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [node. js](tutorial-node-module.md)
> [python](tutorial-python-module.md)