---
title: Invoer in elke indeling lezen met behulp van .NET custom deserializers in Azure Stream Analytics
description: In dit artikel worden de serialisatieindeling en de interfaces uitgelegd die aangepaste .NET-deserializers definiëren voor Azure Stream Analytics-cloud- en edge-taken.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845256"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Invoer in elke indeling lezen met behulp van .NET custom deserializers

Met aangepaste deserializers kan uw Azure Stream Analytics-taak gegevens lezen van indelingen buiten de drie [ingebouwde gegevensindelingen.](stream-analytics-parsing-json.md) In dit artikel worden de serialisatieindeling en de interfaces uitgelegd die aangepaste deserializers voor Azure Stream Analytics-cloud- en edge-taken definiëren. Er zijn ook voorbeelddeserializers voor Protocol buffer en CSV-indeling.

## <a name="net-custom-deserializer"></a>.NET custom deserializer

Volgende codevoorbeelden zijn de interfaces die de `StreamDeserializer<T>`aangepaste deserializer definiëren en implementeren.

`UserDefinedOperator`is de basisklasse voor alle aangepaste streaming operators. Het initialiseert `StreamingContext`, die context biedt die mechanisme voor het publiceren van diagnostiek waarvoor u nodig hebt om eventuele problemen met uw deserializer debuggen omvat.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Het volgende codefragment is de deserialisatie voor streaminggegevens. 

Skippable fouten moeten worden `IStreamingDiagnostics` uitgezonden `UserDefinedOperator`met behulp van doorgegeven via 's Initialiseren methode. Alle uitzonderingen worden als fouten behandeld en de deserializer wordt opnieuw gemaakt. Na een bepaald aantal fouten wordt de taak mislukt.

`StreamDeserializer<T>`deserialiseert een stroom in `T`object van type . Aan de volgende voorwaarden moet worden voldaan:

1. T is een klasse of een truc.
1. Alle openbare velden in T zijn ofwel
    1. Een van [sbyte, byte, kort, ushort, int, uint, lang, DateTime, string, float, double] of hun niet te doen equivalenten.
    1. Een andere struct of klasse volgens dezelfde regels.
    1. Array van `T2` het type dat dezelfde regels volgt.
    1. IList`T2` waar T2 dezelfde regels volgt.
    1. Heeft geen recursieve types.

De `stream` parameter is de stroom die het geserialiseerde object bevat. `Deserialize`retourneert `T` een verzameling exemplaren.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`biedt context die mechanisme voor het publiceren van diagnostiek voor de gebruiker operator omvat.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`is de diagnose voor door de gebruiker gedefinieerde operatoren, waaronder serialisator, deserializer en door de gebruiker gedefinieerde functies.

`WriteError`hiermee wordt een foutbericht naar diagnostische logboeken verzonden en wordt de fout naar diagnostische gegevens verzonden.

`briefMessage`is een korte foutmelding. Dit bericht wordt weergegeven in diagnostische gegevens en wordt door het productteam gebruikt voor foutopsporingsdoeleinden. Neem geen gevoelige informatie op en bewaar het bericht minder dan 200 tekens

`detailedMessage`is een gedetailleerd foutbericht dat alleen wordt toegevoegd aan uw diagnostische logboeken in uw opslag. Dit bericht moet minder dan 2000 tekens bevatten.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Voorbeelden van deserializer

In deze sectie ziet u hoe u aangepaste deserializers voor Protobuf en CSV schrijven. Ga voor aanvullende voorbeelden, zoals AVRO-indeling voor Event Hub Capture, naar [Azure Stream Analytics op GitHub.](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)

### <a name="protocol-buffer-protobuf-format"></a>Protocolbuffer (Protobuf) indeling

Dit is een voorbeeld met de protocolbufferindeling.

Ga uit van de volgende protocolbufferdefinitie.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

Hardlopen `protoc.exe` vanaf de **Google.Protobuf.Tools** NuGet genereert een .cs-bestand met de definitie. Het gegenereerde bestand wordt hier niet weergegeven.

Het volgende codefragment is de implementatie van deserializer, ervan uitgaande dat het gegenereerde bestand in het project is opgenomen. Deze implementatie is slechts een dunne wrapper over het gegenereerde bestand.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Het volgende codefragment is een eenvoudige CSV-deserializer die ook voortplantende fouten aantoont.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Serialisatie-indeling voor REST API's

Elke Stream Analytics-invoer heeft een **serialisatie-indeling.** Zie de [input REST API-documentatie](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) voor meer informatie over invoeropties.

De volgende Javascript-code is een voorbeeld van de serialisatie-indeling .NET-deserialisator bij het gebruik van de REST API:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName`moet een klasse die `StreamDeserializer<T>`implementeert . Dit wordt beschreven in de volgende sectie.

## <a name="region-support"></a>Ondersteuning voor regio's

Deze functie is beschikbaar in de volgende regio's:

* VS - west-centraal
* Europa - noord
* VS - oost
* VS - west
* VS - oost 2
* Europa -west

U [ondersteuning aanvragen](https://aka.ms/ccodereqregion) voor extra regio's.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Wanneer is deze functie beschikbaar in alle Azure-regio's?

Deze functie is beschikbaar in [6 regio's.](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support) Als u geïnteresseerd bent in het gebruik van deze functionaliteit in een andere regio, u [een aanvraag indienen.](https://aka.ms/ccodereqregion) Ondersteuning voor alle Azure-regio's staat op de roadmap.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Kan ik metadatapropertyvalue openen vanuit mijn ingangen vergelijkbaar met de functie GetMetadataPropertyValue?

Deze functionaliteit wordt niet ondersteund. Als je deze mogelijkheid nodig hebt, kun je stemmen voor dit verzoek op [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Kan ik mijn implementatie van deserializer delen met de community, zodat anderen hiervan kunnen profiteren?

Zodra u uw deserializer hebt geïmplementeerd, u anderen helpen door deze te delen met de community. Stuur uw code naar de [GitHub-repo van Azure Stream Analytics.](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)

## <a name="next-steps"></a>Volgende stappen

* [.NET custom deserializers for Azure Stream Analytics cloud jobs .NET custom deserializers for Azure Stream Analytics cloud jobs .NET custom deserializers for Azure Stream Analytics cloud jobs .NET](custom-deserializer.md)
