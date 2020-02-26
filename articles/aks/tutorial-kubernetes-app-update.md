---
title: 'Zelfstudie voor Kubernetes in Azure: een toepassing bijwerken'
description: In deze zelfstudie over Azure Kubernetes Service (AKS) leert u hoe u de implementatie van een bestaande toepassing bijwerkt naar AKS met een nieuwe versie van de toepassingscode.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: d5457d790cd3c95bb23ec0c517097b443a2389ed
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593373"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Zelfstudie: Een toepassing bijwerken in AKS (Azure Kubernetes Service)

Nadat een toepassing is geïmplementeerd in Kubernetes, kunt u deze bijwerken door een nieuwe containerinstallatiekopie of versie van de installatiekopie op te geven. De update wordt gefaseerd, zodat telkens maar een deel van de implementatie wordt bijgewerkt. Dankzij deze gefaseerde update kan de toepassing tijdens de update worden uitgevoerd. Het biedt ook een terugdraaimechanisme als er een implementatiefout optreedt.

In deze zelfstudie, deel zes van zeven, wordt de voorbeeldapp Azure Vote bijgewerkt. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * De code van de front-endtoepassing bijwerken
> * Een bijgewerkte containerinstallatiekopie maken
> * De containerinstallatiekopieën naar Azure Container Registry pushen
> * De bijgewerkte containerinstallatiekopie implementeren

## <a name="before-you-begin"></a>Voordat u begint

In eerdere zelfstudies is een toepassing verpakt in een containerinstallatiekopie. Deze installatiekopie is geüpload naar Azure Container Registry en u hebt een AKS-cluster gemaakt. De toepassing is vervolgens geïmplementeerd in het AKS-cluster.

Er is ook een toepassingsopslagplaats gekloond die de broncode van de toepassing bevat en een vooraf gemaakt Docker Compose-bestand dat in deze zelfstudie wordt gebruikt. Controleer of u een kloon van de opslagplaats hebt gemaakt en of u mappen in de gekloonde map hebt gewijzigd. Als u deze stappen niet hebt voltooid en wilt volgen, kunt u beginnen met [zelf studie 1: container installatie kopieën maken][aks-tutorial-prepare-app].

Voor deze zelfstudie moet u Azure CLI versie 2.0.53 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="update-an-application"></a>Een toepassing bijwerken

We gaan een wijziging aanbrengen in de voorbeeldtoepassing en de versie die al is geïmplementeerd in uw AKS-cluster vervolgens bijwerken. Zorg ervoor dat u zich bevindt in de gekloonde map *azure-voting-app-redis*. De broncode van de voorbeeldtoepassing vindt u dan in de map *azure-vote*. Open het bestand *config_file.cfg* met een teksteditor, zoals `vi`:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Wijzig de waarden voor *VOTE1VALUE* en *VOTE2VALUE* in verschillende waarden, zoals kleuren. In het volgende voorbeeld worden de bijgewerkte waarden weergegeven:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Sla het bestand op en sluit het. In `vi` gebruikt u `:wq`.

## <a name="update-the-container-image"></a>De containerinstallatiekopie bijwerken

Als u de front-end-installatie kopie opnieuw wilt maken en de bijgewerkte toepassing wilt testen, gebruikt u [docker-opstellen][docker-compose]. Het argument `--build` wordt gebruikt om Docker Compose te instrueren de installatiekopie van de toepassing opnieuw te maken:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>De toepassing lokaal testen

Om te controleren of uw wijzigingen worden weergegeven in de bijgewerkte containerinstallatiekopie, opent u een lokale webbrowser met `http://localhost:8080`.

![Afbeelding van Kubernetes-cluster in Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

De bijgewerkte waarden die zijn opgegeven in het bestand *config_file.cfg* worden weergegeven in de actieve toepassing.

## <a name="tag-and-push-the-image"></a>De installatiekopie taggen en pushen

Om de bijgewerkte installatiekopie correct te gebruiken, tagt u de installatiekopie *azure-vote-front* met de naam van de aanmeldingsserver van het ACR-register. Haal de naam van de aanmeldingsserver op met de opdracht [az acr list](/cli/azure/acr):

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Gebruik [docker tag][docker-tag] om de installatiekopie te taggen. Vervang `<acrLoginServer>` door de naam van de ACR-aanmeldingsserver of hostnaam van het openbare register, en werk de versie van de installatiekopie als volgt bij naar *: v2*:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Gebruik nu [docker push][docker-push] om de installatie kopie naar uw REGI ster te uploaden. Vervang `<acrLoginServer>` door de naam van de ACR-aanmeldingsserver.

> [!NOTE]
> Als u problemen ondervindt met het pushen van uw ACR-REGI ster, moet u ervoor zorgen dat u nog steeds bent aangemeld. Voer de opdracht [AZ ACR login][az-acr-login] uit met behulp van de naam van uw Azure container Registry die u hebt gemaakt in de stap [een Azure container Registry maken](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry) . Bijvoorbeeld `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>De bijgewerkte toepassing implementeren

Voor een maximale uptime moeten meerdere exemplaren van de toepassingsschil worden uitgevoerd. Controleer het aantal uitgevoerde front-end-instanties met de opdracht [kubectl Get peul][kubectl-get] :

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Als u niet meerdere front-end-schillen hebt, kunt u de *azure-vote-front*-implementatie als volgt schalen:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Gebruik de opdracht [kubectl set][kubectl-set] om de toepassing bij te werken. Werk `<acrLoginServer>` bij met de aanmeldingsserver of hostnaam van uw containerregister en geef de *v2*-toepassingsversie op:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Voor het controleren van de implementatie gebruikt u de opdracht [kubectl get pod][kubectl-get]. Terwijl de bijgewerkte toepassing wordt geïmplementeerd, worden uw schillen beëindigd en opnieuw gemaakt met de nieuwe containerinstallatiekopie.

```console
kubectl get pods
```

De volgende voorbeelduitvoer toont schillen die worden beëindigd en nieuwe exemplaren die worden uitgevoerd naarmate de implementatie verder gaat:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>De bijgewerkte toepassing testen

Als u de bijgewerkte toepassing wilt bekijken, moet u eerst het externe IP-adres van de `azure-vote-front` service ophalen:

```console
kubectl get service azure-vote-front
```

Open nu een lokale webbrowser met het IP-adres van uw service:

![Afbeelding van Kubernetes-cluster in Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een toepassing bijgewerkt en deze update geïmplementeerd in uw AKS-cluster. U hebt geleerd hoe u:

> [!div class="checklist"]
> * De code van de front-endtoepassing bijwerken
> * Een bijgewerkte containerinstallatiekopie maken
> * De containerinstallatiekopieën naar Azure Container Registry pushen
> * De bijgewerkte containerinstallatiekopie implementeren

Ga naar de volgende zelfstudie voor meer informatie over het upgraden van een AKS-cluster naar een nieuwe versie van Kubernetes.

> [!div class="nextstepaction"]
> [Kubernetes bijwerken][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
