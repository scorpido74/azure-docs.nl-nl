---
title: Verifiëren vanuit Kubernetes-cluster
description: Meer informatie over het bieden van toegang tot afbeeldingen in uw Azure-containerregister door een pull-geheim te maken met behulp van een serviceprincipal
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154892"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Afbeeldingen uit een Azure-containerregister naar een Kubernetes-cluster halen

U een Azure-containerregister gebruiken als een bron van containerafbeeldingen met elk Kubernetes-cluster, inclusief 'lokale' Kubernetes-clusters zoals [minikube](https://minikube.sigs.k8s.io/) en [vriendelijk.](https://kind.sigs.k8s.io/) In dit artikel ziet u hoe u een Kubernetes-pull-geheim maakt op basis van een Azure Active Directory-serviceprincipal. Gebruik vervolgens het geheim om afbeeldingen uit een Azure-containerregister te halen in een Kubernetes-implementatie.

> [!TIP]
> Als u de beheerde [Azure Kubernetes-service](../aks/intro-kubernetes.md)gebruikt, u [uw cluster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) ook integreren met een doelazure-containerregister voor het trekken van afbeeldingen. 

In dit artikel wordt ervan uitgegaan dat u al een privé-Azure-containerregister hebt gemaakt. U moet ook een Kubernetes-cluster hebben `kubectl` dat wordt uitgevoerd en toegankelijk is via de command-line tool.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Als u het hoofdwachtwoord van de service niet opslaat of onthoudt, u het opnieuw instellen met de opdracht az [ad sp-referenties opnieuw instellen:][az-ad-sp-credential-reset]

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Met deze opdracht wordt een nieuw, geldig wachtwoord geretourneerd voor uw serviceprincipal.

## <a name="create-an-image-pull-secret"></a>Een afbeeldingstrekgeheim maken

Kubernetes gebruikt een *afbeeldingstrekgeheim* om informatie op te slaan die nodig is om te verifiëren in uw register. Als u het pull-geheim voor een Azure-containerregister wilt maken, geeft u de servicehoofd-id, het wachtwoord en de url van het register op. 

Maak een afbeeldingstrekgeheim `kubectl` met de volgende opdracht:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
Hierbij

| Waarde | Beschrijving |
| :--- | :--- |
| `secret-name` | Naam van de afbeelding trek geheim, bijvoorbeeld *acr-secret* |
| `namespace` | Kubernetes naamruimte om het geheim in <br/> Alleen nodig als u het geheim in een andere naamruimte dan de standaardnaamruimte wilt plaatsen |
| `container-registry-name` | Naam van uw Azure-containerregister |
| `service-principal-ID` | ID van de serviceprincipal die door Kubernetes wordt gebruikt om toegang te krijgen tot uw register |
| `service-principal-password` | Servicehoofdwachtwoord |

## <a name="use-the-image-pull-secret"></a>Het geheim van de afbeeldingstrek gebruiken

Zodra u de afbeelding pull geheim hebt gemaakt, u deze gebruiken om Kubernetes-pods en -implementaties te maken. Geef de naam op `imagePullSecrets` van het geheim onder in het implementatiebestand. Bijvoorbeeld:

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

In het voorgaande `your-awesome-app:v1` voorbeeld is de naam van de afbeelding `acr-secret` die u uit het Azure-containerregister moet halen en is dit de naam van het pull-geheim dat u hebt gemaakt om toegang te krijgen tot het register. Wanneer u de pod implementeert, haalt Kubernetes de afbeelding automatisch uit uw register, als deze nog niet aanwezig is op het cluster.


## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Container Registry-verificatie met serviceprincipals](container-registry-auth-service-principal.md) voor meer informatie over het werken met serviceprincipals en Azure Container Registry-verificatie
* Meer informatie over geheimen voor het trekken van afbeeldingen in de [Kubernetes-documentatie](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset