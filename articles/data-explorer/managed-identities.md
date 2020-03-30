---
title: Beheerde identiteiten configureren voor Azure Data Explorer-cluster
description: Meer informatie over het configureren van beheerde identiteiten voor azure data explorer-cluster.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f9592f5d2666684e0cf5eef687b1e69cfb55066c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065575"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Beheerde identiteiten configureren voor uw Azure Data Explorer-cluster

Met een [beheerde identiteit van Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) heeft uw cluster eenvoudig toegang tot andere door AAD beveiligde bronnen, zoals Azure Key Vault. De identiteit wordt beheerd door het Azure-platform en vereist niet dat u geheimen indient of roteert. In dit artikel ziet u hoe u een beheerde identiteit maakt voor Azure Data Explorer-clusters. Beheerde identiteitsconfiguratie wordt momenteel alleen ondersteund om [door de klant beheerde sleutels voor uw cluster in](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)te schakelen.

> [!Note]
> Beheerde identiteiten voor Azure Data Explorer gedragen zich niet zoals verwacht als uw app is gemigreerd tussen abonnementen of tenants. De app moet een nieuwe identiteit verkrijgen, wat kan worden gedaan door de functie [uit te schakelen](#remove-a-system-assigned-identity) en opnieuw in te [schakelen.](#add-a-system-assigned-identity) Toegangsbeleid van downstreambronnen moet ook worden bijgewerkt om de nieuwe identiteit te gebruiken.

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen
                                                                                                    
Wijs een door het systeem toegewezen identiteit toe die is gekoppeld aan uw cluster en wordt verwijderd als uw cluster wordt verwijderd. Een cluster kan slechts één door het systeem toegewezen identiteit hebben. Voor het maken van een cluster met een door het systeem toegewezen identiteit moet een extra eigenschap op het cluster worden ingesteld. De door het systeem toegewezen identiteit wordt toegevoegd met Behulp van C#,ARM-sjablonen of de Azure-portal zoals hieronder beschreven.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Een door het systeem toegewezen identiteit toevoegen met behulp van de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Nieuw Azure Data Explorer-cluster

1. [Een Azure Data Explorer-cluster maken](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. Selecteer op het tabblad **Beveiliging** > **door het systeem toegewezen identiteit**, de optie **Aan**. Als u de toegewezen identiteit van het systeem wilt verwijderen, selecteert u **Uit .**
2. Selecteer **Volgende:Tags>** of **Review + maken** om het cluster te maken.

    ![Systeemtoegewezen identiteit toevoegen aan nieuw cluster](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Bestaand Azure Data Explorer-cluster

1. Open een bestaand Azure Data Explorer-cluster.
1. Selecteer > **Instellingen-identiteit** in het linkerdeelvenster van de portal. **Settings**
1. Ga in het tabblad **Identiteit>** **Systeem toegewezen:**
   1. De schuifregelaar **Status** verplaatsen naar **Aan**.
   1. Selecteer **Opslaan**
   1. Selecteer **Ja** in het pop-upvenster

    ![Systeemtoegewezen identiteit toevoegen](media/managed-identities/turn-system-assigned-identity-on.png)

1. Na een paar minuten, het scherm toont: 
  * **Object-id** - gebruikt voor door klanten beheerde sleutels 
  * **Roltoewijzingen** - klik op koppeling om relevante rollen toe te wijzen

    ![Systeem toegewezen identiteit op](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Een door het systeem toegewezen identiteit toevoegen met C #

#### <a name="prerequisites"></a>Vereisten

Ga als lid van het werk om een beheerde identiteit in te stellen met de Azure Data Explorer C#-client:

* Installeer het [NuGet-pakket (Azure Data Explorer) (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Installeer het [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet-pakket](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) voor verificatie.
* [Maak een Azure AD-toepassing](/azure/active-directory/develop/howto-create-service-principal-portal) en serviceprincipal die toegang heeft tot bronnen. U voegt roltoewijzing toe aan het `Directory (tenant) ID` `Application ID`abonnementsbereik `Client Secret`en krijgt de vereiste , en .

#### <a name="create-or-update-your-cluster"></a>Uw cluster maken of bijwerken

1. Uw cluster maken of `Identity` bijwerken met de eigenschap:

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
    
2. Voer de volgende opdracht uit om te controleren of uw cluster is gemaakt of bijgewerkt met een identiteit:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Als het `ProvisioningState` resultaat `Succeeded` de waarde bevat, is het cluster gemaakt of bijgewerkt en moet het de volgende eigenschappen hebben:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`en `TenantId` worden vervangen door GUIDs. De `TenantId` eigenschap identificeert de AAD-huurder waartoe de identiteit behoort. Het `PrincipalId` is een unieke id voor de nieuwe identiteit van het cluster. Binnen AAD heeft de serviceprincipal dezelfde naam die u aan uw appservice of Azure-functieinstantie hebt gegeven.

# <a name="arm-template"></a>[ARM-sjabloon](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Een door het systeem toegewezen identiteit toevoegen met behulp van een Azure Resource Manager-sjabloon

Een Azure Resource Manager-sjabloon kan worden gebruikt om de implementatie van uw Azure-resources te automatiseren. Zie [Een Azure Data Explorer-cluster en -database maken met behulp van een Azure Resource Manager-sjabloon](create-cluster-database-resource-manager.md)voor meer informatie over het implementeren naar Azure Data Explorer.

Als u het systeemtoegewezen type toevoegt, moet Azure de identiteit voor uw cluster maken en beheren. Elke resource `Microsoft.Kusto/clusters` van het type kan worden gemaakt met een identiteit door de volgende eigenschap op te geven in de resourcedefinitie: 

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

Wanneer het cluster wordt gemaakt, heeft het de volgende extra eigenschappen:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`en `<PRINCIPALID>` worden vervangen door GUIDs. De `TenantId` eigenschap identificeert de AAD-huurder waartoe de identiteit behoort. Het `PrincipalId` is een unieke id voor de nieuwe identiteit van het cluster. Binnen AAD heeft de serviceprincipal dezelfde naam die u aan uw appservice of Azure-functieinstantie hebt gegeven.

---

## <a name="remove-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit verwijderen

Als u een door het systeem toegewezen identiteit verwijdert, wordt deze ook uit AAD verwijderd. Door het systeem toegewezen identiteiten worden ook automatisch uit AAD verwijderd wanneer de clusterbron wordt verwijderd. Een door het systeem toegewezen identiteit kan worden verwijderd door de functie uit te schakelen.  De door het systeem toegewezen identiteit wordt verwijderd met Behulp van C#,ARM-sjablonen of de Azure-portal zoals hieronder beschreven.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

### <a name="remove-a-system-assigned-identity-using-the-azure-portal"></a>Een door het systeem toegewezen identiteit verwijderen met behulp van de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer > **Instellingen-identiteit** in het linkerdeelvenster van de portal. **Settings**
1. Ga in het tabblad **Identiteit>** **Systeem toegewezen:**
    1. De schuifregelaar **Status** naar **Uit verplaatsen**.
    1. Selecteer **Opslaan**
    1. Selecteer in het pop-upvenster **Ja** om de door het systeem toegewezen identiteit uit te schakelen. Het **identiteitsvenster** wordt teruggezet naar dezelfde voorwaarde als vóór de toevoeging van de door het systeem toegewezen identiteit.

    ![Systeem toegewezen identiteit uit](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Een door het systeem toegewezen identiteit verwijderen met C #

Voer het volgende uit om de door het systeem toegewezen identiteit te verwijderen:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM-sjabloon](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Een door het systeem toegewezen identiteit verwijderen met behulp van een Azure Resource Manager-sjabloon

Voer het volgende uit om de door het systeem toegewezen identiteit te verwijderen:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Explorer-clusters beveiligen in Azure](security.md)
* [Beveilig uw cluster in Azure Data Explorer - Azure-portal](manage-cluster-security.md) door versleuteling in rust in te schakelen.
 * [Door de klant beheerde sleutels configureren met C #](customer-managed-keys-csharp.md)
 * [Door de klant beheerde sleutels configureren met de sjabloon Azure Resource Manager](customer-managed-keys-resource-manager.md)
