---
title: Taaldetectiecontainer uitvoeren in Kubernetes-service
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Implementeer de taaldetectiecontainer, met een lopend voorbeeld, naar de Azure Kubernetes-service en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 1968bc03bfddb9d6f6c8fe743a2a1a99722c074d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399174"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>De taaldetectiecontainer text Analytics implementeren in Azure Kubernetes-service

Meer informatie over het implementeren van de taaldetectiecontainer. Deze procedure laat zien hoe u de lokale Docker-containers maakt, de containers naar uw eigen privécontainerregister pusht, de container in een Kubernetes-cluster uitvoert en deze test in een webbrowser.

## <a name="prerequisites"></a>Vereisten

Deze procedure vereist verschillende tools die lokaal moeten worden geïnstalleerd en uitgevoerd. Gebruik azure cloudshell niet.

* Gebruik een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* [Git](https://git-scm.com/downloads) voor uw besturingssysteem, zodat u het [monster](https://github.com/Azure-Samples/cognitive-services-containers-samples) dat in deze procedure wordt gebruikt, klonen.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Docker-engine](https://www.docker.com/products/docker-engine) en valideren dat de Docker CLI werkt in een consolevenster.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Een Azure-bron met de juiste prijscategorie. Niet alle prijsniveaus werken met deze container:
  * **Text Analytics-bron** met alleen F0- of Standaardprijsniveaus.
  * **Cognitive Services** resource met de Prijscategorie S0.

## <a name="running-the-sample"></a>Het voorbeeld uitvoeren

Deze procedure laadt en voert het voorbeeld van cognitive services container uit voor taaldetectie. Het voorbeeld heeft twee containers, een voor de clienttoepassing en een voor de container Cognitive Services. We duwen beide afbeeldingen naar het Azure Container Registry. Zodra ze in uw eigen register staan, maakt u een Azure Kubernetes-service om toegang te krijgen tot deze afbeeldingen en de containers uit te voeren. Wanneer de containers actief zijn, gebruikt u de **kubectl** CLI om de prestaties van de containers te bekijken. Toegang tot de clienttoepassing met een HTTP-aanvraag en bekijk de resultaten.

![Conceptueel idee van het uitvoeren van monstercontainers](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>De monstercontainers

Het voorbeeld heeft twee containerafbeeldingen, een voor de frontend-website. De tweede afbeelding is de taaldetectiecontainer die de gedetecteerde taal (cultuur) van tekst retourt. Beide containers zijn toegankelijk vanaf een extern IP wanneer u klaar bent.

### <a name="the-language-frontend-container"></a>De taal-frontend container

Deze website is gelijk aan uw eigen client-side applicatie die verzoeken van de taal detectie eindpunt maakt. Wanneer de procedure is voltooid, krijgt u de gedetecteerde taal van een reeks `http://<external-IP>/<text-to-analyze>`tekens door toegang te krijgen tot de websitecontainer in een browser met . Een voorbeeld van `http://132.12.23.255/helloworld!`deze URL is . Het resultaat in `English`de browser is .

### <a name="the-language-container"></a>De taalcontainer

De taaldetectiecontainer is in deze specifieke procedure toegankelijk voor externe aanvragen. De container is op geen enkele manier gewijzigd, dus de standaard Cognitive Services containerspecifieke taaldetectie-API is beschikbaar.

Voor deze container is die API een POST-verzoek voor taaldetectie. Zoals met alle Cognitive Services containers, u meer te weten `http://<external-IP>:5000/swagger/index.html`komen over de container van de gehoste Swagger informatie,.

Poort 5000 is de standaardpoort die wordt gebruikt bij de containers Cognitive Services.

## <a name="create-azure-container-registry-service"></a>Azure Container Registry-service maken

Als u de container wilt implementeren in de Azure Kubernetes-service, moeten de containerafbeeldingen toegankelijk zijn. Maak uw eigen Azure Container Registry-service om de afbeeldingen te hosten.

1. Aanmelden bij de Azure CLI

    ```azurecli-interactive
    az login
    ```

1. Maak een resourcegroep met de naam die is gemaakt `cogserv-container-rg` om elke resource die in deze procedure is gemaakt, vast te houden.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Maak vervolgens `registry`uw eigen Azure Container Registry met `pattyregistry`de indeling van uw naam, zoals . Gebruik geen streepjes of onderstrepingstekens in de naam.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Sla de resultaten op om de eigenschap **loginServer** te krijgen. Dit maakt deel uit van het adres van `language.yml` de gehoste container, dat later in het bestand wordt gebruikt.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Meld u aan bij uw containerregister. Je moet inloggen voordat je afbeeldingen naar je register pushen.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Website Docker-afbeelding bekijken

1. De voorbeeldcode die in deze procedure wordt gebruikt, bevindt zich in de opslagplaats voor containers voor cognitive services-containers. Kloon de repository om een lokale kopie van het voorbeeld te hebben.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Zodra de opslagplaats zich op uw lokale computer bevindt, zoekt u de website in de [map \dotnet\Language\FrontendService.](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) Deze website fungeert als de clienttoepassing die de taaldetectie-API aanroept die wordt gehost in de taaldetectiecontainer.  

1. Bouw de Docker afbeelding voor deze website. Controleer of de console zich in de [\FrontendService-map](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) bevindt waar het Dockerbestand zich bevindt wanneer u de volgende opdracht uitvoert:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Als u de versie in uw containerregister wilt bijhouden, voegt u de tag toe met een versie-indeling, zoals `v1`.

1. Duw de afbeelding naar uw containerregister. Dit kan enkele minuten duren.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Als u `unauthorized: authentication required` een fout krijgt, logt u in met de `az acr login --name <your-container-registry-name>` opdracht. 

    Wanneer het proces is afgerond, moeten de resultaten vergelijkbaar zijn met:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Afbeelding van de Docker-taaldetectie

1. Trek de nieuwste versie van de Docker-afbeelding naar de lokale machine. Dit kan enkele minuten duren. Als er een nieuwere versie van deze container `1.1.006770001-amd64-preview` is, wijzigt u de waarde van de nieuwere versie.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Tag afbeelding met uw containerregister. Zoek de nieuwste versie `1.1.006770001-amd64-preview` en vervang de versie als u een recentere versie hebt. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Duw de afbeelding naar uw containerregister. Dit kan enkele minuten duren.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Referenties containerregister ophalen

De volgende stappen zijn nodig om de vereiste informatie te krijgen om uw containerregister te verbinden met de Azure Kubernetes-service die u later in deze procedure maakt.

1. Serviceprincipal maken.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Sla de `appId` resultaatwaarde voor de parameter assignee op in stap 3. `<appId>` Sla `password` de parameter voor de volgende `<client-secret>`sectie op voor de client-secret parameter .

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Haal uw containerregister-ID op.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Sla de uitvoer op voor `<acrId>`de waarde van de bereikparameter in de volgende stap. Het lijkt erop dat:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Sla de volledige waarde op voor stap 3 in deze sectie.

1. Als u de juiste toegang wilt verlenen voor het AKS-cluster om afbeeldingen te gebruiken die zijn opgeslagen in uw containerregister, maakt u een roltoewijzing. Vervang `<appId>` `<acrId>` en met de waarden die in de vorige twee stappen zijn verzameld.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Azure Kubernetes-service maken

1. Maak de Kubernetes-cluster. Alle parameterwaarden zijn afkomstig van vorige secties, behalve de parameter naam. Kies een naam die aangeeft wie de `patty-kube`naam heeft gemaakt en het doel ervan, zoals .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Deze stap kan enkele minuten duren. Het resultaat is:

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    De service is gemaakt, maar het heeft nog niet de website container of taal detectie container.  

1. Haal referenties van het Kubernetes-cluster op.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>De orchestration-definitie in uw Kubernetes-service laden

In deze sectie wordt de **kubectl** CLI gebruikt om te praten met de Azure Kubernetes-service.

1. Controleer voordat u de orchestration-definitie laadt, of **Kubectl** toegang heeft tot de knooppunten.

    ```console
    kubectl get nodes
    ```

    Het antwoord ziet eruit als volgt:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Kopieer het volgende bestand `language.yml`en geef een naam. Het bestand `service` heeft een `deployment` sectie en een sectie `language-frontend` voor de `language` twee containertypen, de websitecontainer en de detectiecontainer.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Wijzig de implementatieregels voor `language.yml` taalfrontend op basis van de volgende tabel om uw eigen instellingen voor containerregisterafbeelding, clientgeheim en tekstanalyse-instellingen toe te voegen.

    Instellingen voor taalfrontend-implementatie|Doel|
    |--|--|
    |Lijn 32<br> `image`Eigenschap|Afbeeldingslocatie voor de frontendafbeelding in uw containerregister<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Lijn 44<br> `name`Eigenschap|Containerregister geheim voor de afbeelding, aangeduid als `<client-secret>` in een vorige sectie.|

1. Wijzig de taalimplementatieregels van `language.yml` de volgende tabel om uw eigen instellingen voor containerregisterafbeelding, clientgeheim en tekstanalyse-instellingen toe te voegen.

    |Instellingen voor taalimplementatie|Doel|
    |--|--|
    |Lijn 78<br> `image`Eigenschap|Afbeeldingslocatie voor de taalafbeelding in uw containerregister<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Lijn 95<br> `name`Eigenschap|Containerregister geheim voor de afbeelding, aangeduid als `<client-secret>` in een vorige sectie.|
    |Lijn 91<br> `apiKey`Eigenschap|Uw bronsleutel voor tekstanalyse|
    |Lijn 92<br> `billing`Eigenschap|Het factureringseindpunt voor uw bron voor tekstanalyse.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Omdat het **apiKey-** en **factureringseindpunt** is ingesteld als onderdeel van de Kubernetes-orchestration-definitie, hoeft de websitecontainer deze niet te weten of door te geven als onderdeel van de aanvraag. De websitecontainer verwijst naar de taaldetectiecontainer `language`met de orchestratornaam .

1. Laad het orchestration definition-bestand voor dit voorbeeld uit `language.yml`de map waar u de .

    ```console
    kubectl apply -f language.yml
    ```

    Het antwoord is:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Externe IP's van containers krijgen

Controleer voor de twee `language-frontend` `language` containers of de services worden uitgevoerd en krijg het externe IP-adres.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Als `EXTERNAL-IP` de voor de service wordt weergegeven als in behandeling, voert u de opdracht opnieuw uit totdat het IP-adres wordt weergegeven voordat u naar de volgende stap gaat.

## <a name="test-the-language-detection-container"></a>De taaldetectiecontainer testen

Open een browser en navigeer naar `language` het externe IP `http://<external-ip>:5000/swagger/index.html`van de container uit de vorige sectie: . U `Try it` de functie van de API gebruiken om het eindpunt voor taaldetectie te testen.

![Bekijk de swaggerdocumentatie van de container](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>De clienttoepassingscontainer testen

Wijzig de URL in de browser `language-frontend` in het externe `http://<external-ip>/helloworld`IP-adres van de container met de volgende indeling: . De Engelse cultuurtekst `helloworld` van `English`wordt voorspeld zoals .

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het cluster, verwijdert u de Azure-brongroep.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Gerelateerde informatie

* [kubectl voor Docker-gebruikers](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Containers voor cognitieve services](../cognitive-services-container-support.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->