---
title: Server beheren-Azure CLI-Azure Database for PostgreSQL-flexibele server
description: Meer informatie over het beheren van een Azure Database for PostgreSQL flexibele server vanuit de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8f078c2c7b22a27c1fd87030361fc56d4495177c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936770"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Een Azure Database for PostgreSQL-flexibele server beheren met de Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

In dit artikel leest u hoe u uw flexibele server kunt beheren die in Azure is geïmplementeerd. Beheer taken zijn onder andere berekening en opslag schalen, beheerders wachtwoord opnieuw instellen en server details weer geven.

## <a name="prerequisites"></a>Vereisten
Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. In dit artikel moet u Azure CLI-versie 2.0 of later lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login). Let op de eigenschap **id**, die verwijst naar **abonnements-id** voor uw Azure-account.

```azurecli-interactive
az login
```

Selecteer het specifieke abonnement in uw account met de opdracht [az account set](/cli/azure/account). Noteer de **id**-waarde uit de uitvoer van **az login** en gebruik deze als de waarde voor het argument **abonnement** in de opdracht. Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Als u nog geen flexibele server hebt gemaakt, kunt u er een maken om aan de slag te gaan met deze hand leiding.

## <a name="scale-compute-and-storage"></a>Schaal berekening en opslag

U kunt uw Compute-laag, vCores en opslag eenvoudig omhoog schalen met de volgende opdracht. U kunt alle server bewerkingen zien die u kunt uitvoeren [AZ post gres Flexible-server server Overview (Engelstalig)](/cli/azure/PostgreSQL/server)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Hier volgen de detailgegevens voor bovenstaande argumenten:

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Voer een unieke naam in voor uw server. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
sku-name|Standard_D4ds_v3|Voer de naam van de compute-laag en de grootte in. Volgt de Conventie Standard_ {VM size} in steno. Raadpleeg de [prijscategorieën](../concepts-pricing-tiers.md) voor meer informatie.
storage-size | 6144 | De opslagcapaciteit van de server (eenheid is MB). Mini maal 5120 en verhogingen in 1024 stappen.

> [!IMPORTANT]
> De opslag kan niet omlaag worden geschaald. 

## <a name="manage-postgresql-databases-on-a-server"></a>PostgreSQL-data bases op een server beheren

Er zijn een aantal toepassingen die kunt gebruiken om verbinding te maken met uw Azure Database voor PostgreSQL-server. Als op uw clientcomputer PostgreSQL is geïnstalleerd, kunt u een lokale instantie van [psql](https://www.postgresql.org/docs/current/static/app-psql.html) gebruiken om verbinding te maken met een Azure PostgreSQL-server. We gaan nu het opdrachtregelprogramma psql gebruiken om verbinding te maken met de Azure PostgreSQL-server.

1. Voer de volgende psql-opdracht uit om verbinding te maken met een Azure-database voor PostgreSQL-server

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met de standaarddatabase **postgres** op uw PostgreSQL-server **mydemoserver.postgres.database.azure.com** met behulp van toegangsreferenties. Voer het `<server_admin_password>` in dat u koos toen u werd gevraagd om een wachtwoord.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Als de verbinding tot stand is gebracht, wordt er door het hulpprogramma psql een postgres-prompt weergegeven. Hier kunt u sql-opdrachten typen. In de uitvoer van de eerste verbinding kan een waarschuwing worden weergegeven, omdat de psql die u gebruikt mogelijk niet dezelfde versie heeft als de Azure Database for PostgreSQL-server.

   Voorbeeld van psql-uitvoer:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Als de firewall niet is geconfigureerd voor het toestaan van het IP-adres van uw client, wordt het volgende foutbericht weergegeven:
   >
   > 'psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Please specify SSL options and retry.
   >
   > Controleer of het IP-adres van de client is toegestaan in de bovenstaande stap voor firewallregels.

2. Maak een lege data base met de naam ' PostgresDB ' bij de prompt door de volgende opdracht te typen:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Voer bij de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte Data Base- **PostgresDB**:

    ```bash
    \c postgresdb
    ```

4. Typ `\q` en druk vervolgens op Enter om psql af te sluiten.

U hebt nu verbinding gemaakt met de Azure Database for PostgreSQL-server via psql en u hebt een lege gebruikersdatabase gemaakt.

## <a name="reset-admin-password"></a>Beheerders wachtwoord opnieuw instellen
U kunt het wacht woord van de beheerdersrol wijzigen met deze opdracht
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Zorg ervoor dat het wacht woord mini maal acht tekens en Maxi maal 128 tekens bevat.
> Het wacht woord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofd letters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.

## <a name="delete-a-server"></a>Een server verwijderen

Als u de PostgreSQL flexibele server wilt verwijderen, kunt u de opdracht [AZ post gres Flexible-server delete](/cli/azure/PostgreSQL/server#az-PostgreSQL-flexible-server-delete) uitvoeren.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over back-up-en herstel concepten](concepts-backup-restore.md)
- [De server afstemmen en bewaken](concepts-monitoring.md)
