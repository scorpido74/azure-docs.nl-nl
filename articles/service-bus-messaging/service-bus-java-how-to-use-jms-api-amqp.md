---
title: Gebruik AMQP 1,0 met de API voor Java-berichten service en Azure Service Bus
description: De Java-berichten service (JMS) gebruiken met Azure Service Bus en Advanced Message Queueing Protocol (AMQP) 1,0.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 03/05/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9dff2cc11b71f314de81fd99ed3b72c6337d977f
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967966"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>De Java-berichten service (JMS) gebruiken met Azure Service Bus en AMQP 1,0
In dit artikel wordt uitgelegd hoe u functies van Azure Service Bus berichten (wacht rijen en publiceren/abonneren) gebruikt vanuit Java-toepassingen met behulp van de populaire API-standaard voor Java-berichten service (JMS). Er is een [aanvullende artikel](service-bus-amqp-dotnet.md) waarin wordt uitgelegd hoe u hetzelfde kunt doen met behulp van de Azure service bus .net API. U kunt deze twee hand leidingen gebruiken om meer te weten te komen over cross-platform berichten met behulp van AMQP 1,0.

De Advanced Message Queueing Protocol (AMQP) 1,0 is een efficiënte, betrouw bare, Wire-level berichten protocol dat u kunt gebruiken voor het bouwen van robuuste, platform onafhankelijke toepassingen voor berichten uitwisseling.

Ondersteuning voor AMQP 1,0 in Azure Service Bus houdt in dat u de functie voor het maken van de wachtrij en het publiceren/abonneren van Brokered Messaging-functies kunt gebruiken vanuit diverse platformen met behulp van een efficiënt binair protocol. Daarnaast kunt u toepassingen bouwen die bestaan uit onderdelen die zijn gebouwd met behulp van een combi natie van talen, frameworks en besturings systemen.

## <a name="get-started-with-service-bus"></a>Aan de slag met Service Bus
In deze hand leiding wordt ervan uitgegaan dat u al een Service Bus naam ruimte hebt met een wachtrij met de naam **basicqueue**. Als dat niet het geval is, kunt u [de naam ruimte en wachtrij maken](service-bus-create-namespace-portal.md) met behulp van de [Azure Portal](https://portal.azure.com). Zie [aan de slag met Service Bus wachtrijen](service-bus-dotnet-get-started-with-queues.md)voor meer informatie over het maken van service bus naam ruimten en wacht rijen.

> [!NOTE]
> Gepartitioneerde wacht rijen en onderwerpen bieden ook ondersteuning voor AMQP. Zie gepartitioneerde [Messa ging-entiteiten](service-bus-partitioning.md) en [AMQP 1,0-ondersteuning voor service bus gepartitioneerde wacht rijen en onderwerpen](service-bus-partitioned-queues-and-topics-amqp-overview.md)voor meer informatie.
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>De AMQP 1,0 JMS-client bibliotheek downloaden
Ga naar [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)voor informatie over waar u de nieuwste versie van de Apache Qpid JMS AMQP 1,0-client bibliotheek kunt downloaden.

U moet de volgende vier JAR-bestanden van het Apache Qpid JMS AMQP 1,0-distributie archief toevoegen aan het Java-KLASSENPAD bij het bouwen en uitvoeren van JMS-toepassingen met Service Bus:

* geronimo-jms\_1.1\_spec-1.0.jar
* QPid-JMS-client-[versie]. jar

> [!NOTE]
> JMSe JAR-namen en-versies zijn mogelijk gewijzigd. Zie [QPID JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)voor meer informatie.

## <a name="coding-java-applications"></a>Java-toepassingen coderen
### <a name="java-naming-and-directory-interface-jndi"></a>Java-naamgeving en Directory-interface (JNDI)
JMS maakt gebruik van de Java-naamgevings-en Directory interface (JNDI) om een schei ding tussen logische namen en fysieke namen te maken. Twee typen JMS-objecten worden omgezet met behulp van JNDI: ConnectionFactory en doel. JNDI maakt gebruik van een provider model waarin u verschillende Directory Services kunt koppelen om taken voor naam omzetting af te handelen. De Apache Qpid JMS AMQP 1,0-bibliotheek wordt geleverd met een eenvoudige JNDI provider op basis van een bestand dat is geconfigureerd met behulp van een eigenschappen bestand van de volgende indeling:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>JNDI-context instellen en de ConnectionFactory configureren

De **Connections Tring** waarnaar wordt verwezen in de beschik baarheid in het beleid voor gedeelde toegang in [Azure Portal](https://portal.azure.com) onder **primaire verbindings reeks**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Producer-en consumenten doel wachtrijen configureren
De vermelding die wordt gebruikt voor het definiëren van een bestemming in het QPid eigenschappen bestand JNDI provider heeft de volgende indeling:

De doel wachtrij voor de producent maken- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Een doel wachtrij maken voor de Consumer- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>De JMS-toepassing schrijven
Er zijn geen speciale Api's of opties vereist wanneer u JMS gebruikt met Service Bus. Er zijn echter enkele beperkingen die later worden besproken. Net als bij elke JMS toepassing is het eerste wat vereist is, de configuratie van de JNDI-omgeving, zodat u een **ConnectionFactory** en doelen kunt omzetten.

#### <a name="configure-the-jndi-initialcontext"></a>De JNDI-InitialContext configureren
De JNDI-omgeving wordt geconfigureerd door een hashtabel van configuratie-informatie door te geven aan de constructor van de klasse javax. name. InitialContext. De twee vereiste elementen in de hashtabel zijn de naam van de klasse van de eerste context-Factory en de provider-URL. De volgende code laat zien hoe u de JNDI-omgeving kunt configureren voor het gebruik van de QPid-eigenschappen bestand op basis van JNDI met een eigenschappen bestand met de naam **servicebus. Properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Een eenvoudige JMS-toepassing met behulp van een Service Bus wachtrij
In het volgende voor beeld wordt JMS TextMessages naar een Service Bus wachtrij verzonden met de logische JNDI-naam van de wachtrij en ontvangt de berichten terug.

U hebt alle toegang tot alle bron code-en configuratie gegevens van de Azure Service Bus-voor [beelden JMS-wachtrij Quick Start](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>De toepassing uitvoeren
Geef de **verbindings reeks** op uit het beleid voor gedeelde toegang om de toepassing uit te voeren.
Hieronder ziet u de uitvoer van het formulier door de toepassing uit te voeren:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Toewijzing van AMQP-toestand en Service Bus-bewerking
Dit is de manier waarop een AMQP-toestand wordt omgezet in een Service Bus bewerking:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS-onderwerpen versus Service Bus-onderwerpen
Het gebruik van Azure Service Bus-onderwerpen en-abonnementen via de API van de Java-berichten service (JMS) biedt eenvoudige mogelijkheden voor verzenden en ontvangen. Het is een handige keuze bij het overbrengen van toepassingen van andere bericht brokers met JMS-compatibele Api's, zelfs als Service Bus-onderwerpen verschillen van JMS-onderwerpen en een paar aanpassingen vereisen. 

Met Azure Service Bus onderwerpen worden berichten gerouteerd naar benoemde, gedeelde, duurzame abonnementen die worden beheerd via de Azure resource management-interface, de Azure-opdracht regel Programma's of via de Azure Portal. Elk abonnement staat Maxi maal 2000 selectie regels toe, die elk een filter voorwaarde kunnen hebben en, voor SQL-filters, ook een transformatie actie voor meta gegevens. Elk overeenkomend filter voorwaarde selecteert het invoer bericht dat moet worden gekopieerd naar het tehj-abonnement.  

Het ontvangen van berichten van abonnementen is gelijk aan het ontvangen van berichten uit wacht rijen. Elk abonnement beschikt over een gekoppelde wachtrij met onbestelbare berichten en de mogelijkheid om bericht automatisch door te sturen naar een andere wachtrij of onderwerpen. 

Met JMS-onderwerpen kunnen clients dynamisch niet-duurzame en duurzame abonnees maken die optioneel filteren op berichten met bericht selectie vakjes. Deze niet-gedeelde entiteiten worden niet ondersteund door Service Bus. De syntaxis van de SQL-filter regel voor Service Bus is echter zeer vergelijkbaar met de syntaxis van de bericht kiezer die wordt ondersteund door JMS. 

Het JMS-onderwerp Uitgever is compatibel met Service Bus, zoals in dit voor beeld wordt weer gegeven, maar dynamische abonnees zijn dat niet. De volgende topologie-gerelateerde JMS-Api's worden niet ondersteund met Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Niet-ondersteunde functies en beperkingen
De volgende beperkingen zijn van het gebruik van JMS over AMQP 1,0 met Service Bus, namelijk:

* Er is slechts één **MessageProducer** of **MessageConsumer** per **sessie**toegestaan. Als u meerdere **MessageProducers** of **MessageConsumers** in een toepassing wilt maken, maakt u een speciale **sessie** voor elk van deze.
* Vluchtige onderwerp-abonnementen worden momenteel niet ondersteund.
* **MessageSelectors** worden momenteel niet ondersteund.
* Transactionele sessies en gedistribueerde trans acties worden niet ondersteund.

Daarnaast wordt Azure Service Bus het besturings element gesplitst van het gegevens vlak en wordt daarom niet meerdere dynamische topologie functies van JMS ondersteund:

| Niet-ondersteunde methode          | Vervangen door                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | een onderwerp-abonnement maken poort voor de bericht kiezer                                 |
| createDurableConsumer       | een onderwerp-abonnement maken poort voor de bericht kiezer                                 |
| createSharedConsumer        | Service Bus-onderwerpen zijn altijd deelbaar, zie hierboven                                       |
| createSharedDurableConsumer | Service Bus-onderwerpen zijn altijd deelbaar, zie hierboven                                       |
| createTemporaryTopic        | een onderwerp maken via beheer-API/hulpprogram ma's/Portal waarbij *AutoDeleteOnIdle* is ingesteld op een verloop periode |
| createTopic                 | een onderwerp maken via Management API/tools/Portal                                           |
| afmelden                 | de topic Management API/tools/Portal verwijderen                                             |
| createBrowser               | Niet-ondersteunde. De functie Peek () van de Service Bus-API gebruiken                         |
| createQueue                 | een wachtrij maken via Management API/tools/Portal                                           | 
| createTemporaryQueue        | een wachtrij maken via beheer-API/hulpprogram ma's/Portal waarbij *AutoDeleteOnIdle* is ingesteld op een verloop periode |
| receiveNoWait               | Gebruik de methode receive () die is opgegeven door de Service Bus SDK en geef een zeer lage of nul time-out op |

## <a name="summary"></a>Samenvatting
In deze hand leiding wordt uitgelegd hoe u Service Bus Brokered Messaging functies (wacht rijen en onderwerpen voor publiceren/abonneren) kunt gebruiken vanuit Java met behulp van de populaire JMS-API en AMQP 1,0.

U kunt ook Service Bus AMQP 1,0 gebruiken uit andere talen, waaronder .NET, C, python en PHP. Onderdelen die zijn gebouwd met behulp van deze verschillende talen kunnen berichten veilig en met volledige kwaliteit uitwisselen met behulp van de AMQP 1,0-ondersteuning in Service Bus.

## <a name="next-steps"></a>Volgende stappen
* [Ondersteuning voor AMQP 1,0 in Azure Service Bus](service-bus-amqp-overview.md)
* [AMQP 1,0 gebruiken met de Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1,0-hand leiding voor ontwikkel aars](service-bus-amqp-dotnet.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Java Developer Center](https://azure.microsoft.com/develop/java/)

