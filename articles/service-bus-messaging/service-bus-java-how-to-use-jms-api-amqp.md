---
title: Gebruik AMQP met de API voor Java-berichten service & Azure Service Bus
description: De Java-berichten service (JMS) gebruiken met Azure Service Bus en Advanced Message Queueing Protocol (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119154"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>De Java-berichten service (JMS) gebruiken met Azure Service Bus en AMQP 1,0

Ondersteuning voor **Advanced Message queueing Protocol (AMQP) 1,0-** Protocol in azure service bus betekent dat u de wachtrij kunt gebruiken voor het publiceren en abonneren van brokered messaging-functies vanuit diverse platformen met behulp van een efficiënt binair protocol. Daarnaast kunt u toepassingen bouwen die bestaan uit onderdelen die zijn gebouwd met behulp van een combi natie van talen, frameworks en besturings systemen.

In dit artikel wordt uitgelegd hoe u Azure Service Bus functies voor berichten (wacht rijen en publiceren/abonneren) gebruikt vanuit Java-toepassingen met behulp van de populaire API voor **Java-berichten service (JMS)** via het AMQP-protocol.

## <a name="get-started-with-service-bus"></a>Aan de slag met Service Bus
In deze hand leiding wordt ervan uitgegaan dat u al een Service Bus naam ruimte hebt met een wachtrij met de naam `basicqueue` . Als dat niet het geval is, kunt u [de naam ruimte en wachtrij maken](service-bus-create-namespace-portal.md) met behulp van de [Azure Portal](https://portal.azure.com). Zie [aan de slag met Service Bus wachtrijen](service-bus-dotnet-get-started-with-queues.md)voor meer informatie over het maken van service bus naam ruimten en wacht rijen.

> [!NOTE]
> Gepartitioneerde wacht rijen en onderwerpen bieden ook ondersteuning voor AMQP. Zie [gepartitioneerde Messa ging-entiteiten](service-bus-partitioning.md) en [AMQP 1,0-ondersteuning voor service bus gepartitioneerde wacht rijen en onderwerpen](service-bus-partitioned-queues-and-topics-amqp-overview.md)voor meer informatie.
> 
>

## <a name="what-jms-features-are-supported"></a>Welke JMS-functies worden ondersteund?

Hier vindt u de JMS-functies die worden ondersteund in Azure Service Bus.

| Functies | Azure Service Bus Standard-laag-JMS 1,1 | Azure Service Bus Premium-laag-JMS 2,0 (preview-versie) |
|---|---|---|
| Automatisch maken van entiteiten via AMQP | Niet ondersteund | **Ondersteund** |
| Wachtrijen | **Ondersteund** | **Ondersteund** |
| Onderwerpen | **Ondersteund** | **Ondersteund** |
| Tijdelijke wacht rijen | Niet ondersteund <br/> (Maak in plaats daarvan een normale wachtrij met *AutoDeleteOnIdle* ingesteld) | **Ondersteund** |
| Tijdelijke onderwerpen | Niet ondersteund | **Ondersteund** |
| Bericht kiezer | Niet ondersteund | **Ondersteund** |
| Wachtrij browsers | Niet ondersteund <br/> (Gebruik de functie *Peek* van de Service Bus-API) | **Ondersteund** |
| Gedeelde duurzame abonnementen | **Ondersteund** | **Ondersteund**|
| Niet-gedeelde duurzame abonnementen | Niet ondersteund | **Ondersteund** |
| Gedeelde niet-duurzame abonnementen | Niet ondersteund | **Ondersteund** |
| Niet-verdeelde niet-duurzame abonnementen | Niet ondersteund | **Ondersteund** |
| Afmelden voor duurzame abonnementen | Niet ondersteund | **Ondersteund** |
| ReceiveNoWait | Niet ondersteund | **Ondersteund** |
| Gedistribueerde trans acties | Niet ondersteund | Niet ondersteund |
| Duurzaam Ondersteuningabonnementen | Niet ondersteund | Niet ondersteund |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Aanvullende voor behoud voor Service Bus Standard-laag
Er is slechts één **MessageProducer** of **MessageConsumer** per **sessie**toegestaan. Als u meerdere **MessageProducers** of **MessageConsumers** in een toepassing wilt maken, maakt u een speciale **sessie** voor elk van deze.

## <a name="downloading-the-java-message-service-jms-client-library"></a>De client bibliotheek van de Java-berichten service (JMS) downloaden

De onderstaande bibliotheken moeten worden gebruikt om verbinding te maken met Azure Service Bus en de API van de Java-berichten service (JMS) via AMQP te gebruiken. Deze moeten worden toegevoegd aan het build-pad met behulp van het voorkeurs beheer programma voor afhankelijkheden van uw project.

De vereiste client bibliotheek is afhankelijk van de prijs categorie die wordt gebruikt.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Premium-laag-JMS 2,0 over AMQP (preview-versie)

Als u gebruik wilt maken van alle preview-functies die beschikbaar zijn in Azure Service Bus Premium-laag, gebruikt u de [JMS-bibliotheek van Azure-servicebus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) .

### <a name="standard-tier---jms-11-over-amqp"></a>Standard-laag-JMS 1,1 via AMQP

Als u de JMS-functies wilt gebruiken die worden ondersteund door Service Bus Standard-laag (Zie [welke JMS-functies worden ondersteund?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)) gebruikt u de onderstaande bibliotheken:

* [Geronimo JMS 1,1 spec](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [QPid JMS-client](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> JMSe JAR-namen en-versies zijn mogelijk gewijzigd. Zie [QPID JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)voor meer informatie.
>

## <a name="coding-java-applications"></a>Java-toepassingen coderen

Zodra de afhankelijkheden zijn geïmporteerd, kunnen de Java-toepassingen worden geschreven in een JMS provider neutraal.

Omdat Azure Service Bus Standard en Premium verschillen van de afhankelijkheden en het aantal JMS-functies die worden ondersteund, is het programmeer model enigszins anders voor de twee.

> [!IMPORTANT]
> De onderstaande hand leiding laat zien hoe u verbinding maakt met Azure Service Bus op basis van een eenvoudige toepassing.
>
> Gezien dat de meeste bedrijfs toepassings architecturen een aangepaste manier hebben om afhankelijkheden en configuraties te beheren, kunt u de onderstaande hand leiding gebruiken om te begrijpen wat er nodig is en uw toepassing op de juiste wijze aan te passen.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Verbinding maken met Azure Service Bus met behulp van JMS

Als u verbinding wilt maken met Azure Service Bus met behulp van JMS-clients, hebt u de **Connections Tring** die beschikbaar is in het beleid voor gedeelde toegang in de [Azure Portal](https://portal.azure.com) onder **primaire verbindings reeks**.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Verbinding maken met Azure Service Bus Premium via JMS 2,0 (preview-versie)

1. Exemplaar maken van de`ServiceBusJmsConnectionFactorySettings`
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Exemplaar `ServiceBusJmsConnectionFactory` met de juiste `ServiceBusConnectionString` .
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Gebruik de `ConnectionFactory` om een te maken `Connection` en vervolgens een`Session` 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    of a `JMSContext` (voor JMS 2,0-clients)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Verbinding maken met Azure Service Bus Standard via JMS 1,1

1. Voeg Azure Service Bus configuratie in JNDI properties-bestand met de naam **servicebus. Properties**in.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. JNDI-context instellen en de ConnectionFactory configureren
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. Gebruik de `ConnectionFactory` om a `Connection` en vervolgens een te maken `Session` .
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>De JMS-toepassing schrijven

Zodra de `Session` of `JMSContext` is geïnstantieerd, kan uw toepassing gebruikmaken van de vertrouwde JMS-api's om zowel beheer-als gegevens bewerkingen uit te voeren.

Raadpleeg de lijst met [ondersteunde JMS-functies](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) voor de standaard-en Premium-laag om te zien welke api's in elke laag worden ondersteund.

## <a name="summary"></a>Samenvatting
In deze hand leiding wordt uitgelegd hoe Java-client toepassingen die gebruikmaken van Java Message Service (JMS) via AMQP 1,0 kunnen communiceren met Azure Service Bus.

U kunt ook Service Bus AMQP 1,0 gebruiken uit andere talen, waaronder .NET, C, python en PHP. Onderdelen die zijn gebouwd met behulp van deze verschillende talen kunnen berichten veilig en met volledige kwaliteit uitwisselen met behulp van de AMQP 1,0-ondersteuning in Service Bus.

## <a name="next-steps"></a>Volgende stappen

Bekijk de onderstaande koppelingen voor meer informatie over Azure Service Bus en informatie over JMS-entiteiten (Java Message Service). 
* [Service Bus-wacht rijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java-berichten service-entiteiten](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Ondersteuning voor AMQP 1,0 in Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0-hand leiding voor ontwikkel aars](service-bus-amqp-dotnet.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)

