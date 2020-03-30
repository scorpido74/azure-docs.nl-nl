---
title: Virtuele netwerkregels gebruiken - Azure CLI - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u VNet-serviceeindpunten en -regels voor Azure Database voor PostgreSQL maakt en beheert met behulp van de opdrachtregel Azure CLI.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 5e80b48e43e72bc3eda16a5dcc64f980065c923f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974112"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>VNet-serviceeindpunten voor Azure Database voor PostgreSQL - Single Server maken en beheren met Azure CLI
Eindpunten en regels voor Virtual Network -services breiden de privéadresruimte van een virtueel netwerk uit tot uw Azure-database voor PostgreSQL-server. Met handige CLI-opdrachten (Azure Command Line Interface) u vnet-serviceeindpunten en -regels maken, bijwerken, verwijderen, aanbieden en weergeven om uw server te beheren. Zie [Azure Database voor PostgreSQL Server VNet-serviceeindpunten voor](concepts-data-access-and-security-vnet.md)een overzicht van Azure Database voor PostgreSQL VNet-serviceeindpunten. VNet-serviceeindpunten zijn beschikbaar in alle ondersteunde regio's voor Azure Database voor PostgreSQL.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- Installeer [de Azure CLI](/cli/azure/install-azure-cli) of gebruik de Azure Cloud Shell in de browser.
- Een [Azure-database voor PostgreSQL-server en -database](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Ondersteuning voor VNet-serviceeindpunten is alleen voor servers met algemeen gebruik en geheugengeoptimaliseerd.
> Als vnet-peering in het geval van VNet-peering door een gemeenschappelijke VNet-gateway met serviceeindpunten stroomt en naar de peer moet stromen, maakt u een ACL/VNet-regel waarmee Azure Virtual Machines in de Gateway VNet toegang krijgen tot de Azure Database voor PostgreSQL-server.


## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Vnet-serviceeindpunten configureren voor Azure Database voor PostgreSQL
De vnet-commando's [van het AZ-netwerk](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) worden gebruikt om virtuele netwerken te configureren.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). 

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
> Het wordt ten zeerste aanbevolen om dit artikel over serviceeindpuntconfiguraties en -overwegingen te lezen voordat u het voorbeeldscript hieronder uitvoert of serviceeindpunten configureert. **Eindpunt van de virtuele netwerkservice:** Een [eindpunt van de service voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschapswaarden een of meer formele namen van azure-servicetypen bevatten. VNet-serviceseindpunten gebruiken de servicenaam **Microsoft.Sql**, die verwijst naar de Azure-service met de naam SQL Database. Deze servicetag is ook van toepassing op de Azure SQL Database, Azure Database voor PostgreSQL- en MySQL-services. Het is belangrijk op te merken dat bij het toepassen van de **Microsoft.Sql-servicetag** op een VNet-serviceeindpunt serviceeindpunt wordt geconfigureerd voor alle Azure Database-services, waaronder Azure SQL Database, Azure Database voor PostgreSQL en Azure Database voor MySQL-servers op het subnet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Voorbeeldscript om een Azure Database voor PostgreSQL-database te maken, een VNet- en VNet-serviceeindpunt te maken en de server te beveiligen met het subnet met een VNet-regel
Wijzig in dit voorbeeldscript de gemarkeerde regels om de gebruikersnaam en het wachtwoord van de beheerder aan te passen. Vervang de SubscriptionID `az account set --subscription` die in de opdracht wordt gebruikt door uw eigen abonnements-id.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md