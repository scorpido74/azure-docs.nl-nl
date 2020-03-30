---
title: Integreren met de door Azure beheerde services met behulp van Open Service Broker for Azure (OSBA)
description: Integreren met de door Azure beheerde services met behulp van Open Service Broker for Azure (OSBA)
author: zr-msft
ms.topic: overview
ms.date: 12/05/2017
ms.author: zarhoads
ms.openlocfilehash: 2eddedea7d626a92e21442c81aa49e00491958a1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78273026"
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Integreren met de door Azure beheerde services met behulp van Open Service Broker for Azure (OSBA)

Net als [Kubernetes Service Catalog][kubernetes-service-catalog] stelt Open Service Broker for Azure (OSBA) ontwikkelaars in staat om door Azure beheerde services in Kubernetes te gebruiken. Deze handleiding richt zich op het gebruik van Kubernetes voor de implementatie van Kubernetes Service Catalog, Open Service Broker for Azure (OSBA) en de toepassingen die gebruikmaken van door Azure beheerde services.

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement

* Azure CLI: [installeer deze lokaal ][azure-cli-install] of gebruik deze in [Azure Cloud Shell][azure-cloud-shell].

* Helm CLI 2.7+: [installeer deze lokaal ][helm-cli-install] of gebruik deze in [Azure Cloud Shell][azure-cloud-shell].

* Machtigingen voor het maken van een service-principal met de rol Inzender in uw Azure-abonnement

* Een bestaand AKS-cluster (Azure Kubernetes Service). Als u een cluster AKS nodig hebt, volgt u de snelstartgids [Een AKS-cluster maken][create-aks-cluster].

## <a name="install-service-catalog"></a>Service Catalog installeren

De eerste stap bestaat uit het installeren van Service Catalog in uw Kubernetes-cluster met behulp van een Helm-diagram.

Ga [https://shell.azure.com](https://shell.azure.com) naar Cloud Shell openen in uw browser.

Upgrade uw Tiller-installatie (Helm-server) in het cluster met:

```console
helm init --upgrade
```

Voeg nu het Service Catalog-diagram toe aan de Helm-opslagplaats:

```console
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

Installeer tot slot Service Catalog met het Helm-diagram. Als in uw cluster RBAC is ingeschakeld, moet u deze opdracht uitvoeren.

```console
helm install svc-cat/catalog --name catalog --namespace catalog --set apiserver.storage.etcd.persistence.enabled=true --set apiserver.healthcheck.enabled=false --set controllerManager.healthcheck.enabled=false --set apiserver.verbosity=2 --set controllerManager.verbosity=2
```

Als in uw cluster RBAC niet is ingeschakeld, moet u deze opdracht uitvoeren.

```console
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false --set apiserver.storage.etcd.persistence.enabled=true --set apiserver.healthcheck.enabled=false --set controllerManager.healthcheck.enabled=false --set apiserver.verbosity=2 --set controllerManager.verbosity=2
```

Nadat het Helm-diagram is uitgevoerd, controleert u of `servicecatalog` in de uitvoer van de volgende opdracht wordt weergegeven:

```console
kubectl get apiservice
```

U zou bijvoorbeeld uitvoer moeten zien die vergelijkbaar is met het volgende (hier ingekort weergegeven):

```output
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Open Service Broker for Azure installeren

De volgende stap bestaat uit het installeren van [Open Service Broker for Azure][open-service-broker-azure], waar ook de catalogus voor de door Azure beheerde services deel van uitmaakt. Voorbeelden van beschikbare Azure-services zijn onder andere Azure Database for PostgreSQL, Azure Database for MySQL en Azure SQL Database.

Begin met het toevoegen van de Helm-opslagplaats van Open Service Broker voor Azure:

```console
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

Maak een [service-principal][create-service-principal] met de volgende Azure CLI-opdracht:

```azurecli-interactive
az ad sp create-for-rbac
```

De uitvoer moet er ongeveer als volgt uitzien. Noteer de `appId`, `password` en de waarden van `tenant`. Deze gaat u in de volgende stap gebruiken.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

Stel de volgende omgevingsvariabelen in op basis van de bovenstaande waarden:

```console
AZURE_CLIENT_ID=<appId>
AZURE_CLIENT_SECRET=<password>
AZURE_TENANT_ID=<tenant>
```

Haal nu uw Azure-abonnements-id op:

```azurecli-interactive
az account show --query id --output tsv
```

Stel weer de volgende omgevingsvariabele in op basis van de bovenstaande waarde:

```console
AZURE_SUBSCRIPTION_ID=[your Azure subscription ID from above]
```

Nu u deze omgevingsvariabelen hebt ingevuld, voert u de volgende opdracht uit voor het installeren van Open Service Broker for Azure met behulp van het Helm-diagram:

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

Zodra de OSBA-implementatie is voltooid, installeert u [Service Catalog CLI][service-catalog-cli], dit is een gebruiksvriendelijke opdrachtregelinterface voor het uitvoeren van query's op servicebrokers, serviceklassen, serviceabonnementen en meer.

Voer de volgende opdrachten uit als u Service Catalog CLI binair wilt installeren:

```console
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

Nu zorgt u dat er u een lijst met servicebrokers wordt weergegeven:

```console
./svcat get brokers
```

De uitvoer ziet er als volgt uit:

```output
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

Vervolgens zorgt u ervoor dat er een lijst met de beschikbare serviceklassen wordt weergegeven. De weergegeven serviceklassen zijn de beschikbare door Azure beheerde services die kunnen worden ingericht met behulp van Open Service Broker for Azure.

```console
./svcat get classes
```

En tot slot gaat u een lijst met alle beschikbare serviceabonnementen weergeven. Serviceabonnementen zijn de servicelagen voor de door Azure beheerde services. Bijvoorbeeld: voor Azure Database for MySQL variëren de abonnementen van `basic50` voor de Basic-laag met 50 DTU's (Database Transaction Units) tot `standard800` voor de Standard-laag met 800 DTU's.

```console
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>WordPress vanaf een Helm-diagram installeren met behulp van Azure Database for MySQL

In deze stap gaat u Helm gebruiken om een bijgewerkt Helm-diagram voor WordPress te installeren. Het diagram zorgt ervoor dat een extern exemplaar van Azure Database for MySQL wordt ingericht dat door WordPress kan worden gebruikt. Dit proces kan enkele minuten duren.

```console
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0 --set replicaCount=1
```

Als u wilt controleren of met de installatie de juiste resources zijn ingericht, moet u ervoor zorgen dat de geïnstalleerde service-exemplaren en -bindingen worden weergeven:

```console
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

Lijst met geïnstalleerde geheimen weergeven:

```console
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>Volgende stappen

Door dit artikel te volgen, hebt u Service Catalog geïmplementeerd naar een AKS-cluster (Azure Kubernetes Service). U hebt Open Service Broker for Azure gebruikt om een WordPress-installatie te implementeren die gebruikmaakt van door Azure beheerde services; in dit geval Azure Database for MySQL.

Raadpleeg de opslagplaats met [Azure/Helm-diagrammen][helm-charts] voor toegang tot andere bijgewerkte op OSBA gebaseerde Helm-diagrammen. Zie [Creating a New Chart][helm-create-new-chart] (Een nieuw diagram maken) als u graag uw eigen diagrammen wilt maken die met OSBA werken.

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: https://docs.helm.sh/helm/#helm-install
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
