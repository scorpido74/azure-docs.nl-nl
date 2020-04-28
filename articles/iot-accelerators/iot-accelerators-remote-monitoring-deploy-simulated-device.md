---
title: Aangepaste gesimuleerde apparaten met IoT implementeren-Azure | Microsoft Docs
description: In deze hand leiding vindt u informatie over het implementeren van aangepaste gesimuleerde apparaten naar de oplossings versneller voor externe controle.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66427565"
---
# <a name="deploy-a-new-simulated-device"></a>Een nieuw gesimuleerd apparaat implementeren

Met de ontwikkel oplossingen voor externe controle en simulatie van een apparaat kunt u uw eigen gesimuleerde apparaten definiëren. In dit artikel wordt beschreven hoe u een aangepast Chiller-apparaattype en een nieuw gloeilamp-apparaattype implementeert naar de oplossings versneller voor externe controle.

Voor de stappen in dit artikel wordt ervan uitgegaan dat u de hand leiding [een nieuw gesimuleerd apparaat maken en testen](iot-accelerators-remote-monitoring-create-simulated-device.md) hebt uitgevoerd en de bestanden hebt die de aangepaste Chiller en nieuwe gloeilamp-apparaattypen definiëren.

Met de stappen in deze hand leiding leert u het volgende:

1. Gebruik SSH om toegang te krijgen tot het bestands systeem van de virtuele machine die als host fungeert voor de oplossings versneller voor externe controle.

1. Configureer docker om de modellen van een locatie buiten de docker-container te laden.

1. Voer de oplossings versneller voor externe controle uit met behulp van aangepaste model bestanden voor apparaten.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u de stappen in deze hand leiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volgen, hebt u het volgende nodig:

- Een geïmplementeerd exemplaar van de [oplossings versneller voor externe controle](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Een lokale **bash** -shell om de `ssh` opdrachten `scp` en uit te voeren. Een eenvoudige manier om **bash** te installeren in Windows is door [Git](https://git-scm.com/download/win)te installeren.
- Uw aangepaste model bestanden voor apparaten, zoals de modellen die zijn beschreven in [een nieuw gesimuleerd apparaat maken en testen](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Docker configureren

In deze sectie configureert u docker om de model bestanden van het apparaat te laden vanuit de map **/tmp/devicemodels** op de virtuele machine, in plaats van vanuit de docker-container. Voer de opdrachten in deze sectie uit in een **bash** -shell op uw lokale computer:

In deze sectie configureert u docker om de model bestanden van het apparaat te laden vanuit de map **/tmp/devicemodels** op de virtuele machine, in plaats van vanuit de docker-container. Voer de opdrachten in deze sectie uit in een **bash** -shell op uw lokale computer:

1. Gebruik SSH om verbinding te maken met de virtuele machine in azure vanaf uw lokale computer. Bij de volgende opdracht wordt ervan uitgegaan dat het open bare IP-adres van de virtuele machine **VM-vikxv** is **104.41.128.108** --Vervang deze waarde door het open bare IP-adres van uw virtuele machine uit de vorige sectie:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Volg de aanwijzingen om u aan te melden bij de virtuele machine met het wacht woord dat u in de vorige sectie hebt ingesteld.

1. Configureer de Device simulatie service om de apparaat modellen van buiten de container te laden. Open eerst het docker-configuratie bestand:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Zoek de instellingen voor de **devicesimulation** -container en bewerk de **volume** -instelling, zoals wordt weer gegeven in het volgende code fragment:

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

1. Kopieer de bestaande model bestanden van het apparaat van de container naar de nieuwe locatie. Zoek eerst de container-ID voor de apparaat simulatie container:

    ```sh
    sudo docker ps
    ```

    Kopieer vervolgens de model bestanden van het apparaat naar de map **tmp** in de virtuele machine. De volgende opdracht wordt ervan uitgegaan dat de container-ID c378d6878407 is--Vervang deze waarde door de ID van de simulatie container van uw apparaat:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Houd het venster **bash** met uw SSH-sessie geopend.

1. Kopieer uw aangepaste apparaat-model bestanden naar de virtuele machine. Voer deze opdracht uit in een andere **bash** -shell op de computer waarop u uw aangepaste modellen voor apparaten hebt gemaakt. Ga eerst naar de lokale map met de JSON-bestanden van uw device model. Bij de volgende opdrachten wordt ervan uitgegaan dat het open bare IP-adres van de virtuele machine **104.41.128.108** is: Vervang deze waarde door het open bare IP-adres van uw virtuele machine. Voer het wacht woord voor uw virtuele machine in wanneer u hierom wordt gevraagd:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Start de Device simulatie docker-container opnieuw op om de nieuwe modellen te gebruiken. Voer de volgende opdrachten uit in de **bash** -shell met de open SSH-sessie naar de virtuele machine:

    ```sh
    sudo /app/start.sh
    ```

    Als u de status van de actieve docker-containers en de bijbehorende container-Id's wilt zien, gebruikt u de volgende opdracht:

    ```sh
    sudo docker ps
    ```

    Als u het logboek van de apparaat simulatie container wilt weer geven, voert u de volgende opdracht uit. Vervang de container-ID door de ID van de simulatie container van uw apparaat:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Simulatie uitvoeren

U kunt nu uw aangepaste apparaatprofielen gebruiken in de oplossing voor externe controle:

1. Start uw dash board voor externe controle van [Microsoft Azure IOT-oplossings Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Gebruik de pagina **apparaten** om gesimuleerde apparaten toe te voegen. Wanneer u een nieuw gesimuleerd apparaat toevoegt, zijn uw nieuwe apparaten beschikbaar om te kiezen.

1. U kunt het dash board gebruiken om de telemetrie van apparaten te bekijken en de methoden van het apparaat aan te roepen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan, kunt u de oplossing voor externe controle van oplossingen implementeren.

Als u de oplossings versneller niet meer nodig hebt, verwijdert u deze van de pagina [ingerichte oplossingen](https://www.azureiotsolutions.com/Accelerators#dashboard) door deze te selecteren en vervolgens op **oplossing verwijderen**te klikken.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding wordt uitgelegd hoe u aangepaste apparaten kunt implementeren in de oplossing voor externe controle. De voorgestelde volgende stap is om te leren hoe u [een echt apparaat aansluit op uw oplossing voor externe controle](iot-accelerators-connecting-devices-node.md).
