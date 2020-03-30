---
title: Extern bewaken-oplossing lokaal implementeren - Docker - Azure | Microsoft Documenten
description: Deze handleiding laat u zien hoe u de versneller van de externe bewakingsoplossing implementeren op uw lokale machine met Docker voor testen en ontwikkelen.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888823"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>De remote monitoring oplossingsversneller lokaal implementeren - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In dit artikel ziet u hoe u de remote monitoring oplossingsversneller implementeren op uw lokale machine voor testen en ontwikkelen. U leert hoe u de microservices implementeren op lokale Docker-containers. Een lokale implementatie van microservices maakt gebruik van de volgende cloudservices: IoT Hub, Cosmos DB, Azure Streaming Analytics en Azure Time Series Insights-services in de cloud.

Als u de remote monitoring oplossingsversneller in een IDE op uw lokale machine wilt uitvoeren, [raadpleegt u De oplossingsversneller voor externe bewaking lokaal implementeren - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Vereisten

Als u de Azure-services wilt implementeren die worden gebruikt door de versneller van de oplossing voor externe bewaking, hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

### <a name="machine-setup"></a>Machine-installatie

Als u de lokale implementatie wilt voltooien, hebt u de volgende hulpprogramma's nodig die op uw lokale ontwikkelingsmachine zijn geïnstalleerd:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - als u van plan bent om wijzigingen aan te brengen in de microservices.
* [Node.js v8](https://nodejs.org/) - deze software is een voorwaarde voor de PCS CLI die de scripts gebruiken om Azure-resources te maken. Gebruik node.js v10 niet.

> [!NOTE]
> Deze tools zijn beschikbaar op veel platforms, waaronder Windows, Linux en iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>De microservices uitvoeren in Docker

Open een nieuwe opdrachtprompt om er zeker van te zijn dat u toegang hebt tot de omgevingsvariabelen die zijn ingesteld door het **script start.cmd.** In Windows u controleren of de omgevingsvariabelen zijn ingesteld door de volgende opdracht uit te voeren:

```cmd
set PCS
```

De opdracht toont alle omgevingsvariabelen die zijn ingesteld door het **begin-cmd-script.**

Zorg ervoor dat Docker op uw lokale machine draait.
> [!NOTE]
> Docker moet [Linux-containers](https://docs.docker.com/docker-for-windows/) draaien als deze op Windows wordt uitgevoerd.

De microservices die worden uitgevoerd in de lokale Docker-containers moeten toegang krijgen tot de Azure-cloudservices. U de internetverbinding van uw Docker-omgeving testen met de volgende opdracht om een internetadres vanuit een container te pingen:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Als u de oplossingsversneller wilt uitvoeren, navigeert u naar de lokale map **servicesscripts\\\\** in uw opdrachtregelomgeving en voert u de volgende opdracht uit:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Zorg ervoor dat u een lokale schijf `docker-compose up` [deelt](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) met Docker voordat u deze uitvoert.

De eerste keer dat u deze opdracht uitvoert, downloadt Docker de microserviceafbeeldingen van docker-hub om de containers lokaal te bouwen. Bij volgende runs runt Docker de containers onmiddellijk.

> [!TIP]
> Microsoft publiceert regelmatig nieuwe Docker-afbeeldingen met nieuwe functionaliteit. U de volgende set opdrachten gebruiken om uw lokale Docker-containers en bijbehorende afbeeldingen op te schonen voordat u de nieuwste afbeeldingen ophaalt:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

U een aparte shell gebruiken om de logboeken van de container te bekijken. Zoek eerst de container-ID met behulp van de `docker ps` opdracht. Vervolgens `docker logs {container-id} --tail 1000` kunt u de laatste 1000 vermeldingen voor de opgegeven container weergeven.

### <a name="start-the-stream-analytics-job"></a>De functie Stream Analytics starten

Volg de volgende stappen om de streamanalytics-taak te starten:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
1. Navigeer naar de **resourcegroep** die is gemaakt voor uw oplossing. De naam van de resourcegroep is de naam die u voor uw oplossing hebt gekozen toen u het **beginscript** hebt uitgevoerd.
1. Klik op de **functie Stream Analytics** in de lijst met bronnen.
1. Klik op de **pagina** Overzicht van de taak Stream Analytics op de knop **Start.** Klik vervolgens op **Start** om de taak nu te starten.

### <a name="connect-to-the-dashboard"></a>Verbinding maken met het dashboard

Als u toegang wilt krijgen `http://localhost:8080` tot het dashboard met de oplossing voor externe bewaking, navigeert u in uw browser. U nu de webgebruikersinterface en de lokale microservices gebruiken.

## <a name="clean-up"></a>Opruimen

Als u onnodige kosten wilt voorkomen, verwijdert u bij het testen de cloudservices uit uw Azure-abonnement. Als u de services wilt verwijderen, navigeert u naar de [Azure-portal](https://ms.portal.azure.com) en verwijdert u de brongroep die het **begin-cmd-script** heeft gemaakt.

Gebruik `docker-compose down --rmi all` de opdracht om de Docker-afbeeldingen te verwijderen en ruimte vrij te maken op uw lokale machine. U ook de lokale kopie verwijderen van de remote monitoring repository die is gemaakt toen u de broncode van GitHub kloonde.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing voor externe bewaking hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het oplossingsdashboard.](quickstart-remote-monitoring-deploy.md)
