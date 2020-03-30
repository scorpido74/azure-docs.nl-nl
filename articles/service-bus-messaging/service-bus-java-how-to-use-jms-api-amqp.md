---
title: AMQP gebruiken met Java Message Service API & Azure Service Bus
description: De Java Message Service (JMS) gebruiken met Azure Service Bus en Advanced Message Queuing Protocol (AMQP) 1.0.
services: service-bus-messaging
documentationcenter: java
author: axisc
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: cd06838abbb69af5684fdea18c42f6a8f95ffe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371264"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>De Java Message Service (JMS) gebruiken met Azure Service Bus en AMQP 1.0
In dit artikel wordt uitgelegd hoe u azure servicebus-berichtenfuncties (wachtrijen en publicatie-/abonneeronderwerpen) van Java-toepassingen gebruiken met behulp van de populaire API-standaard (Java Message Service) (JMS). Er is een [begeleidend artikel](service-bus-amqp-dotnet.md) waarin wordt uitgelegd hoe u hetzelfde doen met de Azure Service Bus .NET API. U deze twee gidsen samen gebruiken om meer te weten te komen over cross-platform messaging met AMQP 1.0.

Het Advanced Message Queuing Protocol (AMQP) 1.0 is een efficiënt, betrouwbaar messaging-protocol op draadniveau dat u gebruiken om robuuste, cross-platform messaging-toepassingen te bouwen.

Ondersteuning voor AMQP 1.0 in Azure Service Bus betekent dat u de wachtrijen en publicatie/abonneren van brokered messaging-functies van een reeks platforms gebruiken met behulp van een efficiënt binair protocol. Bovendien u toepassingen bouwen die bestaan uit componenten die zijn gebouwd met behulp van een mix van talen, frameworks en besturingssystemen.

## <a name="get-started-with-service-bus"></a>Aan de slag met Service Bus
In deze handleiding wordt ervan uitgegaan dat u al `basicqueue`een naamruimte van de servicebus hebt met een wachtrij met de naam . Als u dit niet doet, u [de naamruimte en wachtrij maken](service-bus-create-namespace-portal.md) met behulp van de [Azure-portal.](https://portal.azure.com) Zie [Aan de slag met wachtrijen voor servicebus's](service-bus-dotnet-get-started-with-queues.md)voor meer informatie over het maken van naamruimten en wachtrijen voor servicebussen.

> [!NOTE]
> Partitiewachtrijen en onderwerpen ondersteunen ook AMQP. Zie [Partitioned messaging-entiteiten](service-bus-partitioning.md) en [AMQP 1.0-ondersteuning voor wachtrijen en onderwerpen voor servicebus-indelingen](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>De AMQP 1.0 JMS-clientbibliotheek downloaden
Voor informatie over waar de nieuwste versie van de Apache Qpid JMS [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)AMQP 1.0 clientbibliotheek te downloaden, bezoek .

U moet de volgende vier JAR-bestanden uit het Apache Qpid JMS AMQP 1.0-distributiearchief toevoegen aan het Java CLASSPATH bij het bouwen en uitvoeren van JMS-toepassingen met Service Bus:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> JMS JAR namen en versies kunnen zijn veranderd. Zie [Qpid JMS - AMQP 1.0 voor](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)meer informatie.

## <a name="coding-java-applications"></a>Java-toepassingen coderen
### <a name="java-naming-and-directory-interface-jndi"></a>Java-naamgeving en directory-interface (JNDI)
JMS gebruikt de Java Naming and Directory Interface (JNDI) om een scheiding te maken tussen logische namen en fysieke namen. Twee typen JMS-objecten worden opgelost met JNDI: ConnectionFactory en Destination. JNDI maakt gebruik van een providermodel waarin u verschillende directoryservices aansluiten om taken voor naamomzetting te verwerken. De Apache Qpid JMS AMQP 1.0-bibliotheek wordt geleverd met een eenvoudige JNDI-provider met een eenvoudige eigenschapsbestand die is geconfigureerd met behulp van een eigenschappenbestand met de volgende indeling:

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

De **verbindingstekenreeks** waarnaar wordt verwezen in de tekenreeks die beschikbaar is in de 'Gedeelde toegangsbeleidsregels' in de [Azure-portal](https://portal.azure.com) onder **primaire verbindingstekenreeks**
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Wachtrijen voor productie- en consumentenbestemmingen configureren
De vermelding die wordt gebruikt om een bestemming in de JNDI-provider van Qpid-eigenschappen te definiëren, heeft de volgende indeling:

De doelwachtrij voor de producent maken - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Een bestemmingswachtrij maken voor de consument - 
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
Er zijn geen speciale API's of opties vereist bij het gebruik van JMS met Service Bus. Er zijn echter een paar beperkingen die later zullen worden behandeld. Zoals bij elke JMS-toepassing, het eerste wat nodig is configuratie van de JNDI-omgeving, om te kunnen een **ConnectionFactory** en bestemmingen op te lossen.

#### <a name="configure-the-jndi-initialcontext"></a>De JNDI InitialContext configureren
De JNDI-omgeving wordt geconfigureerd door een hashtable met configuratiegegevens door te geven aan de constructor van de klasse javax.naming.InitialContext. De twee vereiste elementen in de hashtabel zijn de klassennaam van de initialcontextfabriek en de URL van de provider. In de volgende code ziet u hoe u de JNDI-omgeving configureert om de JNDI-provider op basis van het Qpid-eigenschappenbestand te gebruiken met een eigenschappenbestand met de naam **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Een eenvoudige JMS-toepassing met een wachtrij voor servicebus
In het volgende voorbeeldprogramma worden JMS TextMessages naar een wachtrij voor servicebussen verzonden met de logische naam Van De JNDI van QUEUE en worden de berichten teruggestuurd.

U hebt allemaal snel toegang tot alle broncode- en configuratiegegevens van de [JMS-wachtrij voor Azure Service Bus Samples](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

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
Geef de **verbindingstekenreeks door** vanuit het beleid voor gedeelde toegang om de toepassing uit te voeren.
Hieronder vindt u de uitvoer van het formulier door de toepassing uit te voeren:

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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP-toewijzing van dispositie en servicebus
Hier is hoe een AMQP-dispositie zich vertaalt naar een Service Bus-bewerking:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS-onderwerpen vs. Service Bus-onderwerpen
Het gebruik van Azure Service Bus-onderwerpen en -abonnementen via de JAVA Message Service (JMS)-API biedt basismogelijkheden voor verzenden en ontvangen. Het is een handige keuze bij het overzetten van toepassingen van andere berichtenmakelaars met JMS-compatibele API's, hoewel Service Bus-onderwerpen verschillen van JMS-onderwerpen en een paar aanpassingen vereisen. 

Azure Service Bus-onderwerpen leiden berichten naar benoemde, gedeelde, duurzame abonnementen die worden beheerd via de Azure Resource Management-interface, de Azure-opdrachtregelhulpprogramma's of via de Azure-portal. Elk abonnement biedt maximaal 2000 selectieregels, die elk een filtervoorwaarde kunnen hebben en, voor SQL-filters, ook een actie voor het transformeren van metagegevens. Elke filtervoorwaardeovereenkomst selecteert het invoerbericht dat naar het abonnement moet worden gekopieerd.  

Het ontvangen van berichten van abonnementen is identiek ontvangen van berichten uit wachtrijen. Elk abonnement heeft een bijbehorende wachtrij voor dode letters en de mogelijkheid om berichten automatisch door te sturen naar een andere wachtrij of onderwerpen. 

JMS-onderwerpen stellen clients in staat om dynamisch niet-duurzame en duurzame abonnees te maken die optioneel filterberichten met berichtkiezer toestaan. Deze niet-gedeelde entiteiten worden niet ondersteund door Service Bus. De syntaxis van de SQL-filterregel voor Service Bus is echter vergelijkbaar met de syntaxis van de berichtkiezer die wordt ondersteund door JMS. 

De uitgeverkant van HET JMS-onderwerp is compatibel met Service Bus, zoals in dit voorbeeld wordt weergegeven, maar dynamische abonnees niet. De volgende topologiegerelateerde JMS-API's worden niet ondersteund met Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Niet-ondersteunde functies en beperkingen
De volgende beperkingen bestaan bij het gebruik van JMS via AMQP 1.0 met Service Bus, namelijk:

* Per **sessie**is slechts één **MessageProducer** of **MessageConsumer** toegestaan. Als u meerdere **MessageProducers** of **MessageConsumers** in een toepassing wilt maken, maakt u een speciale **sessie** voor elk van hen.
* Vluchtige onderwerpabonnementen worden momenteel niet ondersteund.
* **MessageSelectors** worden momenteel niet ondersteund.
* Gedistribueerde transacties worden niet ondersteund (maar afgehandelde sessies worden ondersteund).

Bovendien splitst Azure Service Bus het besturingsvlak uit het gegevensvlak en ondersteunt het daarom geen verschillende dynamische topologiefuncties van JMS:

| Niet-ondersteunde methode          | Vervangen door                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| SustainableSubscriber maken     | Een onderwerpabonnement maken dat de berichtkiezer overstuurt                                 |
| creëertSustainableConsumer       | Een onderwerpabonnement maken dat de berichtkiezer overstuurt                                 |
| makenSharedConsumer        | Service Bus onderwerpen zijn altijd deelbaar, zie hierboven                                       |
| creërenSharedDurableConsumer | Service Bus onderwerpen zijn altijd deelbaar, zie hierboven                                       |
| makenTemporaryTopic        | een onderwerp maken via beheer-API/tools/portal met *AutoDeleteOnIdle* ingesteld op een vervaldatum |
| createTopic                 | een onderwerp maken via beheer-API/tools/portal                                           |
| Uitschrijven                 | de API/tools/portal voor onderwerpbeheer verwijderen                                             |
| createBrowser               | Unsupported. De peek-functionaliteit van de API servicebus gebruiken                         |
| makenWachtrij                 | een wachtrij maken via beheer-API/tools/portal                                           | 
| tijdelijke wachtrij maken        | een wachtrij maken via beheer-API/tools/portal met *AutoDeleteOnIdle* ingesteld op een vervaldatum |
| ontvangenNoWait               | gebruik maken van de receive() methode die door de Service Bus SDK en geef een zeer lage of nul time-out |

## <a name="summary"></a>Samenvatting
Deze handleiding liet zien hoe u servicebus-berichtenfuncties (wachtrijen en publicatie-/subscribe-onderwerpen) van Java gebruiken met behulp van de populaire JMS API en AMQP 1.0.

U servicebus AMQP 1.0 ook uit andere talen gebruiken, waaronder .NET, C, Python en PHP. Componenten die met deze verschillende talen zijn gebouwd, kunnen berichten betrouwbaar en op volledige getrouwheid uitwisselen met behulp van de AMQP 1.0-ondersteuning in Service Bus.

## <a name="next-steps"></a>Volgende stappen
* [AMQP 1.0-ondersteuning in Azure Service Bus](service-bus-amqp-overview.md)
* [AMQP 1.0 gebruiken met de Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 Developer's Guide](service-bus-amqp-dotnet.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Java Developer Center](https://azure.microsoft.com/develop/java/)

