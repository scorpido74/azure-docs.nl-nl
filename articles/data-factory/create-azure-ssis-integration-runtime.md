---
title: Azure-SSIS Integration Runtime maken in Azure Data Factory | Microsoft Docs
description: Meer informatie over het maken van Azure-SSIS Integration Runtime in Azure Data Factory zodat u SSIS-pakketten kunt implementeren en uitvoeren in Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 36cb4d306cd74dcde09fa55fc582a043bc324f65
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010040"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure-SSIS Integration Runtime in Azure Data Factory maken

Deze zelf studie bevat stappen voor het inrichten van een Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory (ADF). Azure-SSIS IR ondersteunt het uitvoeren van pakketten die zijn geïmplementeerd in SSIS Catalog (SSISDB) die worden gehost door Azure SQL Database Server/beheerd exemplaar (project implementatie model) en die zijn geïmplementeerd in bestands systemen/bestands shares/Azure Files (pakket implementatie model). Als Azure-SSIS IR is ingericht, kunt u vervolgens vertrouwde hulpprogram ma's gebruiken, zoals SQL Server Data tools (SSDT)/SQL Server Management Studio (SSMS) en opdracht regel Programma's `dtinstall`, zoals / `dtutil` / `dtexec`, naar uw pakketten implementeren en uitvoeren in Azure.

De [zelf studie: Bij het inrichten](tutorial-create-azure-ssis-runtime-portal.md) van Azure-SSIS IR wordt beschreven hoe u een Azure-SSIS IR maakt via de app Azure Portal/ADF en optioneel Azure SQL database server/managed instance gebruiken om SSISDB te hosten. In dit artikel wordt de zelf studie uitgebreid en wordt uitgelegd hoe u het volgende kunt doen:

- Gebruik eventueel Azure SQL Database Server met virtuele netwerk service-eind punten/een beheerd exemplaar met een persoonlijk eind punt om SSISDB te hosten. Als vereiste moet u de machtigingen/instellingen van het virtuele netwerk voor uw Azure-SSIS IR configureren om lid te worden van een virtueel netwerk.

- Gebruik eventueel Azure Active Directory (AAD)-verificatie met de beheerde identiteit voor uw ADF om verbinding te maken met Azure SQL Database Server/beheerd exemplaar. Als vereiste moet u de beheerde identiteit voor uw ADF toevoegen als een database gebruiker die SSISDB kan maken.

- Voeg eventueel uw Azure-SSIS IR toe aan een virtueel netwerk of configureer zelf-Hostende IR als proxy voor uw Azure-SSIS IR om toegang te krijgen tot gegevens on-premises.

## <a name="overview"></a>Overzicht

In dit artikel worden verschillende manieren voor het inrichten van Azure-SSIS IR weer gegeven:

- [Azure-portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager-sjabloon](#azure-resource-manager-template)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u nog geen abonnement hebt, kunt u een [gratis proef](https://azure.microsoft.com/pricing/free-trial/) account maken.
- **Azure SQL database server/beheerd exemplaar (optioneel)** . Als u nog geen database server hebt, maakt u er een in de Azure Portal voordat u aan de slag gaat. ADF maakt SSISDB op deze database server. Het wordt aangeraden om de databaseserver in dezelfde Azure-regio te maken als de Integration Runtime. Met deze configuratie kan de integratie-runtime uitvoer logboeken schrijven naar SSISDB zonder overschrijding van Azure-regio's. 
    - Op basis van de geselecteerde databaseserver kan SSISDB namens u worden gemaakt als een enkele database, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie voor hulp bij het kiezen van het type database server om SSISDB te hosten [vergelijken Azure SQL database afzonderlijke data base/elastische groep/beheerd exemplaar](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Als u Azure SQL Database Server met virtuele netwerk service-eind punten/een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten of toegang tot on-premises gegevens nodig hebt zonder zelf-Hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [Azure-SSIS IR toevoegen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
    - Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor de databaseserver. Dit is niet van toepassing wanneer u Azure SQL Database Server met virtuele netwerk service-eind punten/een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten. Zie [Secure your Azure SQL database](../sql-database/sql-database-security-tutorial.md#create-firewall-rules) (Azure SQL-database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)om deze instelling in te scha kelen met behulp van Power shell.
    - Voeg het IP-adres van de client computer, of een bereik van IP-adressen met het IP-adres van de client computer, toe aan de lijst met client-IP-adressen in de firewall instellingen voor de database server. Zie [Overzicht van firewallregels op Azure SQL Database-serverniveau en -databaseniveau](../sql-database/sql-database-firewall-configure.md) voor meer informatie.
    - U kunt verbinding maken met de database server met behulp van SQL-verificatie met de referenties van de server beheerder of de Azure Active Directory (AAD)-verificatie met de beheerde identiteit voor uw ADF.  Voor de laatste moet u de beheerde identiteit voor uw ADF toevoegen aan een AAD-groep met toegangs machtigingen voor de database server. Zie [Aad-verificatie inschakelen voor Azure-SSIS IR voor](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)meer informatie.
    - Controleer of de database server al een SSISDB heeft. Het inrichten van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaande SSISDB.
- **Azure Resource Manager virtueel netwerk (optioneel)** . U moet een Azure Resource Manager virtueel netwerk hebben als ten minste één van de volgende voor waarden waar is:
    - U host SSISDB op Azure SQL Database Server met virtuele netwerk service-eind punten/beheerde exemplaren met een persoonlijk eind punt.
    - U wilt verbinding maken met on-premises gegevens archieven vanaf SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR zonder zelf-Hostende IR te configureren.
- **Azure PowerShell (optioneel)** . Volg de instructies voor het [installeren en configureren van Azure PowerShell](/powershell/azure/install-az-ps), als u een Power shell-script wilt uitvoeren om uw Azure-SSIS IR in te richten.

### <a name="region-support"></a>Regio ondersteuning

Zie [ADF + SSIS IR Beschik baarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)voor een lijst met Azure-regio's, waarin ADF en Azure-SSIS IR momenteel beschikbaar zijn.

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>SQL Database afzonderlijke data base/elastische pool en SQL Database beheerd exemplaar vergelijken

In de volgende tabel worden bepaalde functies van Azure SQL Database Server en het beheerde exemplaar vergeleken met de Azure-SSIR IR:

| Functie | Eén data base/elastische pool| Beheerd exemplaar |
|---------|--------------|------------------|
| **Schema** | SQL Server Agent is niet beschikbaar.<br/><br/>Zie [een pakket uitvoering plannen in ADF-pijp lijn](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Managed instance agent is beschikbaar. |
| **Verificatie** | U kunt SSISDB maken met een Inge sloten database gebruiker die een AAD-groep vertegenwoordigt met de beheerde identiteit van uw ADF als lid van de rol **db_owner** .<br/><br/>Zie [Azure AD-verificatie inschakelen om SSISDB te maken in Azure SQL database server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | U kunt SSISDB maken met een Inge sloten database gebruiker die de beheerde identiteit van uw ADF vertegenwoordigt. <br/><br/>Zie [Azure AD-verificatie inschakelen om SSISDB te maken in Azure SQL database Managed instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Servicelaag** | Wanneer u Azure-SSIS IR met uw Azure SQL Database-Server maakt, kunt u de servicelaag voor SSISDB selecteren. Er zijn meerdere service lagen. | Wanneer u Azure-SSIS IR maakt met uw beheerde exemplaar, kunt u de servicelaag voor SSISDB niet selecteren. Alle data bases in uw beheerde exemplaar delen dezelfde resource die aan dat exemplaar is toegewezen. |
| **Virtueel netwerk** | Ondersteunt alleen Azure Resource Manager virtuele netwerken voor uw Azure-SSIS IR om toe te voegen als u gebruikmaakt van Azure SQL Database Server met virtuele netwerk service-eind punten of als u toegang wilt hebben tot on-premises gegevens archieven zonder zelf-Hostende IR te configureren. | Ondersteunt alleen Azure Resource Manager virtuele netwerken voor uw Azure-SSIS IR om lid te worden. Het virtuele netwerk is vereist wanneer u geen openbaar eind punt inschakelt voor uw beheerde exemplaar.<br/><br/>Als u uw Azure-SSIS IR lid maakt van hetzelfde virtuele netwerk als uw beheerde exemplaar, moet u ervoor zorgen dat uw Azure-SSIS IR zich in een ander subnet bevindt dan uw beheerde exemplaar. Als u uw Azure-SSIS IR koppelt aan een ander virtueel netwerk dan uw beheerde exemplaar, raden we u aan een virtuele netwerk-peering of virtueel netwerk te koppelen aan een virtuele netwerk verbinding. Zie [uw toepassing verbinden met Azure SQL database beheerde instantie](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Gedistribueerde trans acties** | Ondersteund door elastische trans acties. MSDTC-trans acties (micro soft Distributed Transaction Coordinator) worden niet ondersteund. Als uw SSIS-pakketten gebruikmaken van MSDTC voor het coördineren van gedistribueerde trans acties, kunt u overwegen om te migreren naar elastische trans acties voor Azure SQL Database. Zie [gedistribueerde trans acties in Cloud databases](../sql-database/sql-database-elastic-transactions-overview.md)voor meer informatie. | Wordt niet ondersteund. |
| | | |

## <a name="azure-portal"></a>Azure Portal

In deze sectie gebruikt u Azure Portal, met name de gebruikers interface (UI)/app van ADF, om Azure-SSIS IR te maken.

### <a name="create-a-data-factory"></a>Data factory maken

Als u uw ADF wilt maken via Azure Portal, volgt u de stapsgewijze instructies in [Create ADF via UI](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) -artikel en selecteert **u vastmaken aan dash board** terwijl u er toegang toe hebt. 

Zodra de ADF is gemaakt, opent u de pagina overzicht op Azure Portal en klikt u op de tegel **auteur & monitor** om de pagina aan de **slag** te starten op een afzonderlijk tabblad waar u uw Azure-SSIS IR kunt blijven maken.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Een Azure SSIS-integratieruntime inrichten

1. Klik op de pagina **aan de slag** op de Integration runtime-tegel **Configure SSIS** .

   ![De tegel SSIS-integratieruntime configureren](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Voer op de pagina **Algemene instellingen** van **Installatie van integratieruntime** de volgende stappen uit:

   ![Algemene instellingen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Voer bij **Naam** de naam van de integratieruntime in.

    b. Voer bij **beschrijving** de beschrijving van de integratieruntime in.

    c. Selecteer bij **Locatie** de locatie voor de integratieruntime. Alleen ondersteunde locaties worden weergegeven. We raden u aan dezelfde locatie van uw databaseserver te selecteren voor het hosten van SSISDB.

    d. Selecteer bij **Knooppuntgrootte** de grootte van knooppunt in het integratieruntimecluster. Alleen ondersteunde knooppuntgrootten worden weergegeven. Selecteer een grote knooppuntgrootte (omhoog schalen) als u veel reken-/geheugenintensieve pakketten wilt uitvoeren.

    e. Selecteer bij **Aantal knooppunten** het aantal knooppunten in het integratieruntimecluster. Alleen ondersteunde knooppuntaantallen worden weergegeven. Selecteer een groot cluster met veel knooppunten (uitschalen), als u veel pakketten parallel wilt uitvoeren.

    f. Selecteer bij **Editie/licentie** de SQL Server-editie/licentie voor uw integratie runtime: Standard of Enterprise. Selecteer Enterprise als u geavanceerde/premium functies in de integratieruntime wilt gebruiken.

    g. Selecteer bij **Geld besparen** de optie Azure Hybrid Benefit (AHB) voor uw integratieruntime: Ja of Nee. Selecteer Ja als u uw eigen SQL Server-licentie met Software Assurance wilt gebruiken om te profiteren van de kostenbesparingen met hybride gebruik.

    h. Klik op **Volgende**.

3. Voer op de pagina **SQL-instellingen** de volgende stappen uit:

   ![SQL-instellingen](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Selecteer in **SSIS Catalog maken...** het implementatie model voor pakketten die moeten worden uitgevoerd op uw Azure-SSIS IR: Het implementatie model van het project waarbij pakketten worden geïmplementeerd in SSISDB die worden gehost door uw database server of pakket implementatie model waarin pakketten worden geïmplementeerd in uw bestands systemen/bestands shares/Azure Files. Als u het controleert, moet u uw eigen database server voor het hosten van SSISDB die u namens u kunt maken en beheren.
   
    b. Selecteer bij **Abonnement** het Azure-abonnement dat uw databaseserver heeft voor het hosten van SSISDB. 

    c. Selecteer bij **Locatie** de locatie van uw databaseserver voor het hosten van SSISDB. We raden u aan dezelfde locatie van uw integratieruntime te selecteren. 

    d. Selecteer bij het **Eindpunt voor de Catalog-databaseserver** het eindpunt van uw databaseserver voor het hosten van SSISDB. Op basis van de geselecteerde databaseserver kan SSISDB namens u worden gemaakt als een enkele database, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie voor hulp bij het kiezen van het type database server om SSISDB te hosten [vergelijken Azure SQL database afzonderlijke data base/elastische groep/beheerd exemplaar](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Als u Azure SQL Database Server met virtuele netwerk service-eind punten/een beheerd exemplaar met een persoonlijk eind punt selecteert om SSISDB te hosten of toegang tot on-premises gegevens nodig hebt zonder zelf-Hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk Zie [Azure-SSIS IR toevoegen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    e. Selecteer op het selectievakje **AAD-verificatie gebruiken...**  de verificatiemethode voor de databaseserver voor het hosten van SSISDB: SQL-verificatie of AAD-verificatie met de beheerde identiteit voor uw ADF. Als u het controleert, moet u de beheerde identiteit voor uw ADF toevoegen aan een AAD-groep met toegangs machtigingen voor uw database server. Zie [Aad-verificatie inschakelen voor Azure-SSIS IR voor](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)meer informatie. 

    f. Voer bij **Gebruikersnaam van beheerder** de gebruikersnaam voor SQL-verificatie voor uw databaseserver voor het hosten van SSISDB in. 

    g. Voer bij **Beheerderswachtwoord** het wachtwoord voor SQL-verificatie voor uw databaseserver voor het hosten van SSISDB in. 

    h. Selecteer bij **Serverlaag catalogusdatabase** de servicelaag voor uw databaseserver voor het hosten van SSISDB: Basic-/Standard-/Premium-laag of de naam van een elastische pool. 

    i. Klik op **Verbinding testen**. Als dit lukt, klikt u op **Volgende**. 

4. Voer op de pagina **Geavanceerde instellingen** de volgende stappen uit:

    ![Geavanceerde instellingen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Selecteer bij het **maximale aantal parallelle uitvoeringen per knooppunt** het maximum aantal pakketten dat gelijktijdig per knooppunt kan worden uitgevoerd in het integratieruntimecluster. Alleen ondersteunde pakketaantallen worden weergegeven. Selecteer een laag getal als u meer dan één kern wilt gebruiken voor het uitvoeren van één groot/zwaar gewichts pakket dat reken-en geheugen intensief is. Selecteer een groot aantal als u een of meer kleine/lichte pakketten in één kern wilt uitvoeren.

    b. Bij **SAS URI aangepaste installatie container**  voert u desgewenst de SAS (Shared Access Signature) URI (Uniform Resource Identifier) van uw Azure Storage Blob-container in waar uw installatiescript en de bijbehorende bestanden zijn opgeslagen. Zie [Aangepaste installatie voor Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Selecteer bij **een virtueel netwerk selecteren...** selectie vakje of u wilt samen voegen met uw Integration runtime-verbinding met een virtueel netwerk. Controleer het als u Azure SQL Database Server met Virtual Network Service-eind punten/een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten of toegang tot on-premises gegevens te vereisen, d.w.z. dat u on-premises gegevens bronnen of-bestemmingen hebt in uw SSIS-pakketten, zonder een zelf-Hostende IR configureren Zie [Azure-SSIS IR toevoegen aan een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Als u het controleert, voert u de volgende stappen uit:

   ![Geavanceerde instellingen met virtueel netwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Selecteer bij **abonnement**het Azure-abonnement met uw virtuele netwerk.

    b. Voor **locatie**is dezelfde locatie van de Integration runtime geselecteerd.

    c. Selecteer bij **type**het type van uw virtuele netwerk: Klassiek of Azure Resource Manager. We raden u aan Azure Resource Manager virtuele netwerk te selecteren, omdat het klassieke virtuele netwerk binnenkort wordt afgeschaft.

    d. Selecteer voor **VNet-naam**de naam van het virtuele netwerk. Dit virtuele netwerk moet hetzelfde zijn als het account dat wordt gebruikt voor Azure SQL Database Server met virtuele netwerk service-eind punten/beheerde exemplaren in een virtueel netwerk om SSISDB of de verbinding met uw on-premises netwerk te hosten.

    e. Selecteer bij **subnet naam**de naam van het subnet voor het virtuele netwerk. Dit moet een ander subnet zijn dat wordt gebruikt voor het beheerde exemplaar in een virtueel netwerk om SSISDB te hosten.

6. Als u zelf-hostende IR wilt configureren als proxy voor uw Azure-SSIS IR, raadpleegt u [zelf-hostende IR instellen als een proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)server op het selectie vakje **zelf gehost...** . Als u het controleert, voert u de volgende stappen uit:

   ![Geavanceerde instellingen met een zelf-Hostende IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

    a. Voor **zelf-hostende Integration runtime**selecteert u uw bestaande zelf-hostende IR als proxy voor Azure-SSIS IR.

    b. Voor **faserings opslag gekoppelde service**selecteert u uw bestaande Azure Blob Storage gekoppelde service, of maakt u een nieuwe voor fase ring.

    c. Geef voor het **tijdelijke pad**een BLOB-container op in uw geselecteerde Azure-Blob Storage of laat het vak leeg om een standaard waarde te gebruiken voor fase ring.

7. Klik op **VNet-validatie** en of vervolgens op **volgende**. 

8. Controleer op de pagina **samen vatting** alle inrichtings instellingen, blad wijzer de aanbevolen documentatie koppelingen en klik op **volt ooien** om het maken van de Integration runtime te starten.

    > [!NOTE]
    > Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid, maar kan het ongeveer 20-30 minuten duren voordat Azure-SSIS IR lid wordt van een virtueel netwerk.
    >
    > Als u SSISDB gebruikt, maakt de ADF-service verbinding met uw database server om SSISDB voor te bereiden. Ook worden machtigingen en instellingen voor uw virtuele netwerk geconfigureerd, indien opgegeven, en worden uw Azure-SSIS IR aan het virtuele netwerk toegevoegd.
    > 
    > Wanneer u een Azure-SSIS IR inricht, worden er ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze onderdelen bieden connectiviteit voor Excel/Access-bestanden en verschillende Azure-gegevens bronnen, naast de gegevens bronnen die al door ingebouwde onderdelen worden ondersteund. U kunt ook extra onderdelen installeren. Zie [aangepaste installatie voor Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

7. Ga in het venster **Verbindingen** naar **Integratieruntimes**, indien nodig. Klik op **Vernieuwen** om de status te vernieuwen.

   ![Status van het maken](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Gebruik de kolom koppelingen onder **acties** om de Integration runtime te stoppen of te starten, te bewerken of te verwijderen. Gebruik de laatste koppeling om JSON-code weer te geven voor de integratieruntime. De knoppen Bewerken en Verwijderen zijn alleen beschikbaar wanneer de IR is gestopt.

   ![Azure SSIS-IR - acties](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integratieruntimes in de portal

1. Ga in de Azure Data Factory-UI naar het tabblad **Bewerken**, klik op **Verbindingen** en ga vervolgens naar het tabblad **Integratieruntimes** om bestaande integratieruntimes in uw gegevensfactory weer te geven.

   ![Bestaande IRs weer geven](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Klik op **Nieuw** om een nieuwe Azure SSIS-IR te maken.

   ![Integratieruntime via menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Als u een Azure SSIS-integratieruntime wilt maken, klikt u op **Nieuw** zoals wordt weergegeven in de afbeelding.

4. Selecteer in het venster Installatie van integratieruntime de optie voor het **verplaatsen van bestaande SSIS-pakketten voor uitvoering in Azure** en klik vervolgens op **Volgende**.

   ![Geef het type integratieruntime op](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Zie de sectie [Een Azure SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure SSIS-IR.

## <a name="azure-powershell"></a>Azure PowerShell

In deze sectie gebruikt u de Azure PowerShell om een Azure-SSIS IR te maken.

### <a name="create-variables"></a>Variabelen maken

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
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Aanmelden en abonnement selecteren

Voeg de volgende code toe aan het script om u aan te melden en uw Azure-abonnement te selecteren:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>De verbinding met de database server valideren

Voeg het volgende script toe om uw Azure SQL Database Server/beheerd exemplaar te valideren.

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
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
    }
}
```

### <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voeg het volgende script toe om automatisch machtigingen/instellingen voor het virtuele netwerk te configureren waarmee uw Azure SSIS Integration Runtime moet worden verbonden.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resource groep](../azure-resource-manager/resource-group-overview.md) met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

Als uw resource groep al bestaat, kopieert u deze code niet naar het script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Data factory maken

Voer de volgende opdracht uit om een data factory te maken.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Een Integration Runtime maken

Voer de volgende opdrachten uit om een Azure SSIS Integration runtime te maken waarmee SSIS-pakketten worden uitgevoerd in Azure.

Als u SSISDB niet gebruikt, kunt u CatalogServerEndpoint-, CatalogPricingTier-en CatalogAdminCredential-para meters weglaten.

Als u geen Azure SQL Database Server met virtuele netwerk service-eind punten/een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten of toegang tot on-premises gegevens nodig hebt, kunt u VNetId en subnet-para meters weglaten of lege waarden voor deze items door geven. U kunt ze ook weglaten als u zelf-Hostende IR als proxy voor uw Azure-SSIS IR configureert om toegang te krijgen tot gegevens on-premises. Als dat niet het geval is, kunt u ze niet weglaten en moet u geldige waarden opgeven uit de configuratie van uw virtuele netwerk. Zie [Azure-SSIS IR lid worden van een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Als u een beheerd exemplaar gebruikt om SSISDB te hosten, kunt u de para meter CatalogPricingTier weglaten of een lege waarde hiervoor door geven. Als dat niet het geval is, kunt u deze niet weglaten en moet u een geldige waarde door geven uit de lijst met ondersteunde prijs categorieën voor Azure SQL Database, Zie [SQL database resource limieten](../sql-database/sql-database-resource-limits.md).

Als u gebruikmaakt van Azure Active Directory (AAD)-verificatie met de beheerde identiteit voor uw ADF om verbinding te maken met de database server, kunt u de para meter CatalogAdminCredential weglaten, maar moet u de beheerde identiteit voor uw ADF toevoegen aan een AAD-groep met toegang machtigingen voor de database server, Zie [Aad-verificatie inschakelen voor Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Als dat niet het geval is, kunt u deze niet weglaten en moet u een geldig object door geven uit de gebruikers naam en het wacht woord van uw server beheerder voor SQL-verificatie

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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
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

### <a name="start-integration-runtime"></a>Integration Runtime starten

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
> Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid, maar kan het ongeveer 20-30 minuten duren voordat Azure-SSIS IR lid wordt van een virtueel netwerk.
>
> Als u SSISDB gebruikt, maakt de ADF-service verbinding met uw database server om SSISDB voor te bereiden. Ook worden machtigingen en instellingen voor uw virtuele netwerk geconfigureerd, indien opgegeven, en worden uw Azure-SSIS IR aan het virtuele netwerk toegevoegd.
> 
> Wanneer u een Azure-SSIS IR inricht, worden er ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze onderdelen bieden connectiviteit voor Excel/Access-bestanden en verschillende Azure-gegevens bronnen, naast de gegevens bronnen die al door ingebouwde onderdelen worden ondersteund. U kunt ook extra onderdelen installeren. Zie [aangepaste installatie voor Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Volledige script

Dit is het volledige script waarmee een Azure SSIS Integration runtime wordt gemaakt.

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
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
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
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
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

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

In deze sectie gebruikt u de sjabloon Azure Resource Manager om Azure SSIS Integration runtime te maken. Hier volgt een voor beeld van een scenario:

1. Maak een JSON-bestand met de volgende Azure Resource Manager sjabloon. Vervang waarden in de vier Kante haken (plaats houders) door uw eigen waarden.

    ```json
    {
      "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Voor het implementeren van de Azure Resource Manager-sjabloon voert u de opdracht New-AzResourceGroupDeployment uit, zoals wordt weer gegeven in het volgende voor beeld, waarbij ADFTutorialResourceGroup de naam is van uw resource groep en ADFTutorialARM. json het bestand is met de JSON-definitie voor uw data factory en Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Met deze opdracht worden uw data factory gemaakt en Azure-SSIS IR, maar wordt de IR niet gestart.

3. Voer start-AzDataFactoryV2IntegrationRuntime opdracht uit om de Azure-SSIS IR te starten:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid, maar kan het ongeveer 20-30 minuten duren voordat Azure-SSIS IR lid wordt van een virtueel netwerk.
>
> Als u SSISDB gebruikt, maakt de ADF-service verbinding met uw database server om SSISDB voor te bereiden. Ook worden machtigingen en instellingen voor uw virtuele netwerk geconfigureerd, indien opgegeven, en worden uw Azure-SSIS IR aan het virtuele netwerk toegevoegd.
> 
> Wanneer u een Azure-SSIS IR inricht, worden er ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze onderdelen bieden connectiviteit voor Excel/Access-bestanden en verschillende Azure-gegevens bronnen, naast de gegevens bronnen die al door ingebouwde onderdelen worden ondersteund. U kunt ook extra onderdelen installeren. Zie [aangepaste installatie voor Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>SSIS-pakketten implementeren

Als u SSISDB gebruikt, kunt u uw pakketten erin implementeren en deze uitvoeren op Azure-SSIS IR met behulp van SSDT/SSMS-hulpprogram ma's die via het server-eind punt verbinding maken met uw database server.  Voor Azure SQL database server/beheerd exemplaar in met een privé-eind punt/beheerde instantie met een openbaar eind punt `<server name>.database.windows.net`, is / `<server name>.<dns prefix>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`de indeling van het server eindpunt. Als u geen gebruik maakt van SSISDB, kunt u uw pakketten implementeren in bestands systemen/bestands shares/Azure files en deze uitvoeren op `dtinstall` Azure-SSIS IR met behulp / / `dtutil` `dtexec` van de opdracht regel Programma's. Zie [SSIS-pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)voor meer informatie. In beide gevallen kunt u uw geïmplementeerde pakketten ook uitvoeren op Azure-SSIS IR met behulp van de uitvoering van SSIS-pakket activiteit in ADF-pijp lijnen. Zie de uitvoering van SSIS-pakketten [aanroepen als een ADF-activiteit voor de eerste klasse](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Volgende stappen

Zie ook andere Azure-SSIS IR onderwerpen in deze documentatie:

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dit artikel bevat informatie over integratie-Runtimes in het algemeen, inclusief Azure-SSIS IR.
- [Azure-SSIS IR bewaken](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over uw Azure-SSIS IR ophaalt en begrijpt.
- [Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel wordt beschreven hoe u uw Azure-SSIS IR kunt stoppen, starten of verwijderen. u ziet ook hoe u uw Azure-SSIS IR kunt uitschalen door meer knoop punten toe te voegen.
- [Azure-SSIS IR toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat informatie over het toevoegen van uw Azure-SSIS IR aan een virtueel netwerk.