---
title: Certificaten roteren in Azure Kubernetes Service (AKS)
description: Meer informatie over het roteren van uw certificaten in een AKS-cluster (Azure Kubernetes Service).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00dcef4ae0f04fc7f550859238ae8c7e1ad19384
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549072"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Certificaten roteren in Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) gebruikt certificaten voor verificatie met veel van de componenten. Het kan zijn dat u deze certificaten periodiek moet roteren om veiligheidsredenen of beleidsredenen. U bijvoorbeeld een beleid hebben om al uw certificaten elke 90 dagen te roteren.

In dit artikel ziet u hoe u de certificaten in uw AKS-cluster roteren.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel moet u de Azure CLI-versie 2.0.77 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS-certificaten, certificaatautoriteiten en serviceaccounts

AKS genereert en gebruikt de volgende certificaten, certificaatautoriteiten en serviceaccounts:

* De AKS API-server maakt een Certificaatautoriteit (CA) genaamd de Cluster CA.
* De API-server heeft een cluster-CA, die certificaten voor eenrichtingscommunicatie van de API-server naar kubelets ondertekent.
* Elke kubelet maakt ook een Certificate Signing Request (CSR), die is ondertekend door de Cluster CA, voor communicatie van de kubelet naar de API-server.
* In het geëetcd-sleutelwaardearchief is een certificaat ondertekend door de cluster-CA voor communicatie van etcd naar de API-server.
* Het geëetcd-sleutelwaardearchief maakt een CA die certificaten ondertekent om gegevensreplicatie tussen geëetcd replica's in het AKS-cluster te verifiëren en te autoriseren.
* De API-aggregator gebruikt de cluster-CA om certificaten uit te geven voor communicatie met andere API's. De API-aggregator kan ook een eigen CA hebben voor de uitgifte van deze certificaten, maar gebruikt momenteel de Cluster CA.
* Elk knooppunt maakt gebruik van een SA-token (Service Account), dat is ondertekend door de Cluster CA.
* De `kubectl` client heeft een certificaat voor communicatie met het AKS-cluster.

> [!NOTE]
> AKS-clusters die vóór maart 2019 zijn gemaakt, hebben certificaten die na twee jaar verlopen. Elk cluster dat na maart 2019 is gemaakt of een cluster waarop zijn certificaten zijn geroteerd, hebben Cluster CA-certificaten die na 30 jaar verlopen. Alle andere certificaten vervallen na twee jaar. Als u wilt controleren wanneer `kubectl get nodes` uw cluster is gemaakt, gebruikt u de *leeftijd* van uw knooppuntgroepen.
> 
> Bovendien u de vervaldatum van het certificaat van uw cluster controleren. In de volgende opdracht worden bijvoorbeeld de certificaatgegevens voor het *myAKSCluster* weergegeven.
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Uw clustercertificaten roteren

> [!WARNING]
> Het roteren van `az aks rotate-certs` uw certificaten met behulp van kan leiden tot maximaal 30 minuten downtime voor uw AKS-cluster.

Gebruik [az aks-ophaalreferenties][az-aks-get-credentials] om je aan te melden bij je AKS-cluster. Deze opdracht downloadt en `kubectl` configureert ook het clientcertificaat op uw lokale machine.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Hiermee `az aks rotate-certs` u alle certificaten, CV's en SA's op uw cluster roteren.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Het kan tot 30 `az aks rotate-certs` minuten duren voordat deze is voltooid. Als de opdracht mislukt voordat `az aks show` de opdracht is voltooid, gebruikt u om de status van het cluster te *verifiëren: Certificaat roteren .* Als het cluster zich in een `az aks rotate-certs` mislukte status bevindt, wordt u opnieuw uitgevoerd om de certificaten opnieuw te roteren.

Controleer of de oude certificaten niet `kubectl` langer geldig zijn door een opdracht uit te voeren. Aangezien u de certificaten die `kubectl`door worden gebruikt niet hebt bijgewerkt, ziet u een fout.  Bijvoorbeeld:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Werk het certificaat `kubectl` bij `az aks get-credentials`dat wordt gebruikt door het uitvoeren van .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Controleer of de certificaten zijn `kubectl` bijgewerkt door een opdracht uit te voeren, die nu succesvol is. Bijvoorbeeld:

```console
kubectl get no
```

> [!NOTE]
> Als u services hebt die bovenop AKS worden uitgevoerd, zoals [Azure Dev Spaces,][dev-spaces]moet u mogelijk ook [certificaten met betrekking tot die services bijwerken.][dev-spaces-rotate]

## <a name="next-steps"></a>Volgende stappen

In dit artikel ziet u hoe u de certificaten, DE's en SA's van uw cluster automatisch roteren. U [Aanbevolen procedures voor clusterbeveiliging en upgrades in Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] bekijken voor meer informatie over aanbevolen procedures voor AKS-beveiliging.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
