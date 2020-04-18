---
title: Runtime aad voor Azure SSIS-integratie inschakelen
description: In dit artikel wordt beschreven hoe u Azure Active Directory-verificatie inschakelt met de beheerde identiteit voor Azure Data Factory om runtime van Azure-SSIS-integratie te maken.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 77b49d8446c7882a155742e8455d77bd1ec110cb
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606194"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure Active Directory-verificatie inschakelen voor Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel ziet u hoe u Azure Active Directory -verificatie (Azure AD) inschakelt met de beheerde identiteit voor uw Azure Data Factory (ADF) en deze gebruiken in plaats van conventionele verificatiemethoden (zoals SQL-verificatie) om:

- Maak een Azure-SSIS Integration Runtime (IR) die op zijn beurt ssis-catalogusdatabase (SSISDB) in Azure SQL Database server/Managed Instance voor uw rekening zal voorzien.

- Maak verbinding met verschillende Azure-bronnen wanneer u SSIS-pakketten uitvoert op Azure-SSIS IR.

Zie [Beheerde identiteit voor Gegevensfabriek voor](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)meer informatie over de beheerde identiteit van uw ADF.

> [!NOTE]
>-  In dit scenario wordt Azure AD-verificatie met de beheerde identiteit voor uw ADF alleen gebruikt bij het maken en aansluiten van de volgende startbewerkingen van uw SSIS IR, die op zijn beurt worden in- en geprocedeert en verbinding maakt met SSISDB. Voor SSIS-pakketuitvoeringen maakt uw SSIS IR nog steeds verbinding met SSISDB met SQL-verificatie met volledig beheerde accounts die zijn gemaakt tijdens de inrichting van SSISDB.
>-  Als u uw SSIS IR al hebt gemaakt met SQL-verificatie, u deze op dit moment niet opnieuw configureren om Azure AD-verificatie via PowerShell te gebruiken, maar u dit wel doen via de Azure-portal/ADF-app. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Azure AD inschakelen in Azure SQL-database

Azure SQL Database-server ondersteunt het maken van een database met een Azure AD-gebruiker. Eerst moet u een Azure AD-groep maken met de beheerde identiteit voor uw ADF als lid. Vervolgens moet u een Azure AD-gebruiker instellen als active directory-beheerder voor uw Azure SQL Database-server en er vervolgens verbinding mee maken op SQL Server Management Studio (SSMS) met die gebruiker. Ten slotte moet u een opgenomen gebruiker maken die de Azure AD-groep vertegenwoordigt, zodat de beheerde identiteit voor uw ADF door Azure-SSIS IR kan worden gebruikt om Namens u SSISDB te maken.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Een Azure AD-groep maken met de beheerde identiteit voor uw ADF als lid

U een bestaande Azure AD-groep gebruiken of een nieuwe groep maken met Azure AD PowerShell.

1.  Installeer de [Azure AD PowerShell-module.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)

2.  Log in `Connect-AzureAD`met de volgende cmdlet om een groep te maken en sla deze op in een variabele:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Het resultaat ziet eruit als het volgende voorbeeld, waarin ook de variabele waarde wordt weergegeven:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Voeg de beheerde identiteit voor uw ADF toe aan de groep. U het artikel [Beheerde identiteit voor Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) volgen om de belangrijkste Managed Identity Object ID te krijgen (bijvoorbeeld 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, maar gebruik hiervoor geen Managed Identity Application ID).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    U ook het groepslidmaatschap achteraf controleren.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Azure AD-verificatie configureren voor Azure SQL Database-server

U [Azure AD-verificatie configureren en beheren met SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) met de volgende stappen:

1.  Selecteer in Azure-portal **SQL-servers alle services** -> **SQL servers** via de linkernavigatie.

2.  Selecteer uw Azure SQL Database-server die moet worden geconfigureerd met Azure AD-verificatie.

3.  Selecteer **Active Directory-beheerder**in het gedeelte **Instellingen** van het blad .

4.  Selecteer in de opdrachtbalk de optie **Beheerder instellen**.

5.  Selecteer een Azure AD-gebruikersaccount dat als beheerder van de server moet worden gemaakt en selecteer **Selecteer Selecteren.**

6.  Selecteer Opslaan in de **opdrachtbalk.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Een opgenomen gebruiker maken in Azure SQL Database-server die de Azure AD-groep vertegenwoordigt

Voor deze volgende stap hebt u [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) nodig.

1. Start SSMS.

2. Voer in het dialoogvenster **Verbinding maken met server** de naam van uw Azure SQL Database-server in het veld **Servernaam** in.

3. Selecteer **in** het veld Verificatie de optie **Active Directory - Universal met MFA-ondersteuning** (u ook de andere twee Active Directory-verificatietypen gebruiken, zie Azure [AD-verificatie configureren en beheren met SQL).](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

4. Voer in het veld **Gebruikersnaam** de naam in van het Azure AD-account testuser@xxxonline.comdat u als serverbeheerder hebt ingesteld, bijvoorbeeld .

5. selecteer **Verbinding maken** en het aanmeldingsproces voltooien.

6. Vouw in de **objectverkenner**de map **Systeemdatabases** -> **uit.**

7. Klik met de rechtermuisknop op **de hoofddatabase** en selecteer **Nieuwe query**.

8. Voer in het queryvenster de volgende opdracht T-SQL in en selecteer **Uitvoeren** op de werkbalk.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   De opdracht moet worden voltooid en moet een opgenomen gebruiker maken om de groep weer te geven.

9. Schakel het queryvenster uit, voer de volgende opdracht T-SQL in en selecteer **Uitvoeren** op de werkbalk.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   De opdracht moet met succes worden voltooid, waardoor de opgenomen gebruiker de mogelijkheid krijgt om een database (SSISDB) te maken.

10. Als uw SSISDB is gemaakt met SQL-verificatie en u wilt overschakelen naar Azure AD-verificatie voor uw Azure-SSIS IR gebruiken om toegang te krijgen tot de **SSISDB-database** en selecteert u **Nieuwe query**.

11. Voer in het queryvenster de volgende opdracht T-SQL in en selecteer **Uitvoeren** op de werkbalk.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    De opdracht moet worden voltooid en moet een opgenomen gebruiker maken om de groep weer te geven.

12. Schakel het queryvenster uit, voer de volgende opdracht T-SQL in en selecteer **Uitvoeren** op de werkbalk.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    De opdracht moet met succes worden voltooid, waardoor de opgenomen gebruiker toegang krijgt tot SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Azure AD inschakelen op Azure SQL Database Managed Instance

Azure SQL Database Managed Instance ondersteunt het rechtstreeks maken van een database met de beheerde identiteit voor uw ADF. U hoeft niet lid te worden van de beheerde identiteit van uw ADF in een Azure AD-groep en ook geen opgenomen gebruiker te maken die die groep vertegenwoordigt in uw beheerde instantie.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Azure AD-verificatie configureren voor Azure SQL Database Managed Instance

Volg de stappen in [Het inrichten van een Azure Active Directory-beheerder voor uw beheerde instantie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>De beheerde identiteit voor uw ADF toevoegen als gebruiker in Azure SQL Database Managed Instance

Voor deze volgende stap hebt u [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) nodig.

1.  Start SSMS.

2.  Maak verbinding met uw beheerde instantie met een SQL Server-account dat een **sysadmin**is. Dit is een tijdelijke beperking die wordt verwijderd zodra Azure AD-serverprincipals (aanmeldingen) voor Azure SQL Database Managed Instance GA worden. U ziet de volgende fout als u een Azure AD-beheerdersaccount probeert te gebruiken om de aanmelding te maken: Msg 15247, Level 16, State 1, Line 1-gebruiker heeft geen toestemming om deze actie uit te voeren.

3.  Vouw in de **objectverkenner**de map **Systeemdatabases** -> **uit.**

4.  Klik met de rechtermuisknop op **de hoofddatabase** en selecteer **Nieuwe query**.

5.  Voer in het queryvenster het volgende T-SQL-script uit om de beheerde identiteit voor uw ADF als gebruiker toe te voegen

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    De opdracht moet met succes worden voltooid, waardoor de beheerde identiteit voor uw ADF de mogelijkheid krijgt om een database (SSISDB) te maken.

6.  Als uw SSISDB is gemaakt met SQL-verificatie en u wilt overschakelen naar Azure AD-verificatie voor uw Azure-SSIS IR gebruiken om toegang te krijgen tot de **SSISDB-database** en selecteert u **Nieuwe query**.

7.  Voer in het queryvenster de volgende opdracht T-SQL in en selecteer **Uitvoeren** op de werkbalk.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    De opdracht moet met succes worden voltooid, waardoor de beheerde identiteit voor uw ADF de mogelijkheid krijgt om toegang te krijgen tot SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure-SSIS IR inrichten in Azure-portal/ADF-app

Wanneer u uw Azure-SSIS IR indient in de Azure-portal/ADF-app, selecteert u op de pagina **SQL-instellingen** de optie **AAD-verificatie gebruiken met de beheerde identiteit voor uw ADF-optie.** In de volgende schermafbeelding worden de instellingen voor IR weergegeven met Azure SQL Database-server die SSISDB host. Voor IR met Beheerde instantie die SSISDB host, zijn de **Catalog Database Service Tier** en **Azure-services toestaan toegang tot** instellingen niet van toepassing, terwijl andere instellingen hetzelfde zijn.

Zie [Runtime van Azure-SSIS-integratie maken in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie over het maken van een Azure-SSIS IR.

![Instellingen voor de runtime van Azure-SSIS-integratie](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Azure-SSIS IR inrichten met PowerShell

Ga als volgt te werk om uw Azure-SSIS IR in te richten met PowerShell:

1.  Installeer [Azure PowerShell-module.](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 

2.  Stel in uw script `CatalogAdminCredential` geen parameter in. Bijvoorbeeld:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>SSIS-pakketten uitvoeren met beheerde identiteitsverificatie

Wanneer u SSIS-pakketten uitvoert op Azure-SSIS IR, u beheerde identiteitsverificatie gebruiken om verbinding te maken met verschillende Azure-bronnen. Momenteel hebben we al beheerde identiteitsverificatie ondersteund in de volgende verbindingsmanagers.

- [OLE DB Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Verbindingsbeheer](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
