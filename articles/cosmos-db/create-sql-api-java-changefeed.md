---
title: Zelf studie-een end-to-end async voor Java SQL API-toepassing met wijzigings feed
description: In deze zelf studie wordt u begeleid bij een eenvoudige Java-toepassing voor SQL-API waarmee documenten in een Azure Cosmos DB container worden ingevoegd, terwijl een gerealiseerde weer gave van de container met behulp van Change feed wordt gehandhaafd.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80586699"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Zelf studie-een end-to-end async voor Java SQL API-toepassing met wijzigings feed

In deze zelf studie leert u een eenvoudige Java SQL API-toepassing waarmee documenten in een Azure Cosmos DB container worden ingevoegd, terwijl een gerealiseerde weer gave van de container met behulp van Change feed wordt gehandhaafd.

## <a name="prerequisites"></a>Vereisten

* Personal computer

* De URI en sleutel voor uw Azure Cosmos DB-account

* Maven

* Java 8

## <a name="background"></a>Achtergrond

De Azure Cosmos DB Change feed biedt een op gebeurtenissen gebaseerde interface waarmee acties kunnen worden geactiveerd als reactie op het invoegen van documenten. Dit heeft veel toepassingen. Bijvoorbeeld in toepassingen die zowel lees-als schrijf bewerkingen zijn, is een hoofd gebruik van wijzigings invoer het maken van een real-time gerealiseerde **weer gave** van een container tijdens het opnemen van documenten. De gerealiseerde weergave container bevat dezelfde gegevens, maar is gepartitioneerd voor efficiënte Lees bewerkingen, waardoor de toepassing zowel lees-als schrijf efficiëntie is.

Het werk van het beheren van wijzigingen in feed-gebeurtenissen wordt grotendeels uitgevoerd door de processor bibliotheek voor wijzigings invoer die in de SDK is ingebouwd. Deze bibliotheek is krachtig genoeg voor het distribueren van wijzigingen in feed-activiteiten tussen meerdere werk rollen, indien gewenst. U hoeft alleen maar een call back in te voeren voor de wijzigings bibliotheek.

In dit eenvoudige voor beeld ziet u de bibliotheek van de wijzigings processor met één werk nemer documenten maken en verwijderen uit een gerealiseerde weer gave.

## <a name="setup"></a>Instellen

Als u dit nog niet hebt gedaan, kloont u het voor beeld van de app opslag plaats:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> U hebt de keuze om deze Snelstartgids te door lopen met Java SDK 4,0 of Java SDK 3.7.0. **Als u Java SDK 3.7.0 wilt gebruiken, typt u het type ```git checkout SDK3.7.0```Terminal **. Als dat niet het geval ```master``` is, blijft de vertakking in de standaard instelling Java SDK 4,0.

Open een terminal in de opslag plaats-map. De app bouwen door uit te voeren

```bash
mvn clean package
```

## <a name="walkthrough"></a>Walkthrough

1. Als eerste controle moet u een Azure Cosmos DB-account hebben. Open de **Azure-Portal** in uw browser, ga naar uw Azure Cosmos DB-account en navigeer in het linkerdeel venster naar **Data Explorer**.

    ![Azure Cosmos DB-account](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Voer de app uit in de Terminal met de volgende opdracht:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Druk op ENTER wanneer u ziet

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    Ga vervolgens terug naar de Azure Portal Data Explorer in uw browser. U ziet dat er een Data Base **GroceryStoreDatabase** is toegevoegd met drie lege containers: 

    * **InventoryContainer** : het inventaris record voor onze voor beeld-boodschappen opslag, gepartitioneerd ```id``` op een item dat een uuid is.
    * **InventoryContainer-pktype** -een gerealiseerde weer gave van de inventaris record, geoptimaliseerd voor query's over het item```type```
    * **InventoryContainer-leases** -er is altijd een leases-container nodig voor wijzigings invoer. leases volgen de voortgang van de app bij het lezen van de wijzigings feed.


    ![Lege containers](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. In de terminal ziet u nu een prompt

    ```bash
    Press enter to start creating the materialized view...
    ```

    Druk op ENTER. Het volgende code blok wordt nu uitgevoerd en initialiseert de processor voor wijzigings invoer op een andere thread: 


    **Java SDK 4,0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

    **Java SDK 3.7.0**
    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```

    ```"SampleHost_1"```is de naam van de werk nemer van de wijzigings feed-processor. ```changeFeedProcessorInstance.start()```Hiermee wordt de wijzigings verwerkings processor daad werkelijk gestart.

    Ga terug naar de Azure Portal Data Explorer in uw browser. Klik onder de container **InventoryContainer-leases** op **items** om de inhoud ervan weer te geven. U ziet dat de wijzigings processor van de feed de lease-container heeft gevuld. de processor heeft de ```SampleHost_1``` werk nemer een lease toegewezen op sommige partities van de **InventoryContainer**.

    ![Leases](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Druk nogmaals op ENTER in de Terminal. Hiermee worden 10 documenten geactiveerd die in **InventoryContainer**worden ingevoegd. Elke invoeging van documenten wordt weer gegeven in de wijzigings feed als JSON. met de volgende Terugbel code worden deze gebeurtenissen verwerkt door de JSON-documenten naar een gerealiseerde weer gave te spie gelen:

    **Java SDK 4,0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

    **Java SDK 3.7.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```

1. Sta toe dat de code 5-10SEC uitvoert. Ga vervolgens terug naar Azure Portal Data Explorer en navigeer naar **InventoryContainer > items**. U ziet dat de items worden ingevoegd in de inventarisatie container; Noteer de partitie sleutel (```id```).

    ![Feed-container](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. In Data Explorer gaat u nu naar **InventoryContainer-pktype > items**. Dit is de gerealiseerde weer gave: de items in deze container spiegel **InventoryContainer** omdat ze in een programma zijn ingevoegd door de wijzigings feed. Noteer de partitie sleutel (```type```). Deze gerealiseerde weer gave is daarom geoptimaliseerd voor query's die ```type```worden gefilterd. Dit zou inefficiënt zijn op **InventoryContainer** omdat deze ```id```is gepartitioneerd op.

    ![Gerealiseerde weergave](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. We gaan een document verwijderen uit zowel **InventoryContainer** als **InventoryContainer-pktype** met slechts één ```upsertItem()``` gesprek. Bekijk eerst de Data Explorer van Azure Portal. We verwijderen het document waarvoor ```/type == "plums"```; deze wordt in rood encircled

    ![Gerealiseerde weergave](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Klik opnieuw op ENTER om de functie ```deleteDocument()``` in de voorbeeld code aan te roepen. Deze functie, zoals hieronder weer gegeven, upsert een nieuwe versie van het ```/ttl == 5```document met, waarmee de TTL (time-to-Live) van het document wordt ingesteld op 5sec. 
    
    **Java SDK 4,0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    **Java SDK 3.7.0**
    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    De wijzigings ```feedPollDelay``` feed is ingesteld op 100 MS; Daarom reageert Change feed deze update bijna onmiddellijk en de hierboven weer ```updateInventoryTypeMaterializedView()``` gegeven oproepen. Met deze aanroep van de laatste functie wordt het nieuwe upsert-document met TTL van 5sec naar **InventoryContainer-pktype**.

    Het resultaat is dat na ongeveer vijf seconden het document verloopt en uit beide containers wordt verwijderd.

    Deze procedure is nood zakelijk omdat wijzigingen in de feed alleen gebeurtenissen bij het invoegen of bijwerken van items veroorzaken, niet bij het verwijderen van items.

1. Druk één keer op ENTER om het programma te sluiten en de bijbehorende resources op te schonen.
