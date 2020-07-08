---
title: Aanstaande wijzigingen in de opname-en afvlakking regels in Azure Time Series Insights | Microsoft Docs
description: Wijzigingen in opname regel
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: 067244aa40256e3cc76239343790974bc3c06481
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85919032"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Aanstaande wijzigingen in de JSON afvlakking en Escape-regels voor nieuwe omgevingen

Deze wijzigingen worden alleen toegepast op *nieuwe* Azure time series Insights-omgevingen met betalen per gebruik (PAYG). Deze wijzigingen zijn niet van toepassing op de standaard (S) SKU-omgevingen.

Uw Azure Time Series Insights omgeving maakt dynamisch uw opslag kolommen volgens een bepaalde set naam conventies. Wanneer een gebeurtenis wordt opgenomen, wordt een set regels toegepast op de JSON-nettolading en eigenschaps namen. Wijzigingen in de manier waarop JSON-gegevens worden afgevlakt en opgeslagen, worden doorgevoerd voor nieuwe Azure Time Series Insights betalen per gebruik-omgevingen in juli 2020. Deze wijziging is van invloed op de volgende gevallen:

* Als uw JSON-nettolading geneste objecten bevat
*  Als uw JSON-nettolading matrices bevat
*  Als u een van de volgende vier speciale tekens in de naam van een JSON-eigenschap gebruikt: [\. '
*  Als een of meer van uw TS ID-eigenschappen binnen een genest object vallen.

Als u een nieuwe omgeving maakt en een of meer van de bovenstaande gevallen van toepassing zijn op uw gebeurtenis lading, worden uw gegevens afgevlakt en anders opgeslagen. Hieronder ziet u een overzicht van de wijzigingen:

| Huidige regel | Nieuwe regel | Voor beeld JSON | Vorige kolom naam | Nieuwe kolom naam
|---|---| ---| ---|  ---|
| Geneste JSON wordt afgevlakt met een onderstrepings teken als de afbakening |Geneste JSON wordt afgevlakt met een punt als de afbakening  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Speciale tekens worden niet met een escape teken | De namen van JSON-eigenschappen die de speciale tekens bevatten. [\ en ' worden voorafgegaan door [' en ']. Binnen [' en '] is er nog meer Escapes van enkele aanhalings tekens en backslashes. Er wordt één aanhalings teken geschreven alsof \' een back slash wordt geschreven als\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| Matrices van primitieven worden opgeslagen als een teken reeks | Matrices van primitieve typen worden opgeslagen als een dynamisch type  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Matrices met objecten worden altijd afgevlakt, waarbij meerdere gebeurtenissen worden geproduceerd | Als de objecten in een matrix geen eigen TS-ID of Time Stamp (s) hebben, wordt de matrix van objecten geheel opgeslagen als een dynamisch type | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Aanbevolen wijzigingen voor nieuwe omgevingen

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Als uw TS-ID en/of tijds tempel eigenschap binnen een object is genest:

*  Nieuwe implementaties moeten overeenkomen met de nieuwe opname regels. Als uw TS-ID bijvoorbeeld is, `telemetry_tagId` moet u alle arm-sjablonen of geautomatiseerde implementatie scripts bijwerken om te configureren `telemetry.tagId` als de omgevings-TS-ID. Deze wijziging is ook nodig voor de tijds tempels van gebeurtenis bronnen in geneste JSON.

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Als uw payload geneste JSON of speciale tekens bevat en u de variabelen expressies voor het maken van de [tijd reeks](.\time-series-insights-update-tsm.md) kunt automatiseren

*  Werk uw client code bij het uitvoeren van [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch#typesbatchput) uit zodat deze overeenkomt met de nieuwe opname regels. Een voor beeld: een vorige expressie voor een [tijd reeks](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) van `"value": {"tsx": "$event.series_value.Double"}` moet worden bijgewerkt naar een van de volgende opties.
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`



## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over het toevoegen van ondersteuning voor gegevens van het type Long](./time-series-insights-long-data-type.md).

- Lees [Azure time series Insights voor beeld-opslag en](./time-series-insights-update-storage-ingress.md)-inkomend verkeer.

