---
title: De functie volgerdatabase gebruiken om databases in Azure Data Explorer te koppelen
description: Meer informatie over het koppelen van databases in Azure Data Explorer met behulp van de functie vervolgdatabase.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: f6dbdb54c1c5a5d477c3ccb988963758faab83b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140011"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Volgerdatabase gebruiken om databases in Azure Data Explorer te koppelen

Met de functie **volgerdatabase** u een database in een ander cluster koppelen aan uw Azure Data Explorer-cluster. De **volger database** is gekoppeld in *alleen-lezen* modus, waardoor het mogelijk is om de gegevens te bekijken en query's uit te voeren op de gegevens die werd ingenomen in de **leader database**. De volgerdatabase synchroniseert wijzigingen in de leaderdatabases. Als gevolg van de synchronisatie, is er een gegevensvertraging van een paar seconden tot een paar minuten in de beschikbaarheid van gegevens. De lengte van de vertraging is afhankelijk van de totale grootte van de metagegevens van de leader-database. De databases van de leider en de volger gebruiken hetzelfde opslagaccount om de gegevens op te halen. De opslag is eigendom van de leader database. De volger database bekijkt de gegevens zonder dat deze hoeft in te nemen. Aangezien de bijgevoegde database een alleen-lezen database is, kunnen de gegevens, tabellen en [beleidsregels](#manage-permissions)in de database niet worden gewijzigd, behalve voor [cachingbeleid](#configure-caching-policy), [principals](#manage-principals)en machtigingen . Gekoppelde databases kunnen niet worden verwijderd. Ze moeten worden losgemaakt door de leider of volgeling en alleen dan kunnen ze worden verwijderd. 

Het koppelen van een database aan een ander cluster met behulp van de volger mogelijkheid wordt gebruikt als de infrastructuur om gegevens te delen tussen organisaties en teams. De functie is handig om rekenresources te scheiden om een productieomgeving te beschermen tegen niet-productieusecases. Volger kan ook worden gebruikt om de kosten van het Azure Data Explorer-cluster te koppelen aan de partij die query's op de gegevens uitvoert.

## <a name="which-databases-are-followed"></a>Welke databases worden gevolgd?

* Een cluster kan één database, meerdere databases of alle databases van een leadercluster volgen. 
* Eén cluster kan databases uit meerdere leaderclusters volgen. 
* Een cluster kan zowel volgerdatabases als leaderdatabases bevatten

## <a name="prerequisites"></a>Vereisten

1. Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
1. [Maak cluster en DB](/azure/data-explorer/create-cluster-database-portal) voor de leider en volger.
1. [Inname van gegevens](/azure/data-explorer/ingest-sample-data) tot leider database met behulp van een van de verschillende methoden besproken in [inname overzicht](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Een database koppelen

Er zijn verschillende methoden die u gebruiken om een database bij te voegen. In dit artikel bespreken we het koppelen van een database met C# of een Azure Resource Manager-sjabloon. Als u een database wilt koppelen, moet u machtigingen hebben voor het leiderscluster en het cluster van de volger. Zie [Machtigingen beheren voor](#manage-permissions)meer informatie over machtigingen.

### <a name="attach-a-database-using-c"></a>Een database koppelen met C #

#### <a name="needed-nugets"></a>Benodigde NuGets

* Installeer [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installeer [Microsoft.Rest.ClientRuntime.Azure.Authentication voor verificatie](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Codevoorbeeld

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Een database koppelen met Python

#### <a name="needed-modules"></a>Benodigde modules

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Codevoorbeeld

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Een database toevoegen met een Azure Resource Manager-sjabloon

In deze sectie leert u een database aan een bestaande cluser te koppelen met behulp van een [Azure Resource Manager-sjabloon](../azure-resource-manager/management/overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>De sjabloon implementeren 

U de azure resource manager-sjabloon implementeren [met behulp van de Azure-portal](https://portal.azure.com) of met powershell.

   ![sjabloonimplementatie](media/follower/template-deployment.png)


|**Instelling**  |**Beschrijving**  |
|---------|---------|
|Clusternaam volger     |  De naam van het cluster van de volger; waar de sjabloon wordt geïmplementeerd.  |
|Naam bijgevoegde databaseconfiguraties    |    De naam van het object bijgevoegde databaseconfiguraties. De naam kan elke tekenreeks zijn die uniek is op clusterniveau.     |
|Databasenaam     |      De naam van de te volgen database. Als u alle databases van de leider wilt volgen, gebruikt u '*'.   |
|Leader Cluster Resource ID    |   De resource-ID van het leiderscluster.      |
|Standaard Principals Wijziging Soort    |   De standaard hoofdwijzigingssoort. Kan `Union` `Replace` worden, `None`of . Zie [hoofdwijzigingsregelopdracht](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind)voor meer informatie over de standaardhoofdswijzigingsvorm.      |
|Locatie   |   De locatie van alle middelen. De leider en de volgeling moeten zich op dezelfde locatie bevinden.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Controleren of de database is gekoppeld

Als u wilt controleren of de database is gekoppeld, zoekt u uw gekoppelde databases in de [Azure-portal.](https://portal.azure.com) 

1. Navigeren naar het cluster van volgers en **selecteer Databases**
1. Zoek naar nieuwe alleen-lezen databases in de databaselijst.

    ![Database met alleen-lezen-volgers](media/follower/read-only-follower-database.png)

U kunt ook het volgende doen:

1. Navigeren naar het leiderscluster en **selecteer Databases**
2. Controleer of de relevante databases zijn gemarkeerd als **GEDEELD MET ANDEREN** > **Ja**

    ![Bijgevoegde databases lezen en schrijven](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>De volgerdatabase loskoppelen met C # 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>De gekoppelde volgerdatabase loskoppelen van het volgcluster

Het cluster van de volger kan elke gekoppelde database als volgt loskoppelen:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>De gekoppelde volgerdatabase loskoppelen van het leadercluster

Het leadercluster kan elke bijgevoegde database als volgt loskoppelen:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>De volgerdatabase loskoppelen met Python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>De gekoppelde volgerdatabase loskoppelen van het volgcluster

Het cluster van de volger kan elke gekoppelde database als volgt loskoppelen:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>De gekoppelde volgerdatabase loskoppelen van het leadercluster

Het leadercluster kan elke bijgevoegde database als volgt loskoppelen:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Beleid voor principals, machtigingen en caching beheren

### <a name="manage-principals"></a>Principals beheren

Wanneer u een database koppelt, geeft u de **wijzigingsvorm van de standaardprincipals op.** De standaard is het bijhouden van de leider database verzameling van [geautoriseerde opdrachtgevers](/azure/kusto/management/access-control/index#authorization)

|**Soort** |**Beschrijving**  |
|---------|---------|
|**Unie**     |   De bijgevoegde database principals zal altijd de oorspronkelijke database principals plus extra nieuwe opdrachtgevers toegevoegd aan de volger database.      |
|**Vervangen**   |    Geen erfenis van opdrachtgevers uit de oorspronkelijke database. Er moeten nieuwe principals worden gemaakt voor de bijgevoegde database.     |
|**Geen**   |   De bijgevoegde databaseprincipals bevatten alleen de opdrachtgevers van de oorspronkelijke database zonder extra opdrachtgevers.      |

Zie Opdrachten voor het [beheren van een volgercluster voor](/azure/kusto/management/cluster-follower)meer informatie over het gebruik van besturingselementopdrachten voor het configureren van de geautoriseerde principals.

### <a name="manage-permissions"></a>Machtigingen beheren

Het beheren van alleen-lezen databasetoestemming is hetzelfde als voor alle databasetypen. Zie [machtigingen beheren in de Azure-portal](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Caching-beleid configureren

De beheerder van de volgerdatabase kan het [cachingbeleid](/azure/kusto/management/cache-policy) van de bijgevoegde database of een van de tabellen op het hostingcluster wijzigen. De standaardinstelling is het bijhouden van de databaseverzameling van database- en tabelniveau caching-beleid. U bijvoorbeeld een cachebeleid van 30 dagen hebben op de leader-database voor het uitvoeren van maandelijkse rapportage en een driedaags cachingbeleid op de volgende database om alleen de recente gegevens voor het oplossen van problemen op te vragen. Zie [Besturingselementopdrachten voor het beheren van een volgercluster voor](/azure/kusto/management/cluster-follower)meer informatie over het gebruik van besturingselementopdrachten voor het configureren van het cachebeleid in de volgtabeldatabase of -tabel.

## <a name="limitations"></a>Beperkingen

* De volger en de leider clusters moeten zich in dezelfde regio bevinden.
* [Streaming kan](/azure/data-explorer/ingest-data-streaming) niet worden gebruikt in een database die wordt gevolgd.
* Gegevensversleuteling met behulp van [door de klant beheerde sleutels](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) wordt niet ondersteund op zowel leader- als volgerclusters. 
* U een database die is gekoppeld aan een ander cluster niet verwijderen voordat u deze loskoppelt.
* U een cluster waarop een database is gekoppeld aan een ander cluster niet verwijderen voordat u deze loskoppelt.
* U een cluster met een aangekoppelde volger of leader-database(s) niet stoppen. 

## <a name="next-steps"></a>Volgende stappen

* Zie [Besturingselementopdrachten voor het beheren van een volgercluster voor](/azure/kusto/management/cluster-follower)informatie over de clusterconfiguratie van volgers.
