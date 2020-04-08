---
title: Een service voor splitsen en samenvoegen implementeren
description: Gebruik de samenvoeging ook om gegevens tussen geshard databases te verplaatsen.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: b6f61de23ab4b637cfb5b8ee365ddea9764bf515
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810202"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Een gesplitste samenvoegservice implementeren om gegevens tussen geshard databases te verplaatsen

Met het gereedschap Splitsen samenvoegen u gegevens verplaatsen tussen geshard databases. Zie [Gegevens verplaatsen tussen geschaalde clouddatabases](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>De split-merge-pakketten downloaden

1. Download de nieuwste NuGet-versie van [NuGet.](https://docs.nuget.org/docs/start-here/installing-nuget)

1. Open een opdrachtprompt en navigeer naar de map waar je nuget.exe hebt gedownload. De download bevat PowerShell-opdrachten.

1. Download het nieuwste Split-Merge-pakket in de huidige map met de onderstaande opdracht:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

De bestanden worden geplaatst in een map met de naam **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** waar *x.x.xxx.x* het versienummer weergeeft. Zoek de split-merge Service-bestanden in de submap **content\splitmerge\service** en de PowerShell-scripts (en vereiste clientdlls) in de **submap content\splitmerge\powershell.**

## <a name="prerequisites"></a>Vereisten

1. Maak een Azure SQL DB-database die wordt gebruikt als de statusdatabase voor gesplitste bewerking. Ga naar de [Azure-portal.](https://portal.azure.com) Maak een nieuwe **SQL-database**. Geef de database een naam en maak een nieuwe beheerder en wachtwoord. Zorg ervoor dat u de naam en het wachtwoord voor later gebruik opte nemen.

1. Zorg ervoor dat azure services op uw Azure SQL-server verbinding kunnen maken. Zorg er in de portal in de **firewall-instellingen**voor dat de instelling **Toegang tot Azure Services toestaan** is ingesteld op **Aan**. Klik op het pictogram Opslaan.

1. Maak een Azure Storage-account voor diagnostische uitvoer.

1. Maak een Azure Cloud Service voor uw Split-Merge service.

## <a name="configure-your-split-merge-service"></a>De service Splitsen samenvoegen configureren

### <a name="split-merge-service-configuration"></a>Serviceconfiguratie voor gesplitst samenvoegen

1. Maak in de map waarin u de samenstellingen van Splitsen samenvoegen hebt gedownload een kopie van het bestand *ServiceConfiguration.Template.cscfg* dat naast *SplitMergeService.cspkg* is verzonden en wijzigt u *serviceConfiguratie.cscfg*.

1. Open *ServiceConfiguration.cscfg* in een teksteditor zoals Visual Studio die invoer valideert, zoals de indeling van certificaatduimafdrukken.

1. Maak een nieuwe database of kies een bestaande database die wordt weergegeven als de statusdatabase voor split-merge-bewerkingen en haal de verbindingstekenreeks van die database op.

   > [!IMPORTANT]
   > Op dit moment moet de statusdatabase de\_Latijnse\_collatie (SQL Latin1 General\_CP1\_CI\_AS) gebruiken. Zie [Windows Collation Name (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx)voor meer informatie.

   Bij Azure SQL DB is de verbindingstekenreeks meestal van het formulier:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Voer deze verbindingstekenreeks in het *bestand .cscfg* in de rolsecties **SplitMergeWeb** en **SplitMergeWorker** in de instelling ElasticScaleMetadata in.

1. Voer voor de rol **SplitMergeWorker** een geldige verbindingstekenreeks in voor Azure-opslag voor de instelling **WorkerRoleSynchronizationStorageAccountConnectionString.**

### <a name="configure-security"></a>Beveiliging configureren

Raadpleeg de [beveiligingsconfiguratie Splitsen samenvoegen](sql-database-elastic-scale-split-merge-security-configuration.md)voor gedetailleerde instructies om de beveiliging van de service te configureren.

Voor de toepassing van een eenvoudige testimplementatie voor deze zelfstudie wordt een minimale set configuratiestappen uitgevoerd om de service operationeel te krijgen. Met deze stappen kunnen alleen de één machine/account die ze uitvoert, met de service communiceren.

### <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Maak een nieuwe map en voer vanuit deze map de volgende opdracht uit met een [opdrachtprompt voor ontwikkelaars voor het](https://msdn.microsoft.com/library/ms229859.aspx) venster Visual Studio:

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

U wordt gevraagd om een wachtwoord om de privésleutel te beschermen. Voer een sterk wachtwoord in en bevestig het. U wordt dan gevraagd om het wachtwoord daarna nog eens te gebruiken. Klik op **Ja** aan het einde om het te importeren in de hoofdmap van vertrouwde certificeringsinstanties.

### <a name="create-a-pfx-file"></a>Een PFX-bestand maken

Voer de volgende opdracht uit vanuit hetzelfde venster waar makecert is uitgevoerd; gebruik hetzelfde wachtwoord dat u hebt gebruikt om het certificaat te maken:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Het clientcertificaat importeren in de persoonlijke winkel

1. Dubbelklik in Windows Verkenner op *MyCert.pfx*.
2. Selecteer in de **wizard Certificaat importeren** de optie Huidige **gebruiker** en klik op **Volgende**.
3. Bevestig het bestandspad en klik op **Volgende**.
4. Typ het wachtwoord, laat **Alle uitgebreide eigenschappen opgeven** ingeschakeld en klik op **Volgende**.
5. Leave **Selecteer automatisch het certificaatarchief[...]** aangevinkt en klik op **Volgende**.
6. Klik **op Voltooien** en **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Het PFX-bestand uploaden naar de cloudservice

1. Ga naar de [Azure-portal.](https://portal.azure.com)
2. Selecteer **Cloudservices**.
3. Selecteer de cloudservice die u hierboven hebt gemaakt voor de split/merge-service.
4. Klik op **Certificaten** in het bovenste menu.
5. Klik **op Uploaden** in de onderste balk.
6. Selecteer het PFX-bestand en voer hetzelfde wachtwoord in als hierboven.
7. Zodra u klaar bent, kopieert u de duimafdruk van het certificaat uit het nieuwe item in de lijst.

### <a name="update-the-service-configuration-file"></a>Het serviceconfiguratiebestand bijwerken

Plak de hierboven gekopieerde certificaatduimafdruk in het kenmerk duimafdruk/waarde van deze instellingen.
Voor de rol van de werknemer:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Voor de webrol:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Houd er rekening mee dat voor productie-implementaties afzonderlijke certificaten moeten worden gebruikt voor de CA, voor versleuteling, het Servercertificaat en clientcertificaten. Zie [Beveiligingsconfiguratie](sql-database-elastic-scale-split-merge-security-configuration.md)voor gedetailleerde instructies hierover.

## <a name="deploy-your-service"></a>Uw service implementeren

1. Ga naar [Azure Portal](https://portal.azure.com)
2. Selecteer de cloudservice die u eerder hebt gemaakt.
3. Klik op **Overzicht**.
4. Kies de faseringsomgeving en klik op **Uploaden.**
5. Voer in het dialoogvenster een implementatielabel in. Klik voor zowel 'Pakket' als 'Configuratie' op 'Van lokaal' en kies het bestand *SplitMergeService.cspkg* en het cscfg-bestand dat u eerder hebt geconfigureerd.
6. Controleer of het selectievakje met het label **Implementeren is ingeschakeld, zelfs als een of meer rollen één instantie bevatten.**
7. Druk op de vinkje rechtsonder om de implementatie te starten. Verwacht dat het een paar minuten in beslag neemt.

## <a name="troubleshoot-the-deployment"></a>Problemen met de implementatie oplossen

Als uw webrol niet online komt, is dit waarschijnlijk een probleem met de beveiligingsconfiguratie. Controleer of de TLS/SSL is geconfigureerd zoals hierboven beschreven.

Als uw werknemersrol niet online komt, maar uw webrol slaagt, is het waarschijnlijk een probleem om verbinding te maken met de statusdatabase die u eerder hebt gemaakt.

- Zorg ervoor dat de verbindingstekenreeks in uw cscfg nauwkeurig is.
- Controleer of de server en database bestaan en of de gebruikersnaam en het wachtwoord correct zijn.
- Voor Azure SQL DB moet de verbindingstekenreeks van het formulier zijn:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Zorg ervoor dat de servernaam niet begint met **https://**.
- Zorg ervoor dat azure services op uw Azure SQL-server verbinding kunnen maken. Open hiervoor uw database in de portal en zorg ervoor dat de instelling **Toegang tot Azure Services toestaan** is ingesteld op **On****..

## <a name="test-the-service-deployment"></a>De service-implementatie testen

### <a name="connect-with-a-web-browser"></a>Verbinding maken met een webbrowser

Bepaal het webeindpunt van uw split-merge-service. U dit vinden in de portal door naar het **overzicht** van uw cloudservice te gaan en onder **Site-URL** aan de rechterkant te kijken. Vervang **http://** door **https://** omdat de standaardbeveiligingsinstellingen het HTTP-eindpunt uitschakelen. Laad de pagina voor deze URL in uw browser.

### <a name="test-with-powershell-scripts"></a>Testen met PowerShell-scripts

De implementatie en uw omgeving kunnen worden getest door de meegeleverde PowerShell-scripts uit te voeren.

De meegeleverde scriptbestanden zijn:

1. *SetupSampleSplitMergeEnvironment.ps1* - stelt een testgegevenslaag in voor splitsen/samenvoegen (zie onderstaande tabel voor gedetailleerde beschrijving)
2. *ExecuteSampleSplitMerge.ps1* - voert testbewerkingen uit op de testgegevenslaag (zie onderstaande tabel voor gedetailleerde beschrijving)
3. *GetMappings.ps1* - voorbeeldscript op het hoogste niveau dat de huidige status van de shardtoewijzingen afdrukt.
4. *ShardManagement.psm1* - helperscript dat de ShardManagement API omsluit
5. *SqlDatabaseHelpers.psm1* - helperscript voor het maken en beheren van SQL-databases
   
   <table style="width:100%">
     <tr>
       <th>PowerShell-bestand</th>
       <th>Stappen</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1. Maakt een database voor shardmapbeheer</td>
     </tr>
     <tr>
       <td>2. Hiermee maakt u 2 sharddatabases.
     </tr>
     <tr>
       <td>3. Hiermee maakt u een shardkaart voor deze databases (verwijdert bestaande shardkaarten in die databases). </td>
     </tr>
     <tr>
       <td>4. Hiermee maakt u een kleine voorbeeldtabel in beide shards en vult u de tabel in een van de shards.</td>
     </tr>
     <tr>
       <td>5. Declareert de SchemaInfo voor de geshard tabel.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-bestand</th>
       <th>Stappen</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. Hiermee verzendt u een gesplitste aanvraag naar de webfrontend van de Split-Merge Service, die de helft van de gegevens splitst van de eerste shard naar de tweede shard.</td>
     </tr>
     <tr>
       <td>2. Polls het web frontend voor de split request status en wacht tot het verzoek is voltooid.</td>
     </tr>
     <tr>
       <td>3. Hiermee wordt een samenvoegaanvraag naar de webfrontend van de Split-Merge Service-web verplaatst, waarmee de gegevens van de tweede shard naar de eerste shard worden verplaatst.</td>
     </tr>
     <tr>
       <td>4. Polls het web frontend voor de status van de samenvoegaanvraag en wacht tot de aanvraag is voltooid.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>PowerShell gebruiken om uw implementatie te verifiëren

1. Open een nieuw PowerShell-venster en navigeer naar de map waar u het Split-Merge-pakket hebt gedownload en navigeer vervolgens naar de map 'powershell'.

2. Maak een Azure SQL Database-server (of kies een bestaande server) waar de shardmapbeheer en shards worden gemaakt.

   > [!NOTE]
   > Met *het Script SetupSampleSplitMerge.ps1* worden al deze databases standaard op dezelfde server gemaakt om het script eenvoudig te houden. Dit is geen beperking van de Split-Merge-service zelf.

   Een SQL-verificatielogin met lees-/schrijftoegang tot de DB's is nodig voor de Split-Merge-service om gegevens te verplaatsen en de shardkaart bij te werken. Aangezien de Split-Merge-service in de cloud wordt uitgevoerd, biedt deze momenteel geen ondersteuning voor geïntegreerde verificatie.

   Controleer of de Azure SQL-server is geconfigureerd om toegang toe te staan vanaf het IP-adres van de machine waarop deze scripts worden uitgevoerd. U deze instelling vinden onder de Azure SQL-server / configuratie / toegestane IP-adressen.

3. Voer het script *SetupSampleSplitMerge.ps1* uit om de voorbeeldomgeving te maken.

   Als u dit script uitvoert, worden alle bestaande gegevensstructuren voor het beheer van de shardkaart op de database met shardmapbeheer en de shards gewist. Het kan handig zijn om het script opnieuw uit te voeren als u de shardkaart of shards opnieuw wilt initialiseren.

   Voorbeeldopdrachtregel:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Voer het script Getmappings.ps1 uit om de toewijzingen weer te geven die momenteel in de voorbeeldomgeving bestaan.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Voer het *script ExecuteSampleSplitMerge.ps1* uit om een gesplitste bewerking uit te voeren (de helft van de gegevens op de eerste shard naar de tweede shard verplaatsen) en vervolgens een samenvoegbewerking (de gegevens terug naar de eerste shard). Als u TLS hebt geconfigureerd en het http-eindpunt uitgeschakeld hebt gelaten, moet u ervoor zorgen dat u het https:// eindpunt gebruikt.

   Voorbeeldopdrachtregel:

   ```cmd
   .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' 
    -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```

   Als u de onderstaande fout ontvangt, is dit waarschijnlijk een probleem met het certificaat van uw webeindpunt. Probeer verbinding te maken met het webeindpunt met uw favoriete webbrowser en controleer of er een certificaatfout is.

     `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

   Als dit is gelukt, moet de uitvoer er als volgt uitzien:

   ```output
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```

6. Experimenteer met andere gegevenstypen! Al deze scripts nemen een optionele -ShardKeyType parameter waarmee u het sleuteltype opgeven. De standaardinstelling is Int32, maar u ook Int64, Guid of Binary opgeven.

## <a name="create-requests"></a>Aanvragen maken

De service kan worden gebruikt door gebruik te maken van de web-gebruikersinterface of door de SplitMerge.psm1 PowerShell-module te importeren en te gebruiken die uw verzoeken via de webrol zal indienen.

De service kan gegevens verplaatsen in zowel geshardtabellen als referentietabellen. Een geshard tabel heeft een sharding key kolom en heeft verschillende rijgegevens op elke scherf. Een referentietabel is niet gehard, zodat deze dezelfde rijgegevens op elke scherf bevat. Referentietabellen zijn handig voor gegevens die niet vaak worden gewijzigd en worden gebruikt om JOIN te gebruiken met geshardtabellen in query's.

Als u een gesplitste samenvoegbewerking wilt uitvoeren, moet u de geshardtabellen en referentietabellen declareren die u wilt hebben verplaatst. Dit wordt bereikt met de **SchemaInfo** API. Deze API bevindt zich in de naamruimte **microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema.**

1. Maak voor elke geharde tabel een object **ShardedTableInfo** waarin de bovenliggende schemanaam van de tabel wordt beschreven (optioneel, standaard standaard de tabelnaam en de kolomnaam in die tabel die de shardingssleutel bevat.
2. Maak voor elke referentietabel een **object ReferenceTableInfo** waarin de naam van het bovenliggende schema van de tabel wordt beschreven (optioneel, standaard 'dbo') en de tabelnaam.
3. Voeg de bovenstaande TableInfo-objecten toe aan een nieuw **SchemaInfo-object.**
4. Hier vindt u een verwijzing naar een **shardmapmanager-object** en bel **GetSchemaInfoCollection**.
5. Voeg de **SchemaInfo** toe aan de **SchemaInfoCollection**, met de naam van de shardkaart.

Een voorbeeld hiervan is te zien in het Script SetupSampleSplitMergeEnvironment.ps1.

De split-merge-service maakt niet de doeldatabase (of schema voor tabellen in de database) voor u. Ze moeten vooraf zijn gemaakt voordat ze een verzoek naar de service sturen.

## <a name="troubleshooting"></a>Problemen oplossen

Mogelijk ziet u het onderstaande bericht wanneer u de powershell-scripts van het voorbeeld uitvoert:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Deze fout betekent dat uw TLS/SSL-certificaat niet correct is geconfigureerd. Volg de instructies in de rubriek 'Verbinden met een webbrowser'.

Als u geen aanvragen indienen, ziet u mogelijk het:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

Controleer in dit geval uw configuratiebestand, met name de instelling voor **WorkerRoleSynchronizationStorageAccountConnectionString**. Deze fout geeft meestal aan dat de werkrol de metagegevensdatabase niet kan initialiseren bij het eerste gebruik.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
