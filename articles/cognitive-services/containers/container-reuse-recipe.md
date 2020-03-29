---
title: Recepten voor Docker containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het bouwen, testen en opslaan van containers met sommige of alle configuratie-instellingen voor implementatie en hergebruik.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 97342f1dd4f6ce343626ba6c294f09dabe3db5c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717204"
---
# <a name="create-containers-for-reuse"></a>Containers maken voor hergebruik

Gebruik deze containerrecepten om Cognitive Services-containers te maken die opnieuw kunnen worden gebruikt. Containers kunnen worden gebouwd met sommige of alle configuratie-instellingen, zodat ze _niet_ nodig zijn wanneer de container wordt gestart.

Zodra u deze nieuwe containerlaag (met instellingen) hebt en u deze lokaal hebt getest, u de container opslaan in een containerregister. Wanneer de container wordt gestart, heeft deze alleen de instellingen nodig die momenteel niet in de container zijn opgeslagen. De privéregistercontainer biedt configuratieruimte om deze instellingen in te geven.

## <a name="docker-run-syntax"></a>Syntaxis van dockerrun

Voorbeelden `docker run` in dit document gaan uit `^` van een Windows-console met een regelvervolgteken. Overweeg het volgende voor eigen gebruik:

* Wijzig de volgorde van de argumenten niet, tenzij u zeer vertrouwd bent met dockercontainers.
* Als u een ander besturingssysteem dan Windows of een andere console dan Windows-console gebruikt, gebruikt u de juiste console/terminal, mapsyntaxis voor bevestigingen en regelvervolgteken voor uw console en systeem.  Omdat de container Cognitive Services een Linux-besturingssysteem is, maakt de doelhouder gebruik van een syntaxis in Linux-stijl.
* `docker run`voorbeelden gebruiken de directory `c:` buiten het station om eventuele machtigingsconflicten op Windows te voorkomen. Als u een specifieke map als invoermap moet gebruiken, moet u mogelijk de dockerservicetoestemming verlenen.

## <a name="store-no-configuration-settings-in-image"></a>Geen configuratie-instellingen opslaan in afbeelding

De `docker run` voorbeeldopdrachten voor elke service slaan geen configuratie-instellingen op in de container. Wanneer u de container start vanuit een console- of registerservice, moeten deze configuratie-instellingen worden doorgegeven. De privéregistercontainer biedt configuratieruimte om deze instellingen in te geven.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Recept opnieuw gebruiken: alle configuratie-instellingen opslaan met container

Als u alle configuratie-instellingen `Dockerfile` wilt opslaan, maakt u een met deze instellingen.

Problemen met deze aanpak:

* De nieuwe container heeft een aparte naam en tag van de originele container.
* Als u deze instellingen wilt wijzigen, moet u de waarden van het Dockerbestand wijzigen, de afbeelding opnieuw opbouwen en opnieuw publiceren in uw register.
* Als iemand toegang krijgt tot uw containerregister of uw lokale host, kan deze de container uitvoeren en de eindpunten van Cognitive Services gebruiken.
* Als uw cognitieve service geen invoerbevestigingen vereist, `COPY` voegt u de regels niet toe aan uw Dockerfile.

Maak Dockerfile, haal uit de bestaande cognitive services-container die u wilt gebruiken en gebruik dockeropdrachten in het Dockerfile om informatie in te stellen of op te halen die de container nodig heeft.

Dit voorbeeld:

* Hiermee stelt u `{BILLING_ENDPOINT}` het factureringseindpunt in `ENV`op basis van de omgevingssleutel van de host met behulp van .
* Hiermee stelt u de `{ENDPOINT_KEY}` facturerings-API-sleutel in vanaf de omgevingssleutel van de host met 'ENV'.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Recept opnieuw gebruiken: factureringsinstellingen opslaan met container

In dit voorbeeld ziet u hoe u de sentimentcontainer van Text Analytics bouwen vanuit een Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Bouw en voer de container [naar behoefte uit](#how-to-use-container-on-your-local-host) vanuit uw [privéregistercontainer.](#how-to-add-container-to-private-registry)

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Recept opnieuw gebruiken: facturering opslaan en instellingen met container monteren

In dit voorbeeld ziet u hoe u Taalbegrip gebruikt en facturering en modellen opslaat in het Dockerfile.

* Kopieert het LUIS-modelbestand (Language Understanding) van `COPY`het bestandssysteem van de host met behulp van .
* De LUIS-container ondersteunt meer dan één model. Als alle modellen in dezelfde map zijn `COPY` opgeslagen, hebt u allemaal één instructie nodig.
* Voer het dockerbestand uit vanaf de relatieve bovenliggende map van het modelinvoermap. Voer in het volgende `docker build` `docker run` voorbeeld de opdrachten en `/input`opdrachten uit van de relatieve bovenliggende van . De `/input` eerste `COPY` op de opdracht is de map van de hostcomputer. De `/input` tweede is de map van de container.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Bouw en voer de container [naar behoefte uit](#how-to-use-container-on-your-local-host) vanuit uw [privéregistercontainer.](#how-to-add-container-to-private-registry)

## <a name="how-to-use-container-on-your-local-host"></a>Container gebruiken op uw lokale host

Als u het Docker-bestand wilt bouwen, vervangt u `<your-image-name>` de nieuwe naam van de afbeelding en gebruikt u het volgende:

```console
docker build -t <your-image-name> .
```

De afbeelding uitvoeren en verwijderen wanneer de`--rm`container stopt ( ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Container toevoegen aan privéregister

Volg deze stappen om het Dockerbestand te gebruiken en plaats de nieuwe afbeelding in uw privécontainerregister.  

1. Maak `Dockerfile` een met de tekst uit recept hergebruik. A `Dockerfile` heeft geen verlenging.

1. Vervang alle waarden in de hoekhaakjes door uw eigen waarden.

1. Bouw het bestand in een afbeelding op de opdrachtregel of terminal met de volgende opdracht. Vervang de waarden in de `<>`hoekhaakjes, door uw eigen containernaam en -tag.  

    De tagoptie `-t`is een manier om informatie toe te voegen over wat u voor de container hebt gewijzigd. Een containernaam van `modified-LUIS` geeft bijvoorbeeld aan dat de oorspronkelijke container is gelaagd. Een tagnaam `with-billing-and-model` van geeft aan hoe de LUIS-container (Language Understanding) is gewijzigd.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Meld u aan bij Azure CLI vanaf een console. Deze opdracht opent een browser en vereist verificatie. Nadat u bent geverifieerd, u de browser sluiten en in de console blijven werken.

    ```azurecli
    az login
    ```

1. Meld u aan bij uw privéregister met Azure CLI vanaf een console.

    Vervang de waarden in de `<my-registry>`hoekhaakjes, met uw eigen registernaam.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    U zich ook aanmelden met docker-aanmelding als u een serviceprincipal toegewezen krijgt.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Tag de container met de locatie privéregister. Vervang de waarden in de `<my-registry>`hoekhaakjes, met uw eigen registernaam. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Als u geen tagnaam gebruikt, `latest` wordt geïmpliceerd.

1. Duw de nieuwe afbeelding naar uw privécontainerregister. Wanneer u uw privécontainerregister bekijkt, is de containernaam die wordt gebruikt in de volgende opdracht CLI de naam van de opslagplaats.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Container Instance maken en gebruiken](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->