---
title: Zelfstudie - een end-to-end Voorbeeld van Async Java SQL API-toepassings met Change Feed
description: Deze zelfstudie leidt u door een eenvoudige Java SQL API-toepassing die documenten invoegt in een Azure Cosmos DB-container, terwijl een gematerialiseerde weergave van de container wordt behouden met behulp van Change Feed.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586699"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Zelfstudie - een end-to-end Voorbeeld van Async Java SQL API-toepassings met Change Feed

Deze zelfstudiegids leidt u door een eenvoudige Java SQL API-toepassing die documenten invoegt in een Azure Cosmos DB-container, terwijl een gematerialiseerde weergave van de container wordt behouden met behulp van Change Feed.

## <a name="prerequisites"></a>Vereisten

* Personal computer

* De URI en de sleutel voor uw Azure Cosmos DB-account

* Maven

* Java 8

## <a name="background"></a>Achtergrond

De Azure Cosmos DB Change Feed biedt een gebeurtenisgestuurde interface om acties te activeren als reactie op het invoegen van documenten. Dit heeft vele toepassingen. Bijvoorbeeld in toepassingen die zowel lezen als schrijven zwaar, een belangrijkste gebruik van Change Feed is het creëren van een real-time **gematerialiseerde weergave** van een container als het is het innemen van documenten. De gematerialiseerde weergavecontainer houdt dezelfde gegevens vast, maar wordt verdeeld voor efficiënte reads, waardoor de toepassing zowel lezen als efficiënt is.

Het beheer van Change Feed-gebeurtenissen wordt grotendeels verzorgd door de Change Feed Processor-bibliotheek die in de SDK is ingebouwd. Deze bibliotheek is krachtig genoeg om change feed-gebeurtenissen te distribueren onder meerdere werknemers, indien gewenst. Het enige wat u hoeft te doen is de bibliotheek Feed wijzigen een terugroep.

In dit eenvoudige voorbeeld wordt de bibliotheek Feedprocessor wijzigen met één werknemer die documenten maakt en verwijderd uit een gematerialiseerde weergave.

## <a name="setup"></a>Instellen

Als u dit nog niet hebt gedaan, kloont u het voorbeeld repo van de app:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> Je hebt de keuze om deze Quickstart te doorlopen met Java SDK 4.0 of Java SDK 3.7.0. **Als u Java SDK 3.7.0 wilt gebruiken, ```git checkout SDK3.7.0```in het terminaltype. ** Anders blijft u ```master``` op het filiaal, dat standaard wordt ingesteld op Java SDK 4.0.

Open een terminal in de repo-map. De app bouwen door

```bash
mvn clean package
```

## <a name="walkthrough"></a>Kennismaking

1. Als eerste controle moet u een Azure Cosmos DB-account hebben. Open de **Azure Portal** in uw browser, ga naar uw Azure Cosmos DB-account en navigeer in het linkerdeelvenster naar **Gegevensverkenner.**

    ![Azure Cosmos DB-account](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Voer de app in de terminal uit met de volgende opdracht:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Druk op enter wanneer u ziet

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    keer vervolgens terug naar de Azure Portal Data Explorer in uw browser. U ziet een database **GroceryStoreDatabase** is toegevoegd met drie lege containers: 

    * **InventoryContainer** - De voorraadrecord voor onze voorbeeldsupermarkt, verdeeld over een uuid-artikel. ```id```
    * **InventoryContainer-pktype** - Een gematerialiseerde weergave van de voorraadrecord, geoptimaliseerd voor query's boven artikel```type```
    * **InventoryContainer-leases** - Een leasecontainer is altijd nodig voor Change Feed; leases houden de voortgang van de app bij bij het lezen van de change feed.


    ![Lege containers](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. In de terminal ziet u nu een prompt

    ```bash
    Press enter to start creating the materialized view...
    ```

    Druk op enter. Nu wordt de volgende codeblok uitgevoerd en de Change Feed-processor op een andere thread geïnitialiseren: 


    **Java SDK 4.0**
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

    ```"SampleHost_1"```is de naam van de processormedewerker wijzigen. ```changeFeedProcessorInstance.start()```is wat daadwerkelijk begint de Change Feed processor.

    Ga terug naar de Azure Portal Data Explorer in uw browser. Klik onder de container **InventoryContainer-leases** op **artikelen** om de inhoud ervan te bekijken. U ziet dat De feedprocessor wijzigen de leasecontainer heeft ingevuld, ```SampleHost_1``` d.w.z. de verwerker heeft de werknemer een lease-overeenkomst toegewezen voor sommige partities van de **InventoryContainer.**

    ![Leases](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Druk weer op enter in de terminal. Hierdoor worden 10 documenten geactiveerd die in **InventoryContainer**moeten worden ingevoegd. Elke documentinvoeging wordt in de feed Wijzigen als JSON weergegeven; de volgende terugbelcode verwerkt deze gebeurtenissen door de JSON-documenten te spiegelen in een gematerialiseerde weergave:

    **Java SDK 4.0**
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

1. Laat de code 5-10sec uitvoeren. Ga vervolgens terug naar de Azure Portal Data Explorer en navigeer naar **InventoryContainer > items**. U moet zien dat artikelen in de voorraadcontainer worden ingevoegd. let op de```id```partitiesleutel ( ).

    ![Voercontainer](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Navigeer in Data Explorer naar **>->-items van InventoryContainer-pktype**. Dit is de gematerialiseerde weergave - de items in deze containerspiegel **InventoryContainer** omdat ze programmatisch zijn ingevoegd door Change Feed. Let op de```type```partitiesleutel ( ). Deze gematerialiseerde weergave is dus geoptimaliseerd ```type```voor query's die overfilteren , wat ```id```inefficiënt zou zijn op **InventoryContainer** omdat deze is verdeeld op .

    ![Gerealiseerde weergave](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. We verwijderen een document uit zowel **InventoryContainer** als **InventoryContainer-pktype** met slechts één ```upsertItem()``` oproep. Kijk eerst eens naar Azure Portal Data Explorer. We verwijderen het document ```/type == "plums"```waarvoor ; het is omcirkeld in rood hieronder

    ![Gerealiseerde weergave](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Druk opnieuw op enter ```deleteDocument()``` om de functie in de voorbeeldcode aan te roepen. Deze functie, hieronder weergegeven, upserts een ```/ttl == 5```nieuwe versie van het document met , die document Time-To-Live (TTL) sets op 5sec. 
    
    **Java SDK 4.0**
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

    De change ```feedPollDelay``` feed is ingesteld op 100 ms; Daarom reageert Change Feed vrijwel direct op ```updateInventoryTypeMaterializedView()``` deze update en oproepen hierboven weergegeven. Die laatste functieoproep zal het nieuwe document met TTL van 5sec upsert in **InventoryContainer-pktype**.

    Het effect is dat na ongeveer 5 seconden, het document verloopt en wordt verwijderd uit beide containers.

    Deze procedure is nodig omdat Feed wijzigen alleen gebeurtenissen uitgeeft voor het invoegen of bijwerken van objecten, niet bij het verwijderen van objecten.

1. Druk nog een keer op enter om het programma te sluiten en de bronnen op te schonen.
