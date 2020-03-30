---
title: Remote Monitoring-oplossing lokaal implementeren - Visual Studio Code - Azure | Microsoft Documenten
description: Deze handleiding laat u zien hoe u de versneller van de externe bewakingsoplossing implementeren op uw lokale machine met behulp van Visual Studio Code voor testen en ontwikkelen.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890960"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>De Remote Monitoring-oplossingsversneller lokaal implementeren - Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In dit artikel ziet u hoe u de remote monitoring oplossingsversneller implementeren op uw lokale machine voor testen en ontwikkelen. U leert hoe u de microservices uitvoert in Visual Studio Code. Een lokale implementatie van microservices maakt gebruik van de volgende cloudservices: IoT Hub, Cosmos DB, Azure Streaming Analytics en Azure Time Series Insights.

## <a name="prerequisites"></a>Vereisten

Als u de Azure-services wilt implementeren die worden gebruikt door de versneller van de oplossing voor externe bewaking, hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

### <a name="machine-setup"></a>Machine-installatie

Als u de lokale implementatie wilt voltooien, hebt u de volgende hulpprogramma's nodig die op uw lokale ontwikkelingsmachine zijn geïnstalleerd:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio-code](https://code.visualstudio.com/)
* [VS Code's C#-extensie](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) - deze software is een voorwaarde voor de PCS CLI die de scripts gebruiken om Azure-resources te maken. Gebruik Node.js v10 niet

> [!NOTE]
> Visual Studio Code is beschikbaar voor Windows, Mac en Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>De microservices uitvoeren

In deze sectie voert u de microservices voor externe bewaking uit. U voert de web-gebruikersinterface native uit, de apparaatsimulatieservice in Docker en de microservices in Visual Studio Code.

### <a name="build-the-code"></a>De code bouwen

Navigeer naar azure-iot-pcs-remote-monitoring-dotnet\services in de opdrachtprompt en voer de volgende opdrachten uit om de code te bouwen.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Alle andere microservices implementeren op lokale machines

In de volgende stappen ziet u hoe u de microservices voor externe bewaking uitvoert in Visual Studio Code:

1. Start Visual Studio Code.
1. Open in VS-code de map **azure-iot-pcs-remote-monitoring-dotnet.**
1. Maak een nieuwe map genaamd **.vscode** in de map **azure-iot-pcs-remote-monitoring-dotnet.**
1. Kopieer de bestanden **launch.json** en **tasks.json** van services\scripts\local\launch\idesettings\vscode naar de **.vscode-map** die u zojuist hebt gemaakt.
1. Open het **deelvenster Foutopsporing** in VS-code en voer de configuratie **Alle microservices uitvoeren** uit. Deze configuratie voert de microservice voor apparaatsimulatie uit in Docker en voert de andere microservices in de foutopsporing uit.

De uitvoer van het uitvoeren van **Run All-microsoervices** in de Foutopsporingsconsole ziet er als volgt uit:

[![Services voor lokaal-microservices implementeren](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>De webgebruikersinterface uitvoeren

In deze stap start u de webgebruikersinterface. Navigeer naar de map **azure-iot-pcs-remote-monitoring-dotnet\webui** in uw lokale kopie en voer de volgende opdrachten uit:

```cmd
npm install
npm start
```

Wanneer de start is voltooid, geeft uw browser de pagina **\/http: /localhost:3000/dashboard**weer. De fouten op deze pagina worden verwacht. Voer de volgende stap uit om de toepassing zonder fouten te bekijken.

### <a name="configure-and-run-nginx"></a>NGINX configureren en uitvoeren

Stel een reverse proxy server in om de webtoepassing en microservices die op uw lokale machine worden uitgevoerd te koppelen:

* Kopieer het **nginx.conf-bestand** van de **map webui\scripts\localhost** naar de **map nginx\conf** installeren.
* Run **nginx**.

Voor meer informatie over het uitvoeren van **nginx,** zie [nginx voor Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Verbinding maken met het dashboard

Als u toegang wilt krijgen tot\/het dashboard met de oplossing voor externe bewaking, navigeert u naar http: /localhost:9000 in uw browser.

## <a name="clean-up"></a>Opruimen

Als u onnodige kosten wilt voorkomen, verwijdert u bij het testen de cloudservices uit uw Azure-abonnement. Als u de services wilt verwijderen, navigeert u naar de [Azure-portal](https://ms.portal.azure.com) en verwijdert u de brongroep die het **begin-cmd-script** heeft gemaakt.

U ook de lokale kopie verwijderen van de remote monitoring repository die is gemaakt toen u de broncode van GitHub kloonde.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing voor externe bewaking hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het oplossingsdashboard.](quickstart-remote-monitoring-deploy.md)
