---
title: Taaldetectie-container uitvoeren in Kubernetes-service
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Implementeer de taal detectie container, met een actief voor beeld, naar de Azure Kubernetes-service en test deze in een webbrowser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: cdd1cf255c943c8dc6d55a5b749b30357bdcd373
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876722"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>De Text Analytics taal detectie container implementeren naar de Azure Kubernetes-service

Meer informatie over het implementeren van de taal detectie container. In deze procedure ziet u hoe u de lokale docker-containers maakt, de containers naar uw eigen persoonlijke container register pusht, de container in een Kubernetes-cluster uitvoert en deze test in een webbrowser.

## <a name="prerequisites"></a>Vereisten

Voor deze procedure zijn verschillende hulpprogram ma's vereist die moeten worden geïnstalleerd en lokaal worden uitgevoerd. Gebruik Azure Cloud shell niet.

* Een Azure-abonnement gebruiken. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Git](https://git-scm.com/downloads) voor uw besturings systeem, zodat u het voor [beeld](https://github.com/Azure-Samples/cognitive-services-containers-samples) in deze procedure kunt klonen.
* [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Docker-engine](https://www.docker.com/products/docker-engine) en controleer of de docker-cli in een console venster werkt.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Een Azure-resource met de juiste prijs categorie. Niet alle prijs categorieën werken met deze container:
  * **Text Analytics** resource alleen met F0 of Standard-prijs categorieën.
  * **Cognitive Services** resource met de prijs categorie s0.

## <a name="running-the-sample"></a>Het voorbeeld uitvoeren

Met deze procedure wordt het Cognitive Services container voorbeeld voor taal detectie geladen en uitgevoerd. Het voor beeld heeft twee containers, een voor de client toepassing en één voor de Cognitive Services container. Beide installatie kopieën worden naar de Azure Container Registry gepusht. Als ze zich in uw eigen REGI ster bevinden, maakt u een Azure Kubernetes-service voor toegang tot deze installatie kopieën en voert u de containers uit. Wanneer de containers worden uitgevoerd, gebruikt u de **kubectl** CLI om de prestaties van de containers te bekijken. Open de client toepassing met een HTTP-aanvraag en Bekijk de resultaten.

![Conceptueel idee van het uitvoeren van voorbeeld containers](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>De voorbeeld containers

Het voor beeld heeft twee container installatie kopieën, één voor de frontend-website. De tweede afbeelding is de container voor taal detectie die de gedetecteerde taal (cultuur) van tekst retourneert. Beide containers zijn toegankelijk vanuit een extern IP-adres wanneer u klaar bent.

### <a name="the-language-frontend-container"></a>De taal-frontend-container

Deze website is gelijk aan uw eigen toepassing aan de client zijde die aanvragen van het taal detectie-eind punt maakt. Wanneer de procedure is voltooid, krijgt u de gedetecteerde taal van een teken reeks door toegang te krijgen tot de website container in `http://<external-IP>/<text-to-analyze>`een browser met. Een voor beeld van deze URL `http://132.12.23.255/helloworld!`is. Het resultaat in de browser is `English`.

### <a name="the-language-container"></a>De taal container

De taal detectie container, in deze specifieke procedure, is toegankelijk voor alle externe aanvragen. De container is op geen enkele manier gewijzigd, dus de standaard Cognitive Services-providerspecifieke taal detectie-API is beschikbaar.

Voor deze container is deze API een POST-aanvraag voor taal detectie. Net als bij alle Cognitive Services containers kunt u meer informatie over de container vinden op basis van de gehoste Swagger- `http://<external-IP>:5000/swagger/index.html`gegevens.

Poort 5000 is de standaard poort die wordt gebruikt met de Cognitive Services containers.

## <a name="create-azure-container-registry-service"></a>Azure Container Registry-service maken

Als u de container wilt implementeren in de Azure Kubernetes-service, moeten de container installatie kopieën toegankelijk zijn. Maak uw eigen Azure Container Registry-service om de installatie kopieën te hosten.

1. Aanmelden bij de Azure CLI

    ```azurecli-interactive
    az login
    ```

1. Maak een resource groep met `cogserv-container-rg` de naam voor elke resource die in deze procedure wordt gemaakt.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Maak uw eigen Azure Container Registry met de indeling van uw naam `registry`, bijvoorbeeld. `pattyregistry` Gebruik geen streepjes of onderstrepings tekens in de naam.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Sla de resultaten op om de eigenschap **login server** op te halen. Dit maakt deel uit van het adres van de gehoste container, die `language.yml` later in het bestand wordt gebruikt.

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

1. Meld u aan bij uw container register. U moet zich aanmelden voordat u installatie kopieën naar uw REGI ster kunt pushen.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Site docker-installatie kopie ophalen

1. De voorbeeld code die in deze procedure wordt gebruikt, bevindt zich in de opslag plaats Cognitive Services containers voor beelden. Kloon de opslag plaats met een lokale kopie van het voor beeld.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Zodra de opslag plaats op de lokale computer is, gaat u naar de website in de [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) -map. Deze website fungeert als de client toepassing die de API voor taal detectie aanroept die wordt gehost in de taal detectie container.  

1. Bouw de docker-installatie kopie voor deze website. Controleer of de-console zich in de [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) -map bevindt met de Dockerfile wanneer u de volgende opdracht uitvoert:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Als u de versie in het container register wilt bijhouden, voegt u het label toe met een versie `v1`-indeling, zoals.

1. Push de installatie kopie naar het container register. Dit kan enkele minuten duren.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Als er een `unauthorized: authentication required` fout optreedt, meldt u `az acr login --name <your-container-registry-name>` zich aan met de opdracht. 

    Wanneer het proces is voltooid, moeten de resultaten er ongeveer als volgt uitzien:

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

## <a name="get-language-detection-docker-image"></a>Docker-installatie kopie voor taal detectie ophalen

1. Haal de nieuwste versie van de docker-installatie kopie op de lokale computer op. Dit kan enkele minuten duren. Als er een nieuwere versie van deze container is, wijzigt u de waarde `1.1.006770001-amd64-preview` van in de nieuwere versie.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Codeer een afbeelding met het container register. Zoek de nieuwste versie en vervang de versie `1.1.006770001-amd64-preview` als u een recentere versie hebt. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Push de installatie kopie naar het container register. Dit kan enkele minuten duren.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Container Registry referenties ophalen

De volgende stappen zijn nodig om de vereiste informatie te krijgen om uw container register te verbinden met de Azure Kubernetes-service die u later in deze procedure maakt.

1. Service-Principal maken.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Sla de resultaten `appId` waarde op voor de para meter assigned in stap `<appId>`3,. Sla de `password` para meter `<client-secret>`op voor de client-Secret van de volgende sectie.

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Haal de register-ID van uw container op.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Sla de uitvoer voor de waarde `<acrId>`van de bereik parameter op in de volgende stap. Het ziet er als volgt uit:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Sla de volledige waarde voor stap 3 in deze sectie op.

1. Als u de juiste toegang wilt verlenen voor het AKS-cluster om afbeeldingen te gebruiken die zijn opgeslagen in het container register, maakt u een roltoewijzing. Vervang `<appId>` en `<acrId>` door de waarden die in de vorige twee stappen zijn verzameld.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Azure Kubernetes-service maken

1. Maak de Kubernetes-cluster. Alle parameter waarden zijn afkomstig uit vorige secties, met uitzonde ring van de para meter name. Kies een naam die aangeeft wie het heeft gemaakt en het doel ervan, `patty-kube`zoals.

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

    De service is gemaakt, maar heeft nog geen website container of taal detectie container.  

1. Referenties ophalen van het Kubernetes-cluster.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Laad de indelings definitie in uw Kubernetes-service

In deze sectie wordt de **kubectl** cli gebruikt om te communiceren met de Azure Kubernetes-service.

1. Controleer voordat u de indelings definitie laadt **kubectl** toegang heeft tot de knoop punten.

    ```console
    kubectl get nodes
    ```

    Het antwoord ziet er als volgt uit:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Kopieer het volgende bestand en geef het `language.yml`de naam. Het bestand bevat een `service` sectie en een `deployment` sectie voor de twee container typen, de `language-frontend` website container en de `language` detectie container.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Wijzig de implementatie regels voor taal-frontend `language.yml` van op basis van de volgende tabel om uw eigen container register installatie kopie namen, client geheim en tekst analyse-instellingen toe te voegen.

    Implementatie-instellingen voor taal-frontend|Doel|
    |--|--|
    |Regel 32<br> `image`eigenschap|Afbeeldings locatie voor de front-end-installatie kopie in uw Container Registry<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Regel 44<br> `name`eigenschap|Container Registry geheim voor de installatie kopie, waarnaar wordt `<client-secret>` verwezen in een vorige sectie.|

1. Wijzig de taal implementatie regels op `language.yml` basis van de volgende tabel om uw eigen container register installatie kopie namen, client geheim en instellingen voor tekst analyse toe te voegen.

    |Instellingen voor taal implementatie|Doel|
    |--|--|
    |Regel 78<br> `image`eigenschap|Afbeeldings locatie voor de taal installatie kopie in uw Container Registry<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Regel 95<br> `name`eigenschap|Container Registry geheim voor de installatie kopie, waarnaar wordt `<client-secret>` verwezen in een vorige sectie.|
    |Regel 91<br> `apiKey`eigenschap|De bron sleutel voor tekst analyse|
    |Regel 92<br> `billing`eigenschap|Het facturerings eindpunt voor uw tekst analyse resource.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Omdat het **apiKey** -en **facturerings eindpunt** zijn ingesteld als onderdeel van de Kubernetes-indelings definitie, hoeft de website container deze niet te kennen of door te geven als onderdeel van de aanvraag. De website container verwijst naar de taal detectie container met de naam `language`van de Orchestrator.

1. Laad het bestand met de indelings definitie voor dit voor beeld vanuit de map waarin u de `language.yml`hebt gemaakt en opgeslagen.

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

## <a name="get-external-ips-of-containers"></a>Externe IP-adressen van containers ophalen

Controleer voor de twee containers of de `language-frontend` `language` Services actief zijn en ontvang het externe IP-adres.

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

Als de `EXTERNAL-IP` voor de service wordt weer gegeven als in behandeling, voert u de opdracht opnieuw uit totdat het IP-adres wordt weer gegeven voordat u verdergaat met de volgende stap.

## <a name="test-the-language-detection-container"></a>De taal detectie container testen

Open een browser en navigeer naar het externe IP-adres `language` van de container uit de vorige `http://<external-ip>:5000/swagger/index.html`sectie:. U kunt de `Try it` functie van de API gebruiken om het taal detectie-eind punt te testen.

![De Swagger-documentatie van de container weer geven](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>De client toepassings container testen

Wijzig de URL in de browser naar het externe IP-adres `language-frontend` van de container met de volgende `http://<external-ip>/helloworld`indeling:. De Engelse cultuur tekst van `helloworld` wordt voor speld als `English`.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het cluster, verwijdert u de Azure-resource groep.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Gerelateerde informatie

* [kubectl voor docker-gebruikers](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Cognitive Services containers](../cognitive-services-container-support.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->