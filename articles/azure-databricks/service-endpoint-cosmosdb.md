---
title: Zelfstudie - Azure Databricks implementeren met een Cosmos DB-eindpunt
description: In deze zelfstudie wordt beschreven hoe u Azure Databricks implementeert in een virtueel netwerk met een service-eindpunt dat is ingeschakeld voor Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: e18c2b0f03f9ac2155c441580d62d6085581de12
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779600"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Zelfstudie: Azure Databricks implementeren met een Cosmos DB-eindpunt

Deze zelfstudie beschrijft hoe u een in VNet opgenomen Databricks-omgeving implementeert met een service-eindpunt ingeschakeld voor Cosmos DB.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Databricks-werkruimte maken in een virtueel netwerk
> * Een Cosmos DB-service-eindpunt maken
> * Cosmos DB-account maken en gegevens importeren
> * Een Azure Databricks-cluster maken
> * Cosmos DB opvragen van een Azure Databricks-notebook

## <a name="prerequisites"></a>Vereisten

Doe voordat u begint het volgende:

* Een [Azure Databricks-werkruimte maken in een virtueel netwerk](quickstart-create-databricks-workspace-vnet-injection.md).

* De [Spark-connector](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar) downloaden.

* Download voorbeeldgegevens van de [NOAA National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/). Selecteer een staat of gebied en selecteer **Zoeken**. Accepteer de standaardinstellingen op de volgende pagina en selecteer **Zoeken**. Selecteer vervolgens **CSV downloaden** aan de linkerkant van de pagina om de resultaten te downloaden.

* Download de [vooraf gecompileerde binaire code](https://aka.ms/csdmtool) van het Azure Cosmos DB-hulpprogramma voor gegevensmigratie.

## <a name="create-a-cosmos-db-service-endpoint"></a>Een Cosmos DB-service-eindpunt maken

1. Nadat u een Azure Databricks-werkruimte naar een virtueel netwerk hebt geïmplementeerd, gaat u naar het virtuele netwerk in de [Azure-portal](https://portal.azure.com). Let op de openbare en particuliere subnetten die zijn gemaakt via de Databricks-implementatie.

   ![Virtueel netwerk-subnetten](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Selecteer het *openbare subnet* en maak een Cosmos DB-service-eindpunt. Kies dan **Opslaan**.
   
   ![Een Cosmos DB-service-eindpunt toevoegen](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-account maken

1. Open Azure Portal. Selecteer linksboven in het scherm **Een resource maken > Databases > Azure Cosmos DB**.

2. Vul de **Details van het exemplaar** in op het tabblad **Basisinstellingen** met de volgende instellingen:

   |Instelling|Waarde|
   |-------|-----|
   |Abonnement|*uw abonnement*|
   |Resourcegroep|*uw resourcegroep*|
   |Accountnaam|db-vnet-service-endpoint|
   |API|Core (SQL)|
   |Locatie|VS - west|
   |Geografisch redundantie|Uitschakelen|
   |Schrijfbewerkingen in meerdere regio's|Inschakelen|

   ![Een Cosmos DB-service-eindpunt toevoegen](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Selecteer het tabblad **Netwerk** en configureer het virtuele netwerk. 

   a. Kies het virtuele netwerk dat u hebt gemaakt als een vereiste en selecteer vervolgens *Openbaar subnet*. U ziet dat *persoonlijk subnet* de notitie *‘Microsoft AzureCosmosDB’-eindpunt ontbreekt* heeft. Dit komt doordat u het Cosmos DB Service-eindpunt alleen hebt ingeschakeld op het *Openbare subnet*.

   b. Zorg dat **Toegang vanuit Azure-portal toestaan** is ingeschakeld. Deze instelling geeft u toegang tot uw Cosmos DB-account kunt krijgen via de Azure-portal. Als deze optie is ingesteld op **Weigeren**, worden er fouten weergegeven wanneer u probeert toegang te krijgen tot uw account. 

   > [!NOTE]
   > Het is niet nodig voor deze zelfstudie, maar u kunt ook *Toegang vanaf mijn IP-adres toestaan* inschakelen als u toegang tot uw Cosmos DB-account vanaf uw lokale computer wilt. Als u bijvoorbeeld verbinding maakt met uw account met behulp van de SDK van Cosmos DB, moet u deze instelling inschakelen. Als deze is uitgeschakeld, ontvangt u het foutbericht "Toegang geweigerd".

   ![Netwerkinstellingen van Cosmos DB-account](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Selecteer **Beoordelen en maken** en vervolgens **Maken** om uw Cosmos DB-account in het virtuele netwerk te maken.

5. Nadat uw Cosmos DB-account is gemaakt, gaat u naar **Sleutels** onder **Instellingen**. Kopieer de primaire verbindingsreeks en sla deze op in een teksteditor voor later gebruik.

    ![Sleutelpagina van Cosmos DB-account](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Selecteer **Data Explorer** en **Nieuwe verzameling** om een nieuwe database en verzameling toe te voegen aan uw Cosmos DB-account.

    ![Nieuwe verzameling van Cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Gegevens uploaden naar Cosmos DB

1. Open de grafische interface-versie van het [hulpprogramma voor gegevensmigratie voor Cosmos DB](https://aka.ms/csdmtool), **Dtui. exe-** .

    ![Cosmos DB-hulpprogramma voor gegevensmigratie](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Selecteer op het tabblad **Broninformatie** **CSV-bestand(en)** in de vervolgkeuzelijst **Importeren vanuit**. Selecteer vervolgens **Bestanden toevoegen** en voeg het CSV met stormgegevens toe die u hebt gedownload als een vereiste.

    ![Cosmos DB-hulpprogramma voor migratie van broninformatie](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Voer uw verbindingsreeks in op het tabblad **Doelinformatie**. De indeling van de verbindingsreeks is `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. De AccountEndpoint en AccountKey zijn opgenomen in de primaire verbindingsreeks die u in de vorige sectie hebt opgeslagen. Voeg `Database=<your database name>` toe aan het einde van de verbindingsreeks en selecteer **Verifiëren**. Voeg vervolgens de naam van de Verzameling en de partitiesleutel toe.

    ![Cosmos DB-hulpprogramma voor migratie van doelinformatie](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Selecteer **Volgende** totdat de pagina Samenvatting wordt weergegeven. Selecteer vervolgens **Importeren**.

## <a name="create-a-cluster-and-add-library"></a>Een cluster maken en bibliotheek toevoegen

1. Navigeer naar uw Azure Databricks-service in de [Azure-portal](https://portal.azure.com) en selecteer **Werkruimte starten**.

2. Een nieuw cluster maken. Kies een clusternaam en accepteer de overige standaardinstellingen.

   ![Nieuwe clusterinstellingen](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Nadat het cluster is gemaakt, gaat u naar de clusterpagina en selecteert u het tabblad **Bibliotheken**. Selecteer **Nieuwe installeren** en upload het JAR-bestand Spark-connector om de bibliotheek te installeren.

    ![Spark-connectorbibliotheek installeren](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    U kunt controleren of de bibliotheek is geïnstalleerd op het tabblad **Bibliotheken**.

    ![Tabblad Bibliotheken in een Databricks-cluster](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Cosmos DB opvragen van een Databricks-notebook

1. Navigeer naar uw Azure Databricks-werkruimte en maak een nieuw Python-notebook.

    ![Nieuwe Databricks-notebook maken](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Voer de volgende Python-code uit om de configuratie van de Cosmos DB-verbinding in te stellen. Wijzig het **eindpunt**, de **hoofdsleutel**, **Database**en **Verzameling** dienovereenkomstig.

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

3. Gebruik de volgende Python-code om de gegevens te laden en een tijdelijke weergave te maken.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Gebruik de volgende Magic-opdracht om een SQL-instructie uit te voeren waarmee gegevens worden geretourneerd.

    ```python
    %sql
    select * from storm
    ```

    U hebt de door VNet opgenomen Databricks-werkruimte gekoppeld aan een service-eindpunt waarvoor Cosmos DB-resource is ingeschakeld. Voor meer informatie over het maken van verbinding met Cosmos DB raadpleegt u [Azure Cosmos DB-connector voor Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de Azure Databricks-werkruimte en alle gerelateerde resources. Als u de taak verwijdert, vermijdt u onnodige facturering. Als u van plan bent de Azure Databricks-werkruimte in de toekomst te gebruiken, kunt u het cluster stoppen en later opnieuw opstarten. Als u deze Azure Databricks-werkruimte niet meer gaat gebruiken, verwijdert u alle resources die u in deze zelfstudie hebt gemaakt. Daarvoor voert u de volgende stappen uit:

1. Klik in het menu aan de linkerkant in de Azure-portal op **Resourcegroepen** en klik vervolgens op de resourcegroep die u hebt gemaakt.

2. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens weer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Databricks-werkruimte geïmplementeerd in een virtueel netwerk en de Cosmos DB Spark-connector gebruikt om Cosmos DB-gegevens op te vragen uit Databricks. Ga verder met de zelfstudie voor het gebruik van SQL Server met Azure Databricks voor meer informatie over het werken met Azure Databricks in een virtueel netwerk.

> [!div class="nextstepaction"]
> [Zelfstudie: Een query uitvoeren op een SQL Server Linux Docker-container in een virtueel netwerk vanuit een Azure Databricks-notebook](vnet-injection-sql-server.md)
