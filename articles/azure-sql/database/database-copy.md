---
title: Een database kopiëren
description: Een transactioneel consistente kopie maken van een bestaande data base in Azure SQL Database op dezelfde server of op een andere server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 07/27/2020
ms.openlocfilehash: 4dd27a5d3bca5ca1c0395feb049d5a814211c539
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87309253"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Een transactioneel consistente kopie van een data base in Azure SQL Database kopiëren

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database biedt verschillende methoden voor het maken van een kopie van een bestaande [Data Base](single-database-overview.md) op dezelfde server of op een andere server. U kunt een Data Base kopiëren met behulp van Azure Portal, Power shell, Azure CLI of T-SQL.

## <a name="overview"></a>Overzicht

Een database kopie is een transactionele consistente moment opname van de bron database vanaf het moment dat de Kopieer aanvraag wordt gestart. U kunt dezelfde server of een andere server voor de kopie selecteren. U kunt er ook voor kiezen om de servicelaag en de berekenings grootte van de bron database te hand haven of een andere reken grootte te gebruiken binnen dezelfde of een andere servicelaag. Nadat de kopie is voltooid, wordt deze een volledig functionele, onafhankelijke data base. De aanmeldingen, gebruikers en machtigingen in de gekopieerde Data Base worden onafhankelijk van de bron database beheerd. De kopie wordt gemaakt met behulp van de geo-replicatie technologie. Zodra de replica-seeding is voltooid, wordt de koppeling met geo-replicatie automatisch beëindigd. Alle vereisten voor het gebruik van geo-replicatie zijn van toepassing op de Kopieer bewerking van de data base. Zie [overzicht van actieve geo-replicatie](active-geo-replication-overview.md) voor meer informatie.

## <a name="logins-in-the-database-copy"></a>Aanmeldingen in de database kopie

Wanneer u een Data Base naar dezelfde server kopieert, kunnen dezelfde aanmeldingen voor beide data bases worden gebruikt. De beveiligings-principal die u gebruikt om de data base te kopiëren, wordt de data base-eigenaar van de nieuwe data base.

Wanneer u een Data Base naar een andere server kopieert, wordt de beveiligingsprincipal die de Kopieer bewerking heeft gestart op de doel server de eigenaar van de nieuwe data base.

Ongeacht de doel server worden alle database gebruikers, hun machtigingen en hun beveiligings-id's (Sid's) gekopieerd naar de kopie van de data base. Wanneer u [Inge sloten database gebruikers](logins-create-manage.md) gebruikt voor gegevens toegang, zorgt u ervoor dat de gekopieerde data base dezelfde gebruikers referenties heeft, zodat u, nadat de kopie is voltooid, u deze onmiddellijk kunt openen met dezelfde referenties.

Als u aanmeldingen op server niveau gebruikt voor gegevens toegang en de Data Base naar een andere server kopieert, werkt de op aanmelding gebaseerde toegang mogelijk niet. Dit kan gebeuren omdat de aanmeldingen niet bestaan op de doel server of omdat hun wacht woord en de beveiligings-id's (Sid's) verschillen. Zie [Azure SQL database beveiliging beheren na nood herstel](active-geo-replication-security-configure.md)voor meer informatie over het beheren van aanmeldingen wanneer u een Data Base naar een andere server kopieert. Nadat de Kopieer bewerking naar een andere server is geslaagd, en voordat andere gebruikers opnieuw worden toegewezen, kan alleen de aanmelding die is gekoppeld aan de eigenaar van de data base of de server beheerder zich aanmelden bij de gekopieerde data base. Zie [aanmeldingen oplossen](#resolve-logins)om aanmeldingen op te lossen en gegevens toegang tot stand te brengen nadat de Kopieer bewerking is voltooid.

## <a name="copy-using-the-azure-portal"></a>Kopiëren met Azure Portal

Als u een Data Base wilt kopiëren met behulp van de Azure Portal, opent u de pagina voor uw data base en klikt u vervolgens op **kopiëren**.

   ![Data base kopiëren](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Kopiëren met behulp van Power shell of de Azure CLI

Gebruik de volgende voor beelden om een Data Base te kopiëren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voor Power shell gebruikt u de cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> De module Power shell Azure Resource Manager (RM) wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft oplossingen ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit.

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Het kopiëren van de data base is een asynchrone bewerking, maar de doel database wordt gemaakt onmiddellijk nadat de aanvraag is geaccepteerd. Als u de Kopieer bewerking wilt annuleren terwijl deze nog wordt uitgevoerd, verwijdert u de doel database met behulp van de cmdlet [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

Zie [een Data Base naar een nieuwe server kopiëren](scripts/copy-database-to-new-server-powershell.md)voor een volledig voor beeld Power shell-script.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Het kopiëren van de data base is een asynchrone bewerking, maar de doel database wordt gemaakt onmiddellijk nadat de aanvraag is geaccepteerd. Als u de Kopieer bewerking wilt annuleren terwijl deze nog wordt uitgevoerd, verwijdert u de doel database met behulp van de opdracht [AZ SQL DB delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

## <a name="copy-using-transact-sql"></a>Kopiëren met behulp van Transact-SQL

Meld u aan bij de hoofd database met de aanmelding van de server beheerder of de aanmelding die de data base heeft gemaakt die u wilt kopiëren. Het kopiëren van de data base slaagt alleen als de server beheerder lid is van de `dbmanager` rol. Zie [aanmeldingen beheren](logins-create-manage.md)voor meer informatie over aanmeldingen en het maken van een verbinding met de server.

Kopiëren van de bron database starten met de [Data Base maken... ALS kopie van](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) de instructie. De T-SQL-instructie blijft actief totdat de Kopieer bewerking van de data base is voltooid.

> [!NOTE]
> Als u de T-SQL-instructie beëindigt, wordt de Kopieer bewerking van de data base niet beëindigd. Als u de bewerking wilt beëindigen, verwijdert u de doel database.
>

### <a name="copy-to-the-same-server"></a>Kopiëren naar dezelfde server

Meld u aan bij de hoofd database met de aanmelding van de server beheerder of de aanmelding die de data base heeft gemaakt die u wilt kopiëren. Om het kopiëren van de data base te volt ooien, moeten aanmeldingen die niet de server beheerder zijn, lid zijn van de `dbmanager` rol.

Met deze opdracht kopieert u Database1 naar een nieuwe Data Base met de naam Database2 op dezelfde server. Afhankelijk van de grootte van uw data base kan het enige tijd duren voordat de Kopieer bewerking is voltooid.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-a-different-server"></a>Kopiëren naar een andere server

Meld u aan bij de hoofd database van de doel server waarop u de nieuwe Data Base wilt maken. Gebruik een aanmelding met dezelfde naam en hetzelfde wacht woord als de data base-eigenaar van de bron database op de bron server. De aanmelding op de doel server moet ook lid zijn van de `dbmanager` rol of de aanmelding van de server beheerder zijn.

Met deze opdracht kopieert u Database1 op server1 naar een nieuwe Data Base met de naam Database2 op server2. Afhankelijk van de grootte van uw data base kan het enige tijd duren voordat de Kopieer bewerking is voltooid.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> De firewalls van beide servers moeten zodanig worden geconfigureerd dat binnenkomende verbindingen vanaf het IP-adres van de client die de T-SQL-data base uitgeven, worden uitgegeven... ALS kopie van opdracht.

### <a name="copy-to-a-different-subscription"></a>Kopiëren naar een ander abonnement

U kunt de stappen in de sectie [een SQL database kopiëren naar een andere server](#copy-to-a-different-server) gebruiken om uw Data Base naar een server in een ander abonnement te kopiëren met T-SQL. Zorg ervoor dat u een aanmelding met dezelfde naam en hetzelfde wacht woord gebruikt als de data base-eigenaar van de bron database. Daarnaast moet de aanmelding lid zijn van de `dbmanager` rol of een server beheerder op zowel de bron-als de doel server.

> [!NOTE]
> De [Azure Portal](https://portal.azure.com), Power shell en de Azure cli bieden geen ondersteuning voor het kopiëren van data bases naar een ander abonnement.

## <a name="monitor-the-progress-of-the-copying-operation"></a>De voortgang van de Kopieer bewerking bewaken

Bewaak het kopieer proces door een query uit te geven op de weer gaven [sys. data bases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys. dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)en [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . Terwijl het kopiëren wordt uitgevoerd, wordt de kolom **state_desc** van de weer gave sys. data bases voor de nieuwe data base ingesteld op **kopiëren**.

* Als het kopiëren mislukt, wordt de kolom **state_desc** van de weer gave sys. data bases voor de nieuwe data base ingesteld op **verdacht**. Voer de instructie DROP uit op de nieuwe data base en probeer het later opnieuw.
* Als het kopiëren is voltooid, wordt de kolom **state_desc** van de weer gave sys. data bases voor de nieuwe data base ingesteld op **online**. Het kopiëren is voltooid en de nieuwe Data Base is een reguliere data base die onafhankelijk van de bron database kan worden gewijzigd.

> [!NOTE]
> Als u besluit het kopiëren te annuleren terwijl deze wordt uitgevoerd, voert u de instructie [Drop data base](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) uit op de nieuwe data base.

> [!IMPORTANT]
> Als u een kopie moet maken met een aanzienlijk kleinere service doelstelling dan de bron, is het mogelijk dat de doel database onvoldoende resources heeft om het seeding proces te volt ooien. Dit kan ertoe leiden dat de Kopieer bewerking mislukt. In dit scenario gebruikt u een geo-Restore-aanvraag om een kopie te maken op een andere server en/of een andere regio. Zie [een Azure SQL database herstellen met behulp van database back-ups](recovery-using-backups.md#geo-restore) voor meer informatie.

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC-rollen voor het beheren van de database kopie

Als u een database kopie wilt maken, moet u de volgende rollen hebben

* Abonnements eigenaar of
* SQL Server rol Inzender of
* Aangepaste rol op de bron-en doel database met de volgende machtiging:

   Micro soft. SQL/servers/data bases/Lees micro soft. SQL/servers/data bases/write

Als u een kopie van een Data Base wilt annuleren, moet u de volgende rollen hebben

* Abonnements eigenaar of
* SQL Server rol Inzender of
* Aangepaste rol op de bron-en doel database met de volgende machtiging:

   Micro soft. SQL/servers/data bases/Lees micro soft. SQL/servers/data bases/write

Als u het kopiëren van de data base met behulp van de Azure Portal wilt beheren, hebt u ook de volgende machtigingen nodig:

   Micro soft. resources/abonnementen/resources/Lees micro soft. resources/abonnementen/resources/schrijf micro soft. resources/implementaties/Lees micro soft. resources/implementaties/schrijf micro soft. resources/implementaties/operationstatuses/lezen

Als u de bewerkingen wilt zien onder implementaties in de resource groep op de portal, bewerkingen in meerdere resource providers, waaronder SQL-bewerkingen, hebt u de volgende extra RBAC-rollen nodig:

   Micro soft. resources/abonnementen/ResourceGroups/implementaties/bewerkingen/Lees micro soft. resources/abonnementen/ResourceGroups/implementaties/operationstatuses/lezen

## <a name="resolve-logins"></a>Aanmeldingen oplossen

Nadat de nieuwe Data Base online is op de doel server, gebruikt u de instructie [Alter User](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) om de gebruikers van de nieuwe data base toe te wijzen aan aanmeldingen op de doel server. Zie [problemen met zwevende gebruikers oplossen](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)voor informatie over het oplossen van zwevende gebruikers. Zie ook [hoe u Azure SQL database beveiliging beheert na nood herstel](active-geo-replication-security-configure.md).

Alle gebruikers in de nieuwe data base behouden de machtigingen die ze in de bron database hadden. De gebruiker die de database kopie heeft gestart, wordt de data base-eigenaar van de nieuwe data base. Nadat het kopiëren is voltooid en voordat andere gebruikers opnieuw zijn toegewezen, kan alleen de eigenaar van de data base zich aanmelden bij de nieuwe data base.

Zie [Azure SQL database beveiliging beheren na nood herstel](active-geo-replication-security-configure.md)voor meer informatie over het beheren van gebruikers en aanmeldingen wanneer u een Data Base naar een andere server kopieert.

## <a name="database-copy-errors"></a>Database kopie fouten

De volgende fouten zijn opgetreden tijdens het kopiëren van een data base in Azure SQL Database. Zie [Een Azure SQL Database kopiëren](database-copy.md) voor meer informatie.

| Foutcode | Ernst | Beschrijving |
| ---:| ---:|:--- |
| 40635 |16 |De client met het IP-adres%. &#x2a;LS is tijdelijk uitgeschakeld. |
| 40637 |16 |Het maken van de data base is momenteel uitgeschakeld. |
| 40561 |16 |Het kopiëren van de data base is mislukt. De bron-of doel database bestaat niet. |
| 40562 |16 |Het kopiëren van de data base is mislukt. De bron database is verwijderd. |
| 40563 |16 |Het kopiëren van de data base is mislukt. De doel database is verwijderd. |
| 40564 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het opnieuw. |
| 40565 |16 |Het kopiëren van de data base is mislukt. Er is niet meer dan één gelijktijdige database kopie van dezelfde bron toegestaan. Verwijder de doel database en probeer het later opnieuw. |
| 40566 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het opnieuw. |
| 40567 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het opnieuw. |
| 40568 |16 |Het kopiëren van de data base is mislukt. De bron database is niet meer beschikbaar. Verwijder de doel database en probeer het opnieuw. |
| 40569 |16 |Het kopiëren van de data base is mislukt. De doel database is niet meer beschikbaar. Verwijder de doel database en probeer het opnieuw. |
| 40570 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het later opnieuw. |
| 40571 |16 |Het kopiëren van de data base is mislukt vanwege een interne fout. Verwijder de doel database en probeer het later opnieuw. |

## <a name="next-steps"></a>Volgende stappen

* Zie [aanmeldingen beheren](logins-create-manage.md) en [Azure SQL database beveiliging beheren na nood herstel](active-geo-replication-security-configure.md)voor meer informatie over aanmeldingen.
* Zie [de data base exporteren naar een BACPAC](database-export.md)om een Data Base te exporteren.
