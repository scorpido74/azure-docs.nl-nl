---
title: Een aangepaste apparaatsimulatieafbeelding implementeren - Azure| Microsoft Documenten
description: In deze handleiding leert u hoe u een aangepaste Docker-afbeelding van de oplossing apparaatsimulatie implementeert in Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61448393"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Een aangepaste dockerafbeelding voor apparaatsimulatie implementeren

U de oplossing apparaatsimulatie wijzigen om aangepaste functies toe te voegen. In het artikel [Telemetrie Serialiseren met protocolbuffers](iot-accelerators-device-simulation-protobuf.md) ziet u bijvoorbeeld hoe u een aangepast apparaat toevoegt aan de oplossing die Protocolbuffers (Protobuf) gebruikt om telemetrie te verzenden. Nadat u uw wijzigingen lokaal hebt getest, is de volgende stap het implementeren van uw wijzigingen in uw apparaatsimulatie-instantie in Azure. Als u deze taak wilt voltooien, moet u een Docker-afbeelding maken en implementeren die uw gewijzigde service bevat.

De stappen in deze handleiding laten u zien hoe u:

1. Een ontwikkelomgeving voorbereiden
1. Een nieuwe Docker-afbeelding genereren
1. Apparaatsimulatie configureren om uw nieuwe Docker-afbeelding te gebruiken
1. Een simulatie uitvoeren met de nieuwe afbeelding

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u het volgende nodig:

* Een [geïmplementeerdapparaatsimulatie-exemplaar.](quickstart-device-simulation-deploy.md)
* Docker. Download de [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) voor je platform.
* Een [Docker Hub-account](https://hub.docker.com/) waar u uw Docker-afbeeldingen uploaden. Maak in uw Docker Hub-account een openbare opslagplaats genaamd **apparaatsimulatie.**
* Een aangepaste en geteste [device simulation oplossing](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) op uw lokale machine. U de oplossing bijvoorbeeld wijzigen om [telemetrie te serialiseren met behulp van Protocolbuffers.](iot-accelerators-device-simulation-protobuf.md)
* Een schelp die SSH kan draaien. Als u Git Voor Windows installeert, u de **bash-shell** gebruiken die deel uitmaakt van de installatie. U uw [Azure Cloud Shell](https://shell.azure.com/)ook gebruiken.

De instructies in dit artikel gaan ervan uit dat u Windows gebruikt. Als u een ander besturingssysteem gebruikt, moet u mogelijk een aantal bestandspaden en opdrachten aanpassen aan uw omgeving.

## <a name="create-a-new-docker-image"></a>Een nieuwe Docker-afbeelding maken

Als u uw eigen wijzigingen wilt implementeren in de apparaatsimulatieservice, moet u de build- en implementatiescripts bewerken in de map **scripts\docker** om de containers te uploaden naar uw docker-hub-account

### <a name="modify-the-docker-scripts"></a>De dockerscripts wijzigen

Wijzig de Docker **build.cmd,** **publish.cmd**en **run.cmd** scripts in de map **scripts\docker** met uw Docker Hub-archiefinformatie. Deze stappen gaan ervan uit dat u een openbare opslagplaats hebt gemaakt, **apparaatsimulatie**genaamd:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Werk het **docker-compose.yml-bestand** als volgt bij:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>De oplossing configureren om nieuwe bestanden op te nemen

Als u nieuwe apparaatmodelbestanden hebt toegevoegd, moet u deze expliciet in de oplossing opnemen. Voeg een vermelding toe aan de **services/services.csproj** voor elk extra bestand dat moet worden opgenomen. Als u bijvoorbeeld de [telemetrie van Serialize hebt voltooid met behulp van protocolbuffers](iot-accelerators-device-simulation-protobuf.md) how-to, voegt u de volgende vermeldingen toe:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Nieuwe Docker-afbeeldingen genereren en naar Docker Hub pushen

Publiceer de nieuwe Docker-afbeelding op Docker Hub met uw dockerhub-account:

1. Open een opdrachtprompt en navigeer naar uw lokale kopie van de apparaatsimulatieopslagplaats.

1. Navigeer naar de **map docker:**

    ```cmd
    cd scripts\docker
    ```

1. Voer de volgende opdracht uit om de Docker-afbeelding te bouwen:

    ```cmd
    build.cmd
    ```

1. Voer de volgende opdracht uit om de Docker-afbeelding naar uw Docker Hub-opslagplaats te publiceren. Meld u aan bij Docker met uw Docker Hub-referenties:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>De service bijwerken

Voer de volgende stappen uit om de apparaatsimulatiecontainer bij te werken om uw aangepaste afbeelding te gebruiken:

* Gebruik SSH om verbinding te maken met de virtuele machine die uw apparaatsimulatie-instantie host. Gebruik het IP-adres en wachtwoord waar u in de vorige sectie rekening mee hebt gemaakt:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navigeer naar de **/app-map:**

    ```sh
    cd /app
    ```

* Bewerk het **docker-compose.yml-bestand:**

    ```sh
    sudo nano docker-compose.yml
    ```

    Wijzig de **afbeelding** om de aangepaste apparaatsimulatieafbeelding die u hebt geüpload naar uw Docker **Hub-opslagplaats** te plaatsen:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Sla uw wijzigingen op.

* Voer de volgende opdracht uit om de microservices opnieuw te starten:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Voer uw simulatie uit

U nu een simulatie uitvoeren met uw aangepaste apparaatsimulatieoplossing:

1. Start de webgebruikersinterface voor apparaatsimulatie vanaf [Microsoft Azure IoT Solution Accelerators.](https://www.azureiotsolutions.com/Accelerators#dashboard)

1. Gebruik de web-gebruikersinterface om een simulatie te configureren en uit te voeren. Als u telemetrie eerder hebt voltooid [met behulp van Protocolbuffers,](iot-accelerators-device-simulation-protobuf.md)u uw aangepaste apparaatmodel gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een aangepaste apparaatsimulatieafbeelding implementeert, u leren hoe u [een bestaande IoT-hub gebruiken met de apparaatsimulatieoplossingsversneller.](iot-accelerators-device-simulation-choose-hub.md)