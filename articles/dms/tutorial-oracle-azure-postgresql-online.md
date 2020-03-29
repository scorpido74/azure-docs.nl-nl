---
title: 'Zelfstudie: Oracle online migreren naar Azure Database voor PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Leer een online migratie uitvoeren van Oracle on-premises of op virtuele machines naar Azure Database voor PostgreSQL met behulp van Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 956523e2b51795a4bc97c653dab8b408b06061f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255570"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Zelfstudie: Oracle migreren naar Azure Database voor PostgreSQL online met DMS (Preview)

U Azure Database Migration Service gebruiken om de databases van Oracle-databases die on-premises of op virtuele machines worden gehost, te migreren naar [Azure Database voor PostgreSQL](https://docs.microsoft.com/azure/postgresql/) met minimale downtime. Met andere woorden, u de migratie voltooien met minimale downtime naar de toepassing. In deze zelfstudie **HR** migreert u de HR-voorbeelddatabase van een on-premises of virtuele machine-instantie van Oracle 11g naar Azure Database voor PostgreSQL met behulp van de online migratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Beoordeel de migratie-inspanning met behulp van de ora2pg tool.
> * Migreer het voorbeeldschema met het gereedschap ora2pg.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, moet u een instantie maken op basis van de prijscategorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt beschreven hoe u een onlinemigratie uitvoert van Oracle naar Azure Database voor PostgreSQL.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Oracle [11g Release 2 (Standard Edition, Standard Edition One of Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)downloaden en installeren.
* Download de voorbeeld **HR-database** [vanaf hier](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Download en [installeer ora2pg op Windows of Linux.](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Een exemplaar maken in Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Maak verbinding met de instantie en maak een database met behulp van de instructie in dit [document](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.

  > [!NOTE]
  > Als u ExpressRoute gebruikt met netwerkpeering naar Microsoft, voegt u tijdens de installatie van het virtuele netwerk ExpressRoute met netwerkpeering aan Microsoft de volgende [serviceeindpunten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
  >
  > * Eindpunt van doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
  > * Eindpunt voor opslag
  > * Eindpunt van servicebus
  >
  > Deze configuratie is nodig omdat azure database migratieservice geen internetverbinding heeft.

* Zorg ervoor dat de regels van uw NSG-regels (Virtual Network Network Security Group) de volgende binnenkomende communicatiepoorten naar Azure Database Migration Service niet blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)voor meer informatie over het filteren van het virtuele netwerk.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows-firewall zodat Azure Database Migration Service toegang heeft tot de bron Oracle-server, die standaard TCP-poort 1521 is.
* Wanneer u een firewalltoestel gebruikt voor uw brondatabase(s), moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie.
* Maak een firewallregel op [serverniveau](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor Azure Database voor PostgreSQL om Azure Database Migration Service toegang te geven tot de doeldatabases. Geef het subnetbereik op van het virtuele netwerk dat wordt gebruikt voor Azure Database Migration Service.
* Toegang tot de bronOracle-databases inschakelen.

  > [!NOTE]
  > De DBA-rol is vereist voor een gebruiker om verbinding te maken met de Oracle-bron.

  * ArchiefRedo-logboeken zijn vereist voor incrementele synchronisatie in Azure Database Migration Service om gegevenswijzigingen vast te leggen. Voer de volgende stappen uit om de Oracle-bron te configureren:
    * Meld u aan met de SYSDBA-bevoegdheid door de volgende opdracht uit te voeren:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Sluit de database-instantie af door de volgende opdracht uit te voeren.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Wacht op `'ORACLE instance shut down'`de bevestiging.

    * Start de nieuwe instantie en monteer (maar open niet) de database om de archiveringsbu in of uit te schakelen met de volgende opdracht:

      ```
      STARTUP MOUNT;
      ```

      De databank moet worden gemonteerd; wachten op bevestiging 'Oracle instance started'.

    * Wijzig de databasearchiveringsmodus door de volgende opdracht uit te voeren:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Open de database voor normale bewerkingen door de volgende opdracht uit te voeren:

      ```
      ALTER DATABASE OPEN;
      ```

      Mogelijk moet u het ARC-bestand opnieuw starten om te worden weergegeven.

    * Voer de volgende opdracht uit om deze te verifiëren:

      ```
      SELECT log_mode FROM v$database;
      ```

      U moet een `'ARCHIVELOG'`antwoord ontvangen. Als het `'NOARCHIVELOG'`antwoord is, dan is de eis niet voldaan.

  * Aanvullende logboekregistratie inschakelen voor replicatie met een van de volgende opties.

    * **Optie 1**.
      Wijzig de aanvullende logboekregistratie op databaseniveau om alle tabellen met PK en unieke index te dekken. De detectiequery `'IMPLICIT'`wordt teruggekeerd.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Wijzig de aanvullende logboekregistratie op tabelniveau. Voer alleen uit voor tabellen met gegevensmanipulatie en geen PK's of unieke indexen.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Optie 2**.
      Wijzig de aanvullende logboekregistratie op databaseniveau voor alle `'YES'`tabellen en de detectiequery retourneert .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Wijzig de aanvullende logboekregistratie op tabelniveau. Volg de onderstaande logica om slechts één instructie voor elke tabel uit te voeren.

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

    Voer de volgende opdracht uit om deze te verifiëren:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    U moet een `'YES'`antwoord ontvangen.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>De inspanning voor een Oracle naar Azure Database voor PostgreSQL-migratie beoordelen

We raden u aan ora2pg te gebruiken om de inspanning te beoordelen die nodig is om van Oracle naar Azure Database voor PostgreSQL te migreren. Gebruik `ora2pg -t SHOW_REPORT` de richtlijn om een rapport te maken met alle Oracle-objecten, de geschatte migratiekosten (in ontwikkelaarsdagen) en bepaalde databaseobjecten die mogelijk speciale aandacht vereisen als onderdeel van de conversie.

De meeste klanten zullen veel tijd besteden aan het beoordelen van het beoordelingsrapport en rekening houdend met de automatische en handmatige conversie-inspanning.

Zie de sectie **Premigratie: Beoordeling** van de [sectie Oracle naar Azure Database voor PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)om ora2pg te configureren en uit te voeren om een beoordelingsrapport te maken. Een voorbeeld ora2pg assessment report is [hier](https://ora2pg.darold.net/report.html)beschikbaar voor referentie .

## <a name="export-the-oracle-schema"></a>Het Oracle-schema exporteren

We raden u aan ora2pg te gebruiken om het Oracle-schema en andere Oracle-objecten (typen, procedures, functies, enz.) om te zetten in een schema dat compatibel is met Azure Database voor PostgreSQL. ora2pg bevat veel richtlijnen om u te helpen bepaalde gegevenstypen vooraf te definiëren. U de `DATA_TYPE` richtlijn bijvoorbeeld gebruiken om alle GETAL(*,0) te vervangen door bigint in plaats van NUMERIEKE waarde(38).

U ora2pg uitvoeren om elk van de databaseobjecten in .sql-bestanden te exporteren. U vervolgens de .sql-bestanden controleren voordat u ze importeert naar Azure Database voor PostgreSQL met behulp van psql of u de. SQL-script in PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Bijvoorbeeld:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Zie de sectie **Migratie: schema en gegevens** van de sectie Oracle naar Azure Database [voor PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)om ora2pg voor schemaconversie te configureren en uit te voeren.

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Het schema instellen in Azure Database voor PostgreSQL

U ervoor kiezen om Oracle-tabelschema's, opgeslagen procedures, pakketten en andere databaseobjecten om te converteren naar Postgres-compatibel maken met ora2pg voordat u een migratiepijplijn start in Azure Database Migration Service. Zie de onderstaande links voor hoe te werken met ora2pg:

* [Ora2pg installeren op Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle naar Azure PostgreSQL Migration Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service kan ook het PostgreSQL-tabelschema maken. De service heeft toegang tot het tabelschema in de verbonden Oracle-bron en maakt een compatibel tabelschema in Azure Database voor PostgreSQL. Zorg ervoor dat u de schema-indeling in Azure Database voor PostgreSQL valideert en controleert nadat Azure Database Migration Service is voltooid bij het maken van het schema en het verplaatsen van de gegevens.

> [!IMPORTANT]
> Azure Database Migration Service maakt alleen het tabelschema. andere databaseobjecten, zoals opgeslagen procedures, pakketten, indexen, enz., worden niet gemaakt.

Zorg er ook voor dat u de buitenlandse sleutel in de doeldatabase laat vallen voor de volledige belasting die moet worden uitgevoerd. Raadpleeg [hier](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) de sectie **Het voorbeeldschema** van het artikel migreren voor een script dat u gebruiken om de buitenlandse sleutel te laten vallen. Gebruik Azure Database Migration Service om uit te voeren voor volledige belasting en synchronisatie.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Wanneer het PostgreSQL-tabelschema al bestaat

Als u een PostgreSQL-schema maakt met behulp van hulpprogramma's zoals ora2pg voordat u de gegevensbeweging start met Azure Database Migration Service, stelt u de brontabellen toe aan de doeltabellen in Azure Database Migration Service.

1. Wanneer u een nieuw Oracle naar Azure Database maakt voor het PostgreSQL-migratieproject, wordt u gevraagd om doeldatabase- en doelschema's te selecteren in de stap Schema's selecteren. Vul de doeldatabase en het doelschema in.

   ![Portal-abonnementen weergeven](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. In het scherm **Migratieinstellingen** wordt een lijst met tabellen weergegeven in de Oracle-bron. Azure Database Migration Service probeert tabellen in de bron en de doeltabellen aan te passen op basis van de tabelnaam. Als er meerdere overeenkomende doeltabellen met verschillende behuizingen bestaan, u selecteren aan welke doeltabel u wilt toewijzen.

    ![Portal-abonnementen weergeven](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Als u brontabelnamen moet toewijzen aan tabellen [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) met verschillende namen, e-mail en kunnen we een script bieden om het proces te automatiseren.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Wanneer het PostgreSQL-tabelschema niet bestaat

Als de doelPostgreSQL-database geen tabelschemagegevens bevat, converteert Azure Database Migration Service het bronschema en maakt deze opnieuw in de doeldatabase. Houd er rekening mee dat Azure Database Migration Service alleen het tabelschema maakt, niet andere databaseobjecten zoals opgeslagen procedures, pakketten en indexen.
Als u azure databasemigratieservice het schema voor u wilt laten maken, moet u ervoor zorgen dat uw doelomgeving een schema bevat zonder bestaande tabellen. Als Azure Database Migration Service een tabel detecteert, gaat de service ervan uit dat het schema is gemaakt door een extern hulpprogramma zoals ora2pg.

> [!IMPORTANT]
> Azure Database Migration Service vereist dat alle tabellen op dezelfde manier worden gemaakt, met behulp van Azure Database Migration Service of een tool zoals ora2pg, maar niet beide.

Aan de slag:

1. Maak een schema in de doeldatabase op basis van uw toepassingsvereisten. Standaard worden postgreSQL-tabelschema's en kolommennamen in kleine letters verwijderd. Oracle tabel schema en kolommen, aan de andere kant, zijn standaard in alle hoofdletters.
2. Geef in de stap Schema's selecteren de doeldatabase en het doelschema op.
3. Op basis van het schema dat u maakt in Azure Database voor PostgreSQL, gebruikt Azure Database Migration Service de volgende transformatieregels:

    Als de schemanaam in de Oracle-bron overeenkomt met die in Azure Database voor PostgreSQL, maakt Azure Database Migration Service *het tabelschema met dezelfde case als in het doel*.

    Bijvoorbeeld:

    | Bron Oracle-schema | Doel PostgreSQL Database.Schema | DMS heeft schema.table.column gemaakt |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." LANDEN"." COUNTRY_ID" |
    | HR | targetHR.HR | "HR"." LANDEN"." COUNTRY_ID" |
    | HR | targetHR.Hr | *Kan gemengde aanvragen niet in kaart brengen |

    *Neem contact op [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com)met contact met contact wilt maken met het schema en tabelnamen voor gemengde letters in doelPostgreSQL. We kunnen een script bieden voor het instellen van een tabelschema voor gemengde letters in de doelPostgreSQL-database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Een DMS-exemplaar maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron Oracle en de doelAzure Database voor PostgreSQL-instantie.

    Zie het artikel Een virtueel netwerk maken met de [Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

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
4. Geef in het scherm **Nieuw migratieproject** een naam voor het project op, selecteer in het tekstvak **Bronservertype** **Oracle**, selecteer in het tekstvak **Doelservertype** De optie **Azure Database voor PostgreSQL**.
5. Selecteer online **gegevensmigratie**in de sectie **Type activiteit kiezen** .

   ![Azure Database Migration Service-project maken](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > U afwisselend **project maken kiezen** om nu het migratieproject te maken en de migratie later uit te voeren.

6. Selecteer **Opslaan,** noteer de vereisten om Azure Database Migration Service te gebruiken om een online migratie uit te voeren en selecteer **vervolgens Activiteit maken en uitvoeren**.

## <a name="specify-source-details"></a>Geef brondetails op

* Geef in het scherm **Brondetails toevoegen** de verbindingsgegevens op voor de bron-Oracle-instantie.

  ![Scherm Brondetails toevoegen](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Oracle OCI-stuurprogramma uploaden

1. Selecteer **Opslaan**en vervolgens op het **scherm OCI-stuurprogramma installeren,** meld u aan bij uw Oracle-account en download de driver **instantclient-basiclite-windows.x64-12.2.0.0.0.zip** (37.128.586 Byte(s)) (SHA1 Checksum: 865082268) vanaf [hier](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Download het stuurprogramma naar een gedeelde map.

   Zorg ervoor dat de map wordt gedeeld met de gebruikersnaam die u hebt opgegeven met minimale alleen-lezen toegang. Azure Database Migration Service opent en leest uit het aandeel om het OCI-stuurprogramma naar Azure te uploaden door zich voor te doen als de gebruikersnaam die u opgeeft.

   De opgegeven gebruikersnaam moet een Windows-gebruikersaccount zijn.

   ![OCI Driver Installeren](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan**en geef vervolgens op het scherm **Doeldetails** de verbindingsgegevens op voor de doelazure-database voor PostgreSQL-server, het vooraf ingerichte exemplaar van Azure Database voor PostgreSQL waaraan het **HR-schema** is geïmplementeerd.

    ![Scherm Doeldetails](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase dezelfde databasenaam bevat als de brondatabase, selecteert Azure Database Migration Service standaard de doeldatabase.

    ![Toewijzen aan doeldatabases](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

  Het venster migratieactiviteit wordt weergegeven en de **status** van de activiteit **initialiseert**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

     ![Activiteitsstatus - actief](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Selecteer **onder Databasenaam**een specifieke database om de migratiestatus voor **Volledige gegevensbelasting** en **Incrementele gegevenssynchronisatiebewerkingen** te openen.

    ‘Alle gegevens worden geladen’ toont de migratiestatus van de eerste lading terwijl ‘Incrementele gegevenssynchronisatie’ de CDC-status (Change Data Capture) toont.

     ![Activiteitenstatus: Volledig geladen](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Activiteitenstatus: Incrementele gegevenssynchronisatie](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.

   ![Cutover starten](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de status van de databasemigratie **Voltooid**weergeeft, verbindt u uw toepassingen met de nieuwe doelstelling Azure Database voor PostgreSQL-instantie.

 > [!NOTE]
 > Aangezien PostgreSQL standaard schema.table.column in kleine letters heeft, u eerder in dit artikel terugkeren van hoofdletters naar kleine letters door het script in de **sectie Schema in Azure Database voor PostgreSQL in** te stellen.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for PostgreSQL.
* Zie het artikel Wat is Azure [Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Zie het artikel [Wat is Azure Database voor PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
