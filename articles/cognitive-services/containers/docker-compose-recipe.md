---
title: Docker Compose gebruiken om meerdere containers te implementeren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van meerdere Cognitive Services-containers. In dit artikel ziet u hoe u meerdere Docker-containerafbeeldingen orkestreren met Docker Compose.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 458cda927a6a123fcd9962efc6ab705e13f43286
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878779"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Docker Compose gebruiken om meerdere containers te implementeren

In dit artikel ziet u hoe u meerdere Azure Cognitive Services-containers implementeert. In het bijzonder leert u hoe u Docker Compose gebruiken om meerdere Docker-containerafbeeldingen te orkestreren.

> [Docker Compose](https://docs.docker.com/compose/) is een hulpmiddel voor het definiëren en uitvoeren van dockertoepassingen met meerdere containers. In Compose gebruikt u een YAML-bestand om de services van uw toepassing te configureren. Vervolgens maakt en start u alle services vanuit uw configuratie door één opdracht uit te voeren.

Het kan handig zijn om meerdere containerafbeeldingen op één hostcomputer te orkestreren. In dit artikel brengen we de containers Lees en Formulierherkenning samen.

## <a name="prerequisites"></a>Vereisten

Deze procedure vereist verschillende hulpprogramma's die lokaal moeten worden geïnstalleerd en uitgevoerd:

* Een Azure-abonnement. Als je nog geen account hebt, maak je een [gratis account](https://azure.microsoft.com/free/) aan voordat je begint.
* [Docker Engine](https://www.docker.com/products/docker-engine). Controleer of de Docker CLI werkt in een consolevenster.
* Een Azure-bron met de juiste prijscategorie. Alleen de volgende prijsniveaus werken met deze container:
  * **Computer Vision-bron** met alleen f0- of standaardprijslaag.
  * **Form Recognizer** resource met alleen F0- of Standard-prijscategorie.
  * **Cognitive Services** resource met de Prijscategorie S0.

## <a name="request-access-to-the-container-registry"></a>Toegang tot het containerregister aanvragen

Vul het [formulier Voor het aanvragen van cognitive services speechcontainers in](https://aka.ms/speechcontainerspreview/)en dien deze in. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker Compose-bestand

Het YAML-bestand definieert alle services die moeten worden geïmplementeerd. Deze services zijn `DockerFile` afhankelijk van een of een bestaande containerafbeelding. In dit geval gebruiken we twee voorbeeldafbeeldingen. Kopieer en plak het volgende YAML-bestand en sla het op als *docker-compose.yaml*. Geef de juiste **apikey-,** **facturerings-** en **EndpointUri-waarden** in het bestand.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Maak de mappen op de hostmachine die zijn opgegeven onder het **volumesknooppunt.** Deze aanpak is vereist omdat de mappen moeten bestaan voordat u probeert om een afbeelding te monteren met behulp van volumebindingen.

## <a name="start-the-configured-docker-compose-services"></a>De geconfigureerde Docker Compose-services starten

Een Docker Compose-bestand maakt het beheer van alle fasen in de levenscyclus van een gedefinieerde service mogelijk: services starten, stoppen en opnieuw opbouwen; het weergeven van de servicestatus; en log streaming. Open een opdrachtregelinterface vanuit de projectmap (waar het docker-compose.yaml-bestand zich bevindt).

> [!NOTE]
> Om fouten te voorkomen, moet u ervoor zorgen dat de hostmachine schijven correct deelt met Docker Engine. Als *E:\publicpreview* bijvoorbeeld wordt gebruikt als een map in het *docker-compose.yaml-bestand,* deelt u station **E** met Docker.

Voer vanuit de opdrachtregelinterface de volgende opdracht uit om alle services die zijn gedefinieerd in het *docker-compose.yaml-bestand* te starten (of opnieuw op te starten):

```console
docker-compose up
```

De eerste keer dat Docker de opdracht **docker-compose** uitvoert met behulp van deze configuratie, trekt hij de afbeeldingen die onder het **knooppunt van** de services zijn geconfigureerd en downloadt en monteert ze:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Nadat de afbeeldingen zijn gedownload, worden de beeldservices gestart:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>De beschikbaarheid van de service controleren

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Hier is een voorbeeld uitgang:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>Testcontainers

Open een browser op de hostmachine en ga naar **localhost** met behulp van de opgegeven http://localhost:5021/swagger/index.htmlpoort vanuit het *docker-compose.yaml-bestand,* zoals . U bijvoorbeeld de functie **Try It** in de API gebruiken om het eindpunt Van formulierherkenning te testen. Beide containers swagger pagina's moeten beschikbaar en toetsbaar zijn.

![Formulierherkenningscontainer](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Cognitive Services containers](../cognitive-services-container-support.md)
