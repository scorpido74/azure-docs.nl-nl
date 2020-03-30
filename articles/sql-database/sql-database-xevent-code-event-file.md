---
title: XEvent-gebeurtenisbestandscode
description: Biedt PowerShell en Transact-SQL voor een codevoorbeeld in twee fasen dat het doel van gebeurtenisbestand demonstreert in een uitgebreide gebeurtenis op Azure SQL Database. Azure Storage is een vereist onderdeel van dit scenario.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 03/12/2019
ms.openlocfilehash: a9bf28fb1b3c5278d25b417fc646d2ad3d6f1abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213980"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Doelcode gebeurtenisbestand voor uitgebreide gebeurtenissen in SQL-database

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

U wilt een volledig codevoorbeeld voor een robuuste manier om informatie vast te leggen en te rapporteren voor een uitgebreide gebeurtenis.

In Microsoft SQL Server wordt het [doel gebeurtenisbestand](https://msdn.microsoft.com/library/ff878115.aspx) gebruikt om gebeurtenisuitvoer op te slaan in een lokaal bestand met harde schijf. Dergelijke bestanden zijn echter niet beschikbaar voor Azure SQL Database. In plaats daarvan gebruiken we de Azure Storage-service om het doel van gebeurtenisbestand te ondersteunen.

In dit onderwerp wordt een tweefasencodevoorbeeld gepresenteerd:

- PowerShell, om een Azure Storage-container in de cloud te maken.
- Transact-SQL:
  
  - De Azure Storage-container toewijzen aan een gebeurtenisbestanddoel.
  - De gebeurtenissessie maken en starten, enzovoort.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

- Een Azure-account en -abonnement. U  kunt zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). 
- Elke database waarin u een tabel maken.
  
  - Optioneel u binnen enkele minuten [een **AdventureWorksLT-demonstratiedatabase** maken.](sql-database-get-started.md)

- SQL Server Management Studio (ssms.exe), idealiter de nieuwste maandelijkse updateversie.
  U de nieuwste ssms.exe downloaden van:
  
  - Onderwerp met de titel [SQL Server Management Studio downloaden](https://msdn.microsoft.com/library/mt238290.aspx).
  - [Een directe link naar de download.](https://go.microsoft.com/fwlink/?linkid=616025)

- U moet de [Azure PowerShell-modules](https://go.microsoft.com/?linkid=9811175) hebben geïnstalleerd.

  - De modules bieden opdrachten zoals - **Nieuw-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: PowerShell-code voor Azure Storage-container

Deze PowerShell is fase 1 van het tweefasencodemonster.

Het script begint met opdrachten om op te schonen na een mogelijke vorige run en is opnieuw uittevoeren.

1. Plak het PowerShell-script in een eenvoudige teksteditor zoals Notepad.exe en sla het script op als bestand met de extensie **.ps1**.
2. PowerShell ISE starten als beheerder.
3. Typ bij de prompt<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>en druk vervolgens op Enter.
4. Open in PowerShell ISE het **PS1-bestand.** Voer het script uit.
5. Het script start eerst een nieuw venster waarin u zich aanmeldt bij Azure.

   - Als u het script opnieuw uitvoert zonder uw sessie te verstoren, hebt u de handige optie om de opdracht **Toevoegen-AzureAccount** uit te voeren.

![PowerShell ISE, met Azure-module geïnstalleerd, klaar om script uit te voeren.][30_powershell_ise]

### <a name="powershell-code"></a>PowerShell-code

Dit PowerShell-script gaat ervan uit dat u de Az-module al hebt geïnstalleerd. Zie De [Azure PowerShell-module installeren](/powershell/azure/install-Az-ps)voor meer informatie .

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Let op de weinige benoemde waarden die het PowerShell-script wordt afgedrukt wanneer het afloopt. U moet deze waarden bewerken in het Transact-SQL-script dat volgt als fase 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: Transact-SQL-code die Azure Storage-container gebruikt

- In fase 1 van dit codevoorbeeld hebt u een PowerShell-script uitgevoerd om een Azure Storage-container te maken.
- Vervolgens in fase 2 moet het volgende Transact-SQL-script de container gebruiken.

Het script begint met opdrachten om op te schonen na een mogelijke vorige run en is opnieuw uittevoeren.

Het PowerShell-script heeft een paar benoemde waarden afgedrukt toen het eindigde. U moet het Transact-SQL-script bewerken om deze waarden te gebruiken. Zoek **TODO** in het Transact-SQL-script om de bewerkingspunten te vinden.

1. Open SQL Server Management Studio (ssms.exe).
2. Maak verbinding met uw Azure SQL Database-database.
3. Klik hierom een nieuw queryvenster te openen.
4. Plak het volgende Transact-SQL-script in het queryvenster.
5. Vind elke **TODO** in het script en maak de juiste bewerkingen.
6. Sla op en voer het script uit.

> [!WARNING]
> De SAS-sleutelwaarde die wordt gegenereerd door het vorige PowerShell-script, kan beginnen met een '?' (vraagteken). Wanneer u de SAS-toets in het volgende T-SQL-script gebruikt, moet u *de voorloop '?' verwijderen.* Anders kunnen uw inspanningen worden geblokkeerd door de beveiliging.

### <a name="transact-sql-code"></a>Transact-SQL-code

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Als het doel niet wordt gekoppeld wanneer u wordt uitgevoerd, moet u de gebeurtenissessie stoppen en opnieuw starten:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Uitvoer

Wanneer het Transact-SQL-script is voltooid, klikt u op een cel onder de **kolomkop event_data_XML.** Er ** \<wordt** één gebeurtenis>element weergegeven waarin één update-instructie wordt weergegeven.

Hier is ** \<** een gebeurtenis>element dat is gegenereerd tijdens het testen:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

Het voorgaande Transact-SQL-script gebruikte de volgende systeemfunctie om de event_file te lezen:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

Een uitleg van geavanceerde opties voor het weergeven van gegevens van uitgebreide gebeurtenissen is beschikbaar op:

- [Geavanceerde weergave van doelgegevens van uitgebreide gebeurtenissen](https://msdn.microsoft.com/library/mt752502.aspx)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Het codevoorbeeld converteren naar SQL Server

Stel dat u het voorgaande Transact-SQL-voorbeeld op Microsoft SQL Server wilt uitvoeren.

- Voor de eenvoud wilt u het gebruik van de Azure Storage-container volledig vervangen door een eenvoudig bestand zoals *C:\myeventdata.xel*. Het bestand wordt geschreven naar de lokale harde schijf van de computer die SQL Server host.
- U hebt geen enkele vorm van Transact-SQL-instructies nodig voor **CREATE MASTER KEY** en CREATE **CREDENTIAL.**
- In de instructie **GEBEURTENISSESSIE MAKEN** vervangt u in de component **TARGET TOEVOEGEN** de Http-waarde die is toegewezen aan **bestandsnaam=** door een volledige padtekenreeks zoals *C:\myfile.xel*.
  
  - Er hoeft geen Azure Storage-account bij betrokken te zijn.

## <a name="more-information"></a>Meer informatie

Zie voor meer informatie over accounts en containers in de Azure Storage-service:

- [Blob-opslag gebruiken vanuit .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [Containers, blobs en metagegevens benoemen en verwijzen](https://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Werken met de hoofdcontainer](https://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Les 1: Een opgeslagen toegangsbeleid en een handtekening voor gedeelde toegang maken op een Azure-container](https://msdn.microsoft.com/library/dn466430.aspx)
  - [Les 2: Een SQL Server-referentie maken met behulp van een handtekening voor gedeelde toegang](https://msdn.microsoft.com/library/dn466435.aspx)
- [Uitgebreide gebeurtenissen voor Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!-- Image references. -->
[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png
