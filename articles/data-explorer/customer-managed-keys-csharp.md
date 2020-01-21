---
title: Door de klant beheerde sleutels configureren metC#
description: In dit artikel wordt beschreven hoe u door de klant beheerde sleutels versleuteling configureert voor uw gegevens in azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 16c108790dd696e98a1264691254c9e99dac6cd3
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280596"
---
# <a name="configure-customer-managed-keys-using-c"></a>Door de klant beheerde sleutels configureren metC#

> [!div class="op_single_selector"]
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-sjabloon](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

In deze sectie wordt beschreven hoe u versleuteling van door de klant beheerde sleutels configureert met C# behulp van de Azure Data Explorer-client. 

### <a name="prerequisites"></a>Vereisten

* Als Visual Studio 2019 niet is geïnstalleerd, kunt u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

### <a name="install-c-nuget"></a>NuGet C# installeren

* Installeer het [Azure Data Explorer (Kusto) NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Installeer het [NuGet-pakket micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) voor verificatie.

### <a name="authentication"></a>Verificatie

Als u de voor beelden in dit artikel wilt uitvoeren, [maakt u een Azure AD-toepassing](/azure/active-directory/develop/howto-create-service-principal-portal) en service-principal die toegang hebben tot resources. U kunt roltoewijzing toevoegen aan het abonnements bereik en de vereiste `Directory (tenant) ID`, `Application ID`en `Client Secret`ophalen.

### <a name="configure-cluster"></a>Cluster configureren

Data Explorer versleuteling van Azure maakt standaard gebruik van door micro soft beheerde sleutels. Configureer uw Azure Data Explorer-cluster voor het gebruik van door de klant beheerde sleutels en geef de sleutel op die u aan het cluster wilt koppelen.

1. Werk het cluster bij met de volgende code:

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

1. Voer de volgende opdracht uit om te controleren of het cluster is bijgewerkt:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Als het resultaat `ProvisioningState` met de `Succeeded` waarde bevat, is het cluster bijgewerkt.

## <a name="update-the-key-version"></a>De sleutel versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u het cluster bijwerken voor gebruik van de nieuwe versie. Roep eerst `Get-AzKeyVaultKey` aan om de nieuwste versie van de sleutel op te halen. Werk vervolgens de sleutel kluis eigenschappen van het cluster bij om de nieuwe versie van de sleutel te gebruiken, zoals wordt weer gegeven in [cluster configureren](#configure-cluster).

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Explorer-clusters beveiligen in azure](security.md)
* [Beheerde identiteiten voor uw Azure Data Explorer-cluster configureren](managed-identities.md)
* [Beveilig uw cluster in Azure Data Explorer-Azure Portal](manage-cluster-security.md) door versleuteling op rest in te scha kelen.
* [Door de klant beheerde sleutels configureren met behulp van de Azure Resource Manager sjabloon](customer-managed-keys-resource-manager.md)


