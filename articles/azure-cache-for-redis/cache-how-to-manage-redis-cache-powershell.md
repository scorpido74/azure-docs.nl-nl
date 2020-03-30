---
title: Azure-cache voor Redis beheren met Azure PowerShell
description: Meer informatie over het uitvoeren van beheertaken voor Azure Cache voor Redis met Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: a385d3ed7ef46389f96de72c98ffc29cebf60ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278530"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Azure-cache voor Redis beheren met Azure PowerShell
> [!div class="op_single_selector"]
> * [Powershell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure-CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit onderwerp ziet u hoe u veelvoorkomende taken uitvoeren, zoals het maken, bijwerken en schalen van uw Azure-cache voor Redis-exemplaren, het regenereren van toegangssleutels en het weergeven van informatie over uw caches. Zie [Azure Cache voor Redis-cmdlets voor](https://docs.microsoft.com/powershell/module/az.rediscache)een volledige lijst met Azure-cache voor Redis-cmdlets.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Zie [Azure Resource Manager vs. klassieke implementatie: Inzicht in implementatiemodellen en de status van uw resources](../azure-resource-manager/management/deployment-models.md)voor meer informatie over het klassieke implementatiemodel.

## <a name="prerequisites"></a>Vereisten
Als u Azure PowerShell al hebt geïnstalleerd, moet u Azure PowerShell-versie 1.0.0 of hoger hebben. U de versie van Azure PowerShell controleren die u met deze opdracht hebt geïnstalleerd op de opdrachtprompt azure PowerShell.

    Get-Module Az | format-table version


Eerst moet u zich met deze opdracht aanmelden bij Azure.

    Connect-AzAccount

Geef het e-mailadres van uw Azure-account en het wachtwoord ervan op in het aanmeldingsdialoogvenster van Microsoft Azure.

Als u vervolgens meerdere Azure-abonnementen hebt, moet u uw Azure-abonnement instellen. Voer deze opdracht uit om een lijst met huidige abonnementen te bekijken.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Voer de volgende opdracht uit om het abonnement op te geven. In het volgende voorbeeld is `ContosoSubscription`de naam van het abonnement .

    Select-AzSubscription -SubscriptionName ContosoSubscription

Voordat u Windows PowerShell met Azure Resource Manager gebruiken, hebt u het volgende nodig:

* Windows PowerShell, versie 3.0 of 4.0. Als u de versie van Windows`$PSVersionTable` PowerShell wilt `PSVersion` vinden, typt u: en controleert u de waarde van 3.0 of 4.0. Zie [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) of [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)als u een compatibele versie wilt installeren.

Gebruik de get-help-cmdlet om gedetailleerde hulp te krijgen voor elke cmdlet die u in deze zelfstudie ziet.

    Get-Help <cmdlet-name> -Detailed

Als u bijvoorbeeld hulp `New-AzRedisCache` wilt krijgen voor de cmdlet, typt u:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Verbinding maken met andere clouds
Standaard is `AzureCloud`de Azure-omgeving , die de globale Azure-cloudinstantie vertegenwoordigt. Als u verbinding wilt maken `Connect-AzAccount` met `-Environment` een`EnvironmentName` andere instantie, gebruikt u de opdracht met de opdrachtregelschakelaar met de gewenste omgeving of omgevingsnaam.

Voer de `Get-AzEnvironment` cmdlet uit om de lijst met beschikbare omgevingen te bekijken.

### <a name="to-connect-to-the-azure-government-cloud"></a>Verbinding maken met de Azure Government Cloud
Als u verbinding wilt maken met de Azure Government Cloud, gebruikt u een van de volgende opdrachten.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

of

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Als u een cache wilt maken in de Azure Government Cloud, gebruikt u een van de volgende locaties.

* USGov - Virginia
* USGov Iowa

Zie [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) en Microsoft Azure [Government Developer Guide](../azure-government-developer-guide.md)voor meer informatie over de Azure Government Cloud.

### <a name="to-connect-to-the-azure-china-cloud"></a>Verbinding maken met de Azure China Cloud
Als u verbinding wilt maken met de Azure China Cloud, gebruikt u een van de volgende opdrachten.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

of

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Als u een cache wilt maken in de Azure China Cloud, gebruikt u een van de volgende locaties.

* China East
* China - noord

Zie [AzureChinaCloud voor Azure voor Azure dat wordt beheerd door 21Vianet in China](https://www.windowsazure.cn/)voor meer informatie over de Azure China Cloud.

### <a name="to-connect-to-microsoft-azure-germany"></a>Verbinding maken met Microsoft Azure Germany
Als u verbinding wilt maken met Microsoft Azure Germany, gebruikt u een van de volgende opdrachten.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


of

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Als u een cache wilt maken in Microsoft Azure Germany, gebruikt u een van de volgende locaties.

* Duitsland - centraal
* Duitsland - noordoost

Zie Microsoft Azure Germany voor meer informatie over Microsoft Azure [Germany.](https://azure.microsoft.com/overview/clouds/germany/)

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Eigenschappen die worden gebruikt voor Azure Cache voor Redis PowerShell
De volgende tabel bevat eigenschappen en beschrijvingen voor veelgebruikte parameters bij het maken en beheren van uw Azure-cache voor Redis-exemplaren met Azure PowerShell.

| Parameter | Beschrijving | Standaard |
| --- | --- | --- |
| Name |Naam van de cache | |
| Locatie |Locatie van de cache | |
| ResourceGroupName |Naam van de brongroep waarin de cache moet worden gemaakt | |
| Grootte |De grootte van de cache. Geldige waarden zijn: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB |1 GB |
| ShardCount (ShardCount) |Het aantal shards dat moet worden gemaakt bij het maken van een premium cache met clustering ingeschakeld. Geldige waarden zijn: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Hiermee geeft u de SKU van de cache op. Geldige waarden zijn: Basic, Standard, Premium |Standard |
| RedisConfiguratie |Hiermee geeft u de configuratie-instellingen van Redis op. Zie de volgende tabel [Eigenschappen van RedisConfiguration](#redisconfiguration-properties) voor meer informatie over elke instelling. | |
| EnableNonSslPort |Geeft aan of de niet-SSL-poort is ingeschakeld. |False |
| MaxMemoryPolicy |Deze parameter is afgeschaft - gebruik in plaats daarvan RedisConfiguration. | |
| StaticIP (StaticIP) |Wanneer u uw cache host in een VNET, geeft u een uniek IP-adres op in het subnet voor de cache. Indien niet voorzien, wordt er een voor u gekozen uit het subnet. | |
| Subnet |Wanneer u uw cache host in een VNET, geeft u de naam op van het subnet waarin de cache moet worden geïmplementeerd. | |
| VirtualNetwork |Wanneer u uw cache host in een VNET, geeft u de bron-id op van het VNET waarin de cache moet worden geïmplementeerd. | |
| Keytype |Hiermee geeft u op welke toegangssleutel moet worden geregenereren bij het vernieuwen van toegangssleutels. Geldige waarden zijn: Primair, Secundair | |

### <a name="redisconfiguration-properties"></a>Eigenschappen van RedisConfiguration
| Eigenschap | Beschrijving | Prijscategorieën |
| --- | --- | --- |
| rdb-back-up ingeschakeld |Of [de gegevenspersistentie van Redis](cache-how-to-premium-persistence.md) is ingeschakeld |Alleen premium |
| rdb-storage-connection-string |De verbindingstekenreeks met het opslagaccount voor [de persistentie van Redis-gegevens](cache-how-to-premium-persistence.md) |Alleen premium |
| rdb-back-up-frequentie |De back-upfrequentie voor [de persistentie van Redis-gegevens](cache-how-to-premium-persistence.md) |Alleen premium |
| maxmemory-gereserveerd |Configureert het [geheugen dat is gereserveerd](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) voor niet-cacheprocessen |Standaard en Premium |
| maxmemory-beleid |Hiermee configureert u het [uitzettingsbeleid](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) voor de cache |Alle prijsniveaus |
| notify-keyspace-events |[Keyspace-meldingen](cache-configure.md#keyspace-notifications-advanced-settings) configureren |Standaard en Premium |
| hash-max-ziplist-items |Geheugenoptimalisatie [memory optimization](https://redis.io/topics/memory-optimization) configureren voor kleine geaggregeerde gegevenstypen |Standaard en Premium |
| hash-max-ziplist-waarde |Geheugenoptimalisatie [memory optimization](https://redis.io/topics/memory-optimization) configureren voor kleine geaggregeerde gegevenstypen |Standaard en Premium |
| set-max-intset-items |Geheugenoptimalisatie [memory optimization](https://redis.io/topics/memory-optimization) configureren voor kleine geaggregeerde gegevenstypen |Standaard en Premium |
| zset-max-ziplist-entries |Geheugenoptimalisatie [memory optimization](https://redis.io/topics/memory-optimization) configureren voor kleine geaggregeerde gegevenstypen |Standaard en Premium |
| zset-max-ziplist-waarde |Geheugenoptimalisatie [memory optimization](https://redis.io/topics/memory-optimization) configureren voor kleine geaggregeerde gegevenstypen |Standaard en Premium |
| databases |Hiermee configureert u het aantal databases. Deze eigenschap kan alleen worden geconfigureerd bij het maken van cache. |Standaard en Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Een Azure-cache voor Redis maken
Nieuwe Azure-cache voor Redis-exemplaren wordt gemaakt met de cmdlet [Nieuw-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache)

> [!IMPORTANT]
> De eerste keer dat u een Azure-cache voor Redis maakt `Microsoft.Cache` in een abonnement met de Azure-portal, registreert de portal de naamruimte voor dat abonnement. Als u probeert de eerste Azure-cache voor Redis in een abonnement met PowerShell te maken, moet u die naamruimte eerst registreren met de volgende opdracht. anders cmdlets `New-AzRedisCache` zoals `Get-AzRedisCache` en mislukken.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Voer de volgende opdracht uit om `New-AzRedisCache`een lijst met beschikbare parameters en hun beschrijvingen voor te bekijken.

    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Als u een cache met standaardparameters wilt maken, voert u de volgende opdracht uit.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`en `Location` zijn vereiste parameters, maar de rest is optioneel en heeft standaardwaarden. Als u de vorige opdracht uitvoert, wordt een Standaard SKU Azure-cache voor Redis-instantie uitgevoerd met de opgegeven naam, locatie en resourcegroep, die 1 GB groot is en de niet-SSL-poort is uitgeschakeld.

Als u een premium cache wilt maken, geeft u een grootte van P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) of P4 (53 GB - 530 GB) op. Als u clustering wilt inschakelen, `ShardCount` geeft u een shardtelling op met behulp van de parameter. In het volgende voorbeeld wordt een P1 premium cache met 3 shards. Een P1 premium cache is 6 GB groot en aangezien we drie scherven hebben opgegeven, is de totale grootte 18 GB (3 x 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Als u waarden `RedisConfiguration` voor de parameter `{}` wilt opgeven, sluit `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`u de waarden binnen in als sleutel/waardeparen zoals . In het volgende voorbeeld wordt `allkeys-random` een standaard cache van 1 `KEA`GB met maxmemory-beleid en keyspace-meldingen geconfigureerd met . Zie [Keyspace-meldingen (geavanceerde instellingen)](cache-configure.md#keyspace-notifications-advanced-settings) en [Geheugenbeleid](cache-configure.md#memory-policies)voor meer informatie.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>De databasesinstelling configureren tijdens het maken van cache
De `databases` instelling kan alleen worden geconfigureerd tijdens het maken van de cache. In het volgende voorbeeld wordt een premium P3-cache (26 GB) gemaakt met 48 databases met de cmdlet [Nieuw-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache)

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Zie `databases` [StandaardAzure-cache voor Redis-serverconfiguratie voor](cache-configure.md#default-redis-server-configuration)meer informatie over de eigenschap. Zie de vorige azure-cache voor Redis voor meer informatie over het maken van een cache met de cmdlet [Nieuw-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache)

## <a name="to-update-an-azure-cache-for-redis"></a>Een Azure-cache voor Redis bijwerken
Azure Cache voor Redis-exemplaren worden bijgewerkt met de cmdlet [Set-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache)

Voer de volgende opdracht uit om `Set-AzRedisCache`een lijst met beschikbare parameters en hun beschrijvingen voor te bekijken.

    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

De `Set-AzRedisCache` cmdlet kan worden gebruikt `Size`om `Sku` `EnableNonSslPort`eigenschappen zoals `RedisConfiguration` , , en de waarden bij te werken. 

Met de volgende opdracht wordt het maxmemory-beleid voor de Azure-cache voor Redis met de naam myCache bijgewerkt.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Een Azure-cache voor Redis schalen
`Set-AzRedisCache`kan worden gebruikt om een Azure-cache `Size`voor `Sku`bijvoorbeeld `ShardCount` Redis te schalen wanneer de eigenschappen of eigenschappen worden gewijzigd. 

> [!NOTE]
> Voor het schalen van een cache met PowerShell gelden dezelfde limieten en richtlijnen als het schalen van een cache vanuit de Azure-portal. U schalen naar een andere prijscategorie met de volgende beperkingen.
> 
> * U niet schalen van een hogere prijslaag naar een lagere prijscategorie.
> * U niet schalen van een **Premium-cache** naar een **Standaard-** of **Basic-cache.**
> * U niet schalen van een **standaardcache** naar een **Basic-cache.**
> * U schalen van een **Basic-cache** naar een **standaardcache,** maar u de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, u een volgende schalingbewerking uitvoeren naar de gewenste grootte.
> * U niet rechtstreeks vanuit een **Basic-cache** naar een **Premium-cache** schalen. U moet schalen van **Basic** naar **Standard** in één schalingbewerking en vervolgens van **Standaard** naar **Premium** in een volgende schalingbewerking.
> * U niet schalen van een groter formaat naar de Grootte van de **C0 (250 MB).**
> 
> Zie [Azure-cache schalen voor Redis voor](cache-how-to-scale.md)meer informatie.
> 
> 

In het volgende voorbeeld ziet `myCache` u hoe u een cache schalen die is vernoemd naar een cache van 2,5 GB. Deze opdracht werkt voor zowel een Basic- als een standaardcache.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Nadat deze opdracht is uitgegeven, wordt de status `Get-AzRedisCache`van de cache geretourneerd (vergelijkbaar met bellen). Merk op `ProvisioningState` `Scaling`dat de is .

    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Wanneer de schaalbewerking is `ProvisioningState` voltooid, wordt de wijziging gewijzigd in `Succeeded`. Als u een volgende schalingbewerking moet uitvoeren, zoals het wijzigen van Basic naar Standard en vervolgens de grootte wijzigen, moet u wachten tot de vorige bewerking is voltooid of ontvangt u een fout die vergelijkbaar is met de volgende.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Informatie over een Azure-cache voor Redis
U informatie over een cache ophalen met de cmdlet [Get-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache)

Voer de volgende opdracht uit om `Get-AzRedisCache`een lijst met beschikbare parameters en hun beschrijvingen voor te bekijken.

    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Als u informatie wilt retourneren over `Get-AzRedisCache` alle caches in het huidige abonnement, voert u deze uit zonder parameters.

    Get-AzRedisCache

Voer met de `Get-AzRedisCache` `ResourceGroupName` parameter uit om informatie over alle caches in een specifieke resourcegroep terug te geven.

    Get-AzRedisCache -ResourceGroupName myGroup

Als u informatie over een `Get-AzRedisCache` specifieke `Name` cache wilt retourneren, voert `ResourceGroupName` u de parameter uit die de naam van de cache bevat en de parameter met de brongroep die die cache bevat.

    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>De toegangssleutels voor een Azure-cache voor Redis ophalen
Als u de toegangssleutels voor uw cache wilt ophalen, u de cmdlet [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey) gebruiken.

Voer de volgende opdracht uit om `Get-AzRedisCacheKey`een lijst met beschikbare parameters en hun beschrijvingen voor te bekijken.

    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Als u de sleutels voor `Get-AzRedisCacheKey` uw cache wilt ophalen, roept u de cmdlet aan en geeft u de naam van de brongroep die de cache bevat, de naam van uw cache door.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Toegangssleutels voor uw Azure-cache voor Redis opnieuw genereren
Om de toegangssleutels voor uw cache te regenereren, u de cmdlet [Nieuw-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey) gebruiken.

Voer de volgende opdracht uit om `New-AzRedisCacheKey`een lijst met beschikbare parameters en hun beschrijvingen voor te bekijken.

    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Als u de primaire of secundaire sleutel `New-AzRedisCacheKey` voor uw cache wilt regenereren, roept `Primary` u `Secondary` de `KeyType` cmdlet en -pas aan in de naam, resourcegroep en geeft u een van beide of voor de parameter op. In het volgende voorbeeld wordt de secundaire toegangssleutel voor een cache geregenereerd.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Een Azure-cache voor Redis verwijderen
Als u een Azure-cache voor Redis wilt verwijderen, gebruikt u de cmdlet [Remove-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache)

Voer de volgende opdracht uit om `Remove-AzRedisCache`een lijst met beschikbare parameters en hun beschrijvingen voor te bekijken.

    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

In het volgende voorbeeld `myCache` wordt de naam cache verwijderd.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Een Azure-cache importeren voor Redis
U gegevens importeren in een Azure-cache voor bijvoorbeeld Redis met behulp van de `Import-AzRedisCache` cmdlet.

> [!IMPORTANT]
> Import/export is alleen beschikbaar voor [caches met premiumlagen.](cache-premium-tier-intro.md) Zie [Gegevens importeren en exporteren in Azure-cache voor Redis voor](cache-how-to-import-export-data.md)meer informatie over Importeren/exporteren.
> 
> 

Voer de volgende opdracht uit om `Import-AzRedisCache`een lijst met beschikbare parameters en hun beschrijvingen voor te bekijken.

    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Met de volgende opdracht worden gegevens uit de blob geïmporteerd die door de SAS-uri is opgegeven in Azure-cache voor Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Een Azure-cache exporteren voor Redis
U gegevens exporteren vanuit een Azure-cache voor bijvoorbeeld Redis met behulp van de `Export-AzRedisCache` cmdlet.

> [!IMPORTANT]
> Import/export is alleen beschikbaar voor [caches met premiumlagen.](cache-premium-tier-intro.md) Zie [Gegevens importeren en exporteren in Azure-cache voor Redis voor](cache-how-to-import-export-data.md)meer informatie over Importeren/exporteren.
> 
> 

Voer de volgende opdracht uit om `Export-AzRedisCache`een lijst met beschikbare parameters en hun beschrijvingen voor te bekijken.

    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Met de volgende opdracht worden gegevens uit een Azure-cache voor redis-instantie geëxporteerd naar de container die is opgegeven door de SAS-uri.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Een Azure-cache opnieuw opstarten voor Redis
U uw Azure-cache voor `Reset-AzRedisCache` bijvoorbeeld Redis opnieuw opstarten met de cmdlet.

> [!IMPORTANT]
> Reboot is alleen beschikbaar voor [premium tier](cache-premium-tier-intro.md) caches. Zie [Cachebeheer opnieuw opstarten](cache-administration.md#reboot)voor meer informatie over het opnieuw opstarten van uw cache.
> 
> 

Voer de volgende opdracht uit om `Reset-AzRedisCache`een lijst met beschikbare parameters en hun beschrijvingen voor te bekijken.

    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Met de volgende opdracht worden beide knooppunten van de opgegeven cache opnieuw opgestart.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie over het gebruik van Windows PowerShell met Azure:

* [Azure Cache for Redis cmdlet-documentatie op MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Azure Resource Manager-cmdlets:](https://go.microsoft.com/fwlink/?LinkID=394765)Leer de cmdlets gebruiken in de Azure Resource Manager-module.
* [Resourcegroepen gebruiken om uw Azure-resources te beheren:](../azure-resource-manager/templates/deploy-portal.md)meer informatie over het maken en beheren van resourcegroepen in de Azure-portal.
* [Azure-blog](https://azure.microsoft.com/blog/): Meer informatie over nieuwe functies in Azure.
* [Windows PowerShell-blog:](https://blogs.msdn.com/powershell)meer informatie over nieuwe functies in Windows PowerShell.
* ["Hé, Scripting Guy!" Blog:](https://blogs.technet.com/b/heyscriptingguy/)Ontvang tips en trucs uit de echte wereld van de Windows PowerShell-community.

