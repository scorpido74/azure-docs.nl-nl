---
title: Werken met grote gegevenssets
description: Meer informatie over het ophalen en beheren van grote gegevens sets tijdens het werken met Azure resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 4da890a5ef7acb44d0e8628dc4ec3904f6a065e4
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980335"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Werken met grote Azure-resource gegevens sets

Azure resource Graph is ontworpen voor het werken met en het verkrijgen van informatie over resources in uw Azure-omgeving. Met resource grafiek worden deze gegevens snel opgehaald, zelfs wanneer er een query wordt uitgevoerd op duizenden records. Resource grafiek biedt verschillende opties voor het werken met deze grote gegevens sets.

Zie [richt lijnen voor beperkte aanvragen](./guidance-for-throttled-requests.md)voor meer informatie over het werken met query's met een hoge frequentie.

## <a name="data-set-result-size"></a>Grootte van resultaat van gegevensset

Resource grafiek beperkt standaard elke query om alleen **100** records te retour neren. Met dit besturings element worden de gebruiker en de service beschermd tegen onbedoelde query's die zouden leiden tot grote gegevens sets. Deze gebeurtenis treedt meestal op als een klant experimenteert met query's om resources te zoeken en te filteren op de manier die aan hun specifieke behoeften voldoet. Dit besturings element verschilt van het gebruik van de [Top](/azure/kusto/query/topoperator) of het [beperken](/azure/kusto/query/limitoperator) van Azure Data Explorer taal operators om de resultaten te beperken.

> [!NOTE]
> Wanneer u de **eerste keer**gebruikt, is het raadzaam om de resultaten te rangschikken op ten minste één kolom met `asc` of `desc`. Zonder te sorteren, worden de geretourneerde resultaten wille keurig en niet herhaald.

De standaard limiet kan worden overschreven door alle methoden van interactie met de resource grafiek. In de volgende voor beelden ziet u hoe u de maximale grootte van de gegevensset wijzigt in _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

In de [rest API](/rest/api/azureresourcegraph/resources/resources)is het besturings element **$Top** en maakt het deel uit van **QueryRequestOptions**.

Het besturings element dat het _meest beperkend_ is, wint. Als uw query bijvoorbeeld gebruikmaakt van de Opera tors **Top** of **Limit** en vervolgens meer records zou opleveren dan **eerst**, zijn de geretourneerde maximum records dus gelijk aan het **eerst**. Als **Top** of **Limit** kleiner is dan **eerst**, zou de opgehaalde recordset de kleinere waarde zijn die wordt geconfigureerd met **boven** of **limiet**.

De **eerste** heeft momenteel een Maxi maal toegestane waarde van _5000_.

## <a name="skipping-records"></a>Records overs Laan

De volgende optie voor het werken met grote gegevens sets is het besturings element **overs Laan** . Met dit besturings element kan uw query overstappen of het gedefinieerde aantal records overs Laan voordat de resultaten worden geretourneerd. **Overs Laan** is handig voor query's waarbij het sorteren resulteert in een zinvolle manier, waarbij de bedoeling is om records ergens in het midden van de resultatenset op te halen. Als de resultaten aan het einde van de geretourneerde gegevensset worden weer gegeven, is het efficiënter om een andere Sorteer configuratie te gebruiken en de resultaten op te halen uit de bovenkant van de gegevensset in plaats daarvan.

> [!NOTE]
> Wanneer u **overs Laan**gebruikt, is het raadzaam om de resultaten te rangschikken op ten minste één kolom met `asc` of `desc`. Zonder te sorteren, worden de geretourneerde resultaten wille keurig en niet herhaald.

In de volgende voor beelden ziet u hoe u de eerste _tien_ records kunt overs Laan waarmee een query zou leiden, in plaats daarvan de geretourneerde resultatenset met de elfde record te starten:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

In de [rest API](/rest/api/azureresourcegraph/resources/resources)is het besturings element **$Skip** en maakt het deel uit van **QueryRequestOptions**.

## <a name="paging-results"></a>Resultaten pagineren

Wanneer een resultaatset moet worden opgesplitst in kleinere sets records voor verwerking of omdat een resultaatset de Maxi maal toegestane waarde van _1000_ geretourneerde records zou overschrijden, gebruikt u paginering. De [rest API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** biedt waarden om aan te geven dat een resultatenset is opgesplitst: **resultTruncated** en **$skipToken**.
**resultTruncated** is een Booleaanse waarde die de Consumer informeert als er geen aanvullende records worden geretourneerd in het antwoord. Deze voor waarde kan ook worden geïdentificeerd wanneer de eigenschap **Count** kleiner is dan de eigenschap **totalRecords** . **totalRecords** definieert het aantal records dat overeenkomt met de query.

Als **resultTruncated** is ingesteld op **True**, wordt de eigenschap **$skipToken** in de reactie. Deze waarde wordt gebruikt met dezelfde query-en abonnements waarden om de volgende set records op te halen die overeenkomen met de query.

In de volgende voor beelden ziet u hoe u de eerste 3000 records **overs laat** en de **eerste** 1000 records retourneert nadat deze zijn overgeslagen met Azure CLI en Azure PowerShell:

```azurecli-interactive
az graph query -q "project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> De query moet het veld **id** **projecteren** om de paginering te kunnen uitvoeren. Als deze ontbreekt in de query, bevat het antwoord niet de **$skipToken**.

Zie de [query volgende pagina](/rest/api/azureresourcegraph/resources/resources#next-page-query) in de documenten van de rest API voor een voor beeld.

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Start query's](../samples/starter.md).
- Zie Geavanceerd gebruik in [Geavanceerde query's](../samples/advanced.md).
- Meer informatie over [bronnen verkennen](explore-resources.md).