---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779944"
---
### <a name="event-system-properties-mapping"></a>Toewijzing gebeurtenis systeem eigenschappen

Als u **gebeurtenis systeem eigenschappen** hebt geselecteerd in de sectie **gegevens bron** van de bovenstaande tabel, gaat u naar de [Web-UI](https://dataexplorer.azure.com/) om de relevante KQL-opdracht uit te voeren om de juiste toewijzing te maken.

   **Voor CSV-toewijzing:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **Voor json-toewijzing:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * U moet alle geselecteerde eigenschappen in de toewijzing toevoegen. 
   > * De volg orde van eigenschappen is belang rijk in CSV-toewijzing. De systeem eigenschappen moeten worden vermeld vóór alle andere eigenschappen en in dezelfde volg orde waarin ze worden weer gegeven in de vervolg keuzelijst **gebeurtenis systeem eigenschappen** .