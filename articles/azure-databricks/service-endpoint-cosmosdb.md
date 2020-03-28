---
title: Zelfstudie - Azure Databricks implementeren met een Cosmos DB-eindpunt
description: In deze zelfstudie wordt beschreven hoe u Azure Databricks implementeert in een virtueel netwerk met een Service Endpoint ingeschakeld voor Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 4ac8c01e986cf1f3158c615a0791ba476e5bf1bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706168"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Zelfstudie: Azure Databricks implementeren met een Cosmos DB-eindpunt

In deze zelfstudie wordt beschreven hoe u een Door VNet geïnjecteerde Databricks-omgeving implementeert met een Service Endpoint ingeschakeld voor Cosmos DB.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Databricks-werkruimte maken in een virtueel netwerk
> * Een eindpunt van de Cosmos DB-service maken
> * Een Cosmos DB-account maken en gegevens importeren
> * Een Azure Databricks-cluster maken
> * Query Cosmos DB vanuit een Azure Databricks-notitieblok

## <a name="prerequisites"></a>Vereisten

Ga als volgt te werk voordat u begint:

* Maak een [Azure Databricks-werkruimte in een virtueel netwerk.](quickstart-create-databricks-workspace-vnet-injection.md)

* Download de [Spark-connector](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Download voorbeeldgegevens van de [NOAA National Centers for Environmental Information.](https://www.ncdc.noaa.gov/stormevents/) Selecteer een status of gebied en selecteer **Zoeken**. Accepteer op de volgende pagina de standaardinstellingen en selecteer **Zoeken**. Selecteer vervolgens **CSV Downloaden** aan de linkerkant van de pagina om de resultaten te downloaden.

* Download de [vooraf gecompileerde binaire](https://aka.ms/csdmtool) van de Azure Cosmos DB Data Migration Tool.

## <a name="create-a-cosmos-db-service-endpoint"></a>Een eindpunt van de Cosmos DB-service maken

1. Nadat u een Azure Databricks-werkruimte hebt geïmplementeerd in een virtueel netwerk, navigeert u naar het virtuele netwerk in de [Azure-portal.](https://portal.azure.com) Let op de openbare en private subnetten die zijn gemaakt via de databricks-implementatie.

   ![Virtuele netwerksubnetten](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Selecteer het subnet openbaar en maak een eindpunt van de Cosmos *DB-service.* Dan **opslaan**.
   
   ![Een eindpunt van de Cosmos DB-service toevoegen](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-account maken

1. Open Azure Portal. Selecteer linksboven in het scherm **de**optie Een bron > Databases maken > Azure Cosmos DB .

2. Vul de **instantiegegevens** op het tabblad **Basis met** de volgende instellingen in:

   |Instelling|Waarde|
   |-------|-----|
   |Abonnement|*uw abonnement*|
   |Resourcegroep|*uw resourcegroep*|
   |Accountnaam|db-vnet-service-eindpunt|
   |API|Core (SQL)|
   |Locatie|VS - west|
   |Georedundantie|Uitschakelen|
   |Schrijven voor meerdere regio's|Inschakelen|

   ![Een eindpunt van de Cosmos DB-service toevoegen](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Selecteer het tabblad **Netwerk** en configureer uw virtuele netwerk. 

   a. Kies het virtuele netwerk dat u als voorwaarde hebt gemaakt en selecteer vervolgens *openbaar subnet*. Merk op dat *private-subnet* heeft de opmerking *'Microsoft AzureCosmosDB' eindpunt ontbreekt'*. Dit komt omdat u alleen het eindpunt van de Cosmos *DB-service*hebt ingeschakeld op het openbare subnet .

   b. Zorg ervoor dat u **Toegang toestaan vanuit azure-portal** hebt ingeschakeld. Met deze instelling hebt u toegang tot uw Cosmos DB-account via de Azure-portal. Als deze optie is ingesteld op **Weigeren,** ontvangt u fouten wanneer u toegang probeert te krijgen tot uw account. 

   > [!NOTE]
   > Het is niet nodig voor deze tutorial, maar u ook *toegang toestaan vanaf mijn IP* inschakelen als u de mogelijkheid wilt om toegang te krijgen tot uw Cosmos DB-account vanaf uw lokale machine. Als u bijvoorbeeld verbinding maakt met uw account met de Cosmos DB SDK, moet u deze instelling inschakelen. Als het is uitgeschakeld, ontvangt u fouten 'Toegang geweigerd'.

   ![Netwerkinstellingen voor Cosmos DB-account](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Selecteer **Controleren + Maken**en maak **vervolgens** om uw Cosmos DB-account te maken binnen het virtuele netwerk.

5. Zodra uw Cosmos DB-account is gemaakt, navigeert u naar **Toetsen** onder **Instellingen**. Kopieer de primaire verbindingstekenreeks en sla deze op in een teksteditor voor later gebruik.

    ![Pagina Cosmos DB-accountsleutels](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Selecteer **Data Explorer** en Nieuwe **verzameling** om een nieuwe database en verzameling toe te voegen aan uw Cosmos DB-account.

    ![Cosmos DB nieuwe collectie](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Gegevens uploaden naar Cosmos DB

1. Open de grafische interfaceversie van het [hulpprogramma voor gegevensmigratie voor Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Cosmos DB-hulpprogramma voor gegevensmigratie](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Selecteer **csvbestand(en)** op het tabblad **Brongegevens** in de vervolgkeuzelijst **Importeren uit.** Selecteer vervolgens **Bestanden toevoegen** en voeg de stormgegevens toe die CSV als vereiste hebt gedownload.

    ![Broninformatie over gegevensmigratie van Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Voer op het tabblad **Doelgegevens** de verbindingstekenreeks in. De indeling van `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`de verbindingstekenreeks is . Het AccountEndpoint en AccountKey zijn opgenomen in de primaire verbindingstekenreeks die u in de vorige sectie hebt opgeslagen. Toevoegen `Database=<your database name>` aan het einde van de verbindingstekenreeks en selecteer **Verifiëren**. Voeg vervolgens de naam van de verzameling en de partitiesleutel toe.

    ![Cosmos DB Data Migration Tool doelinformatie](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Selecteer **Volgende** totdat u de overzichtspagina hebt. Selecteer vervolgens **Importeren**.

## <a name="create-a-cluster-and-add-library"></a>Een cluster maken en bibliotheek toevoegen

1. Navigeer naar uw Azure Databricks-service in de [Azure-portal](https://portal.azure.com) en selecteer **Werkruimte starten.**

   ![Werkruimte Databricks starten](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Een nieuw cluster maken. Kies een clusternaam en accepteer de resterende standaardinstellingen.

   ![Nieuwe clusterinstellingen](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Nadat uw cluster is gemaakt, navigeert u naar de clusterpagina en selecteert u het tabblad **Bibliotheken.** Selecteer **Nieuw installeren** en upload het spark-connectorpotbestand om de bibliotheek te installeren.

    ![Spark-connectorbibliotheek installeren](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    U controleren of de bibliotheek is geïnstalleerd op het tabblad **Bibliotheken.**

    ![Tabblad Gegevensstenenclusterbibliotheken](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Query Cosmos DB vanuit een Databricks-notitieblok

1. Navigeer naar uw Azure Databricks-werkruimte en maak een nieuw python-notitieblok.

    ![Nieuw Databricks-notitieblok maken](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Voer de volgende python-code uit om de configuratie van de Cosmos DB-verbinding in te stellen. Wijzig het **eindpunt,** **masterkey,** **database**en **verzameling** dienovereenkomstig.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Gebruik de volgende python-code om de gegevens te laden en een tijdelijke weergave te maken.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Gebruik de volgende magische opdracht om een SQL-instructie uit te voeren die gegevens retourneert.

    ```python
    %sql
    select * from storm
    ```

    U hebt uw Met VNet geïnjecteerde Databricks-werkruimte met succes verbonden met een Cosmos DB-bron met serviceeindpunt. Zie Azure Cosmos DB Connector voor [Apache Spark voor](https://github.com/Azure/azure-cosmosdb-spark)meer informatie over hoe u verbinding maken met Cosmos DB.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de brongroep, de Azure Databricks-werkruimte en alle gerelateerde resources wanneer dit niet meer nodig is. Het verwijderen van de taak voorkomt onnodige facturering. Als u de Azure Databricks-werkruimte in de toekomst wilt gebruiken, u het cluster stoppen en het later opnieuw starten. Als u deze Azure Databricks-werkruimte niet meer wilt gebruiken, verwijdert u alle resources die u in deze zelfstudie hebt gemaakt met de volgende stappen:

1. Klik in het linkermenu in de Azure-portal op **Resourcegroepen** en klik vervolgens op de naam van de resourcegroep die u hebt gemaakt.

2. Selecteer op de pagina Met uw brongroep de optie **Verwijderen**, typ de naam van de bron die u wilt verwijderen in het tekstvak en selecteer opnieuw **verwijderen.**

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Databricks-werkruimte geïmplementeerd in een virtueel netwerk en hebt u de Cosmos DB Spark-connector gebruikt om Cosmos DB-gegevens van Databricks op te vragen. Ga voor meer informatie over het werken met Azure Databricks in een virtueel netwerk verder naar de zelfstudie voor het gebruik van SQL Server met Azure Databricks.

> [!div class="nextstepaction"]
> [Zelfstudie: Een SQL Server Linux Docker-container in een virtueel netwerk opvragen vanuit een Azure Databricks-notitieblok](vnet-injection-sql-server.md)
