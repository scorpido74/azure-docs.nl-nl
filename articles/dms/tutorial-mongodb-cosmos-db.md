---
title: 'Zelfstudie: MongoDB offline migreren naar Azure Cosmos DB API voor MongoDB'
titleSuffix: Azure Database Migration Service
description: Leer offline migreren van MongoDB naar Azure Cosmos DB API voor MongoDB met Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 08fa94dbe71299a6653df0b40aa5083375526172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255579"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Zelfstudie: MongoDB migreren naar Azure Cosmos DB's API voor MongoDB offline met DMS

U Azure Database Migration Service gebruiken om een offline (eenmalige) migratie van databases uit te voeren van een on-premises of cloud-instantie van MongoDB naar de API van Azure Cosmos DB voor MongoDB.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.

In deze zelfstudie migreert u een gegevensset in MongoDB die wordt gehost in een Azure Virtual Machine naar de API van Azure Cosmos DB voor MongoDB met azure databasemigratieservice. Als u nog geen MongoDB-bron hebt ingesteld, raadpleegt u het artikel over het [installeren en configureren van MongoDB op een Windows-VM in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* [Voer de premigratiestappen uit,](../cosmos-db/mongodb-pre-migration.md) zoals het schatten van de doorvoer, het kiezen van een partitiesleutel en het indexeringsbeleid.
* [Maak een account voor Azure Cosmos DB's API voor MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.

    > [!NOTE]
    > Als u ExpressRoute gebruikt met netwerkpeering naar Microsoft, voegt u tijdens de installatie van het virtuele netwerk ExpressRoute met netwerkpeering aan Microsoft de volgende [serviceeindpunten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Eindpunt voor opslag
    > * Eindpunt van servicebus
    >
    > Deze configuratie is nodig omdat azure database migratieservice geen internetverbinding heeft.

* Zorg ervoor dat de regels van uw NSG-regels (Virtual Network Network Security Group) de volgende communicatiepoorten niet blokkeren: 53, 443, 445, 9354 en 10000-20000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van het virtuele netwerk.
* Open uw Windows-firewall zodat Azure Database Migration Service toegang heeft tot de bron-MongoDB-server, die standaard TCP-poort 27017 is.
* Wanneer u een firewalltoestel gebruikt voor uw brondatabase(s), moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Een instantie maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waar u de instantie van Azure Database Migration Service wilt maken. 

5. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot het bron-MongoDB-exemplaar en het doelAzure Cosmos DB-account.

    Zie het artikel Een virtueel netwerk maken met de [Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Services** naar de naam van de instantie Azure Database Migration Service die u hebt gemaakt en selecteer vervolgens de instantie.

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project in het tekstvak **bronservertype**, selecteer **MongoDB**, selecteer in het tekstvak **Type doelserver****CosmosDB (MongoDB-API)**, en selecteer bij **Type activiteit kiezen** de optie **Offline gegevensmigratie**. 

    ![Database Migration Service-project maken](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brondetails** de verbindingsgegevens op voor de MongoDB-bronserver.

   > [!IMPORTANT]
   > Azure Database Migration Service ondersteunt Azure Cosmos DB niet als bron.

    Er zijn drie modi om verbinding te maken met een bron:
   * **Standaardmodus**: deze accepteert een Fully Qualified Domain Name of een IP-adres, poortnummer en verbindingsreferenties.
   * **Modus verbindingsreeks**: deze accepteert een MongoDB-verbindingsreeks, zoals beschreven in het artikel [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (URI-indeling van verbindingsreeks).
   * **Gegevens uit Azure-opslag**: deze accepteert een SAS-URL van de blob-container. Selecteer **Blob contains BSON dump** als de blob-container BSON-dumps bevat die zijn geproduceerd door het [bsondump-hulpprogramma](https://docs.mongodb.com/manual/reference/program/bsondump/) van MongoDB en deselecteer het als de container JSON-bestanden bevat.

     Als u deze optie selecteert, controleer dan of de verbindingsreeks van het opslagaccount wordt weergegeven in de volgende indeling:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Deze SAS-verbindingstekenreeks voor blobcontainers is te vinden in Azure Storage Explorer. Als u de SAS voor de betrokken container maakt, krijgt u de URL in de bovenstaande gewenste indeling.
     
     Houd ook rekening met de volgende details op basis van de informatie over het type dumpin Azure Storage.

     * Voor BSON-dumps moeten de gegevens in de blob-container de bsondump-indeling hebben, zodat de gegevensbestanden worden geplaatst in mappen die worden genoemd naar de omvattende databases in de collection.bson-indeling. Metagegevensbestanden (indien aanwezig) moeten een naam krijgen op basis van de indeling *verzameling*.metadata.json.

     * Voor JSON-dumps moeten de bestanden in de blob-container worden geplaatst in mappen die zijn genoemd naar de omvattende databases. In elke databasemap moeten gegevensbestanden worden geplaatst in een submap met de naam 'data' en ze moeten een naam krijgen op basis van de indeling *verzameling*.json. Metagegevensbestanden (indien aanwezig) moeten worden geplaatst in een submap met de naam 'metadata' en ze moeten een naam krijgen op basis van dezelfde indeling *verzameling*.json. De metagegevensbestanden moeten de indeling hebben die wordt geproduceerd door het MongoDB-hulpprogramma bsondump.

    > [!IMPORTANT]
    > Het wordt afgeraden om een zelfondertekend certificaat te gebruiken op de mongo-server. Als er echter een wordt gebruikt, moet u verbinding maken met de server via de **verbindingstekenreeksmodus** en ervoor zorgen dat uw verbindingstekenreeks ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

   U kunt ook het IP-adres gebruiken voor situaties waarin DNS-naamomzetting niet mogelijk is.

   ![Geef brondetails op](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Details migratiedoel** de verbindingsgegevens op voor het Azure Cosmos DB-doelaccount, dat bestaat uit het vooraf ingerichte Azure Cosmos DB's API voor MongoDB-account waarnaar u de MongoDB-database migreert.

    ![Doeldetails opgeven](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Selecteer **Opslaan**.

## <a name="map-to-target-databases"></a>Toewijzen aan doeldatabases

1. Wijs in het scherm **Toewijzen aan doeldatabases** de bron- en doeldatabase voor de migratie toe.

    Als de doeldatabase dezelfde databasenaam bevat als de brondatabase, selecteert Azure Database Migration Service standaard de doeldatabase.

    Als de tekenreeks **Maken** naast de databasenaam wordt weergegeven, geeft dit aan dat azure databasemigratieservice de doeldatabase niet heeft gevonden en dat de service de database voor u maakt.

    Op dit moment in de migratie kunt u [doorvoer inrichten](https://docs.microsoft.com/azure/cosmos-db/set-throughput). In Cosmos DB kunt u doorvoer inrichten op het niveau van de database of afzonderlijk voor elke verzameling. Doorvoer wordt gemeten in [Aanvraageenheden](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU's). Meer informatie over de [prijzen van Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Toewijzen aan doeldatabases](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Selecteer **Opslaan**.
3. Vouw in het scherm **Verzamelingsinstelling** de lijst met verzamelingen uit en bekijk welke verzamelingen worden gemigreerd.

    Azure Database Migration Service selecteert automatisch alle verzamelingen die bestaan op het bron-MongoDB-exemplaar dat niet bestaat op het doelAzure Cosmos DB-account. Als u verzamelingen die al gegevens bevatten opnieuw wilt migreren, moet u die verzamelingen expliciet selecteren op deze blade.

    U kunt opgeven hoeveel RU's u voor de verzamelingen wilt gebruiken. Azure Database Migration Service stelt slimme standaardinstellingen voor op basis van de grootte van de verzameling.

    > [!NOTE]
    > Voer de databasemigratie en -verzameling parallel uit met meerdere exemplaren van Azure Database Migration Service, indien nodig, om de run te versnellen.

    U kunt ook een shardsleutel opgeven om te profiteren van [partitionering in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) voor optimale schaalbaarheid. Lees de [best practices voor het selecteren van een shard-/partitiesleutel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Verzamelingstabellen selecteren](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Selecteer **Opslaan**.

5. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

    ![Migratieoverzicht](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **status** van de activiteit is **Niet gestart**.

    ![Activiteitsstatus](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Bewaak de migratie

* Selecteer op het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

   > [!NOTE]
   > U kunt de activiteit selecteren voor meer informatie over metrische migratiegegevens op het niveau van een database of verzameling.

    ![Activiteitsstatus Voltooid](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Gegevens controleren in Cosmos DB

* Nadat de migratie is voltooid, kunt u in uw Azure Cosmos DB-account controleren of alle verzamelingen met succes zijn gemigreerd.

    ![Activiteitsstatus Voltooid](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimalisatie na migratie

Nadat u de gegevens die zijn opgeslagen in de MongoDB-database hebt gemigreerd naar de API van Azure Cosmos DB voor MongoDB, u verbinding maken met Azure Cosmos DB en de gegevens beheren. U ook andere optimalisatiestappen na migratie uitvoeren, zoals het optimaliseren van het indexeringsbeleid, het standaardconsistentieniveau bijwerken of globale distributie configureren voor uw Azure Cosmos DB-account. Zie het artikel [voor optimalisatie na migratie](../cosmos-db/mongodb-post-migration.md) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Informatie over Cosmos DB-service](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Volgende stappen

* Lees de Microsoft-[handleiding voor databasemigratie](https://datamigration.microsoft.com/) voor hulp bij andere migratiescenario's.
