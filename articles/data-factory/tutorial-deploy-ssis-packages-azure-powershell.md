---
title: De Azure-SSIS-integratieruntime inrichten met PowerShell | Microsoft Docs
description: Informatie over het inrichten van de Azure-SSIS-integratieruntime in Azure Data Factory met PowerShell, zodat u SSIS-pakketten in Azure kunt implementeren en uitvoeren.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7a4ae2198653ea8adab136caef0f812019efd998
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326111"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>De Azure-SSIS-integratieruntime inrichten in Azure Data Factory met PowerShell

Deze zelf studie bevat stappen voor het inrichten van een Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory (ADF). Azure-SSIS IR ondersteunt het uitvoeren van pakketten die zijn geïmplementeerd in SSIS Catalog (SSISDB) die worden gehost door Azure SQL Database Server/beheerd exemplaar (project implementatie model) en die zijn geïmplementeerd in bestands systemen/bestands shares/Azure Files (pakket implementatie model). Als Azure-SSIS IR is ingericht, kunt u vervolgens vertrouwde hulpprogram ma's gebruiken, zoals SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) en opdracht regel Programma's, zoals `dtinstall` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4, om uw pakketten te implementeren en uit te voeren in Azure. In deze zelfstudie voert u de volgende stappen uit:

> [!NOTE]
> In dit artikel wordt gebruikgemaakt van Azure PowerShell om een Azure-SSIS IR in te richten. Zie [zelf studie: inrichten Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)als u de app Azure Portal/ADF wilt gebruiken om een Azure-SSIS IR in te richten. 

> [!div class="checklist"]
> * Een gegevensfactory maakt.
> * Een Azure SSIS Integration Runtime maken
> * De Azure SSIS Integration Runtime starten
> * Het volledige script bekijken
> * SSIS-pakketten implementeren

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Zie [Overzicht van Integration Runtime in Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime) voor algemene informatie over Azure-SSIS IR.
- **Azure SQL database server (optioneel)** . Als u nog geen database server hebt, maakt u er een in de Azure Portal voordat u aan de slag gaat. ADF maakt SSISDB op deze database server. Het wordt aangeraden om de databaseserver in dezelfde Azure-regio te maken als de Integration Runtime. Met deze configuratie kan de integratie-runtime uitvoer logboeken schrijven naar SSISDB zonder overschrijding van Azure-regio's. 
    - Op basis van de geselecteerde databaseserver kan SSISDB namens u worden gemaakt als een enkele database, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie [een Azure SQL database afzonderlijke Data Base, elastische pool en beheerde instantie vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)voor hulp bij het kiezen van het type database server voor het hosten van SSISDB. Als u Azure SQL Database Server met virtuele netwerk service-eind punten/beheerde exemplaren in een virtueel netwerk gebruikt voor het hosten van SSISDB of toegang tot on-premises gegevens nodig hebt, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [Azure-SSIS IR maken in een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor de databaseserver. Dit is niet van toepassing wanneer u Azure SQL Database Server met virtuele netwerk service-eind punten/beheerde exemplaren in een virtueel netwerk gebruikt om SSISDB te hosten. Zie [Secure your Azure SQL database](../sql-database/sql-database-security-tutorial.md#create-firewall-rules) (Azure SQL-database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)om deze instelling in te scha kelen met behulp van Power shell.
    - Voeg het IP-adres van de client computer, of een bereik van IP-adressen met het IP-adres van de client computer, toe aan de lijst met client-IP-adressen in de firewall instellingen voor de database server. Zie [Overzicht van firewallregels op Azure SQL Database-serverniveau en -databaseniveau](../sql-database/sql-database-firewall-configure.md) voor meer informatie.
    - U kunt verbinding maken met de database server met behulp van SQL-verificatie met de referenties van de server beheerder of de Azure Active Directory (AAD)-verificatie met de beheerde identiteit voor uw ADF.  Voor het laatste moet u de beheerde identiteit voor de ADF toevoegen aan een AAD-groep met machtigingen voor toegang tot de databaseserver. Zie [Azure-SSIS IR met AAD-verificaties maken](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Controleer of de database server al een SSISDB heeft. Het inrichten van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaande SSISDB.
- **Azure PowerShell**. Volg de instructies in het [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps)als u een Power shell-script wilt uitvoeren om uw Azure-SSIS IR in te richten.

> [!NOTE]
> - Zie [ADF + SSIS IR Beschik baarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)voor een lijst met Azure-REGIO'S waarin ADF en Azure-SSIS IR momenteel beschikbaar zijn. 

## <a name="launch-windows-powershell-ise"></a>Windows PowerShell ISE starten

Start **Windows PowerShell ISE** met beheerdersbevoegdheden. 

## <a name="create-variables"></a>Variabelen maken

Kopieer en plak het volgende script: Geef waarden op voor de variabelen. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Aanmelden en abonnement selecteren

Voeg de volgende code toe aan het script om u aan te melden en uw Azure-abonnement te selecteren.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>De verbinding met de database server valideren

Voeg het volgende script toe om uw Azure SQL Database Server te valideren. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Zie het volgende voor beeld om een Azure SQL Database te maken als onderdeel van het script: 

Stel waarden in voor de variabelen die nog niet zijn gedefinieerd. Bijvoorbeeld: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resource groep](../azure-resource-manager/resource-group-overview.md) met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

Als uw resource groep al bestaat, kopieert u deze code niet naar het script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voer de volgende opdracht uit om een data factory te maken:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Een Integration Runtime maken

Voer de volgende opdrachten uit om een Azure SSIS Integration runtime te maken waarmee SSIS-pakketten worden uitgevoerd in Azure.

Als u SSISDB niet gebruikt, kunt u CatalogServerEndpoint-, CatalogPricingTier-en CatalogAdminCredential-para meters weglaten.

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
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Integration Runtime starten

Voer de volgende opdrachten uit om de Azure-SSIS Integration runtime te starten.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid.
>
> Als u SSISDB gebruikt, maakt de ADF-service verbinding met uw database server om SSISDB voor te bereiden. 
> 
> Wanneer u een Azure-SSIS IR inricht, worden er ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze onderdelen bieden connectiviteit voor Excel/Access-bestanden en verschillende Azure-gegevens bronnen, naast de gegevens bronnen die al door ingebouwde onderdelen worden ondersteund. U kunt ook extra onderdelen installeren. Zie [aangepaste installatie voor Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Volledige script

Met het Power shell-script in deze sectie configureert u een exemplaar van Azure-SSIS IR dat SSIS-pakketten uitvoert. Nadat u dit script hebt uitgevoerd, kunt u SSIS-pakketten implementeren en uitvoeren in Azure.

1. Start Windows PowerShell ISE (Integrated Scripting Environment).
2. Voer in ISE de volgende opdracht uit vanaf de opdrachtprompt.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Kopieer het PowerShell-script in deze sectie en plak het in ISE.
4. Geef aan het begin van het script de juiste waarden voor alle parameters op.
5. Voer het script uit. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Azure-SSIS IR controleren en beheren

Zie de volgende artikelen voor meer informatie over het controleren en beheren van een Azure-SSIS IR. 

- [Azure-SSIS IR bewaken](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>SSIS-pakketten implementeren

Als u SSISDB gebruikt, kunt u uw pakketten erin implementeren en deze uitvoeren op Azure-SSIS IR met behulp van SSDT/SSMS-hulpprogram ma's die via het server-eind punt verbinding maken met uw database server. Voor Azure SQL Database Server/beheerd exemplaar met een openbaar eind punt is de eindpunt indeling van de server `<server name>.database.windows.net` @ no__t-1 @ no__t-2. Als u geen gebruik maakt van SSISDB, kunt u uw pakketten implementeren in bestands systemen/bestands shares/Azure Files en deze uitvoeren op Azure-SSIS IR met behulp van `dtinstall` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4-opdracht regel Programma's. Zie [SSIS-pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)voor meer informatie. In beide gevallen kunt u uw geïmplementeerde pakketten ook uitvoeren op Azure-SSIS IR met behulp van de uitvoering van SSIS-pakket activiteit in ADF-pijp lijnen. Zie de uitvoering van SSIS-pakketten [aanroepen als een ADF-activiteit voor de eerste klasse](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Zie ook de volgende artikelen uit de SSIS-documentatie: 

- [SSIS-pakketten implementeren, uitvoeren en bewaken in azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Pakket uitvoeringen plannen in azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie) 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd: 

> [!div class="checklist"]
> * Een gegevensfactory maakt.
> * Een Azure SSIS Integration Runtime maken
> * De Azure SSIS Integration Runtime starten
> * Het volledige script bekijken
> * SSIS-pakketten implementeren

Voor meer informatie over uw Azure-SSIS-integratieruntime, gaat u verder met het volgende artikel:

> [!div class="nextstepaction"]
>[Azure-SSIS IR aanpassen](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)