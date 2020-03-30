---
title: 'Een Azure Data Explorer-cluster & DB maken met C #'
description: 'Meer informatie over het maken van een Azure Data Explorer-cluster en -database met behulp van de C #'
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0c32d438ac8551f061343edb747e9fc035b498e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246407"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Een Azure Data Explorer-cluster en -database maken met C #

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager-sjabloon](create-cluster-database-resource-manager.md)

Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. De volgende stap is het opnemen (laden) van gegevens in een database, zodat u er query's op kunt uitvoeren. In dit artikel maakt u een cluster en een database met C#.

## <a name="prerequisites"></a>Vereisten

* Als u Visual Studio 2019 niet hebt geïnstalleerd, u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>Authentication
Voor het uitvoeren van de voorbeelden in dit artikel hebben we een Azure AD-toepassing en serviceprincipal nodig die toegang heeft tot bronnen. Schakel [een Azure AD-toepassing maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) om een gratis Azure AD-toepassing te maken en roltoewijzing toe te voegen aan het abonnementsbereik. Het laat ook zien `Directory (tenant) ID` `Application ID`hoe `Client Secret`je de , , en .

## <a name="create-the-azure-data-explorer-cluster"></a>Het Azure Data Explorer-cluster maken

1. Maak uw cluster met de volgende code:

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
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
   |---|---|---|
   | clusterName | *mykustocluster* | De gewenste naam van uw cluster.|
   | skuName | *Standard_D13_v2* | De SKU die wordt gebruikt voor uw cluster. |
   | laag | *Standaard* | De SKU-laag. |
   | capacity | *Nummer* | Het aantal exemplaren van het cluster. |
   | resourceGroupName | *testrg* | Naam van de resourcegroep waar het cluster wordt gemaakt. |

    > [!NOTE]
    > **Een cluster maken** is een langdurige bewerking, dus het is ten zeerste aan te raden om CreateOrUpdateAsync te gebruiken in plaats van CreateOrUpdate. 

1. Voer de volgende opdracht uit om te controleren of het cluster is gemaakt:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Als het resultaat `ProvisioningState` met waarde `Succeeded` bevat, is het maken van het cluster geslaagd.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>De database in het Azure Data Explorer-cluster maken

1. Maak uw database met de volgende code:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
   |---|---|---|
   | clusterName | *mykustocluster* | De naam van het cluster waar de database wordt gemaakt.|
   | Databasenaam | *mykustodatabase* | De naam van uw database.|
   | resourceGroupName | *testrg* | Naam van de resourcegroep waar het cluster wordt gemaakt. |
   | softDeletePeriod softDeletePeriod softDeletePeriod softDelete | *3650:00:00:00* | Hoe lang gegevens worden opgeslagen om query's erop te kunnen uitvoeren. |
   | hotCachePeriode | *3650:00:00:00* | Hoe lang gegevens worden opgeslagen in de cache. |

2. Voer de volgende opdracht uit om de database te bekijken die u hebt gemaakt:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

U hebt nu een cluster en een database.

## <a name="clean-up-resources"></a>Resources opschonen

* Als u van plan bent onze andere artikelen te volgen, behoudt u de resources die u hebt gemaakt.
* Als u resources wilt opschonen, moet u het cluster verwijderen. Wanneer u een cluster verwijdert, worden alle databases hierin ook verwijderd. Gebruik de volgende opdracht om uw cluster te verwijderen:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Volgende stappen

* [Gegevens opnemen met de Azure Data Explorer .NET Standard SDK (Preview)](net-standard-ingest-data.md)
