---
title: 'Quickstart: Server maken - Azure CLI - Azure Database for PostgreSQL - Eén server'
description: Quickstart-gids voor het maken van een Azure Database for PostgreSQL-server met behulp van Azure CLI (opdrachtregelinterface).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc
ms.openlocfilehash: d103ed0ebd565df77032237638c775991324ea44
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030136"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Quickstart: Een Azure Database for PostgreSQL-server maken met behulp van Azure CLI

> [!TIP]
> Overweeg het gebruik van de eenvoudigere Azure CLI-opdracht [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) (momenteel als preview-versie). Probeer de [quickstart](./quickstart-create-server-up-azure-cli.md).

In deze quickstart wordt beschreven hoe u [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)-opdrachten in [Azure Cloud Shell](https://shell.azure.com) gebruikt om binnen ongeveer vijf minuten een Azure Database for PostgreSQL-server te maken.  Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren. 

## <a name="prerequisites"></a>Vereisten
In dit artikel moet u Azure CLI-versie 2.0 of later lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login). Zoals u kunt zien verwijst de eigenschap **id** naar de **Abonnements-id** voor uw Azure-account. 

```azurecli-interactive
az login
```

Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Noteer de **id**-waarde uit de uitvoer van **az login** en gebruik deze als de waarde voor het argument **abonnement** in de opdracht. Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met behulp van de opdracht [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) en maak vervolgens de PostgreSQL-server in deze resourcegroep. U moet een unieke naam opgeven. In het volgende voorbeeld wordt een resourcegroep met de naam `myresourcegroup` gemaakt op de locatie `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Maak een [Azure-database voor PostgreSQL-server](overview.md) met behulp van de opdracht [az postgres server create](/cli/azure/postgres/server). Een server kan meerdere databases bevatten.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Hier volgen de detailgegevens voor bovenstaande argumenten: 

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Kies een unieke naam ter identificatie van uw Azure-database voor PostgreSQL-server. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
location | westus | De Azure-locatie voor de server.
admin-user | myadmin | De gebruikersnaam voor aanmelding als beheerder. De aanmeldingsnaam van de beheerder mag niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.
admin-password | *veilig wachtwoord* | Het wachtwoord van het beheerdersaccount. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.
sku-name|GP_Gen5_2|Voer de naam van de prijscategorie en de berekeningsconfiguratie in. Volgt de verkorte notatie voor conventie {prijscategorie} _{compute-generatie}_ {vCores}. Zie [Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/) voor meer informatie.

>[!IMPORTANT] 
>- De standaardversie van PostgreSQL op uw server is 9.6. Bekijk [hier](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) alle versies die worden ondersteund.
>- Als u alle argumenten voor de opdracht **az postgres server create** wilt weergeven, raadpleegt u dit [referentiedocument](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create)
>- SSL is standaard ingeschakeld op de server. Zie [SSL-connectiviteit configureren](./concepts-ssl-connection-security.md) voor meer informatie over SSL

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren 
De nieuwe server die wordt gemaakt, is standaard beveiligd met firewallregels en is niet openbaar toegankelijk. U kunt de firewallregel op uw server configureren met behulp van de opdracht [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) zodat uw lokale omgeving toegang tot de server heeft en verbinding kan maken. 

In het volgende voorbeeld wordt een firewallregel met de naam `AllowMyIP` gemaakt, die verbindingen van een specifiek IP-adres, 192.168.0.1, toestaat. Vervang dit adres door het IP-adres of de reeks IP-adressen die overeenkomen met het IP-adres waarmee u verbinding gaat maken.  Als u niet weet hoe u uw IP-adres kunt opzoeken, gaat u naar [https://whatismyipaddress.com/](https://whatismyipaddress.com/) om uw IP-adres te verkrijgen.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  Zorg ervoor dat de firewall van uw netwerk poort 5432 toestaat. Deze wordt door Azure Database for PostgreSQL-servers gebruikt om verbindingsproblemen te voorkomen. 

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Het resultaat wordt in JSON-indeling weergegeven. Noteer de **aanmeldgegevens van de beheerder** en **fullyQualifiedDomainName**.
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

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Verbinding maken met een Azure Database for PostgreSQL-server via psql
[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) is een populaire client die wordt gebruikt om verbinding te maken met PostgreSQL-servers. U kunt verbinding met uw server maken door **psql** met [Azure Cloud Shell](../cloud-shell/overview.md) te gebruiken. U kunt psql ook in uw lokale omgeving gebruiken, indien beschikbaar. Er is al een lege database, 'postgres', gemaakt met uw nieuwe PostgreSQL-server. U kunt deze gebruiken om verbinding te maken met psql, zoals hieronder wordt weer gegeven 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Als u liever een URL-pad gebruikt om verbinding te maken met Postgres, URL-codeert u het teken @ in de gebruikersnaam met `%40`. De verbindingsreeks voor psql is bijvoorbeeld
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>Resources opschonen
Als u deze resources niet voor een andere quickstart of zelfstudie nodig hebt, kunt u ze verwijderen met de volgende opdracht: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Als u alleen de zojuist gemaakte server wilt verwijderen, kunt u de opdracht [az postgres server delete](/cli/azure/postgres/server) uitvoeren.
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een database migreren met behulp van Exporteren en importeren](./howto-migrate-using-export-and-import.md)
> 
> [Een Django-web-app implementeren met PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Verbinding maken met Node.JS](./connect-nodejs.md)

