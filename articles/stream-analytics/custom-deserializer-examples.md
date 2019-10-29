---
title: .Net-deserialisatie gebruiken voor Azure stream Analytics-taken
description: In dit artikel worden de serialisatie-indeling en de interfaces beschreven die aangepaste .NET-deserialers definiëren voor Azure Stream Analytics Cloud-en Edge-taken.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 6c7d94761e4c5d5087dc8ac4e10854989a18f0fc
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992104"
---
# <a name="use-net-deserializers-for-azure-stream-analytics-jobs"></a>.Net-deserialisatie gebruiken voor Azure stream Analytics-taken

Met aangepaste .net-deserialisatie kan uw Azure stream Analytics-taak gegevens lezen uit indelingen buiten de drie [ingebouwde gegevens indelingen](stream-analytics-parsing-json.md). In dit artikel worden de serialisatie-indeling en de interfaces beschreven die aangepaste .NET-deserialers definiëren voor Azure Stream Analytics Cloud-en Edge-taken. Er zijn ook voor beelden van deserializers voor protocol buffer en CSV-indeling.

## <a name="custom-net-deserializer"></a>Aangepaste .NET-deserialer

De volgende code voorbeelden zijn de interfaces die de aangepaste deserializer definiëren en `StreamDeserializer<T>`implementeren.

`UserDefinedOperator` is de basis klasse voor alle aangepaste streaming-Opera tors. Er wordt geinitialiseerd `StreamingContext`, dat context bevat voor het publiceren van diagnostische gegevens waarvoor u fouten wilt opsporen in problemen met uw deserializer.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Het volgende code fragment is de deserialisatie voor het streamen van gegevens. 

Skippable-fouten moeten worden gegenereerd met `IStreamingDiagnostics` door gegeven via de initialisatie methode van `UserDefinedOperator`. Alle uitzonde ringen worden beschouwd als fouten en de deserializer wordt opnieuw gemaakt. Na een bepaald aantal fouten wordt de taak naar de status mislukt.

`StreamDeserializer<T>` deserialiseren van een stream naar een object van het type `T`. Aan de volgende voor waarden moet worden voldaan:

1. T is een klasse of een struct.
1. Alle open bare velden in T zijn
    1. Een van [Long, DateTime, String, Double] of hun null-equivalenten.
    1. Een andere struct of klasse volgens dezelfde regels.
    1. Matrix van het type `T2` die dezelfde regels volgen.
    1. IList`T2` waarbij T2 dezelfde regels volgt.
    1. Heeft geen recursieve typen.

De para meter `stream` is de stroom die het geserialiseerde object bevat. `Deserialize` retourneert een verzameling `T` exemplaren.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` biedt context met het mechanisme voor het publiceren van diagnostische gegevens voor de gebruikers operator.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` is de diagnostische gegevens voor door de gebruiker gedefinieerde Opera Tors, zoals serialisatiefunctie, deserializer en door de gebruiker gedefinieerde functies.

`WriteError` een fout bericht naar Diagnostische logboeken schrijft en de fout naar diagnostische gegevens verzendt.

`briefMessage` is een beknopt fout bericht. Dit bericht wordt weer gegeven in diagnostische gegevens en wordt door het product team gebruikt voor fout opsporing. Neem geen gevoelige informatie op en behoud het bericht van minder dan 200 tekens

`detailedMessage` is een gedetailleerd fout bericht dat alleen wordt toegevoegd aan de diagnostische Logboeken in uw opslag. Dit bericht moet kleiner zijn dan 2000 tekens.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Voor beelden van deserialiseren

In deze sectie wordt beschreven hoe u aangepaste deserializers schrijft voor protobuf en CSV. Ga naar [Azure stream Analytics op github](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)voor meer voor beelden.

### <a name="protocol-buffer-protobuf-format"></a>De protobuf-indeling (protocol buffer)

Dit is een voor beeld van protocol buffer indeling.

Neem de volgende protocol buffer definitie op.

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

Als u `protoc.exe` uitvoert vanuit de **Google. protobuf. tools** NuGet, wordt een. CS-bestand met de definitie gegenereerd. Het gegenereerde bestand wordt hier niet weer gegeven.

Het volgende code fragment is de implementatie van de deserializer, ervan uitgaande dat het gegenereerde bestand is opgenomen in het project. Deze implementatie is een smalle wrapper op het gegenereerde bestand.

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

### <a name="csv"></a>BESTAND

Het volgende code fragment is een eenvoudige CSV-deserializer die ook het door geven van fouten illustreert.

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

## <a name="serialization-format-for-rest-apis"></a>Serialisatie-indeling voor REST-Api's

Elke Stream Analytics invoer heeft een **serialisatie-indeling**. Zie de documentatie over [invoer rest API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) voor meer informatie over invoer opties.

De volgende Java script-code is een voor beeld van de serialisatie-indeling voor .NET-deserialisatie bij gebruik van de REST API:

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

`serializationClassName` moet een klasse zijn die `StreamDeserializer<T>`implementeert. Dit wordt beschreven in de volgende sectie.

## <a name="region-support"></a>Regio ondersteuning

Deze functie is beschikbaar in de volgende regio's:

* Europa - west
* VS - oost
* Europa - noord
* VS - west
* VS - oost 2
* VS - west-centraal

U kunt [ondersteuning aanvragen](https://aka.ms/ccodereqregion) voor extra regio's.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Wanneer is deze functie beschikbaar in alle Azure-regio's?

Deze functie is beschikbaar in 6 regio's (#region-ondersteuning). Als u deze functionaliteit in een andere regio wilt gebruiken, kunt u [een aanvraag indienen](https://aka.ms/ccodereqregion). Ondersteuning voor alle Azure-regio's bevindt zich op het schema.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Kan ik toegang krijgen tot MetadataPropertyValue van mijn invoer vergelijkbaar met de functie GetMetadataPropertyValue?

Deze functionaliteit wordt niet ondersteund. Als u deze mogelijkheid nodig hebt, kunt u voor deze aanvraag stemmen op [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="streamdeserializer-deserializes-a-stream-into-object-of-type-t-can-the-public-fields-in-t-be-any-supported-type-in-net"></a>StreamDeserializer deserialeert een stream naar een object van het type T. Kunnen de open bare velden in al het type worden ondersteund in .NET?

Ondersteuning voor alle ondersteunde typen in .NET vindt u op het schema.

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Kan ik mijn implementatie voor deserialisatie delen met de community zodat anderen kunnen profiteren?

Wanneer u uw deserializer hebt geïmplementeerd, kunt u anderen helpen door deze te delen met de community. Verzend uw code naar het [Azure stream Analytics github opslag plaats](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste .NET-deserialers voor Azure Stream Analytics Cloud taken](custom-deserializer.md)
