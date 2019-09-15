---
title: Een Azure Kubernetes service-cluster beheren met het webdashboard
description: Meer informatie over het gebruik van het ingebouwde Kubernetes Web UI-dash board voor het beheren van een Azure Kubernetes service-cluster (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: f150103c8e9534bfd1bb93d20e3d65d715767184
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996967"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Toegang tot het Kubernetes Web dash board in azure Kubernetes service (AKS)

Kubernetes bevat een webdashboard dat kan worden gebruikt voor elementaire beheer bewerkingen. Met dit dash board kunt u de basis status en-metrische gegevens voor uw toepassingen bekijken, services maken en implementeren, en bestaande toepassingen bewerken. In dit artikel wordt beschreven hoe u met de Azure CLI toegang krijgt tot het Kubernetes-dash board en vervolgens een aantal eenvoudige dashboard bewerkingen doorloopt.

Voor meer informatie over het Kubernetes-dash board raadpleegt u [Kubernetes Web UI-dash board][kubernetes-dashboard].

## <a name="before-you-begin"></a>Voordat u begint

Voor de stappen die in dit document worden beschreven, wordt ervan uitgegaan dat u een AKS `kubectl` -cluster hebt gemaakt en een verbinding met het cluster tot stand hebt gebracht. Als u een AKS-cluster moet maken, raadpleegt u de [AKS Quick][aks-quickstart]start.

Ook moet de Azure CLI-versie 2.0.46 of later zijn geïnstalleerd en geconfigureerd. Voer  `az --version`  uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Het Kubernetes-dash board starten

Als u het Kubernetes-dash board wilt starten, gebruikt u de opdracht [AZ AKS Browse][az-aks-browse] . In het volgende voor beeld wordt het dash board geopend voor het cluster met de naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Met deze opdracht maakt u een proxy tussen uw ontwikkel systeem en de Kubernetes-API en opent u een webbrowser op het Kubernetes-dash board. Als een webbrowser niet wordt geopend in het Kubernetes-dash board, kopieert en plakt u het URL-adres dat u in `http://127.0.0.1:8001`de Azure cli hebt genoteerd, meestal.

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Als uw AKS-cluster gebruikmaakt van RBAC, moet er een *ClusterRoleBinding* worden gemaakt voordat u het dash board correct kunt openen. Standaard wordt het Kubernetes-dash board geïmplementeerd met minimale Lees toegang en worden RBAC-toegangs fouten weer gegeven. Het Kubernetes-dash board biedt momenteel geen ondersteuning voor door de gebruiker geleverde referenties om het toegangs niveau te bepalen. hiervoor worden de rollen gebruikt die aan het service account worden verleend. Een cluster beheerder kan ervoor kiezen om extra toegang te verlenen aan het *kubernetes-dash board-* service account, maar dit kan een vector zijn voor het escaleren van bevoegdheden. U kunt ook Azure Active Directory-verificatie integreren om een nauw keuriger toegangs niveau te bieden.
> 
> Als u een binding wilt maken, gebruikt u de opdracht [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] . In het volgende voor beeld ziet u hoe u een voor beeld-binding maakt, maar deze voorbeeld binding heeft geen betrekking op extra verificatie onderdelen en kan leiden tot onveilig gebruik. Het Kubernetes-dash board is geopend voor iedereen die toegang heeft tot de URL. Maak het Kubernetes-dash board niet openbaar zichtbaar.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Voor meer informatie over het gebruik van de verschillende verificatie methoden, zie de Kubernetes dash board-wiki op [toegangs beheer][dashboard-authentication].

![De overzichts pagina van het Kubernetes Web dash board](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Een app maken

Als u wilt zien hoe het Kubernetes-dash board de complexiteit van beheer taken kan verminderen, gaan we een toepassing maken. U kunt een toepassing vanuit het Kubernetes-dash board maken door tekst invoer, een YAML-bestand of een grafische wizard te leveren.

Voer de volgende stappen uit om een toepassing te maken:

1. Selecteer de knop **maken** in het rechter venster.
1. Als u de grafische wizard wilt gebruiken, kiest u voor het **maken van een app**.
1. Geef een naam op voor de implementatie, bijvoorbeeld *nginx*
1. Voer de naam in voor de container installatie kopie die moet worden gebruikt, zoals *nginx: 1.15.5*
1. Als u poort 80 voor webverkeer beschikbaar wilt maken, maakt u een Kubernetes-service. Onder **service**selecteert u **extern**en voert u **80** in voor de poort en de doel poort.
1. Wanneer u klaar bent, selecteert u **implementeren** om de app te maken.

![Een app implementeren in het Kubernetes Web dash board](./media/kubernetes-dashboard/create-app.png)

Het duurt enkele minuten voordat een openbaar extern IP-adres wordt toegewezen aan de Kubernetes-service. Klik aan de linkerkant onder **detectie en taak verdeling** **Services**selecteren. De service van uw toepassing wordt vermeld, inclusief de *externe eind punten*, zoals wordt weer gegeven in het volgende voor beeld:

![Lijst met Services en eind punten weer geven](./media/kubernetes-dashboard/view-services.png)

Selecteer het eindpunt adres om een webbrowser venster te openen op de standaard NGINX-pagina:

![De standaard NGINX-pagina van de geïmplementeerde toepassing weer geven](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Pod-gegevens weer geven

Het Kubernetes-dash board kan basis gegevens over bewaking en informatie over het oplossen van problemen bieden, zoals Logboeken.

Als u meer informatie over uw toepassing wilt zien, selecteert u in het menu aan de linkerkant de optie **peul** . De lijst met beschik bare peulen wordt weer gegeven. Kies uw *nginx* pod om informatie weer te geven, zoals Resource verbruik:

![Pod-gegevens weer geven](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>De toepassing bewerken

Naast het maken en weer geven van toepassingen, kunt u het Kubernetes-dash board gebruiken om toepassings implementaties te bewerken en bij te werken. Als u aanvullende redundantie voor de toepassing wilt bieden, kunt u het aantal NGINX-replica's verhogen.

Een implementatie bewerken:

1. Selecteer **implementaties** in het menu links en kies vervolgens uw *nginx* -implementatie.
1. Selecteer **bewerken** in de rechter bovenhoek navigatie balk.
1. Zoek de `spec.replica` waarde, rond regel 20. Als u het aantal replica's voor de toepassing wilt verhogen, wijzigt u deze waarde van *1* in *3*.
1. Selecteer **bijwerken** wanneer gereed.

![De implementatie bewerken om het aantal replica's bij te werken](./media/kubernetes-dashboard/edit-deployment.png)

Het kan even duren voordat het nieuwe Peul is gemaakt binnen een replicaset. Kies in het menu links de optie **replica sets**en kies vervolgens uw *nginx* -replicaset. De lijst met peulen weerspiegelt nu het bijgewerkte aantal replica's, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

![Informatie over de replicaset weer geven](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het Kubernetes-dash board vindt u in het [dash board Kubernetes Web UI][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
