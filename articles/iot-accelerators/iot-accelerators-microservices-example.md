---
title: Een micro service wijzigen en opnieuw implementeren-Azure | Microsoft Docs
description: Deze zelf studie laat zien hoe u een micro service kunt wijzigen en opnieuw kunt implementeren in externe bewaking
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ff3e12721628e244f247e174af101e71ea91191
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998319"
---
# <a name="customize-and-redeploy-a-microservice"></a>Een microservice aanpassen en opnieuw implementeren

In deze zelf studie leert u hoe u een van de micro [Services](https://azure.com/microservices) in de oplossing voor externe controle kunt bewerken, een installatie kopie van uw micro service kunt bouwen, de installatie kopie naar uw docker-hub kunt implementeren en deze vervolgens kunt gebruiken in de oplossing voor externe controle. Ter introductie van dit concept maakt de zelf studie gebruik van een basis scenario waarin u een micro Service-API aanroept en het status bericht wijzigt van ' Alive ' en ' goed ' op nieuwe bewerkingen die hier worden aangebracht.

De oplossing voor externe controle maakt gebruik van micro services die zijn gebouwd met behulp van docker-installatie kopieën die worden opgehaald uit een docker-hub. 

In deze zelfstudie leert u het volgende:

>[!div class="checklist"]
> * Bewerk en bouw een micro service in de oplossing voor bewaking op afstand
> * Een docker-installatie kopie bouwen
> * Een docker-installatie kopie naar uw docker hub pushen
> * De nieuwe docker-installatie kopie ophalen
> * De wijzigingen visualiseren 

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volgen, hebt u het volgende nodig:

>[!div class="checklist"]
> * [De oplossings versneller voor externe controle lokaal implementeren](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Een docker-account](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) -vereist om het API-antwoord weer te geven

## <a name="call-the-api-and-view-response-status"></a>De API aanroepen en de antwoord status weer geven

In dit gedeelte roept u de standaard IoT hub Manager micro Service API aan. De API retourneert een status bericht dat u later wijzigt door de micro service aan te passen.

1. Zorg ervoor dat de externe bewakings oplossing lokaal op uw computer wordt uitgevoerd.
2. Ga naar de locatie waar u postman hebt gedownload en open deze.
3. Voer in postman het volgende in het bericht GET: `http://localhost:8080/iothubmanager/v1/status` .
4. Bekijk het resultaat en u ziet dat "status": "OK: Alive en goed" wordt weer gegeven.

    ![Alive en goed postman-bericht](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>De status wijzigen en de installatie kopie bouwen

Wijzig nu het status bericht van de micro service IOT hub manager in ' nieuwe bewerkingen die hier worden gemaakt '. en bouw de docker-installatie kopie vervolgens opnieuw op met deze nieuwe status. Als u hier problemen ondervindt, raadpleegt u de sectie [probleem oplossing](#Troubleshoot) .

1. Zorg ervoor dat de Terminal is geopend en ga naar de map waarin u de oplossing voor controle op afstand hebt gekloond. 
1. Wijzig uw directory in ' Azure-IOT-PCs-Remote-Monitoring-DotNet/Services/iothub-Manager/Services '.
1. Open StatusService.cs in een wille keurige tekst editor of IDE. 
1. Zoek de volgende code:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    en wijzig deze in de onderstaande code en sla deze op.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Ga terug naar de Terminal, maar Wijzig nu in de volgende map: ' Azure-IOT-PCs-Remote-Monitoring-DotNet/Services/iothub-Manager/scripts/docker '.
6. Als u uw nieuwe docker-installatie kopie wilt maken, typt u

    ```sh
    sh build
    ```
    
    of in Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Als u wilt controleren of de nieuwe installatie kopie is gemaakt, typt u

    ```cmd/sh
    docker images 
    ```

De opslag plaats moet ' azureiotpcs/iothub-Manager-DotNet ' zijn.

![Voltooide docker-installatie kopie](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>De installatiekopie taggen en pushen
Voordat u uw nieuwe docker-installatie kopie naar een docker-hub kunt pushen, verwacht docker dat uw installatie kopieën worden gelabeld. Als u hier problemen ondervindt, raadpleegt u de sectie [probleem oplossing](#Troubleshoot) .

1. Zoek de afbeeldings-ID van de docker-installatie kopie die u hebt gemaakt door het volgende te typen:

    ```cmd/sh
    docker images
    ```

2. Uw afbeelding labelen met het type testen

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Als u de zojuist gelabelde installatie kopie naar uw docker-hub wilt pushen, typt u

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Open uw Internet browser en ga naar uw [docker-hub](https://hub.docker.com/) en meld u aan.
5. U ziet nu de zojuist gepushte docker-installatie kopie op uw docker-hub.
![Docker-installatie kopie in docker hub](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Uw oplossing voor externe controle bijwerken
U moet nu uw lokale docker-Compose. yml bijwerken om uw nieuwe docker-installatie kopie van uw docker-hub te halen. Als u hier problemen ondervindt, raadpleegt u de sectie [probleem oplossing](#Troubleshoot) .

1. Ga terug naar de Terminal en wijzig de volgende map: ' Azure-IOT-pc's-Remote-Monitoring-DotNet/Services/scripts/local '.
2. Open docker-Compose. yml in een wille keurige tekst editor of IDE.
3. Zoek de volgende code:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    en wijzig deze zodat deze eruitziet als de onderstaande afbeelding en sla het op.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>De nieuwe reactie status weer geven
Voltooi de installatie van een lokaal exemplaar van de oplossing voor externe controle en Bekijk het nieuwe status antwoord in postman.

1. Ga terug naar de Terminal en wijzig de volgende map: ' Azure-IOT-PCs-extern-bewaking-DotNet/scripts/local '.
2. Start uw lokale exemplaar van de oplossing voor externe controle door de volgende opdracht in te voeren in de terminal:

    ```cmd/sh
    docker-compose up
    ```

3. Ga naar de locatie waar u postman hebt gedownload en open deze.
4. Voer in postman de volgende aanvraag in het bericht GET: `http://localhost:8080/iothubmanager/v1/status` . U ziet nu ' status ': ' OK: nieuwe wijzigingen die hier worden aangebracht! '.

![Nieuwe wijzigingen die hier worden aangebracht, plaatst u bericht](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Problemen oplossen

Als u problemen ondervindt, kunt u de docker-installatie kopieën en-containers verwijderen op de lokale computer.

1. Als u alle containers wilt verwijderen, moet u eerst alle actieve containers stoppen. Open de Terminal en typ

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Als u alle installatie kopieën wilt verwijderen, opent u de Terminal en typt u 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. U kunt controleren of er containers aanwezig zijn op de computer door het volgende te typen

    ```cmd/sh
    docker ps -aq 
    ```

    Als u alle containers hebt verwijderd, moet er niets worden weer gegeven.

4. U kunt controleren of er installatie kopieën op de computer zijn door het volgende te typen

    ```cmd/sh
    docker images
    ```

    Als u alle containers hebt verwijderd, moet er niets worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u gezien hoe u het volgende kunt doen:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Bewerk en bouw een micro service in de oplossing voor bewaking op afstand
> * Een docker-installatie kopie bouwen
> * Een docker-installatie kopie naar uw docker hub pushen
> * De nieuwe docker-installatie kopie ophalen
> * De wijzigingen visualiseren 

Het volgende voor het [aanpassen van de micro service Device Simulator in de oplossing voor controle op afstand](iot-accelerators-microservices-example.md)

Voor meer informatie over ontwikkel aars over de oplossing voor controle op afstand raadpleegt u:

* [Snelzoekgids voor ontwikkelaars](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

