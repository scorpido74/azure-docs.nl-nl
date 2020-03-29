---
title: Firewallregels beheren - Azure CLI - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u firewallregels maakt en beheert in Azure Database voor PostgreSQL - Single Server met azure cli-opdrachtregel.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4af0fb288961689fb051bab8091c838f793cfcc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765644"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Firewallregels maken en beheren in Azure Database voor PostgreSQL - Single Server met Azure CLI
Firewallregels op serverniveau kunnen worden gebruikt om de toegang tot een Azure-database voor PostgreSQL Server te beheren vanaf een specifiek IP-adres of een specifiek IP-adres. Met handige Azure CLI-opdrachten u firewallregels maken, bijwerken, verwijderen, aanbieden en weergeven om uw server te beheren. Zie [Azure Database voor PostgreSQL Server-firewallregels voor](concepts-firewall-rules.md)een overzicht van Azure Database voor PostgreSQL Server-firewallregels .

VNet-regels (Virtual Network) kunnen ook worden gebruikt om de toegang tot uw server te beveiligen. Meer informatie over [het maken en beheren van eindpunten en regels voor virtual network-service met behulp van azure cli](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- Installeer Azure CLI-opdrachtregelhulpprogramma's of gebruik de Azure Cloud Shell in de browser. [Azure CLI](/cli/azure/install-azure-cli)
- Een [Azure-database voor PostgreSQL-server en -database](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Firewallregels configureren voor Azure Database voor PostgreSQL
De [firewallregelopdrachten van az postgres server](/cli/azure/postgres/server/firewall-rule) worden gebruikt om firewallregels te configureren.

## <a name="list-firewall-rules"></a>Firewallregels weergeven 
Voer de opdracht voor de lijst met [firewall-regels van az-postgres server](/cli/azure/postgres/server/firewall-rule) uit om de bestaande serverfirewallregels weer te geven.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
De uitvoer geeft standaard een overzicht van de firewallregels in JSON-indeling. U de `--output table` schakelaar gebruiken voor een beter leesbare tabelindeling als uitvoer.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Firewallregel maken
Als u een nieuwe firewallregel op de server wilt maken, voert u de opdracht voor het maken van de [firewall-regel van de AZ-postgres-server uit.](/cli/azure/postgres/server/firewall-rule) 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Als u wilt toestaan dat toepassingen van Azure IP-adressen verbinding maken met uw Azure Database voor PostgreSQL-server, geeft u het IP-adres 0.0.0 als het ip-ip-begin en het eind-IP op, zoals in dit voorbeeld.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

Na succes geeft de opdrachtuitvoer de details weer van de firewallregel die u hebt gemaakt, standaard in JSON-indeling. Als er een fout optreedt, wordt in de uitvoer een foutbericht weergegeven.

## <a name="update-firewall-rule"></a>Firewallregel bijwerken 
Werk een bestaande firewallregel op de server bij met de opdracht [firewall-regelupdate van AZ Postgres Server.](/cli/azure/postgres/server/firewall-rule) Geef de naam van de bestaande firewallregel op als invoer en de ip-ip- en eind-IP-kenmerken voor het starten om bij te werken.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Na succes geeft de opdrachtuitvoer de details weer van de firewallregel die u hebt bijgewerkt, standaard in JSON-indeling. Als er een fout optreedt, wordt in de uitvoer een foutbericht weergegeven.
> [!NOTE]
> Als de firewallregel niet bestaat, wordt deze gemaakt door de opdracht Update.

## <a name="show-firewall-rule-details"></a>Details van firewallregel weergeven
U ook de details van een bestaande firewallregel op serverniveau weergeven door [de opdracht voor de firewall-regel van de AZ-postgresserver te](/cli/azure/postgres/server/firewall-rule) uitvoeren.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Na succes geeft de opdrachtuitvoer de details weer van de firewallregel die u hebt opgegeven, standaard in JSON-indeling. Als er een fout optreedt, wordt in de uitvoer een foutbericht weergegeven.

## <a name="delete-firewall-rule"></a>Firewallregel verwijderen
Als u de toegang voor een IP-bereik voor de server wilt intrekken, verwijdert u een bestaande firewallregel door de firewall-regel verwijderen van de [AZ-postgresserver uit te](/cli/azure/postgres/server/firewall-rule) voeren. Geef de naam op van de bestaande firewallregel.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Bij succes is er geen output. Na het mislukken wordt de foutberichttekst geretourneerd.

## <a name="next-steps"></a>Volgende stappen
- Op dezelfde manier u een webbrowser gebruiken om [Azure Database voor PostgreSQL-firewallregels te maken en te beheren met behulp van de Azure-portal.](howto-manage-firewall-using-portal.md)
- Meer informatie over [Azure Database voor PostgreSQL Server-firewallregels](concepts-firewall-rules.md).
- Verdere veilige toegang tot uw server door [eindpunten en regels voor virtual network-service te maken en te beheren met behulp van de Azure CLI.](howto-manage-vnet-using-cli.md)
- Zie [Verbindingsbibliotheken voor Azure Database voor PostgreSQL](concepts-connection-libraries.md)voor hulp bij het maken van verbinding met een Azure Database voor PostgreSQL.
