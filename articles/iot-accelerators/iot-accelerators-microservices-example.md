---
title: Een microservice wijzigen en opnieuw implementeren - Azure | Microsoft Documenten
description: In deze zelfstudie ziet u hoe u een microservice wijzigen en opnieuw implementeren in Externe bewaking
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447621"
---
# <a name="customize-and-redeploy-a-microservice"></a>Een microservice aanpassen en opnieuw implementeren

In deze zelfstudie ziet u hoe u een van de [microservices](https://azure.com/microservices) in de oplossing voor externe bewaking bewerkt, een afbeelding van uw microservice maken, de afbeelding implementeren in uw dockerhub en deze vervolgens gebruiken in de oplossing voor externe bewaking. Om dit concept te introduceren, gebruikt de zelfstudie een basisscenario waarbij u een microservice-API aanroept en het statusbericht wijzigt van "Levend en wel" in "Nieuwe bewerkingen hier gemaakt!"

Remote Monitoring-oplossing maakt gebruik van microservices die zijn gebouwd met behulp van docker-afbeeldingen die uit een dockerhub worden getrokken. 

In deze zelfstudie leert u het volgende:

>[!div class="checklist"]
> * Een microservice bewerken en bouwen in de oplossing voor externe bewaking
> * Een dockerafbeelding maken
> * Een dockerafbeelding naar uw dockerhub duwen
> * Trek de nieuwe dockerafbeelding
> * Visualiseer de wijzigingen 

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te volgen, moet je het volgende volgen:

>[!div class="checklist"]
> * [De remote monitoring oplossingsversneller lokaal implementeren](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Een Docker-account](https://hub.docker.com/)
> * [Postbode](https://www.getpostman.com/) - Nodig om de API-respons te bekijken

## <a name="call-the-api-and-view-response-status"></a>De API aanroepen en de status van het antwoord weergeven

In dit deel noemt u de standaard IoT hub manager microservice API. De API retourneert een statusbericht dat u later wijzigt door de microservice aan te werken.

1. Zorg ervoor dat de oplossing voor externe bewaking lokaal op uw machine wordt uitgevoerd.
2. Zoek waar je Postman hebt gedownload en open hem.
3. Voer in Postman het volgende `http://localhost:8080/iothubmanager/v1/status`in de GET: .
4. Bekijk de terugkeer en je moet zien, "Status": "OK: Levend en wel".

    ![Alive and Well Postman Bericht](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>De status wijzigen en de afbeelding maken

Wijzig nu het statusbericht van de microservice iot Hub Manager in 'Nieuwe bewerkingen hier gemaakt!' en vervolgens de docker afbeelding opnieuw met deze nieuwe status. Als u hier problemen ondervindt, raadpleegt u de sectie [Probleemoplossing.](#Troubleshoot)

1. Zorg ervoor dat uw terminal open is en wijzig de map waar u de oplossing voor externe bewaking hebt gekloond. 
1. Wijzig uw directory in "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Open StatusService.cs in een teksteditor of IDE die u leuk vindt. 
1. Zoek de volgende code:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    en verander het in de onderstaande code en sla deze op.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Ga terug naar uw terminal, maar verander nu naar de volgende directory: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Als u uw nieuwe dockerafbeelding wilt maken, typt u

    ```sh
    sh build
    ```
    
    of op Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Als u wilt controleren of uw nieuwe afbeelding is gemaakt, typt u

    ```cmd/sh
    docker images 
    ```

De repository moet "azureiotpcs/iothub-manager-dotnet" zijn.

![Succesvolle dockerafbeelding](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>De installatiekopie taggen en pushen
Voordat u uw nieuwe dockerafbeelding naar een dockerhub pushen, verwacht Docker dat uw afbeeldingen worden getagd. Als u hier problemen ondervindt, raadpleegt u de sectie [Probleemoplossing.](#Troubleshoot)

1. Zoek de image-id van de dockerafbeelding die u hebt gemaakt door te typen:

    ```cmd/sh
    docker images
    ```

2. Uw afbeelding taggen met het type 'testen'

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Als u uw nieuw gelabelde afbeelding naar uw dockerhub wilt pushen, typt u

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Open uw internetbrowser en ga naar uw [dockerhub](https://hub.docker.com/) en meld u aan.
5. U ziet nu uw nieuwe dockerafbeelding op uw dockerhub.
![Dockerafbeelding in dockerhub](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Uw oplossing voor externe bewaking bijwerken
U moet nu uw lokale docker-compose.yml bijwerken om uw nieuwe dockerafbeelding van uw dockerhub te halen. Als u hier problemen ondervindt, raadpleegt u de sectie [Probleemoplossing.](#Troubleshoot)

1. Ga terug naar de terminal en verander naar de volgende directory: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Open docker-compose.yml in elke teksteditor of IDE die je leuk vindt.
3. Zoek de volgende code:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    en verander het om te lijken op de afbeelding hieronder en op te slaan.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>De nieuwe reactiestatus weergeven
Werk af door een lokaal exemplaar van de oplossing voor externe bewaking opnieuw in te zetten en de nieuwe statusrespons in Postman te bekijken.

1. Ga terug naar uw terminal en verander naar de volgende directory: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Start uw lokale instantie van de oplossing Voor externe bewaking door de volgende opdracht in de terminal te typen:

    ```cmd/sh
    docker-compose up
    ```

3. Zoek waar je Postman hebt gedownload en open hem.
4. Voer in Postman het volgende verzoek `http://localhost:8080/iothubmanager/v1/status`in de GET in: . U moet nu zien, "Status": "OK: Nieuwe bewerkingen hier gemaakt!".

![Nieuwe bewerkingen Made Here postbode bericht](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Oplossen

Als u problemen ondervindt, probeert u de afbeeldingen en containers van de docker op de lokale machine te verwijderen.

1. Als u alle containers wilt verwijderen, moet u eerst alle lopende containers stoppen. Open uw terminal en typ

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Als u alle afbeeldingen wilt verwijderen, opent u uw terminal en typt u 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. U controleren of er containers op de machine staan door

    ```cmd/sh
    docker ps -aq 
    ```

    Als u alle containers met succes hebt verwijderd, mag er niets verschijnen.

4. U controleren of er afbeeldingen op de machine staan door

    ```cmd/sh
    docker images
    ```

    Als u alle containers met succes hebt verwijderd, mag er niets verschijnen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zag je hoe je:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Een microservice bewerken en bouwen in de oplossing voor externe bewaking
> * Een dockerafbeelding maken
> * Een dockerafbeelding naar uw dockerhub duwen
> * Trek de nieuwe dockerafbeelding
> * Visualiseer de wijzigingen 

Het volgende ding om te proberen is [het aanpassen van het apparaat simulator microservice in de Remote Monitoring oplossing](iot-accelerators-microservices-example.md)

Zie voor meer informatie van ontwikkelaars over de oplossing voor externe monitoring:

* [Snelzoekgids voor ontwikkelaars](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

