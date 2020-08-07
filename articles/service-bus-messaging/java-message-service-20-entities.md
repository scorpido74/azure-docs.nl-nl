---
title: Azure Service Bus berichten-service-entiteiten voor Java-berichten (preview-versie)
description: Dit artikel bevat een overzicht van Azure Service Bus Messa ging-entiteiten die toegankelijk zijn via de API voor Java-berichten service.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801582"
---
# <a name="java-message-service-jms-20-entities-preview"></a>JMS (Java Message Service) 2,0-entiteiten (preview-versie)

Client toepassingen die verbinding maken met Azure Service Bus Premium en gebruikmaken van de [Azure service bus JMS-bibliotheek](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) kunnen gebruikmaken van de onderstaande entiteiten.

## <a name="queues"></a>Wachtrijen

Wacht rijen in JMS zijn semantisch vergelijkbaar met de traditionele [service bus-wacht rijen](service-bus-queues-topics-subscriptions.md#queues).

Als u een wachtrij wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` -klasse-

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Onderwerpen

De onderwerpen in JMS zijn semantisch vergelijkbaar met de traditionele [Service Bus-onderwerpen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Gebruik de onderstaande methoden in de-klasse om een onderwerp te maken `JMSContext` :

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Tijdelijke wacht rijen

Wanneer een client toepassing een tijdelijke entiteit vereist die voor de levens duur van de toepassing bestaat, kan deze gebruikmaken van tijdelijke wacht rijen. Deze worden gebruikt in het antwoord op de [aanvraag](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Als u een tijdelijke wachtrij wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` -klasse-

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tijdelijke onderwerpen

Net als tijdelijke wacht rijen bestaan er tijdelijke onderwerpen om publiceren/abonneren mogelijk te maken via een tijdelijke entiteit die bestaat voor de levens duur van de toepassing.

Als u een tijdelijk onderwerp wilt maken, gebruikt u de onderstaande methoden in de `JMSContext` klasse-

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>JMS-abonnementen (Java Message Service)

Hoewel dit in semantische richting vergelijkbaar is met de [abonnementen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (op een onderwerp en voor het publiceren/abonneren van semantiek), introduceert de Java-bericht service specificatie de concepten van **gedeelde**, niet- **gedeelde**, **duurzame** en **niet-duurzame** kenmerken van een bepaald abonnement.

> [!NOTE]
> De onderstaande abonnementen zijn beschikbaar in Azure Service Bus Premium-laag voor Preview-versies van client toepassingen die verbinding maken met Azure Service Bus met behulp van de [Azure service bus JMS-bibliotheek](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Voor de open bare Preview kunnen deze abonnementen niet worden gemaakt met behulp van de Azure Portal.
>

### <a name="shared-durable-subscriptions"></a>Gedeelde duurzame abonnementen

Er wordt een gedeeld duurzaam abonnement gebruikt wanneer alle berichten die op een onderwerp worden gepubliceerd, moeten worden ontvangen en verwerkt door een toepassing, ongeacht of de toepassing altijd actief verbruikt van het abonnement.

Aangezien dit een gedeeld abonnement is, kan elke toepassing die is geverifieerd om te ontvangen van Service Bus, worden ontvangen van het abonnement.

Als u een gedeeld duurzaam abonnement wilt maken, gebruikt u de onderstaande methoden voor de `JMSContext` klasse-

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

Het gedeelde duurzame abonnement blijft bestaan, tenzij u dit verwijdert met de `unsubscribe` methode voor de `JMSContext` klasse.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Niet-gedeelde duurzame abonnementen

Net als bij een gedeeld duurzaam abonnement wordt een niet-gedeeld duurzaam abonnement gebruikt wanneer alle berichten die op een onderwerp worden gepubliceerd, moeten worden ontvangen en verwerkt door een toepassing, ongeacht of de toepassing op elk moment actief gebruikmaakt van het abonnement.

Omdat dit echter een niet-gedeeld abonnement is, kan alleen de toepassing die het abonnement heeft gemaakt hiervan worden ontvangen.

Als u een niet-gedeeld duurzaam abonnement wilt maken, gebruikt u de onderstaande methoden van `JMSContext` klasse- 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> De `noLocal` functie wordt momenteel niet ondersteund en wordt genegeerd.
>

Het niet-gedeelde duurzame abonnement blijft bestaan, tenzij u dit verwijdert met de `unsubscribe` methode voor de `JMSContext` klasse.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Gedeelde niet-duurzame abonnementen

Een gedeeld niet-duurzaam abonnement wordt gebruikt wanneer meerdere client toepassingen berichten van een enkel abonnement moeten ontvangen en verwerken, alleen tot ze er actief op worden verbruikt/ontvangen.

Omdat het abonnement niet duurzaam is, wordt het niet bewaard. Berichten worden niet ontvangen door dit abonnement als er geen actieve gebruikers zijn.

Als u een gedeeld niet-duurzaam abonnement wilt maken, maakt u een `JmsConsumer` zoals wordt weer gegeven in de onderstaande methoden van de `JMSContext` klasse-

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

Het gedeelde niet-duurzame abonnement blijft bestaan tot er actieve consumers worden ontvangen.

### <a name="unshared-non-durable-subscriptions"></a>Niet-verdeelde niet-duurzame abonnementen

Er wordt een niet-verwisselbaar niet-duurzaam abonnement gebruikt wanneer de client toepassing een bericht van een abonnement moet ontvangen en verwerken, alleen tot het systeem actief is. Er kan slechts één consument bestaan in dit abonnement, dat wil zeggen de client die het abonnement heeft gemaakt.

Omdat het abonnement niet duurzaam is, wordt het niet bewaard. Berichten worden niet ontvangen door dit abonnement als er geen actieve consument op is.

Als u een niet-duurzaam niet-gedeeld abonnement wilt maken, maakt u een `JMSConsumer` zoals wordt weer gegeven in de onderstaande methoden van de `JMSContext` klasse-

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> De `noLocal` functie wordt momenteel niet ondersteund en wordt genegeerd.
>

Het niet-duurzame niet-duurzaam abonnement blijft bestaan totdat er een actieve Consumer is die er een ontvangt.

### <a name="message-selectors"></a>Bericht kiezer

Net zoals **filters en acties** bestaan voor gewone service bus abonnementen, bestaan er **bericht selectieers** voor JMS-abonnementen.

Bericht selectie vakjes kunnen worden ingesteld voor elk van de JMS-abonnementen en bestaan als filter voorwaarde voor de eigenschappen van de bericht header. Alleen berichten met header-eigenschappen die overeenkomen met de bericht kiezer-expressie worden geleverd. Een waarde van null of een lege teken reeks geeft aan dat er geen bericht kiezer is voor het JMS-abonnement/de gebruiker.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende geavanceerde onderwerpen voor meer informatie en voor beelden van het gebruik van Service Bus Messa ging:

* [Overzicht van Service Bus berichten](service-bus-messaging-overview.md)
* [De API voor de Java-berichten Service 2,0 gebruiken met Azure Service Bus Premium (preview)](how-to-use-java-message-service-20.md)



