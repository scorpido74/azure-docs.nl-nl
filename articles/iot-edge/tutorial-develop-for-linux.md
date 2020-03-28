---
title: Zelfstudie - Module ontwikkelen voor Linux-apparaten met Azure IoT Edge
description: Deze zelfstudie leidt door het opzetten van uw ontwikkelmachine en cloudresources om IoT Edge-modules te ontwikkelen met Linux-containers voor Linux-apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a127c8c77f97708729cf91d6ced214e98924fb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78943015"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Zelfstudie: IoT Edge-modules ontwikkelen voor Linux-apparaten

Gebruik Visual Studio Code om code te ontwikkelen en te implementeren op Linux-apparaten met IoT Edge.

In de quickstart hebt u een IoT Edge-apparaat gemaakt met behulp van een Virtuele Linux-machine en een module geïmplementeerd vanuit de Azure Marketplace. Deze zelfstudie loopt door het ontwikkelen en implementeren van uw eigen code naar een IoT Edge-apparaat. Dit artikel is een nuttige voorwaarde voor de andere zelfstudies, die meer in detail treden over specifieke programmeertalen of Azure-services.

Deze zelfstudie gebruikt het voorbeeld van het implementeren van een **C#-module op een Linux-apparaat.** Dit voorbeeld is gekozen omdat het het meest voorkomende ontwikkelaarsscenario is voor IoT Edge-oplossingen. Zelfs als u van plan bent een andere taal te gebruiken of een Azure-service te implementeren, is deze zelfstudie nog steeds handig om meer te weten te komen over de ontwikkeltools en -concepten. Voltooi deze inleiding tot het ontwikkelingsproces en kies vervolgens uw gewenste taal of Azure-service om in de details te duiken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Stel je ontwikkelmachine in.
> * Gebruik de IoT Edge-tools voor Visual Studio Code om een nieuw project te maken.
> * Bouw uw project als container en sla het op in een Azure-containerregister.
> * Implementeer uw code op een IoT Edge-apparaat.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Belangrijkste concepten

Deze zelfstudie doorloopt de ontwikkeling van een IoT Edge-module. Een *IoT Edge-module*, of soms gewoon *module* in het kort, is een container met uitvoerbare code. U een of meer modules implementeren op een IoT Edge-apparaat. Modules voeren specifieke taken uit, zoals het innemen van gegevens van sensoren, het reinigen en analyseren van gegevens of het verzenden van berichten naar een IoT-hub. Zie [Azure IoT Edge-modules begrijpen](iot-edge-modules.md)voor meer informatie.

Bij het ontwikkelen van IoT Edge-modules is het belangrijk om het verschil te begrijpen tussen de ontwikkelingsmachine en het doel-IoT Edge-apparaat waar de module uiteindelijk zal worden geïmplementeerd. De container die u bouwt om uw modulecode vast te houden, moet overeenkomen met het besturingssysteem (OS) van het *doelapparaat.* Het meest voorkomende scenario is bijvoorbeeld iemand die een module ontwikkelt op een Windows-computer die een Linux-apparaat met IoT Edge wil targeten. In dat geval zou het containerbesturingssysteem Linux zijn. Als je door deze tutorial, houd in gedachten het verschil tussen de *ontwikkeling machine OS* en de container *OS*.

Deze zelfstudie richt zich op Linux-apparaten met IoT Edge. U uw favoriete besturingssysteem gebruiken zolang uw ontwikkelingsmachine Linux-containers uitvoert. We raden u aan Visual Studio Code te gebruiken om te ontwikkelen voor Linux-apparaten, dus dat is wat deze zelfstudie zal gebruiken. U Visual Studio ook gebruiken, hoewel er verschillen zijn in ondersteuning tussen de twee tools.

In de volgende tabel worden de ondersteunde ontwikkelingsscenario's voor **Linux-containers** weergegeven in Visual Studio Code en Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux-apparaatarchitectuur** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-services** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Talen** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Meer informatie** | [Azure IoT Edge voor Visual Studio-code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-hulpprogramma's voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge-hulpprogramma's voor Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Ondersteuning voor Linux ARM64-apparaten is beschikbaar in [openbare preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Zie [ARM64 IoT Edge-modules ontwikkelen en debugen in Visual Studio Code (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)voor meer informatie.

Deze zelfstudie leert de ontwikkelingsstappen voor Visual Studio Code. Als u Visual Studio liever gebruikt, raadpleegt u de instructies in [Visual Studio 2019 gebruiken om modules voor Azure IoT Edge te ontwikkelen en te debuggen.](how-to-visual-studio-develop-module.md)

## <a name="prerequisites"></a>Vereisten

Een ontwikkelmachine:

* U uw eigen computer of een virtuele machine gebruiken, afhankelijk van uw ontwikkelingsvoorkeuren.
  * Zorg ervoor dat uw ontwikkelingsmachine geneste virtualisatie ondersteunt. Deze mogelijkheid is nodig voor het uitvoeren van een containermotor, die u in de volgende sectie installeert.
* De meeste besturingssystemen die een containerengine kunnen draaien, kunnen worden gebruikt om IoT Edge-modules voor Linux-apparaten te ontwikkelen. Deze zelfstudie maakt gebruik van een Windows-computer, maar wijst op bekende verschillen op MacOS of Linux.
* Installeer [Git](https://git-scm.com/), om module sjabloon pakketten later in deze tutorial te trekken.  
* [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Een Azure IoT Edge-apparaat op Linux:

* We raden u aan IoT Edge niet op uw ontwikkelingsmachine uit te voeren, maar in plaats daarvan een apart apparaat te gebruiken. Dit onderscheid tussen ontwikkelingsmachine en IoT Edge-apparaat weerspiegelt nauwkeuriger een echt implementatiescenario en helpt de verschillende concepten recht te houden.
* Als u geen tweede apparaat beschikbaar hebt, gebruikt u het quickstart-artikel om een IoT Edge-apparaat in Azure te maken met een [virtuele Linux-machine.](quickstart-linux.md)

Cloudresources:

* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in Azure.

## <a name="install-container-engine"></a>Containermotor installeren

IoT Edge-modules worden verpakt als containers, dus je hebt een containermotor op je ontwikkelingsmachine nodig om ze te bouwen en te beheren. We raden Docker Desktop aan voor ontwikkeling vanwege de ondersteuning en populariteit van functies. Met Docker Desktop op Windows u schakelen tussen Linux-containers en Windows-containers, zodat u eenvoudig modules ontwikkelen voor verschillende typen IoT Edge-apparaten.

Gebruik de Docker-documentatie om op uw ontwikkelingsmachine te installeren:

* [Docker Desktop voor Windows installeren](https://docs.docker.com/docker-for-windows/install/)

  * Wanneer u Docker Desktop voor Windows installeert, wordt u gevraagd of u Linux- of Windows-containers wilt gebruiken. Deze beslissing kan op elk moment worden gewijzigd met behulp van een eenvoudige schakelaar. Voor deze zelfstudie gebruiken we Linux-containers omdat onze modules zich richten op Linux-apparaten. Zie [Schakelen tussen Windows- en Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)voor meer informatie.

* [Docker-bureaublad voor Mac installeren](https://docs.docker.com/docker-for-mac/install/)

* Lees [over Docker CE](https://docs.docker.com/install/) voor installatie-informatie op verschillende Linux-platforms.

## <a name="set-up-vs-code-and-tools"></a>VS-code en -hulpprogramma's instellen

Gebruik de IoT-extensies voor Visual Studio Code om IoT Edge-modules te ontwikkelen. Deze extensies bieden projectsjablonen, automatiseren het maken van het implementatiemanifest en stellen u in staat om IoT Edge-apparaten te bewaken en te beheren. In deze sectie installeert u Visual Studio Code en de IoT-extensie en stelt u vervolgens uw Azure-account in om IoT Hub-bronnen te beheren vanuit Visual Studio Code.

1. Installeer [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelmachine.

2. Zodra de installatie is voltooid, selecteert u **Extensies weergeven** > **Extensions**.

3. Zoek naar **Azure IoT-hulpprogramma's**, wat eigenlijk een verzameling extensies is waarmee u communiceren met IoT Hub- en IoT-apparaten en iot-edgemodules ontwikkelen.

4. Selecteer **Installeren**. Elke meegeleverde extensie installeert individueel.

5. Wanneer de extensies klaar zijn met installeren, opent u het opdrachtpalet door **Opdrachtpalet weergeven** > **te**selecteren.

6. Zoek in het opdrachtpalet naar Azure en selecteer **deze: Meld u aan.** Volg de aanwijzingen om u aan te melden bij uw Azure-account.

7. Zoek en selecteer azure **IoT Hub**opnieuw in het opdrachtpalet. Volg de aanwijzingen om uw Azure-abonnement en IoT-hub te selecteren.

8. Open het gedeelte Explorer van Visual Studio Code door het pictogram in de activiteitenbalk aan de linkerkant te selecteren of door **Verkenner** > **te**selecteren.

9. Vouw onder aan de sectie Explorer het menu samengevouwen **Azure IoT Hub-apparaten** uit. U ziet de apparaten en IoT Edge-apparaten die zijn gekoppeld aan de IoT-hub die u hebt geselecteerd via het opdrachtpalet.

   ![Apparaten weergeven in uw IoT-hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Een nieuw moduleproject maken

De azure IoT-hulpprogramma's bieden projectsjablonen voor alle ondersteunde IoT Edge-moduletalen in Visual Studio Code. Deze sjablonen bevatten alle bestanden en code die u nodig hebt om een werkende module te implementeren om IoT Edge te testen, of u een startpunt te geven om de sjabloon aan te passen met uw eigen bedrijfslogica.

Voor deze zelfstudie gebruiken we de c#-modulesjabloon omdat deze de meest gebruikte sjabloon is.

### <a name="create-a-project-template"></a>Een projectsjabloon maken

Zoek en selecteer **Azure IoT Edge: Nieuwe IoT Edge-oplossing**in het opdrachtpalet Visual Studio Code. Volg de aanwijzingen en gebruik de volgende waarden om uw oplossing te maken:

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **C# Module**. |
   | Een modulenaam opgeven | Accepteer de standaard **voorbeeldmodule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke afbeeldingsopslagplaats ziet eruit als \<registernaam\>.azurecr.io/samplemodule. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-develop-for-linux/image-repository.png)

Zodra uw nieuwe oplossing wordt geladen in het venster Visual Studio Code, neemt u even de tijd om vertrouwd te raken met de bestanden die zijn gemaakt:

* De **map .vscode** bevat een bestand genaamd **launch.json**, dat wordt gebruikt voor het debuggen van modules.
* De **map modules** bevat een map voor elke module in uw oplossing. Op dit moment, dat moet alleen **SampleModule**, of welke naam je gaf aan de module. De map SampleModule bevat de hoofdprogrammacode, de metagegevens van de module en verschillende Docker-bestanden.
* Het **.env-bestand** bevat de referenties van uw containerregister. Deze referenties worden gedeeld met uw IoT Edge-apparaat, zodat het toegang heeft tot het trekken van de containerafbeeldingen.
* Het **bestand deployment.debug.template.json-bestand** en **deployment.template.json** zijn sjablonen waarmee u een implementatiemanifest maken. Een *implementatiemanifest* is een bestand dat precies definieert welke modules u op een apparaat wilt implementeren, hoe ze moeten worden geconfigureerd en hoe ze met elkaar en de cloud kunnen communiceren. De sjabloonbestanden gebruiken aanwijzers voor sommige waarden. Wanneer u de sjabloon omzet in een echt implementatiemanifest, worden de aanwijzers vervangen door waarden uit andere oplossingsbestanden. Zoek de twee gemeenschappelijke tijdelijke aanduidingen in uw implementatiesjabloon:

  * In de sectie registerreferenties wordt het adres automatisch ingevuld op basis van de informatie die u hebt verstrekt toen u de oplossing hebt gemaakt. De gebruikersnaam en het wachtwoord verwijzen echter naar de variabelen die zijn opgeslagen in het .env-bestand. Deze configuratie is voor de beveiliging, omdat het .env-bestand git wordt genegeerd, maar de implementatiesjabloon niet.
  * In de sectie SampleModule wordt de containerafbeelding niet ingevuld, ook al hebt u de afbeeldingsopslagplaats opgegeven toen u de oplossing hebt gemaakt. Deze tijdelijke aanduiding verwijst naar het **module.json-bestand** in de map SampleModule. Als u naar dat bestand gaat, ziet u dat het afbeeldingsveld wel de opslagplaats bevat, maar ook een tagwaarde die bestaat uit de versie en het platform van de container. U de versie handmatig herhalen als onderdeel van uw ontwikkelingscyclus en u selecteert het containerplatform met behulp van een switcher die we later in deze sectie introduceren.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Geef uw registerreferenties op aan de IoT Edge-agent

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw containerafbeeldingen naar het IoT Edge-apparaat te trekken.

De IoT Edge-extensie probeert uw containerregisterreferenties uit Azure te halen en ze in het omgevingsbestand te vullen. Controleer of uw referenties al zijn opgenomen. Zo niet, voeg ze dan nu toe:

1. Open het **.env-bestand** in uw moduleoplossing.
2. Voeg de **gebruikersnaam-** en **wachtwoordwaarden** toe die u hebt gekopieerd uit uw Azure-containerregister.
3. Sla uw wijzigingen op in het .env-bestand.

### <a name="select-your-target-architecture"></a>Selecteer uw doelarchitectuur

Momenteel kan Visual Studio Code C#-modules ontwikkelen voor Linux AMD64- en ARM32v7-apparaten. U moet selecteren welke architectuur u bij elke oplossing target, omdat dit van invloed is op de manier waarop de container wordt gebouwd en wordt uitgevoerd. De standaard is Linux AMD64.

1. Open het opdrachtpalet en zoek naar **Azure IoT Edge: Stel standaarddoelplatform in voor edge-oplossing**of selecteer het sneltoetspictogram in de zijbalk onder aan het venster.

   ![Architectuurpictogram selecteren in zijbalk](./media/tutorial-develop-for-linux/select-architecture.png)

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. Voor deze tutorial gebruiken we een Virtuele Ubuntu-machine als het IoT Edge-apparaat, dus houdt de standaard **amd64**.

### <a name="review-the-sample-code"></a>De voorbeeldcode controleren

De oplossingssjabloon die u hebt gemaakt, bevat voorbeeldcode voor een IoT Edge-module. Deze voorbeeldmodule ontvangt eenvoudig berichten en geeft ze vervolgens door. De pijplijnfunctionaliteit toont een belangrijk concept in IoT Edge, dat is hoe modules met elkaar communiceren.

Elke module kan meerdere *invoer-* en *uitvoerwachtrijen* in hun code hebben gedeclareerd. De IoT Edge-hub die op het apparaat wordt uitgevoerd, leidt berichten van de uitvoer van één module naar de invoer van een of meer modules. De specifieke taal voor het declareren van inputs en outputs verschilt per taal, maar het concept is hetzelfde voor alle modules. Zie [Routes declareren voor](module-composition.md#declare-routes)meer informatie over routering tussen modules.

De voorbeeldc#-code die bij de projectsjabloon wordt geleverd, gebruikt de [moduleclientklasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) van de IoT Hub SDK voor .NET.

1. Open het **Program.cs** bestand, dat zich in de **map modules/SampleModule/bevindt.**

2. Zoek in program.cs de methode **SetInputMessageHandlerAsync.**

3. Met de methode [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) wordt een invoerwachtrij ingesteld om binnenkomende berichten te ontvangen. Bekijk deze methode en zie hoe deze een invoerwachtrij met de naam **input1**initialiseert.

   ![De invoernaam zoeken in de constructor SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Zoek vervolgens de **methode SendEventAsync.**

5. De [Methode SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) verwerkt ontvangen berichten en stelt een uitvoerwachtrij in om ze door te geven. Controleer deze methode en zie dat deze een uitvoerwachtrij met de naam **uitvoer1**initialiseert.

   ![De uitvoernaam zoeken in SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Open het bestand **deployment.template.json.**

7. Vind de **modules** eigenschap van de $edgeAgent gewenste eigenschappen.

   Er moeten twee modules hier worden vermeld. De eerste is **SimulatedTemperatureSensor**, die standaard in alle sjablonen is opgenomen om gesimuleerde temperatuurgegevens te verstrekken die u gebruiken om uw modules te testen. De tweede is de **SampleModule-module** die u hebt gemaakt als onderdeel van deze oplossing.

8. Zoek onder aan het bestand de gewenste eigenschappen voor de **$edgeHub** module.

   Een van de functies van de IoT Edge hub module is het routeren van berichten tussen alle modules in een implementatie. Bekijk de waarden in de eigenschap **routes.** De eerste route, **SampleModuleToIoTHub,** gebruikt**\*** een jokerteken ( ) om berichten aan te geven die afkomstig zijn van uitvoerwachtrijen in de SampleModule-module. Deze berichten gaan in *$upstream*, dat is een gereserveerde naam die IoT Hub aangeeft. De tweede route, sensorToSampleModule, brengt berichten afkomstig van de module SimulatedTemperatureSensor en leidt deze naar de *invoerwachtrij van input1* die u in de SampleModule-code hebt geparafeerd.

   ![Routes bekijken in deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>De oplossing bouwen en pushen

U hebt de modulecode en de implementatiesjabloon gecontroleerd om een aantal belangrijke implementatieconcepten te begrijpen. Nu bent u klaar om de samplemodule-containerafbeelding te bouwen en naar uw containerregister te duwen. Met de Extensie IoT-hulpprogramma's voor Visual Studio Code genereert deze stap ook het implementatiemanifest op basis van de informatie in het sjabloonbestand en de module-informatie uit de oplossingsbestanden.

### <a name="sign-in-to-docker"></a>Aanmelden bij Docker

Geef uw containerregisterreferenties op aan Docker, zodat deze uw containerafbeelding kan pushen om in het register te worden opgeslagen.

1. Open de geïntegreerde visual studiocodeterminal door **View** > **Terminal te**selecteren.

2. Meld u aan bij Docker met de Azure-containerregisterreferenties die u hebt opgeslagen na het maken van het register.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk ontvangt u een beveiligingswaarschuwing `--password-stdin`waarin het gebruik van . Hoewel die aanbevolen procedures worden aanbevolen voor productiescenario's, valt deze buiten het bereik van deze zelfstudie. Zie de [inlogreferentie voor docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

### <a name="build-and-push"></a>Bouwen en pushen

Visual Studio Code heeft nu toegang tot uw containerregister, dus het is tijd om de oplossingscode om te zetten in een containerafbeelding.

1. Klik in de verkenner van Visual Studio Code met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **IoT Edge-oplossing bouwen en pushen**.

   ![IoT Edge-modules bouwen en pushen](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   De opdracht bouwen en pushen start drie bewerkingen. Ten eerste maakt het een nieuwe map in de oplossing genaamd **config** die het volledige implementatiemanifest bevat, opgebouwd uit informatie in de implementatiesjabloon en andere oplossingsbestanden. Ten tweede `docker build` wordt het uitgevoerd om de containerafbeelding te bouwen op basis van het juiste dockerbestand voor uw doelarchitectuur. Vervolgens wordt `docker push` het uitgevoerd om de afbeeldingsopslagplaats naar uw containerregister te duwen.

   Dit proces kan de eerste keer enkele minuten duren, maar is sneller de volgende keer dat u de opdrachten uitvoert.

2. Open het **bestand deployment.amd64.json** in de nieuw gemaakte config-map. De bestandsnaam weerspiegelt de doelarchitectuur, dus het zal anders zijn als u een andere architectuur kiest.

3. Merk op dat de twee parameters die tijdelijke aanduidingen nu had zijn ingevuld met hun juiste waarden. De sectie **registerCredentials** heeft uw registergebruikersnaam en wachtwoord uit het .env-bestand gehaald. De **SampleModule** heeft de volledige afbeeldingsopslagplaats met de naam, versie en architectuurtag uit het module.json-bestand.

4. Open het **bestand module.json** in de map SampleModule.

5. Wijzig het versienummer voor de moduleafbeelding. (De versie, niet de $schema-versie.) Het nummer van de patchversie bijvoorbeeld verhogen naar **0.0.2** alsof we een kleine oplossing in de modulecode hadden gemaakt.

   >[!TIP]
   >Moduleversies maken versiebeheer mogelijk en stellen u in staat om wijzigingen op een kleine set apparaten te testen voordat u updates voor de productie implementeert. Als u de moduleversie niet verhoogt voordat u bouwt en duwt, overschrijft u de opslagplaats in uw containerregister.

6. Sla uw wijzigingen op in het module.json-bestand.

7. Klik nogmaals met de rechtermuisknop op het bestand **deployment.template.json** en selecteer opnieuw **IoT Edge-oplossing bouwen en pushen.**

8. Open het **bestand deployment.amd64.json** opnieuw. Merk op dat er geen nieuw bestand is gemaakt toen u de opdracht Build en Push opnieuw hebt uitgevoerd. Integendeel, hetzelfde bestand is bijgewerkt om de wijzigingen weer te geven. De samplemodule-afbeelding verwijst nu naar de 0.0.2-versie van de container.

9. Als u verder wilt controleren wat de opdracht build en push heeft gedaan, gaat u naar de [Azure-portal](https://portal.azure.com) en navigeert u naar uw containerregister.

10. Selecteer in uw containerregister **repositories en** **samplemodule**. Controleer of beide versies van de afbeelding naar het register zijn gepusht.

    ![Beide afbeeldingsversies weergeven in containerregister](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Problemen oplossen

Als u fouten tegenkomt bij het bouwen en pushen van uw moduleafbeelding, heeft dit vaak te maken met dockerconfiguratie op uw ontwikkelingsmachine. Gebruik de volgende controles om uw configuratie te controleren:

* Heeft u `docker login` de opdracht uitgevoerd met behulp van de referenties die u hebt gekopieerd uit uw containerregister? Deze referenties zijn anders dan die welke u gebruikt om u aan te melden bij Azure.
* Hebt u de juiste containeropslagplaats? Heeft het uw juiste naam van het containerregister en uw correcte modulenaam? Open het **module.json-bestand** in de map SampleModule om te controleren. De repository-waarde moet er uitzien als ** \<\>registernaam .azurecr.io/samplemodule**.
* Als u een andere naam dan **SampleModule** voor uw module hebt gebruikt, is die naam dan consistent in de hele oplossing?
* Draait uw machine hetzelfde type containers als u bouwt? Deze tutorial is voor Linux IoT Edge-apparaten, dus Visual Studio Code moet zeggen **amd64** of **arm32v7** in de zijbalk, en Docker Desktop moet worden uitgevoerd Linux containers.  

## <a name="deploy-modules-to-device"></a>Modules implementeren op apparaat

U hebt gecontroleerd of de ingebouwde containerafbeeldingen zijn opgeslagen in uw containerregister, dus het is tijd om ze te implementeren op een apparaat. Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de visual studiocodeverkenner de sectie Azure IoT Hub-apparaten uit.

2. Klik met de rechtermuisknop op het IoT Edge-apparaat dat u wilt implementeren en selecteer **vervolgens Implementatie maken voor één apparaat**.

   ![Implementatie voor één apparaat maken](./media/tutorial-develop-for-linux/create-deployment.png)

3. Navigeer in de verkenner naar de **map config** en selecteer vervolgens het bestand **deployment.amd64.json.**

   Gebruik het bestand deployment.template.json niet, waarin de referenties van het containerregister of de moduleafbeeldingswaarden niet zijn opgenomen. Als u zich richt op een Linux ARM32-apparaat, wordt het implementatiemanifest deployment.arm32v7.json genoemd.

4. Vouw de details voor uw IoT Edge-apparaat uit en vouw vervolgens de lijst **Modules** voor uw apparaat uit.

5. Gebruik de vernieuwingsknop om de apparaatweergave bij te werken totdat u de modules SimulatedTemperatureSensor en SampleModule op uw apparaat ziet draaien.

   Het kan enkele minuten duren voordat beide modules zijn gestart. De IoT Edge runtime moet zijn nieuwe implementatiemanifest ontvangen, de moduleafbeeldingen uit de runtime van de container halen en vervolgens elke nieuwe module starten.

   ![Modules weergeven die worden uitgevoerd op uw IoT Edge-apparaat](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Berichten vanaf apparaat weergeven

De SampleModule-code ontvangt berichten via de invoerwachtrij en geeft deze door de uitvoerwachtrij. In het implementatiemanifest worden routes gedeclareerd die berichten doorgegeven aan SampleModule van SimulatedTemperatureSensor en vervolgens berichten doorgestuurd van SampleModule naar IoT Hub. Met de Azure IoT-hulpprogramma's voor Visual Studio Code u berichten zien wanneer ze vanaf uw afzonderlijke apparaten bij IoT Hub aankomen.

1. Klik in de verkenner voor Visual Studio Code met de rechtermuisknop op het IoT Edge-apparaat dat u wilt controleren en selecteer **vervolgens Het ingebouwde gebeurteniseindpunt starten.**

2. Bekijk het uitvoervenster in Visual Studio Code om berichten te zien die bij uw IoT-hub binnenkomen.

   ![Binnenkomend apparaat weergeven in cloudberichten](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wijzigingen weergeven op apparaat

Als u wilt zien wat er op uw apparaat zelf gebeurt, gebruikt u de opdrachten in deze sectie om de runtime en modules van IoT Edge die op uw apparaat worden uitgevoerd, te inspecteren.

De opdrachten in deze sectie zijn voor uw IoT Edge-apparaat, niet voor uw ontwikkelingsmachine. Als u een virtuele machine voor uw IoT Edge-apparaat gebruikt, maakt u er nu verbinding mee. Ga in Azure naar de overzichtspagina van de virtuele machine en selecteer **Verbinding maken** om toegang te krijgen tot de beveiligde shell-verbinding.

* Bekijk alle modules die op uw apparaat zijn geïmplementeerd en controleer de status ervan:

   ```bash
   iotedge list
   ```

   U ziet vier modules: de twee IoT Edge runtime-modules, SimulatedTemperatureSensor en SampleModule. Alle vier moeten worden vermeld als running.

* Controleer de logboeken voor een specifieke module:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge-modules zijn casegevoelig.

   De logboeken SimulatedTemperatureSensor en SampleModule moeten de berichten weergeven die ze verwerken. De edgeAgent-module is verantwoordelijk voor het starten van de andere modules, zodat de logboeken informatie bevatten over de implementatievan het implementatiemanifest. Als een module niet wordt vermeld of niet wordt uitgevoerd, hebben de edgeAgent-logboeken waarschijnlijk de fouten. De edgeHub-module is verantwoordelijk voor de communicatie tussen de modules en IoT Hub. Als de modules actief zijn, maar de berichten niet bij uw IoT-hub aankomen, hebben de edgeHub-logboeken waarschijnlijk de fouten.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stelt u Visual Studio Code in op uw ontwikkelingsmachine en hebt u uw eerste IoT Edge-module van deze handleiding geïmplementeerd. Nu u de basisconcepten kent, probeert u functionaliteit toe te voegen aan een module, zodat deze de gegevens kan analyseren die erdoorheen gaan. Kies uw voorkeurstaal:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)
