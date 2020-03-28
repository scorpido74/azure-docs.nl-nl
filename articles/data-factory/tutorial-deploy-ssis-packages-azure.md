---
title: De runtime van Azure-SSIS-integratie inrichten
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75496567"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>De runtime voor Azure-SSIS-integratie inAzure Data Factory inrichten

Deze zelfstudie bevat stappen voor het gebruik van de Azure-portal voor het inrichten van een SSIS-runtime (Azure-SQL Server Integration Services) (IR) in Azure Data Factory. Een Azure-SSIS IR ondersteunt:

- Uitvoeren van pakketten die zijn geïmplementeerd in de SSIS-catalogus (SSISDB) die worden gehost door een Azure SQL Database-server of een beheerde instantie (Project Deployment Model).
- Uitvoeren van pakketten die zijn geïmplementeerd in bestandssystemen, bestandsshares of Azure Files (Package Deployment Model). 

Nadat een Azure-SSIS IR is ingericht, u vertrouwde hulpprogramma's gebruiken om uw pakketten in Azure te implementeren en uit te voeren. Deze hulpprogramma's omvatten SQL Server Data Tools (SSDT), SQL Server `dtinstall`Management `dtutil`Studio `dtexec`(SSMS) en command-line tools zoals , en .

Zie [Overzicht van integratieruntime in Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime) voor algemene informatie over een Azure-SSIS IR.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Een Azure-SSIS-integratieruntime inrichten.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- **Azure SQL Database-server (optioneel).** Als u nog geen databaseserver hebt, maakt u die in Azure Portal voordat u begint. Data Factory maakt op zijn beurt een SSISDB-exemplaar op deze databaseserver. 

  Het wordt aangeraden om de databaseserver in dezelfde Azure-regio te maken als de Integration Runtime. Met deze configuratie kan de uitvoering van de integratieruntime worden opgeslagen in SSISDB zonder Azure-regio's te overschrijden.

  Houd rekening met deze punten:

  - Op basis van de geselecteerde databaseserver kan de SSISDB-instantie namens u worden gemaakt als één database, als onderdeel van een elastische groep of in een beheerde instantie. Het kan toegankelijk zijn in een openbaar netwerk of door lid te worden van een virtueel netwerk. Zie [Een azure SQL Database-database, elastische pool en beheerde instantie vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)voor richtlijnen voor het kiezen van het type databaseserver om SSISDB te hosten. 
  
    Als u een Azure SQL Database-server met IP-firewallregels/eindpunten voor virtuele netwerkservices of een beheerde instantie met privéeindpunt gebruikt om SSISDB te hosten, of als u toegang nodig hebt tot on-premises gegevens zonder een zelf gehoste IR te configureren, moet u uw Azure-SSIS IR aansluiten bij een virtueel netwerk. Zie [Een Azure-SSIS IR maken in een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.

  - Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor de databaseserver. Deze instelling is niet van toepassing wanneer u een Azure SQL Database-server gebruikt met IP-firewallregels/eindpunten voor virtuele netwerkservices of een beheerde instantie met privéeindpunt om SSISDB te hosten. Zie [Secure your Azure SQL database](../sql-database/sql-database-security-tutorial.md#create-firewall-rules) (Azure SQL-database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)om deze instelling in te schakelen met PowerShell.

  - Voeg het IP-adres van de clientmachine of een reeks IP-adressen die het IP-adres van de clientmachine bevat, toe aan de lijst met client-IP-adres in de firewall-instellingen voor de databaseserver. Zie [Overzicht van firewallregels op Azure SQL Database-serverniveau en -databaseniveau](../sql-database/sql-database-firewall-configure.md) voor meer informatie.

  - U verbinding maken met de databaseserver door SQL-verificatie te gebruiken met uw serverbeheerdersreferenties of door Azure AD-verificatie te gebruiken met de beheerde identiteit voor uw gegevensfabriek. Voor dit laatste moet u de beheerde identiteit van uw gegevensfabriek toevoegen aan een Azure AD-groep met toegangsmachtigingen voor de databaseserver. Zie [Een Azure-SSIS IR maken met Azure AD-verificatie](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.

  - Controleer of uw databaseserver nog geen SSISDB-exemplaar heeft. De inrichting van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaand SSISDB-exemplaar.


> [!NOTE]
> Zie [Gegevensfabriek en SSIS IR beschikbaarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)voor een lijst met Azure-regio's en een Azure-SSIS IR. 

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Als u uw gegevensfabriek wilt maken via de Azure-portal, volgt u de stapsgewijze instructies in [Een gegevensfabriek maken via de gebruikersinterface.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) Selecteer **Vastmaken aan** het dashboard terwijl u dit doet, zodat u snel toegang krijgen na het maken ervan. 

Nadat uw gegevensfabriek is gemaakt, opent u de overzichtspagina in de Azure-portal. Selecteer de tegel **Auteur & monitor** om de pagina Laten **beginnen** op een apart tabblad te openen. Daar u uw Azure-SSIS IR blijven maken.

## <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure SSIS Integration Runtime maken

### <a name="from-the-data-factory-overview"></a>Vanuit het overzicht van Data Factory

1. Selecteer op de pagina **Aan de slag** de tegel **SSIS-integratieruntime configureren**. 

   ![De tegel SSIS-integratieruntime configureren](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Zie de sectie [Een Azure-SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS-IR. 

### <a name="from-the-authoring-ui"></a>Van de gebruikersinterface voor het schrijven

1. Schakel in de gebruikersinterface van Azure Data Factory over naar het tabblad **Bewerken** en selecteer **Verbindingen**. Ga vervolgens over naar het tabblad **Programma's voor integratie** om bestaande werktijden voor integratie in uw gegevensfabriek weer te geven. 

   ![Selecties voor het weergeven van bestaande IR’s](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecteer **Nieuw** om een Azure-SSIS-IR te maken. 

   ![Integratieruntime via menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Selecteer **in** het deelvenster Instellingen voor uitvoering van de integratie de **bestaande SSIS-pakketten optillen en verschuiven om uit te voeren in Azure-tegel** en selecteer **Volgende**. 

   ![Geef het type integratieruntime op](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zie de sectie [Een Azure-SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS-IR. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Een Azure-SSIS-integratieruntime inrichten

1. Voer in het gedeelte **Algemene instellingen** van het deelvenster Instellingen voor **integratie runtime-instellingen** de volgende stappen uit. 

   ![Algemene instellingen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Voer bij **Naam** de naam van de integratieruntime in. 

   1. Voer bij **beschrijving** de beschrijving van de integratieruntime in. 

   1. Selecteer bij **Locatie** de locatie voor de integratieruntime. Alleen ondersteunde locaties worden weergegeven. We raden u aan dezelfde locatie van uw databaseserver te selecteren voor het hosten van SSISDB. 

   1. Selecteer bij **Knooppuntgrootte** de grootte van knooppunt in het integratieruntimecluster. Alleen ondersteunde knooppuntgrootten worden weergegeven. Selecteer een grote knooppuntgrootte (opschalen) als u veel rekenintensieve of geheugenintensieve pakketten wilt uitvoeren. 

   1. Selecteer bij **Aantal knooppunten** het aantal knooppunten in het integratieruntimecluster. Alleen ondersteunde knooppuntaantallen worden weergegeven. Selecteer een groot cluster met veel knooppunten (uitschalen) als u veel pakketten parallel wilt uitvoeren. 

   1. Selecteer voor **Edition/License**de SQL Server-editie voor uw inburgeringsruntime: Standard of Enterprise. Selecteer Enterprise als u geavanceerde functies wilt gebruiken in uw inburgeringsruntime. 

   1. Selecteer voor **geld besparen**de azure hybrid benefit-optie voor uw ingebruiksruntime voor integratie: **ja** of **nee.** Selecteer **Ja** als u uw eigen SQL Server-licentie met Software Assurance wilt meenemen om te profiteren van kostenbesparingen bij hybride gebruik. 

   1. Selecteer **Volgende**. 

1. Voer in de sectie **SQL-instellingen** de volgende stappen uit. 

   ![SQL-instellingen](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Schakel het selectievakje **SSIS-catalogus (SSISDB) maken dat wordt gehost door Azure SQL Database server/Managed Instance in om het selectievakje Projecten/pakketten/omgevingen/uitvoeringslogboeken op te slaan** om het implementatiemodel te kiezen voor pakketten die op uw Azure-SSIS IR moeten worden uitgevoerd. U kiest het ProjectDeployment-model waarbij pakketten worden geïmplementeerd in SSISDB dat wordt gehost door uw databaseserver, of het pakketimplementatiemodel waarbij pakketten worden geïmplementeerd in bestandssystemen, bestandsshares of Azure-bestanden.
   
      Als u het selectievakje inschakelt, moet u uw eigen databaseserver meenemen om SSISDB te hosten die we namens u zullen maken en beheren.
   
      1. Selecteer bij **Abonnement** het Azure-abonnement dat uw databaseserver heeft voor het hosten van SSISDB. 

      1. Selecteer bij **Locatie** de locatie van uw databaseserver voor het hosten van SSISDB. We raden u aan dezelfde locatie van uw integratieruntime te selecteren.

      1. Selecteer bij het **Eindpunt voor de Catalog-databaseserver** het eindpunt van uw databaseserver voor het hosten van SSISDB. 
   
         Op basis van de geselecteerde databaseserver kan de SSISDB-instantie namens u worden gemaakt als één database, als onderdeel van een elastische groep of in een beheerde instantie. Het kan toegankelijk zijn in een openbaar netwerk of door lid te worden van een virtueel netwerk. Zie [Een azure SQL Database-database, elastische pool en beheerde instantie vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)voor richtlijnen voor het kiezen van het type databaseserver om SSISDB te hosten.   

         Als u een Azure SQL Database-server selecteert met IP-firewallregels/eindpunten voor virtuele netwerkservices of een beheerde instantie met een privéeindpunt om SSISDB te hosten, of als u toegang nodig hebt tot on-premises gegevens zonder een zelf gehoste IR te configureren, moet u uw Azure-SSIS IR aansluiten bij een virtueel netwerk. Zie [Een Azure-SSIS IR maken in een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.

      1. Schakel het selectievakje **AAD-verificatie gebruiken met de beheerde identiteit voor uw ADF** in om de verificatiemethode voor uw databaseserver te kiezen om SSISDB te hosten. U kiest SQL-verificatie of Azure AD-verificatie met de beheerde identiteit voor uw gegevensfabriek.

         Als u het selectievakje inschakelt, moet u de beheerde identiteit van uw gegevensfabriek toevoegen aan een Azure AD-groep met toegangsmachtigingen aan uw databaseserver. Zie [Een Azure-SSIS IR maken met Azure AD-verificatie](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie.
   
      1. Voer voor **gebruikersnaam voor beheerders**de SQL-verificatiegebruikersnaam in voor uw databaseserver om SSISDB te hosten. 

      1. Voer voor **beheerderswachtwoord**het SQL-verificatiewachtwoord in voor uw databaseserver om SSISDB te hosten. 

      1. Selecteer **voor Catalog Database Service Tier**de servicelaag voor uw databaseserver om SSISDB te hosten. Selecteer de laag Basic, Standard of Premium of selecteer een elastische poolnaam.

      1. Selecteer **Testverbinding**. Als de test is geslaagd, selecteert u **Volgende**. 

1. Voer in de sectie **Geavanceerde instellingen** de volgende stappen uit. 

   ![Geavanceerde instellingen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Selecteer **voor maximale parallelle uitvoeringen per knooppunt**het maximum aantal pakketten dat gelijktijdig per knooppunt in uw runtimecluster voor integratie moet worden uitgevoerd. Alleen ondersteunde pakketaantallen worden weergegeven. Selecteer een laag getal als u meer dan één kern wilt gebruiken om één groot pakket uit te voeren dat reken- of geheugenintensief is. Selecteer een hoog getal als u een of meer kleine pakketten in één kern wilt uitvoeren. 

   1. Schakel het selectievakje **Runtime voor Azure-SSIS-integratie aanpassen met extra systeemconfiguraties/componentinstallaties** in om te kiezen of u aangepaste standaard-/expres-instellingen wilt toevoegen aan uw Azure-SSIS IR. Zie [Aangepaste installatie voor een Azure-SSIS IR voor](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)meer informatie.
   
   1. Schakel **het selectievakje Een VNet selecteren in voor uw Azure-SSIS-integratieruntime om lid te worden, ADF in staat te stellen bepaalde netwerkbronnen te maken en optioneel uw eigen statische openbare IP-adressen mee** te nemen om te kiezen of u uw Azure-SSIS IR wilt aansluiten bij een virtueel netwerk.

      Selecteer deze als u een Azure SQL Database-server gebruikt met IP-firewallregels/eindpunten voor virtuele netwerkservices of een beheerde instantie met privéeindpunt om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf gehoste IR te configureren. Zie [Een Azure-SSIS IR maken in een virtueel netwerk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)voor meer informatie. 
   
   1. Schakel het selectievakje **Runtime voor zelfgehoste integratie instellen als proxy voor uw Azure-SSIS-integratieruntime** in om te kiezen of u een zelf gehoste IR wilt configureren als proxy voor uw Azure-SSIS IR. Zie [Een zelfgehoste IR instellen als proxy voor](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)meer informatie.   

   1. Selecteer **Doorgaan**. 

1. Bekijk **in** de sectie Overzicht alle inrichtingsinstellingen, bookmark de aanbevolen documentatiekoppelingen en selecteer **Voltooien** om de aanmaak van uw inburgeringsruntime te starten. 

   > [!NOTE]
   > Zonder aangepaste insteltijd moet dit proces binnen 5 minuten zijn voltooid.
   >
   > Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw databaseserver om SSISDB voor te bereiden. 
   > 
   > Wanneer u een Azure-SSIS IR indient, worden ook Access Redistributable en Azure Feature Pack voor SSIS geïnstalleerd. Deze componenten bieden connectiviteit met Excel-bestanden, Access-bestanden en verschillende Azure-gegevensbronnen, naast de gegevensbronnen die ingebouwde componenten al ondersteunen. Zie [Aangepaste installatie voor een Azure-SSIS IR voor](how-to-configure-azure-ssis-ir-custom-setup.md)informatie over andere onderdelen die u installeren.

1. Ga op het tabblad **Verbindingen** indien nodig naar **Integratieruntimes**. Selecteer **Vernieuwen** om de status te vernieuwen. 

   ![De status vernieuwen](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Gebruik de koppelingen in de kolom **Acties** om de integratieruntime te stoppen/starten, te bewerken of te verwijderen. Gebruik de laatste koppeling om JSON-code weer te geven voor de integratieruntime. De knoppen Bewerken en Verwijderen zijn alleen beschikbaar wanneer de IR is gestopt. 

   ![Koppelingen in de kolom 'Acties'](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS-pakketten implementeren

Als u SSISDB gebruikt, u uw pakketten erin implementeren en deze uitvoeren op de Azure-SSIS IR met SQL Server Data Tools (SSDT) of SQL Server Management Studio (SSMS)-hulpprogramma's. Deze hulpprogramma's maken verbinding met uw databaseserver via het servereindpunt: 

- Voor een Azure SQL Database-server is `<server name>.database.windows.net`de servereindpuntindeling .
- Voor een beheerde instantie met privéeindpunt is `<server name>.<dns prefix>.database.windows.net`de servereindpuntindeling .
- Voor een beheerde instantie met openbaar eindpunt `<server name>.public.<dns prefix>.database.windows.net,3342`is de servereindpuntindeling . 

Als u SSISDB niet gebruikt, u uw pakketten implementeren in bestandssystemen, bestandsshares of Azure-bestanden en `dtinstall` `dtutil`ze `dtexec` uitvoeren op azure-SSIS IR met behulp van de hulpprogramma's en opdrachtregelgereedschappen. Zie [SSIS-pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)voor meer informatie. 

In beide gevallen u uw geïmplementeerde pakketten ook uitvoeren op Azure-SSIS IR met behulp van de activiteit SSIS-pakket uitvoeren in Data Factory-pijplijnen. Zie [SSIS-pakketuitvoering inroepen als een eersteklas datafabriekactiviteit](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)voor meer informatie.

Zie ook de volgende SSIS-documentatie: 

- [SSIS-pakketten implementeren, uitvoeren en bewaken in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Verbinding maken met SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Pakketuitvoeringen plannen in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie) 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over uw Azure-SSIS-integratieruntime, gaat u verder met het volgende artikel: 

> [!div class="nextstepaction"]
> [Een Azure-SSIS IR aanpassen](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)