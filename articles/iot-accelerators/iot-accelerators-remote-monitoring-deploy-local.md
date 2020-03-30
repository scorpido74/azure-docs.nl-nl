---
title: Externe bewakingsoplossing lokaal implementeren - VS IDE - Azure | Microsoft Documenten
description: Deze handleiding laat u zien hoe u de versneller van de externe bewakingsoplossing implementeren op uw lokale machine met behulp van Visual Studio voor testen en ontwikkelen.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890890"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>De Remote Monitoring-oplossingsversneller lokaal implementeren - Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In dit artikel ziet u hoe u de remote monitoring oplossingsversneller implementeren op uw lokale machine voor testen en ontwikkelen. Je leert hoe je de microservices uitvoert in Visual Studio. Een lokale implementatie van microservices maakt gebruik van de volgende cloudservices: IoT Hub, Cosmos DB, Azure Streaming Analytics en Azure Time Series Insights-services in de cloud.

Als u de remote monitoring-oplossingsversneller in Docker op uw lokale machine wilt uitvoeren, [raadpleegt u De versneller van de oplossing voor externe bewaking lokaal implementeren - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Vereisten

Als u de Azure-services wilt implementeren die worden gebruikt door de versneller van de oplossing voor externe bewaking, hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

### <a name="machine-setup"></a>Machine-installatie

Als u de lokale implementatie wilt voltooien, hebt u de volgende hulpprogramma's nodig die op uw lokale ontwikkelingsmachine zijn geïnstalleerd:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - deze software is een voorwaarde voor de PCS CLI die de scripts gebruiken om Azure-resources te maken. Gebruik node.js v10 niet.

> [!NOTE]
> Visual Studio is beschikbaar voor Windows en Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>De microservices uitvoeren

In deze sectie voert u de microservices voor externe bewaking uit. U voert de web-gebruikersinterface native uit, de apparaatsimulatieservice in Docker en de microservices in Visual Studio.

### <a name="run-the-device-simulation-service"></a>De apparaatsimulatieservice uitvoeren

Open een nieuw opdrachtpromptvenster om er zeker van te zijn dat u toegang hebt tot de omgevingsvariabelen die zijn ingesteld door het **begin-cmd-script** in de vorige sectie.

Voer de volgende opdracht uit om de Docker-container voor de apparaatsimulatieservice te starten. De service simuleert apparaten voor de oplossing voor bewaking op afstand.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Alle andere microservices implementeren op lokale machines

In de volgende stappen ziet u hoe u de microservices voor externe bewaking uitvoert in Visual Studio:

1. Start Visual Studio.
1. Open de **remote-monitoring.sln-oplossing** in de **mappen met services** in uw lokale kopie van de opslagplaats.
1. Klik in **Solution Explorer**met de rechtermuisknop op de oplossing en klik op **Eigenschappen**.
1. Selecteer **Algemene eigenschappen > opstartproject**.
1. Selecteer **Meerdere opstartprojecten** en stel **Actie** in **op Start** voor de volgende projecten:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (apparaattelemetrie\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (opslagadapter\WebService)
1. Klik op **OK** om uw keuzes op te slaan.
1. Klik **op Foutopsporing > Foutopsporing starten** om de webservices op de lokale machine te bouwen en uit te voeren.

Elke webservice opent een opdrachtprompt en een webbrowservenster. Bij de opdrachtprompt ziet u de uitvoer vanuit de lopende service en in het browservenster u de status controleren. Sluit de opdrachtprompts of webpagina's niet, deze actie stopt de webservice.

### <a name="start-the-stream-analytics-job"></a>De functie Stream Analytics starten

Volg de volgende stappen om de streamanalytics-taak te starten:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
1. Navigeer naar de **resourcegroep** die is gemaakt voor uw oplossing. De naam van de resourcegroep is de naam die u voor uw oplossing hebt gekozen toen u het **beginscript** hebt uitgevoerd.
1. Klik **op** de taak Analytics streamen in de lijst met bronnen.
1. Klik op de **pagina** Overzicht van de taak Stream Analytics op de knop **Start.** Klik vervolgens op **Start** om de taak nu te starten.

### <a name="run-the-web-ui"></a>De webgebruikersinterface uitvoeren

In deze stap start u de webgebruikersinterface. Open een nieuw opdrachtpromptvenster om er zeker van te zijn dat u toegang hebt tot de omgevingsvariabelen die zijn ingesteld door het **begin-cmd-script.** Navigeer naar de **map webui** in uw lokale kopie van de opslagplaats en voer de volgende opdrachten uit:

```cmd
npm install
npm start
```

Wanneer de start is voltooid, geeft uw browser de pagina **\/http: /localhost:3000/dashboard**weer. De fouten op deze pagina worden verwacht. Voer de volgende stap uit om de toepassing zonder fouten te bekijken.

### <a name="configure-and-run-nginx"></a>NGINX configureren en uitvoeren

Stel een reverse proxy server in om de webtoepassing en microservices die op uw lokale machine worden uitgevoerd te koppelen:

* Kopieer het **nginx.conf-bestand** van de **map webui\scripts\localhost** in uw lokale kopie van de opslagplaats naar de **map nginx\conf** installeren.
* Run **nginx**.

Voor meer informatie over het uitvoeren van **nginx,** zie [nginx voor Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Verbinding maken met het dashboard

Als u toegang wilt krijgen tot\/het dashboard met de oplossing voor externe bewaking, navigeert u naar http: /localhost:9000 in uw browser.

## <a name="clean-up"></a>Opruimen

Als u onnodige kosten wilt voorkomen, verwijdert u bij het testen de cloudservices uit uw Azure-abonnement. Als u de services wilt verwijderen, navigeert u naar de [Azure-portal](https://ms.portal.azure.com) en verwijdert u de brongroep die het **begin-cmd-script** heeft gemaakt.

U ook de lokale kopie verwijderen van de remote monitoring repository die is gemaakt toen u de broncode van GitHub kloonde.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing voor externe bewaking hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het oplossingsdashboard.](quickstart-remote-monitoring-deploy.md)
