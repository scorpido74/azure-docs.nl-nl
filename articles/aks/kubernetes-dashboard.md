---
title: Een Azure Kubernetes Service-cluster beheren met het webdashboard
description: Meer informatie over het gebruik van het ingebouwde Kubernetes Web UI-dashboard voor het beheren van een AKS-cluster (Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595345"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Toegang tot het Kubernetes-webdashboard in Azure Kubernetes Service (AKS)

Kubernetes bevat een webdashboard dat kan worden gebruikt voor basisbeheerbewerkingen. Met dit dashboard u de basisstatus en statistieken voor uw toepassingen weergeven, services maken en implementeren en bestaande toepassingen bewerken. In dit artikel ziet u hoe u toegang krijgt tot het Kubernetes-dashboard met behulp van de Azure CLI en begeleidt u vervolgens door enkele basisdashboardbewerkingen.

Zie [Kubernetes Web UI Dashboard][kubernetes-dashboard]voor meer informatie over het Kubernetes-dashboard.

## <a name="before-you-begin"></a>Voordat u begint

De stappen in dit document gaan ervan uit dat u `kubectl` een AKS-cluster hebt gemaakt en een verbinding met het cluster hebt gemaakt. Als u een AKS-cluster wilt maken, raadpleegt u de [AKS snelstart][aks-quickstart].

Ook moet de Azure CLI-versie 2.0.46 of later zijn geïnstalleerd en geconfigureerd. Voer  `az --version`  uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Het Kubernetes-dashboard starten

Als u het Kubernetes-dashboard wilt starten, gebruikt u de opdracht [az aks browse.][az-aks-browse] In het volgende voorbeeld wordt het dashboard geopend voor het cluster met de naam *myAKSCluster* in de brongroep *myResourceGroup:*

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Met deze opdracht wordt een proxy gemaakt tussen uw ontwikkelingssysteem en de Kubernetes-API en wordt een webbrowser geopend voor het Kubernetes-dashboard. Als een webbrowser niet openstaat voor het Kubernetes-dashboard, kopieert en plakt `http://127.0.0.1:8001`u het URL-adres dat is vermeld in de Azure CLI, meestal .

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
> Als uw AKS-cluster RBAC gebruikt, moet een *ClusterRoleBinding* worden gemaakt voordat u het dashboard correct openen. Het Kubernetes-dashboard wordt standaard geïmplementeerd met minimale leestoegang en geeft RBAC-toegangsfouten weer. Het Kubernetes-dashboard ondersteunt momenteel geen door gebruikers verstrekte referenties om het toegangsniveau te bepalen, maar gebruikt de rollen die aan het serviceaccount worden toegekend. Een clusterbeheerder kan ervoor kiezen om extra toegang te verlenen tot het *kubernetes-dashboardserviceaccount,* maar dit kan een vector zijn voor escalatie van bevoegdheden. U azure Active Directory-verificatie ook integreren om een gedetailleerder toegangsniveau te bieden.
> 
> Als u een binding wilt maken, gebruikt u de opdracht [clusterrolebinding voor het maken van clusterrolebinding.][kubectl-create-clusterrolebinding] In het volgende voorbeeld ziet u hoe u een voorbeeldbinding maakt, maar deze voorbeeldbinding past geen extra verificatiecomponenten toe en kan leiden tot onveilig gebruik. Het Kubernetes-dashboard staat open voor iedereen die toegang heeft tot de URL. Stel het Kubernetes-dashboard niet openbaar bloot.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Zie de Kubernetes dashboardwiki over [toegangsbesturingselementen][dashboard-authentication]voor meer informatie over het gebruik van de verschillende verificatiemethoden.

![De overzichtspagina van het Kubernetes-webdashboard](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Een app maken

Als u wilt zien hoe het Kubernetes-dashboard de complexiteit van beheertaken kan verminderen, maken we een toepassing. U een toepassing maken vanuit het Kubernetes-dashboard door tekstinvoer, een YAML-bestand of via een grafische wizard te verstrekken.

Voer de volgende stappen uit om een toepassing te maken:

1. Selecteer de knop **Maken** in het rechterbovenvenster.
1. Als u de grafische wizard wilt gebruiken, kiest u **een app maken**.
1. Geef een naam op voor de implementatie, zoals *nginx*
1. Voer de naam in voor de containerafbeelding die u wilt gebruiken, zoals *nginx:1.15.5*
1. Als u poort 80 voor webverkeer wilt blootleggen, maakt u een Kubernetes-service. Selecteer **onder Service**, Selecteer **Extern**en voer **vervolgens 80** in voor zowel de poort als de doelpoort.
1. Selecteer **Implementeren** om de app te maken als je klaar bent.

![Een app implementeren in het Kubernetes-webdashboard](./media/kubernetes-dashboard/create-app.png)

Het duurt een minuut of twee voordat een openbaar extern IP-adres wordt toegewezen aan de Kubernetes-service. Selecteer **Services**onder Detectie **en Taakverdeling** onder de linkergrootte . De service van uw toepassing wordt weergegeven, inclusief de *externe eindpunten,* zoals in het volgende voorbeeld wordt weergegeven:

![Lijst met services en eindpunten weergeven](./media/kubernetes-dashboard/view-services.png)

Selecteer het eindpuntadres om een webbrowservenster te openen op de standaard NGINX-pagina:

![De standaardNGINX-pagina van de geïmplementeerde toepassing weergeven](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Pod-informatie weergeven

Het Kubernetes-dashboard kan basiscontrolestatistieken en informatie over probleemoplossing bieden, zoals logboeken.

Als u meer informatie wilt over uw toepassingspods, selecteert u **Pods** in het linkermenu. De lijst met beschikbare pods wordt weergegeven. Kies uw *nginx-pod* om informatie weer te geven, zoals resourceverbruik:

![Pod-informatie weergeven](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>De toepassing bewerken

Naast het maken en bekijken van applicaties, kan het Kubernetes-dashboard worden gebruikt om toepassingsimplementaties te bewerken en bij te werken. Om extra redundantie voor de toepassing te bieden, laten we het aantal NGINX-replica's verhogen.

Een implementatie bewerken:

1. Selecteer **Implementaties** in het linkermenu en kies vervolgens uw *nginx-implementatie.*
1. Selecteer **Bewerken** in de rechterbovenhoek.
1. Zoek `spec.replica` de waarde rond lijn 20. Als u het aantal replica's voor de toepassing wilt verhogen, wijzigt u deze waarde van *1* naar *3*.
1. Selecteer **Bijwerken** wanneer u gereed bent.

![De implementatie bewerken om het aantal replica's bij te werken](./media/kubernetes-dashboard/edit-deployment.png)

Het duurt even voordat de nieuwe pods in een replicaset worden gemaakt. Kies in het linkermenu **Replicasets**en kies vervolgens uw *nginx-replicaset.* De lijst met pods geeft nu het aantal bijgewerkte replica's weer, zoals in de volgende voorbeelduitvoer wordt weergegeven:

![Informatie weergeven over de replicaset](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Volgende stappen

Zie het [Kubernetes Web UI Dashboard voor][kubernetes-dashboard]meer informatie over het Kubernetes-dashboard.

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
