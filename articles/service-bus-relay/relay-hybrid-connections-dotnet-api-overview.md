---
title: Overzicht van Azure Relay .NET Standard API's | Microsoft Documenten
description: In dit artikel wordt een overzicht gegeven van een overzicht van de a.s.-norm-api voor Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 18eaf2d2daae817107be6cdb0da9359bb5f9b4e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514532"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Overzicht van Azure Relay Hybrid Connections .NET Standard API

In dit artikel worden enkele van de belangrijkste Azure Relay Hybrid Connections .NET Standard [client API's](/dotnet/api/microsoft.azure.relay)samengevat.
  
## <a name="relay-connection-string-builder-class"></a>Tekenreeksbouwer relay- tekenreeks, klasse

De klasse [RelayConnectionStringBuilder][RelayConnectionStringBuilder] maakt verbindingstekenreeksen op die specifiek zijn voor hybride verbindingen doorgeven. U deze gebruiken om de indeling van een verbindingstekenreeks te verifiëren of om een verbindingstekenreeks helemaal opnieuw te maken. Zie de volgende code voor een voorbeeld:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

U ook een verbindingstekenreeks `RelayConnectionStringBuilder` rechtstreeks aan de methode doorgeven. Met deze bewerking u controleren of de verbindingstekenreeks een geldige indeling heeft. Als een van de parameters ongeldig is, genereert de constructor een `ArgumentException`.

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Hybride verbindingsstroom

De klasse [HybridConnectionStream][HCStream] is het primaire object dat wordt gebruikt om gegevens van een Azure Relay-eindpunt te verzenden en te ontvangen, of u nu werkt met een [HybridConnectionClient][HCClient]of een [HybridConnectionListener.][HCListener]

### <a name="getting-a-hybrid-connection-stream"></a>Een hybride verbindingsstroom krijgen

#### <a name="listener"></a>Listener

Met een object [HybridConnectionListener][HCListener] kunt `HybridConnectionStream` u een object als volgt verkrijgen:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client

Met een [object HybridConnectionClient][HCClient] kunt `HybridConnectionStream` u een object als volgt verkrijgen:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Gegevens ontvangen

De [klasse HybridConnectionStream][HCStream] maakt communicatie in twee richtingen mogelijk. In de meeste gevallen ontvangt u continu van de stream. Als u tekst uit de stream leest, u ook een [StreamReader-object](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) gebruiken, waardoor de gegevens gemakkelijker kunnen worden ontleed. U gegevens bijvoorbeeld lezen als tekst, in plaats van als `byte[]`.

De volgende code leest afzonderlijke regels tekst uit de stream totdat een annulering wordt aangevraagd:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Gegevens verzenden

Zodra u een verbinding hebt gemaakt, u een bericht verzenden naar het eindpunt van het relay. Omdat het verbindingsobject [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)overneemt, `byte[]`verzendt u uw gegevens als een . In het volgende voorbeeld ziet u hoe u dit doet:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Als u echter rechtstreeks tekst wilt verzenden, zonder dat u de tekenreeks `hybridConnectionStream` telkens hoeft te coderen, u het object omwikkelen met een [Object StreamWriter.](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx)

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over Azure Relay naar de volgende koppelingen:

* [Microsoft.Azure.Relay-verwijzing](/dotnet/api/microsoft.azure.relay)
* [Wat is Azure Relay?](relay-what-is-it.md)
* [Beschikbare Relay-API's](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener