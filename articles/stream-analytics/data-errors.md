---
title: Gegevensfouten diagnostische logboekgegevens van Azure Stream Analytics
description: In dit artikel worden de verschillende fouten in invoer- en uitvoergegevens uitgelegd die kunnen optreden bij het gebruik van Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 5457308d577b95201fa31bfad0a6634a7a79eda3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398135"
---
# <a name="azure-stream-analytics-data-errors"></a>Gegevensfouten in Azure Stream Analytics

Gegevensfouten zijn fouten die optreden tijdens het verwerken van de gegevens.  Deze fouten komen meestal voor tijdens gegevensdeserialisatie, serialisatie en schrijfbewerkingen.  Wanneer er gegevensfouten optreden, schrijft Stream Analytics gedetailleerde informatie en voorbeeldgebeurtenissen naar de diagnostische logboeken.  In sommige gevallen wordt deze informatie ook samengevat via portalmeldingen.

In dit artikel worden de verschillende fouttypen, oorzaken en diagnostische logboekgegevens beschreven voor invoer- en uitvoergegevensfouten.

## <a name="diagnostic-log-schema"></a>Schema voor diagnostische logboeken

Zie [Azure Stream Analytics oplossen door diagnostische logboeken](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) te gebruiken om het schema voor diagnostische logboeken weer te geven. De volgende JSON is een voorbeeldwaarde voor het veld **Eigenschappen** van een diagnostisch logboek voor een gegevensfout.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Fouten in invoergegevens

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Oorzaak: het geselecteerde type invoercompressie komt niet overeen met de gegevens.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: Berichten met eventuele deserialisatiefouten, waaronder ongeldig compressietype, worden uit de invoer verwijderd.
* Logboekgegevens
   * Invoerbericht-id. Voor gebeurtenishub is de id het partitionid-, verschuivings- en volgnummer.

**Foutbericht**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InvoerDeserializerError.InvalidHeader

* Oorzaak: de kop van invoergegevens is ongeldig. Een CSV heeft bijvoorbeeld kolommen met dubbele namen.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: Berichten met eventuele deserialisatiefouten, waaronder ongeldige koptekst, worden uit de invoer verwijderd.
* Logboekgegevens
   * Invoerbericht-id. 
   * Werkelijke payload tot enkele kilobytes.

**Foutbericht**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InvoerDeserializerError.MissingColumns

* Oorzaak: de invoerkolommen die zijn gedefinieerd met TABEL MAKEN of via TIMESTAMP BY, bestaan niet.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: gebeurtenissen met ontbrekende kolommen worden uit de invoer verwijderd.
* Logboekgegevens
   * Invoerbericht-id. 
   * Namen van de kolommen die ontbreken. 
   * Werkelijke payload tot een paar kilobytes.

**Foutberichten**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Oorzaak: kan de invoer niet converteren naar het type dat is opgegeven in de instructie TABEL MAKEN.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: gebeurtenissen met typeconversiefout worden uit de invoer verwijderd.
* Logboekgegevens
   * Invoerbericht-id. 
   * Naam van de kolom en verwacht type.

**Foutberichten**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InvoerDeserializerError.InvalidData

* Oorzaak: invoergegevens zijn niet in de juiste indeling. De invoer is bijvoorbeeld niet geldig JSON.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: Alle gebeurtenissen in het bericht nadat er een ongeldige gegevensfout is opgetreden, worden uit de invoer verwijderd.
* Logboekgegevens
   * Invoerbericht-id. 
   * Werkelijke payload tot enkele kilobytes.

**Foutberichten**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>Ongeldig invoertijdstempel

* Oorzaak: de waarde van de expressie TIMESTAMP BY kan niet worden geconverteerd naar datumtijd.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: gebeurtenissen met ongeldige invoertijdstempel worden uit de invoer verwijderd.
* Logboekgegevens
   * Invoerbericht-id. 
   * Foutbericht. 
   * Werkelijke payload tot enkele kilobytes.

**Foutbericht**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>Ongeldig invoertijdstempelsleutel

* Oorzaak: De waarde van TIMESTAMP BY OVER timestampColumn is NULL.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: gebeurtenissen met ongeldige invoertijdstempeltoets worden uit de invoer verwijderd.
* Logboekgegevens
   * De werkelijke payload tot enkele kilobytes.

**Foutbericht**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Oorzaak: Het verschil tussen de toepassingstijd en de aankomsttijd is groter dan het venster voor late aankomsttolerantie.
* Portal kennisgeving verstrekt: Nee
* Diagnostisch logboekniveau: informatie
* Impact: Late invoergebeurtenissen worden verwerkt volgens de instelling 'Andere gebeurtenissen afhandelen' in het gedeelte Gebeurtenisvolgorde van de taakconfiguratie. Zie Beleid [voor tijdafhandeling](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)voor meer informatie .
* Logboekgegevens
   * Aanvraagtijd en aankomsttijd. 
   * Werkelijke payload tot enkele kilobytes.

**Foutbericht**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Oorzaak: Het verschil tussen de toepassingstijd en de aankomsttijd is groter dan 5 minuten.
* Portal kennisgeving verstrekt: Nee
* Diagnostisch logboekniveau: informatie
* Impact: Vroege invoergebeurtenissen worden afgehandeld volgens de instelling 'Andere gebeurtenissen afhandelen' in het gedeelte Gebeurtenisvolgorde van de taakconfiguratie. Zie Beleid [voor tijdafhandeling](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)voor meer informatie .
* Logboekgegevens
   * Aanvraagtijd en aankomsttijd. 
   * Werkelijke payload tot enkele kilobytes.

**Foutbericht**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutoforderEvent

* Oorzaak: Gebeurtenis wordt als niet in orde beschouwd volgens het gedefinieerde venster out of ordertolerantie.
* Portal kennisgeving verstrekt: Nee
* Diagnostisch logboekniveau: informatie
* Impact: Gebeurtenissen buiten de volgorde worden afgehandeld volgens de instelling 'Andere gebeurtenissen afhandelen' in het gedeelte Gebeurtenisvolgorde van de taakconfiguratie. Zie Beleid [voor tijdafhandeling](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)voor meer informatie .
* Logboekgegevens
   * Werkelijke payload tot enkele kilobytes.

**Foutbericht**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Fouten in uitvoergegevens

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataconversieError.requiredColumnMissing

* Oorzaak: de kolom die nodig is voor de uitvoer bestaat niet. Een kolom die is gedefinieerd als Azure Table PartitionKey bestaat bijvoorbeeld niet.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: alle conversiefouten voor uitvoergegevens, inclusief ontbrekende vereiste kolom, worden verwerkt volgens de instelling [Uitvoergegevensbeleid.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logboekgegevens
   * Naam van de kolom en de record-id of een deel van de record.

**Foutbericht**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataconversieD.ColumnNameOngeldig

* Oorzaak: de kolomwaarde komt niet overeen met de uitvoer. De kolomnaam is bijvoorbeeld geen geldige Azure-tabelkolom.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: alle conversiefouten voor uitvoergegevens, inclusief ongeldige kolomnaam, worden verwerkt volgens de instelling [Uitvoergegevensbeleid.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logboekgegevens
   * Naam van de kolom en record-id of een deel van de record.

**Foutbericht**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataconversionError.typeconversionError

* Oorzaak: een kolom kan niet worden geconverteerd naar een geldig type in de uitvoer. De waarde van de kolom is bijvoorbeeld onverenigbaar met beperkingen of tekst die is gedefinieerd in de SQL-tabel.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: alle conversiefouten voor uitvoergegevens, inclusief typeconversiefouten, worden verwerkt volgens de instelling [Uitvoergegevensbeleid.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logboekgegevens
   * Naam van de kolom.
   * Record-id of een deel van de record.

**Foutbericht**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversieError.RecordExceededSizeLimit

* Oorzaak: de waarde van het bericht is groter dan de ondersteunde uitvoergrootte. Een record is bijvoorbeeld groter dan 1 MB voor een Gebeurtenishub-uitvoer.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: alle conversiefouten voor uitvoergegevens, inclusief recordoverschrijdingen, worden verwerkt volgens de instelling [Uitvoergegevensbeleid.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logboekgegevens
   * Record-id of een deel van de record.

**Foutbericht**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataconversieD.DuplicateKey

* Oorzaak: een record bevat al een kolom met dezelfde naam als een kolom systeem. CosmosDB-uitvoer met een kolom met de naam ID wanneer de id-kolom naar een andere kolom gaat.
* Portal melding verstrekt: Ja
* Diagnostisch logboekniveau: waarschuwing
* Impact: alle conversiefouten voor uitvoergegevens, inclusief dubbele sleutel, worden verwerkt volgens de instelling [Uitvoergegevensbeleid.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logboekgegevens
   * Naam van de kolom.
   * Record-id of een deel van de record.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics oplossen met diagnostische logboeken](stream-analytics-job-diagnostic-logs.md)

* [Inzicht in de taakbewaking van Stream Analytics en hoe u query's controleren](stream-analytics-monitoring.md)
