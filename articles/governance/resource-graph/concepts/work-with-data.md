---
title: Werken met grote gegevenssets
description: Meer informatie over het verzamelen, opmaken, pagina's en overslaan van records in grote gegevenssets tijdens het werken met Azure Resource Graph.
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064746"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Werken met grote Azure-brongegevenssets

Azure Resource Graph is ontworpen voor het werken met en het verkrijgen van informatie over resources in uw Azure-omgeving. Resource Graph maakt het snel verkrijgen van deze gegevens, zelfs bij het opvragen van duizenden records. Resource Graph heeft verschillende opties voor het werken met deze grote gegevenssets.

Zie [Richtlijnen voor aangezocht voor gethrottled aanvragen voor](./guidance-for-throttled-requests.md)richtlijnen voor het werken met query's op hoge frequentie.

## <a name="data-set-result-size"></a>Resultaatgrootte gegevensset

Resource Graph beperkt standaard elke query tot het retourneren van slechts **100** records. Dit besturingselement beschermt zowel de gebruiker als de service tegen onbedoelde query's die zouden resulteren in grote gegevenssets. Deze gebeurtenis gebeurt meestal als een klant experimenteert met query's om resources te vinden en te filteren op de manier die past bij hun specifieke behoeften. Dit besturingselement is anders dan het gebruik van de [taaloperatoren van](/azure/kusto/query/topoperator) [Azure](/azure/kusto/query/limitoperator) Data Explorer om de resultaten te beperken.

> [!NOTE]
> Bij het gebruik van **First**wordt aanbevolen om de resultaten `asc` `desc`te bestellen met ten minste één kolom met of . Zonder sorteren, de resultaten geretourneerd zijn willekeurig en niet herhaalbaar.

De standaardlimiet kan worden overschreven via alle methoden voor interactie met Resource Graph. In de volgende voorbeelden ziet u hoe u de limiet voor de grootte van de gegevensset wijzigen in _200:_

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

In de [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)wordt het besturingselement **$top** en maakt het deel uit van **QueryRequestOptions**.

De controle die _het meest beperkend_ is, zal winnen. Als uw query bijvoorbeeld de **hoogste** of **limietoperatoren** gebruikt en zou resulteren in meer records dan **Eerst,** zijn de maximaal geretourneerde records gelijk aan **First**. Als **boven** of **limiet** kleiner is dan **eerst,** is de geretourneerde recordset de kleinere waarde die is geconfigureerd op **boven-** of **limiet**.

**Eerst** heeft momenteel een maximaal toegestane waarde van _5000_.

## <a name="skipping-records"></a>Records overslaan

De volgende optie voor het werken met grote gegevenssets is het **besturingselement Overslaan.** Met dit besturingselement kan uw query over het gedefinieerde aantal records springen of overslaan voordat de resultaten worden retournerd. **Overslaan** is handig voor query's die resultaten sorteren op een zinvolle manier waarbij de intentie is om records ergens in het midden van de resultatenset te krijgen. Als de benodigde resultaten zich aan het einde van de geretourneerde gegevensset bevinden, is het efficiënter om een andere sorteerconfiguratie te gebruiken en in plaats daarvan de resultaten van de bovenkant van de gegevensset op te halen.

> [!NOTE]
> Bij het gebruik van **Overslaan**wordt aanbevolen om de resultaten `asc` `desc`te bestellen met ten minste één kolom met of . Zonder sorteren, de resultaten geretourneerd zijn willekeurig en niet herhaalbaar.

In de volgende voorbeelden ziet u hoe u de eerste _10_ records die een query zou overslaan, overslaan, in plaats van het geretourneerde resultaat te starten dat is ingesteld met de 11e record:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

In de [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)wordt het besturingselement **$skip** en maakt het deel uit van **QueryRequestOptions**.

## <a name="paging-results"></a>Resultaten pagineren

Wanneer het nodig is om een resultaat dat is ingesteld in kleinere sets records voor verwerking te verbreken of omdat een resultaatset de maximaal toegestane waarde van _1000_ geretourneerde records zou overschrijden, gebruikt u paging. De [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse** geeft waarden om aan te geven dat een resultatenset is opgesplitst: **resultTruncated** en **$skipToken**.
**resultaatAfcated** is een booleaanse waarde die de consument informeert als er aanvullende records zijn die niet zijn geretourneerd in het antwoord. Deze voorwaarde kan ook worden geïdentificeerd wanneer de **eigenschap aantal** minder is dan de **eigenschap totalRecords.** **totalRecords** definieert hoeveel records overeenkomen met de query.

 **resultaatAfgekapt** is **waar** wanneer paging is uitgeschakeld of niet `id` mogelijk is vanwege geen kolom of wanneer er minder resources beschikbaar zijn dan een query vraagt. Wanneer **resultaatAfcatcated** **waar**is, is de **eigenschap $skipToken** niet ingesteld.

In de volgende voorbeelden ziet u hoe u de eerste 3000 records **kunt overslaan** en de **eerste** 1000 records retourneren nadat deze records zijn overgeslagen met Azure CLI en Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> De query moet het **id-veld** **projecteren** om paginatie te laten werken. Als deze ontbreekt in de query, bevat het antwoord niet de **$skipToken**.

Zie Voorbeeld volgende [paginaquery](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) in de API-documenten van DE REST.

## <a name="formatting-results"></a>Opmaakresultaten

De resultaten van een resourcegrafiekquery worden weergegeven in twee _indelingen, Tabel_ en _ObjectArray_. De indeling is geconfigureerd met de parameter **resultFormat** als onderdeel van de aanvraagopties. De _tabelindeling_ is de standaardwaarde voor **resultaatopmaak**.

Resultaten van Azure CLI worden standaard weergegeven in JSON. Resultaten in Azure PowerShell zijn standaard een **PSCustomObject,** maar ze `ConvertTo-Json` kunnen snel worden geconverteerd naar JSON met behulp van de cmdlet. Voor andere SDK's kunnen de queryresultaten worden geconfigureerd om de _ObjectArray-indeling_ uit te geven.

### <a name="format---table"></a>Opmaak - Tabel

De standaardindeling, _Tabel_, retourneert resultaten in een JSON-indeling die is ontworpen om de kolomontwerp en rijwaarden van de eigenschappen die door de query worden geretourneerd, te markeren. Deze indeling lijkt sterk op gegevens zoals gedefinieerd in een gestructureerde tabel of spreadsheet met de kolommen die eerst worden geïdentificeerd en vervolgens elke rij die gegevens vertegenwoordigt die zijn uitgelijnd op die kolommen.

Hier vindt u een voorbeeld van een queryresultaat met de _opmaak tabel:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Opmaak - ObjectArray

De _objectarray-indeling_ retourneert ook resultaten in een JSON-indeling. Dit ontwerp wordt echter uitgelijnd met de relatie tussen sleutel/waardepaar die gebruikelijk is in JSON, waarbij de kolom en de rijgegevens worden gekoppeld aan matrixgroepen.

Hier vindt u een voorbeeld van een queryresultaat met de opmaak _ObjectArray:_

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Hier volgen enkele voorbeelden van het instellen van **resultaatIndeling** om de _objectarray-indeling_ te gebruiken:

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Starter-query's](../samples/starter.md).
- Zie geavanceerde toepassingen in [Geavanceerde query's](../samples/advanced.md).
- Meer informatie over het [verkennen van bronnen.](explore-resources.md)