---
title: (AFGESCHAFT) Service-principal voor Azure Kubernetes-cluster
description: Een service-principal voor Azure Active Directory voor een Kubernetes-cluster in Azure Container Service maken en beheren
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 40d4dc898efe6b719ec5e1f1ec0471a9677d3c95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371117"
---
# <a name="deprecated-set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>(AFGESCHAFT) Een service-principal voor Azure Active Directory voor een Kubernetes-cluster in Container Service instellen

> [!TIP]
> Voor de bijgewerkte versie van dit artikel, dat gebruikmaakt van Azure Kubernetes Service, raadpleegt u [Service-principals met AKS (Azure Kubernetes Service)](../../aks/kubernetes-service-principal.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

In Azure Container Service is voor een Kubernetes-cluster een [service-principal voor Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) vereist voor gebruik met Azure-API's. De Service-Principal is nodig om resources zoals door de [gebruiker gedefinieerde routes](../../virtual-network/virtual-networks-udr-overview.md) en de [laag 4-Azure Load Balancer](../../load-balancer/load-balancer-overview.md)dynamisch te beheren.


In dit artikel worden verschillende opties getoond om een service-principal in te stellen voor uw Kubernetes-cluster. Bijvoorbeeld: als u de [Azure CLI](/cli/azure/install-az-cli2) hebt geïnstalleerd en ingesteld, kunt u de opdracht [`az acs create`](/cli/azure/acs) uitvoeren om tegelijkertijd het Kubernetes-cluster en de service-principal te maken.


## <a name="requirements-for-the-service-principal"></a>Vereisten voor de service-principal

U kunt een bestaande Azure AD-service-principal gebruiken die voldoet aan de volgende vereisten, maar u kunt ook een nieuwe maken.

* **Bereik**: resourcegroep

* **Rol**: Inzender

* **Client geheim**: moet een wacht woord zijn. U kunt momenteel geen service-principal gebruiken om verificatie via een certificaat in te stellen.

> [!IMPORTANT]
> Als u een service-principal wilt maken, moet u beschikken over machtigingen voor het registreren van een toepassing bij uw Azure AD-tenant. U moet ook machtigingen hebben om de toepassing aan een rol toe te wijzen in uw abonnement. [Open de portal](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) om te controleren of u over de vereiste machtigingen beschikt.
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Optie 1: een service-principal maken in Azure AD

Azure biedt verschillende methoden om een Azure AD-service-principal te maken voordat u het Kubernetes-cluster implementeert.

In de volgende voorbeeldopdrachten ziet u hoe u dit kunt doen met de [Azure CLI](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md). U kunt ook een service-principal maken met behulp van [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md), de [klassieke portal](../../active-directory/develop/howto-create-service-principal-portal.md) of een ander hulpmiddel.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>"
```

De uitvoer ziet er ongeveer zo uit (dit voorbeeld is geredigeerd):

![Een service-principal maken](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Gemarkeerd zijn de **client-id** (`appId`) en het **client geheim** (`password`) dat u gebruikt als para meters voor de service-principal voor cluster implementatie.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Service-principal opgeven bij het maken van het Kubernetes-cluster

Geef bij het maken van het Kubernetes-cluster de **client-id** (ook wel de `appId` genoemd, voor toepassings-id) en het **clientgeheim** (`password`) als parameters op voor een bestaande service-principal. Zorg ervoor dat de service-principal voldoet aan de vereisten die aan het begin van dit artikel zijn vermeld.

U kunt deze parameters opgeven tijdens het implementeren van het Kubernetes-cluster via de [Azure CLI (Command-Line Interface)](container-service-kubernetes-walkthrough.md), via [Azure Portal](../dcos-swarm/container-service-deployment.md) of via een andere methode.

>[!TIP]
>Zorg ervoor dat u bij het opgeven van de **client-id** gebruikmaakt van de `appId` en niet de `ObjectId` van de service-principal.
>

In het volgende voorbeeld wordt één manier getoond om de parameters door te geven met behulp van de Azure CLI. In dit voorbeeld wordt gebruikgemaakt van de [Kubernetes-snelstartsjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Download](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) het bestand met sjabloonparameters `azuredeploy.parameters.json` van GitHub.

2. Voer de waarden voor `servicePrincipalClientId` en `servicePrincipalClientSecret` in het bestand in om de service-principal op te geven. (U moet ook uw eigen waarden voor `dnsNamePrefix` en `sshRSAPublicKey` opgeven. De laatste is de open bare SSH-sleutel voor toegang tot het cluster.) Sla het bestand op.

    ![Parameters voor de service-principal doorgeven](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Voer de volgende opdracht uit met behulp van `--parameters` om het pad naar het bestand azuredeploy.parameters.json in te stellen. Met deze opdracht wordt het cluster geïmplementeerd in een resourcegroep die u `myResourceGroup` hebt genoemd, in de regio VS - west.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Optie 2: een service-principal genereren tijdens het maken van het cluster met `az acs create`

Als u de [`az acs create`](/cli/azure/acs#az-acs-create) opdracht uitvoert voor het maken van het Kubernetes-cluster, hebt u de mogelijkheid om automatisch een service-principal te genereren.

Net zoals bij andere opties voor het maken van Kubernetes-clusters kunt u parameters voor een bestaande service-principal opgeven tijdens het uitvoeren van `az acs create`. Als u deze parameters echter weglaat, maakt de Azure CLI er automatisch een voor gebruik met Container Service. Dit vindt transparant plaats tijdens de implementatie.

Met de volgende opdracht wordt een Kubernetes-cluster gemaakt en worden beide SSH-sleutels en de referenties voor de service-principal gegenereerd:

```azurecli
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Als uw account niet beschikt over de Azure AD- en abonnementsmachtigingen voor het maken van een service-principal, wordt er bij de opdracht een fout gegenereerd die vergelijkbaar is met `Insufficient privileges to complete the operation.`
>

## <a name="additional-considerations"></a>Aanvullende overwegingen

* Als u geen machtigingen hebt om een service-principal te maken in uw abonnement, moet u mogelijk uw Azure AD- of abonnementsbeheerder vragen om de benodigde machtigingen toe te wijzen. U kunt ook de beheerder vragen om een service-principal voor gebruik met Azure Container Service.

* De service-principal voor Kubernetes is een onderdeel van de configuratie van het cluster. Gebruik echter niet de id voor het implementeren van het cluster.

* Elke service-principal is gekoppeld aan een Azure AD-toepassing. De service-principal voor een Kubernetes-cluster kan zijn gekoppeld aan elke geldige Azure AD-toepassingsnaam (bijvoorbeeld `https://www.contoso.org/example`). De URL van de toepassing hoeft geen echt eindpunt te zijn.

* Als u de **client-id** voor de service-principal opgeeft, kunt u de waarde van de `appId` gebruiken (zoals beschreven in dit artikel) of de bijbehorende service-principal `name` (bijvoorbeeld `https://www.contoso.org/example`).

* Op de hoofd- en agent-VM's in het Kubernetes-cluster worden de referenties voor de service-principal opgeslagen in het bestand `/etc/kubernetes/azure.json`.

* Als u de opdracht `az acs create` gebruikt om de service-principal automatisch te genereren, worden de referenties voor de service-principal naar het bestand `~/.azure/acsServicePrincipal.json` geschreven op de computer die wordt gebruikt om de opdracht uit te voeren.

* Als u de opdracht `az acs create` gebruikt om de service-principal automatisch te genereren, kan de service-principal ook worden geverifieerd bij een [Azure Container Registry](../../container-registry/container-registry-intro.md) dat in hetzelfde abonnement wordt gemaakt.

* Referenties voor service-principals kunnen verlopen, waardoor uw clusterknooppunten de status **NotReady** krijgen. Zie de sectie [Het verlopen van referenties](#credential-expiration) voor informatie waarmee u dit risico kunt beperken.

## <a name="credential-expiration"></a>Het verlopen van referenties

Tenzij u tijdens het maken van een service-principal een tijdvenster opgeeft waarbinnen u de geldigheid kunt aanpassen met de parameter `--years`, blijven de referenties ervan tot 1 jaar na de aanmaakdatum geldig. Wanneer de referenties verlopen, krijgen uw clusterknooppunten mogelijk de status **NotReady**.

Als u de vervaldatum van een service-principal wilt controleren, moet u de opdracht [az ad app show](/cli/azure/ad/app#az-ad-app-show) met de parameter `--debug` uitvoeren, en naar de waarde `endDate` van `passwordCredentials` in het onderste gedeelte van de uitvoer zoeken:

```azurecli
az ad app show --id <appId> --debug
```

De uitvoer (hier ingekort weergegeven):

```json
...
"passwordCredentials":[{"customKeyIdentifier":null,"endDate":"2018-11-20T23:29:49.316176Z"
...
```

Als de referenties voor uw service-principal zijn verlopen, gebruikt u de opdracht [az ad sp reset-credentials](/cli/azure/ad/sp) om de referenties bij te werken:

```azurecli
az ad sp reset-credentials --name <appId>
```

Uitvoer:

```json
{
  "appId": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "name": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "password": "404203c3-0000-0000-0000-d1d2956f3606",
  "tenant": "72f988bf-0000-0000-0000b-2d7cd011db47"
}
```

Werk vervolgens op alle clusterknooppunten `/etc/kubernetes/azure.json` bij met de nieuwe referenties en start de knooppunten opnieuw.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Kubernetes](container-service-kubernetes-walkthrough.md) in de Cluster Container Service-cluster.

* Zie de [ACS-enginedocumentatie](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting) voor informatie over het oplossen van problemen met de service-principal voor Kubernetes.
