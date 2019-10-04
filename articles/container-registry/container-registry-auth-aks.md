---
title: Verifiëren met Azure Container Registry van de Azure Kubernetes-service
description: Meer informatie over het bieden van toegang tot installatie kopieën in uw persoonlijke container register vanuit de Azure Kubernetes-service met behulp van een Azure Active Directory Service-Principal.
services: container-service
author: dlepow
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/27/2019
ms.author: danlep
ms.openlocfilehash: dc5276227913d2da6e52ee3c0fb493b98e86688a
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827767"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Verifiëren met Azure Container Registry van de Azure Kubernetes-service

Wanneer u Azure Container Registry (ACR) met Azure Kubernetes service (AKS) gebruikt, moet er een verificatie mechanisme tot stand worden gebracht. In dit artikel vindt u meer informatie over de aanbevolen configuraties voor verificatie tussen deze twee Azure-Services.

U hoeft slechts een van deze verificatie methoden te configureren. De meest voorkomende benadering is het [verlenen van toegang met behulp van de Service-Principal AKS](#grant-aks-access-to-acr). Als u specifieke vereisten hebt, kunt u desgewenst [toegang verlenen met Kubernetes geheimen](#access-with-kubernetes-secret).

In dit artikel wordt ervan uitgegaan dat u al een AKS-cluster hebt gemaakt en u toegang hebt tot het `kubectl` cluster met de opdracht regel-client. Als u in plaats daarvan een cluster wilt maken en de toegang tot een container register wilt configureren tijdens het maken van het cluster, raadpleegt u [Tutorial: Implementeer een AKS-cluster @ no__t-0 of [Verifieer met Azure container Registry van de Azure Kubernetes-service](../aks/cluster-container-registry-integration.md).

## <a name="grant-aks-access-to-acr"></a>AKS toegang verlenen tot ACR

Wanneer u een AKS-cluster maakt, maakt Azure ook een Service-Principal ter ondersteuning van cluster operability met andere Azure-resources. U kunt deze automatisch gegenereerde service-principal voor verificatie met een ACR-REGI ster gebruiken. Hiervoor moet u een Azure AD- [roltoewijzing](../role-based-access-control/overview.md#role-assignments) maken waarmee de service-principal van het cluster toegang krijgt tot het container register.

Gebruik het volgende script om de door de AKS gegenereerde Service-Principal toegang te verlenen tot een Azure container Registry. Wijzig de `AKS_*` variabelen `ACR_*` en voor uw omgeving voordat u het script uitvoert.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Toegang met Kubernetes-geheim

In sommige gevallen is het mogelijk dat u de vereiste rol niet kunt toewijzen aan de automatisch gegenereerde Service-Principal AKS die toegang verleent aan ACR. Als gevolg van het beveiligings model van uw organisatie hebt u bijvoorbeeld mogelijk niet voldoende machtigingen in uw Azure Active Directory-Tenant om een rol toe te wijzen aan de door AKS gegenereerde Service-Principal. Voor het toewijzen van een rol aan een Service-Principal moet uw Azure AD-account schrijf machtigingen hebben voor uw Azure AD-Tenant. Als u niet gemachtigd bent, kunt u een nieuwe service-principal maken en deze vervolgens toegang verlenen tot het container register met behulp van een Kubernetes-installatie kopie pull Secret.

Gebruik het volgende script om een nieuwe service-principal te maken (u gebruikt de referenties voor het Kubernetes-installatie kopie pull Secret). Wijzig de `ACR_NAME` variabele voor uw omgeving voordat u het script uitvoert.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name http://$SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

U kunt de referenties van de Service-Principal nu opslaan in een Kubernetes- [pull-geheim][image-pull-secret], waarnaar uw AKS-cluster verwijst wanneer containers worden uitgevoerd.

Gebruik de volgende **kubectl** -opdracht om het Kubernetes-geheim te maken. Vervang `<acr-login-server>` door de volledig gekwalificeerde naam van uw Azure container Registry (in de indeling ' acrname.azurecr.io '). Vervang `<service-principal-ID>` en`<service-principal-password>` door de waarden die u hebt verkregen door het vorige script uit te voeren. Vervang `<email-address>` door een goed gevormd e-mail adres.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

U kunt nu het Kubernetes-geheim in pod-implementaties gebruiken door de naam (in dit geval "ACR-auth") op te `imagePullSecrets` geven in de para meter:

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
