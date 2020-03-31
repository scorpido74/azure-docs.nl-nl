---
title: Externe bewakingsoplossing lokaal implementeren - IntelliJ IDE - Azure | Microsoft Documenten
description: Deze handleiding laat u zien hoe u de Remote Monitoring oplossingsversneller implementeren op uw lokale machine door IntelliJ te gebruiken voor testen en ontwikkelen.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888816"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>De remote monitoring oplossingsversneller lokaal implementeren - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In dit artikel ziet u hoe u de remote monitoring oplossingsversneller implementeren op uw lokale machine voor testen en ontwikkelen. Je leert hoe je de microservices in IntelliJ uitvoeren. Een lokale implementatie van microservices maakt gebruik van de volgende cloudservices: IoT Hub, Azure Cosmos DB, Azure Streaming Analytics en Azure Time Series Insights.

Als u de remote monitoring-oplossingsversneller in Docker op uw lokale machine wilt uitvoeren, [raadpleegt u De versneller van de oplossing voor externe bewaking lokaal implementeren - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Vereisten

Als u de Azure-services wilt implementeren die worden gebruikt door de versneller van de oplossing voor externe bewaking, hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

### <a name="machine-setup"></a>Machine-installatie

Als u de lokale implementatie wilt voltooien, hebt u de volgende hulpprogramma's nodig die op uw lokale ontwikkelingsmachine zijn geïnstalleerd:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala plug-in](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT-plug-in](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT Executor plugin](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 is een voorwaarde voor de PCS CLI die de scripts gebruiken om Azure-resources te maken. Gebruik node.js v10 niet.

> [!NOTE]
> IntelliJ IDE is beschikbaar voor Windows en Mac.

## <a name="download-the-source-code"></a>De broncode downloaden

De broncoderepositories voor externe bewaking bevatten de broncode en de Docker-configuratiebestanden die u nodig hebt om de Docker-afbeeldingen van microservices uit te voeren.

Als u een lokale versie van de opslagplaats wilt klonen en maken, gebruikt u uw opdrachtregelomgeving om naar een geschikte map op uw lokale machine te gaan. Voer vervolgens een van de volgende opdrachten uit om de Java-opslagplaats te klonen:

* Voer de volgende opdracht uit om de nieuwste versie van de Java-microservice-implementaties te downloaden:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Voer de volgende opdrachten uit om de nieuwste submodules op te halen:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Deze opdrachten downloaden de broncode voor alle microservices naast de scripts die u gebruikt om de microservices lokaal uit te voeren. U hebt de broncode niet nodig om de microservices in Docker uit te voeren. Maar de broncode is handig als u later van plan bent om de oplossingsversneller aan te passen en uw wijzigingen lokaal te testen.

## <a name="deploy-the-azure-services"></a>De Azure-services implementeren

Hoewel in dit artikel wordt uitgelegd hoe u de microservices lokaal uitvoeren, zijn deze afhankelijk van Azure-services die in de cloud worden uitgevoerd. Gebruik het volgende script om de Azure-services te implementeren. De scriptvoorbeelden gaan ervan uit dat u de Java-opslagplaats op een Windows-machine gebruikt. Als u in een andere omgeving werkt, past u de paden, bestandsextensies en padscheidingen op de juiste manier aan.

### <a name="create-new-azure-resources"></a>Nieuwe Azure-bronnen maken

Als u nog niet de vereiste Azure-bronnen hebt gemaakt, voert u de volgende stappen uit:

1. Ga in uw opdrachtregelomgeving naar de **map \services\scripts\local\launch** in uw gekloonde kopie van de opslagplaats.

1. Voer de volgende opdrachten uit om het **CLI-hulpprogramma voor pc's** te installeren en u aan te melden bij uw Azure-account:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Voer het **start.cmd** script uit. Het script vraagt u om de volgende informatie:

   * Een oplossingsnaam.
   * Het te gebruiken Azure-abonnement.
   * De locatie van het Te gebruiken Azure-datacenter.

   Het script maakt een resourcegroep in Azure met de naam van uw oplossing. Deze brongroep bevat de Azure-resources die de oplossingsversneller gebruikt. U deze brongroep verwijderen nadat u de bijbehorende resources niet meer nodig hebt.

   Het script voegt ook een set omgevingsvariabelen toe aan uw lokale machine. Elke variabele naam heeft het voorvoegsel **PCS**. Deze omgevingsvariabelen bieden details waarmee Externe bewaking de configuratiewaarden kan lezen uit een Azure Key Vault-bron.

   > [!TIP]
   > Wanneer het script is voltooid, worden de omgevingsvariabelen opgeslagen in een bestand dat ** \<\>\\de naam\\\<\>van de thuismap .pcs-oplossing .env wordt**genoemd. U ze gebruiken voor toekomstige implementaties van oplossingen. Houd er rekening mee dat alle omgevingsvariabelen die op uw lokale machine zijn ingesteld, de waarden in het **\\\\\\lokale .env-bestand van services** overschrijven wanneer u **docker-compose uitvoert.**

1. Sluit de opdrachtlijnomgeving.

### <a name="use-existing-azure-resources"></a>Bestaande Azure-bronnen gebruiken

Als u al de vereiste Azure-resources hebt gemaakt, stelt u de bijbehorende omgevingsvariabelen in op uw lokale machine:
* **PCS_KEYVAULT_NAME:** de naam van de Key Vault-bron.
* **PCS_AAD_APPID:** de Azure Active Directory (Azure AD) toepassings-id.
* **PCS_AAD_APPSECRET**: Het geheim van de Azure AD-toepassing.

Configuratiewaarden worden gelezen uit deze Key Vault-bron. Deze omgevingsvariabelen kunnen worden opgeslagen in de ** \<\>\\\\\<\>naam van de .pcs-oplossing .env-bestand** van de implementatie. Houd er rekening mee dat omgevingsvariabelen die zijn ingesteld op uw lokale machine, waarden overschrijven in het **\\\\\\lokale .env-bestand van services** wanneer u **docker-compose uitvoert.**

Een deel van de configuratie die nodig is voor de microservice, wordt opgeslagen in een exemplaar van Key Vault dat is gemaakt bij de eerste implementatie. De bijbehorende variabelen in de sleutelkluis moeten zo nodig worden gewijzigd.

## <a name="run-the-microservices"></a>De microservices uitvoeren

In deze sectie voert u de microservices voor externe bewaking uit. Je rent:

* De web-gebruikersinterface native.
* De Azure IoT Device Simulation, Auth en Azure Stream Analytics Manager-services in Docker.
* De microservices in IntelliJ.

### <a name="run-the-device-simulation-service"></a>De apparaatsimulatieservice uitvoeren

Open een nieuw opdrachtpromptvenster. Controleer of u toegang hebt tot de omgevingsvariabelen die zijn ingesteld door het **begin-cmd-script** in de vorige sectie.

Voer de volgende opdracht uit om de Docker-container voor de apparaatsimulatieservice te openen. De service simuleert apparaten voor de oplossing voor externe bewaking.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>De Auth-service uitvoeren

Open een nieuw venster Opdrachtprompt en voer de volgende opdracht uit om de Docker-container voor de Auth-service te openen. Door deze service te gebruiken, u de gebruikers beheren die gemachtigd zijn om toegang te krijgen tot Azure IoT-oplossingen.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>De Stream Analytics Manager-service uitvoeren

Open een nieuw opdrachtpromptvenster en voer de volgende opdracht uit om de Docker-container voor de Stream Analytics Manager-service te openen. Met deze service u Stream Analytics-taken beheren. Een dergelijk beheer omvat het instellen van taakconfiguratie en het starten, stoppen en bewaken van de taakstatus.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Implementeer alle andere microservices op uw lokale machine

In de volgende stappen ziet u hoe u de microservices voor externe bewaking in IntelliJ uitvoeren.

#### <a name="import-a-project"></a>Een project importeren

1. Open de IntelliJ IDE.
1. Selecteer **Project importeren**.
1. Kies **azure-iot-pcs-remote-monitoring-java\services\build.sbt**.

#### <a name="create-run-configurations"></a>Run-configuraties maken

1. Selecteer**Configuraties bewerken** **uitvoeren** > .
1. Selecteer **Nieuwe configuratie** > **toevoegen sbt taak**.
1. Voer **Naam**in en voer **taken** in als **run**.
1. Selecteer de **werkmap** op basis van de service die u wilt uitvoeren.
1. Selecteer**OK** **toepassen** > om uw keuzes op te slaan.
1. Run-configuraties maken voor de volgende webservices:
    * WebService (services\config)
    * WebService (services\apparaat-telemetrie)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

In de volgende afbeelding ziet u bijvoorbeeld hoe u een configuratie voor een service toevoegt:

[![Schermafbeelding van het venster IntelliJ IDE Run/Debug Configurations, met de optie storageAdapter die is gemarkeerd in de lijst met sbt-taken in het linkerdeelvenster en vermeldingen in de vakken Naam, Taken, Werk en VM-parameters in het rechterdeelvenster.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Een samengestelde configuratie maken

1. Als u alle services samen wilt uitvoeren, selecteert u **Nieuwe configuratiecompound** > **toevoegen**.
1. Voer **Naam**in en selecteer **vervolgens sbt-taken toevoegen**.
1. Selecteer**OK** **toepassen** > om uw keuzes op te slaan.

In de volgende afbeelding ziet u bijvoorbeeld hoe u alle sbt-taken aan één configuratie toevoegt:

[![Schermafbeelding van het venster IntelliJ IDE Run/Debug Configurations, met de optie AllServices gemarkeerd in de lijst Compound in het linkerdeelvenster en de optie sbt Taak 'deviceTelemetry' die in het rechterdeelvenster is gemarkeerd.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Selecteer **Uitvoeren** om de webservices op de lokale machine te bouwen en uit te voeren.

Elke webservice opent een opdrachtpromptvenster en een webbrowservenster. Bij de opdrachtprompt ziet u de uitvoer van de lopende service. In het browservenster u de status controleren. Sluit de vensteren opdrachtprompt of webpagina's niet, omdat met deze acties de webservice wordt gestopt.

Ga naar de volgende URL's om toegang te krijgen tot de status van de services:

* IoT-Hub Manager:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Apparaattelemetrie:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Config:[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* opslagadapter:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>De functie Stream Analytics starten

Volg de volgende stappen om de streamanalytics-taak te starten:

1. Ga naar de [Azure-portal.](https://portal.azure.com)
1. Ga naar de **resourcegroep** die is gemaakt voor uw oplossing. De naam van de resourcegroep is de naam die u voor uw oplossing hebt gekozen toen u het **beginscript** hebt uitgevoerd.
1. Selecteer de **taak Stream Analytics** in de lijst met bronnen.
1. Selecteer op de pagina Het **overzicht van** de taak Stream Analytics de knop **Start** en selecteer **Vervolgens Start** om de taak te starten.

### <a name="run-the-web-ui"></a>De webgebruikersinterface uitvoeren

In deze stap start u de webgebruikersinterface. Open een nieuw opdrachtpromptvenster. Controleer of u toegang hebt tot de omgevingsvariabelen die zijn ingesteld door het **begin-cmd-script.** Ga naar de **webui-map** in uw lokale kopie van de opslagplaats en voer de volgende opdrachten uit:

```cmd
npm install
npm start
```

Wanneer de **opdracht start** is voltooid, wordt [http://localhost:3000/dashboard](http://localhost:3000/dashboard)de pagina op het adres weergegeven wanneer de opdracht start is voltooid. De fouten op deze pagina worden verwacht. Voer de volgende stappen uit om de toepassing zonder fouten te bekijken.

### <a name="configure-and-run-nginx"></a>Nginx configureren en uitvoeren

Stel een reverse proxy-server in die de webtoepassing koppelt aan de microservices die op uw lokale machine worden uitgevoerd:

1. Kopieer het **nginx.conf-bestand** van de **webui\scripts\localhost-map** in uw lokale kopie van de opslagplaats naar de **nginx\conf-installatiemap.**
1. Run Nginx.

Zie [nginx voor Windows voor](https://nginx.org/en/docs/windows.html)meer informatie over het uitvoeren van Nginx.

### <a name="connect-to-the-dashboard"></a>Verbinding maken met het dashboard

Ga naar in uw browser http://localhost:9000 om toegang te krijgen tot het dashboard met externe bewakingsoplossingen.

## <a name="clean-up"></a>Opruimen

Verwijder de cloudservices uit uw Azure-abonnement nadat u klaar bent met het testen. Als u de services wilt verwijderen, gaat u naar de [Azure-portal](https://ms.portal.azure.com)en verwijdert u de brongroep die het **begin-cmd-script** heeft gemaakt.

U ook de lokale kopie verwijderen van de remote monitoring repository die is gemaakt toen u de broncode van GitHub kloonde.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing voor externe bewaking hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het oplossingsdashboard.](quickstart-remote-monitoring-deploy.md)
