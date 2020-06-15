---
title: Azure Key Vault integreren met Kubernetes
description: In deze zelfstudie gaat u geheimen openen en ophalen uit Azure Key Vault met behulp van het stuurprogramma Secrets Store CSI, om deze vervolgens te koppelen aan Kubernetes-pods.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636932"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Zelfstudie: De Azure Key Vault-provider voor het stuurprogramma voor het CSI voor het archief voor geheimen configureren en uitvoeren op Kubernetes

In deze zelfstudie gaat u geheimen openen en ophalen uit Azure Key Vault met behulp van het stuurprogramma Secrets Store CSI, om deze vervolgens te koppelen aan Kubernetes-pods.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een service-principal maken
> * Een Azure Kubernetes Service-cluster implementeren
> * Helm en het stuurprogramma Secrets Store CSI installeren
> * Een Azure Key Vault maken en geheimen instellen
> * Uw eigen SecretProviderClass-object maken
> * Uw pod met gekoppelde geheimen van Key Vault implementeren

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voordat u met deze zelfstudie begint, moet u [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) installeren.

## <a name="create-a-service-principal-or-use-managed-identities"></a>Een service-principal maken of beheerde identiteiten gebruiken

Als u beheerde identiteiten wilt gebruiken, kunt u doorgaan naar de volgende sectie.

Maak een service-principal om te bepalen welke resources vanuit uw Azure Key Vault toegankelijk zijn. De toegang van deze service-principal wordt beperkt door de rollen die eraan toegewezen zijn. Met deze functie bepaalt u hoe de service-principal uw geheimen kan beheren. In het onderstaande voorbeeld is de naam van de service-principal **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Met deze bewerking wordt een reeks sleutel-waardeparen geretourneerd:

![Installatiekopie](../media/kubernetes-key-vault-1.png)

Kopieer de **app-id** en het **wachtwoord**. U hebt deze referenties later nodig.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Een Azure Kubernetes Service-cluster implementeren met behulp van Azure CLI

U hoeft Azure Cloud Shell niet te gebruiken. Als Azure CLI is geïnstalleerd, kunt u ook de opdrachtprompt (terminal) gebruiken. 

Volg de instructies in deze [gids](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) en voltooi de volgende secties: **Een resourcegroep maken**, **Een AKS-cluster maken** en **Verbinding maken met het cluster**.

**Opmerking:** Als u van plan bent een pod-id te gebruiken in plaats van een service-principal, geldt het volgende. U moet deze inschakelen bij het maken van het Kubernetes-cluster, zoals hieronder wordt weergegeven:

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Stel de omgevingsvariabele PATH in](https://www.java.com/en/download/help/path.xml) op het gedownloade bestand “kubectl.exe”.
1. Controleer uw Kubernetes-versie met behulp van de onderstaande opdracht. Met deze opdracht worden de client- en serverversie weergegeven. De clientversie is de versie van “kubectl.exe” die u hebt geïnstalleerd en de serverversie is de versie van Azure Kubernetes Service waarop uw cluster wordt uitgevoerd.
    ```azurecli
    kubectl version
    ```
1. Uw Kubernetes-versie moet **v1.16.0** of hoger zijn. Met deze opdracht wordt zowel het Kubernetes-cluster als de knooppuntgroep bijgewerkt. Het kan een paar minuten duren voordat de opdracht is uitgevoerd. In dit voorbeeld is de resourcegroep **contosoResourceGroup** en is het Kubernetes-cluster **contosoAKSCluster**.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Geef de metagegevens van het door u gemaakte AKS-cluster weer met behulp van de onderstaande opdracht. Kopieer de waarden van **principalId**, **clientId**, **subscriptionId** en **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Dit is de uitvoer, waarbij beide parameters zijn gemarkeerd.
    
    ![Afbeelding](../media/kubernetes-key-vault-5.png) ![Afbeelding](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Helm en het stuurprogramma Secrets Store CSI installeren

U moet [Helm](https://helm.sh/docs/intro/install/) installeren om het stuurprogramma Secrets Store CSI te kunnen installeren.

Met de stuurprogramma-interface van [Secrets Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) kunt u geheime inhoud ontvangen die is opgeslagen in een Azure Key Vault-exemplaar en de stuurprogramma-interface gebruiken om die geheime inhoud te koppelen in Kubernetes-pods.

1. Controleer de versie van Helm v3 of hoger is:
    ```azurecli
    helm version
    ```
1. Installeer het stuurprogramma Secrets Store CSI en de Azure Key Vault-provider voor het stuurprogramma:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Een Azure Key Vault maken en geheimen instellen

Volg deze [gids](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) om uw eigen Key Vault (sleutelkluis) te maken en uw geheimen in te stellen.

**Opmerking:** U hoeft niet Azure Cloud Shell te gebruiken of een nieuwe resourcegroep te maken. U kunt ook de resourcegroep gebruiken die u eerder hebt gemaakt voor het Kubernetes-cluster.

## <a name="create-your-own-secretproviderclass-object"></a>Uw eigen SecretProviderClass-object maken

Gebruik deze [sjabloon](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml) die wordt geboden om uw eigen aangepaste SecretProviderClass-object te maken voor providerspecifieke parameters voor het stuurprogramma Secrets Store CSI. Dit object biedt identiteit- en toegangsbeheer voor uw Key Vault.

Gebruik hiervoor het geleverde YAML-voorbeeldbestand voor SecretProviderClass. U gaat de ontbrekende parameters invullen. De volgende parameters zijn vereist:

1.  **userAssignedIdentityID:** Client-id van service-principal
1.  **KeyVaultName:** Naam van Key Vault
1.  **objects:** Dit object bevat alle geheime inhoud die u wilt koppelen
    1.  **objectName:** Naam van geheime inhoud
    1.  **objectType:** Objecttype (geheim, sleutel, certificaat)
1.  **resourceGroup:** Naam van resourcegroep
1.  **subscriptionId:** Abonnements-id van de Key Vault
1.  **tenantID:** Tenant-id (dat wil zeggen, map-id) van de Key Vault

Hieronder ziet u de bijgewerkte sjabloon. Download deze als een YAML-bestand en vult u de bijbehorende verplichte velden in. In dit voorbeeld is de Key Vault **contosoKeyVault5** en heeft deze twee geheimen: **secret1** en **secret2**.

**Opmerking:** Als u beheerde identiteiten gebruikt, moet het veld **usePodIdentity** zijn ingesteld op **true** en mag het veld **userAssignedIdentityID** alleen aanhalingstekens **""** bevatten. 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
Hieronder ziet u de uitvoer van de console voor "az keyvault show - name contosoKeyVault5" met de relevante gemarkeerde metagegevens:

![Installatiekopie](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Uw service-principal toewijzen of beheerde identiteiten gebruiken

### <a name="using-service-principal"></a>Service-principal gebruiken

Als u een service-principal gebruikt, geldt het volgende. U moet uw service-principal toestemming geven om toegang te krijgen tot uw Key Vault en geheimen op te halen. Wijs de rol **"Lezer"** toe en geef de service-principal **"get"** -toestemming om geheimen op te halen uit uw Key Vault door de volgende stappen uit te voeren:

1. Service-principal toewijzen aan bestaande Key Vault. De parameter **$AZURE_CLIENT_ID** is de **appId** die u hebt gekopieerd na het maken van uw service-principal.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Hieronder ziet u de uitvoer van de opdracht: 

    ![Installatiekopie](../media/kubernetes-key-vault-3.png)

1. Service-principal machtigen voor het ophalen van geheimen:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. U hebt nu de service-principal geconfigureerd met machtigingen voor het lezen van geheimen uit uw Key Vault. Het **$AZURE_CLIENT_SECRET** is het **wachtwoord** van uw service-principal. Uw service-principal-referenties toevoegen als een Kubernetes-geheim dat toegankelijk is voor het stuurprogramma Secrets Store CSI:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Opmerking:** Als u later een foutbericht over een ongeldige clientgeheim-id ontvangt bij het implementeren van de Kubernetes-pod, geldt het volgende. Mogelijk hebt u een oudere clientgeheim-id die is verlopen of opnieuw is ingesteld. U kunt dit probleem oplossen door uw geheimen ("secrets-store-creds") te verwijderen en een nieuw geheim te maken met de huidige clientgeheim-id. Voer de onderstaande opdracht uit om uw “secrets-store-creds” te verwijderen:
```azurecli
kubectl delete secrets secrets-store-creds
```

Als u de clientgeheim-id van uw service-principal vergeten bent, kunt u deze opnieuw instellen met de volgende opdracht:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Beheerde identiteiten gebruiken

Als u beheerde identiteiten gebruikt, wijst u specifieke rollen toe aan het AKS-cluster dat u hebt gemaakt. 
1. Als u een door de gebruiker toegewezen beheerde identiteit wilt maken/vermelden of lezen, moet aan uw AKS-cluster de rol [Inzender beheerde identiteit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor) worden toegewezen. Zorg ervoor dat **$clientId** de client-id van het Kubernetes-cluster is.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Installeer de Azure Active Directory-identiteit (Azure AD) in AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Maak een Azure Active Directory-identiteit. Kopieer de **clientId** en **principalId**.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Wijs de rol van lezer toe aan de Azure AD-identiteit die u zojuist hebt gemaakt voor uw Key Vault. Machtig de identiteit vervolgens om geheimen uit uw Key Vault op te halen. U gaat de **clientId** en **principalId** van de Azure-identiteit die u zojuist hebt gemaakt, gebruiken.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Uw pod met gekoppelde geheimen van Key Vault implementeren

Met de onderstaande opdracht wordt uw SecretProviderClass-object geconfigureerd:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Service-principal gebruiken

Als u een service-principal gebruikt, geldt het volgende. Met de onderstaande opdracht worden uw Kubernetes-pods geïmplementeerd met de SecretProviderClass en secrets-store-creds die u hebt geconfigureerd. Hier volgt de sjabloon voor een [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml)- of [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)-implementatie.
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Beheerde identiteiten gebruiken

Als u beheerde identiteiten gebruikt, geldt het volgende. U maakt een **AzureIdentity** in uw cluster die verwijst naar de id die u eerder hebt gemaakt. Maak vervolgens een **AzureIdentityBinding-** die verwijst naar de **AzureIdentity** die u hebt gemaakt. Gebruik de onderstaande sjabloon, vul de bijbehorende parameters in en sla deze op als **podIdentityAndBinding.yaml**.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Voer de volgende opdracht uit om de binding uit te voeren:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Nu volgt de daadwerkelijke implementatie van de pod. Hieronder ziet u het YAML-implementatiebestand dat gebruikmaakt van de pod-id-binding uit de laatste stap. Sla dit bestand op als **podBindingDeployment.yaml**.

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

Voer de volgende opdracht uit om de pod te implementeren:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>Status en geheime inhoud controleren 
De pods weergeven die u hebt geïmplementeerd:
```azurecli
kubectl get pods
```

Gebruik de volgende opdracht om de status van uw pod te controleren:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Installatiekopie](../media/kubernetes-key-vault-4.png)

De geïmplementeerde pod moet de status Wordt uitgevoerd hebben. In de sectie Gebeurtenissen onderaan worden alle typen gebeurtenissen links geclassificeerd als Normaal.
Zodra u hebt gecontroleerd of de pod wordt uitgevoerd, kunt u controleren of uw pod de geheimen van uw Key Vault bevat.

Alle geheimen weergeven die de pod bevat:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Inhoud uit een specifiek geheim ophalen:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Controleer de inhoud van het geheim dat wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

Ervoor zorgen dat de Key Vault kan worden hersteld:
> [!div class="nextstepaction"]
> [Voorlopig verwijderen inschakelen](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
