---
title: 'Door de klant beheerde sleutels configureren met C #'
description: In dit artikel wordt beschreven hoe u versleuteling van door de klant beheerde sleutels op uw gegevens configureren in Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a00b0876c4a188b932032129ed5a394e94198930
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297949"
---
# <a name="configure-customer-managed-keys-using-c"></a>Door de klant beheerde sleutels configureren met C #

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-sjabloon](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

In deze sectie ziet u hoe u versleuteling met door de klant beheerde sleutels configureert met de Azure Data Explorer C#-client. 

### <a name="prerequisites"></a>Vereisten

* Als u Visual Studio 2019 niet hebt geïnstalleerd, u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

### <a name="install-c-nuget"></a>C# NuGet installeren

* Installeer het [NuGet-pakket (Azure Data Explorer) (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)

* Installeer het [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet-pakket](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) voor verificatie.

### <a name="authentication"></a>Authentication

Als u de voorbeelden in dit artikel wilt uitvoeren, [maakt u een Azure AD-toepassing](/azure/active-directory/develop/howto-create-service-principal-portal) en serviceprincipal die toegang heeft tot bronnen. U roltoewijzing toevoegen aan het `Directory (tenant) ID`abonnementsbereik en de vereiste `Application ID`, en `Client Secret`.

### <a name="configure-cluster"></a>Cluster configureren

Standaard maakt Azure Data Explorer-versleuteling gebruik van door Microsoft beheerde sleutels. Configureer uw Azure Data Explorer-cluster om door de klant beheerde sleutels te gebruiken en geef de sleutel op die aan het cluster moet worden gekoppeld.

1. Werk uw cluster bij met de volgende code:

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
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Voer de volgende opdracht uit om te controleren of uw cluster is bijgewerkt:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Als het `ProvisioningState` resultaat `Succeeded` met de waarde wordt bevat, is uw cluster bijgewerkt.

## <a name="update-the-key-version"></a>De belangrijkste versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u het cluster bijwerken om de nieuwe versie te gebruiken. Bel eerst `Get-AzKeyVaultKey` om de nieuwste versie van de sleutel te krijgen. Werk vervolgens de eigenschappen van de sleutelkluis van het cluster bij om de nieuwe versie van de sleutel te gebruiken, zoals weergegeven in [Cluster configureren](#configure-cluster).

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Explorer-clusters beveiligen in Azure](security.md)
* [Beheerde identiteiten configureren voor uw Azure Data Explorer-cluster](managed-identities.md)
* [Beveilig uw cluster in Azure Data Explorer - Azure-portal](manage-cluster-security.md) door versleuteling in rust in te schakelen.
* [Door de klant beheerde sleutels configureren met de sjabloon Azure Resource Manager](customer-managed-keys-resource-manager.md)


