---
title: De functie voor het volgen van de Data Base gebruiken om data bases in azure Data Explorer te koppelen
description: Meer informatie over het koppelen van data bases in azure Data Explorer met behulp van de functie voor het volgen van de data base.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: dd2c29632d70da64251c5e1736a9cb7d82f5d0dc
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667357"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>De follower-Data Base gebruiken om data bases in azure Data Explorer te koppelen

Met de functie voor het volgen van de **Data Base** kunt u een Data Base die zich in een ander cluster bevindt, koppelen aan uw Azure Data Explorer-cluster. De **follower-data base** is gekoppeld in de modus *alleen-lezen* , waardoor het mogelijk is om de gegevens weer te geven en query's uit te voeren op de gegevens die zijn opgenomen in de **Leader-data base**. De follower-data base synchroniseert wijzigingen in de voorloop databases. Vanwege de synchronisatie is er een gegevens vertraging van enkele seconden tot enkele minuten in de beschik baarheid van gegevens. De lengte van de tijds vertraging is afhankelijk van de totale grootte van de meta gegevens van de Leader database. De leiders-en follower-data bases gebruiken hetzelfde opslag account om de gegevens op te halen. De opslag is eigendom van de Leader-data base. De volgende data base kan de gegevens weer geven zonder deze te hoeven op te nemen. Omdat de gekoppelde Data Base een alleen-lezen database is, kunnen de gegevens, tabellen en beleids regels in de-data base niet worden gewijzigd, behalve voor het [beleid voor caching](#configure-caching-policy), [principals](#manage-principals)en [machtigingen](#manage-permissions). Bijgevoegde data bases kunnen niet worden verwijderd. Ze moeten worden losgekoppeld van de leider of de volger en kunnen alleen worden verwijderd. 

Het koppelen van een data base aan een ander cluster met behulp van de volgende mogelijkheid wordt gebruikt als de infra structuur voor het delen van gegevens tussen organisaties en teams. De functie is handig voor het scheiden van reken resources om een productie omgeving te beschermen tegen niet-productie-use cases. Volgers kunnen ook worden gebruikt om de kosten van Azure Data Explorer cluster te koppelen aan de partij die query's uitvoert op de gegevens.

## <a name="which-databases-are-followed"></a>Welke data bases worden er gevolgd?

* Een cluster kan één data base, verschillende data bases of alle data bases van een Leader cluster volgen. 
* Eén cluster kan data bases uit meerdere leider clusters volgen. 
* Een cluster kan zowel follow-data bases als Leader databases bevatten

## <a name="prerequisites"></a>Vereisten

1. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
1. [Maak het cluster en de data base](/azure/data-explorer/create-cluster-database-portal) voor de leider en volger.
1. [Gegevens](/azure/data-explorer/ingest-sample-data) opnemen in de Leader database met behulp van een van de verschillende methoden die worden beschreven in het [overzicht van opname](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Een database koppelen

Er zijn verschillende methoden die u kunt gebruiken om een Data Base te koppelen. In dit artikel bespreken we het koppelen van een Data Base C# met behulp van of een Azure Resource Manager sjabloon. Als u een Data Base wilt koppelen, moet u machtigingen hebben voor het cluster van de leider en het Volg cluster. Zie [Manage permissions](#manage-permissions)voor meer informatie over machtigingen.

### <a name="attach-a-database-using-c"></a>Een Data Base koppelen metC#

#### <a name="needed-nugets"></a>Benodigde NuGets

* Installeer [micro soft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installeer [micro soft. rest. ClientRuntime. Azure. Authentication voor authenticatie](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Code voorbeeld

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
var attachedDatabaseConfigurationName = "adc";
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

### <a name="attach-a-database-using-python"></a>Een Data Base koppelen met behulp van python

#### <a name="needed-modules"></a>Benodigde modules

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Code voorbeeld

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
attached_database_Configuration_name = "adc"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Een Data Base koppelen met behulp van een Azure Resource Manager sjabloon

In deze sectie leert u hoe u een Data Base kunt koppelen met behulp van een [Azure Resource Manager sjabloon](../azure-resource-manager/resource-group-overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
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
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
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
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
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

U kunt de Azure Resource Manager-sjabloon implementeren met [behulp van de Azure Portal](https://portal.azure.com) of met behulp van Power shell.

   ![sjabloon implementatie](media/follower/template-deployment.png)


|**Instelling**  |**Beschrijving**  |
|---------|---------|
|Naam van follower-cluster     |  De naam van het opvolg cluster       |
|Naam van gekoppelde database configuraties    |    De naam van het gekoppelde database configuratie object. De naam moet uniek zijn op het cluster niveau.     |
|Databasenaam     |      De naam van de data base die moet worden gevolgd. Als u alle data bases van de leider wilt volgen, gebruikt u *.   |
|Resource-ID van Leader cluster    |   De resource-ID van het leider cluster.      |
|Type aanpassing van standaard-principals    |   De standaard instelling voor het wijzigen van de principal. Kan `Union`, `Replace` of `None`zijn. Zie voor meer informatie over de standaard instelling voor het wijzigen van principals de [opdracht voor het wijzigen van het type Principal](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Locatie   |   De locatie van alle resources. De leider en de volger moeten zich op dezelfde locatie bestaan.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Controleren of de data base is gekoppeld

Als u wilt controleren of de data base is gekoppeld, zoekt u de gekoppelde data bases in de [Azure Portal](https://portal.azure.com). 

1. Navigeer naar het follower-cluster en selecteer **data bases**
1. Zoek naar nieuwe alleen-lezen data bases in de lijst met data bases.

    ![Alleen-lezen follower-data base](media/follower/read-only-follower-database.png)

U kunt ook

1. Navigeer naar het Leader cluster en selecteer **data bases**
2. Controleer of de relevante data bases zijn gemarkeerd als **gedeeld met anderen** > **Ja**

    ![Bijgevoegde data bases lezen en schrijven](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>De opvolger-data base loskoppelen metC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>De gekoppelde opvolgende data base loskoppelen van het opvolg cluster

Het volgende cluster kan gekoppelde data bases loskoppelen:

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
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>De gekoppelde opvolgende data base loskoppelen van het Leader cluster

Het leider cluster kan gekoppelde data bases als volgt loskoppelen:

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
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>De follower-data base loskoppelen met python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>De gekoppelde opvolgende data base loskoppelen van het opvolg cluster

Het volgende cluster kan gekoppelde data bases loskoppelen:

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
attached_database_configurationName = "adc"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>De gekoppelde opvolgende data base loskoppelen van het Leader cluster

Het leider cluster kan gekoppelde data bases als volgt loskoppelen:

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
attached_database_configuration_name = "adc"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Principals, machtigingen en beleid voor cache beheer beheren

### <a name="manage-principals"></a>Principals beheren

Wanneer u een Data Base koppelt, geeft u het **type standaard-principals wijzigen**op. Met de standaard instelling wordt de Leader database verzameling van [geautoriseerde principals](/azure/kusto/management/access-control/index#authorization) behouden

|**Type** |**Beschrijving**  |
|---------|---------|
|**Réunion**     |   De gekoppelde data base-principals bevatten altijd de oorspronkelijke data base-principals, plus aanvullende nieuwe principals die aan de follower-Data Base worden toegevoegd.      |
|**Vervangen**   |    Geen overname van principals van de oorspronkelijke data base. Er moeten nieuwe principals worden gemaakt voor de gekoppelde data base.     |
|**Geen**   |   De gekoppelde data base-principals bevatten alleen de principals van de oorspronkelijke data base zonder extra principals.      |

Zie voor meer informatie over het gebruik van besturings opdrachten voor het configureren van de geautoriseerde principals [beheer opdrachten voor het beheren van een opvolg cluster](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Machtigingen beheren

Het beheren van een alleen-lezen database machtiging is hetzelfde als voor alle database typen. Zie [machtigingen beheren in de Azure Portal](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Cache beleid configureren

De beheerder van de follower-data base kan het [cache beleid](/azure/kusto/management/cache-policy) van de gekoppelde data base of een van de bijbehorende tabellen op het hosting cluster wijzigen. De standaard instelling is de Leader database verzameling van data base-en cache beleidsregels op tabel niveau. U kunt bijvoorbeeld een cache beleid van 30 dagen hebben op de Leader-Data Base voor het uitvoeren van maandelijkse rapportage en een cache beleid voor drie dagen op de follower-data base om alleen de recente gegevens op te vragen voor probleem oplossing. Zie voor meer informatie over het gebruik van besturings opdrachten voor het configureren van het cache beleid voor de follow-data base of-tabel de [besturings opdrachten voor het beheren van een follower-cluster](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Beperkingen

* De volgers en de Leader clusters moeten zich in dezelfde regio bevinden.
* [Streaming-opname](/azure/data-explorer/ingest-data-streaming) kan niet worden gebruikt voor een Data Base die wordt gevolgd.
* U kunt een Data Base die aan een ander cluster is gekoppeld, niet verwijderen voordat u deze loskoppelt.
* U kunt een cluster met een Data Base die is gekoppeld aan een ander cluster, niet verwijderen voordat u het loskoppelt.
* U kunt een cluster dat is gekoppeld aan een of meer data base (s) niet stoppen. 

## <a name="next-steps"></a>Volgende stappen

* Zie [beheer opdrachten voor het beheren van een follower-cluster](/azure/kusto/management/cluster-follower)voor meer informatie over de configuratie van de follower-cluster.