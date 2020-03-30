---
title: Een ingress-controller maken met een bestaande toepassingsgateway
description: In dit artikel vindt u informatie over het implementeren van een Application Gateway Ingress Controller met een bestaande Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 048ab7249b27839890bab3e677154ca3c7a0cc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239424"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Een Application Gateway Ingress Controller (AGIC) installeren met behulp van een bestaande Application Gateway

De Application Gateway Ingress Controller (AGIC) is een pod binnen uw Kubernetes-cluster.
AGIC bewaakt de Kubernetes [Ingress-bronnen](https://kubernetes.io/docs/concepts/services-networking/ingress/) en maakt en past Application Gateway config toe op basis van de status van het Kubernetes-cluster.

## <a name="outline"></a>Overzicht:
- [Vereisten](#prerequisites)
- [Azure Resource Manager-verificatie (ARM)](#azure-resource-manager-authentication)
    - Optie 1: [Aad-pod-identity instellen](#set-up-aad-pod-identity) en Azure Identity maken op ARMs
    - Optie 2: [Een serviceprincipal gebruiken](#using-a-service-principal)
- [Ingress Controller installeren met Helm](#install-ingress-controller-as-a-helm-chart)
- [Multi-cluster / Shared Application Gateway](#multi-cluster--shared-application-gateway): Installeer AGIC in een omgeving, waar Application Gateway wordt gedeeld tussen een of meer AKS-clusters en/of andere Azure-componenten.

## <a name="prerequisites"></a>Vereisten
In dit document wordt ervan uitgegaan dat u al de volgende hulpprogramma's en infrastructuur hebt geïnstalleerd:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) met [geavanceerde netwerken](https://docs.microsoft.com/azure/aks/configure-azure-cni) ingeschakeld
- [Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) in hetzelfde virtuele netwerk als AKS
- [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) geïnstalleerd op uw AKS-cluster
- [Cloud Shell](https://shell.azure.com/) is de Azure `az` shell-omgeving, die CLI heeft en `kubectl` `helm` geïnstalleerd. Deze tools zijn vereist voor de onderstaande opdrachten.

Maak __een back-up van de configuratie van uw Application Gateway__ voordat u AGIC installeert:
  1. [Azure-portal](https://portal.azure.com/) gebruiken `Application Gateway` om naar uw exemplaar te navigeren
  2. van `Export template` klik`Download`

Het zip-bestand dat u hebt gedownload, heeft JSON-sjablonen, bash- en PowerShell-scripts die u gebruiken om App Gateway te herstellen als dat nodig is

## <a name="install-helm"></a>Helm installeren
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) is package manager voor Kubernetes. We zullen het gebruiken `application-gateway-kubernetes-ingress` om het pakket te installeren.
Gebruik [Cloud Shell](https://shell.azure.com/) om Helm te installeren:

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

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager-verificatie

AGIC communiceert met de Kubernetes API-server en de Azure Resource Manager. Het vereist een identiteit om toegang te krijgen tot deze API's.

## <a name="set-up-aad-pod-identity"></a>AAD Pod-identiteit instellen

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) is een controller, vergelijkbaar met AGIC, die ook op uw AKS draait. Het bindt Azure Active Directory-identiteiten aan uw Kubernetes-pods. Identiteit is vereist voor een toepassing in een Kubernetes-pod om te kunnen communiceren met andere Azure-componenten. In het specifieke geval hier, hebben we toestemming nodig voor de AGIC pod om HTTP-verzoeken te maken aan [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Volg de [installatie-instructies](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) van AAD Pod Identity om dit onderdeel aan uw AKS toe te voegen.

Vervolgens moeten we een Azure-identiteit maken en deze machtigingen GEVEN.
Gebruik [Cloud Shell](https://shell.azure.com/) om alle volgende opdrachten uit te voeren en een identiteit te maken:

1. Maak een Azure-identiteit **in dezelfde brongroep als de AKS-knooppunten**. Het kiezen van de juiste resourcegroep is belangrijk. De resourcegroep die vereist is in de onderstaande opdracht is *niet* de groep waarnaar wordt verwezen in het AKS-portaalvenster. Dit is de resourcegroep van de `aks-agentpool` virtuele machines. Meestal begint die resourcegroep met `MC_` en bevat de naam van uw AKS. Bijvoorbeeld:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Voor de onderstaande opdrachtopdrachten voor `principalId` roltoewijzingdie we moeten verkrijgen voor de nieuw gemaakte identiteit:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Geef de `Contributor` identiteit toegang tot uw Application Gateway. Hiervoor heb je de ID van de Application Gateway nodig, die er ongeveer zo uitziet:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Download de lijst met toepassingsgateway-------iD's in uw abonnement met:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Geef de `Reader` identiteit toegang tot de brongroep Application Gateway. De resourcegroep-id ziet `/subscriptions/A/resourceGroups/B`eruit als: . U alle resourcegroepen krijgen met:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Een serviceprincipal gebruiken
Het is ook mogelijk om AGIC toegang te bieden tot ARM via een Kubernetes geheim.

1. Maak een Active Directory Service Principal en codeer met base64. De base64-codering is vereist om de JSON-blob op te slaan in Kubernetes.

```azurecli
az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0
```

2. Voeg de gecodeerde JSON-blob van Base64 toe aan het `helm-config.yaml` bestand. Meer informatie `helm-config.yaml` over is in de volgende sectie.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Ingress Controller installeren als helmdiagram
In de eerste stappen installeren we Helm's Tiller op je Kubernetes-cluster. Gebruik [Cloud Shell](https://shell.azure.com/) om het AGIC Helm-pakket te installeren:

1. Voeg `application-gateway-kubernetes-ingress` de helmrepo toe en voer een helmupdate uit

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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

1. Bewerk helm-config.yaml en vul `appgw` de `armAuth`waarden in voor en .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > De `<identity-resource-id>` `<identity-client-id>` eigenschappen van de Azure AD-identiteit die u in de vorige sectie hebt ingesteld. U deze informatie ophalen door `az identity show -g <resourcegroup> -n <identity-name>`de `<resourcegroup>` volgende opdracht uit te voeren:, waar is de brongroep waarin het AKS-clusterobject op het hoogste niveau, Application Gateway en Managed Identify zijn geïmplementeerd.

1. Helmdiagram `application-gateway-kubernetes-ingress` installeren `helm-config.yaml` met de configuratie van de vorige stap

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    U de `helm-config.yaml` opdracht en het roer in één stap combineren:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Controleer het logboek van de nieuw gemaakte pod om te controleren of deze goed is gestart

Raadpleeg deze handleiding om te begrijpen hoe u een [AKS-service](ingress-controller-expose-service-over-http-https.md) via HTTP of HTTPS blootstellen aan het internet, met behulp van een Azure Application Gateway.



## <a name="multi-cluster--shared-application-gateway"></a>Cluster - / gedeelde toepassingsgateway
Standaard neemt AGIC de volledige eigendom van de Application Gateway aan. AGIC-versie 0.8.0 en hoger kan één toepassingsgateway delen met andere Azure-componenten. We kunnen bijvoorbeeld dezelfde Application Gateway gebruiken voor een app die wordt gehost op virtuele machineschaalset en een AKS-cluster.

Maak __een back-up van de configuratie van de Toepassingsgateway__ voordat u deze instelling inschakelt:
  1. [Azure-portal](https://portal.azure.com/) gebruiken `Application Gateway` om naar uw exemplaar te navigeren
  2. van `Export template` klik`Download`

Het zip-bestand dat u hebt gedownload, heeft JSON-sjablonen, bash- en PowerShell-scripts die u gebruiken om Application Gateway te herstellen

### <a name="example-scenario"></a>Voorbeeldscenario
Laten we eens kijken naar een denkbeeldige Application Gateway, die verkeer voor twee websites beheert:
  - `dev.contoso.com`- gehost op een nieuwe AKS, met behulp van Application Gateway en AGIC
  - `prod.contoso.com`- gehost op een [Azure Virtual Machine Scale Set](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Met standaardinstellingen neemt AGIC 100% eigenaar van de Application Gateway waarnaar wordt gewezen. AGIC overschrijft alle configuratie van App Gateway. Als we handmatig een listener `prod.contoso.com` voor (op Application Gateway) zouden maken, zonder deze te `prod.contoso.com` definiëren in de Kubernetes Ingress, zal AGIC de config binnen enkele seconden verwijderen.

Om AGIC te `prod.contoso.com` installeren en ook te bedienen vanaf onze Virtual `dev.contoso.com` Machine Scale Set-machines, moeten we AGIC beperken tot alleen configureren. Dit wordt vergemakkelijkt door de volgende [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)te instantiën:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Met de opdracht `AzureIngressProhibitedTarget` hierboven wordt een object gemaakt. Dit maakt AGIC (versie 0.8.0 en hoger) bewust van `prod.contoso.com` het bestaan van Application Gateway config voor en uitdrukkelijk instrueert het om te voorkomen dat een configuratie met betrekking tot die hostname.


### <a name="enable-with-new-agic-installation"></a>Inschakelen met nieuwe AGIC-installatie
Als u AGIC (versie 0.8.0 en hoger) wilt beperken `helm-config.yaml` tot een subset van de configuratie van de Application Gateway, wijzigt u de sjabloon.
Voeg `appgw:` onder de `shared` sectie de toets `true`toe en stel deze in op .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Pas de wijzigingen van het roer toe:
  1. Zorg `AzureIngressProhibitedTarget` ervoor dat de CRD is geïnstalleerd met:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Update Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Als gevolg hiervan heeft uw AKS `AzureIngressProhibitedTarget` `prohibit-all-targets`een nieuw exemplaar van de naam:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Het `prohibit-all-targets`object , zoals de naam al aangeeft, verbiedt AGIC van het veranderen van config voor *een* host en pad.
Helm installeren `appgw.shared=true` met zal implementeren AGIC, maar zal geen wijzigingen aanbrengen in Application Gateway.


### <a name="broaden-permissions"></a>Machtigingen verbreden
Aangezien Helm `appgw.shared=true` met `prohibit-all-targets` en de standaard blokkeert AGIC van het toepassen van een config.

Verbreed AGIC-machtigingen met:
1. Maak een `AzureIngressProhibitedTarget` nieuwe met uw specifieke setup:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Pas nadat u uw eigen aangepaste verbod hebt gemaakt, u de standaardverbod verwijderen, die te breed is:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Inschakelen voor een bestaande AGIC-installatie
Laten we aannemen dat we al een werkende AKS, Application Gateway en geconfigureerde AGIC in ons cluster hebben. We hebben een `prod.contosor.com` Ingress voor en zijn met succes het dienen van het verkeer voor het van AKS. We willen `staging.contoso.com` toevoegen aan onze bestaande Application Gateway, maar moeten deze hosten op een [VM.](https://azure.microsoft.com/services/virtual-machines/) We gaan de bestaande Application Gateway opnieuw gebruiken en handmatig `staging.contoso.com`een listener- en backendpools configureren voor . Maar handmatig tweaken Application Gateway config (via [portal,](https://portal.azure.com) [ARM API's](https://docs.microsoft.com/rest/api/resources/) of [Terraform)](https://www.terraform.io/)zou in strijd zijn met agic's veronderstellingen van volledige eigendom. Kort nadat we wijzigingen hebben toegepast, zal AGIC ze overschrijven of verwijderen.

We kunnen AGIC verbieden wijzigingen aan te brengen in een subset van configuratie.

1. Een `AzureIngressProhibitedTarget` object maken:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Het nieuw gemaakte object weergeven:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Wijzigen Application Gateway config via portal - voeg luisteraars, routing regels, backends etc. Het nieuwe object`manually-configured-staging-environment`dat we hebben gemaakt ( ) zal `staging.contoso.com`agic verbieden om Application Gateway-configuratie te overschrijven met betrekking tot .
