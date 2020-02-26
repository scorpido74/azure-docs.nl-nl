---
title: 'Snelstart: een AKS-cluster (Azure Kubernetes Service) maken'
description: Meer informatie over hoe u snel een Kubernetes-cluster maakt met behulp van een Azure Resource Manager sjabloon en hoe u een toepassing implementeert in azure Kubernetes service (AKS)
services: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.custom: mvc
ms.openlocfilehash: 5fe82dbce2069b0ae1a88599d020f4ba6b116027
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595289"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Snelstartgids: een AKS-cluster (Azure Kubernetes service) implementeren met behulp van een Azure Resource Manager sjabloon

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In deze Quick Start implementeert u een AKS-cluster met behulp van een Azure Resource Manager sjabloon. In het cluster wordt een toepassing met meerdere containers uitgevoerd die bestaat uit een web-front-end en een Redis-exemplaar.

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

In deze snelstart wordt ervan uitgegaan dat u een basisbegrip hebt van Kubernetes-concepten. Zie [Kubernetes core-concepten voor Azure Kubernetes service (AKS)][kubernetes-concepts]voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quick Start de Azure CLI-versie 2.0.61 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="prerequisites"></a>Vereisten

Als u een AKS-cluster wilt maken met behulp van een resource manager-sjabloon, geeft u een open bare SSH-sleutel en Azure Active Directory Service-Principal op. Als u een van deze resources nodig hebt, raadpleegt u de volgende sectie. Ga anders naar de sectie [een AKS-cluster maken](#create-an-aks-cluster) .

### <a name="create-an-ssh-key-pair"></a>Een SSH-sleutelpaar maken

Als u toegang wilt krijgen tot AKS-knoop punten, maakt u verbinding met een SSH-sleutel paar. Gebruik de `ssh-keygen` opdracht om open bare en persoonlijke SSH-sleutel bestanden te genereren. Deze bestanden worden standaard gemaakt in de map *~/.ssh* . Als er op de opgegeven locatie een SSH-sleutel paar met dezelfde naam bestaat, worden deze bestanden overschreven.

Met de volgende opdracht wordt een SSH-sleutel paar gemaakt met behulp van RSA-versleuteling en een bitlengte van 2048:

```azurecli-interactive
ssh-keygen -t rsa -b 2048
```

Zie [SSH-sleutels voor verificatie in azure maken en beheren][ssh-keys]voor meer informatie over het maken van SSH-sleutels.

### <a name="create-a-service-principal"></a>Een service-principal maken

Een AKS-cluster heeft een service-principal van Azure Active Directory nodig om met andere Azure-resources te kunnen communiceren. Maak een service-principal met behulp van de opdracht [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. De parameter `--skip-assignment` zorgt ervoor dat eventuele extra machtigingen beperkt kunnen worden toegewezen. Standaard is de service-principal geldig voor één jaar.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De uitvoer lijkt op die in het volgende voorbeeld:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Noteer de *appId* en *wachtwoord*. Deze waarden worden gebruikt in de volgende stappen.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

De sjabloon die in deze Quick Start wordt gebruikt, is het [implementeren van een Azure Kubernetes service-cluster](https://azure.microsoft.com/resources/templates/101-aks/). Zie de site [AKS Quick][aks-quickstart-templates] start-sjablonen voor meer AKS-voor beelden.

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen.

    [![Implementeren in Azure](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.  

    Voor deze Quick start moet u de standaard waarden voor de *besturingssysteem schijf grootte GB*, het *aantal agents*, de *VM-grootte*van de agent, het *type besturings systeem*en de *Kubernetes-versie*behouden. Geef uw eigen waarden op voor de volgende sjabloon parameters:

    * **Subscription**: selecteer een Azure-abonnement.
    * **Resource groep**: Selecteer **nieuwe maken**. Voer een unieke naam in voor de resource groep, zoals *myResourceGroup*, en kies vervolgens **OK**.
    * **Locatie**: Selecteer een locatie, bijvoorbeeld **VS-Oost**.
    * **Cluster naam**: Voer een unieke naam in voor het AKS-cluster, zoals *myAKSCluster*.
    * **DNS-voor voegsel**: Voer een uniek DNS-voor voegsel voor uw cluster in, zoals *myakscluster*.
    * **Gebruikers naam voor Linux-beheerder**: Voer een gebruikers naam in om verbinding te maken via SSH, zoals *azureuser*.
    * **Open bare SSH RSA-sleutel**: Kopieer en plak het *open bare* deel van uw SSH-sleutel paar (standaard de inhoud van *~/.ssh/id_rsa. pub*).
    * **Service-Principal-client-id**: Kopieer en plak de *AppID* van uw service-principal van de `az ad sp create-for-rbac` opdracht.
    * **Service-Principal-client geheim**: Kopieer en plak het *wacht woord* van de service-principal van de `az ad sp create-for-rbac` opdracht.
    * **Ik ga akkoord met de status van de voor waarden hierboven**: Schakel dit selectie vakje in om akkoord te gaan.

    ![Resource Manager-sjabloon voor het maken van een Azure Kubernetes service-cluster in de portal](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Selecteer **Aankoop**.

Het duurt enkele minuten om het AKS-cluster te maken. Wacht totdat het cluster met succes is geïmplementeerd voordat u verdergaat met de volgende stap.

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdracht regel-client. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [AZ AKS install-cli][az-aks-install-cli] :

```azurecli
az aks install-cli
```

Gebruik de opdracht `kubectl`az aks get-credentials[ om ][az-aks-get-credentials] zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

In de volgende voorbeeld uitvoer ziet u de knoop punten die zijn gemaakt in de vorige stappen. Zorg ervoor dat de status voor alle knoop punten *gereed*is:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In deze snelstart worden met behulp van een manifest alle objecten gemaakt die nodig zijn om de Azure Vote-toepassing uit te voeren. Dit manifest bevat twee [Kubernetes-implementaties][kubernetes-deployment] : een voor de voor beelden van Azure stem python-toepassingen en de andere voor een redis-exemplaar. Er zijn twee [Kubernetes-Services][kubernetes-service] gemaakt: een interne service voor het redis-exemplaar en een externe service voor toegang tot de Azure stem-toepassing via internet.

> [!TIP]
> In deze snelstart maakt en implementeert u handmatig uw toepassingsmanifesten in het AKS-cluster. In meer Real-World scenario's kunt u [Azure dev Spaces][azure-dev-spaces] gebruiken om snel uw code te herhalen en fouten in het AKS-cluster op te sporen. U kunt Dev Spaces gebruiken op alle OS-platformen en in alle ontwikkelomgevingen, en u kunt samenwerken met andere leden van uw team.

Maak een bestand met de naam `azure-vote.yaml` en kopieer de volgende YAML-definitie naar het bestand. Als u Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met behulp van `vi` of `nano`, zoals bij een virtueel of fysiek systeem:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Implementeer de toepassing met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

In de volgende voorbeelduitvoer ziet u dat je implementaties en services zijn gemaakt:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren.

Gebruik de opdracht [kubectl get service][kubectl-get] met het argument `--watch` om de voortgang te controleren.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Eerst wordt het *EXTERNAL-IP*-adres voor de service *azure-vote-front* weergegeven als *in behandeling*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het *Extern IP-adres* is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Open een webbrowser naar het externe IP-adres van uw service om de Azure Vote-app te zien.

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="delete-cluster"></a>Cluster verwijderen

Wanneer het cluster niet meer nodig is, gebruikt u de opdracht [AZ Group delete][az-group-delete] om de resource groep, de container service en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [AKS Service Principal overwegingen en verwijderen][sp-delete]voor stappen voor het verwijderen van de Service-Principal.

## <a name="get-the-code"></a>Code ophalen

In deze snelstartgids zijn vooraf gemaakte containerinstallatiekopieën gebruikt om een Kubernetes-implementatie te maken. De gerelateerde toepassingscode, Dockerfile en het Kubernetes-manifestbestand zijn beschikbaar op GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een Kubernetes-cluster geïmplementeerd en vervolgens een toepassing met meerdere containers geïmplementeerd. [Open het Kubernetes-webdashboard][kubernetes-dashboard] voor het cluster dat u hebt gemaakt.

Voor meer informatie over AKS en een volledig stapsgewijs voorbeeld van code tot implementatie gaat u naar de zelfstudie over Kubernetes-clusters.

> [!div class="nextstepaction"]
> [Zelf studie voor AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
