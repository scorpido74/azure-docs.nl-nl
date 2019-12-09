---
title: De Azure SSIS Integration runtime inrichten
description: Informatie over het inrichten van de Azure-SSIS-integratieruntime in Azure Data Factory, zodat u SSIS-pakketten in Azure kunt implementeren en uitvoeren.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 2cec7fdae1d3a2a336decc11347ef9bd1039ce7f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926554"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>De Azure SSIS Integration runtime inrichten in Azure Data Factory

Deze zelf studie bevat stappen voor het gebruik van de Azure Portal om een Azure-SQL Server Integration Services (SSIS) Integration runtime (IR) in Azure Data Factory in te richten. Een Azure-SSIS IR ondersteunt:

- Het uitvoeren van pakketten die worden geïmplementeerd in de SSIS-catalogus (SSISDB) die wordt gehost door een Azure SQL Database-Server of een beheerd exemplaar (project implementatie model).
- Het uitvoeren van pakketten die zijn geïmplementeerd in bestands systemen, bestands shares of Azure Files (pakket implementatie model). 

Nadat een Azure-SSIS IR is ingericht, kunt u de vertrouwde hulpprogram ma's gebruiken om uw pakketten in azure te implementeren en uit te voeren. Deze hulpprogram ma's omvatten SQL Server Data Tools, SQL Server Management Studio en opdracht regel Programma's zoals `dtinstall`, `dtutil`en `dtexec`.

Zie [Overzicht van integratieruntime in Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime) voor algemene informatie over een Azure-SSIS IR.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maakt.
> * Een Azure-SSIS-integratieruntime inrichten.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- **Azure SQL database server (optioneel)** . Als u nog geen databaseserver hebt, maakt u die in Azure Portal voordat u begint. Met Data Factory wordt een SSISDB-exemplaar op deze database server gemaakt. 

  Het wordt aangeraden om de databaseserver in dezelfde Azure-regio te maken als de Integration Runtime. Met deze configuratie kan de integratie-runtime uitvoer logboeken schrijven naar SSISDB zonder overschrijding van Azure-regio's.

  Houd u aan de volgende punten:

  - Op basis van de geselecteerde database server kan het SSISDB-exemplaar namens u worden gemaakt als één data base, als onderdeel van een elastische pool of in een beheerd exemplaar. Het kan toegankelijk zijn in een openbaar netwerk of door lid te worden van een virtueel netwerk. Zie [een Azure SQL database afzonderlijke Data Base, elastische pool en beheerde instantie vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)voor hulp bij het kiezen van het type database server voor het hosten van SSISDB. 
  
    Als u een Azure SQL Database Server met virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten of als u toegang nodig hebt tot on-premises gegevens zonder zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [een Azure-SSIS IR in een virtueel netwerk maken](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.
  - Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor de databaseserver. Deze instelling is niet van toepassing wanneer u een Azure SQL Database Server met virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten. Zie [Secure your Azure SQL database](../sql-database/sql-database-security-tutorial.md#create-firewall-rules) (Azure SQL-database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)om deze instelling in te scha kelen met behulp van Power shell.
  - Voeg het IP-adres van de client computer of een bereik van IP-adressen met het IP-adres van de client computer toe aan de lijst met client-IP-adressen in de firewall instellingen voor de database server. Zie [Overzicht van firewallregels op Azure SQL Database-serverniveau en -databaseniveau](../sql-database/sql-database-firewall-configure.md) voor meer informatie.
  - U kunt verbinding maken met de database server door SQL-verificatie te gebruiken met de referenties van uw server beheerder of door Azure AD-verificatie te gebruiken met de beheerde identiteit voor uw data factory. Voor de laatste moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure AD-groep met toegangs machtigingen voor de database server. Zie [een Azure-SSIS IR maken met Azure AD-verificatie](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.
  - Controleer of de database server al een SSISDB-exemplaar heeft. Het inrichten van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaand SSISDB-exemplaar.


> [!NOTE]
> Zie [Data Factory en SSIS IR-Beschik baarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)voor een lijst met Azure-regio's waarin Data Factory en een Azure-SSIS IR op dit moment beschikbaar zijn. 

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Als u uw data factory via de Azure Portal wilt maken, volgt u de stapsgewijze instructies in [een Data Factory maken via de gebruikers interface](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Selecteer **in dat geval vastmaken aan dash board** om snelle toegang toe te staan nadat het is gemaakt. 

Nadat uw data factory is gemaakt, opent u de pagina overzicht in de Azure Portal. Selecteer de tegel **Author & monitor** om de pagina aan de **slag** te openen op een afzonderlijk tabblad. Daar kunt u door gaan met het maken van uw Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure SSIS Integration Runtime maken

### <a name="from-the-data-factory-overview"></a>Vanuit het overzicht van Data Factory

1. Selecteer op de pagina **Aan de slag** de tegel **SSIS-integratieruntime configureren**. 

   ![De tegel SSIS-integratieruntime configureren](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Zie de sectie [Een Azure-SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS-IR. 

### <a name="from-the-authoring-ui"></a>Vanuit de gebruikers interface voor ontwerpen

1. Ga in de Azure Data Factory gebruikers interface naar het tabblad **bewerken** en selecteer **verbindingen**. Schakel vervolgens over naar het tabblad **Integration Runtimes** om bestaande integratie-Runtimes in uw Data Factory weer te geven. 

   ![Selecties voor het weergeven van bestaande IR’s](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecteer **Nieuw** om een Azure-SSIS-IR te maken. 

   ![Integratieruntime via menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Selecteer in het venster **Installatie van integratieruntime** de**optie voor het verplaatsen van bestaande SSIS-pakketten voor uitvoering in Azure** en selecteer vervolgens **Volgende**. 

   ![Geef het type integratieruntime op](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zie de sectie [Een Azure-SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS-IR. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Een Azure-SSIS-integratieruntime inrichten

1. Voer de volgende stappen uit op de pagina **algemene instellingen** van **Integration runtime Setup**. 

   ![Algemene instellingen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Voer bij **Naam** de naam van de integratieruntime in. 

   b. Voer bij **beschrijving** de beschrijving van de integratieruntime in. 

   c. Selecteer bij **Locatie** de locatie voor de integratieruntime. Alleen ondersteunde locaties worden weergegeven. We raden u aan dezelfde locatie van uw databaseserver te selecteren voor het hosten van SSISDB. 

   d. Selecteer bij **Knooppuntgrootte** de grootte van knooppunt in het integratieruntimecluster. Alleen ondersteunde knooppuntgrootten worden weergegeven. Selecteer de grootte van een groot knoop punt (omhoog schalen) als u veel reken intensief of geheugenintensieve pakketten wilt uitvoeren. 

   e. Selecteer bij **Aantal knooppunten** het aantal knooppunten in het integratieruntimecluster. Alleen ondersteunde knooppuntaantallen worden weergegeven. Selecteer een groot cluster met veel knoop punten (uitschalen) als u veel pakketten parallel wilt uitvoeren. 

   f. Voor **editie/licentie**selecteert u de SQL Server Edition voor uw Integration runtime: Standard of ENTER prise. Selecteer onderneming als u geavanceerde functies wilt gebruiken in uw Integration runtime. 

   g. Selecteer voor **Bespaar geld**de Azure Hybrid Benefit optie voor de Integration runtime: **Ja** of **Nee**. Selecteer **Ja** als u uw eigen SQL Server licentie met Software Assurance wilt gebruiken om kosten besparingen met hybride gebruik te voor komen. 

   h. Selecteer **Next**. 

1. Voer op de pagina **SQL-instellingen** de volgende stappen uit. 

   ![SQL-instellingen](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Schakel het selectie vakje **SSIS-catalogus maken** in om het implementatie model voor pakketten te kiezen dat u op uw Azure-SSIS IR wilt uitvoeren. U kiest ofwel het project implementatie model waarin pakketten worden geïmplementeerd in SSISDB die worden gehost door uw database server, of het implementatie model van het pakket waarbij pakketten worden geïmplementeerd in bestands systemen, bestands shares of Azure Files.
   
   Als u het selectie vakje inschakelt, moet u uw eigen database server om SSISDB te hosten die u namens u kunt maken en beheren.
   
   b. Selecteer bij **Abonnement** het Azure-abonnement dat uw databaseserver heeft voor het hosten van SSISDB. 

   c. Selecteer bij **Locatie** de locatie van uw databaseserver voor het hosten van SSISDB. We raden u aan dezelfde locatie van uw integratieruntime te selecteren.

   d. Selecteer bij het **Eindpunt voor de Catalog-databaseserver** het eindpunt van uw databaseserver voor het hosten van SSISDB. 
   
   Op basis van de geselecteerde database server kan het SSISDB-exemplaar namens u worden gemaakt als één data base, als onderdeel van een elastische pool of in een beheerd exemplaar. Het kan toegankelijk zijn in een openbaar netwerk of door lid te worden van een virtueel netwerk. Zie [een Azure SQL database afzonderlijke Data Base, elastische pool en beheerde instantie vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)voor hulp bij het kiezen van het type database server voor het hosten van SSISDB.   

   Als u een Azure SQL Database Server met Service-eind punten van een virtueel netwerk of een beheerd exemplaar met een persoonlijk eind punt selecteert om SSISDB te hosten of als u toegang nodig hebt tot on-premises gegevens zonder zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [een Azure-SSIS IR in een virtueel netwerk maken](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.

   e. Schakel het selectie vakje **Aad-verificatie gebruiken met de beheerde identiteit voor uw ADF** in om de verificatie methode voor uw database server te kiezen voor het hosten van SSISDB. U kiest SQL-verificatie of Azure AD-verificatie met de beheerde identiteit voor uw data factory.

   Als u het selectie vakje inschakelt, moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure AD-groep met toegangs machtigingen voor uw database server. Zie [een Azure-SSIS IR maken met Azure AD-verificatie](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.
   
   f. Voor **beheerders naam**voert u de gebruikers naam van de SQL-verificatie voor uw database server in om SSISDB te hosten. 

   g. Voor **beheerders wachtwoord**voert u het wacht woord voor SQL-verificatie voor uw database server in om SSISDB te hosten. 

   h. Selecteer voor de Servicelaag van de **catalogus database**de servicelaag voor de database server om SSISDB te hosten. Selecteer de laag Basic, Standard of Premium of selecteer de naam van een elastische groep.

   i. Selecteer **Verbinding testen**. Als de test is geslaagd, selecteert u **volgende**. 

1. Voer de volgende stappen uit op de pagina **Geavanceerde instellingen** . 

   ![Geavanceerde instellingen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Voor **maximale parallelle uitvoeringen per knoop punt**selecteert u het maximum aantal pakketten dat gelijktijdig per knoop punt in uw Integration runtime-cluster moet worden uitgevoerd. Alleen ondersteunde pakketaantallen worden weergegeven. Selecteer een laag aantal als u meer dan één kern wilt gebruiken voor het uitvoeren van één groot pakket dat wordt berekend of geheugenintensieve. Selecteer een hoog nummer als u een of meer kleine pakketten wilt uitvoeren in één kern. 

   b. Voor de SAS-URI voor de **aangepaste installatie container**voert u optioneel de SAS-URI (Shared Access Signature) in van de Azure Blob Storage-container waar uw installatie script en de bijbehorende bestanden worden opgeslagen. Zie [aangepaste installatie voor een Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)voor meer informatie.
   
   c. Schakel het selectie vakje **Selecteer een VNet voor uw Azure-SSIS Integration runtime om te koppelen en ADF toestaan bepaalde netwerk resources te maken** in om te kiezen of u wilt samen voegen met een virtueel netwerk.

   Selecteer deze optie als u een Azure SQL Database Server met virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren. Zie [een Azure-SSIS IR in een virtueel netwerk maken](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie. 
   
   d. Selecteer het selectie vakje **zelf-hostende Integration runtime als proxy voor uw Azure-SSIS Integration runtime instellen** om te kiezen of u een zelf-hostende IR wilt configureren als proxy voor uw Azure-SSIS IR. Zie [een zelf-hostende IR instellen als proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)voor meer informatie.   

1. Selecteer **Next**. 

1. Controleer op de pagina **samen vatting** alle inrichtings instellingen, blad wijzer de aanbevolen documentatie koppelingen en selecteer **volt ooien** om het maken van de Integration runtime te starten. 

   > [!NOTE]
   > Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid.
   >
   > Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw database server om SSISDB voor te bereiden. 
   > 
   > Wanneer u een Azure-SSIS IR inricht, worden er ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze onderdelen bieden connectiviteit voor Excel-bestanden, Access-bestanden en verschillende Azure-gegevens bronnen, naast de gegevens bronnen die in de ingebouwde onderdelen al worden ondersteund. Zie [aangepaste installatie voor een Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md)voor informatie over andere onderdelen die u kunt installeren.

1. Ga op het tabblad **Verbindingen** indien nodig naar **Integratieruntimes**. Selecteer **Vernieuwen** om de status te vernieuwen. 

   ![De status vernieuwen](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Gebruik de koppelingen in de kolom **Acties** om de integratieruntime te stoppen/starten, te bewerken of te verwijderen. Gebruik de laatste koppeling om JSON-code weer te geven voor de integratieruntime. De knoppen Bewerken en Verwijderen zijn alleen beschikbaar wanneer de IR is gestopt. 

   ![Koppelingen in de kolom 'Acties'](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS-pakketten implementeren

Als u SSISDB gebruikt, kunt u uw pakketten erin implementeren en deze uitvoeren op de Azure-SSIS IR met behulp van SQL Server Data Tools of SQL Server Management Studio hulpprogram ma's. Deze hulpprogram ma's maken verbinding met uw database server via het server-eind punt: 

- Voor een Azure SQL Database-Server met een persoonlijk eind punt, wordt de indeling van het server eindpunt `<server name>.database.windows.net`.
- Voor een beheerd exemplaar met een persoonlijk eind punt is de indeling van het server eindpunt `<server name>.<dns prefix>.database.windows.net`.
- Voor een beheerd exemplaar met een openbaar eind punt is de indeling van het server eindpunt `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Als u SSISDB niet gebruikt, kunt u uw pakketten implementeren in bestands systemen, bestands shares of Azure Files. U kunt deze vervolgens uitvoeren op de Azure-SSIS IR met behulp van de opdracht regel Programma's `dtinstall`, `dtutil`en `dtexec`. Zie [SSIS-pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)voor meer informatie. 

In beide gevallen kunt u uw geïmplementeerde pakketten ook uitvoeren op het Azure-SSIS IR met behulp van de activiteit SSIS-pakket uitvoeren in Data Factory pijp lijnen. Zie [de uitvoering van SSIS-pakketten aanroepen als een eerste klasse Data Factory-activiteit](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)voor meer informatie.

Zie ook de volgende SSIS-documentatie: 

- [SSIS-pakketten implementeren, uitvoeren en bewaken in azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Pakket uitvoeringen plannen in azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie) 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over uw Azure-SSIS-integratieruntime, gaat u verder met het volgende artikel: 

> [!div class="nextstepaction"]
> [Een Azure-SSIS IR aanpassen](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)