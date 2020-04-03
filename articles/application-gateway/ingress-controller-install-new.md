---
title: Een invallende controller maken met een nieuwe Application Gateway
description: In dit artikel vindt u informatie over het implementeren van een Application Gateway Ingress Controller met een nieuwe Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b46c9f8b0cad74f3a4e9be8903270a60993c01f4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585883"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Een Application Gateway Ingress Controller (AGIC) installeren met een nieuwe toepassingsgateway

De onderstaande instructies gaan ervan uit dat Application Gateway Ingress Controller (AGIC) wordt geïnstalleerd in een omgeving zonder reeds bestaande componenten.

## <a name="required-command-line-tools"></a>Vereiste opdrachtregelgereedschappen

We raden het gebruik van [Azure Cloud Shell](https://shell.azure.com/) aan voor alle opdrachten hieronder. Start je shell vanaf shell.azure.com of door op de link te klikken:

[![Start insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

Als alternatief start u Cloud Shell vanuit Azure-portal met het volgende pictogram:

![Portal lancering](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Uw [Azure Cloud Shell](https://shell.azure.com/) beschikt al over alle benodigde tools. Als u ervoor kiest om een andere omgeving te gebruiken, moet u ervoor zorgen dat de volgende opdrachtregelgereedschappen zijn geïnstalleerd:

* `az`- Azure CLI: [installatie-instructies](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Kubernetes command-line tool: [installatie-instructies](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Kubernetes package manager: [installatie-instructies](https://github.com/helm/helm/releases/latest)
* `jq`- command-line JSON-processor: [installatie-instructies](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Een identiteit maken

Volg de onderstaande stappen om een [AAD-servicehoofdobject](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)(Azure Active Directory) te maken . `appId`worden in de volgende stappen `password` `objectId` gebruikt.

1. Ad-serviceprincipal maken ([Lees meer over RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    De `appId` `password` waarden en waarden van de JSON-uitvoer worden gebruikt in de volgende stappen


1. Gebruik `appId` de uitvoer van de vorige `objectId` opdracht om de van de nieuwe serviceprincipal te krijgen:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    De uitvoer van `objectId`deze opdracht is , die wordt gebruikt in de azure resource manager-sjabloon hieronder

1. Maak het parameterbestand dat later wordt gebruikt in de azure resource manager-sjabloonimplementatie.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Als u een **RBAC-cluster** `aksEnabledRBAC` wilt implementeren, stelt u het veld in op`true`

## <a name="deploy-components"></a>Onderdelen implementeren
Met deze stap worden de volgende onderdelen aan uw abonnement toegevoegd:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Virtueel netwerk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) met 2 [subnetten](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Openbaar IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Managed Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), die zal worden gebruikt door [AAD Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Download de sjabloon Azure Resource Manager en wijzig de sjabloon indien nodig.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Implementeer de sjabloon Azure `az cli`Resource Manager met behulp van . Dit kan tot 5 minuten duren.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Zodra de implementatie is voltooid, downloadt `deployment-outputs.json`u de implementatieuitvoer naar een bestand met de naam .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Application Gateway Ingress-controller instellen

Met de instructies in de vorige sectie hebben we een nieuw AKS-cluster en een Application Gateway gemaakt en geconfigureerd. We zijn nu klaar om een voorbeeld-app en een ingress-controller te implementeren in onze nieuwe Kubernetes-infrastructuur.

### <a name="setup-kubernetes-credentials"></a>Kubernetes-referenties instellen
Voor de volgende stappen hebben we een [installatie-kubectl-opdracht](https://kubectl.docs.kubernetes.io/) nodig, die we zullen gebruiken om verbinding te maken met ons nieuwe Kubernetes-cluster. [Cloud](https://shell.azure.com/) Shell `kubectl` heeft al geïnstalleerd. We zullen `az` CLI gebruiken om referenties voor Kubernetes te verkrijgen.

Ontvang referenties voor uw nieuw geïmplementeerde AKS[(lees meer):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>AAD Pod-identiteit installeren
  Azure Active Directory Pod Identity biedt op token gebaseerde toegang tot [Azure Resource Manager (ARM).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

  [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) voegt de volgende componenten toe aan uw Kubernetes-cluster:
   * Kubernetes [CRD's](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
   * [MIC-component (Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * [Component Node Managed Identity (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Ga als het gaat om het installeren van AAD Pod Identity in uw cluster:

   - *RBAC ingeschakeld* AKS-cluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC uitgeschakeld* AKS-cluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Helm installeren
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) is package manager voor Kubernetes. We zullen het gebruiken `application-gateway-kubernetes-ingress` om het pakket te installeren:

1. Installeer [Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) en voer `application-gateway-kubernetes-ingress` het volgende uit om het helmpakket toe te voegen:

    - *RBAC ingeschakeld* AKS-cluster

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC uitgeschakeld* AKS-cluster

        ```bash
        helm init
        ```

1. Voeg de AGIC Helm repository toe:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Helmdiagram voor invallencontroller installeren

1. Gebruik `deployment-outputs.json` het bestand dat hierboven is gemaakt en maak de volgende variabelen.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Download helm-config.yaml, die AGIC zal configureren:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Of kopieer het YAML-bestand hieronder: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Bewerk de nieuw gedownloade helm-config.yaml `appgw` en `armAuth`vul de secties in en .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Waarden:
     - `verbosityLevel`: Hiermee stelt u het niveau van de agic-houtkap in. Zie [Logboekregistratieniveaus](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) voor mogelijke waarden.
     - `appgw.subscriptionId`: De Azure-abonnements-id waarin application gateway zich bevindt. Voorbeeld: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Naam van de Azure Resource Group waarin Application Gateway is gemaakt. Voorbeeld: `app-gw-resource-group`
     - `appgw.name`: Naam van de Application Gateway. Voorbeeld: `applicationgatewayd0f0`
     - `appgw.shared`: Deze booleaanse vlag `false`moet standaard worden ingesteld op . Stel `true` in op een [gedeelde toepassingsgateway.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)
     - `kubernetes.watchNamespace`: Geef de naamruimte op, waarop AGIC moet kijken. Dit kan een enkele tekenreekswaarde zijn of een door komma's gescheiden lijst met naamruimten.
    - `armAuth.type`: zou `aadPodIdentity` kunnen zijn of`servicePrincipal`
    - `armAuth.identityResourceID`: Resource-id van de Azure Managed Identity
    - `armAuth.identityClientId`: De client-id van de identiteit. Zie hieronder voor meer informatie over Identiteit
    - `armAuth.secretJSON`: Alleen nodig wanneer het Secret-type Van De Dienst wordt gekozen (wanneer `armAuth.type` is ingesteld op) `servicePrincipal` 


   > [!NOTE]
   > De `identityResourceID` `identityClientID` waarden en waarden die zijn gemaakt tijdens de stappen [Onderdelen implementeren](ingress-controller-install-new.md#deploy-components) en opnieuw kunnen worden verkregen met de volgende opdracht:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`in de bovenstaande opdracht is de brongroep van uw toepassingsgateway. `<identity-name>`is de naam van de gemaakte identiteit. Alle identiteiten voor een bepaald abonnement kunnen worden vermeld met:`az identity list`


1. Installeer het pakket ingress-controller van Application Gateway:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Een voorbeeld-app installeren
Nu we Application Gateway, AKS en AGIC hebben geïnstalleerd, kunnen we een voorbeeld-app installeren via [Azure Cloud Shell:](https://shell.azure.com/)

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

U ook:

* Download het YAML-bestand hierboven:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Pas het YAML-bestand toe:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Andere voorbeelden
Deze [handleiding](ingress-controller-expose-service-over-http-https.md) bevat meer voorbeelden over hoe je een AKS-service via HTTP of HTTPS blootstellen aan het internet met Application Gateway.
