---
author: baanders
description: include-bestand voor filter opties voor Azure Digital Apparaatdubbels-route
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902187"
---
| Bestandsnaam | Beschrijving | Filter tekst schema | Ondersteunde waarden | 
| --- | --- | --- | --- |
| Waar/onwaar | Hiermee kunt u een route maken zonder te filteren, of een route uitschakelen zodat er geen gebeurtenissen worden verzonden | `<true/false>` | `true`= route is ingeschakeld zonder filtering <br> `false`= route is uitgeschakeld |
| Type | Het [type gebeurtenis dat](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) door uw digitale dubbele instantie wordt doorlopend | `type = '<eventType>'` | Dit zijn de mogelijke gebeurtenis type waarden: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Bron | Naam van het Azure Digital Apparaatdubbels-exemplaar | `source = '<hostname>'`| Dit zijn de mogelijke hostnamen: <br> **Voor meldingen**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Voor telemetrie**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Onderwerp | Een beschrijving van de gebeurtenis in de context van de bovenstaande gebeurtenis bron | `subject = '<subject>'` | Dit zijn de mogelijke waarden voor het onderwerp: <br>**Voor meldingen**: het onderwerp is`<twinid>` <br> of een URI-indeling voor onderwerpen, die uniek worden geïdentificeerd door meerdere onderdelen of Id's:<br>`<twinid>/relationships/<relationshipid>`<br> **Voor telemetrie**: het onderwerp is het pad van het onderdeel (als de telemetrie wordt verzonden vanuit een twee ledig onderdeel), zoals `comp1.comp2` . Als de telemetrie niet vanuit een onderdeel wordt verzonden, is het veld onderwerp leeg. |
| Gegevens schema | DTDL-model-ID | `dataschema = '<model-dtmi-ID>'` | **Voor telemetrie**: het gegevens schema is de model-id van de dubbele of het onderdeel dat de telemetrie verzendt. Bijvoorbeeld: `dtmi:example:com:floor4;2` <br>**Voor meldingen**: gegevens schema wordt niet ondersteund|
| Inhoudstype | Inhouds type van gegevens waarde | `datacontenttype = '<contentType>'` | Het inhouds type is`application/json` |
| Specificatie versie | De versie van het gebeurtenis schema dat u gebruikt | `specversion = '<version>'` | De versie moet zijn `1.0` . Dit geeft de CloudEvents-schema versie 1,0 |

Het is ook mogelijk om filters te combi neren met behulp van de volgende bewerkingen:

| Bestandsnaam | Filter tekst schema | Voorbeeld | 
| --- | --- | --- |
| EN/OF | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| EN/OF | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Geneste bewerkingen | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> Tijdens de preview-periode wordt alleen gelijkheid van teken reeksen ondersteund (=,! =).

Wanneer u een filter implementeert of bijwerkt, kan het enkele minuten duren voordat de wijziging in de gegevens pijplijn wordt weer gegeven.
