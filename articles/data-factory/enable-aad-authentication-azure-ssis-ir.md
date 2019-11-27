---
title: Azure Active Directory voor Azure-SSIS Integration Runtime inschakelen
description: In dit artikel wordt beschreven hoe u Azure Active Directory verificatie inschakelt met de beheerde identiteit voor Azure Data Factory om Azure-SSIS Integration Runtime te maken.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.date: 5/14/2019
ms.openlocfilehash: 6973e72b06d51241e883038936270fd0931365d7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217701"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure Active Directory authenticatie inschakelen voor Azure-SSIS Integration Runtime

In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD)-verificatie inschakelt met de beheerde identiteit voor uw Azure Data Factory (ADF) en deze gebruikt in plaats van conventionele verificatie methoden (zoals SQL-verificatie) voor het volgende:

- Maak een Azure-SSIS Integration Runtime (IR) waarmee u de SSIS-catalogus database (SSISDB) in Azure SQL Database Server/beheerd exemplaar namens u kunt inschakelen.

- Verbinding maken met verschillende Azure-resources wanneer SSIS-pakketten op Azure-SSIS IR worden uitgevoerd.

Zie [beheerde Identiy voor Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)voor meer informatie over de beheerde identiteit voor uw ADF.

> [!NOTE]
>-  In dit scenario wordt Azure AD-verificatie met de beheerde identiteit voor uw ADF alleen gebruikt bij het maken en volgende start bewerkingen van uw SSIS IR, die in inrichtings inrichting en verbinding maken met SSISDB. Voor de uitvoering van SSIS-pakketten zal uw SSIS-IR nog steeds verbinding maken met SSISDB met behulp van SQL-verificatie met volledig beheerde accounts die zijn gemaakt tijdens het inrichten van SSISDB.
>-  Als u uw SSIS-IR al hebt gemaakt met behulp van SQL-verificatie, kunt u deze niet opnieuw configureren voor gebruik van Azure AD-verificatie via Power shell, maar u kunt dit doen via Azure Portal/ADF-app. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Azure AD inschakelen op Azure SQL Database

Azure SQL Database-Server biedt ondersteuning voor het maken van een Data Base met een Azure AD-gebruiker. Eerst moet u een Azure AD-groep met de beheerde identiteit voor uw ADF maken als lid. Vervolgens moet u een Azure AD-gebruiker instellen als de Active Directory-beheerder voor uw Azure SQL Database-Server en vervolgens verbinding maken met die gebruiker via SQL Server Management Studio (SSMS). Ten slotte moet u een Inge sloten gebruiker maken die de Azure AD-groep vertegenwoordigt, zodat de beheerde identiteit voor uw ADF kan worden gebruikt door Azure-SSIS IR om namens u SSISDB te maken.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Een Azure AD-groep maken met de beheerde identiteit voor uw ADF als lid

U kunt een bestaande Azure AD-groep gebruiken of een nieuwe maken met behulp van Azure AD Power shell.

1.  Installeer de [Azure AD Power shell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) -module.

2.  Meld u aan met `Connect-AzureAD`, voer de volgende cmdlet uit om een groep te maken en op te slaan in een variabele:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Het resultaat ziet eruit als in het volgende voor beeld, waarin ook de waarde van de variabele wordt weer gegeven:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Voeg de beheerde identiteit voor uw ADF toe aan de groep. U kunt het artikel [Managed Identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) gebruiken om de object-id van de principal Managed Identity te verkrijgen (bijvoorbeeld 765AD4AB-XXXX-XXXX-XXXX-51ed985819dc, maar geen beheerde identiteits toepassings-id gebruiken voor dit doel).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    U kunt ook het groepslid maatschap achteraf controleren.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Azure AD-verificatie voor Azure SQL Database Server configureren

U kunt met behulp van de volgende stappen [Azure AD-verificatie met SQL configureren en beheren](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) :

1.  Selecteer in Azure Portal **alle services** -> **SQL-servers** in de linkernavigatiebalk.

2.  Selecteer uw Azure SQL Database-Server die u wilt configureren met Azure AD-verificatie.

3.  Selecteer in de sectie **instellingen** van de Blade de optie **Active Directory beheerder**.

4.  Selecteer in de opdracht balk de optie **beheerder instellen**.

5.  Selecteer een Azure AD-gebruikers account om Administrator van de-server te maken en selecteer **selecteren.**

6.  Selecteer in de opdracht balk de optie **opslaan.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Een Inge sloten gebruiker maken in Azure SQL Database Server die de Azure AD-groep vertegenwoordigt

Voor deze volgende stap hebt u [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) nodig.

1. Start SSMS.

2. Voer in het dialoog venster **verbinding maken met server** de naam van de Azure SQL database server in het veld **Server naam** in.

3. In het veld **verificatie** selecteert u **Active Directory-Universal met MFA-ondersteuning** (u kunt ook de andere twee Active Directory verificatie typen gebruiken. Zie [Azure AD-verificatie configureren en beheren met SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)(Engelstalig).

4. Voer in het veld **gebruikers naam** de naam in van het Azure ad-account dat u als server beheerder hebt ingesteld, bijvoorbeeld testuser@xxxonline.com.

5. Selecteer **verbinding maken** en voltooi het aanmeldings proces.

6. Vouw in de **objectverkenner**de map **data bases** -> **System data bases** uit.

7. Klik met de rechter muisknop op de **hoofd** database en selecteer **nieuwe query**.

8. Voer de volgende T-SQL-opdracht in het query venster in en selecteer **uitvoeren** op de werk balk.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   De opdracht is voltooid, waardoor een Inge sloten gebruiker de groep vertegenwoordigt.

9. Wis het query venster, voer de volgende T-SQL-opdracht in en selecteer **uitvoeren** op de werk balk.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   De opdracht moet worden voltooid, waardoor de Inge sloten gebruiker de mogelijkheid heeft om een Data Base (SSISDB) te maken.

10. Als uw SSISDB is gemaakt met behulp van SQL-verificatie en u wilt overschakelen op het gebruik van Azure AD-verificatie voor uw Azure-SSIS IR om het te openen, klikt u met de rechter muisknop op **SSISDB** -data base en selecteert u **nieuwe query**.

11. Voer de volgende T-SQL-opdracht in het query venster in en selecteer **uitvoeren** op de werk balk.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    De opdracht is voltooid, waardoor een Inge sloten gebruiker de groep vertegenwoordigt.

12. Wis het query venster, voer de volgende T-SQL-opdracht in en selecteer **uitvoeren** op de werk balk.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    De opdracht moet worden voltooid, waardoor de Inge sloten gebruiker toegang kan krijgen tot SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Azure AD inschakelen op Azure SQL Database beheerde instantie

Azure SQL Database beheerde instantie ondersteunt het maken van een Data Base met de beheerde identiteit voor uw ADF direct. U hoeft de beheerde identiteit voor uw ADF niet toe te voegen aan een Azure AD-groep of een Inge sloten gebruiker te maken die deze groep vertegenwoordigt in uw beheerde exemplaar.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Azure AD-verificatie voor Azure SQL Database beheerde instantie configureren

Volg de stappen in [een Azure Active Directory beheerder inrichten voor uw beheerde exemplaar](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>De beheerde identiteit voor uw ADF toevoegen als gebruiker in Azure SQL Database Managed instance

Voor deze volgende stap hebt u [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) nodig.

1.  Start SSMS.

2.  Maak verbinding met uw beheerde exemplaar met behulp van een SQL Server-account dat een **sysadmin**is. Dit is een tijdelijke beperking die wordt verwijderd zodra Azure AD server-principals (aanmeldingen) voor Azure SQL Database beheerde instantie GA zijn. Als u probeert een Azure AD-beheerders account te gebruiken om de aanmelding te maken, wordt de volgende fout weer geven: msg 15247, niveau 16, status 1, regel 1 gebruiker heeft geen machtiging om deze actie uit te voeren.

3.  Vouw in de **objectverkenner**de map **data bases** -> **System data bases** uit.

4.  Klik met de rechter muisknop op de **hoofd** database en selecteer **nieuwe query**.

5.  Voer in het query venster het volgende T-SQL-script uit om de beheerde identiteit voor uw ADF toe te voegen als gebruiker

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    De opdracht moet worden voltooid, waardoor de beheerde identiteit voor uw ADF de mogelijkheid heeft om een Data Base te maken (SSISDB).

6.  Als uw SSISDB is gemaakt met behulp van SQL-verificatie en u wilt overschakelen op het gebruik van Azure AD-verificatie voor uw Azure-SSIS IR om het te openen, klikt u met de rechter muisknop op **SSISDB** -data base en selecteert u **nieuwe query**.

7.  Voer de volgende T-SQL-opdracht in het query venster in en selecteer **uitvoeren** op de werk balk.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    De opdracht moet worden voltooid, waardoor de beheerde identiteit voor uw ADF de mogelijkheid heeft om toegang te krijgen tot SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure-SSIS IR inrichten in de Azure Portal/ADF-app

Wanneer u uw Azure-SSIS IR inricht in de Azure Portal/ADF-app, selecteert u op de pagina **SQL-instellingen** de optie **Aad-verificatie gebruiken met de beheerde identiteit voor uw ADF** . De volgende scherm afbeelding toont de instellingen voor IR met Azure SQL Database Server hosting SSISDB. Voor IR met Managed instance hosting SSISDB, de **service tier van de catalogus database** en **toestaan dat Azure-Services toegang hebben tot** instellingen, terwijl andere instellingen hetzelfde zijn.

Zie [een Azure SSIS Integration runtime in azure Data Factory maken](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie over het maken van een Azure-SSIS IR.

![Instellingen voor de Azure-SSIS-integratie-runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Azure-SSIS IR inrichten met Power shell

Ga als volgt te werk om uw Azure-SSIS IR in te richten met Power shell:

1.  Installeer [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) -module.

2.  Stel in uw script niet `CatalogAdminCredential` para meter in. Bijvoorbeeld:

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

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>SSIS-pakketten uitvoeren met beheerde identiteits verificatie

Wanneer u SSIS-pakketten uitvoert op Azure-SSIS IR, kunt u beheerde identiteits verificatie gebruiken om verbinding te maken met verschillende Azure-resources. Momenteel wordt beheerde identiteits verificatie al ondersteund in de volgende verbindings beheer.

- [Verbindings beheer OLE DB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET-verbindings beheer](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Verbindings beheer Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
