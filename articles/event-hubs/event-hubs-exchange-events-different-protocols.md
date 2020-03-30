---
title: Azure Event Hubs - Exchange-gebeurtenissen met verschillende protocollen
description: In dit artikel ziet u hoe consumenten en producenten die verschillende protocollen gebruiken (AMQP, Apache Kafka en HTTPS) gebeurtenissen kunnen uitwisselen wanneer ze Azure Event Hubs gebruiken.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 368cc568c40e878338e6b45205e74cba1d0b6378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372217"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Exchange-evenementen tussen consumenten en producenten die verschillende protocollen gebruiken: AMQP, Kafka en HTTPS
Azure Event Hubs ondersteunt drie protocollen voor consumenten en producenten: AMQP, Kafka en HTTPS. Elk van deze protocollen heeft zijn eigen manier om een bericht te vertegenwoordigen, dus natuurlijk rijst de volgende vraag: als een toepassing gebeurtenissen naar een Event Hub met één protocol stuurt en ze verbruikt met een ander protocol, wat doen de verschillende onderdelen en waarden van de evenement eruit zien wanneer ze aankomen bij de consument? In dit artikel worden best practices voor zowel producent als consument besproken om ervoor te zorgen dat de waarden binnen een gebeurtenis correct worden geïnterpreteerd door de verbruikende toepassing.

Het advies in dit artikel heeft specifiek betrekking op deze klanten, met de genoemde versies die worden gebruikt bij het ontwikkelen van de code fragmenten:

* Kafka Java client (versie 1.1.1 vanhttps://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs Client voor Java (versie 1.1.0 vanafhttps://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs Client voor .NET (versie 2.1.0 vanafhttps://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (versie 5.0.0 vanafhttps://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (ondersteunt alleen producenten)

Andere AMQP-clients kunnen zich iets anders gedragen. AMQP heeft een goed gedefinieerd typesysteem, maar de specifieke kenmerken van het serialiseren van taalspecifieke typen van en naar dat typesysteem zijn afhankelijk van de client, evenals de manier waarop de client toegang biedt tot de delen van een AMQP-bericht.

## <a name="event-body"></a>Gebeurtenisbody
Alle Microsoft AMQP-clients vertegenwoordigen de gebeurtenisinstantie als een niet-geïnterpreteerde zak bytes. Een producerende toepassing geeft een reeks bytes door aan de client en een verbruikende toepassing ontvangt dezelfde reeks van de client. De interpretatie van byte sequentie gebeurt binnen de toepassingscode.

Bij het verzenden van een gebeurtenis via HTTPS is de gebeurtenisbody de POSTed-inhoud, die ook wordt behandeld als niet-geïnterpreteerde bytes. Het is gemakkelijk om dezelfde status te bereiken in een Kafka-producent of -consument met behulp van de meegeleverde ByteArraySerializer en ByteArrayDeserializer, zoals weergegeven in de volgende code:

### <a name="kafka-byte-producer"></a>Kafka byte[] producent

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte[] consument
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Deze code creëert een transparante bytepijplijn tussen de twee helften van de toepassing en stelt de ontwikkelaar van de toepassing in staat om handmatig te serialiseren en te deserialiseren op elke gewenste manier, inclusief het maken van deserialisatiebeslissingen tijdens runtime, bijvoorbeeld op basis van type of afzendergegevens in gebruikersseteigenschappen op de gebeurtenis.

Toepassingen met één vaste gebeurtenisbodytype kunnen mogelijk andere Kafka-serialisators gebruiken en deserialisatoren gebruiken om gegevens transparant om te zetten. Denk bijvoorbeeld aan een toepassing die JSON gebruikt. De constructie en interpretatie van de JSON-tekenreeks gebeurt op toepassingsniveau. Op het niveau Van Gebeurtenishubs is de gebeurtenislichaam altijd een tekenreeks, een opeenvolging van bytes die tekens in de utf-8-codering vertegenwoordigen. In dit geval kan de Kafka-producent of -consument profiteren van de meegeleverde StringSerializer of StringDeserializer zoals weergegeven in de volgende code:

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 snaarproducent
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 snaarconsument
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Voor de AMQP-kant bieden zowel Java als .NET ingebouwde manieren om tekenreeksen om te zetten van en naar utf-8 byte-sequenties. De Microsoft AMQP-clients vertegenwoordigen gebeurtenissen als een klasse met de naam EventData. In de volgende voorbeelden ziet u hoe u een UTF-8-tekenreeks serialiseren in een EventData-gebeurtenisinstantie in een AMQP-producent en hoe u een Gebeurtenisbody van EventData deserialiseren tot een UTF-8-tekenreeks in een AMQP-consument.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 snaarproducent
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 snaarconsument
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 snaarproducent
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 snaarconsument
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Omdat Kafka open-source is, kan de applicatieontwikkelaar de implementatie van elke serializer of deserializer inspecteren en code implementeren, die een compatibele reeks bytes aan de AMQP-kant produceert of verbruikt.

## <a name="event-user-properties"></a>Gebruikerseigenschappen van gebeurtenissen

Eigenschappen van gebruikerssets kunnen worden ingesteld en opgehaald bij beide AMQP-clients (in de Microsoft AMQP-clients worden ze eigenschappen genoemd) en Kafka (waar ze headers worden genoemd). HTTPS-afzenders kunnen gebruikerseigenschappen voor een gebeurtenis instellen door ze te leveren als HTTP-headers in de post-bewerking. Kafka behandelt echter zowel gebeurtenisinstanties als gebeurteniskopwaarden als bytereeksen. Terwijl in AMQP-clients eigenschapswaarden typen hebben, die worden gecommuniceerd door de eigenschapswaarden te coderen volgens het AMQP-typesysteem.

HTTPS is een speciaal geval. Op het moment van verzenden zijn alle eigenschapswaarden UTF-8-tekst. De gebeurtenishubs-service geeft een beperkte hoeveelheid interpretatie om de juiste eigenschapswaarden om te zetten in AMQP-gecodeerde 32-bits en 64-bits ondertekende gehele getallen, 64-bits zwevende puntnummers en booleaans. Elke eigenschapswaarde, die niet in een van deze typen past, wordt behandeld als een tekenreeks.

Het mengen van deze benaderingen voor het typen van eigendommen betekent dat een Kafka-consument de ruwe AMQP-gecodeerde byte-sequentie ziet, inclusief de AMQP-type-informatie. Terwijl een AMQP-consument de ongetypte bytesequentie ziet die door de Kafka-producent wordt verzonden en die de aanvraag moet interpreteren.

Voor Kafka-consumenten die eigenschappen van AMQP- of HTTPS-producenten ontvangen, gebruikt u de klasse AmqpDeserializer, die is gemodelleerd naar de andere deserializers in het Kafka-ecosysteem. Het interpreteert de type-informatie in de AMQP-gecodeerde bytesequenties om de gegevensbytes te deserialiseren tot een Java-type.

Als een aanbevolen praktijk raden we u aan een eigenschap op te nemen in berichten die via AMQP of HTTPS worden verzonden. De Kafka-consument kan deze gebruiken om te bepalen of kopwaarden AMQP-deserialisatie nodig hebben. De waarde van het pand is niet belangrijk. Het heeft alleen een bekende naam nodig die de Kafka-consument kan vinden in de lijst met headers en zijn gedrag dienovereenkomstig kan aanpassen.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP naar Kafka deel 1: een gebeurtenis maken en verzenden in C# (.NET) met eigenschappen
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP naar Kafka deel 2: gebruik AmqpDeserializer om die eigenschappen te deserialiseren in een Kafka-consument
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Als de toepassing het verwachte type voor een eigenschap kent, zijn er deserialisatiemethoden die achteraf geen cast vereisen, maar ze gooien een fout als de eigenschap niet van het verwachte type is.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP naar Kafka deel 3: een andere manier van amqpdeserializer gebruiken in een Kafka-consument
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

De andere richting gaan is meer betrokken, omdat headers ingesteld door een Kafka-producent altijd worden gezien door een AMQP-consument als raw bytes (type org.apache.qpid.proton.amqp.Binary voor de Microsoft Azure Event Hubs Client voor Java, of `System.Byte[]` voor Microsoft's .NET AMQP clients). Het gemakkelijkste pad is om een van de door Kafka geleverde serialisators te gebruiken om de bytes voor de headerwaarden aan de Kafka-producentenzijde te genereren en vervolgens een compatibele deserialisatiecode te schrijven aan de consumentenkant van AMQP.

Net als bij AMQP-to-Kafka, de beste praktijk die we aanbevelen is om een eigenschap op te nemen in berichten die via Kafka worden verzonden. De AMQP-consument kan de eigenschap gebruiken om te bepalen of kopwaarden deserialisatie nodig hebben. De waarde van het pand is niet belangrijk. Het heeft alleen een bekende naam nodig die de AMQP-consument kan vinden in de lijst met headers en zijn gedrag dienovereenkomstig kan aanpassen. Als de Kafka-producent niet kan worden gewijzigd, is het ook mogelijk voor de verbruikende toepassing om te controleren of de eigenschapswaarde van een binair of bytetype is en poging deserialisatie op basis van het type.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka naar AMQP deel 1: maak en stuur een evenement van Kafka met eigenschappen
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka naar AMQP deel 2: deserialiseren van deze eigenschappen in C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka naar AMQP deel 3: handmatig deserialiseren die eigenschappen in Java
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Volgende stappen
In dit artikel heb je geleerd hoe je streamen naar Event Hubs zonder je protocolclients te wijzigen of je eigen clusters uit te voeren. Zie de volgende artikelen voor meer informatie over gebeurtenishubs en gebeurtenishubs voor Kafka:  

* [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
* [Meer informatie over Event Hubs for Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Meer voorbeelden van de Event Hubs for Kafka-GitHub verkennen](https://github.com/Azure/azure-event-hubs-for-kafka)
* Gebruik [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) om [gebeurtenissen van Kafka on-premises naar Event Hubs in de cloud te streamen.](event-hubs-kafka-mirror-maker-tutorial.md)
* Meer informatie over streamen naar Event Hubs met [behulp van native Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md)of [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
