---
title: Azure-cache voor Redis beheren met Azure-klassieke CLI
description: Lees hoe u de klassieke CLI van Azure op elk platform installeert, hoe u deze gebruiken om verbinding te maken met uw Azure-account en hoe u een Azure-cache voor Redis maken en beheren vanuit het klassieke CLI.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: e2b1ed693ea57e3414d465a57a5ba2b1203f67c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277984"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Azure-cache voor Redis maken en beheren met de klassieke CLI van Azure
> [!div class="op_single_selector"]
> * [Powershell](cache-how-to-manage-redis-cache-powershell.md)
> * [Klassieke versie van Azure CLI](cache-manage-cli.md)
>

De Azure-klassieke CLI is een geweldige manier om uw Azure-infrastructuur vanaf elk platform te beheren. In dit artikel ziet u hoe u uw Azure-cache voor Redis-exemplaren maakt en beheert met behulp van de klassieke AZURE-CLI.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Zie [Azure CLI Azure Cache for Redis-voorbeelden voor](cli-samples.md)de nieuwste Azure CLI-voorbeeldscripts .

## <a name="prerequisites"></a>Vereisten
Als u Azure Cache voor Redis-exemplaren wilt maken en beheren met Azure classic CLI, moet u de volgende stappen uitvoeren.

* U moet een Azure-account hebben. Als je nog geen account hebt, kun je in een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aanmaken.
* [Installeer de Azure-klassieke CLI](../cli-install-nodejs.md).
* Verbind uw Azure CLI-installatie met een persoonlijk Azure-account of met een Azure-account `azure login` voor werk of school en meld u met de opdracht aan vanuit de klassieke CLI.
* Voordat u een van de volgende opdrachten uitvoert, schakelt u `azure config mode arm` de klassieke CLI in resourcebeheermodus door de opdracht uit te voeren. Zie [De klassieke CLI van Azure gebruiken om Azure-bronnen en resourcegroepen te beheren](../xplat-cli-azure-resource-manager.md)voor meer informatie.

## <a name="azure-cache-for-redis-properties"></a>Eigenschappen van Azure Cache voor Redis
De volgende eigenschappen worden gebruikt bij het maken en bijwerken van Azure Cache voor Redis-exemplaren.

| Eigenschap | Switch | Beschrijving |
| --- | --- | --- |
| name |-n, --naam |Naam van de Azure-cache voor Redis. |
| resourcegroep |-g, --resource-groep |Naam van de resourcegroep. |
| location |-l, --locatie |Locatie om cache te maken. |
| grootte |-z, --size |Grootte van de Azure-cache voor Redis. Geldige waarden: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |Redis-SKU. Moet een van : [Basic, Standaard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |De eigenschap NonSslPort van de Azure-cache voor Redis inschakelen. Deze vlag toevoegen als u de niet-SSL-poort voor uw cache wilt inschakelen |
| Redis-configuratie |-c, --redis-configuratie |Redis-configuratie. Voer hier een json-opgemaakte reeks configuratiesleutels en -waarden in. Opmaak:"{":""""":":"""}" |
| Redis-configuratie |-f, --redis-configuratie-bestand |Redis-configuratie. Voer hier het pad in van een bestand met configuratiesleutels en -waarden. Opmaak voor de bestandsinvoer: {"":""""":""""} |
| Shard telling |-r, --shard-telling |Aantal shards dat moet worden gemaakt op een Premium-clustercache met clustering. |
| Virtual Network |-v, --virtueel-netwerk |Wanneer u uw cache host in een VNET, geeft u de exacte ARM-bron-id van het virtuele netwerk op om de Azure-cache voor Redis te implementeren. Voorbeeldnotatie: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| toetstype |-t, --key-type |Type sleutel om te vernieuwen. Geldige waarden: [Primair, Secundair] |
| StaticIP (StaticIP) |-p, --static-ip \<static-ip\> |Wanneer u uw cache host in een VNET, geeft u een uniek IP-adres op in het subnet voor de cache. Indien niet voorzien, wordt er een voor u gekozen uit het subnet. |
| Subnet |t, --subnet \<subnet subnet\> |Wanneer u uw cache host in een VNET, geeft u de naam op van het subnet waarin de cache moet worden geïmplementeerd. |
| VirtualNetwork |-v, --virtueel-netwerk \<virtueel-netwerk\> |Wanneer u uw cache host in een VNET, geeft u de exacte ARM-bron-id van het virtuele netwerk op om de Azure-cache voor Redis te implementeren. Voorbeeldnotatie: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Abonnement |-s, --abonnement |De abonnements-id. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Alle Opdrachten voor Azure-cache voor Redis weergeven
Als u alle Opdrachten voor Azure Cache voor `azure rediscache -h` Redis en hun parameters wilt bekijken, gebruikt u de opdracht.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Cache for Redis(s)
    help:
    help:    Create an Azure Cache for Redis
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Azure Cache for Redis
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Azure Cache for Redis
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Azure Cache for Redis
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis-instantie maken
Als u een Azure-cache voor Redis wilt maken, gebruikt u de volgende opdracht:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Voer de `azure rediscache create -h` opdracht uit voor meer informatie over deze opdracht.

    C:\>azure rediscache create -h
    help:    Create an Azure Cache for Redis
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-azure-cache-for-redis"></a>Een bestaande Azure-cache voor Redis verwijderen
Als u een Azure-cache voor Redis wilt verwijderen, gebruikt u de volgende opdracht:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Voer de `azure rediscache delete -h` opdracht uit voor meer informatie over deze opdracht.

    C:\>azure rediscache delete -h
    help:    Delete an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Alle Azure-cache voor Redis weergeven in uw abonnements- of brongroep
Als u alle Azure-cache voor Redis wilt weergeven in uw abonnement of brongroep, gebruikt u de volgende opdracht:

    azure rediscache list [options]

Voer de `azure rediscache list -h` opdracht uit voor meer informatie over deze opdracht.

    C:\>azure rediscache list -h
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Eigenschappen van een bestaande Azure-cache voor Redis weergeven
Als u eigenschappen van een bestaande Azure-cache voor Redis wilt weergeven, gebruikt u de volgende opdracht:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Voer de `azure rediscache show -h` opdracht uit voor meer informatie over deze opdracht.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Instellingen van een bestaande Azure-cache voor Redis wijzigen
Als u de instellingen van een bestaande Azure-cache voor Redis wilt wijzigen, gebruikt u de volgende opdracht:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Voer de `azure rediscache set -h` opdracht uit voor meer informatie over deze opdracht.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>De verificatiesleutel voor een bestaande Azure-cache voor Redis verlengen
Als u de verificatiesleutel voor een bestaande Azure-cache voor Redis wilt verlengen, gebruikt u de volgende opdracht:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Opgeven `Primary` `Secondary` of `key-type`voor .

Voer de `azure rediscache renew-key -h` opdracht uit voor meer informatie over deze opdracht.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Primaire en secundaire sleutels van een bestaande Azure-cache voor Redis weergeven
Als u primaire en secundaire sleutels van een bestaande Azure-cache voor Redis wilt weergeven, gebruikt u de volgende opdracht:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Voer de `azure rediscache list-keys -h` opdracht uit voor meer informatie over deze opdracht.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
