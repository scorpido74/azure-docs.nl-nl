---
title: Een Azure Kubernetes service-cluster beheren met het webdashboard
description: Meer informatie over het gebruik van het ingebouwde Kubernetes Web UI-dash board voor het beheren van een Azure Kubernetes service-cluster (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: 5aa8268fee7d43ad13ea8710760ba493683f502e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126883"
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

![De aanmeldings pagina van het Kubernetes Web dash board](./media/kubernetes-dashboard/dashboard-login.png)

U hebt de volgende opties om u aan te melden bij het dash board van uw cluster:

* Een [kubeconfig-bestand][kubeconfig-file]. U kunt een kubeconfig-bestand genereren met [AZ AKS Get-credentials][az-aks-get-credentials].
* Een token, zoals een [Service account token][aks-service-accounts] of gebruikers token. Op met [Aad ingeschakelde clusters][aad-cluster]is dit token een Aad-token. U kunt gebruiken `kubectl config view` om de tokens in uw kubeconfig-bestand weer te geven. Zie [Azure Active Directory integreren met de Azure Kubernetes-service met de Azure cli][aad-cluster]voor meer informatie over het maken van een Aad-token voor gebruik met een AKS-cluster.
* Het standaard dash board service-account, dat wordt gebruikt als u op *overs Laan*klikt.

> [!WARNING]
> Open het Kubernetes-dash board nooit openbaar, ongeacht de gebruikte verificatie methode.
> 
> Wanneer u verificatie instelt voor het Kubernetes-dash board, is het raadzaam om een token te gebruiken voor het standaard dash board service-account. Met een token kan elke gebruiker hun eigen machtigingen gebruiken. Als u het standaard dash board service-account gebruikt, kan een gebruiker hun eigen machtigingen negeren en in plaats daarvan het service account gebruiken.
> 
> Als u ervoor kiest om het standaard dashboard service account te gebruiken en uw AKS-cluster gebruikmaakt van RBAC, moet u een *ClusterRoleBinding* maken voordat u het dash board correct kunt openen. Standaard wordt het Kubernetes-dash board geïmplementeerd met minimale Lees toegang en worden RBAC-toegangs fouten weer gegeven. Een cluster beheerder kan ervoor kiezen om extra toegang te verlenen aan het *kubernetes-dash board-* service account, maar dit kan een vector zijn voor het escaleren van bevoegdheden. U kunt ook Azure Active Directory-verificatie integreren om een nauw keuriger toegangs niveau te bieden.
>
> Als u een binding wilt maken, gebruikt u de opdracht [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] , zoals weer gegeven in het volgende voor beeld. **Deze voorbeeld binding geldt niet voor aanvullende verificatie onderdelen en kan leiden tot onveilig gebruik.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> U hebt nu toegang tot het Kubernetes-dash board in uw cluster met RBAC-functionaliteit. Als u het Kubernetes-dash board wilt starten, gebruikt u de opdracht [AZ AKS Browse][az-aks-browse] zoals beschreven in de vorige stap.
>
> Als uw cluster geen RBAC gebruikt, wordt het niet aanbevolen een *ClusterRoleBinding*te maken.
> 
> Voor meer informatie over het gebruik van de verschillende verificatie methoden, zie de Kubernetes dash board-wiki op [toegangs beheer][dashboard-authentication].

Nadat u een methode hebt gekozen om u aan te melden, wordt het Kubernetes-dash board weer gegeven. Als u ervoor kiest om *token* of *Skip*te gebruiken, gebruikt het Kubernetes-dash board de machtigingen van de gebruiker die momenteel is aangemeld voor toegang tot het cluster.

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
