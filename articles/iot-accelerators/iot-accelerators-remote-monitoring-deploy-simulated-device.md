---
title: IoT implementeert aangepaste gesimuleerde apparaten - Azure | Microsoft Documenten
description: Deze handleiding laat u zien hoe u aangepaste gesimuleerde apparaten implementeert in de remote monitoring oplossingsversneller.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66427565"
---
# <a name="deploy-a-new-simulated-device"></a>Een nieuw gesimuleerd apparaat implementeren

Met de oplossingen voor remote monitoring en apparaatsimulatie u beide uw eigen gesimuleerde apparaten definiëren. In dit artikel ziet u hoe u een aangepast type koelapparaat en een nieuw type gloeilampapparaat implementeert op de accelerator voor oplossingen voor externe bewaking.

De stappen in dit artikel gaan ervan uit dat u de handleiding [van het nieuwe gesimuleerde apparaat maken en testen,](iot-accelerators-remote-monitoring-create-simulated-device.md) de bestanden hebben die de aangepaste koeler en nieuwe typen gloeilampen definiëren.

De stappen in deze handleiding laten u zien hoe u:

1. Gebruik SSH om toegang te krijgen tot het bestandssysteem van de virtuele machine die de remote monitoring oplossingsversneller host.

1. Configureer Docker om de apparaatmodellen te laden vanaf een locatie buiten de Docker-container.

1. Voer de remote monitoring oplossingsversneller uit met aangepaste apparaatmodelbestanden.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de stappen in deze handleiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze handleiding te volgen, moet u het volgende volgen:

- Een geïmplementeerdexemplaar van de [remote monitoring oplossingsversneller](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Een lokale **bash** shell `ssh` `scp` om de en commando's uit te voeren. Op Windows, een eenvoudige manier om **bash** te installeren is het installeren [van git](https://git-scm.com/download/win).
- Uw aangepaste apparaatmodelbestanden, zoals die beschreven in [Een nieuw gesimuleerd apparaat maken en testen.](iot-accelerators-remote-monitoring-create-simulated-device.md)

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Docker configureren

In deze sectie configureert u Docker om de apparaatmodelbestanden te laden vanuit de map **/tmp/devicemodels** in de virtuele machine in plaats van vanuit de Docker-container. Voer de opdrachten in deze sectie uit in een **bashshell** op uw lokale machine:

In deze sectie configureert u Docker om de apparaatmodelbestanden te laden vanuit de map **/tmp/devicemodels** in de virtuele machine in plaats van vanuit de Docker-container. Voer de opdrachten in deze sectie uit in een **bashshell** op uw lokale machine:

1. Gebruik SSH om verbinding te maken met de virtuele machine in Azure vanaf uw lokale machine. De volgende opdracht gaat ervan uit dat het openbare IP-adres van virtuele machine **vm-vikxv** **104.41.128.108** is - vervang deze waarde door het openbare IP-adres van uw virtuele machine uit de vorige sectie:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Volg de aanwijzingen om u aan te melden bij de virtuele machine met het wachtwoord dat u in de vorige sectie hebt ingesteld.

1. Configureer de apparaatsimulatieservice om de apparaatmodellen van buiten de container te laden. Open eerst het docker-configuratiebestand:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Zoek de instellingen voor de **apparaatsimulatiecontainer** en bewerk de **volume-instelling** zoals weergegeven in het volgende fragment:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Sla de wijzigingen op.

1. Kopieer de bestaande apparaatmodelbestanden van de container naar de nieuwe locatie. Zoek eerst de container-ID voor de apparaatsimulatiecontainer:

    ```sh
    sudo docker ps
    ```

    Kopieer vervolgens de apparaatmodelbestanden naar de **tmp-map** in de virtuele machine. Met de volgende opdracht wordt ervan uitgegaan dat de container-ID c378d6878407 is - vervang deze waarde door de container-ID van uw apparaatsimulatie:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Houd het **bash-venster** open met uw SSH-sessie.

1. Kopieer uw aangepaste apparaatmodelbestanden naar de virtuele machine. Voer deze opdracht uit in een andere **bashshell** op de machine waar u uw aangepaste apparaatmodellen hebt gemaakt. Navigeer eerst naar de lokale map met JSON-bestanden van uw apparaatmodel. De volgende opdrachten gaan ervan uit dat het openbare IP-adres van de virtuele machine **104.41.128.108** is - vervang deze waarde door het openbare IP-adres van uw virtuele machine. Voer het wachtwoord van uw virtuele machine in wanneer u daarom wordt gevraagd:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Start de Docker-container voor apparaatsimulatie opnieuw om de nieuwe apparaatmodellen te gebruiken. Voer de volgende opdrachten in de **bashshell** uit met de open SSH-sessie naar de virtuele machine:

    ```sh
    sudo /app/start.sh
    ```

    Als u de status van de met dockercontainers en hun container------iD's wilt zien, gebruikt u de volgende opdracht:

    ```sh
    sudo docker ps
    ```

    Als u het logboek vanuit de apparaatsimulatiecontainer wilt zien, voert u de volgende opdracht uit. Vervang de container-ID door de ID van uw apparaatsimulatiecontainer:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Simulatie uitvoeren

U nu uw aangepaste apparaatmodellen gebruiken in de oplossing voor externe bewaking:

1. Start uw dashboard voor externe bewaking vanaf [Microsoft Azure IoT Solution Accelerators.](https://www.azureiotsolutions.com/Accelerators#dashboard)

1. Gebruik de pagina **Apparaten** om gesimuleerde apparaten toe te voegen. Wanneer u een nieuw gesimuleerd apparaat toevoegt, zijn uw nieuwe apparaatmodellen beschikbaar om te kiezen.

1. U het dashboard gebruiken om telemetrie en gespreksapparaatmethoden te bekijken.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te verkennen, laat u de remote monitoring oplossingsversneller ingezet.

Als u de oplossingsversneller niet meer nodig hebt, verwijdert u deze van de pagina [Ingerichte oplossingen](https://www.azureiotsolutions.com/Accelerators#dashboard) door deze te selecteren en vervolgens op **Oplossing verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Deze handleiding toonde u hoe u aangepaste apparaatmodellen implementeert in de Remote Monitoring-oplossingsversneller. De voorgestelde volgende stap is om te leren hoe [u een echt apparaat aansluiten op uw oplossing voor bewaking op afstand.](iot-accelerators-connecting-devices-node.md)
