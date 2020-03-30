---
title: VNet-eindpunten beheren - Azure CLI - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u Azure Database maakt en beheert voor eindpunten en regels van MariaDB VNet-service met azure CLI-opdrachtregel.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 46bfab6935d08ac28ced7f392892ade6f68a0492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530849"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Azure Database maken en beheren voor MariaDB VNet-serviceeindpunten met Azure CLI

Service-eindpunten en -regels voor virtuele netwerken (VNets) breiden de privé-adresruimte van een virtueel netwerk uit naar uw Azure Database for MariaDB-server. Met handige CLI-opdrachten (Azure Command Line Interface) u vnet-serviceeindpunten en -regels maken, bijwerken, verwijderen, aanbieden en weergeven om uw server te beheren. Zie [Azure Database voor VNet-serviceeindpunten voor](concepts-data-access-security-vnet.md)Een overzicht van Azure Database voor MariaDB-database voor MariaDB VNet-serviceeindpunten voor Een overzicht van Azure Database voor MariaDB VNet-serviceeindpunten, inclusief beperkingen. VNet-serviceeindpunten zijn beschikbaar in alle ondersteunde regio's voor Azure Database voor MariaDB.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- Installeer [de Azure CLI](/cli/azure/install-azure-cli) of gebruik de Azure Cloud Shell in de browser.
- Een [Azure-database voor MariaDB-server en -database](quickstart-create-mariadb-server-database-using-azure-cli.md).

> [!NOTE]
> Ondersteuning voor VNet-serviceeindpunten is alleen voor servers met algemeen gebruik en geheugengeoptimaliseerd.

## <a name="configure-vnet-service-endpoints"></a>VNet-serviceeindpunten configureren
De vnet-commando's [van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) worden gebruikt om virtuele netwerken te configureren.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). 

Als u de CLI lokaal uitvoert, moet u zich aanmelden bij uw account met behulp van de opdracht [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Let op de **id**-eigenschap van de opdrachtuitvoer voor de naam van het desbetreffende abonnement.
```azurecli-interactive
az login
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Gebruik de **id**-eigenschap uit de **az login**-uitvoer voor uw abonnement in de tijdelijke aanduiding voor de abonnement-id.

- Het account moet beschikken over de benodigde machtigingen voor het maken van een virtueel netwerk en een service-eindpunt.

Serviceeindpunten kunnen onafhankelijk van elkaar worden geconfigureerd op virtuele netwerken, door een gebruiker met schrijftoegang tot het virtuele netwerk.

Om Azure-serviceresources te beveiligen voor een VNet, moet de gebruiker toestemming hebben om "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" te gebruiken voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde service-beheerdersrollen en kan worden gewijzigd door aangepaste rollen te maken.

Meer informatie over [ingebouwde rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

VNets en Azure-serviceresources kunnen in hetzelfde abonnement of in verschillende abonnementen zitten. Als de VNet- en Azure-serviceresources zich in verschillende abonnementen bevinden, moeten de resources onder dezelfde AD-tenant (Active Directory) staan. Controleer of beide abonnementen de **Microsoft.Sql-resourceprovider** hebben geregistreerd. Voor meer informatie verwijzen [resource-manager-registratie][resource-manager-portal]

> [!IMPORTANT]
> Het wordt ten zeerste aanbevolen om dit artikel over serviceeindpuntconfiguraties en -overwegingen te lezen voordat serviceeindpunten worden geconfigureerd. **Eindpunt van de virtuele netwerkservice:** Een [eindpunt van de service voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschapswaarden een of meer formele namen van azure-servicetypen bevatten. VNet-serviceseindpunten gebruiken de servicenaam **Microsoft.Sql**, die verwijst naar de Azure-service met de naam SQL Database. Deze servicetag is ook van toepassing op de Azure SQL Database, Azure Database voor MariaDB, PostgreSQL en MySQL-services. Het is belangrijk op te merken dat bij het toepassen van de **Microsoft.Sql-servicetag** op een VNet-serviceeindpunt serviceeindpunt wordt geconfigureerd voor alle Azure Database-services, waaronder Azure SQL Database, Azure Database voor PostgreSQL, Azure Database voor MariaDB en Azure Database voor MySQL-servers op het subnet.

### <a name="sample-script"></a>Voorbeeldscript

Dit voorbeeldscript wordt gebruikt om een Azure Database voor MariaDB-server te maken, een VNet- en VNet-serviceeindpunt te maken en de server te beveiligen met het subnet met een VNet-regel. Wijzig in dit voorbeeldscript de gebruikersnaam en het wachtwoord van de beheerder. Vervang de SubscriptionID `az account set --subscription` die in de opdracht wordt gebruikt door uw eigen abonnements-id.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md