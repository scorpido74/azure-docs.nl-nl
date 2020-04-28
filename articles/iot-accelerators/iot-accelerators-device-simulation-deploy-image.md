---
title: Een aangepaste afbeelding voor het simuleren van apparaten implementeren-Azure | Microsoft Docs
description: In deze hand leiding vindt u informatie over het implementeren van een aangepaste docker-installatie kopie van de Device simulatie-oplossing naar Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "61448393"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Een aangepaste docker-installatie kopie voor Device simulatie implementeren

U kunt de oplossing voor het simuleren van apparaten wijzigen om aangepaste functies toe te voegen. Het artikel voor het [serialiseren van de telemetrie met protocol buffers](iot-accelerators-device-simulation-protobuf.md) laat bijvoorbeeld zien hoe u een aangepast apparaat toevoegt aan de oplossing die gebruikmaakt van protocol buffers (protobuf) voor het verzenden van telemetrie. Nadat u uw wijzigingen lokaal hebt getest, is de volgende stap het implementeren van uw wijzigingen in het simulatie-exemplaar van uw apparaat in Azure. Als u deze taak wilt volt ooien, moet u een docker-installatie kopie maken en implementeren met de gewijzigde service.

Met de stappen in deze hand leiding leert u het volgende:

1. Een ontwikkel omgeving voorbereiden
1. Een nieuwe docker-installatie kopie genereren
1. Device simulatie configureren voor het gebruik van uw nieuwe docker-installatie kopie
1. Een simulatie uitvoeren met de nieuwe installatie kopie

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, hebt u het volgende nodig:

* Een geimplementeerd simulatie exemplaar van het [apparaat](quickstart-device-simulation-deploy.md) .
* Docker. Down load de [docker-Community Edition](https://www.docker.com/products/docker-engine#/download) voor uw platform.
* Een [docker hub-account](https://hub.docker.com/) waar u uw docker-installatie kopieën kunt uploaden. Maak in uw docker hub-account een open bare opslag plaats met de naam **apparaat-simulatie**.
* Een gewijzigde en geteste [simulatie oplossing voor apparaten](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) op uw lokale computer. U kunt de oplossing bijvoorbeeld wijzigen om [telemetrie te serialiseren met protocol buffers](iot-accelerators-device-simulation-protobuf.md).
* Een shell die SSH kan uitvoeren. Als u git voor Windows installeert, kunt u de **bash** -shell gebruiken die deel uitmaakt van de installatie. U kunt ook uw [Azure Cloud shell](https://shell.azure.com/)gebruiken.

In de instructies in dit artikel wordt ervan uitgegaan dat u Windows gebruikt. Als u een ander besturings systeem gebruikt, moet u mogelijk enkele bestands paden en opdrachten aanpassen aan uw omgeving.

## <a name="create-a-new-docker-image"></a>Een nieuwe docker-installatie kopie maken

Als u uw eigen wijzigingen wilt implementeren in de Device simulatie service, moet u de build-en implementatie scripts in de map **scripts\docker** bewerken om de containers te uploaden naar uw docker-hub-account

### <a name="modify-the-docker-scripts"></a>De docker-scripts aanpassen

Wijzig de docker **Build. cmd**-, **Publish. cmd**-en **Run. cmd** -scripts in de map **Scripts\docker** met de opslagplaats gegevens van uw docker-hub. Bij deze stappen wordt ervan uitgegaan dat u een open bare opslag plaats hebt gemaakt met de naam **apparaat-simulatie**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Werk het bestand **docker-Compose. yml** als volgt bij:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>De oplossing configureren voor het toevoegen van nieuwe bestanden

Als u nieuwe model bestanden voor apparaten hebt toegevoegd, moet u deze expliciet toevoegen aan de oplossing. Voeg een vermelding toe aan de **Services/services. csproj** voor elk extra bestand dat moet worden toegevoegd. Als u bijvoorbeeld de [telemetrie serialisatie hebt voltooid met protocol buffers](iot-accelerators-device-simulation-protobuf.md) instructies, voegt u de volgende vermeldingen toe:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Nieuwe docker-installatie kopieën genereren en pushen naar docker hub

Publiceer de nieuwe docker-installatie kopie naar docker hub met behulp van uw docker-hub-account:

1. Open een opdracht prompt en navigeer naar uw lokale kopie van de opslag plaats voor apparaat simulatie.

1. Navigeer naar de map **docker** :

    ```cmd
    cd scripts\docker
    ```

1. Voer de volgende opdracht uit om de docker-installatie kopie te maken:

    ```cmd
    build.cmd
    ```

1. Voer de volgende opdracht uit om de docker-installatie kopie naar uw docker hub-opslag plaats te publiceren. Meld u aan bij docker met de referenties van uw docker hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>De service bijwerken

Voer de volgende stappen uit om de simulatie container van het apparaat bij te werken voor gebruik van uw aangepaste installatie kopie:

* Gebruik SSH om verbinding te maken met de virtuele machine die als host fungeert voor uw apparaat simulatie exemplaar. Gebruik het IP-adres en het wacht woord dat u in de vorige sectie hebt genoteerd:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Ga naar de map **/app** :

    ```sh
    cd /app
    ```

* Bewerk het bestand **docker-Compose. yml** :

    ```sh
    sudo nano docker-compose.yml
    ```

    Wijzig de **installatie kopie** zo dat de aangepaste **apparaat-simulatie** afbeelding die u hebt geüpload naar uw docker hub-opslag plaats wijst:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Sla uw wijzigingen op.

* Voer de volgende opdracht uit om de micro services opnieuw te starten:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Uw simulatie uitvoeren

U kunt nu een simulatie uitvoeren met uw aangepaste oplossing voor simulatie van apparaten:

1. Start uw web-gebruikers interface voor Device simulatie van [Microsoft Azure IOT-oplossings Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Gebruik de Web-UI om een simulatie te configureren en uit te voeren. Als u de [telemetrie hebt geserialiseerd met protocol buffers](iot-accelerators-device-simulation-protobuf.md), kunt u het aangepaste model voor apparaten gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een aangepaste installatie kopie voor een simulatie van een apparaat implementeert, wilt u mogelijk [een bestaande IOT-hub gebruiken met de apparaat simulatie Solution Accelerator](iot-accelerators-device-simulation-choose-hub.md).