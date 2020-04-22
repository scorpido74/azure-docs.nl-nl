---
title: Toegang tot gegevensarchieven en bestandsshares met Windows-verificatie
description: Meer informatie over het configureren van De SSIS-catalogus in Azure SQL Database en Azure-SSIS Integration Runtime in Azure Data Factory om pakketten uit te voeren die toegang hebben tot gegevensopslag en bestandsshares met Windows-verificatie.
ms.date: 3/22/2018
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 0c4cdc3481fb58efd8eaa4cd83e1d6167f203a4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760184"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Toegang tot gegevensarchieven en bestandsshares met Windows-verificatie van SSIS-pakketten in Azure

U Windows-verificatie gebruiken om toegang te krijgen tot gegevensopslag, zoals SQL-servers, bestandsshares, Azure-bestanden, enz. Uw gegevensopslag kan on-premises zijn, worden gehost op Azure Virtual Machines (VM's) of worden uitgevoerd in Azure als beheerde services. Als deze on-premises zijn, moet u uw Azure-SSIS IR aansluiten bij een Virtueel Netwerk (Microsoft Azure Virtual Network) dat is verbonden met uw on-premises netwerk, zie [Join Azure-SSIS IR naar een Microsoft Azure Virtual Network.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) Er zijn vier methoden om toegang te krijgen tot gegevensopslag met Windows-verificatie vanuit SSIS-pakketten die op uw Azure-SSIS IR worden uitgevoerd:

| Verbindingsmethode | Effectief toepassingsgebied | Installatiestap | Toegangsmethode in pakketten | Aantal referentiesets en verbonden resources | Type verbonden resources | 
|---|---|---|---|---|---|
| Een uitvoeringscontext op activiteitsniveau instellen | SSIS-pakketactiviteit per uitvoering | Configureer de eigenschap **Windows-verificatie** om een context 'Uitvoeren/uitvoeren als' in te stellen bij het uitvoeren van SSIS-pakketten als SSIS-pakketactiviteiten uitvoeren in ADF-pijplijnen.<br/><br/> Zie [Uitvoeren ssis-pakketactiviteit configureren](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)voor meer informatie . | Toegang tot bronnen rechtstreeks in pakketten via UNC-pad, bijvoorbeeld `\\YourFileShareServerName\YourFolderName` als u bestandsshares of Azure-bestanden gebruikt: of`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Ondersteuning voor slechts één referentieset voor alle verbonden resources | - Bestandsshares op premises/Azure VM's<br/><br/> - Azure-bestanden, zie [Een Azure-bestandsshare gebruiken](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SQL Servers on premises/Azure VM's met Windows-verificatie<br/><br/> - Andere bronnen met Windows-verificatie |
| Een uitvoeringscontext op catalogusniveau instellen | Per Azure-SSIS IR, maar wordt overschreven bij het instellen van een uitvoeringscontext op activiteitsniveau (zie hierboven) | SSISDB-opgeslagen `catalog.set_execution_credential` procedure uitvoeren om een "Execution/Run as"-context in te stellen.<br/><br/> Voor meer info, zie de rest van dit artikel hieronder. | Toegang tot bronnen rechtstreeks in pakketten via UNC-pad, bijvoorbeeld `\\YourFileShareServerName\YourFolderName` als u bestandsshares of Azure-bestanden gebruikt: of`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Ondersteuning voor slechts één referentieset voor alle verbonden resources | - Bestandsshares op premises/Azure VM's<br/><br/> - Azure-bestanden, zie [Een Azure-bestandsshare gebruiken](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SQL Servers on premises/Azure VM's met Windows-verificatie<br/><br/> - Andere bronnen met Windows-verificatie |
| Aanhoudende referenties via `cmdkey` opdracht | Per Azure-SSIS IR, maar wordt overschreven bij het instellen van een uitvoeringscontext op activiteitsniveau (zie hierboven) | Voer `cmdkey` de opdracht uit`main.cmd`in een aangepast installatiescript ( ) bij het inrichten van uw Azure-SSIS IR, bijvoorbeeld als u bestandsshares of Azure-bestanden gebruikt: `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword` of `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`.<br/><br/> Zie [Setup aanpassen voor Azure-SSIS IR voor](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)meer informatie. | Toegang tot bronnen rechtstreeks in pakketten via UNC-pad, bijvoorbeeld `\\YourFileShareServerName\YourFolderName` als u bestandsshares of Azure-bestanden gebruikt: of`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Meerdere referentiesets voor verschillende verbonden resources ondersteunen | - Bestandsshares op premises/Azure VM's<br/><br/> - Azure-bestanden, zie [Een Azure-bestandsshare gebruiken](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SQL Servers on premises/Azure VM's met Windows-verificatie<br/><br/> - Andere bronnen met Windows-verificatie |
| Montageschijven op de uitvoeringstijd van het pakket (niet-persistent) | Per pakket | Opdracht `net use` uitvoeren in Procestaak uitvoeren die wordt toegevoegd aan het begin van de besturingsstroom in uw pakketten, bijvoorbeeld`net use D: \\YourFileShareServerName\YourFolderName` | Toegang tot bestandsshares via toegewezen stations | Ondersteuning voor meerdere stations voor verschillende bestandsshares | - Bestandsshares op premises/Azure VM's<br/><br/> - Azure-bestanden, zie [Een Azure-bestandsshare gebruiken](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) |
|||||||

> [!WARNING]
> Als u geen van de bovenstaande methoden gebruikt om toegang te krijgen tot gegevensarchieven met Windows-verificatie, kunnen uw pakketten die afhankelijk zijn van Windows-verificatie er geen toegang toe krijgen en mislukken tijdens het uitvoeren van de tijd. 

In de rest van dit artikel wordt beschreven hoe u SSIS-catalogus (SSISDB) configureren die wordt gehost in Azure SQL Database server/Managed Instance om pakketten uit te voeren op Azure-SSIS IR die Windows-verificatie gebruiken om toegang te krijgen tot gegevensopslag. 

## <a name="you-can-only-use-one-set-of-credentials"></a>U slechts één set referenties gebruiken

Wanneer u Windows-verificatie gebruikt in een SSIS-pakket, u slechts één set referenties gebruiken. De domeinreferenties die u opgeeft wanneer u de stappen in dit artikel volgt, zijn van toepassing op alle pakketuitvoeringen - interactief of gepland - op uw Azure-SSIS IR totdat u ze wijzigt of verwijdert. Als uw pakket verbinding moet maken met meerdere gegevensarchieven met verschillende sets referenties, moet u de bovenstaande alternatieve methoden overwegen.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Domeinreferenties opgeven voor Windows-verificatie

Ga als volgt te werk om domeinreferenties op te geven waarmee pakketten Windows-verificatie kunnen gebruiken om toegang te krijgen tot gegevensarchieven op locatie:

1. Maak met SQL Server Management Studio (SSMS) of een ander hulpprogramma verbinding met Azure SQL Database server/Managed Instance waarmee SSISDB wordt geïnstalleerd. Zie [Verbinding maken met SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een queryvenster met SSISDB als huidige database.

3. Voer de volgende opgeslagen procedure uit en geef de juiste domeinreferenties op:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Voer uw SSIS-pakketten uit. De pakketten maken gebruik van de referenties die u hebt verstrekt om toegang te krijgen tot gegevensarchieven op locatie met Windows-verificatie.

### <a name="view-domain-credentials"></a>Domeinreferenties weergeven

Ga als volgt te werk om de actieve domeinreferenties weer te geven:

1. Maak met SSMS of een ander hulpmiddel verbinding met Azure SQL Database server/Managed Instance waarmee SSISDB wordt geïnstalleerd. Zie [Verbinding maken met SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een queryvenster met SSISDB als huidige database.

3. Voer de volgende opgeslagen procedure uit en controleer de uitvoer:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Domeinreferenties wissen
Ga als volgt te werk om de referenties die u hebt opgegeven zoals beschreven in dit artikel te wissen en te verwijderen:

1. Maak met SSMS of een ander hulpmiddel verbinding met Azure SQL Database server/Managed Instance waarmee SSISDB wordt geïnstalleerd. Zie [Verbinding maken met SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een queryvenster met SSISDB als huidige database.

3. Voer de volgende opgeslagen procedure uit:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Verbinding maken met een SQL Server on-premises

Ga op de volgende dingen om te controleren of u verbinding maken met een SQL Server op locatie:

1. Als u deze test wilt uitvoeren, zoekt u een computer die niet is verbonden aan het domein.

2. Voer op de computer die niet met een domein is verbonden, de volgende opdracht uit om SSMS te starten met de domeinreferenties die u wilt gebruiken:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. Controleer vanaf SSMS of u on-premises verbinding maken met de SQL Server.

### <a name="prerequisites"></a>Vereisten

Ga als volgt te werk om toegang te krijgen tot een SQL Server on-premises vanuit pakketten die in Azure worden uitgevoerd:

1.  Schakel in SQL Server Configuration Manager TCP/IP-protocol in.

2. Toegang toestaan via de Windows-firewall. Zie [Windows firewall configureren om toegang te krijgen tot SQL Server](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access)voor meer informatie.

3. Koppel uw Azure-SSIS IR aan een Microsoft Azure Virtual Network dat on-premises is verbonden met de SQL Server.  Zie [Azure-SSIS IR deelnemen aan een Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)voor meer informatie.

4. Gebruik de opgeslagen `catalog.set_execution_credential` procedure van SSISDB om referenties op te geven zoals beschreven in dit artikel.

## <a name="connect-to-a-file-share-on-premises"></a>Verbinding maken met een bestandsshare op locatie

Ga als volgt te werk om te controleren of u verbinding maken met een bestandsshare op locatie:

1. Als u deze test wilt uitvoeren, zoekt u een computer die niet is verbonden aan het domein.

2. Voer op de computer die niet is aangesloten op het domein de volgende opdrachten uit. Met deze opdrachten wordt een opdrachtpromptvenster geopend met de domeinreferenties die u wilt gebruiken en testen deze de verbinding met de bestandsshare op locatie door een directoryvermelding te krijgen.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Controleer of de lijstlijst wordt geretourneerd voor het delen van bestanden op locatie.

### <a name="prerequisites"></a>Vereisten

Ga als volgt te werk om toegang te krijgen tot een bestandsshare op locatie vanuit pakketten die in Azure worden uitgevoerd:

1. Toegang toestaan via de Windows-firewall.

2. Koppel uw Azure-SSIS IR aan een Microsoft Azure Virtual Network dat is verbonden met het bestandsshare op locatie.  Zie [Azure-SSIS IR deelnemen aan een Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)voor meer informatie.

3. Gebruik de opgeslagen `catalog.set_execution_credential` procedure van SSISDB om referenties op te geven zoals beschreven in dit artikel.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Verbinding maken met een bestandsshare op Azure VM

Ga als volgt te werk om een bestandsshare op Azure VM te openen vanuit pakketten die in Azure worden uitgevoerd:

1. Maak met SSMS of een ander hulpmiddel verbinding met Azure SQL Database server/Managed Instance waarmee SSISDB wordt geïnstalleerd. Zie [Verbinding maken met SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een queryvenster met SSISDB als huidige database.

3. Voer de volgende opgeslagen procedure uit en geef de juiste domeinreferenties op:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Verbinding maken met een bestandsshare in Azure-bestanden

Zie Azure Files voor meer informatie over Azure [Files.](https://azure.microsoft.com/services/storage/files/)

Ga als volgt te werk om toegang te krijgen tot een bestandsshare in Azure-bestanden vanuit pakketten die in Azure worden uitgevoerd:

1. Maak met SSMS of een ander hulpmiddel verbinding met Azure SQL Database server/Managed Instance waarmee SSISDB wordt geïnstalleerd. Zie [Verbinding maken met SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)voor meer informatie.

2. Open een queryvenster met SSISDB als huidige database.

3. Voer de volgende opgeslagen procedure uit en geef de juiste domeinreferenties op:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Volgende stappen

- Zet uw pakketten in. Zie [Een SSIS-project implementeren naar Azure met SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)voor meer informatie.
- Voer je pakjes uit. Zie [SSIS-pakketten uitvoeren in Azure met SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)voor meer informatie.
- Plan uw pakketten. Zie [SSIS-pakketten plannen in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)voor meer informatie.