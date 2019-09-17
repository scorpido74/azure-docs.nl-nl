---
title: De Azure-SSIS-integratieruntime inrichten| Microsoft Docs
description: Informatie over het inrichten van de Azure-SSIS-integratieruntime in Azure Data Factory, zodat u SSIS-pakketten in Azure kunt implementeren en uitvoeren.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b3b180fe4b465f3e0c7de888043326fd1a43cf23
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009627"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>De Azure-SSIS-integratieruntime inrichten in Azure Data Factory

Deze zelf studie bevat stappen voor het gebruik van de Azure Portal om een Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory (ADF) in te richten. Azure-SSIS IR ondersteunt het uitvoeren van pakketten die zijn geïmplementeerd in SSIS Catalog (SSISDB) die worden gehost door Azure SQL Database Server/beheerd exemplaar (project implementatie model) en die zijn geïmplementeerd in bestands systemen/bestands shares/Azure Files (pakket implementatie model). Als Azure-SSIS IR is ingericht, kunt u vervolgens vertrouwde hulpprogram ma's gebruiken, zoals SQL Server Data tools (SSDT)/SQL Server Management Studio (SSMS) en opdracht regel Programma's `dtinstall`, zoals / `dtutil` / `dtexec`, naar uw pakketten implementeren en uitvoeren in Azure. Zie [Overzicht van integratieruntime in Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime) voor algemene informatie over een Azure-SSIS IR.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Een Azure-SSIS-integratieruntime inrichten.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- **Azure SQL database server (optioneel)** . Als u nog geen database server hebt, maakt u er een in de Azure Portal voordat u aan de slag gaat. ADF maakt SSISDB op deze database server. Het wordt aangeraden om de databaseserver in dezelfde Azure-regio te maken als de Integration Runtime. Met deze configuratie kan de integratie-runtime uitvoer logboeken schrijven naar SSISDB zonder overschrijding van Azure-regio's. 
    - Op basis van de geselecteerde databaseserver kan SSISDB namens u worden gemaakt als een enkele database, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie voor hulp bij het kiezen van het type database server om SSISDB te hosten [vergelijken Azure SQL database afzonderlijke data base/elastische groep/beheerd exemplaar](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Als u Azure SQL Database Server met virtuele netwerk service-eind punten/een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten of toegang tot on-premises gegevens nodig hebt zonder zelf-Hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [Azure-SSIS IR maken in een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor de databaseserver. Dit is niet van toepassing wanneer u Azure SQL Database Server met virtuele netwerk service-eind punten/een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten. Zie [Secure your Azure SQL database](../sql-database/sql-database-security-tutorial.md#create-firewall-rules) (Azure SQL-database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)om deze instelling in te scha kelen met behulp van Power shell.
    - Voeg het IP-adres van de client computer, of een bereik van IP-adressen met het IP-adres van de client computer, toe aan de lijst met client-IP-adressen in de firewall instellingen voor de database server. Zie [Overzicht van firewallregels op Azure SQL Database-serverniveau en -databaseniveau](../sql-database/sql-database-firewall-configure.md) voor meer informatie.
    - U kunt verbinding maken met de database server met behulp van SQL-verificatie met de referenties van de server beheerder of de Azure Active Directory (AAD)-verificatie met de beheerde identiteit voor uw ADF. Voor het laatste moet u de beheerde identiteit voor de ADF toevoegen aan een AAD-groep met machtigingen voor toegang tot de databaseserver. Zie [Azure-SSIS IR met AAD-verificaties maken](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Controleer of de database server al een SSISDB heeft. Het inrichten van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaande SSISDB.

> [!NOTE]
> - Zie [ADF + SSIS IR Beschik baarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)voor een lijst met Azure-REGIO'S waarin ADF en Azure-SSIS IR momenteel beschikbaar zijn. 

## <a name="create-a-data-factory"></a>Data factory maken

Als u uw ADF wilt maken via Azure Portal, volgt u de stapsgewijze instructies in [Create ADF via UI](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) -artikel en selecteert **u vastmaken aan dash board** terwijl u er toegang toe hebt. 

Zodra de ADF is gemaakt, opent u de pagina overzicht op Azure Portal en klikt u op de tegel **auteur & monitor** om de pagina aan de **slag** te starten op een afzonderlijk tabblad waar u uw Azure-SSIS IR kunt blijven maken.   

## <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure SSIS Integration Runtime maken

### <a name="from-the-data-factory-overview"></a>Vanuit het overzicht van Data Factory

1. Klik op de pagina **aan de slag** op de Integration runtime-tegel **Configure SSIS** . 

   ![De tegel SSIS-integratieruntime configureren](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Zie de sectie [Een Azure-SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS-IR. 

### <a name="from-the-authoring-ui"></a>Vanuit de gebruikersinterface Ontwerpen

1. Ga in de gebruikersinterface van Azure Data Factory naar het tabblad **Bewerken**, selecteer **Verbindingen** en ga vervolgens naar het tabblad **Integratieruntimes** om bestaande integratieruntimes in uw gegevensfactory weer te geven. 

   ![Selecties voor het weergeven van bestaande IR’s](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecteer **Nieuw** om een Azure-SSIS-IR te maken. 

   ![Integratieruntime via menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Selecteer in het venster **Installatie van integratieruntime** de**optie voor het verplaatsen van bestaande SSIS-pakketten voor uitvoering in Azure** en selecteer vervolgens **Volgende**. 

   ![Geef het type integratieruntime op](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zie de sectie [Een Azure-SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS-IR. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Een Azure-SSIS-integratieruntime inrichten

1. Voer op de pagina **Algemene instellingen** van **Installatie van integratieruntime** de volgende stappen uit: 

   ![Algemene instellingen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Voer bij **Naam** de naam van de integratieruntime in. 

   b. Voer bij **beschrijving** de beschrijving van de integratieruntime in. 

   c. Selecteer bij **Locatie** de locatie voor de integratieruntime. Alleen ondersteunde locaties worden weergegeven. We raden u aan dezelfde locatie van uw databaseserver te selecteren voor het hosten van SSISDB. 

   d. Selecteer bij **Knooppuntgrootte** de grootte van knooppunt in het integratieruntimecluster. Alleen ondersteunde knooppuntgrootten worden weergegeven. Selecteer een grote knooppuntgrootte (omhoog schalen) als u veel reken-/geheugenintensieve pakketten wilt uitvoeren. 

   e. Selecteer bij **Aantal knooppunten** het aantal knooppunten in het integratieruntimecluster. Alleen ondersteunde knooppuntaantallen worden weergegeven. Selecteer een groot cluster met veel knooppunten (uitschalen), als u veel pakketten parallel wilt uitvoeren. 

   f. Selecteer bij **Editie/licentie** de SQL Server-editie/licentie voor uw integratie runtime: Standard of Enterprise. Selecteer Enterprise als u geavanceerde/premium functies in de integratieruntime wilt gebruiken. 

   g. Selecteer bij **Geld besparen** de optie Azure Hybrid Benefit (AHB) voor uw integratieruntime: Ja of Nee. Selecteer Ja als u uw eigen SQL Server-licentie met Software Assurance wilt gebruiken om te profiteren van de kostenbesparingen met hybride gebruik. 

   h. Klik op **Volgende**. 

1. Voer op de pagina **SQL-instellingen** de volgende stappen uit: 

   ![SQL-instellingen](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Selecteer in **SSIS Catalog maken...** het implementatie model voor pakketten die moeten worden uitgevoerd op uw Azure-SSIS IR: Het implementatie model van het project waarbij pakketten worden geïmplementeerd in SSISDB die worden gehost door uw database server of pakket implementatie model waarin pakketten worden geïmplementeerd in uw bestands systemen/bestands shares/Azure Files. Als u het controleert, moet u uw eigen database server voor het hosten van SSISDB die u namens u kunt maken en beheren.
   
   b. Selecteer bij **Abonnement** het Azure-abonnement dat uw databaseserver heeft voor het hosten van SSISDB. 

   c. Selecteer bij **Locatie** de locatie van uw databaseserver voor het hosten van SSISDB. We raden u aan dezelfde locatie van uw integratieruntime te selecteren.

   d. Selecteer bij het **Eindpunt voor de Catalog-databaseserver** het eindpunt van uw databaseserver voor het hosten van SSISDB. Op basis van de geselecteerde databaseserver kan SSISDB namens u worden gemaakt als een enkele database, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie voor hulp bij het kiezen van het type database server om SSISDB te hosten [vergelijken Azure SQL database afzonderlijke data base/elastische groep/beheerd exemplaar](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Als u Azure SQL Database Server met virtuele netwerk service-eind punten/een beheerd exemplaar met een persoonlijk eind punt selecteert om SSISDB te hosten of toegang tot on-premises gegevens nodig hebt zonder zelf-Hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk Zie [Azure-SSIS IR maken in een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Selecteer op het selectievakje **AAD-verificatie gebruiken...**  de verificatiemethode voor de databaseserver voor het hosten van SSISDB: SQL-verificatie of AAD-verificatie met de beheerde identiteit voor uw ADF. Als u het controleert, moet u de beheerde identiteit voor uw ADF toevoegen aan een AAD-groep met toegangs machtigingen voor uw database server. Zie [Create Azure-SSIS IR with Aad-verificatie](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. Voer bij **Gebruikersnaam van beheerder** de gebruikersnaam voor SQL-verificatie voor uw databaseserver voor het hosten van SSISDB in. 

   g. Voer bij **Beheerderswachtwoord** het wachtwoord voor SQL-verificatie voor uw databaseserver voor het hosten van SSISDB in. 

   h. Selecteer bij **Serverlaag catalogusdatabase** de servicelaag voor uw databaseserver voor het hosten van SSISDB: Basic-/Standard-/Premium-laag of de naam van een elastische pool.

   i. Klik op **Verbinding testen**. Als dit lukt, klikt u op **Volgende**. 

1. Voer op de pagina **Geavanceerde instellingen** de volgende stappen uit: 

   ![Geavanceerde instellingen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Selecteer bij het **maximale aantal parallelle uitvoeringen per knooppunt** het maximum aantal pakketten dat gelijktijdig per knooppunt kan worden uitgevoerd in het integratieruntimecluster. Alleen ondersteunde pakketaantallen worden weergegeven. Selecteer een klein aantal als u meer dan één kern wilt gebruiken om een enkel groot/zwaar pakket uit te voeren dat reken-/geheugenintensief is. Selecteer een groot aantal als u een of meer kleine/lichte pakketten in één kern wilt uitvoeren. 

   b. Bij **SAS URI aangepaste installatie container**  voert u desgewenst de SAS (Shared Access Signature) URI (Uniform Resource Identifier) van uw Azure Storage Blob-container in waar uw installatiescript en de bijbehorende bestanden zijn opgeslagen. Zie [Aangepaste installatie voor Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Geef via het selectievakje **Een VNet selecteren...**  aan of u de integratieruntime aan een virtueel netwerk wilt toevoegen. U moet dit controleren als u Azure SQL Database Server met Virtual Network Service-eind punten/een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten of toegang tot on-premises gegevens nodig hebt zonder zelf-Hostende IR te configureren, raadpleegt u [Azure-SSIS IR in een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. Als u zelf-hostende IR wilt configureren als proxy voor uw Azure-SSIS IR, raadpleegt u [zelf-hostende IR instellen als een proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)server op het selectie vakje **zelf gehost...** . 

1. Klik op **Volgende**. 

1. Controleer op de pagina **samen vatting** alle inrichtings instellingen, blad wijzer de aanbevolen documentatie koppelingen en klik op **volt ooien** om het maken van de Integration runtime te starten. 

   > [!NOTE]
   > Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid.
   >
   > Als u SSISDB gebruikt, maakt de ADF-service verbinding met uw database server om SSISDB voor te bereiden. 
   > 
   > Wanneer u een Azure-SSIS IR inricht, worden er ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze onderdelen bieden connectiviteit voor Excel/Access-bestanden en verschillende Azure-gegevens bronnen, naast de gegevens bronnen die al door ingebouwde onderdelen worden ondersteund. U kunt ook extra onderdelen installeren. Zie [aangepaste installatie voor Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Ga op het tabblad **Verbindingen** indien nodig naar **Integratieruntimes**. Selecteer **Vernieuwen** om de status te vernieuwen. 

   ![De status vernieuwen](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Gebruik de koppelingen in de kolom **Acties** om de integratieruntime te stoppen/starten, te bewerken of te verwijderen. Gebruik de laatste koppeling om JSON-code weer te geven voor de integratieruntime. De knoppen Bewerken en Verwijderen zijn alleen beschikbaar wanneer de IR is gestopt. 

   ![Koppelingen in de kolom 'Acties'](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS-pakketten implementeren

Als u SSISDB gebruikt, kunt u uw pakketten erin implementeren en deze uitvoeren op Azure-SSIS IR met behulp van SSDT/SSMS-hulpprogram ma's die via het server-eind punt verbinding maken met uw database server. Voor Azure SQL database server/beheerd exemplaar met een openbaar eind punt is `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`de indeling van het server eindpunt respectievelijk. Als u geen gebruik maakt van SSISDB, kunt u uw pakketten implementeren in bestands systemen/bestands shares/Azure files en deze uitvoeren op `dtinstall` Azure-SSIS IR met behulp / / `dtutil` `dtexec` van de opdracht regel Programma's. Zie [SSIS-pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)voor meer informatie. In beide gevallen kunt u uw geïmplementeerde pakketten ook uitvoeren op Azure-SSIS IR met behulp van de uitvoering van SSIS-pakket activiteit in ADF-pijp lijnen. Zie de uitvoering van SSIS-pakketten [aanroepen als een ADF-activiteit voor de eerste klasse](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Zie ook de volgende artikelen uit de SSIS-documentatie: 

- [SSIS-pakketten implementeren, uitvoeren en bewaken in azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Pakket uitvoeringen plannen in azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie) 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd: 

> [!div class="checklist"]
> * Een data factory maken.
> * Een Azure-SSIS-integratieruntime inrichten.
> * SSIS-pakketten implementeren

Voor meer informatie over uw Azure-SSIS-integratieruntime, gaat u verder met het volgende artikel: 

> [!div class="nextstepaction"]
> [Azure-SSIS IR aanpassen](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)