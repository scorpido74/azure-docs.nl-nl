---
title: 'Snelstart: een AKS-cluster (Azure Kubernetes Service) maken in de portal'
description: Lees hoe u met Azure Portal snel een Kubernetes-cluster kunt maken, een toepassing kunt implementeren en de prestaties kunt bewaken in Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: quickstart
ms.date: 5/31/2019
ms.author: mlearned
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 89bb7014ddb04b63a83dc8c5b520bcf500bdc707
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472705"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Snelstartgids: een AKS-cluster (Azure Kubernetes service) implementeren met behulp van de Azure Portal

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In deze quickstart implementeert u een AKS-cluster met behulp van Azure Portal. Een toepassing met meerdere containers die bestaat uit een web-front-end en een Redis-exemplaar wordt uitgevoerd in het cluster. U ziet vervolgens hoe u de status van het cluster en de pods die uw toepassing uitvoeren kunt bewaken.

![Afbeelding van browsen naar de Azure Vote-voorbeeldtoepassing](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

In deze quickstart wordt ervan uitgegaan dat u een basisbegrip hebt van Kubernetes-concepten. Zie [Kubernetes core-concepten voor Azure Kubernetes service (AKS)][kubernetes-concepts]voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Voltooi de volgende stappen om een AKS-cluster te maken:

1. Selecteer in het menu Azure Portal of op de **Start** pagina de optie **een resource maken**.

2. Selecteer **Containers** >  **Kubernetes-service**.

3. Configureer op de pagina **basis beginselen** de volgende opties:
    - **Project Details**: Selecteer een Azure- **abonnement**en selecteer of maak een Azure- **resource groep**, zoals *myResourceGroup*.
    - **Cluster Details**: Voer een **Kubernetes-cluster naam**in, zoals *myAKSCluster*. Selecteer een **regio**, **Kubernetes-versie**en het voor voegsel van de **DNS-naam** voor het AKS-cluster.
    - **Primaire knooppunt groep**: Selecteer een VM- **knooppunt grootte** voor de AKS-knoop punten. De grootte van de virtuele machine *kan niet* worden gewijzigd nadat een AKS-cluster is geïmplementeerd. 
            -Selecteer het aantal knoop punten dat in het cluster moet worden geïmplementeerd. Stel voor deze quickstart het **Aantal knooppunten** in op *1*. Het aantal knooppunten kan nog *wel* worden gewijzigd als het cluster is geïmplementeerd.
    
    ![AKS-cluster maken - basisgegevens opgeven](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    Selecteer **volgende: schalen** wanneer voltooid.

4. Behoud op de pagina **schaal** de standaard opties. Klik onder aan het scherm op **volgende: authenticatie**.
    > [!CAUTION]
    > Het maken van nieuwe AAD-service-principals kan enige minuten duren en is beschikbaar als gevolg van Service-Principal niet gevonden fouten en validatie fouten in Azure Portal. Als u dit hebt bereikt, kunt u [hier](troubleshooting.md#im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one) naar oplossingen zoeken.

5. Configureer op de pagina **verificatie** de volgende opties:
    - Maak een nieuwe Service-Principal door het veld **Service Principal** te verlaten met **(nieuw) standaard Service-Principal**. U kunt ook *Service-Principal configureren* selecteren om een bestaande te gebruiken. Als u een bestaand account gebruikt, moet u de SPN-client-ID en het geheim opgeven.
    - Schakel de optie voor Kubernetes-toegangsbeheer op basis van rollen (RBAC) in. Dit biedt meer nauw keurige controle over toegang tot de Kubernetes-resources die zijn geïmplementeerd in uw AKS-cluster.

Standaard worden *basis* netwerkfuncties gebruikt en Azure Monitor voor containers is ingeschakeld. Klik op **beoordeling + maken** en **Maak** een validatie wanneer de verificatie is voltooid.

Het duurt enkele minuten om het AKS-cluster te maken. Wanneer de implementatie is voltooid, klikt **u op Ga naar resource**of bladert u naar de AKS-cluster resource groep, zoals *myResourceGroup*, en selecteert u de AKS-resource, zoals *myAKSCluster*. Het AKS-cluster dashboard wordt weer gegeven, zoals in dit voor beeld:

![Voorbeeld van AKS-dashboard in de Azure-portal](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdracht regel-client. De client `kubectl` is vooraf geïnstalleerd in Azure Cloud Shell.

Open Cloud Shell met behulp van de knop `>_` aan de bovenkant van de Azure Portal.

![Open Azure Cloud Shell in de portal](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

Gebruik de opdracht `kubectl`az aks get-credentials[ om ][az-aks-get-credentials] zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties. In het volgende voorbeeld worden de referenties opgehaald voor de clusternaam *myAKSCluster* in de resourcegroep met de naam *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

In de volgende voorbeelduitvoer ziet u het enkele knooppunt dat is gemaakt in de vorige stappen. Zorg ervoor dat de status van het knooppunt *Ready* is:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In deze snelstart worden met behulp van een manifest alle objecten gemaakt die nodig zijn om de Azure Vote-toepassing uit te voeren. Dit manifest bevat twee [Kubernetes-implementaties][kubernetes-deployment] : een voor de voor beelden van Azure stem python-toepassingen en de andere voor een redis-exemplaar. Er zijn twee [Kubernetes-Services][kubernetes-service] gemaakt: een interne service voor het redis-exemplaar en een externe service voor toegang tot de Azure stem-toepassing via internet.

> [!TIP]
> In deze snelstart maakt en implementeert u handmatig uw toepassingsmanifesten in het AKS-cluster. In meer Real-World scenario's kunt u [Azure dev Spaces][azure-dev-spaces] gebruiken om snel uw code te herhalen en fouten in het AKS-cluster op te sporen. U kunt Dev Spaces gebruiken op alle OS-platformen en in alle ontwikkelomgevingen, en u kunt samenwerken met andere leden van uw team.

Gebruik `nano` of `vi` in Cloud shell om een bestand met de naam `azure-vote.yaml` te maken en kopieer de volgende YAML definitie:

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

Zodra het *EXTERNAL-IP*-adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Open een webbrowser naar het externe IP-adres van uw service om de Azure Vote-app te zien.

![Afbeelding van browsen naar de Azure Vote-voorbeeldtoepassing](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Status en logboeken controleren

Toen u het cluster maakte, werd Azure Monitor voor containers ingeschakeld. Deze controlefunctie biedt metrische statusgegevens voor zowel het AKS-cluster als de pods die in het cluster worden uitgevoerd.

Het kan enkele minuten duren voordat deze gegevens in Azure Portal worden ingevuld. Ga terug naar de AKS-resource in de Azure-portal, zoals *myAKSCluster*, om de huidige status, de uptime en het resourcegebruik te zien voor de Azure Vote-pods. Vervolgens kunt u de status als volgt openen:

1. Kies onder **Bewaking** aan de linkerkant de optie **Inzichten**
1. Kies bovenaan **+ Filter toevoegen**
1. Selecteer *Namespace* als eigenschap en kies vervolgens  *\<Alles behalve kube-systeem\>*
1. Kies de weergave **Containers**.

De containers *azure-vote-back* en *azure-vote-front* worden weergegeven, zoals in het volgende voorbeeld:

![De status van actieve containers in AKS weergeven](media/kubernetes-walkthrough-portal/monitor-containers.png)

Als u de logboeken voor de `azure-vote-front` pod wilt bekijken, selecteert u de **container logboeken weer geven** in de vervolg keuzelijst van de lijst met containers. Deze logboeken bevatten de stromen *stdout* en *stderr* van de container.

![De containerlogboeken in AKS weergeven](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Cluster verwijderen

Wanneer het cluster niet meer nodig is, verwijdert u de clusterresource. Hierdoor worden ook alle bijbehorende resources verwijderd. Deze bewerking kan worden voltooid in de Azure-portal door op het AKS-clusterdashboard de knop **Verwijderen** te selecteren. U kunt ook de opdracht [AZ AKS delete][az-aks-delete] gebruiken in de Cloud shell:

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [AKS Service Principal overwegingen en verwijderen][sp-delete]voor stappen voor het verwijderen van de Service-Principal.

## <a name="get-the-code"></a>Code ophalen

In deze snelstartgids zijn vooraf gemaakte containerinstallatiekopieën gebruikt om een Kubernetes-implementatie te maken. De gerelateerde toepassingscode, Dockerfile en het Kubernetes-manifestbestand zijn beschikbaar op GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een Kubernetes-cluster geïmplementeerd en vervolgens een toepassing met meerdere containers geïmplementeerd.

Voor meer informatie over AKS en een volledig stapsgewijs voorbeeld van code tot implementatie gaat u naar de zelfstudie over Kubernetes-clusters.

> [!div class="nextstepaction"]
> [Zelf studie voor AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../monitoring/monitoring-container-health.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
