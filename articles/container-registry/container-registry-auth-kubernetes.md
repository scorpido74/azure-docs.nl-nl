---
title: Verifiëren vanuit het Kubernetes-cluster
description: Meer informatie over het bieden van een Kubernetes-cluster met toegang tot installatie kopieën in uw Azure container Registry door een pull-geheim te maken met behulp van een Service-Principal
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154892"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Installatie kopieën van een Azure container Registry naar een Kubernetes-cluster halen

U kunt een Azure container Registry gebruiken als bron van container installatie kopieën met elk Kubernetes-cluster, met inbegrip van ' lokale ' Kubernetes-clusters, zoals [minikube](https://minikube.sigs.k8s.io/) en [soort](https://kind.sigs.k8s.io/). In dit artikel wordt beschreven hoe u een Kubernetes pull-geheim maakt op basis van een service-principal van Azure Active Directory. Gebruik vervolgens het geheim om installatie kopieën uit een Azure container Registry te halen in een Kubernetes-implementatie.

> [!TIP]
> Als u de beheerde [Azure Kubernetes-service](../aks/intro-kubernetes.md)gebruikt, kunt u [uw cluster ook integreren](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) met een Azure container Registry voor installatie kopieën. 

In dit artikel wordt ervan uitgegaan dat u al een persoonlijk Azure container Registry hebt gemaakt. U moet ook een Kubernetes-cluster met en toegankelijk hebben via het opdracht regel programma `kubectl`.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Als u het wacht woord van de Service-Principal niet opslaat of vergeet, kunt u het opnieuw instellen met de opdracht [AZ AD SP Credential reset][az-ad-sp-credential-reset] :

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Met deze opdracht wordt een nieuw, geldig wacht woord voor uw Service-Principal geretourneerd.

## <a name="create-an-image-pull-secret"></a>Een installatie kopie pull Secret maken

Kubernetes maakt gebruik van een *installatie kopie pull-geheim* voor het opslaan van gegevens die nodig zijn voor de verificatie van het REGI ster. Als u het pull-geheim voor een Azure container Registry wilt maken, geeft u de Service-Principal-ID, het wacht woord en de register-URL op. 

Maak een installatie kopie pull Secret met de volgende `kubectl` opdracht:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
positie

| Waarde | Beschrijving |
| :--- | :--- |
| `secret-name` | De naam van de installatie kopie pull Secret, bijvoorbeeld *ACR-Secret* |
| `namespace` | Naam ruimte Kubernetes om het geheim in te zetten <br/> Alleen nodig als u het geheim wilt plaatsen in een andere naam ruimte dan de standaard naam ruimte |
| `container-registry-name` | De naam van uw Azure container Registry |
| `service-principal-ID` | ID van de service-principal die wordt gebruikt door Kubernetes om toegang te krijgen tot uw REGI ster |
| `service-principal-password` | Service-Principal-wacht woord |

## <a name="use-the-image-pull-secret"></a>De afbeelding pull Secret gebruiken

Zodra u de installatie kopie hebt gemaakt, kunt u deze gebruiken voor het maken van Kubernetes en-implementaties. Geef de naam op van het geheim onder `imagePullSecrets` in het implementatie bestand. Bijvoorbeeld:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

In het vorige voor beeld is `your-awesome-app:v1` de naam van de afbeelding die uit het Azure container Registry moet worden opgehaald en `acr-secret` de naam is van het pull-geheim dat u hebt gemaakt voor toegang tot het REGI ster. Wanneer u de pod implementeert, haalt Kubernetes automatisch de installatie kopie uit het REGI ster, als deze nog niet aanwezig is in het cluster.


## <a name="next-steps"></a>Volgende stappen

* Zie [Azure container Registry verificatie met Service-principals](container-registry-auth-service-principal.md) voor meer informatie over het werken met Service-principals en Azure container Registry.
* Meer informatie over afbeeldings pull geheimen in de [Kubernetes-documentatie](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset