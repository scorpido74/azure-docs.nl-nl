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
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: c41efc1cbccf546d803e1131405907bf37dcf00c
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75496567"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>De Azure SSIS Integration runtime inrichten in Azure Data Factory

Deze zelf studie bevat stappen voor het gebruik van de Azure Portal om een Azure-SQL Server Integration Services (SSIS) Integration runtime (IR) in Azure Data Factory in te richten. Een Azure-SSIS IR ondersteunt:

- Het uitvoeren van pakketten die worden geïmplementeerd in de SSIS-catalogus (SSISDB) die wordt gehost door een Azure SQL Database-Server of een beheerd exemplaar (project implementatie model).
- Het uitvoeren van pakketten die zijn geïmplementeerd in bestands systemen, bestands shares of Azure Files (pakket implementatie model). 

Nadat een Azure-SSIS IR is ingericht, kunt u de vertrouwde hulpprogram ma's gebruiken om uw pakketten in azure te implementeren en uit te voeren. Deze hulpprogram ma's omvatten SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) en opdracht regel Programma's zoals `dtinstall`, `dtutil`en `dtexec`.

Zie [Overzicht van integratieruntime in Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime) voor algemene informatie over een Azure-SSIS IR.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Een Azure-SSIS-integratieruntime inrichten.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- **Azure SQL database server (optioneel)** . Als u nog geen databaseserver hebt, maakt u die in Azure Portal voordat u begint. Met Data Factory wordt een SSISDB-exemplaar op deze database server gemaakt. 

  Het wordt aangeraden om de databaseserver in dezelfde Azure-regio te maken als de Integration Runtime. Met deze configuratie kan de integratie-runtime uitvoer logboeken schrijven naar SSISDB zonder overschrijding van Azure-regio's.

  Houd u aan de volgende punten:

  - Op basis van de geselecteerde database server kan het SSISDB-exemplaar namens u worden gemaakt als één data base, als onderdeel van een elastische pool of in een beheerd exemplaar. Het kan toegankelijk zijn in een openbaar netwerk of door lid te worden van een virtueel netwerk. Zie [een Azure SQL database afzonderlijke Data Base, elastische pool en beheerde instantie vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)voor hulp bij het kiezen van het type database server voor het hosten van SSISDB. 
  
    Als u een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [een Azure-SSIS IR in een virtueel netwerk maken](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.

  - Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor de databaseserver. Deze instelling is niet van toepassing wanneer u een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten. Zie [Secure your Azure SQL database](../sql-database/sql-database-security-tutorial.md#create-firewall-rules) (Azure SQL-database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)om deze instelling in te scha kelen met behulp van Power shell.

  - Voeg het IP-adres van de client computer of een bereik van IP-adressen met het IP-adres van de client computer toe aan de lijst met client-IP-adressen in de firewall instellingen voor de database server. Zie [Overzicht van firewallregels op Azure SQL Database-serverniveau en -databaseniveau](../sql-database/sql-database-firewall-configure.md) voor meer informatie.

  - U kunt verbinding maken met de database server door SQL-verificatie te gebruiken met de referenties van uw server beheerder of door Azure AD-verificatie te gebruiken met de beheerde identiteit voor uw data factory. Voor de laatste moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure AD-groep met toegangs machtigingen voor de database server. Zie [een Azure-SSIS IR maken met Azure AD-verificatie](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.

  - Controleer of de database server al een SSISDB-exemplaar heeft. Het inrichten van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaand SSISDB-exemplaar.


> [!NOTE]
> Zie [Data Factory en SSIS IR-Beschik baarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)voor een lijst met Azure-regio's waarin Data Factory en een Azure-SSIS IR op dit moment beschikbaar zijn. 

## <a name="create-a-data-factory"></a>Een data factory maken

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

1. Selecteer in het configuratie scherm van **Integration runtime** de **lift-en Shift-bestaande SSIS-pakketten die moeten worden uitgevoerd in de Azure-** tegel en selecteer **volgende**. 

   ![Geef het type integratieruntime op](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zie de sectie [Een Azure-SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS-IR. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Een Azure-SSIS-integratieruntime inrichten

1. Voer de volgende stappen uit in het gedeelte **algemene instellingen** van **Integration runtime** configuratie venster. 

   ![Algemene instellingen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Voer bij **Naam** de naam van de integratieruntime in. 

   1. Voer bij **beschrijving** de beschrijving van de integratieruntime in. 

   1. Selecteer bij **Locatie** de locatie voor de integratieruntime. Alleen ondersteunde locaties worden weergegeven. We raden u aan dezelfde locatie van uw databaseserver te selecteren voor het hosten van SSISDB. 

   1. Selecteer bij **Knooppuntgrootte** de grootte van knooppunt in het integratieruntimecluster. Alleen ondersteunde knooppuntgrootten worden weergegeven. Selecteer de grootte van een groot knoop punt (omhoog schalen) als u veel reken intensief of geheugenintensieve pakketten wilt uitvoeren. 

   1. Selecteer bij **Aantal knooppunten** het aantal knooppunten in het integratieruntimecluster. Alleen ondersteunde knooppuntaantallen worden weergegeven. Selecteer een groot cluster met veel knoop punten (uitschalen) als u veel pakketten parallel wilt uitvoeren. 

   1. Voor **editie/licentie**selecteert u de SQL Server Edition voor uw Integration runtime: Standard of ENTER prise. Selecteer onderneming als u geavanceerde functies wilt gebruiken in uw Integration runtime. 

   1. Selecteer voor **Bespaar geld**de Azure Hybrid Benefit optie voor de Integration runtime: **Ja** of **Nee**. Selecteer **Ja** als u uw eigen SQL Server licentie met Software Assurance wilt gebruiken om kosten besparingen met hybride gebruik te voor komen. 

   1. Selecteer **Next**. 

1. Voer de volgende stappen uit in de sectie **SQL-instellingen** . 

   ![SQL-instellingen](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Schakel het selectie vakje **SSIS-catalogus maken (SSISDB) die wordt gehost door Azure SQL database server/beheerd exemplaar om uw projecten/pakketten/omgevingen/uitvoerings logboeken** op te slaan in en kies het implementatie model voor pakketten die moeten worden uitgevoerd op uw Azure-SSIS IR. U kiest ofwel het project implementatie model waarin pakketten worden geïmplementeerd in SSISDB die worden gehost door uw database server, of het implementatie model van het pakket waarbij pakketten worden geïmplementeerd in bestands systemen, bestands shares of Azure Files.
   
      Als u het selectie vakje inschakelt, moet u uw eigen database server om SSISDB te hosten die u namens u kunt maken en beheren.
   
      1. Selecteer bij **Abonnement** het Azure-abonnement dat uw databaseserver heeft voor het hosten van SSISDB. 

      1. Selecteer bij **Locatie** de locatie van uw databaseserver voor het hosten van SSISDB. We raden u aan dezelfde locatie van uw integratieruntime te selecteren.

      1. Selecteer bij het **Eindpunt voor de Catalog-databaseserver** het eindpunt van uw databaseserver voor het hosten van SSISDB. 
   
         Op basis van de geselecteerde database server kan het SSISDB-exemplaar namens u worden gemaakt als één data base, als onderdeel van een elastische pool of in een beheerd exemplaar. Het kan toegankelijk zijn in een openbaar netwerk of door lid te worden van een virtueel netwerk. Zie [een Azure SQL database afzonderlijke Data Base, elastische pool en beheerde instantie vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)voor hulp bij het kiezen van het type database server voor het hosten van SSISDB.   

         Als u een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt selecteert om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [een Azure-SSIS IR in een virtueel netwerk maken](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.

      1. Schakel het selectie vakje **Aad-verificatie gebruiken met de beheerde identiteit voor uw ADF** in om de verificatie methode voor uw database server te kiezen voor het hosten van SSISDB. U kiest SQL-verificatie of Azure AD-verificatie met de beheerde identiteit voor uw data factory.

         Als u het selectie vakje inschakelt, moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure AD-groep met toegangs machtigingen voor uw database server. Zie [een Azure-SSIS IR maken met Azure AD-verificatie](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.
   
      1. Voor **beheerders naam**voert u de gebruikers naam van de SQL-verificatie voor uw database server in om SSISDB te hosten. 

      1. Voor **beheerders wachtwoord**voert u het wacht woord voor SQL-verificatie voor uw database server in om SSISDB te hosten. 

      1. Selecteer voor de Servicelaag van de **catalogus database**de servicelaag voor de database server om SSISDB te hosten. Selecteer de laag Basic, Standard of Premium of selecteer de naam van een elastische groep.

      1. Selecteer **Verbinding testen**. Als de test is geslaagd, selecteert u **volgende**. 

1. Voer de volgende stappen uit in de sectie **Geavanceerde instellingen** . 

   ![Geavanceerde instellingen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Voor **maximale parallelle uitvoeringen per knoop punt**selecteert u het maximum aantal pakketten dat gelijktijdig per knoop punt in uw Integration runtime-cluster moet worden uitgevoerd. Alleen ondersteunde pakketaantallen worden weergegeven. Selecteer een laag aantal als u meer dan één kern wilt gebruiken voor het uitvoeren van één groot pakket dat wordt berekend of geheugenintensieve. Selecteer een hoog nummer als u een of meer kleine pakketten wilt uitvoeren in één kern. 

   1. Schakel het selectie vakje **uw Azure-SSIS Integration runtime aanpassen met aanvullende systeem configuraties/onderdeel installaties** in om te kiezen of u standaard instellingen voor aangepaste/snelle installatie wilt toevoegen aan uw Azure-SSIS IR. Zie [aangepaste installatie voor een Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)voor meer informatie.
   
   1. Selecteer **een VNet selecteren voor uw Azure-SSIS Integration runtime om lid te worden van, ADF toestaan om bepaalde netwerk resources te maken en Schakel eventueel uw eigen statische open bare IP-adressen** in om te kiezen of u wilt samen voegen met uw Azure-SSIS IR aan een virtueel netwerk.

      Selecteer deze optie als u een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren. Zie [een Azure-SSIS IR in een virtueel netwerk maken](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie. 
   
   1. Selecteer het selectie vakje **zelf-hostende Integration runtime als proxy voor uw Azure-SSIS Integration runtime instellen** om te kiezen of u een zelf-hostende IR wilt configureren als proxy voor uw Azure-SSIS IR. Zie [een zelf-hostende IR instellen als proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)voor meer informatie.   

   1. Selecteer **Doorgaan**. 

1. Controleer in de sectie **samen vatting** alle inrichtings instellingen, blad wijzer de aanbevolen documentatie koppelingen en selecteer **volt ooien** om het maken van de Integration runtime te starten. 

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

Als u SSISDB gebruikt, kunt u uw pakketten erin implementeren en deze uitvoeren op de Azure-SSIS IR met behulp van SQL Server Data Tools (SSDT) of SQL Server Management Studio (SSMS)-hulpprogram ma's. Deze hulpprogram ma's maken verbinding met uw database server via het server-eind punt: 

- Voor een Azure SQL Database-Server wordt de eindpunt indeling van het server `<server name>.database.windows.net`.
- Voor een beheerd exemplaar met een persoonlijk eind punt is de indeling van het server eindpunt `<server name>.<dns prefix>.database.windows.net`.
- Voor een beheerd exemplaar met een openbaar eind punt is de indeling van het server eindpunt `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Als u SSISDB niet gebruikt, kunt u uw pakketten implementeren in bestands systemen, bestands shares of Azure Files en deze uitvoeren op de Azure-SSIS IR met behulp van de opdracht regel Programma's `dtinstall`, `dtutil`en `dtexec`. Zie [SSIS-pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)voor meer informatie. 

In beide gevallen kunt u uw geïmplementeerde pakketten ook uitvoeren op Azure-SSIS IR met behulp van de activiteit voor het uitvoeren van SSIS-pakketten in Data Factory-pijp lijnen. Zie [de uitvoering van SSIS-pakketten aanroepen als een eerste klasse Data Factory-activiteit](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)voor meer informatie.

Zie ook de volgende SSIS-documentatie: 

- [SSIS-pakketten implementeren, uitvoeren en bewaken in azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Verbinding maken met SSISDB in azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Pakket uitvoeringen plannen in azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie) 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over uw Azure-SSIS-integratieruntime, gaat u verder met het volgende artikel: 

> [!div class="nextstepaction"]
> [Een Azure-SSIS IR aanpassen](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)