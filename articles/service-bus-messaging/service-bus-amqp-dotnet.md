---
title: Azure-servicebus met .NET en AMQP 1.0 | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Azure Service Bus gebruiken vanuit een .NET-toepassing met AMQP (Advanced Messaging Queuing Protocol).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8157efac5ff1fc135659a84b4f4825ff36307480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297654"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Servicebus van .NET gebruiken met AMQP 1.0

AMQP 1.0-ondersteuning is beschikbaar in het Service Bus-pakket versie 2.1 of hoger. U ervoor zorgen dat u de nieuwste versie hebt door de Service Bus-bits van [NuGet te][NuGet]downloaden.

## <a name="configure-net-applications-to-use-amqp-10"></a>.NET-toepassingen configureren om AMQP 1.0 te gebruiken

Standaard communiceert de servicebus .net-clientbibliotheek met de Service Bus-service via een speciaal SOAP-protocol. Als u AMQP 1.0 wilt gebruiken in plaats van het standaardprotocol, is een expliciete configuratie vereist op de verbindingstekenreeks ServiceBus, zoals beschreven in de volgende sectie. Anders dan deze wijziging blijft de toepassingscode ongewijzigd bij het gebruik van AMQP 1.0.

In de huidige versie zijn er een paar API-functies die niet worden ondersteund bij het gebruik van AMQP. Deze niet-ondersteunde functies worden vermeld in de sectie [Gedragsverschillen](#behavioral-differences). Sommige geavanceerde configuratie-instellingen hebben ook een andere betekenis bij het gebruik van AMQP.

### <a name="configuration-using-appconfig"></a>Configuratie met App.config

Het is een goede gewoonte voor toepassingen om het app.config-configuratiebestand te gebruiken om instellingen op te slaan. Voor Service Bus-toepassingen u App.config gebruiken om de tekenreeks servicebusverbinding op te slaan. Een voorbeeld van App.config is als volgt:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

De waarde `Microsoft.ServiceBus.ConnectionString` van de instelling is de verbindingstekenreeks ServiceBus die wordt gebruikt om de verbinding met ServiceBus te configureren. De indeling is als volgt:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Waar `namespace` `SAS key` en worden verkregen uit de [Azure-portal][Azure portal] wanneer u een servicebusnaamruimte maakt. Zie [Naamruimte van een servicebus maken met de Azure-portal][Create a Service Bus namespace using the Azure portal]voor meer informatie.

Wanneer u AMQP gebruikt, sluit `;TransportType=Amqp`u de verbindingstekenreeks toe aan . Deze notatie instrueert de clientbibliotheek om verbinding te maken met Service Bus met AMQP 1.0.

## <a name="message-serialization"></a>Berichtserialisatie

Bij het gebruik van het standaardprotocol is het standaard serialisatiegedrag van de .NET-clientbibliotheek het type [DataContractSerializer][DataContractSerializer] te gebruiken om een [instantie BrokeredMessage][BrokeredMessage] te serialiseren voor transport tussen de clientbibliotheek en de Service Bus-service. Bij het gebruik van de AMQP-transportmodus gebruikt de clientbibliotheek het AMQP-typesysteem voor serialisatie van het [brokered bericht][BrokeredMessage] in een AMQP-bericht. Met deze serialisatie kan het bericht worden ontvangen en geïnterpreteerd door een ontvangende toepassing die mogelijk op een ander platform wordt uitgevoerd, bijvoorbeeld een Java-toepassing die de JMS-API gebruikt om toegang te krijgen tot Service Bus.

Wanneer u een [instantie BrokeredMessage][BrokeredMessage] maakt, u een .NET-object als parameter aan de constructor opgeven om als hoofdtekst van het bericht te dienen. Voor objecten die kunnen worden toegewezen aan primitief typen AMQP, wordt het lichaam geserialiseerd in AMQP-gegevenstypen. Als het object niet direct kan worden toegewezen aan een AMQP-primitief type; dat wil zeggen, een aangepast type gedefinieerd door de toepassing, vervolgens wordt het object geserialiseerd met behulp van de [DataContractSerializer][DataContractSerializer]en worden de geserialiseerde bytes verzonden in een AMQP-gegevensbericht.

Gebruik alleen .NET-typen die rechtstreeks kunnen worden geserialiseerd in AMQP-typen voor de hoofdtekst van het bericht om de interoperabiliteit met non-.NET clients te vergemakkelijken. In de volgende tabel worden deze typen en de bijbehorende toewijzing aan het AMQP-typesysteem beschreven.

| Objecttype .NET-hoofdobject | Toegewezen AMQP-type | TYPE AMQP-hoofdsectie |
| --- | --- | --- |
| Booleaanse waarde |booleaans |AMQP-waarde |
| Byte |ubyte ubyte |AMQP-waarde |
| ushort ushort |ushort ushort |AMQP-waarde |
| uint |uint |AMQP-waarde |
| ulong |ulong |AMQP-waarde |
| sbyte |Byte |AMQP-waarde |
| Korte |Korte |AMQP-waarde |
| int |int |AMQP-waarde |
| long |long |AMQP-waarde |
| float |float |AMQP-waarde |
| double |double |AMQP-waarde |
| decimal |decimal128 |AMQP-waarde |
| Char |Char |AMQP-waarde |
| DateTime |tijdstempel |AMQP-waarde |
| GUID |uuid |AMQP-waarde |
| byte[] |binair |AMQP-waarde |
| tekenreeks |tekenreeks |AMQP-waarde |
| System.Collections.iList |list |AMQP-waarde: items in de verzameling kunnen alleen items zijn die in deze tabel zijn gedefinieerd. |
| System.Array |matrix |AMQP-waarde: items in de verzameling kunnen alleen items zijn die in deze tabel zijn gedefinieerd. |
| System.Collections.IDictionary |map |AMQP-waarde: items in de verzameling kunnen alleen items zijn die in deze tabel zijn gedefinieerd. Opmerking: alleen tekenreekstoetsen worden ondersteund. |
| Uri |Beschreven tekenreeks (zie de volgende tabel) |AMQP-waarde |
| Datumtijdverschuiving |Lang beschreven (zie de volgende tabel) |AMQP-waarde |
| TimeSpan |Lang beschreven(zie het volgende) |AMQP-waarde |
| Streamen |binair |AMQP-gegevens (kan meerdere zijn). De secties Gegevens bevatten de ruwe bytes die worden gelezen vanuit het object Stream. |
| Ander object |binair |AMQP-gegevens (kan meerdere zijn). Bevat de geserialiseerde binaire van het object dat de DataContractSerializer of een serialisator geleverd door de toepassing gebruikt. |

| .NET-type | Beschreven AMQP-type in kaart gebracht | Opmerkingen |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.Absoluteuri Uri.Absoluteuri |
| Datumtijdverschuiving |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks TimeSpan.Ticks TimeSpan.Ticks TimeSpan |

## <a name="behavioral-differences"></a>Gedragsverschillen

Er zijn enkele kleine verschillen in het gedrag van de Service Bus .NET API bij het gebruik van AMQP, in vergelijking met het standaardprotocol:

* De eigenschap [OperationTimeout][OperationTimeout] wordt genegeerd.
* `MessageReceiver.Receive(TimeSpan.Zero)`wordt uitgevoerd `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`als .
* Het invullen van berichten door vergrendeltokens kan alleen worden gedaan door de berichtontvangers die de berichten in eerste instantie hebben ontvangen.

## <a name="control-amqp-protocol-settings"></a>AmQP-protocolinstellingen beheren

De [.NET API's](/dotnet/api/) stellen verschillende instellingen bloot om het gedrag van het AMQP-protocol te beheren:

* **[MessageReceiver.PrefetchCount:](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)** hiermee wordt het eerste tegoed gecontroleerd dat op een koppeling wordt toegepast. De standaardwaarde is 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize:](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)** Hiermee bepaalt u de maximale AMQP-framegrootte die tijdens de onderhandeling wordt aangeboden tijdens de open tijd van de verbinding. De standaardwaarde is 65.536 bytes.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval:](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)** Als overdrachten batchable zijn, bepaalt deze waarde de maximale vertraging voor het verzenden van disposities. Standaard overgenomen door afzenders/ontvangers. De afzonderlijke afzender/ontvanger kan de standaardwaarde, die 20 milliseconden bedraagt, overschrijven.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity:](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)** Bepaalt of AMQP-verbindingen via een TLS-verbinding tot stand worden gebracht. De standaardinstelling is **waar.**

## <a name="next-steps"></a>Volgende stappen

Wilt u meer weten? Ga naar de volgende links:

* [Overzicht servicebus AMQP]
* [AMQP 1.0-protocolhandleiding]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Overzicht servicebus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protocolhandleiding]: service-bus-amqp-protocol-guide.md

