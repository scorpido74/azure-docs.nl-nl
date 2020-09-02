---
title: 'Quickstart: Server maken - Azure CLI - Azure Database for PostgreSQL - Eén server'
description: In deze quickstart gaat u een Azure Database for PostgreSQL-server maken met behulp van Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 182226a0bb97c9239f1c0d0dc6e7d4d2b8bdbb1c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798870"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Quickstart: Een Azure Database for PostgreSQL-server maken met behulp van Azure CLI

In deze quickstart wordt beschreven hoe u [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)-opdrachten in [Azure Cloud Shell](https://shell.azure.com) gebruikt om binnen ongeveer vijf minuten één Azure Database for PostgreSQL-server te maken. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!TIP]
> Overweeg het gebruik van de eenvoudigere Azure CLI-opdracht [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), die momenteel als preview-versie beschikbaar is. Probeer de [quickstart](./quickstart-create-server-up-azure-cli.md).

## <a name="prerequisites"></a>Vereisten
Voor dit artikel moet u Azure CLI versie 2.0 of hoger lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login). Zoals u kunt zien, verwijst de eigenschap **id** naar de **Abonnements-id** voor uw Azure-account. 

```azurecli-interactive
az login
```

Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Noteer de **id**-waarde uit de uitvoer van **az login** en gebruik deze als de waarde voor het argument **abonnement** in de opdracht. 

```azurecli
az account set --subscription <subscription id>
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met behulp van de opdracht [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) en maak vervolgens de PostgreSQL-server in deze resourcegroep. U moet een unieke naam opgeven. In het volgende voorbeeld wordt een resourcegroep met de naam `myresourcegroup` gemaakt op de locatie `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Maak een [Azure Database for PostgreSQL-server](overview.md) met behulp van de opdracht [az postgres server create](/cli/azure/postgres/server). Een server kan meerdere databases bevatten.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Dit zijn de gegevens van de voorgaande argumenten: 

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Een unieke naam ter identificatie van uw Azure Database for PostgreSQL-server. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet 3 tot 63 tekens bevatten.
resource-group | myResourceGroup | De naam van de Azure-resourcegroep.
location | westus | De Azure-locatie voor de server.
admin-user | myadmin | De gebruikersnaam voor aanmelding als beheerder. Deze gebruikersnaam mag niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.
admin-password | *veilig wachtwoord* | Het wachtwoord van de beheerder. Het moet 8 tot 128 tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.
sku-name|GP_Gen5_2| De naam van de prijscategorie en de berekeningsconfiguratie. Volg de verkorte notatie voor de conventie {prijscategorie} _{compute-generatie}_ {vCores}. Zie [Prijzen voor Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/) voor meer informatie.

>[!IMPORTANT] 
>- De standaardversie van PostgreSQL op uw server is 9.6. Zie [Supported PostgreSQL major versions](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) (Primaire ondersteunde versies van PostgreSQL) voor een overzicht van alle ondersteunde versies.
>- Als u alle argumenten voor de opdracht **az postgres server create** wilt weergeven, raadpleegt u [dit referentiedocument](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).
>- SSL is standaard ingeschakeld op de server. Zie [SSL-connectiviteit configureren](./concepts-ssl-connection-security.md) voor meer informatie over SSL.

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren 
De door u gemaakte server is standaard beveiligd met firewallregels en is niet openbaar toegankelijk. U kunt de firewallregels op uw server configureren met behulp van de opdracht [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule), zodat uw lokale omgeving toegang tot de server krijgt en verbinding kan maken. 

In het volgende voorbeeld wordt een firewallregel met de naam `AllowMyIP` gemaakt, die verbindingen van een specifiek IP-adres, 192.168.0.1, toestaat. Vervang dit adres door het IP-adres of de reeks IP-adressen die overeenkomen met het IP-adres waarmee u verbinding gaat maken. Als u uw IP-adres niet weet, gaat u naar [WhatIsMyIPAddress.com](https://whatismyipaddress.com/) om het te zoeken.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Zorg ervoor dat de firewall van uw netwerk poort 5432 toestaat om verbindingsproblemen te voorkomen. Azure Database for PostgreSQL-servers gebruiken die poort. 

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, geeft u hostgegevens en toegangsreferenties op.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Het resultaat wordt in JSON-indeling weergegeven. Noteer de waarden voor **administratorLogin** en **fullyQualifiedDomainName**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Verbinding maken met de Azure Database for PostgreSQL-server met behulp van psql
De [psql](https://www.postgresql.org/docs/current/static/app-psql.html)-client is een populaire keuze voor het verbinding maken met PostgreSQL-servers. U kunt verbinding met uw server maken door [psql](../cloud-shell/overview.md) met Azure Cloud Shell te gebruiken. U kunt psql ook in uw lokale omgeving gebruiken, indien beschikbaar. Er wordt automatisch een lege database, **postgres**, gemaakt met een nieuwe PostgreSQL-server. U kunt deze database gebruiken om verbinding te maken met psql, zoals wordt weergegeven in de volgende code. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Als u liever een URL-pad gebruikt om verbinding te maken met Postgres, URL-codeert u het teken @ in de gebruikersnaam met `%40`. De verbindingsreeks voor psql zou bijvoorbeeld zijn:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Resources opschonen
Als u deze resources niet voor een andere quickstart of zelfstudie nodig hebt, kunt u ze verwijderen met de volgende opdracht. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Als u alleen de zojuist gemaakte server wilt verwijderen, kunt u de opdracht [az postgres server delete](/cli/azure/postgres/server) uitvoeren.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een database migreren met behulp van exporteren en importeren](./howto-migrate-using-export-and-import.md)
> 
> [Een Django-web-app implementeren met PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Verbinding maken met een Node.js-app](./connect-nodejs.md)

