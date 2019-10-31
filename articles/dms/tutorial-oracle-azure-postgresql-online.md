---
title: 'Zelf studie: Azure Database Migration Service gebruiken voor het uitvoeren van een online migratie van Oracle naar Azure Database for PostgreSQL | Microsoft Docs'
description: Meer informatie over het uitvoeren van een online migratie van Oracle on-premises of op virtuele machines naar Azure Database for PostgreSQL met behulp van Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 1ac5e4dd28f7565f546c700a4bbb0076fd793bb7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163424"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Zelf studie: Oracle migreren naar Azure Database for PostgreSQL online met behulp van DMS (preview)

U kunt Azure Database Migration Service gebruiken om de data bases van Oracle-data bases die on-premises of op virtuele machines worden gehost, te migreren naar [Azure database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) met minimale downtime. Met andere woorden, u kunt de migratie volt ooien met minimale downtime voor de toepassing. In deze zelf studie migreert u de **HR** -voorbeeld database van een on-premises-of virtuele-machine-instantie van Oracle 11g naar Azure database for PostgreSQL met behulp van de online migratie activiteit in azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Evalueer de migratie inspanningen met het hulp programma ora2pg.
> * Migreer het voorbeeld schema met het ora2pg-hulp programma.
> * Maak een instantie van Azure Database Migration Service.
> * Een migratie project maken met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.

> [!NOTE]
> Als u Azure Database Migration Service voor het uitvoeren van een online migratie wilt gebruiken, moet u een instantie maken op basis van de prijs categorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt micro soft aan om een instantie van Azure Database Migration Service te maken in dezelfde Azure-regio als de doel database. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt beschreven hoe u een online migratie uitvoert van Oracle naar Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Down load en Installeer [Oracle 11G release 2 (Standard Edition, Standard Edition One of ENTER prise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Down load [hier](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)de voor beeld- **HR** -data base.
* Down load en installeer ora2pg op [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) of [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Een exemplaar maken in Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Maak verbinding met het exemplaar en maak een Data Base met behulp van de instructie in dit [document](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Een Azure Virtual Network (VNet) maken voor Azure Database Migration Service met behulp van het Azure Resource Manager implementatie model, dat site-naar-site-connectiviteit met uw on-premises bron servers biedt met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Voor meer informatie over het maken van een VNet raadpleegt u de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)en met name de Quick Start-artikelen met stapsgewijze Details.

  > [!NOTE]
  > Als u tijdens de VNet-installatie gebruikmaakt van ExpressRoute met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
  > * Eind punt van de doel database (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt, enzovoort)
  > * Opslag eindpunt
  > * Service Bus-eind punt
  >
  > Deze configuratie is nood zakelijk omdat Azure Database Migration Service geen verbinding met internet heeft.

* Zorg ervoor dat de regels van uw VNet-netwerk beveiligings groep (NSG) niet de volgende binnenkomende communicatie poorten blok keren naar Azure Database Migration Service: 443, 53, 9354, 445, 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)voor meer informatie over het filteren van NSG-verkeer van Azure VNet.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron-Oracle-Server, die standaard TCP-poort 1521 is.
* Wanneer u een firewall apparaat voor uw bron database (s) gebruikt, moet u mogelijk firewall regels toevoegen om Azure Database Migration Service toegang te geven tot de bron database (s) voor de migratie.
* Maak een [firewall regel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) op server niveau voor Azure Database for PostgreSQL om Azure database Migration service toegang tot de doel databases toe te staan. Geef het subnet-bereik van het VNet op dat wordt gebruikt voor Azure Database Migration Service.
* Toegang tot de bron-Oracle-data bases inschakelen.

  > [!NOTE]
  > De rol van de DBA is vereist voor een gebruiker om verbinding te maken met de Oracle-bron.

  * Logboeken voor opnieuw uitvoeren is vereist voor incrementele synchronisatie in Azure Database Migration Service voor het vastleggen van gegevens wijziging. Volg deze stappen om de Oracle-bron te configureren:
    * Meld u aan met de SYSDBA-bevoegdheid door de volgende opdracht uit te voeren:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Sluit het data base-exemplaar af door de volgende opdracht uit te voeren.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Wacht tot het bevestigings `'ORACLE instance shut down'`.

    * Start het nieuwe exemplaar en koppel de data base (maar niet open) om de archiveringsinvoegtoepassing in of uit te scha kelen met de volgende opdracht:

      ```
      STARTUP MOUNT;
      ```

      De data base moet zijn gekoppeld. wacht op de bevestiging ' Oracle-exemplaar gestart '.

    * Wijzig de modus voor het archiveren van de data base door de volgende opdracht uit te voeren:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Open de Data Base voor normale bewerkingen door de volgende opdracht uit te voeren:

      ```
      ALTER DATABASE OPEN;
      ```

      Mogelijk moet u opnieuw opstarten om het ARC-bestand weer te geven.

    * Voer de volgende opdracht uit om te controleren:

      ```
      SELECT log_mode FROM v$database;
      ```

      U ontvangt een antwoord `'ARCHIVELOG'`. Als het antwoord `'NOARCHIVELOG'`is, wordt niet voldaan aan de vereiste.

  * Schakel aanvullende logboek registratie in voor replicatie met een van de volgende opties.

    * **Optie 1**.
      Wijzig de aanvullende logboek registratie op database niveau voor alle tabellen met PK en unieke index. De detectie query retourneert `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      De aanvullende logboek registratie voor tabel niveau wijzigen. Alleen uitvoeren voor tabellen die gegevens bewerken en geen PKs of unieke indexen hebben.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Optie 2**.
      Wijzig de aanvullende logboek registratie op database niveau voor alle tabellen en retourneert de detectie query `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      De aanvullende logboek registratie voor tabel niveau wijzigen. Volg de onderstaande logica om slechts één instructie voor elke tabel uit te voeren.

      Als de tabel een primaire sleutel heeft:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Als de tabel een unieke index heeft:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Voer anders de volgende opdracht uit:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Voer de volgende opdracht uit om te controleren:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    U ontvangt een antwoord `'YES'`.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>De inspanningen evalueren voor een Oracle-Azure Database for PostgreSQL migratie

We raden u aan ora2pg te gebruiken om de inspanningen te evalueren die nodig zijn om van Oracle naar Azure Database for PostgreSQL te migreren. Gebruik de `ora2pg -t SHOW_REPORT` instructie voor het maken van een rapport met alle Oracle-objecten, de geschatte migratie kosten (in ontwikkel aars dagen) en bepaalde database objecten die mogelijk speciale aandacht vereisen als onderdeel van de conversie.

De meeste klanten best Eden veel tijd aan het beoordelen van het beoordelings rapport en het overwegen van de automatische en hand matige conversie.

Als u ora2pg wilt configureren en uitvoeren voor het maken van een evaluatie rapport, raadpleegt u de sectie **premigratie: Assessment** van de [Oracle to Azure database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Er is een voor beeld van een ora2pg-evaluatie rapport beschikbaar [voor naslag informatie](http://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Het Oracle-schema exporteren

We raden u aan ora2pg te gebruiken om het Oracle-schema en andere Oracle-objecten (types, procedures, functions, enzovoort) te converteren naar een schema dat compatibel is met Azure Database for PostgreSQL. ora2pg bevat veel instructies om u te helpen bij het vooraf definiëren van bepaalde gegevens typen. U kunt bijvoorbeeld de `DATA_TYPE`-instructie gebruiken om alle getallen (*, 0) te vervangen door bigint in plaats van NUMERIEK (38).

U kunt ora2pg uitvoeren om alle database objecten in. SQL-bestanden te exporteren. U kunt vervolgens de. SQL-bestanden controleren voordat u ze importeert in Azure Database for PostgreSQL met behulp van psql of u kunt de uitvoeren. SQL-script in PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Bijvoorbeeld:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Zie de sectie **migratie: schema en gegevens** van de [Oracle to Azure database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)om ora2pg voor schema conversie te configureren en uit te voeren.

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Het schema instellen in Azure Database for PostgreSQL

U kunt ervoor kiezen om Oracle-tabel schema's, opgeslagen procedures, pakketten en andere database objecten te converteren om ze post gres compatibel te maken met behulp van ora2pg voordat u een migratie pijplijn in Azure Database Migration Service start. Zie de onderstaande koppelingen voor meer informatie over het werken met ora2pg:

* [Ora2pg installeren in Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf)
* [Migratie Cookbook van Oracle naar Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service kunt ook het PostgreSQL-tabel schema maken. De service opent het tabel schema in de verbonden Oracle-bron en maakt een compatibel tabel schema in Azure Database for PostgreSQL. Zorg ervoor dat u de schema-indeling valideert en controleert in Azure Database for PostgreSQL nadat Azure Database Migration Service klaar bent met het maken van het schema en het verplaatsen van de gegevens.

> [!IMPORTANT]
> Azure Database Migration Service maakt alleen het tabel schema. andere database objecten, zoals opgeslagen procedures, pakketten, indexen, etc., worden niet gemaakt.

Zorg er ook voor dat u de refererende sleutel in de doel database verwijdert om de volledige belasting uit te voeren. Raadpleeg de sectie **het voorbeeld schema migreren** [van het artikel voor een](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) script dat u kunt gebruiken om de refererende sleutel te verwijderen. Gebruik Azure Database Migration Service voor het uitvoeren van volledige belasting en synchronisatie.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Wanneer het PostgreSQL-tabel schema al bestaat

Als u een PostgreSQL-schema maakt met behulp van hulpprogram ma's zoals ora2pg voordat u begint met het verplaatsen van gegevens met Azure Database Migration Service, kunt u de bron tabellen toewijzen aan de doel tabellen in Azure Database Migration Service.

1. Wanneer u een nieuwe Oracle-Azure Database for PostgreSQL maakt voor het migratie project, wordt u gevraagd om de doel database en het doel schema te selecteren in de stap schema's selecteren. Vul de doel database en het doel schema in.

   ![Portal-abonnementen weergeven](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Het scherm **migratie-instellingen** bevat een lijst met tabellen in de Oracle-bron. Azure Database Migration Service probeert tabellen in de bron-en doel tabellen te vergelijken op basis van de tabel naam. Als er meerdere overeenkomende doel tabellen met een ander hoofdletter gebruik bestaan, kunt u selecteren welke doel tabel moet worden toegewezen.

    ![Portal-abonnementen weergeven](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Als u bron tabel namen wilt toewijzen aan tabellen met verschillende namen, e-mail [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) en wij een script kunnen opgeven om het proces te automatiseren.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Wanneer het PostgreSQL-tabel schema niet bestaat

Als de doel-PostgreSQL-data base geen tabel schema gegevens bevat, wordt Azure Database Migration Service het bron schema geconverteerd en opnieuw gemaakt in de doel database. Houd er rekening mee dat Azure Database Migration Service alleen het tabel schema maakt, niet andere database objecten, zoals opgeslagen procedures, pakketten en indexen.
Als u Azure Database Migration Service het schema voor u wilt maken, moet u ervoor zorgen dat uw doel omgeving een schema bevat zonder bestaande tabellen. Als Azure Database Migration Service een tabel detecteert, gaat de service ervan uit dat het schema is gemaakt door een extern hulp programma zoals ora2pg.

> [!IMPORTANT]
> Azure Database Migration Service vereist dat alle tabellen op dezelfde manier worden gemaakt met behulp van Azure Database Migration Service of een hulp programma, zoals ora2pg, maar niet beide.

Aan de slag gaan:

1. Maak een schema in de doel database op basis van uw toepassings vereisten. PostgreSQL-tabel schema en kolommen namen zijn standaard kleine letters. Oracle-tabel schema en-kolommen zijn daarentegen standaard in hoofd letters.
2. In de stap schema's selecteren geeft u de doel database en het doel schema op.
3. Op basis van het schema dat u in Azure Database for PostgreSQL maakt, Azure Database Migration Service de volgende transformatie regels gebruikt:

    Als de schema naam in de Oracle-bron en overeenkomt met die in Azure Database for PostgreSQL, dan *maakt Azure database Migration service het tabel schema met behulp van dezelfde case als in het doel*.

    Bijvoorbeeld:

    | Oracle-bron schema | Doel PostgreSQL-data base. schema | Het DMS-schema is gemaakt. tabel. kolom |
    | ------------- | ------------- | ------------- |
    | HR | targetHR. public | openbaar. landen. country_id |
    | HR | targetHR.trgthr | trgthr. landen. country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." LANDEN "." COUNTRY_ID" |
    | HR | targetHR.HR | ' HR '. LANDEN "." COUNTRY_ID" |
    | HR | targetHR.Hr | \* Kan geen gemengde cases toewijzen |

    \* Als u een schema voor gemengde cases en tabel namen wilt maken in doel PostgreSQL, neemt u contact op met [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com). We kunnen een script opgeven voor het instellen van een schema voor het maken van een gemengde Case-tabel in de doel-PostgreSQL-data base.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Een DMS-exemplaar maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service** **Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer een bestaand VNet of maak een nieuw account.

    Het VNet biedt Azure Database Migration Service toegang tot de bron-Oracle en het doel Azure Database for PostgreSQL exemplaar.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een VNet in de Azure Portal.

5. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt en selecteer vervolgens het exemplaar.

    ![Zoek uw exemplaar van de Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Selecteer + **Nieuw migratieproject**.
4. Geef in het scherm **Nieuw migratie project** een naam op voor het project, Selecteer in het tekstvak **type bron server** de optie **Oracle**, Selecteer in het tekstvak **doel server type** de optie **Azure database for PostgreSQL**.
5. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**.

   ![Azure Database Migration Service-project maken](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > U kunt ook **project maken** selecteren om het migratie project nu te maken en de migratie later uitvoeren.

6. Selecteer **Opslaan**, houd rekening met de vereisten voor het gebruik van Azure database Migration service om een online migratie uit te voeren en selecteer vervolgens **activiteit maken en uitvoeren**.

## <a name="specify-source-details"></a>Geef brondetails op

* Geef in het scherm **bron Details toevoegen** de verbindings gegevens voor de Oracle-bron instantie op.

  ![Scherm Brondetails toevoegen](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Oracle OCI-stuur programma uploaden

1. Selecteer **Opslaan**en meld u vervolgens aan bij uw Oracle-account in het scherm voor het installeren van een **OCI-stuur programma** en down load het stuur programma **instantclient-basiclite-Windows. x64-12.2.0.1.0. zip** (37.128.586 byte (s)) (sha1-controlesom: 865082268) [ ](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Down load het stuur programma naar een gedeelde map.

   Zorg ervoor dat de map wordt gedeeld met de gebruikers naam die u hebt opgegeven met minimale alleen-lezen toegang. Azure Database Migration Service toegang tot en lees bewerkingen van de share om het OCI-stuur programma naar Azure te uploaden door de gebruikers naam die u opgeeft, te imiteren.

   De gebruikers naam die u opgeeft, moet een Windows-gebruikers account zijn.

   ![OCI-stuur programma installeren](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan**en geef vervolgens in het scherm **doel Details** de verbindings gegevens voor de doel Azure database for postgresql server op. Dit is het vooraf ingerichte exemplaar van Azure database for PostgreSQL waarnaar het **HR** -schema is geïmplementeerd.

    ![Scherm Doeldetails](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doel database dezelfde database naam als de bron database bevat Azure Database Migration Service de doel database standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

  Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

     ![Activiteitsstatus-uitvoering](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Selecteer onder **database naam**een specifieke data base om de migratie status voor **volledige gegevens belasting** en **incrementele gegevens synchronisatie** te verkrijgen.

    ‘Alle gegevens worden geladen’ toont de migratiestatus van de eerste lading terwijl ‘Incrementele gegevenssynchronisatie’ de CDC-status (Change Data Capture) toont.

     ![Activiteitenstatus: Volledig geladen](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Activiteitenstatus: Incrementele gegevenssynchronisatie](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.

   ![Cutover starten](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de status van de database migratie **is voltooid**, verbindt u uw toepassingen met het nieuwe doel Azure database for PostgreSQL exemplaar.

 > [!NOTE]
 > Omdat PostgreSQL standaard is ingesteld op schema. table. column in kleine letters, kunt u de hoofd letters in kleine letters herstellen met behulp van het script in de sectie **het schema instellen in azure database for PostgreSQL** eerder in dit artikel.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for PostgreSQL.
* Zie het artikel [Wat is er Azure database Migration service?](https://docs.microsoft.com/azure/dms/dms-overview)voor informatie over de Azure database Migration service.
* Zie het artikel [Wat is er Azure database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview)voor informatie over Azure database for PostgreSQL.
