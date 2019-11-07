---
title: XEvent-gebeurtenis bestands code voor SQL Database
description: Biedt Power shell en Transact-SQL voor een code voorbeeld van twee fasen die het gebeurtenis bestand doel in een uitgebreide gebeurtenis op Azure SQL Database demonstreren. Azure Storage is een vereist onderdeel van dit scenario.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 03/12/2019
ms.openlocfilehash: ea5c90433a4d2928e5fb88df149631c80df9dacf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686834"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Doel code van gebeurtenis bestand voor uitgebreide gebeurtenissen in SQL Database

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

U wilt een volledig code voorbeeld voor een robuuste manier om informatie vast te leggen en te rapporteren voor een uitgebreide gebeurtenis.

In Microsoft SQL Server wordt het [gebeurtenis bestand doel](https://msdn.microsoft.com/library/ff878115.aspx) gebruikt om gebeurtenis uitvoer op te slaan in een lokaal vaste-schijf bestand. Maar dergelijke bestanden zijn niet beschikbaar voor Azure SQL Database. In plaats daarvan gebruiken we de Azure Storage-service voor de ondersteuning van het gebeurtenis bestand doel.

In dit onderwerp vindt u een code voorbeeld van twee fasen:

* Power shell om een Azure Storage-container in de cloud te maken.
* Transact-SQL:
  
  * De Azure Storage-container toewijzen aan een gebeurtenis bestand doel.
  * Om de gebeurtenis sessie te maken en te starten, enzovoort.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

* Een Azure-account en -abonnement. U  kunt zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Elke Data Base waarin u een tabel kunt maken.
  
  * U kunt desgewenst binnen enkele minuten [een **AdventureWorksLT** -demonstratie database maken](sql-database-get-started.md) .
* SQL Server Management Studio (SSMS. exe), de meest recente maandelijkse update versie. 
  U kunt de nieuwste versie van SSMS. exe downloaden van:
  
  * Onderwerp met de titel [down load SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Een directe koppeling naar de down load.](https://go.microsoft.com/fwlink/?linkid=616025)
* U moet de [Azure PowerShell modules](https://go.microsoft.com/?linkid=9811175) hebben geïnstalleerd.
  
  * De modules bieden opdrachten zoals- **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: Power shell-code voor Azure Storage container

Deze Power shell is fase 1 van het code voorbeeld in twee fasen.

Het script begint met opdrachten om op te schonen na een mogelijke vorige uitvoering en is rerunnable.

1. Plak het Power shell-script in een eenvoudige tekst editor zoals Notepad. exe en sla het script op als een bestand met de extensie **. ps1**.
2. Start Power shell ISE als beheerder.
3. Typ bij de prompt<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>en druk op ENTER.
4. Open uw **PS1** -bestand in Power shell ISE. Voer het script uit.
5. Het script start eerst een nieuw venster waarin u zich aanmeldt bij Azure.
   
   * Als u het script opnieuw moet uitvoeren zonder de sessie te onderbreken, hebt u de mogelijkheid om de opdracht **add-AzureAccount** uit te voeren.

![Power shell ISE, waarop Azure-module is geïnstalleerd, klaar om script uit te voeren.][30_powershell_ise]

### <a name="powershell-code"></a>Power shell-code

In dit Power shell-script wordt ervan uitgegaan dat u de AZ-module al hebt geïnstalleerd. Zie [de module Azure PowerShell installeren](/powershell/azure/install-Az-ps)voor meer informatie.

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

If ($storageAccountName)
{
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
Try
{
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
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


Noteer de paar benoemde waarden die het Power shell-script afdrukt wanneer het wordt beëindigd. U moet deze waarden bewerken in het Transact-SQL-script dat volgt als fase 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: Transact-SQL-code die gebruikmaakt van Azure Storage-container

* In fase 1 van dit code voorbeeld hebt u een Power shell-script uitgevoerd om een Azure Storage-container te maken.
* Naast fase 2 moet het volgende Transact-SQL-script de container gebruiken.

Het script begint met opdrachten om op te schonen na een mogelijke vorige uitvoering en is rerunnable.

Het Power shell-script heeft enkele benoemde waarden afgedrukt toen het werd beëindigd. U moet het Transact-SQL-script bewerken om deze waarden te gebruiken. Zoek naar de **TODO** in het Transact-SQL-script om de bewerkings punten te vinden.

1. Open SQL Server Management Studio (SSMS. exe).
2. Verbinding maken met uw Azure SQL Database-data base.
3. Klik om een nieuw query deel venster te openen.
4. Plak het volgende Transact-SQL-script in het query deel venster.
5. Zoek elke **TODO** in het script en breng de juiste wijzigingen aan.
6. Sla het script op en voer het vervolgens uit.


> [!WARNING]
> De waarde van de SAS-sleutel die door het vorige Power shell-script wordt gegenereerd, kan beginnen met een '? ' (vraag teken). Wanneer u de SAS-sleutel gebruikt in het volgende T-SQL-script, moet u *de regel '? ' verwijderen*. Anders is het mogelijk dat uw inspanningen worden geblokkeerd door beveiliging.


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


Als het doel niet kan worden gekoppeld wanneer u uitvoert, moet u de gebeurtenis sessie stoppen en opnieuw starten:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Uitvoer

Wanneer het Transact-SQL-script is voltooid, klikt u op een cel onder de kolomkop **event_data_XML** . Er wordt een **\<gebeurtenis >** element weer gegeven met daarin één update-instructie.

Hier volgt een **\<gebeurtenis >** element dat tijdens het testen is gegenereerd:


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


Het voorafgaande Transact-SQL-script gebruikt de volgende systeem functie om de event_file te lezen:

* [sys. fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

Een uitleg van geavanceerde opties voor het weer geven van gegevens uit uitgebreide gebeurtenissen vindt u op:

* [Geavanceerde weer gave van doel gegevens uit uitgebreide gebeurtenissen](https://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Het code voorbeeld wordt geconverteerd om te worden uitgevoerd op SQL Server

Stel dat u het voor gaande Transact-SQL-voor beeld wilt uitvoeren op Microsoft SQL Server.

* Ter vereenvoudiging wilt u het gebruik van de Azure Storage container volledig vervangen door een eenvoudig bestand zoals **C:\myeventdata.Xel**. Het bestand wordt geschreven naar de lokale harde schijf van de computer die als host fungeert voor SQL Server.
* U hebt geen enkele Transact-SQL-instructies nodig voor het **maken van de hoofd sleutel** en het maken van de **referentie**.
* In de instructie voor het maken van een **gebeurtenis sessie** , in de component **Add Target** , vervangt u de http-waarde die is toegewezen aan **filename =** door een teken reeks van het volledige pad, zoals **C:\myfile.Xel**.
  
  * Er hoeft geen Azure Storage account te worden betrokken.

## <a name="more-information"></a>Meer informatie

Zie voor meer informatie over accounts en containers in de Azure Storage-service:

* [Blob-opslag gebruiken met .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Naamgeving van en verwijzen naar containers, blobs en metagegevens](https://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Werken met de basis container](https://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Les 1: een opgeslagen toegangs beleid en een gedeelde toegangs handtekening maken voor een Azure-container](https://msdn.microsoft.com/library/dn466430.aspx)
  * [Les 2: een SQL Server referentie maken met behulp van een hand tekening voor gedeelde toegang](https://msdn.microsoft.com/library/dn466435.aspx)
* [Uitgebreide gebeurtenissen voor Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

