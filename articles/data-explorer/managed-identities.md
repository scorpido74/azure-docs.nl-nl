---
title: Beheerde identiteiten configureren voor Azure Data Explorer cluster
description: Meer informatie over het configureren van beheerde identiteiten voor Azure Data Explorer cluster.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3b0bb62de4a96a45d607e05b32a87feec692e4d4
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725973"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Beheerde identiteiten voor uw Azure Data Explorer-cluster configureren

Met een [beheerde identiteit van Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) kan uw cluster eenvoudig toegang krijgen tot andere met Aad beveiligde resources zoals Azure Key Vault. De identiteit wordt beheerd door het Azure-platform en u hoeft geen geheimen in te richten of te draaien. In dit artikel wordt beschreven hoe u een beheerde identiteit voor Azure Data Explorer-clusters maakt. 

> [!Note]
> Beheerde identiteiten voor Azure Data Explorer werken niet zoals verwacht als uw app wordt gemigreerd tussen abonnementen of tenants. De app moet een nieuwe identiteit verkrijgen, die kan worden gedaan door het uitschakelen en opnieuw inschakelen van de functie met [een identiteit verwijderen](#remove-an-identity). Het toegangs beleid van downstream-resources moet ook worden bijgewerkt om de nieuwe identiteit te kunnen gebruiken.

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Aan uw cluster kan een door het **systeem toegewezen identiteit** worden toegewezen die is gekoppeld aan uw cluster en wordt verwijderd als uw cluster wordt verwijderd. Een cluster kan slechts één door het systeem toegewezen identiteit hebben. Voor het maken van een cluster met een door het systeem toegewezen identiteit moet er een extra eigenschap worden ingesteld op het cluster.

### <a name="add-a-system-assigned-identity-using-c"></a>Een door het systeem toegewezen identiteit toevoegen metC#

Ga als volgt te werk om een beheerde identiteit in C# te stellen met behulp van de Azure Data Explorer-client:

* Installeer het [Azure Data Explorer (Kusto) NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installeer het [NuGet-pakket micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) voor verificatie.
* Als u het volgende voor beeld wilt uitvoeren, [maakt u een Azure AD-toepassing](/azure/active-directory/develop/howto-create-service-principal-portal) en service-principal die toegang hebben tot resources. U kunt roltoewijzing toevoegen aan het abonnements bereik en de vereiste `Directory (tenant) ID`, `Application ID`en `Client Secret`ophalen.

#### <a name="create-or-update-your-cluster"></a>Uw cluster maken of bijwerken

1. Uw cluster maken of bijwerken met behulp van de eigenschap `Identity`:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Voer de volgende opdracht uit om te controleren of het cluster is gemaakt of bijgewerkt met een identiteit:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Als het resultaat `ProvisioningState` met de `Succeeded`-waarde bevat, is het cluster gemaakt of bijgewerkt en moet het de volgende eigenschappen hebben:
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` en `TenantId` worden vervangen door GUID'S. De eigenschap `TenantId` identificeert de AAD-Tenant waartoe de identiteit behoort. De `PrincipalId` is een unieke id voor de nieuwe identiteit van het cluster. Binnen AAD heeft de service-principal dezelfde naam die u hebt gegeven aan uw App Service-of Azure Functions-exemplaar.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Een door het systeem toegewezen identiteit toevoegen met behulp van een Azure Resource Manager sjabloon

Een Azure Resource Manager sjabloon kan worden gebruikt voor het automatiseren van de implementatie van uw Azure-resources. Zie een [Azure Data Explorer-cluster en-data base maken met behulp van een Azure Resource Manager-sjabloon](create-cluster-database-resource-manager.md)voor meer informatie over het implementeren van Azure Data Explorer.

Door het door het systeem toegewezen type toe te voegen, vertelt Azure om de identiteit voor uw cluster te maken en te beheren. Een resource van het type `Microsoft.Kusto/clusters` kan worden gemaakt met een identiteit door de volgende eigenschap op te nemen in de resource definitie: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Bijvoorbeeld:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

Wanneer het cluster is gemaakt, heeft het de volgende aanvullende eigenschappen:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` en `<PRINCIPALID>` worden vervangen door GUID'S. De eigenschap `TenantId` identificeert de AAD-Tenant waartoe de identiteit behoort. De `PrincipalId` is een unieke id voor de nieuwe identiteit van het cluster. Binnen AAD heeft de service-principal dezelfde naam die u hebt gegeven aan uw App Service-of Azure Functions-exemplaar.

## <a name="remove-an-identity"></a>Een identiteit verwijderen

Als u een door het systeem toegewezen identiteit verwijdert, wordt deze ook uit AAD verwijderd. Door het systeem toegewezen identiteiten worden ook automatisch verwijderd uit AAD wanneer de cluster bron wordt verwijderd. Een door het systeem toegewezen identiteit kan worden verwijderd door de functie uit te scha kelen:

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Explorer-clusters beveiligen in azure](security.md)
* [Beveilig uw cluster in Azure Data Explorer-Azure Portal](manage-cluster-security.md) door versleuteling op rest in te scha kelen.
 * [Door de klant beheerde sleutels configureren metC#](customer-managed-keys-csharp.md)
 * [Door de klant beheerde sleutels configureren met behulp van de Azure Resource Manager sjabloon](customer-managed-keys-resource-manager.md)
