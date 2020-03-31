---
title: Firewallregels beheren - Azure CLI - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u Azure Database voor MySQL-firewallregels maakt en beheert met azure cli-opdrachtregel.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 11aa4a80deba4df14c239e69910ea38bac1b9c55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063516"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Azure Database voor MySQL-firewallregels maken en beheren met behulp van de Azure CLI
Firewallregels op serverniveau kunnen worden gebruikt om de toegang tot een Azure Database voor MySQL Server te beheren vanaf een specifiek IP-adres of een reeks IP-adressen. Met handige Azure CLI-opdrachten u firewallregels maken, bijwerken, verwijderen, aanbieden en weergeven om uw server te beheren. Zie [Azure Database voor MySQL-serverfirewalls voor](./concepts-firewall-rules.md)een overzicht van Azure Database voor MySQL-serverfirewallregels .

VNet-regels (Virtual Network) kunnen ook worden gebruikt om de toegang tot uw server te beveiligen. Meer informatie over [het maken en beheren van eindpunten en regels voor virtual network-service met behulp van azure cli](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Vereisten
* [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Een [Azure-database voor MySQL-server en -database](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Opdrachten voor firewallregels:
De opdracht **firewall-rule** van AZ Mysql Server wordt vanuit de Azure CLI gebruikt om firewallregels te maken, verwijderen, weergeven, weergeven en bijwerken.

Opdrachten:
- **maken:** maak een Azure MySQL-serverfirewallregel.
- **verwijderen:** een Azure MySQL-serverfirewallregel verwijderen.
- **lijst:** Vermeld de Azure MySQL-serverfirewallregels.
- **weergeven:** De details van een Azure MySQL-serverfirewallregel weergeven.
- **update**: Een Azure MySQL-serverfirewallregel bijwerken.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Meld u aan bij Azure en vermelduw Azure Database voor MySQL-servers
Verbind Azure CLI veilig met uw Azure-account met behulp van de **inlogopdracht AZ.**

1. Voer in de opdrachtregel de volgende opdracht uit:
    ```azurecli
    az login
    ```
   Met deze opdracht wordt een code uitgevoerd die in de volgende stap moet worden gebruikt.

2. Gebruik een webbrowser om [https://aka.ms/devicelogin](https://aka.ms/devicelogin)de pagina te openen en voer de code in.

3. Meld u bij de prompt aan met uw Azure-referenties.

4. Nadat uw aanmelding is geautoriseerd, wordt een lijst met abonnementen afgedrukt in de console. Kopieer de ID van het gewenste abonnement om het huidige abonnement in te stellen. Gebruik de opdracht [az-accountset.](/cli/azure/account#az-account-set)
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Vermeld de Azure Databases voor MySQL-servers voor uw abonnements- en brongroep als u niet zeker bent van de namen. Gebruik de opdracht [az mysql-serverlijst.](/cli/azure/mysql/server#az-mysql-server-list)

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Let op het naamkenmerk in de vermelding, waarop u de MySQL-server moet opgeven waaraan u moet werken. Bevestig indien nodig de details voor die server en gebruik het naamkenmerk om ervoor te zorgen dat deze correct is. Gebruik de opdracht [az mysql server show.](/cli/azure/mysql/server#az-mysql-server-show)

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Firewallregels aanbieden in Azure Database voor MySQL Server 
Met de servernaam en de naam van de brongroep worden de bestaande serverfirewallregels op de server weergegeven. Gebruik de opdracht [az mysql server firewall list.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list)  Merk op dat het kenmerk servernaam is opgegeven in de **--server-switch** en niet in de **--naamschakelaar.** 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
De uitvoer geeft de eventuele regels weer in JSON-indeling (standaard). U de **uitvoertabelschakelaar** gebruiken om de resultaten in een leesbaretabelindeling uit te schakelen.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Een firewallregel maken op Azure Database voor MySQL Server
Maak met de naam van de Azure MySQL-server en de naam van de brongroep een nieuwe firewallregel op de server. Gebruik de opdracht [az mysql server firewall create.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) Geef een naam op voor de regel, evenals het begin-IP- en eind-IP (om toegang te bieden tot een reeks IP-adressen) voor de regel.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Als u toegang wilt verlenen voor één IP-adres, geeft u hetzelfde IP-adres op als het IP-begin en het ip-einde, zoals in dit voorbeeld.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Als u wilt toestaan dat toepassingen van Azure IP-adressen verbinding maken met uw Azure Database voor MySQL-server, geeft u het IP-adres 0.0.0 als het ip-ip-begin en het eind-IP op, zoals in dit voorbeeld.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

Na succes geeft elke opdrachtuitvoer van maken de details weer van de firewallregel die u hebt gemaakt, in JSON-indeling (standaard). Als er een fout optreedt, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Een firewallregel bijwerken op Azure Database voor MySQL-server 
Met de naam van de Azure MySQL-server en de naam van de brongroep werkt u een bestaande firewallregel op de server bij. Gebruik de opdracht voor het bijwerken van [de AZ Mysql-serverfirewall.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) Geef de naam van de bestaande firewallregel op als invoer, evenals de begin-IP- en eind-IP-kenmerken die moeten worden bijgewerkt.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Na succes geeft de opdrachtuitvoer de details weer van de firewallregel die u hebt bijgewerkt, in JSON-indeling (standaard). Als er een fout optreedt, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

> [!NOTE]
> Als de firewallregel niet bestaat, wordt de regel gemaakt door de opdracht Bijwerken.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Details van firewallregels weergeven in Azure Database voor MySQL Server
Met de naam van de Azure MySQL-server en de naam van de brongroep worden de bestaande details van de firewallregel van de server weergegeven. Gebruik de opdracht [az mysql server firewall show.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) Geef de naam van de bestaande firewallregel op als invoer.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Na succes geeft de opdrachtuitvoer de details weer van de firewallregel die u hebt opgegeven, in JSON-indeling (standaard). Als er een fout optreedt, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Een firewallregel verwijderen in Azure Database voor MySQL Server
Verwijder met de naam van de Azure MySQL-server en de naam van de brongroep een bestaande firewallregel van de server. Gebruik de opdracht [voor het verwijderen van de AZ Mysql-serverfirewall.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) Geef de naam op van de bestaande firewallregel.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Bij succes is er geen output. Bij het mislukken wordt de tekst van het foutbericht weergegeven.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Database voor MySQL Server-firewallregels](./concepts-firewall-rules.md).
- [Azure Database voor MySQL-firewallregels maken en beheren met behulp van de Azure-portal.](./howto-manage-firewall-using-portal.md)
- Verdere veilige toegang tot uw server door [eindpunten en regels voor virtual network-service te maken en te beheren met behulp van de Azure CLI.](howto-manage-vnet-using-cli.md)