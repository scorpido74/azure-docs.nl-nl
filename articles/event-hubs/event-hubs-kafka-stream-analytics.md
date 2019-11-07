---
title: 'Zelf studie: Apache Kafka gebeurtenissen verwerken met Stream Analytics-Azure Event Hubs'
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
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 7801b3252ab13df1f92e7aa5e0eba071195cb76c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720614"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Zelf studie: Apache Kafka voor Event Hubs gebeurtenissen verwerken met Stream Analytics 
In dit artikel wordt uitgelegd hoe u gegevens kunt streamen naar Kafka-Event Hubs en hoe u deze kunt verwerken met Azure Stream Analytics. U wordt aangeraden de volgende stappen uit te voeren: 

1. Maak een Kafka-naam ruimte die is ingeschakeld Event Hubs.
2. Maak een Kafka-client die berichten naar de Event Hub verzendt.
3. Maak een Stream Analytics taak waarmee gegevens van het Event Hub naar een Azure Blob-opslag worden gekopieerd. 

U hoeft uw protocol-clients niet te wijzigen of uw eigen clusters uit te voeren wanneer u het Kafka-eind punt gebruikt dat door een Event Hub wordt weer gegeven. Azure Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html) en hoger. 


## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet om deze snelstart uit te voeren:

* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair Maven-archief.
* [Git](https://www.git-scm.com/)
* Een **Azure Storage-account**. Als u er nog geen hebt, [maakt u er een](../storage/common/storage-quickstart-create-account.md) voordat u verder gaat. Met de taak Stream Analytics in dit overzicht worden de uitvoer gegevens opgeslagen in een Azure Blob-opslag. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken waarvoor Kafka is ingeschakeld

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com) en klik op **Een resource maken** linksboven in het scherm.
2. Zoek naar **Event hubs** en selecteer de opties die hier worden weer gegeven:
    
    ![Zoeken naar Event Hubs in de portal](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Selecteer op de pagina **Event hubs** **maken**.
4. Voer op de pagina **naam ruimte maken** de volgende acties uit: 
    1. Geef een unieke **naam** voor de naam ruimte op. 
    2. Selecteer een **prijs categorie**. 
    3. Selecteer **Kafka inschakelen**. Deze stap is een **belang rijke** stap. 
    4. Selecteer het **abonnement** waarin u de Event hub naam ruimte wilt maken. 
    5. Maak een nieuwe **resource groep** of selecteer een bestaande resource groep. 
    6. Selecteer een **locatie**. 
    7. Klik op **Maken**.
    
        ![Een naamruimte maken](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. Selecteer in het **meldings bericht**de **naam van de resource groep**. 

    ![Een naamruimte maken](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Selecteer de **Event hub naam ruimte** in de resource groep. 
2. Zodra de naam ruimte is gemaakt, selecteert u **beleid voor gedeelde toegang** onder **instellingen**.

    ![Klikken op Beleid voor gedeelde toegang](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. U kunt de standaardwaarde **RootManageSharedAccessKey** kiezen of een nieuwe beleid toevoegen. Klik op de naam van het beleid en kopieer de **Connection String**. U gebruikt de connection string om de Kafka-client te configureren. 
    
    ![Beleid selecteren](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

U kunt nu gebeurtenissen vanaf uw toepassing, waarbij gebruikgemaakt wordt van het Kafka-protocol, naar Event Hubs streamen.

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

5. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`en open **TestDataReporter. java** -bestand in een editor naar keuze. 
6. Commentaar toevoegen aan de volgende regel code:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Voeg de volgende regel code toe in plaats van de code voor opmerkingen: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Deze code verzendt de gebeurtenis gegevens in **JSON** -indeling. Wanneer u de invoer voor een Stream Analytics taak configureert, geeft u JSON op als de indeling voor de invoer gegevens. 
7. **Voer de producent uit** en stroom naar Kafka ingeschakelde Event hubs. Schakel op een Windows-computer, wanneer u een **node. js-opdracht prompt**gebruikt, over naar de map `azure-event-hubs-for-kafka/quickstart/java/producer` voordat u deze opdrachten uitvoert. 
   
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
In deze sectie maakt u een Azure Stream Analytics taak. De Kafka-client verzendt gebeurtenissen naar het Event Hub. U maakt een Stream Analytics-taak waarmee gebeurtenis gegevens worden ingevoerd als invoer en naar een Azure Blob-opslag worden uitgevoerd. Als u geen **Azure Storage account**hebt, maakt u er [een](../storage/common/storage-quickstart-create-account.md).

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
    2. Selecteer uw Azure-**abonnement**. 
    3. Selecteer uw **Azure Storage-account**. 
    4. Voer een **naam in voor de container** waarin de uitvoer gegevens van de stream Analytics query worden opgeslagen.
    5. Selecteer **Opslaan**.

        ![Configuratie van Blob Storage-uitvoer](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Een query definiëren
Nadat u een Stream Analytics-taak zodanig hebt ingesteld dat een binnenkomende gegevensstroom kan worden gelezen, is de volgende stap het maken van een transformatie waarmee gegevens in real-time worden geanalyseerd. U definieert de transformatie-query met [Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). In dit overzicht definieert u een query die de gegevens doorgeeft zonder trans formatie uit te voeren.

1. Selecteer **query**.
2. Vervang in het query venster `[YourOutputAlias]` door de uitvoer alias die u eerder hebt gemaakt.
3. Vervang `[YourInputAlias]` door de invoer alias die u eerder hebt gemaakt. 
4. Selecteer **Opslaan** op de werkbalk. 

    ![Query’s uitvoeren](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Voer de Stream Analytics-taak uit

1. Selecteer **overzicht** in het menu links. 
2. Selecteer **Starten**. 

    ![Menu Start](./media/event-hubs-kafka-stream-analytics/start-menu.png)
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
