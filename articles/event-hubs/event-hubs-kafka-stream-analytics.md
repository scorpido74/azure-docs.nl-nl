---
title: Azure Event Hubs - Apache Kafka-gebeurtenissen verwerken
description: 'Zelfstudie: In dit artikel wordt uitgelegd hoe u Kafka-gebeurtenissen verwerkt die via gebeurtenishubs worden ingenomen met Azure Stream Analytics'
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 04/02/2020
ms.author: spelluru
ms.openlocfilehash: 9c678a91b88b87acb438311b4968be4cae46733b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632802"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Zelfstudie: Apache Kafka verwerken voor gebeurtenissen in eventhubs met streamanalyses 
In dit artikel ziet u hoe u gegevens streamen naar gebeurtenishubs en deze verwerken met Azure Stream Analytics. Het leidt u door de volgende stappen: 

1. Maak een Event Hubs-naamruimte.
2. Maak een Kafka-client die berichten naar de gebeurtenishub verzendt.
3. Maak een Stream Analytics-taak die gegevens van de gebeurtenishub kopieert naar een Azure blob-opslag. 

U hoeft uw protocolclients niet te wijzigen of uw eigen clusters uit te voeren wanneer u het Kafka-eindpunt gebruikt dat wordt blootgesteld door een gebeurtenishub. Azure Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html) en boven. 


## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet om deze snelstart uit te voeren:

* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een Maven binair archief.
* [Git](https://www.git-scm.com/)
* Een **Azure Storage-account**. Als u er geen hebt, [maakt u er een](../storage/common/storage-account-create.md) voordat u verder gaat. De taak Stream Analytics in deze walkthrough slaat de uitvoergegevens op in een Azure blob-opslag. 


## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Wanneer u een naamruimte voor **standaardgebeurtenishubs** maakt, wordt het Kafka-eindpunt voor de naamruimte automatisch ingeschakeld. U gebeurtenissen streamen vanuit uw toepassingen die het Kafka-protocol gebruiken naar gebeurtenishubs met standaardniveau. Volg stapsgewijze instructies in de [gebeurtenishub maken met Azure-portal](event-hubs-create.md) om een naamruimte voor **standaardniveaugebeurtenishubs** te maken. 

> [!NOTE]
> Event Hubs voor Kafka is alleen beschikbaar op **standaard** en **dedicated** lagen. De **basislaag** biedt geen ondersteuning voor Kafka op gebeurtenishubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Berichten verzenden met Kafka in gebeurtenishubs

1. Kloon de [Azure Event Hubs voor Kafka-opslagplaats naar](https://github.com/Azure/azure-event-hubs-for-kafka) uw machine.
2. Navigeer naar de `azure-event-hubs-for-kafka/quickstart/java/producer`map: . 
4. Werk de configuratiegegevens voor `src/main/resources/producer.config`de producent bij in . Geef de **naam** en **verbindingstekenreeks** op voor de **naamruimte van**de gebeurtenishub . 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navigeer `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`naar en open **TestDataReporter.java-bestand** in een editor naar keuze. 
6. Reageer op de volgende regel code:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Voeg de volgende coderegel toe in plaats van de opmerkingscode: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Deze code verzendt de gebeurtenisgegevens in **JSON-indeling.** Wanneer u invoer configureert voor een Stream Analytics-taak, geeft u JSON op als de indeling voor de invoergegevens. 
7. **Voer de producent** uit en stream naar Event Hubs. Ga op een Windows-machine bij het gebruik van `azure-event-hubs-for-kafka/quickstart/java/producer` een **opdrachtprompt Node.js**naar de map voordat u deze opdrachten uitvoert. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Controleren of de gebeurtenishub de gegevens ontvangt

1. Selecteer **gebeurtenishubs** onder **ENTITEITEN**. Controleer of u een gebeurtenishub met de naam **test**ziet . 

    ![Gebeurtenishub - test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Controleer of er berichten binnenkomen in de gebeurtenishub. 

    ![Gebeurtenishub - berichten](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Gebeurtenisgegevens verwerken met een streamanalytics-taak
In deze sectie maakt u een Azure Stream Analytics-taak. De Kafka-client stuurt gebeurtenissen naar de gebeurtenishub. U maakt een Stream Analytics-taak die gebeurtenisgegevens als invoer en uitvoer naar een Azure blob-opslag brengt. Als u geen **Azure Storage-account hebt,** [maakt u er een.](../storage/common/storage-account-create.md)

De query in de taak Stream Analytics gaat door de gegevens zonder analyses uit te voeren. U een query maken die de invoergegevens transformeert om uitvoergegevens in een andere indeling of met verkregen inzichten te produceren.  

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken 

1. Selecteer **+ Een resource maken** in de [Azure-portal](https://portal.azure.com).
2. Selecteer **Analytics** in het Azure **Marketplace-menu** en selecteer **de taak Stream Analytics**. 
3. Ga op de pagina **New Stream Analytics** de volgende acties uitvoeren: 
    1. Voer een **naam** in voor de taak. 
    2. Selecteer uw **abonnement**.
    3. Selecteer **Nieuw maken** voor de **resourcegroep** en voer de naam in. U ook een bestaande resourcegroep **gebruiken.** 
    4. Selecteer een **locatie** voor de taak.
    5. Selecteer **Maken** om de taak te maken. 

        ![Nieuwe functie Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Taakinvoer configureren

1. Selecteer in het meldingsbericht **Ga naar resource om** de **vacaturepagina Stream Analytics** weer te geven. 
2. Selecteer **Invoer** in de sectie **TAAKTOPOLOGIE** in het linkermenu.
3. Selecteer **Streaminvoer toevoegen**en selecteer **vervolgens Gebeurtenishub**. 

    ![Gebeurtenishub toevoegen als invoer](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Voer op de configuratiepagina **van de gebeurtenishub** de volgende acties uit: 

    1. Geef een **alias** op voor de invoer. 
    2. Selecteer uw **Azure-abonnement**.
    3. Selecteer de **naamruimte van** de gebeurtenishub die u eerder hebt gemaakt. 
    4. Selecteer **test** voor de **gebeurtenishub**. 
    5. Selecteer **Opslaan**. 

        ![Configuratie van gebeurtenishubinvoer](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Taakuitvoer configureren 

1. Selecteer **Uitvoer** in de sectie **TAAKTOPOLOGIE** in het menu. 
2. Selecteren **+ Toevoegen** op de werkbalk en **Blob-opslag** selecteren
3. Ga op de pagina Opslaginstellingen van blobs de volgende acties uitvoeren: 
    1. Geef een **alias** op voor de uitvoer. 
    2. Selecteer uw **Azure-abonnement**. 
    3. Selecteer uw **Azure Storage-account**. 
    4. Voer een **naam in voor de container** waarmee de uitvoergegevens uit de query Stream Analytics worden opgeslagen.
    5. Selecteer **Opslaan**.

        ![Opslagconfiguratie blobopslag](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Een query definiëren
Nadat u een Stream Analytics-taak zodanig hebt ingesteld dat een binnenkomende gegevensstroom kan worden gelezen, is de volgende stap het maken van een transformatie waarmee gegevens in real-time worden geanalyseerd. U definieert de transformatie-query met [Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). In deze walkthrough definieert u een query die door de gegevens gaat zonder een transformatie uit te voeren.

1. Selecteer **Query**.
2. Vervang in `[YourOutputAlias]` het queryvenster de uitvoeralias die u eerder hebt gemaakt.
3. Vervang `[YourInputAlias]` door de invoeralias die u eerder hebt gemaakt. 
4. Selecteer **Opslaan** op de werkbalk. 

    ![Query’s uitvoeren](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Voer de Stream Analytics-taak uit

1. Selecteer **Overzicht** in het linkermenu. 
2. Selecteer **Starten**. 

    ![Het menu Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Selecteer Op de taakpagina **Starten** de optie **Start**. 

    ![Taakpagina starten](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Wacht tot de status van de taak verandert **van Start** naar **start**. 

    ![Functiestatus - uitvoeren](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Het scenario testen
1. Voer de **Kafka-producent** opnieuw uit om evenementen naar de gebeurtenishub te verzenden. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Controleer of er **uitvoergegevens** worden gegenereerd in de **Azure Blob-opslag.** U ziet een JSON-bestand in de container met 100 rijen die lijken op de volgende voorbeeldrijen: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    De taak Azure Stream Analytics heeft invoergegevens ontvangen van de gebeurtenishub en deze opgeslagen in de Azure blob-opslag in dit scenario. 



## <a name="next-steps"></a>Volgende stappen
In dit artikel heb je geleerd hoe je streamen naar Event Hubs zonder je protocolclients te wijzigen of je eigen clusters uit te voeren. Zie [de ontwikkelaarhandleiding voor Azure Event Hubs voor Meer](apache-kafka-developer-guide.md)informatie over gebeurtenishubs voor Apache Kafka. 