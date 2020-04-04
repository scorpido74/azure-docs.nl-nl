---
title: OpenFaaS gebruiken met Azure Kubernetes Service (AKS)
description: Meer informatie over het implementeren en gebruiken van OpenFaaS op een AKS-cluster (Azure Kubernetes Service) om serverloze functies met containers te bouwen.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 95039573c607f516755f08f1ebad8b968416ec8b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631476"
---
# <a name="using-openfaas-on-aks"></a>OpenFaaS gebruiken op AKS

[OpenFaaS][open-faas] is een framework voor het bouwen van serverloze functies door het gebruik van containers. Als een open source project, heeft het opgedaan grootschalige adoptie binnen de gemeenschap. In dit document wordt informatie gegeven over het installeren en gebruiken van OpenFaas op een AKS-cluster (Azure Kubernetes Service).

## <a name="prerequisites"></a>Vereisten

Om de stappen in dit artikel te voltooien, heb je het volgende nodig.

* Basiskennis van Kubernetes.
* Een AZURE Kubernetes Service (AKS) cluster en AKS-referenties die zijn geconfigureerd op uw ontwikkelsysteem.
* Azure CLI geïnstalleerd op uw ontwikkelingssysteem.
* Git command-line tools geïnstalleerd op uw systeem.

## <a name="add-the-openfaas-helm-chart-repo"></a>De repo van de OpenFaaS-helmdiagram toevoegen

Ga [https://shell.azure.com](https://shell.azure.com) naar Azure Cloud Shell openen in uw browser.

OpenFaaS onderhoudt zijn eigen helmdiagrammen om op de hoogte te blijven van de laatste veranderingen.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>OpenFaaS implementeren

Als een goede praktijk, OpenFaaS en OpenFaaS functies moeten worden opgeslagen in hun eigen Kubernetes namespace.

Maak een naamruimte voor het OpenFaaS-systeem en de functies:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Een wachtwoord genereren voor de OpenFaaS UI Portal en REST API:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Je de waarde van `echo $PASSWORD`het geheim krijgen met.

Het wachtwoord dat we hier maken, wordt gebruikt door de helmgrafiek om basisverificatie in te schakelen op de OpenFaaS Gateway, die wordt blootgesteld aan het internet via een cloud LoadBalancer.

Een Helm-diagram voor OpenFaaS is opgenomen in de gekloonde repository. Gebruik deze grafiek om OpenFaaS in uw AKS-cluster te implementeren.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Uitvoer:

```output
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Er wordt een openbaar IP-adres gemaakt voor toegang tot de OpenFaaS-gateway. Als u dit IP-adres wilt ophalen, gebruikt u de opdracht [kubectl get service.][kubectl-get] Het kan even duren voordat het IP-adres aan de service is toegewezen.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Output.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Als u het OpenFaaS-systeem wilt testen, bladert u `http://52.186.64.52:8080` in dit voorbeeld naar het externe IP-adres op poort 8080. U wordt gevraagd in te loggen. Voer . `echo $PASSWORD`

![OpenFaaS-gebruikersinterface](media/container-service-serverless/openfaas.png)

Installeer ten slotte de OpenFaaS CLI. In dit voorbeeld wordt de [OpenFaaS CLI-documentatie][open-faas-cli] voor meer opties gebruikt.

```console
brew install faas-cli
```

Ingesteld `$OPENFAAS_URL` op het openbare IP hierboven gevonden.

Meld u aan bij azure cli:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Eerste functie maken

Nu OpenFaaS operationeel is, maakt u een functie met behulp van de OpenFaas-portal.

Klik op **Nieuwe functie implementeren** en zoek naar **Figlet**. Selecteer de functie Figlet en klik op **Implementeren**.

![Figlet](media/container-service-serverless/figlet.png)

Gebruik krul om de functie aan te roepen. Vervang het IP-adres in het volgende voorbeeld door dat van uw OpenFaas-gateway.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Uitvoer:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Tweede functie maken

Maak nu een tweede functie. Dit voorbeeld wordt geïmplementeerd met de OpenFaaS CLI en bevat een aangepaste containerafbeelding en het ophalen van gegevens uit een Cosmos DB. Er moeten verschillende items worden geconfigureerd voordat u de functie maakt.

Maak eerst een nieuwe brongroep voor de Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Een Voorbeeld van CosmosDB implementeren. `MongoDB` De instantie heeft een `openfaas-cosmos` unieke naam nodig, update naar iets unieks voor uw omgeving.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Haal de cosmos-databaseverbindingstekenreeks op en sla deze op in een variabele.

Werk de waarde `--resource-group` voor het argument bij aan `--name` de naam van uw resourcegroep en het argument naar de naam van uw Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Bevolk nu de Cosmos DB met testgegevens. Maak een `plans.json` bestand met de naam en kopie in de volgende json.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Gebruik het *mongoimport-gereedschap* om de instantie CosmosDB met gegevens te laden.

Installeer indien nodig de MongoDB-tools. In het volgende voorbeeld worden deze tools geïnstalleerd met behulp van brouwsel, zie de [MongoDB-documentatie][install-mongo] voor andere opties.

```console
brew install mongodb
```

Laad de gegevens in de database.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Uitvoer:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Voer de volgende opdracht uit om de functie te maken. Werk de waarde `-g` van het argument bij met uw OpenFaaS-gatewayadres.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Nadat u is geïmplementeerd, ziet u het nieuw gemaakte OpenFaaS-eindpunt voor de functie.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Test de functie met behulp van krul. Werk het IP-adres bij met het OpenFaaS-gatewayadres.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Uitvoer:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

U de functie ook testen binnen de OpenFaaS UI.

![alternatieve tekst](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Volgende stappen

U blijven leren met de OpenFaaS-workshop via een reeks hands-on labs die onderwerpen behandelen zoals het maken van uw eigen GitHub-bot, het consumeren van geheimen, het bekijken van statistieken en automatisch schalen.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
