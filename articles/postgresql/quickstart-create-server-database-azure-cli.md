---
title: 'Snelstart: Server maken - Azure CLI - Azure Database voor PostgreSQL - Single Server'
description: Quickstart-handleiding voor het maken van een Azure Database voor PostgreSQL - Single Server met Azure CLI (command line interface).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2019
ms.custom: mvc
ms.openlocfilehash: ed78d3dd4e6fbde10c69403cc3dcff24072dc676
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75358037"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Snelstart: een Azure-database maken voor PostgreSQL - Eén server met de Azure CLI

> [!TIP]
> Overweeg de eenvoudigere [az-postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-opdracht te gebruiken (momenteel in preview). Probeer de [quickstart.](./quickstart-create-server-up-azure-cli.md)

Azure Database voor PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. Deze Quick Start laat zien hoe u een Azure-database voor PostgreSQL-server kunt maken in een [Azure resourcegroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) met de Azure CLI.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli). 

Als je de CLI lokaal uitvoert, moet je je aanmelden bij je account met de opdracht [AZ login.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Let op de eigenschap **ID** in de opdrachtuitvoer voor de bijbehorende abonnementsnaam.
```azurecli-interactive
az login
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Vervang de **id-eigenschap** van de **az-inloguitvoer** voor uw abonnement in de tijdelijke aanduiding voor de gebruikersnaam.
```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de opdracht [az group create](/cli/azure/group). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd. U moet een unieke naam opgeven. In het volgende voorbeeld wordt een resourcegroep met de naam `myresourcegroup` gemaakt op de locatie `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Maak een [Azure-database voor PostgreSQL-server](overview.md) met behulp van de opdracht [az postgres server create](/cli/azure/postgres/server). Een server kan meerdere databases bevatten.


**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
name | mydemoserver | Kies een unieke naam ter identificatie van uw Azure-database voor PostgreSQL-server. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
sku-name | GP_Gen5_2 | De naam van de SKU. Volgt de verkorte notatie voor conventie {prijscategorie}\_{compute-generatie}\_{vCores}. Onder deze tabel vindt u meer informatie over de parameter sku-name.
backup-retention | 7 | Hoe lang een back-up moet worden bewaard. De eenheid is dagen. Het bereik is 7-35. 
geo-redundant-backup | Uitgeschakeld | Of geografisch redundante back-ups moeten worden ingeschakeld voor deze server. Toegestane waarden: Ingeschakeld, Uitgeschakeld.
location | westus | De Azure-locatie voor de server.
ssl-enforcement | Ingeschakeld | Of ssl moet worden ingeschakeld voor deze server. Toegestane waarden: Ingeschakeld, Uitgeschakeld.
storage-size | 51.200 | De opslagcapaciteit van de server (eenheid is MB). De minimale opslaggrootte is 5120 MB en deze kunt u ophogen in stappen van 1024 MB. Raadpleeg het document [Prijscategorieën](./concepts-pricing-tiers.md) voor meer informatie over opslaglimieten. 
versie | 9.6 | De primaire versie van PostgreSQL.
admin-user | myadmin | De gebruikersnaam voor aanmelding als beheerder. De aanmeldingsnaam van de beheerder mag niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.
admin-password | *veilig wachtwoord* | Het wachtwoord van het beheerdersaccount. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens.


De parameterwaarde voor de sku-naam volgt de conventie {prijscategorie} \_{compute-generatie}\_{vCores}, zoals in de onderstaande voorbeelden:
+ `--sku-name B_Gen5_1` komt overeen met Basic, Gen 5 en 1 vCore. Deze optie is de kleinst beschikbare SKU.
+ `--sku-name GP_Gen5_32` komt overeen met Algemeen gebruik, Gen 5 en 32 vCores.
+ `--sku-name MO_Gen5_2` komt overeen met Geoptimaliseerd voor geheugen, Gen 5 en 2 vCores.

Raadpleeg de documentatie over [prijscategorieën](./concepts-pricing-tiers.md) om de geldige waarden per regio en categorie te begrijpen.

In het volgende voorbeeld wordt een PostgreSQL 9.6-server in VS - west gemaakt met de naam `mydemoserver` in uw resourcegroep `myresourcegroup` met aanmeldgegevens van de serverbeheerder `myadmin`. Dit is een **Gen 4-server voor ** **Algemeen gebruik** met **twee vCores**. Vervang het `<server_admin_password>` door uw eigen waarde.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!NOTE]
> Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload. Servers die zijn gemaakt in de prijscategorie Basic kunnen later niet meer worden geschaald voor Algemeen gebruik of Geoptimaliseerd voor geheugen. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/postgresql/) voor meer informatie.
> 

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren

Maak een Azure PostgreSQL-firewallregel op serverniveau met de opdracht [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). Met een firewallregel op serverniveau kan een externe toepassing, zoals [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) of [PgAdmin](https://www.pgadmin.org/) verbinding maken met uw server via de firewall van de Azure PostgreSQL-service. 

U kunt een firewallregel voor een IP-bereik zodat u vanaf uw netwerk verbinding kunt maken. In het volgende voorbeeld wordt [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) gebruikt om een firewallregel `AllowMyIP` te maken voor één IP-adres.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> De Azure PostgreSQL-server communiceert via poort 5432. Als u verbinding maakt vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan door de firewall van uw netwerk. Vraag aan de IT-afdeling of ze poort 5432 willen openen, zodat u verbinding kunt maken met uw Azure PostgreSQL-server.

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

## <a name="connect-to-postgresql-database-using-psql"></a>Verbinding maken met een PostgreSQL-database met behulp van psql

Als op uw clientcomputer PostgreSQL is geïnstalleerd, kunt u een lokale instantie van [psql](https://www.postgresql.org/docs/current/static/app-psql.html) gebruiken om verbinding te maken met een Azure PostgreSQL-server. We gaan nu het opdrachtregelprogramma psql gebruiken om verbinding te maken met de Azure PostgreSQL-server.

1. Voer de volgende psql-opdracht uit om verbinding te maken met een Azure-database voor PostgreSQL-server
   ```bash
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met de standaarddatabase **postgres** op uw PostgreSQL-server **mydemoserver.postgres.database.azure.com** met behulp van toegangsreferenties. Voer het `<server_admin_password>` in dat u koos toen u werd gevraagd om een wachtwoord.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Als u liever een URL-pad gebruikt om verbinding te maken met Postgres, codeert URL het @ teken in de gebruikersnaam met `%40`. Bijvoorbeeld de verbindingstekenreeks voor psql zou zijn,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


2. Wanneer u met de server bent verbonden, maakt u bij de prompt een lege database.
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. Voer na de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database **mypgsqldb**:
   ```sql
   \c mypgsqldb
   ```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Verbinding maken met de PostgreSQL-server met behulp van pgAdmin

pgAdmin is een open-source hulpprogramma voor PostgreSQL. U kunt pgAdmin installeren vanaf de [pgAdmin-website](https://www.pgadmin.org/). Mogelijk gebruikt u een andere versie van pgAdmin dan de versie in deze snelstart. Raadpleeg de documentatie van pgAdmin als u extra hulp nodig hebt.

1. Start de toepassing pgAdmin op uw clientcomputer.

2. Ga naar **Object** op de werkbalk, wijs **Create** aan met de muisaanwijzer en selecteer **Server**.

3. In het dialoogvenster **Create - Server** gaat u naar het tabblad **General** en voert u een unieke beschrijvende naam in voor de server, zoals **mydemoserver**.

   ![Het tabblad General](./media/quickstart-create-server-database-azure-cli/9-pgadmin-create-server.png)

4. In het dialoogvenster **Create - Server** gaat u naar het tabblad **Connection** en vult u de tabel met instellingen in.

   ![Het tabblad Connection](./media/quickstart-create-server-database-azure-cli/10-pgadmin-create-server.png)

    Parameter pgAdmin |Waarde|Beschrijving
    ---|---|---
    Host name/address | Servernaam | De servernaam die u hebt gebruikt toen u eerder de Azure Database for PostgreSQL-server hebt gemaakt. De server in ons voorbeeld is **mydemoserver.postgres.database.azure.com**. Gebruik de volledig gekwalificeerde domeinnaam**\*(.postgres.database.azure.com)** zoals in het voorbeeld wordt weergegeven. Als u de servernaam niet meer weet, volgt u de stappen in de vorige sectie om de verbindingsgegevens op te halen. 
    Poort | 5432 | De poort die moet worden gebruikt wanneer u verbinding maakt met de Azure Database for PostgreSQL-server. 
    Onderhoudsdatabase | *postgres* | De door het systeem gegenereerde standaarddatabasenaam.
    Gebruikersnaam | Aanmeldingsnaam van serverbeheerder | De aanmeldingsnaam van de serverbeheerder die u hebt opgegeven toen u de Azure Database for PostgreSQL-server eerder hebt gemaakt. Als u de gebruikersnaam niet meer weet, volgt u de stappen in de vorige sectie om de verbindingsgegevens op te halen. De indeling is *\@gebruikersnaam servernaam*.
    Wachtwoord | Uw beheerderswachtwoord | Het wachtwoord dat u hebt gekozen toen u eerder in deze Quick Start de server maakte.
    Rol | Leeg laten | U hoeft op dit moment geen rolnaam op te geven. Laat het veld leeg.
    SSL-modus | *Require* | U de SSL-modus instellen op het SSL-tabblad van pgAdmin. Standaard worden alle Azure Database voor PostgreSQL-servers gemaakt waarbij SSL-handhaving is ingeschakeld. Raadpleeg [SSL afdwingen](./concepts-ssl-connection-security.md) als u geforceerd SSL wilt uitschakelen.
    
5. Selecteer **Opslaan**.

6. Vouw in het linkerdeelvenster **Browser** het knooppunt **Server** uit. Selecteer uw server, bijvoorbeeld **mydemoserver**. Klik erop om er verbinding mee te maken.

7. Vouw het knooppunt Servers uit en vouw vervolgens **Databases** eronder uit. De lijst moet uw bestaande *postgres*-database bevatten, plus eventuele andere databases die u hebt gemaakt. U kunt met Azure Database for PostgreSQL meerdere databases per server maken.

8. Klik met de rechtermuisknop op **Databases**, kies het menu **Create** en selecteer **Database**.

9. Typ een naam voor de database in het veld **Database**, bijvoorbeeld **mypgsqldb2**.

10. Selecteer bij **Owner** de eigenaar voor de database uit de keuzelijst. Kies de aanmeldingsnaam van de serverbeheerder, zoals **my admin** uit het voorbeeld.

    ![Een database maken in pgadmin](./media/quickstart-create-server-database-azure-cli/11-pgadmin-database.png)

11. Klik op **Save** om een nieuwe, lege database te maken.

12. In het deelvenster **Browser** kunt u de database die u hebt gemaakt, zien in de lijst met databases onder de naam van uw server.




## <a name="clean-up-resources"></a>Resources opschonen

Verwijder alle resources die u in deze Quick Start hebt gemaakt door de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) te verwijderen.

> [!TIP]
> Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u door wilt gaan met andere Quick Starts, verwijdert u de resources die u in deze Quick Start hebt gemaakt, niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die tijdens deze Quick Start in de Azure CLI zijn gemaakt.

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

