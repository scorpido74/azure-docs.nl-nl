---
title: Apache Kafka gebeurtenissen van Azure Event Hubs-proces
description: 'Zelf studie: in dit artikel wordt beschreven hoe u Kafka-gebeurtenissen verwerkt die via event hubs worden opgenomen met behulp van Azure Stream Analytics'
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 8047ac5d3db2db230a00583e888a4afea3b282b9
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969377"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Zelf studie: Apache Kafka voor Event Hubs gebeurtenissen verwerken met Stream Analytics 
In dit artikel laat zien hoe gegevens streamen naar Event Hubs waarvoor Kafka is ingeschakeld en met Azure Stream Analytics te verwerken. Dit leidt u door de volgende stappen uit: 

1. Maak een Kafka ingeschakeld Event Hubs-naamruimte.
2. Maak een Kafka-client die berichten naar de event hub verzendt.
3. Maak een Stream Analytics-taak die gegevens van de event hub naar een Azure blob-opslag kopieert. 

U hoeft niet te wijzigen van uw clients protocol of uw eigen clusters worden uitgevoerd wanneer u het Kafka-eindpunt beschikbaar is gemaakt door een event hub. Azure Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html) en hoger. 


## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet om deze snelstart uit te voeren:

* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair Maven-archief.
* [Git](https://www.git-scm.com/)
* Een **Azure Storage-account**. Als u er nog geen hebt, [maakt u er een](../storage/common/storage-account-create.md) voordat u verder gaat. De uitvoergegevens worden opgeslagen in een Azure blob-opslag van de Stream Analytics-taak in dit scenario. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken waarvoor Kafka is ingeschakeld
Wanneer u een **standaardlaag Event hubs** naam ruimte maakt, wordt het Kafka-eind punt voor de naam ruimte automatisch ingeschakeld. U kunt gebeurtenissen streamen vanuit uw toepassingen die gebruikmaken van het Kafka-protocol in de standaardlaag Event Hubs. Volg de stapsgewijze instructies in het **artikel** [een event hub maken met behulp van Azure Portal](event-hubs-create.md) om een standaardlaag Event hubs naam ruimte te maken. 

> [!NOTE]
> Event Hubs voor Kafka is alleen beschikbaar in de lagen **Standard** en **dedicated** . De laag **basis** biedt geen ondersteuning voor Kafka op Event hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Berichten verzenden met Kafka in Event Hubs

1. Kloon de [Azure Event hubs voor de Kafka-opslag plaats](https://github.com/Azure/azure-event-hubs-for-kafka) naar uw computer.
2. Navigeer naar de map: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Werk de configuratie gegevens voor de producent bij in `src/main/resources/producer.config`. Geef de **naam** en **Connection String** op voor de **Event hub naam ruimte**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`en open **TestDataReporter. java** -bestand in een editor naar keuze. 
6. Opmerkingen bij de volgende coderegel:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Voeg de volgende regel code in plaats van de opmerkingen code: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Deze code verzendt de gebeurtenis gegevens in **JSON** -indeling. Wanneer u de invoer voor een Stream Analytics-taak configureert, kunt u JSON opgeven als de notatie voor de invoergegevens. 
7. **Voer de producent uit** en stroom naar Kafka ingeschakelde Event hubs. Schakel op een Windows-computer, wanneer u een **node. js-opdracht prompt**gebruikt, over naar de map `azure-event-hubs-for-kafka/quickstart/java/producer` voordat u deze opdrachten uitvoert. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Controleer of dat de gegevens worden ontvangen die event hub

1. Selecteer **Event hubs** onder **entities**. Controleer of er een Event Hub met de naam **test**wordt weer geven. 

    ![Event hub - testen](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Bevestig dat u berichten die naar de event hub. 

    ![Event hub - berichten](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Gebeurtenis-gegevens verwerken met behulp van een Stream Analytics-taak
In deze sectie maakt u een Azure Stream Analytics-taak. De Kafka-client verzendt gebeurtenissen naar de event hub. U maakt een Stream Analytics-taak die gebeurtenis gegevens als invoer en levert deze naar een Azure blob-opslag. Als u geen **Azure Storage account**hebt, maakt u er [een](../storage/common/storage-account-create.md).

De query in de Stream Analytics-taak wordt doorgegeven via de gegevens zonder de uitvoering van alle analyses. U kunt een query maken die de invoergegevens transformeert om uitvoergegevens in een andere indeling of met de verworven inzichten te produceren.  

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken 

1. Selecteer **+ een resource maken** in de [Azure Portal](https://portal.azure.com).
2. Selecteer **analyse** in het menu van **Azure Marketplace** en selecteer **Stream Analytics taak**. 
3. Voer op de pagina **nieuw stream Analytics** de volgende acties uit: 
    1. Voer een **naam** in voor de taak. 
    2. Selecteer uw **abonnement**.
    3. Selecteer **nieuwe maken** voor de **resource groep** en voer de naam in. U kunt ook **een bestaande** resource groep gebruiken. 
    4. Selecteer een **locatie** voor de taak.
    5. Selecteer **maken** om de taak te maken. 

        ![Nieuwe Stream Analytics-taak](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Taakinvoer configureren

1. Selecteer in het meldings bericht **naar resource gaan** om de pagina **Stream Analytics taak** weer te geven. 
2. Selecteer **invoer** in het gedeelte **taak topologie** in het menu links.
3. Selecteer **stroom invoer toevoegen**en selecteer vervolgens **Event hub**. 

    ![Event hub als invoer toevoegen](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Voer op de pagina **Event hub-invoer** configuratie de volgende acties uit: 

    1. Geef een **alias** op voor de invoer. 
    2. Selecteer uw **Azure-abonnement**.
    3. Selecteer de **Event hub naam ruimte** die u eerder hebt gemaakt. 
    4. Selecteer **testen** voor de **Event hub**. 
    5. Selecteer **Opslaan**. 

        ![Event hub invoer configuratie](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Taakuitvoer configureren 

1. Selecteer **uitvoer** in het gedeelte **taak topologie** in het menu. 
2. Selecteer **+ toevoegen** op de werk balk en selecteer **Blob Storage**
3. Voer de volgende acties op de pagina Blob storage-uitvoer instellingen: 
    1. Geef een **alias** voor de uitvoer op. 
    2. Selecteer uw Azure-**abonnement**. 
    3. Selecteer uw **Azure Storage-account**. 
    4. Voer een **naam in voor de container** waarin de uitvoer gegevens van de stream Analytics query worden opgeslagen.
    5. Selecteer **Opslaan**.

        ![Configuratie van BLOB Storage-uitvoer](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Een query definiëren
Nadat u een Stream Analytics-taak zodanig hebt ingesteld dat een binnenkomende gegevensstroom kan worden gelezen, is de volgende stap het maken van een transformatie waarmee gegevens in real-time worden geanalyseerd. U definieert de transformatie-query met [Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). In dit scenario definieert u een query die wordt doorgegeven via de gegevens zonder uit te voeren van een transformatie.

1. Selecteer **query**.
2. Vervang in het query venster `[YourOutputAlias]` door de uitvoer alias die u eerder hebt gemaakt.
3. Vervang `[YourInputAlias]` door de invoer alias die u eerder hebt gemaakt. 
4. Selecteer **Opslaan** op de werkbalk. 

    ![Query's uitvoeren](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

1. Selecteer **overzicht** in het menu links. 
2. Selecteer **Starten**. 

    ![Startmenu](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Selecteer op de pagina **taak starten** de optie **starten**. 

    ![Startpagina van de taak](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Wacht tot de status van de taak is gewijzigd van **begint** met **uitvoeren**. 

    ![Taakstatus - uitgevoerd](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testen van het scenario
1. Voer de **Kafka-producent** opnieuw uit om gebeurtenissen naar het event hub te verzenden. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Controleer of er **uitvoer gegevens** worden weer gegeven in de **Azure Blob-opslag**. U ziet een JSON-bestand in de container met 100 rijen die lijkt op het volgende voorbeeldrijen: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    De Azure Stream Analytics-taak invoergegevens van de event hub worden ontvangen en opgeslagen in de Azure blob-opslag in dit scenario. 



## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd u hoe u kunt streamen naar Event Hubs waarvoor Kafka is ingeschakeld zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Zie het volgende onderwerp voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Event Hubs maken waarvoor Kafka is ingeschakeld](event-hubs-create-kafka-enabled.md)
- [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Een Kafka-broker spiegelen in een event hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Apache Spark verbinden met een Event Hub waarvoor Kafka is ingeschakeld)
- [Apache Flink verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-flink-tutorial.md)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Kafka Connect integreren met een Event Hub waarvoor Kafka is ingeschakeld)
- [Akka Streams verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub) 
