---
title: JMS-toepassingen (Java Message Service) migreren van ActiveMQ naar Azure Service Bus | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u bestaande JMS-toepassingen die communiceren met actief MQ kunt migreren om te communiceren met Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122341"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Bestaande JMS-toepassingen (Java Message Service 2,0) migreren van actieve MQ naar Azure Service Bus

Azure Service Bus ondersteunt Java/J2EE-en lente-workloads die gebruikmaken van de JMS-API (Java Message Service) 2,0 via het AMQP-Protocol (Advanced Message queueing Protocol).

In deze hand leiding wordt beschreven waar u rekening mee moet houden wanneer u een bestaande JMS-toepassing (Java Message Service 2,0) wilt wijzigen die samenwerkt met een JMS-Broker (met name Apache ActiveMQ of Amazon MQ) om te communiceren met Azure Service Bus.

## <a name="before-you-start"></a>Voordat u begint

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Verschillen tussen Azure Service Bus en Apache ActiveMQ

Azure Service Bus en Apache ActiveMQ zijn beide bericht Brokers die fungeren als JMS-providers voor client toepassingen om berichten te verzenden naar en berichten te ontvangen van. Ze bieden beide de punt-naar-punt semantiek met **wacht rijen** en publiceren en abonneren op semantiek met **onderwerpen** en **abonnementen**. 

Er zijn ook enkele verschillen tussen de twee.

| Categorie | Actieve MQ | Azure Service Bus |
| --- | --- | --- |
| Toepassings lagen | Geclusterde op | Twee lagen <br> (Gateway + back-end) |
| Protocolondersteuning | <ul> <li>AMQP</li> <li> STOMP </li> <li> Openkabel </li> </ul> | AMQP |
| Inrichtingsmodus | <ul> <li> IaaS (on-premises) </li> <li> Amazon MQ (beheerde PaaS) </li> | Beheerde PaaS |
| Berichtgrootte | Klant configureerbaar | 1 MB (Premium-laag) |
| Hoge beschikbaarheid | Door de klant beheerd | Platform beheerd |
| Herstel na noodgevallen | Door de klant beheerd | Platform beheerd | 

### <a name="current-supported-and-unsupported-features"></a>Huidige ondersteunde en niet-ondersteunde functies

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Waarschuwingen

De twee lagen van Azure Service Bus bieden verschillende mogelijkheden voor bedrijfs continuïteit (hoge Beschik baarheid en herstel na nood gevallen). Er zijn echter enkele aandachtspunten bij het gebruik van JMS-functies.

#### <a name="service-upgrades"></a>Service-upgrades

In het geval van service bus-upgrades en opnieuw starten, worden tijdelijke wacht rijen of onderwerpen verwijderd.

Als de toepassing gevoelig is voor gegevens verlies op tijdelijke wacht rijen of onderwerpen, wordt u aangeraden **geen** tijdelijke wacht rijen of onderwerpen te gebruiken en in plaats daarvan duurzame wacht rijen, onderwerpen en abonnementen te gebruiken.

#### <a name="data-migration"></a>Gegevensmigratie

Als onderdeel van het migreren/aanpassen van uw client toepassingen om met Azure Service Bus te communiceren, worden de gegevens die in ActiveMQ worden bewaard, niet gemigreerd naar Service Bus.

Er is mogelijk een aangepaste toepassing nodig om de ActiveMQ-wacht rijen, onderwerpen en abonnementen te verbreken en de berichten opnieuw af te spelen op Service Bus wacht rijen, onderwerpen en abonnementen.

#### <a name="authentication-and-authorization"></a>Verificatie en autorisatie

Op rollen gebaseerde Access Control (RBAC) die worden ondersteund door Azure Active Directory is het voorkeurs verificatie mechanisme voor Azure Service Bus.

Omdat RBAC echter niet wordt ondersteund vanwege een gebrek aan op claims gebaseerde verificatie ondersteuning door Apache QPID JMS.

Voor nu wordt verificatie alleen ondersteund met SAS-sleutels.

## <a name="pre-migration"></a>Voorafgaand aan de migratie

### <a name="version-check"></a>Versie controle

Hieronder vindt u de onderdelen die worden gebruikt tijdens het schrijven van de JMS-toepassingen en de specifieke versies die worden ondersteund. 

| Onderdelen | Versie |
|---|---|
| API voor Java-berichten service (JMS) | 1,1 of hoger |
| AMQP-Protocol | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Zorg ervoor dat de AMQP-poorten zijn geopend

Azure Service Bus ondersteunt communicatie via het AMQP-protocol. Voor dit doel moet communicatie via poorten 5671 (AMQP) en 443 (TCP) zijn ingeschakeld. Afhankelijk van waar de client toepassingen worden gehost, hebt u mogelijk een ondersteunings ticket nodig om communicatie via deze poorten mogelijk te maken.

> [!IMPORTANT]
> Azure Service Bus ondersteunt **alleen** AMQP 1,0-protocol.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Bedrijfs configuraties (VNET, firewall, persoonlijk eind punt, enzovoort) instellen

Azure Service Bus biedt verschillende functies voor beveiliging en hoge Beschik baarheid voor ondernemingen. Volg de onderstaande documentatie koppelingen voor meer informatie hierover.

  * [Service-eind punten Virtual Network](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Versleuteling aan de service zijde met de door de klant beheerde sleutel (BYOK)](configure-customer-managed-key.md)
  * [Privé-eind punten](private-link-service.md)
  * [Verificatie en autorisatie](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Bewaking, waarschuwingen en tracering

De metrische gegevens worden voor elke Service Bus naam ruimte gepubliceerd op Azure Monitor en kunnen worden gebruikt voor waarschuwingen en dynamische schaling van resources die zijn toegewezen aan de naam ruimte.

Lees meer over de verschillende metrische gegevens en hoe u waarschuwingen kunt instellen op [Service Bus metrische gegevens in azure monitor](service-bus-metrics-azure-monitor.md).

Lees ook meer informatie over [tracering aan de client zijde voor gegevens bewerkingen](service-bus-end-to-end-tracing.md) en [operationele/diagnostische logboek registratie voor beheer bewerkingen](service-bus-diagnostic-logs.md)

### <a name="metrics---newrelic"></a>Metrische gegevens-NewRelic

Hieronder vindt u een handige hand leiding over de metrische gegevens in de ActiveMQ, waarmee u de metrische gegevens in Azure Service Bus kunt toewijzen. Vanuit NewRelic wordt verwezen naar de onderstaande.

  * [ActiveMQ/Amazon MQ NewRelic-gegevens](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [NewRelic metrische gegevens Azure Service Bus](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Momenteel heeft NewRelic niet rechtstreeks een naadloze integratie met ActiveMQ, maar er zijn wel metrische gegevens beschikbaar voor Amazon MQ.
> Omdat Amazon MQ is afgeleid van ActiveMQ, wijst de onderstaande hand leiding de NewRelic-metrische gegevens van AmazonMQ toe aan Azure Service Bus.
>

|Metrische groepering| AmazonMQ/actieve MQ-metriek | Azure Service Bus metriek |
|------------|---------------------------|--------------------------|
|Connection|`CpuUtilization`|`CPUXNS`|
|Connection|`MemoryUsage`|`WSXNS`|
|Connection|`CurrentConnectionsCount`|`activeConnections`|
|Connection|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Connection|`InactiveDurableTopicSubscribersCount`|Metrische gegevens van abonnement benutten|
|Connection|`TotalMessageCount`|Maak gebruik van wachtrij/onderwerp/abonnements niveau`activeMessages`|
|Wachtrij/onderwerp|`EnqueueCount`|`incomingMessages`|
|Wachtrij/onderwerp|`DequeueCount`|`outgoingMessages`|
|Wachtrij|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migratie

Als u uw bestaande JMS 2,0-toepassing wilt migreren om te communiceren met Azure Service Bus, moet u de onderstaande stappen uitvoeren.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>Topologie exporteren vanuit ActiveMQ en de entiteiten in Azure Service Bus maken (optioneel)

Om ervoor te zorgen dat de client toepassingen probleemloos verbinding kunnen maken met Azure Service Bus, moet de topologie, met wacht rijen, onderwerpen en abonnementen, worden gemigreerd van **Apache ActiveMQ** naar **Azure service bus**.

> [!NOTE]
> Voor JMS-toepassingen (Java Message Service) is het maken van wacht rijen, onderwerpen en abonnementen een runtime-bewerking. De meeste JMS-providers (Java Message Service) bieden de functionaliteit voor het maken van *wacht rijen*, *onderwerpen* en *abonnementen* tijdens runtime.
>
> Daarom is de bovenstaande stap optioneel.
>
> Om ervoor te zorgen dat uw toepassing beschikt over de machtigingen voor het maken van de topologie tijdens runtime, moet u ervoor zorgen dat de connection string met SAS-machtigingen voor ***beheer*** worden gebruikt.

Functie 
  * Gebruik de [ActiveMQ-opdracht regel Programma's](https://activemq.apache.org/activemq-command-line-tools-reference) voor het exporteren van de topologie
  * Dezelfde topologie opnieuw maken met behulp van een [Azure Resource Manager sjabloon](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Voer de Azure Resource Manager-sjabloon uit.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>De Maven-afhankelijkheid voor Service Bus JMS-implementatie importeren

Om te zorgen voor naadloze connectiviteit met Azure Service Bus, moet het pakket ***Azure-servicebus-JMS*** worden toegevoegd als een afhankelijkheid van het maven- `pom.xml` bestand, zoals hieronder wordt beschreven.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Wijzigingen in de toepassings server configuratie

Dit onderdeel is aangepast aan de toepassings server die als host fungeert voor uw client toepassingen die verbinding maken met een actief MQ.

#### <a name="tomcat"></a>Tomcat

Hier beginnen we met de configuratie die specifiek is voor Active MQ, zoals wordt weer gegeven in het `/META-INF/context.xml` bestand.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

die kunnen worden aangepast als hieronder om te verwijzen naar Azure Service Bus

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Lente toepassingen

##### <a name="update-applicationproperties-file"></a>`application.properties`Bestand bijwerken

Als u een Spring boot-toepassing gebruikt om verbinding te maken met ActiveMQ.

Het doel is hier om de ActiveMQ-specifieke eigenschappen uit het bestand te ***verwijderen*** `application.properties` .

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

***Voeg*** vervolgens de service bus specifieke eigenschappen toe aan het `application.properties` bestand.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>De ActiveMQConnectionFactory vervangen door ServiceBusJmsConnectionFactory

De volgende stap is het vervangen van het exemplaar van ActiveMQConnectionFactory met de ServiceBusJmsConnectionFactory.

> [!NOTE] 
> De werkelijke code wijzigingen zijn specifiek voor de toepassing en de wijze waarop afhankelijkheden worden beheerd, maar het onderstaande voor beeld bevat de richt lijnen voor het wijzigen van ***wat*** er moet worden gewijzigd.
>

U kunt eerder een object van de ActiveMQConnectionFactory maken, zoals hieronder wordt beschreven.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Dit wordt gewijzigd in de instantie een object van de ServiceBusJmsConnectionFactory

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Na migratie

Nu u de toepassing hebt gewijzigd om berichten te verzenden en te ontvangen van Azure Service Bus, moet u controleren of deze werkt zoals verwacht. Zodra u klaar bent, kunt u door gaan met het verder verfijnen en moderniseren van uw toepassings stack.

## <a name="next-steps"></a>Volgende stappen

Maak gebruik van de [Spring boot starter voor Azure service bus JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) voor naadloze integratie met Azure service bus.

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging en Java Message Service (JMS):

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
