---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een Azure-containerinstantiebron vanuit azure cli.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e3542b976921aa45794d62cad9517984c8348ce3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875108"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Een Azure Container Instance-bron maken vanuit de Azure CLI

De YAML hieronder definieert de Azure Container Instance-bron. Kopieer en plak de inhoud in `my-aci.yaml` een nieuw bestand, met de naam en vervang de opmerkingen door die van jou. Raadpleeg de [sjabloonindeling][template-format] voor geldige YAML. Raadpleeg de [containeropslagplaatsen en afbeeldingen][repositories-and-images] voor de beschikbare afbeeldingsnamen en de bijbehorende opslagplaats. Zie [YAML-verwijzing naar YAML-verwijzing naar Meer][aci-yaml-ref]informatie over de YAML-verwijzing naar containerinstanties .

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Niet alle locaties hebben dezelfde CPU en geheugenbeschikbaarheid. Raadpleeg de [tabel locatie en resources][location-to-resource] voor de lijst met beschikbare resources voor containers per locatie en besturingssysteem.

We vertrouwen op het YAML-bestand [`az container create`][azure-container-create] dat we hebben gemaakt voor het commando. Voer vanuit azure cli `az container create` de opdracht `<resource-group>` uit die de opdracht vervangt door de opdracht. Bovendien verwijzen voor het beveiligen van waarden binnen een YAML-implementatie naar [veilige waarden.][secure-values]

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

De uitvoer van `Running...` de opdracht is indien geldig, nadat de uitvoer is gewijzigd in een JSON-tekenreeks die de nieuw gemaakte ACI-bron vertegenwoordigt. De containerafbeelding is waarschijnlijk een tijdje niet beschikbaar, maar de bron is nu geïmplementeerd.

> [!TIP]
> Besteed veel aandacht aan de locaties van azure cognitive service-aanbiedingen voor openbare preview, omdat de YAML dienovereenkomstig moet worden aangepast aan de locatie.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
