---
title: Een database kopiëren
description: Maak een transactioneel consistente kopie van een bestaande Azure SQL-database op dezelfde server of een andere server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473741"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Een transactioneel consistente kopie van een Azure SQL-database kopiëren

Azure SQL Database biedt verschillende methoden voor het maken van een transactioneel consistente kopie van een bestaande Azure SQL-database[(enkele database)](sql-database-single-database.md)op dezelfde server of een andere server. U een SQL-database kopiëren met de Azure-portal, PowerShell of T-SQL.

## <a name="overview"></a>Overzicht

Een databasekopie is een momentopname van de brondatabase vanaf het moment van de kopieeraanvraag. U dezelfde server of een andere server selecteren. U er ook voor kiezen om de servicelaag en rekengrootte te behouden of een andere rekengrootte te gebruiken binnen dezelfde servicelaag (editie). Nadat de kopie is voltooid, wordt het een volledig functionele, onafhankelijke database. Op dit punt u upgraden of downgraden naar elke editie. De aanmeldingen, gebruikers en machtigingen kunnen onafhankelijk worden beheerd. De kopie wordt gemaakt met behulp van de geo-replicatietechnologie en zodra het zaaien is voltooid, wordt de georeplicatiekoppeling automatisch beëindigd. Alle vereisten voor het gebruik van georeplicatie zijn van toepassing op de bewerking voor databasekopieën. Zie [Overzicht van actieve georeplicatie](sql-database-active-geo-replication.md) voor meer informatie.

> [!NOTE]
> [Geautomatiseerde databaseback-ups](sql-database-automated-backups.md) worden gebruikt wanneer u een databasekopie maakt.

## <a name="logins-in-the-database-copy"></a>Aanmeldingen in de databasekopie

Wanneer u een database kopieert naar dezelfde SQL Database-server, kunnen dezelfde aanmeldingen in beide databases worden gebruikt. De beveiligingsprincipal die u gebruikt om de database te kopiëren, wordt de eigenaar van de database in de nieuwe database. 

Wanneer u een database kopieert naar een andere SQL Database-server, wordt de beveiligingsprincipal die de kopieerbewerking op de doelserver heeft gestart, de eigenaar van de nieuwe database. 

Ongeacht de doelserver worden alle databasegebruikers, hun machtigingen en hun beveiligings-id's (SID's) gekopieerd naar de databasekopie. Het gebruik van [opgenomen databasegebruikers](sql-database-manage-logins.md) voor gegevenstoegang zorgt ervoor dat de gekopieerde database dezelfde gebruikersreferenties heeft, zodat u na het voltooien van de kopie onmiddellijk toegang hebt tot de database met dezelfde referenties.

Als u aanmeldingen op serverniveau gebruikt voor gegevenstoegang en de database naar een andere server kopieert, werkt de toegang op basis van aanmelding mogelijk niet. Dit kan gebeuren omdat de aanmeldingen niet bestaan op de doelserver of omdat hun wachtwoorden en beveiligings-id's (SID's) verschillend zijn. Zie [Azure SQL-databasebeveiliging beheren na herstel na een ramp](sql-database-geo-replication-security-config.md)voor meer informatie over het beheren van aanmeldingen wanneer u een database naar een andere SQL-databaseserver kopieert. Nadat de kopieerbewerking naar een andere server is geslaagd en voordat andere gebruikers opnieuw worden toegewezen, kan alleen de aanmelding die is gekoppeld aan de eigenaar van de database of de serverbeheerder zich aanmelden bij de gekopieerde database. Zie [Aanmeldingen oplossen](#resolve-logins)om aanmeldingen op te lossen en gegevenstoegang tot stand te brengen nadat de kopieerbewerking is voltooid.

## <a name="copy-a-database-by-using-the-azure-portal"></a>Een database kopiëren met de Azure-portal

Als u een database wilt kopiëren met de Azure-portal, opent u de pagina voor uw database en klikt u op **Kopiëren**.

   ![Databasekopiëren](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Een database kopiëren met PowerShell of Azure CLI

Als u een database wilt kopiëren, gebruikt u de volgende voorbeelden.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Gebruik voor PowerShell de cmdlet [Nieuw-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> De PowerShell Azure Resource Manager (RM)-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

De databasekopie is een asynchrone bewerking, maar de doeldatabase wordt direct nadat de aanvraag is geaccepteerd. Als u de kopieerbewerking moet annuleren terwijl u nog aan de gang is, laat u de doeldatabase vallen met de cmdlet [Remove-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Zie [Een database kopiëren naar een nieuwe server voor](scripts/sql-database-copy-database-to-new-server-powershell.md)een volledig voorbeeld van PowerShell-script.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

De databasekopie is een asynchrone bewerking, maar de doeldatabase wordt direct nadat de aanvraag is geaccepteerd. Als u de kopieerbewerking moet annuleren terwijl u nog aan de gang is, laat u de doeldatabase vallen met de opdracht [az sql db delete.](/cli/azure/sql/db#az-sql-db-delete)

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC-rollen voor het beheren van databasekopie

Als u een databasekopie wilt maken, moet u zich in de volgende rollen bevinden

- Eigenaar van een abonnement of
- SQL Server-inzenderrol of
- Aangepaste rol op de bron- en doeldatabases met de volgende toestemming:

   Microsoft.Sql/servers/databases/lees Microsoft.Sql/servers/databases/write

Als u een databasekopie wilt annuleren, moet u zich in de volgende rollen bevinden

- Eigenaar van een abonnement of
- SQL Server-inzenderrol of
- Aangepaste rol op de bron- en doeldatabases met de volgende toestemming:

   Microsoft.Sql/servers/databases/lees Microsoft.Sql/servers/databases/write

Als u databasekopie wilt beheren met Azure-portal, hebt u ook de volgende machtigingen nodig:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Als u de bewerkingen onder implementaties in de resourcegroep op de portal wilt zien, bewerkingen voor meerdere resourceproviders, waaronder SQL-bewerkingen, hebt u deze extra RBAC-rollen nodig:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Een database kopiëren met Transact-SQL

Meld u aan bij de hoofddatabase met de aanmelding van de serverbeheerder of de aanmelding die de database heeft gemaakt die u wilt kopiëren. Als u databasekopie wilt slagen, moeten aanmeldingen die `dbmanager` niet de serverbeheerder zijn, lid zijn van de rol. Zie [Aanmeldingen beheren](sql-database-manage-logins.md)voor meer informatie over aanmeldingen en verbinding maken met de server.

Begin met het kopiëren van de brondatabase met de [CREATE DATABASE ... ALS KOPIE VAN](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) verklaring. De T-SQL-instructie blijft worden uitgevoerd totdat de bewerking voor databasekopieën is voltooid.

> [!NOTE]
> Het beëindigen van de T-SQL-instructie beëindigt de bewerking voor databasekopieën niet. Als u de bewerking wilt beëindigen, laat u de doeldatabase vallen.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>Een SQL-database naar dezelfde server kopiëren

Meld u aan bij de hoofddatabase met de aanmelding van de serverbeheerder of de aanmelding die de database heeft gemaakt die u wilt kopiëren. Als u wilt dat de database kopiëren slaagt, moeten aanmeldingen die niet de serverbeheerder zijn, lid zijn van de `dbmanager` rol.

Met deze opdracht kopieert U Database1 naar een nieuwe database met de naam Database2 op dezelfde server. Afhankelijk van de grootte van uw database kan het enige tijd duren voordat de kopieerbewerking is voltooid.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Een SQL-database naar een andere server kopiëren

Log in bij de hoofddatabase van de doelserver waar de nieuwe database moet worden gemaakt. Gebruik een login met dezelfde naam en wachtwoord als de eigenaar van de database van de brondatabase op de bronserver. De login op de doelserver moet `dbmanager` ook lid zijn van de rol of de serverbeheerder zijn die inlogt.

Met deze opdracht kopieert U Database1 op server1 naar een nieuwe database met de naam Database2 op server2. Afhankelijk van de grootte van uw database kan het enige tijd duren voordat de kopieerbewerking is voltooid.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Firewalls van beide servers moeten zijn geconfigureerd om inkomende verbinding mogelijk te maken vanaf het IP van de client die de T-SQL CREATE DATABASE uitgeeft ... ALS KOPIE VAN de opdracht.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Een SQL-database naar een ander abonnement kopiëren

U de stappen in de [SQL-database kopiëren naar een andere serversectie](#copy-a-sql-database-to-a-different-server) gebruiken om uw database te kopiëren naar een SQL Database-server in een ander abonnement met T-SQL. Zorg ervoor dat u een login gebruikt met dezelfde naam en wachtwoord als de eigenaar van de database van de brondatabase. Bovendien moet de login lid zijn `dbmanager` van de rol of een serverbeheerder, op zowel bron- als doelservers.

> [!NOTE]
> De [Azure-portal,](https://portal.azure.com)PowerShell en Azure CLI ondersteunen geen databasekopie naar een ander abonnement.

### <a name="monitor-the-progress-of-the-copying-operation"></a>De voortgang van de kopieerbewerking controleren

Monitor het kopieerproces door de [sys.databases,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)en [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) weergaven op te vragen. Terwijl het kopiëren aan de gang is, is de **state_desc** kolom van de sys.databases-weergave voor de nieuwe database ingesteld op **KOPIËREN**.

* Als het kopiëren mislukt, wordt de **state_desc** kolom van de sys.databases-weergave voor de nieuwe database ingesteld op **SUSPECT**. Voer de drop-instructie uit in de nieuwe database en probeer het later opnieuw.
* Als het kopiëren slaagt, wordt de **state_desc** kolom van de sys.databases-weergave voor de nieuwe database ingesteld op **ONLINE**. Het kopiëren is voltooid en de nieuwe database is een gewone database die onafhankelijk van de brondatabase kan worden gewijzigd.

> [!NOTE]
> Als u besluit het kopiëren te annuleren terwijl deze nog bezig is, voert u de instructie [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) uit op de nieuwe database.

> [!IMPORTANT]
> Als u een kopie moet maken met een aanzienlijk kleinere servicedoelstelling dan de bron, beschikt de doeldatabase mogelijk niet over voldoende resources om het zaaiproces te voltooien en kan deze ertoe leiden dat de kopieeroperaion mislukt. Gebruik in dit scenario een aanvraag voor geoherstel om een kopie te maken in een andere server en/of een andere regio. Zie [Een Azure SQL-database herstellen met databaseback-ups](sql-database-recovery-using-backups.md#geo-restore) voor meer informatie.

## <a name="resolve-logins"></a>Aanmeldingen oplossen

Nadat de nieuwe database online is op de doelserver, gebruikt u de [WIJZIGING-gebruikersinstructie](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) om de gebruikers van de nieuwe database opnieuw in kaart te brengen naar aanmeldingen op de doelserver. Zie [Zwevende gebruikers oplossen](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)om verweesde gebruikers op te lossen. Zie ook [Hoe azure SQL-databasebeveiliging te beheren na herstel na noodgevallen](sql-database-geo-replication-security-config.md).

Alle gebruikers in de nieuwe database behouden de machtigingen die ze in de brondatabase hadden. De gebruiker die de databasekopie heeft gestart, wordt de eigenaar van de database van de nieuwe database. Nadat het kopiëren is geslaagd en voordat andere gebruikers opnieuw zijn aangesloten, kan alleen de eigenaar van de database zich aanmelden bij de nieuwe database.

Zie Hoe u [azure SQL-databasebeveiliging beheert na herstel na een ramp voor](sql-database-geo-replication-security-config.md)meer informatie over het beheren van gebruikers en aanmeldingen wanneer u een database naar een andere SQL-databaseserver kopieert.

## <a name="database-copy-errors"></a>Fouten in databasekopie

De volgende fouten kunnen worden aangetroffen tijdens het kopiëren van een database in Azure SQL Database. Zie [Een Azure SQL Database kopiëren](sql-database-copy.md) voor meer informatie.

| Foutcode | Severity | Beschrijving |
| ---:| ---:|:--- |
| 40635 |16 |Client met IP-adres '%.&#x2a;ls' is tijdelijk uitgeschakeld. |
| 40637 |16 |Databasekopie maken is momenteel uitgeschakeld. |
| 40561 |16 |Databasekopie is mislukt. De bron- of doeldatabase bestaat niet. |
| 40562 |16 |Databasekopie is mislukt. De brondatabase is verwijderd. |
| 40563 |16 |Databasekopie is mislukt. De doeldatabase is verwijderd. |
| 40564 |16 |Databasekopie is mislukt als gevolg van een interne fout. Laat de doeldatabase vallen en probeer het opnieuw. |
| 40565 |16 |Databasekopie is mislukt. Niet meer dan 1 gelijktijdige databasekopie van dezelfde bron is toegestaan. Laat de doeldatabase vallen en probeer het later opnieuw. |
| 40566 |16 |Databasekopie is mislukt als gevolg van een interne fout. Laat de doeldatabase vallen en probeer het opnieuw. |
| 40567 |16 |Databasekopie is mislukt als gevolg van een interne fout. Laat de doeldatabase vallen en probeer het opnieuw. |
| 40568 |16 |Databasekopie is mislukt. Brondatabase is niet meer beschikbaar. Laat de doeldatabase vallen en probeer het opnieuw. |
| 40569 |16 |Databasekopie is mislukt. Doeldatabase is niet meer beschikbaar. Laat de doeldatabase vallen en probeer het opnieuw. |
| 40570 |16 |Databasekopie is mislukt als gevolg van een interne fout. Laat de doeldatabase vallen en probeer het later opnieuw. |
| 40571 |16 |Databasekopie is mislukt als gevolg van een interne fout. Laat de doeldatabase vallen en probeer het later opnieuw. |

## <a name="next-steps"></a>Volgende stappen

- Zie [Aanmeldingen beheren](sql-database-manage-logins.md) en Azure [SQL-databasebeveiliging beheren na herstel na een ramp](sql-database-geo-replication-security-config.md)voor informatie over aanmeldingen.
- Zie De database [exporteren naar een BACPAC als](sql-database-export.md)u een database wilt exporteren.
