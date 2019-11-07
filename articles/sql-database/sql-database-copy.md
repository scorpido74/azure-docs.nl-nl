---
title: Een Azure-SQL database kopiëren
description: Een transactioneel consistente kopie maken van een bestaande Azure-SQL database op dezelfde server of op een andere server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 09/04/2019
ms.openlocfilehash: d49896d8088ae1352cb2785d061cde6c8647cb89
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690803"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Een transactioneel consistente kopie van een Azure-SQL database kopiëren

Azure SQL Database biedt verschillende methoden voor het maken van een transactioneel consistente kopie van een bestaande Azure SQL database ([één data base](sql-database-single-database.md)) op dezelfde server of een andere server. U kunt een SQL database kopiëren met behulp van de Azure Portal, Power shell of T-SQL. 

## <a name="overview"></a>Overzicht

Een database kopie is een moment opname van de bron database vanaf het moment dat de Kopieer aanvraag wordt gekopieerd. U kunt dezelfde server of een andere server selecteren. U kunt er ook voor kiezen om de servicelaag en de berekenings grootte te hand haven of een andere reken grootte te gebruiken binnen dezelfde servicelaag (Edition). Nadat de kopie is voltooid, wordt deze een volledig functionele, onafhankelijke data base. Op dit moment kunt u de upgrade uitvoeren naar een wille keurige versie of deze Down graden. De aanmeldingen, gebruikers en machtigingen kunnen onafhankelijk worden beheerd. De kopie wordt gemaakt met de geo-replicatie technologie en zodra de seeding is voltooid, wordt de koppeling naar geo-replicatie automatisch beëindigd. Alle vereisten voor het gebruik van geo-replicatie zijn van toepassing op de Kopieer bewerking van de data base. Zie [overzicht van actieve geo-replicatie](sql-database-active-geo-replication.md) voor meer informatie.

> [!NOTE]
> [Automatische database back-ups](sql-database-automated-backups.md) worden gebruikt wanneer u een kopie van een Data Base maakt.

## <a name="logins-in-the-database-copy"></a>Aanmeldingen in de database kopie

Wanneer u een Data Base naar dezelfde SQL Database Server kopieert, kunnen dezelfde aanmeldingen voor beide data bases worden gebruikt. De beveiligings-principal die u gebruikt om de data base te kopiëren, wordt de data base-eigenaar van de nieuwe data base. Alle database gebruikers, hun machtigingen en hun beveiligings-id's (Sid's) worden gekopieerd naar de kopie van de data base.  

Wanneer u een Data Base naar een andere SQL Database Server kopieert, wordt de beveiligingsprincipal op de nieuwe server de data base-eigenaar van de nieuwe data base. Als u [Inge sloten database gebruikers](sql-database-manage-logins.md) gebruikt voor gegevens toegang, moet u ervoor zorgen dat zowel de primaire als de secundaire data base altijd dezelfde gebruikers referenties hebben, zodat u deze meteen kunt openen met dezelfde referenties als de kopie is voltooid. 

Als u [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)gebruikt, kunt u de nood zaak voor het beheren van referenties in de kopie volledig elimineren. Wanneer u de data base echter naar een nieuwe server kopieert, werkt de op aanmelding gebaseerde toegang mogelijk niet, omdat de aanmeldingen niet op de nieuwe server aanwezig zijn. Voor meer informatie over het beheren van aanmeldingen wanneer u een Data Base naar een andere SQL Database Server kopieert, raadpleegt u [Azure SQL database Security beheren na nood herstel](sql-database-geo-replication-security-config.md). 

Nadat het kopiëren is voltooid en voordat andere gebruikers opnieuw zijn toegewezen, kan alleen de aanmeldings gegevens die de Kopieer bewerking hebben gestart, de eigenaar van de data base aanmelden bij de nieuwe data base. Als u aanmeldingen wilt oplossen nadat de Kopieer bewerking is voltooid, raadpleegt u [Aanmelden oplossen](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Een Data Base kopiëren met behulp van de Azure Portal

Als u een Data Base wilt kopiëren met behulp van de Azure Portal, opent u de pagina voor uw data base en klikt u vervolgens op **kopiëren**. 

   ![Data base kopiëren](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Een Data Base kopiëren met behulp van Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u een Data Base wilt kopiëren met behulp van Power shell, gebruikt u de cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) . 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Zie [een Data Base naar een nieuwe server kopiëren](scripts/sql-database-copy-database-to-new-server-powershell.md)voor een volledig voorbeeld script.

Het kopiëren van de data base is een asynchrone bewerking, maar de doel database wordt gemaakt onmiddellijk nadat de aanvraag is geaccepteerd. Als u de Kopieer bewerking wilt annuleren terwijl deze nog wordt uitgevoerd, verwijdert u de doel database met behulp van de cmdlet [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .  

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC-rollen voor het beheren van de database kopie

Als u een database kopie wilt maken, moet u de volgende rollen hebben

- Abonnements eigenaar of
- SQL Server rol Inzender of
- Aangepaste rol op de bron-en doel database met de volgende machtiging:

   Micro soft. SQL/servers/data bases/lezen   
   Micro soft. SQL/servers/data bases/schrijven   

Als u een kopie van een Data Base wilt annuleren, moet u de volgende rollen hebben

- Abonnements eigenaar of
- SQL Server rol Inzender of
- Aangepaste rol op de bron-en doel database met de volgende machtiging:

   Micro soft. SQL/servers/data bases/lezen   
   Micro soft. SQL/servers/data bases/schrijven   
   
Als u het kopiëren van de data base met Azure Portal wilt beheren, hebt u ook de volgende machtigingen nodig:

&nbsp; &nbsp; &nbsp; micro soft. resources/abonnementen/resources/lezen   
&nbsp; &nbsp; &nbsp; micro soft. resources/abonnementen/Resources/schrijven   
&nbsp; &nbsp; &nbsp; micro soft. resources/implementaties/lezen   
&nbsp; &nbsp; &nbsp; micro soft. resources/implementaties/schrijven   
&nbsp; &nbsp; &nbsp; micro soft. resources/implementaties/operationstatuses/lezen    

Als u de bewerkingen wilt zien onder implementaties in de resource groep op de portal, bewerkingen in meerdere resource providers, waaronder SQL-bewerkingen, hebt u de volgende extra RBAC-rollen nodig: 

&nbsp; &nbsp; &nbsp; micro soft. resources/abonnementen/ResourceGroups/implementaties/Operations/lezen   
&nbsp; &nbsp; &nbsp; micro soft. resources/abonnementen/ResourceGroups/implementaties/operationstatuses/lezen



## <a name="copy-a-database-by-using-transact-sql"></a>Een Data Base kopiëren met behulp van Transact-SQL

Meld u aan bij de hoofd database met de principal-aanmelding op server niveau of de aanmelding waarmee de data base is gemaakt die u wilt kopiëren. Om het kopiëren van de data base te volt ooien, moeten aanmeldingen die niet de principal op server niveau zijn, lid zijn van de DBManager-rol. Zie [aanmeldingen beheren](sql-database-manage-logins.md)voor meer informatie over aanmeldingen en het maken van een verbinding met de server.

Begin met het kopiëren van de bron database met de instructie [Create Data](https://msdn.microsoft.com/library/ms176061.aspx) base. Als u deze instructie uitvoert, wordt het kopiëren van de data base gestart. Omdat het kopiëren van een Data Base een asynchroon proces is, wordt de instructie CREATE data base geretourneerd voordat het kopiëren van de data base is voltooid.

### <a name="copy-a-sql-database-to-the-same-server"></a>Een SQL database naar dezelfde server kopiëren

Meld u aan bij de hoofd database met de principal-aanmelding op server niveau of de aanmelding waarmee de data base is gemaakt die u wilt kopiëren. Om het kopiëren van de data base te volt ooien, moeten aanmeldingen die niet de principal op server niveau zijn, lid zijn van de DBManager-rol.

Met deze opdracht kopieert u Database1 naar een nieuwe Data Base met de naam Database2 op dezelfde server. Afhankelijk van de grootte van uw data base kan het enige tijd duren voordat de Kopieer bewerking is voltooid.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Een SQL database naar een andere server kopiëren

Meld u aan bij de hoofd database van de doel server, de SQL Database-Server waarop de nieuwe Data Base moet worden gemaakt. Gebruik een aanmelding met dezelfde naam en hetzelfde wacht woord als de data base-eigenaar van de bron database op de bron SQL Database Server. De aanmelding op de doel server moet ook lid zijn van de DBManager-rol of de principal-aanmelding op server niveau.

Met deze opdracht kopieert u Database1 op server1 naar een nieuwe Data Base met de naam Database2 op server2. Afhankelijk van de grootte van uw data base kan het enige tijd duren voordat de Kopieer bewerking is voltooid.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> De firewalls van beide servers moeten zodanig worden geconfigureerd dat binnenkomende verbindingen vanaf het IP-adres van de client die de T-SQL COPY-opdracht uitgeven, worden toegestaan.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Een SQL database naar een ander abonnement kopiëren

U kunt de stappen in de vorige sectie gebruiken om uw Data Base naar een SQL Database-Server in een ander abonnement te kopiëren. Zorg ervoor dat u een aanmelding met dezelfde naam en hetzelfde wacht woord gebruikt als de data base-eigenaar van de bron database en dat deze lid is van de DBManager-rol of de principal-aanmelding op server niveau is. 

> [!NOTE]
> De [Azure Portal](https://portal.azure.com) biedt geen ondersteuning voor het kopiëren naar een ander abonnement omdat de Portal de arm API aanroept en de abonnements certificaten gebruikt om toegang te krijgen tot beide servers die betrokken zijn bij geo-replicatie.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>De voortgang van de Kopieer bewerking bewaken

Bewaak het kopieer proces door een query uit te geven op de weer gaven sys. data bases en sys. DM _database_copies. Terwijl het kopiëren wordt uitgevoerd, wordt de kolom **state_desc** van de weer gave sys. data bases voor de nieuwe data base ingesteld op **kopiëren**.

* Als het kopiëren mislukt, wordt de kolom **state_desc** van de weer gave sys. data bases voor de nieuwe data base ingesteld op **verdacht**. Voer de instructie DROP uit op de nieuwe data base en probeer het later opnieuw.
* Als het kopiëren is voltooid, wordt de kolom **state_desc** van de weer gave sys. data bases voor de nieuwe data base ingesteld op **online**. Het kopiëren is voltooid en de nieuwe Data Base is een reguliere data base die onafhankelijk van de bron database kan worden gewijzigd.

> [!NOTE]
> Als u besluit het kopiëren te annuleren terwijl deze wordt uitgevoerd, voert u de instructie [Drop data base](https://msdn.microsoft.com/library/ms178613.aspx) uit op de nieuwe data base. U kunt ook het kopieer proces annuleren door de instructie DROP data base uit te voeren op de bron database.

> [!IMPORTANT]
> Als u een kopie met een aanzienlijk kleinere SLO moet maken dan de bron, heeft de doel database mogelijk niet voldoende bronnen om het seeding proces te volt ooien en kan dit ertoe leiden dat het kopiëren mislukt. In dit scenario gebruikt u een geo-Restore-aanvraag om een kopie te maken op een andere server en/of een andere regio. Zie [een Azure SQL database herstellen met behulp van database back-ups](sql-database-recovery-using-backups.md#geo-restore) voor meer informaing.


## <a name="resolve-logins"></a>Aanmeldingen oplossen

Nadat de nieuwe Data Base online is op de doel server, gebruikt u de instructie [Alter User](https://msdn.microsoft.com/library/ms176060.aspx) om de gebruikers van de nieuwe data base toe te wijzen aan aanmeldingen op de doel server. Zie [problemen met zwevende gebruikers oplossen](https://msdn.microsoft.com/library/ms175475.aspx)voor informatie over het oplossen van zwevende gebruikers. Zie ook [hoe u Azure SQL database-beveiliging beheert na nood herstel](sql-database-geo-replication-security-config.md).

Alle gebruikers in de nieuwe data base behouden de machtigingen die ze in de bron database hadden. De gebruiker die de kopie van de data base heeft gestart, wordt de data base-eigenaar van de nieuwe data base en krijgt een nieuwe beveiligings-id (SID) toegewezen. Nadat het kopiëren is voltooid en voordat andere gebruikers opnieuw zijn toegewezen, kan alleen de aanmeldings gegevens die de Kopieer bewerking hebben gestart, de eigenaar van de data base aanmelden bij de nieuwe data base.

Zie [Azure SQL database-beveiliging beheren na nood herstel](sql-database-geo-replication-security-config.md)voor meer informatie over het beheren van gebruikers en aanmeldingen wanneer u een Data Base naar een andere SQL database server kopieert.

## <a name="next-steps"></a>Volgende stappen

* Zie [aanmeldingen beheren](sql-database-manage-logins.md) en [Azure SQL database-beveiliging beheren na nood herstel](sql-database-geo-replication-security-config.md)voor meer informatie over aanmeldingen.
* Zie [de data base exporteren naar een BACPAC](sql-database-export.md)om een Data Base te exporteren.
