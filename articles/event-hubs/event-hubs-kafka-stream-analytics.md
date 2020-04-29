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
ms.date: 04/02/2020
ms.author: spelluru
ms.openlocfilehash: 9c678a91b88b87acb438311b4968be4cae46733b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80632802"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Zelf studie: Apache Kafka voor Event Hubs gebeurtenissen verwerken met Stream Analytics 
In dit artikel wordt beschreven hoe u gegevens streamt naar Event Hubs en hoe u deze verwerkt met Azure Stream Analytics. U wordt aangeraden de volgende stappen uit te voeren: 

1. Maak een Event Hubs-naamruimte.
2. Maak een Kafka-client die berichten naar de Event Hub verzendt.
3. Maak een Stream Analytics taak waarmee gegevens van het Event Hub naar een Azure Blob-opslag worden gekopieerd. 

U hoeft uw protocol-clients niet te wijzigen of uw eigen clusters uit te voeren wanneer u het Kafka-eind punt gebruikt dat door een Event Hub wordt weer gegeven. Azure Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html) en hoger. 


## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet om deze snelstart uit te voeren:

* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7 +](https://aka.ms/azure-jdks).
* [Down load](https://maven.apache.org/download.cgi) en [Installeer](https://maven.apache.org/install.html) een binaire maven-archief.
* [Git](https://www.git-scm.com/)
* Een **Azure Storage-account**. Als u er nog geen hebt, [maakt u er een](../storage/common/storage-account-create.md) voordat u verder gaat. Met de taak Stream Analytics in dit overzicht worden de uitvoer gegevens opgeslagen in een Azure Blob-opslag. 


## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Wanneer u een **standaardlaag Event hubs** naam ruimte maakt, wordt het Kafka-eind punt voor de naam ruimte automatisch ingeschakeld. U kunt gebeurtenissen streamen vanuit uw toepassingen die gebruikmaken van het Kafka-protocol in de standaardlaag Event Hubs. Volg de stapsgewijze instructies in het **artikel** [een event hub maken met behulp van Azure Portal](event-hubs-create.md) om een standaardlaag Event hubs naam ruimte te maken. 

> [!NOTE]
> Event Hubs voor Kafka is alleen beschikbaar in de lagen **Standard** en **dedicated** . De laag **basis** biedt geen ondersteuning voor Kafka op Event hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Berichten verzenden met Kafka in Event Hubs

1. Kloon de [Azure Event hubs voor de Kafka-opslag plaats](https://github.com/Azure/azure-event-hubs-for-kafka) naar uw computer.
2. Navigeer naar de map: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Werk de configuratie gegevens voor de producent bij `src/main/resources/producer.config`in. Geef de **naam** en **Connection String** op voor de **Event hub naam ruimte**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`en open **TestDataReporter. java** -bestand in een editor van uw keuze. 
6. Commentaar toevoegen aan de volgende regel code:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Voeg de volgende regel code toe in plaats van de code voor opmerkingen: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Deze code verzendt de gebeurtenis gegevens in **JSON** -indeling. Wanneer u de invoer voor een Stream Analytics taak configureert, geeft u JSON op als de indeling voor de invoer gegevens. 
7. **Voer de producent uit** en stream in Event hubs. Schakel op een Windows-computer, wanneer u een **node. js-opdracht prompt**gebruikt `azure-event-hubs-for-kafka/quickstart/java/producer` , over naar de map voordat u deze opdrachten uitvoert. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Controleren of Event Hub de gegevens ontvangt

1. Selecteer **Event hubs** onder **entities**. Controleer of er een Event Hub met de naam **test**wordt weer geven. 

    ![Event hub-testen](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Controleer of er berichten worden weer geven in de Event Hub. 

    ![Event hub-berichten](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Gebeurtenis gegevens verwerken met een Stream Analytics taak
In deze sectie maakt u een Azure Stream Analytics taak. De Kafka-client verzendt gebeurtenissen naar het Event Hub. U maakt een Stream Analytics-taak waarmee gebeurtenis gegevens worden ingevoerd als invoer en naar een Azure Blob-opslag worden uitgevoerd. Als u geen **Azure Storage account**hebt, maakt u er [een](../storage/common/storage-account-create.md).

De query in de Stream Analytics-taak geeft de gegevens door en zonder dat er analyses worden uitgevoerd. U kunt een query maken die de invoer gegevens transformeert om uitvoer gegevens te produceren in een andere indeling of met verworven inzichten.  

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken 

1. Selecteer **+ een resource maken** in de [Azure Portal](https://portal.azure.com).
2. Selecteer **analyse** in het menu van **Azure Marketplace** en selecteer **Stream Analytics taak**. 
3. Voer op de pagina **nieuw stream Analytics** de volgende acties uit: 
    1. Voer een **naam** in voor de taak. 
    2. Selecteer uw **abonnement**.
    3. Selecteer **nieuwe maken** voor de **resource groep** en voer de naam in. U kunt ook **een bestaande** resource groep gebruiken. 
    4. Selecteer een **locatie** voor de taak.
    5. Selecteer **maken** om de taak te maken. 

        ![Nieuwe Stream Analytics taak](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Taakinvoer configureren

1. Selecteer in het meldings bericht **naar resource gaan** om de pagina **Stream Analytics taak** weer te geven. 
2. Selecteer **invoer** in het gedeelte **taak topologie** in het menu links.
3. Selecteer **stroom invoer toevoegen**en selecteer vervolgens **Event hub**. 

    ![Event Hub toevoegen als invoer](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Voer op de pagina **Event hub-invoer** configuratie de volgende acties uit: 

    1. Geef een **alias** op voor de invoer. 
    2. Selecteer uw **Azure-abonnement**.
    3. Selecteer de **Event hub naam ruimte** die u eerder hebt gemaakt. 
    4. Selecteer **testen** voor de **Event hub**. 
    5. Selecteer **Opslaan**. 

        ![Invoer configuratie van Event hub](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Taakuitvoer configureren 

1. Selecteer **uitvoer** in het gedeelte **taak topologie** in het menu. 
2. Selecteer **+ toevoegen** op de werk balk en selecteer **Blob Storage**
3. Voer de volgende handelingen uit op de pagina uitvoer instellingen voor Blob-opslag: 
    1. Geef een **alias** voor de uitvoer op. 
    2. Selecteer uw Azure- **abonnement**. 
    3. Selecteer uw **Azure Storage-account**. 
    4. Voer een **naam in voor de container** waarin de uitvoer gegevens van de stream Analytics query worden opgeslagen.
    5. Selecteer **Opslaan**.

        ![Configuratie van Blob Storage-uitvoer](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Een query definiëren
Nadat u een Stream Analytics-taak zodanig hebt ingesteld dat een binnenkomende gegevensstroom kan worden gelezen, is de volgende stap het maken van een transformatie waarmee gegevens in real-time worden geanalyseerd. U definieert de transformatie-query met [Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). In dit overzicht definieert u een query die de gegevens doorgeeft zonder trans formatie uit te voeren.

1. Selecteer **query**.
2. Vervang `[YourOutputAlias]` in het query venster de uitvoer alias die u eerder hebt gemaakt.
3. Vervang `[YourInputAlias]` door de invoer alias die u eerder hebt gemaakt. 
4. Selecteer **Opslaan** op de werkbalk. 

    ![Query’s uitvoeren](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Voer de Stream Analytics-taak uit

1. Selecteer **overzicht** in het menu links. 
2. Selecteer **Starten**. 

    ![Het menu Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Selecteer op de pagina **taak starten** de optie **starten**. 

    ![Taak pagina starten](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Wacht tot de status van de taak is gewijzigd van **begint** met **uitvoeren**. 

    ![Taak status-wordt uitgevoerd](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Het scenario testen
1. Voer de **Kafka-producent** opnieuw uit om gebeurtenissen naar het event hub te verzenden. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Controleer of er **uitvoer gegevens** worden weer gegeven in de **Azure Blob-opslag**. U ziet een JSON-bestand in de container met 100 rijen die eruitzien als de volgende voorbeeld rijen: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    De Azure Stream Analytics taak heeft invoer gegevens ontvangen van de Event Hub en opgeslagen in de Azure Blob-opslag in dit scenario. 



## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u kunt streamen naar Event Hubs zonder uw protocol-clients te wijzigen of uw eigen clusters uit te voeren. Zie [Apache Kafka ontwikkelaars handleiding voor Azure Event hubs](apache-kafka-developer-guide.md)voor meer informatie over Event Hubs voor Apache Kafka. 