---
title: Zelfstudie - Module ontwikkelen voor Windows-apparaten met Azure IoT Edge
description: Deze zelfstudie doorloopt het instellen van uw ontwikkelmachine en cloudresources om IoT Edge-modules te ontwikkelen met Windows-containers voor Windows-apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 58a63c9e11cf86318f0e9f051d034cbbaf7c40a9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772252"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Zelfstudie: IoT Edge-modules ontwikkelen voor Windows-apparaten

Gebruik Visual Studio om code te ontwikkelen en te implementeren op Windows-apparaten met IoT Edge.

In de quickstart hebt u een IoT Edge-apparaat gemaakt met een virtuele Windows-machine en een vooraf gebouwde module van de Azure Marketplace geïmplementeerd. Deze zelfstudie doorloopt wat er nodig is om uw eigen code te ontwikkelen en te implementeren op een IoT Edge-apparaat. Deze zelfstudie is een handige voorwaarde voor de andere zelfstudies, die meer in detail treden over specifieke programmeertalen of Azure-services.

In deze zelfstudie wordt het voorbeeld gebruikt van het implementeren van een **C#-module op een Windows-apparaat.** Dit voorbeeld is gekozen omdat dit het meest voorkomende ontwikkelingsscenario is. Als u geïnteresseerd bent in het ontwikkelen in een andere taal of van plan bent Azure-services als modules te implementeren, is deze zelfstudie nog steeds handig om meer te weten te komen over de ontwikkeltools. Zodra u de ontwikkelingsconcepten begrijpt, u uw voorkeurstaal of Azure-service kiezen om in de details te duiken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Stel je ontwikkelmachine in.
> * Gebruik de IoT Edge-tools voor Visual Studio om een nieuw project te maken.
> * Bouw uw project als container en sla het op in een Azure-containerregister.
> * Implementeer uw code op een IoT Edge-apparaat.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Belangrijkste concepten

Deze zelfstudie doorloopt de ontwikkeling van een IoT Edge-module. Een *IoT Edge-module*, of soms gewoon *module* in het kort, is een container die uitvoerbare code bevat. U een of meer modules implementeren op een IoT Edge-apparaat. Modules voeren specifieke taken uit, zoals het innemen van gegevens van sensoren, het uitvoeren van gegevensanalyse of gegevensreinigingsbewerkingen of het verzenden van berichten naar een IoT-hub. Zie [Azure IoT Edge-modules begrijpen](iot-edge-modules.md)voor meer informatie.

Bij het ontwikkelen van IoT Edge-modules is het belangrijk om het verschil te begrijpen tussen de ontwikkelingsmachine en het doel-IoT Edge-apparaat waar de module uiteindelijk zal worden geïmplementeerd. De container die u bouwt om uw modulecode vast te houden, moet overeenkomen met het besturingssysteem (OS) van het *doelapparaat.* Voor windows container ontwikkeling, dit concept is eenvoudiger omdat Windows containers alleen draaien op Windows-besturingssystemen. Maar u bijvoorbeeld uw Windows-ontwikkelingsmachine gebruiken om modules te bouwen voor Linux IoT Edge-apparaten. In dat scenario moet je ervoor zorgen dat je ontwikkelmachine Linux-containers draait. Als je door deze tutorial, houd in gedachten het verschil tussen *de ontwikkeling machine OS* en de container *OS*.

Deze zelfstudie richt zich op Windows-apparaten met IoT Edge. Windows IoT Edge-apparaten gebruiken Windows-containers. We raden u aan Visual Studio te gebruiken om te ontwikkelen voor Windows-apparaten, dus dat is wat deze zelfstudie zal gebruiken. U Visual Studio Code ook gebruiken, hoewel er verschillen zijn in ondersteuning tussen de twee tools.

In de volgende tabel worden de ondersteunde ontwikkelingsscenario's voor **Windows-containers** weergegeven in Visual Studio Code en Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-services** | Azure Functions <br> Azure Stream Analytics |   |
| **Talen** | C# (foutopsporing niet ondersteund) | C <br> C# |
| **Meer informatie** | [Azure IoT Edge voor Visual Studio-code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-hulpprogramma's voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge-hulpprogramma's voor Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Vereisten

Een ontwikkelmachine:

* Windows 10 met 1809 update of nieuwer.
* U uw eigen computer of een virtuele machine gebruiken, afhankelijk van uw ontwikkelingsvoorkeuren.
  * Zorg ervoor dat uw ontwikkelingsmachine geneste virtualisatie ondersteunt. Deze mogelijkheid is nodig voor het uitvoeren van een containermotor, die u in de volgende sectie installeert.
* Installeer [Git](https://git-scm.com/).

Een Azure IoT Edge-apparaat in Windows:

* We raden u aan IoT Edge niet op uw ontwikkelingsmachine uit te voeren, maar in plaats daarvan een apart apparaat te gebruiken. Dit onderscheid tussen ontwikkelingsmachine en IoT Edge-apparaat weerspiegelt nauwkeuriger een echt implementatiescenario en helpt de verschillende concepten recht te houden.
* Als u geen tweede apparaat beschikbaar hebt, gebruikt u het snelstartartikel om een IoT Edge-apparaat in Azure te maken met een [virtuele Windows-machine.](quickstart.md)

Cloudresources:

* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in Azure.

## <a name="install-container-engine"></a>Containermotor installeren

IoT Edge-modules worden verpakt als containers, dus u hebt een containermotor op uw ontwikkelingsmachine nodig om de containers te bouwen en te beheren. We raden aan Docker Desktop te gebruiken voor ontwikkeling vanwege de vele functies en populariteit als containermotor. Met Docker Desktop op een Windows-computer u schakelen tussen Linux-containers en Windows-containers, zodat u eenvoudig modules ontwikkelen voor verschillende typen IoT Edge-apparaten.

Gebruik de Docker-documentatie om op uw ontwikkelingsmachine te installeren:

* [Docker Desktop voor Windows installeren](https://docs.docker.com/docker-for-windows/install/)

  * Wanneer u Docker Desktop voor Windows installeert, wordt u gevraagd of u Linux- of Windows-containers wilt gebruiken. Gebruik voor deze zelfstudie **Windows-containers**. Zie [Schakelen tussen Windows- en Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)voor meer informatie.

## <a name="set-up-visual-studio-and-tools"></a>Visual Studio en hulpprogramma's instellen

De IoT-extensies voor Visual Studio helpen u bij het ontwikkelen van IoT Edge-modules. Deze extensies bieden projectsjablonen, automatiseren het maken van het implementatiemanifest en stellen u in staat om IoT Edge-apparaten te bewaken en te beheren. In deze sectie installeert u Visual Studio en de IoT Edge-extensie en stelt u vervolgens uw Azure-account in om IoT Hub-bronnen vanuit Visual Studio te beheren.

Deze tutorial leert de ontwikkelstappen voor Visual Studio 2019. Als u Visual Studio 2017 (versie 15.7 of hoger) gebruikt, zijn de stappen vergelijkbaar. Als u Visual Studio Code liever gebruikt, raadpleegt u de instructies in [Visual Studio Code gebruiken om modules voor Azure IoT Edge te ontwikkelen en te debuggen.](how-to-vs-code-develop-module.md)

1. Bereid Visual Studio 2019 voor op je ontwikkelmachine.

   * Als je Visual Studio nog niet op je ontwikkelmachine hebt, [installeerje Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) met de volgende workloads:

      * Azure-ontwikkeling
      * Desktopontwikkeling met C++
      * Platformoverschrijdende ontwikkelmogelijkheden van .NET Core

   * Als je Visual Studio 2019 al op je ontwikkelmachine hebt staan, volg je de stappen in [Visual Studio wijzigen](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) om de vereiste workloads toe te voegen.

2. Download en installeer de [Azure IoT Edge Tools-extensie](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) voor Visual Studio 2019.

   Als u Visual Studio 2017 (versie 15.7 of hoger) gebruikt, downloadt en installeert u de [Azure IoT Edge Tools voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Wanneer uw installaties zijn voltooid, opent u Visual Studio 2019 en selecteert **u Doorgaan zonder code**.

4. Selecteer Cloud Explorer **weergeven** > **Cloud Explorer**.

5. Selecteer het profielpictogram in de cloudverkenner en meld u aan bij uw Azure-account als u nog niet bent aangemeld.

6. Zodra u zich aanmeldt, worden uw Azure-abonnementen weergegeven. Breid het abonnement uit met uw IoT-hub.

7. Vouw onder uw abonnement **IoT-hubs** uit en vervolgens uw IoT-hub. U ziet een lijst met uw IoT-apparaten en deze verkenner gebruiken om deze te beheren.

   ![Toegang tot IoT Hub-bronnen in Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Een nieuw moduleproject maken

De Azure IoT Edge Tools-extensie biedt projectsjablonen voor alle ondersteunde IoT Edge-moduletalen in Visual Studio. Deze sjablonen bevatten alle bestanden en code die u nodig hebt om een werkende module te implementeren om IoT Edge te testen, of u een startpunt te geven om de sjabloon aan te passen met uw eigen bedrijfslogica.

1. Selecteer**New** > Nieuw **project bestand...** > **Project...**

2. Zoek in het nieuwe projectvenster naar **IoT Edge** en kies het **Azure IoT Edge -project (Windows AMD64).** Klik op **Volgende**.

   ![Een nieuw Azure IoT Edge-project maken](./media/tutorial-develop-for-windows/new-project.png)

3. Wijzig in het venster uw nieuwe project de naam van het project en de oplossing voor iets beschrijvends zoals **CSharpTutorialApp**. Klik **op Maken** om het project te maken.

   ![Een nieuw Azure IoT Edge-project configureren](./media/tutorial-develop-for-windows/configure-project.png)

4. Configureer uw project in het venster Module toevoegen met de volgende waarden:

   | Veld | Waarde |
   | ----- | ----- |
   | Sjabloon voor Visual Studio | Selecteer **C#-module**. |
   | Modulenaam | Accepteer de standaard **IotEdgeModule1**. |
   | Url van opslagplaats | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf ingevuld vanaf de waarde van de projectnaam module. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U de waarde van de **inlogserver** ophalen op de **pagina Overzicht** van uw containerregister in de Azure-portal. <br><br> De uiteindelijke afbeeldingsopslagplaats ziet eruit als \<registernaam\>.azurecr.io/iotedgemodule1. |

      ![Uw project configureren op doelapparaat, moduletype en containerregister](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Selecteer **Toevoegen** om de module te maken.

Zodra uw nieuwe project wordt geladen in het venster Visual Studio, neemt u even de tijd om vertrouwd te raken met de bestanden die het heeft gemaakt:

* Een IoT Edge-project genaamd **CSharpTutorialApp**.
  * De map **Modules** bevat verwijzingen naar de modules die in het project zijn opgenomen. In dit geval moet het gewoon IotEdgeModule1 zijn.
  * Het verborgen **.env-bestand** bevat de referenties van uw containerregister. Deze referenties worden gedeeld met uw IoT Edge-apparaat, zodat het toegang heeft tot het trekken van de containerafbeeldingen.
  * Het bestand **deployment.template.json** is een sjabloon waarmee u een implementatiemanifest maken. Een *implementatiemanifest* is een bestand dat precies definieert welke modules u op een apparaat wilt implementeren, hoe ze moeten worden geconfigureerd en hoe ze met elkaar en de cloud kunnen communiceren.
    > [!TIP]
    > In de sectie registerreferenties wordt het adres automatisch ingevuld op basis van de informatie die u hebt verstrekt toen u de oplossing hebt gemaakt. De referentievariabelen voor gebruikersnaam en wachtwoord die zijn opgeslagen in het .env-bestand. Dit is voor de beveiliging, omdat het .env-bestand git wordt genegeerd, maar de implementatiesjabloon niet.
* Een IoT Edge-moduleproject genaamd **IotEdgeModule1**.
  * Het **program.cs-bestand** bevat de standaard C#-modulecode die bij de projectsjabloon wordt geleverd. De standaardmodule neemt invoer van een bron en geeft deze door aan IoT Hub.
  * Het **module.json-bestand** bevat details over de module, inclusief de volledige afbeeldingsopslagplaats, de afbeeldingsversie en welke Dockerfile voor elk ondersteund platform moet worden gebruikt.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Geef uw registerreferenties op aan de IoT Edge-agent

De IoT Edge-runtime heeft uw registerreferenties nodig om uw containerafbeeldingen naar het IoT Edge-apparaat te trekken. De IoT Edge-extensie probeert uw containerregistergegevens uit Azure te halen en deze in de implementatiesjabloon te vullen.

1. Open het bestand **deployment.template.json** in uw moduleoplossing.

1. Zoek de eigenschap **registryCredentials** in de gewenste $edgeAgent gewenste eigenschappen. Het moet uw registeradres automatisch laten invullen van de informatie die u bij het maken van het project hebt opgegeven, en vervolgens moeten gebruikersnaam- en wachtwoordvelden variabele namen bevatten. Bijvoorbeeld:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Open het **.env-bestand** in uw moduleoplossing. (Het is standaard verborgen in de Oplossingsverkenner, dus het kan nodig zijn om de knop **Alle bestanden weergeven** te selecteren om het weer te geven.)

1. Voeg de **waarden gebruikersnaam** en **wachtwoord** toe die u hebt gekopieerd uit uw Azure-containerregister.

1. Sla uw wijzigingen op in het .env-bestand.

### <a name="review-the-sample-code"></a>De voorbeeldcode controleren

De oplossingssjabloon die u hebt gemaakt, bevat voorbeeldcode voor een IoT Edge-module. Deze voorbeeldmodule ontvangt eenvoudig berichten en geeft ze vervolgens door. De pijplijnfunctionaliteit toont een belangrijk concept in IoT Edge, dat is hoe modules met elkaar communiceren.

Elke module kan meerdere *invoer-* en *uitvoerwachtrijen* in hun code hebben gedeclareerd. De IoT Edge-hub die op het apparaat wordt uitgevoerd, leidt berichten van de uitvoer van één module naar de invoer van een of meer modules. De specifieke taal voor het declareren van inputs en outputs verschilt per taal, maar het concept is hetzelfde voor alle modules. Zie [Routes declareren voor](module-composition.md#declare-routes)meer informatie over routering tussen modules.

De voorbeeldc#-code die bij de projectsjabloon wordt geleverd, gebruikt de [moduleclientklasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) van de IoT Hub SDK voor .NET.

1. Zoek in het **program.cs-bestand** de methode **SetInputMessageHandlerAsync.**

2. Met de methode [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) wordt een invoerwachtrij ingesteld om binnenkomende berichten te ontvangen. Bekijk deze methode en zie hoe deze een invoerwachtrij met de naam **input1**initialiseert.

   ![De invoernaam zoeken in de constructor SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Zoek vervolgens de **methode SendEventAsync.**

4. De [Methode SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) verwerkt ontvangen berichten en stelt een uitvoerwachtrij in om ze door te geven. Controleer deze methode en zie dat deze een uitvoerwachtrij met de naam **uitvoer1**initialiseert.

   ![De uitvoernaam zoeken in de SendEventAsync-constructor](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open het bestand **deployment.template.json.**

6. Vind de **modules** eigenschap van de $edgeAgent gewenste eigenschappen.

   Er moeten twee modules hier worden vermeld. De eerste is **SimulatedTemperatureSensor**, die standaard in alle sjablonen is opgenomen om gesimuleerde temperatuurgegevens te verstrekken die u gebruiken om uw modules te testen. De tweede is de **IotEdgeModule1-module** die u hebt gemaakt als onderdeel van dit project.

   Deze eigenschap modules geeft aan welke modules moeten worden opgenomen in de implementatie naar uw apparaat of apparaten.

7. Vind de **routes** eigenschap van de $edgeHub gewenste eigenschappen.

   Een van de functies van de IoT Edge hub module is het routeren van berichten tussen alle modules in een implementatie. Bekijk de waarden in de eigenschap routes. De eerste route, **IotEdgeModule1ToIoTHub**,**\*** gebruikt een jokerteken ( ) om berichten op te nemen die afkomstig zijn van een uitvoerwachtrij in de IotEdgeModule1-module. Deze berichten gaan in *$upstream*, dat is een gereserveerde naam die IoT Hub aangeeft. De tweede route, **sensorToIotEdgeModule1,** brengt berichten afkomstig van de module SimulatedTemperatureSensor en leidt ze naar de *input1-invoerwachtrij* van de IotEdgeModule1-module.

   ![Routes bekijken in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>De oplossing bouwen en pushen

U hebt de modulecode en de implementatiesjabloon gecontroleerd om een aantal belangrijke implementatieconcepten te begrijpen. Nu bent u klaar om de iotEdgeModule1-containerafbeelding te bouwen en naar uw containerregister te duwen. Met de extensie IoT-hulpprogramma's voor Visual Studio genereert deze stap ook het implementatiemanifest op basis van de informatie in het sjabloonbestand en de module-informatie uit de oplossingsbestanden.

### <a name="sign-in-to-docker"></a>Aanmelden bij Docker

Geef uw containerregisterreferenties op aan Docker op uw ontwikkelingsmachine, zodat deze uw containerafbeelding kan pushen om in het register te worden opgeslagen.

1. Open PowerShell of een opdrachtprompt.

2. Meld u aan bij Docker met de Azure-containerregisterreferenties die u hebt opgeslagen na het maken van het register.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk ontvangt u een beveiligingswaarschuwing `--password-stdin`waarin het gebruik van . Hoewel die aanbevolen procedures worden aanbevolen voor productiescenario's, valt deze buiten het bereik van deze zelfstudie. Zie de [inlogreferentie voor docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

### <a name="build-and-push"></a>Bouwen en pushen

Uw ontwikkelmachine heeft nu toegang tot uw containerregister en uw IoT Edge-apparaten ook. Het is tijd om de projectcode om te zetten in een containerafbeelding.

1. Klik met de rechtermuisknop op de **projectmap CSharpTutorialApp** en selecteer **IoT-randmodules bouwen en pushen**.

   ![IoT Edge-modules bouwen en pushen](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   De opdracht bouwen en pushen start drie bewerkingen. Ten eerste maakt het een nieuwe map in de oplossing genaamd **config** die het volledige implementatiemanifest bevat, opgebouwd uit informatie in de implementatiesjabloon en andere oplossingsbestanden. Ten tweede `docker build` wordt het uitgevoerd om de containerafbeelding te bouwen op basis van het juiste dockerbestand voor uw doelarchitectuur. Vervolgens wordt `docker push` het uitgevoerd om de afbeeldingsopslagplaats naar uw containerregister te duwen.

   Dit proces kan de eerste keer enkele minuten duren, maar is sneller de volgende keer dat u de opdrachten uitvoert.

2. Open het bestand **deployment.windows-amd64.json** in de nieuw gemaakte config-map. (De map config wordt mogelijk niet weergegeven in de Solution Explorer in Visual Studio. Als dat het geval is, selecteert u het pictogram **Alle bestanden weergeven** in de taakbalk van Solution Explorer.)

3. Zoek de **imageparameter** van de sectie IotEdgeModule1. Merk op dat de afbeelding de volledige afbeeldingsopslagplaats bevat met de naam, versie en architectuurtag uit het module.json-bestand.

4. Open het **bestand module.json** in de map IotEdgeModule1.

5. Wijzig het versienummer voor de moduleafbeelding. (De versie, niet de $schema-versie.) Het nummer van de patchversie bijvoorbeeld verhogen naar **0.0.2** alsof we een kleine oplossing in de modulecode hadden gemaakt.

   >[!TIP]
   >Moduleversies maken versiebeheer mogelijk en stellen u in staat om wijzigingen op een kleine set apparaten te testen voordat u updates voor de productie implementeert. Als u de moduleversie niet verhoogt voordat u bouwt en duwt, overschrijft u de opslagplaats in uw containerregister.

6. Sla uw wijzigingen op in het module.json-bestand.

7. Klik nogmaals met de rechtermuisknop op de **projectmap CSharpTutorialApp** en selecteer opnieuw **IoT Edge-modules bouwen en pushen.**

8. Open het **bestand deployment.windows-amd64.json** opnieuw. Merk op dat er geen nieuw bestand is gemaakt toen u de opdracht Build en Push opnieuw hebt uitgevoerd. Integendeel, hetzelfde bestand is bijgewerkt om de wijzigingen weer te geven. De IotEdgeModule1-afbeelding verwijst nu naar de 0.0.2-versie van de container. Deze wijziging in het implementatiemanifest is hoe u het IoT Edge-apparaat vertelt dat er een nieuwe versie van een module moet worden aantetrekken.

9. Als u verder wilt controleren wat de opdracht build en push heeft gedaan, gaat u naar de [Azure-portal](https://portal.azure.com) en navigeert u naar uw containerregister.

10. Selecteer **repositories in** uw containerregister en **iotedgemodule1**. Controleer of beide versies van de afbeelding naar het register zijn gepusht.

    ![Beide afbeeldingsversies weergeven in containerregister](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Problemen oplossen

Als u fouten tegenkomt bij het bouwen en pushen van uw moduleafbeelding, heeft dit vaak te maken met dockerconfiguratie op uw ontwikkelingsmachine. Gebruik de volgende controles om uw configuratie te controleren:

* Heeft u `docker login` de opdracht uitgevoerd met behulp van de referenties die u hebt gekopieerd uit uw containerregister? Deze referenties zijn anders dan die welke u gebruikt om u aan te melden bij Azure.
* Hebt u de juiste containeropslagplaats? Heeft het uw juiste naam van het containerregister en uw correcte modulenaam? Open het **module.json-bestand** in de map IotEdgeModule1 om te controleren. De repository-waarde moet er uitzien als ** \<\>registernaam .azurecr.io/iotedgemodule1**.
* Als u een andere naam dan **IotEdgeModule1** voor uw module hebt gebruikt, is die naam dan consistent in de hele oplossing?
* Draait uw machine hetzelfde type containers als u bouwt? Deze zelfstudie is bedoeld voor Windows IoT Edge-apparaten, dus uw Visual Studio-bestanden moeten de **windows-amd64-extensie** hebben en Docker Desktop moet Windows-containers uitvoeren.

## <a name="deploy-modules-to-device"></a>Modules implementeren op apparaat

U hebt gecontroleerd of de ingebouwde containerafbeeldingen zijn opgeslagen in uw containerregister, dus het is tijd om ze te implementeren op een apparaat. Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Open de Cloud Explorer in Visual Studio en breid de details uit voor uw IoT-hub.

2. Selecteer de naam van het apparaat waarop u wilt implementeren. Selecteer **implementatie maken**in de lijst **Acties** .

   ![Implementatie voor één apparaat maken](./media/tutorial-develop-for-windows/create-deployment.png)

3. Navigeer in de verkenner naar de map config van uw project en selecteer het bestand **deployment.windows-amd64.json.** Dit bestand bevindt zich vaak op`C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Gebruik het bestand deployment.template.json niet, waarin niet de volledige moduleafbeeldingswaarden staan.

4. Vouw de details voor uw IoT Edge-apparaat in de Cloud Explorer uit om de modules op uw apparaat te bekijken.

5. Gebruik de knop **Vernieuwen** om de apparaatstatus bij te werken om te zien of de Modules SimulatedTemperatureSensor en IotEdgeModule1 uw apparaat zijn geïmplementeerd.

   ![Modules weergeven die worden uitgevoerd op uw IoT Edge-apparaat](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Berichten vanaf apparaat weergeven

De IotEdgeModule1-code ontvangt berichten via de invoerwachtrij en geeft deze door de uitvoerwachtrij. In het implementatiemanifest worden routes gedeclareerd die berichten van SimulatedTemperatureSensor naar IotEdgeModule1 hebben doorgegeven en vervolgens berichten van IotEdgeModule1 naar IoT Hub doorgestuurd. Met de Azure IoT Edge-tools voor Visual Studio u berichten zien wanneer ze vanaf uw afzonderlijke apparaten bij IoT Hub aankomen.

1. Selecteer in de Visual Studio-cloudexplorer de naam van het IoT Edge-apparaat dat u hebt geïmplementeerd.

2. Selecteer in het menu **Acties** **het eindpunt van ingebouwde gebeurtenismeting starten**.

3. Bekijk de sectie **Uitvoer** in Visual Studio om berichten te zien die bij uw IoT-hub binnenkomen.

   Het kan enkele minuten duren voordat beide modules zijn gestart. De IoT Edge runtime moet zijn nieuwe implementatiemanifest ontvangen, de moduleafbeeldingen uit de runtime van de container halen en vervolgens elke nieuwe module starten.

   ![Binnenkomend apparaat weergeven in cloudberichten](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wijzigingen weergeven op apparaat

Als u wilt zien wat er op uw apparaat zelf gebeurt, gebruikt u de opdrachten in deze sectie om de runtime en modules van IoT Edge die op uw apparaat worden uitgevoerd, te inspecteren.

De opdrachten in deze sectie zijn voor uw IoT Edge-apparaat, niet voor uw ontwikkelingsmachine. Als u een virtuele machine voor uw IoT Edge-apparaat gebruikt, maakt u er nu verbinding mee. Ga in Azure naar de overzichtspagina van de virtuele machine en selecteer **Verbinding maken** om toegang te krijgen tot de verbinding met extern bureaublad. Open op het apparaat een opdracht of `iotedge` PowerShell-venster om de opdrachten uit te voeren.

* Bekijk alle modules die op uw apparaat zijn geïmplementeerd en controleer de status ervan:

   ```cmd
   iotedge list
   ```

   U ziet vier modules: de twee IoT Edge runtime-modules, SimulatedTemperatureSensor en IotEdgeModule1. Alle vier moeten worden vermeld als running.

* Controleer de logboeken voor een specifieke module:

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge-modules zijn casegevoelig.

   De simulatedtemperaturesensor- en iotEdgeModule1-logboeken moeten de berichten weergeven die ze verwerken. De edgeAgent-module is verantwoordelijk voor het starten van de andere modules, zodat de logboeken informatie bevatten over de implementatievan het implementatiemanifest. Als een module niet wordt vermeld of niet wordt uitgevoerd, hebben de edgeAgent-logboeken waarschijnlijk de fouten. De edgeHub-module is verantwoordelijk voor de communicatie tussen de modules en IoT Hub. Als de modules actief zijn, maar de berichten niet bij uw IoT-hub aankomen, hebben de edgeHub-logboeken waarschijnlijk de fouten.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stel je Visual Studio 2019 in op je ontwikkelmachine en zet je je eerste IoT Edge-module van daaruit in. Nu u de basisconcepten kent, probeert u functionaliteit toe te voegen aan een module, zodat deze de gegevens kan analyseren die erdoorheen gaan. Kies uw voorkeurstaal:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
