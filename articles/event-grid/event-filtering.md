---
title: Gebeurtenis filtering voor Azure Event Grid
description: Hierin wordt beschreven hoe u gebeurtenissen filtert bij het maken van een Azure Event Grid-abonnement.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: f9fca0a9fefb5959747a4492139ae422a118db02
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390181"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Gebeurtenis filters begrijpen voor Event Grid-abonnementen

In dit artikel worden de verschillende manieren beschreven waarop u kunt filteren welke gebeurtenissen naar uw eind punt worden verzonden. Wanneer u een gebeurtenis abonnement maakt, hebt u drie opties voor het filteren:

* Gebeurtenis typen
* Onderwerp begint met of eindigt met
* Geavanceerde velden en Opera tors

## <a name="event-type-filtering"></a>Filter gebeurtenis type

Standaard worden alle [gebeurtenis typen](event-schema.md) voor de gebeurtenis bron naar het eind punt verzonden. U kunt ervoor kiezen om alleen bepaalde gebeurtenis typen naar uw eind punt te verzenden. U kunt bijvoorbeeld een melding ontvangen over updates voor uw resources, maar niet op de hoogte worden gesteld van andere bewerkingen, zoals verwijderingen. In dat geval filtert u op `Microsoft.Resources.ResourceWriteSuccess` het gebeurtenis type. Geef een matrix met de gebeurtenis typen op, of `All` Geef op om alle gebeurtenis typen voor de gebeurtenis bron op te halen.

De JSON-syntaxis voor filteren op gebeurtenis type is:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filteren op onderwerp

Geef voor eenvoudig filteren op onderwerp een begin-of eind waarde voor het onderwerp op. U kunt bijvoorbeeld opgeven dat het onderwerp eindigt met `.txt` om alleen gebeurtenissen op te halen met betrekking tot het uploaden van een tekst bestand naar een opslag account. U kunt ook filteren of het onderwerp begint met `/blobServices/default/containers/testcontainer` om alle gebeurtenissen voor die container op te halen, maar niet andere containers in het opslag account.

Bij het publiceren van gebeurtenissen naar aangepaste onderwerpen maakt u onderwerpen voor uw evenementen waarmee abonnees eenvoudig kunnen zien of ze geïnteresseerd zijn in de gebeurtenis. Abonnees gebruiken de eigenschap Subject om gebeurtenissen te filteren en te routeren. Overweeg het pad toe te voegen voor waar de gebeurtenis zich voordeed, zodat abonnees kunnen filteren op segmenten van dat pad. Met het pad kunnen abonnees de gebeurtenissen op een smalle of brede manier filteren. Als u een drie segment pad opgeeft, `/A/B/C` zoals in het onderwerp, kunnen abonnees filteren op het eerste `/A` segment om een grote set gebeurtenissen te verkrijgen. Deze abonnees ontvangen gebeurtenissen met onderwerpen als `/A/B/C` of `/A/D/E`. Andere abonnees kunnen filteren `/A/B` op om een smalle set gebeurtenissen te verkrijgen.

De JSON-syntaxis voor filteren op onderwerp is:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Geavanceerd filteren

Als u wilt filteren op waarden in de gegevens velden en de vergelijkings operator wilt opgeven, gebruikt u de geavanceerde filter optie. In geavanceerde filtering geeft u het volgende op:

* operator type: het type vergelijking.
* sleutel: het veld in de gebeurtenis gegevens die u gebruikt voor het filteren van. Dit kan een getal, een Booleaanse waarde of een teken reeks zijn.
* waarde of waarden: de waarde of waarden die moeten worden vergeleken met de sleutel.

Als u één filter met meerdere waarden opgeeft, wordt een **of** -bewerking uitgevoerd, zodat de waarde van het sleutel veld een van deze waarden moet zijn. Hier volgt een voorbeeld:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Als u meerdere verschillende filters opgeeft, wordt er een **en** -bewerking uitgevoerd, dus er moet aan elke filter voorwaarde worden voldaan. Hier volgt een voorbeeld: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operator"></a>Operator

De beschik bare Opera tors voor getallen zijn:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

De beschik bare operator voor Booleaanse waarden is: BoolEquals

De beschik bare Opera tors voor teken reeksen zijn:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Alle teken reeks vergelijkingen zijn hoofdletter-insensitve.

### <a name="key"></a>Sleutel

Voor gebeurtenissen in het Event Grid schema gebruikt u de volgende waarden voor de sleutel:

* id
* Onderwerp
* Subject
* EventType
* DataVersion
* Gebeurtenis gegevens (zoals data. Key1)

Voor gebeurtenissen in het schema voor Cloud gebeurtenissen gebruikt u de volgende waarden voor de sleutel:

* Gebeurtenis-id
* Source
* EventType
* EventTypeVersion
* Gebeurtenis gegevens (zoals data. Key1)

Voor het aangepaste invoer schema gebruikt u de velden voor gebeurtenis gegevens (zoals data. Key1).

### <a name="values"></a>Waarden

De waarden kunnen zijn:

* nummer
* string
* boolean
* array

### <a name="limitations"></a>Beperkingen

Geavanceerde filtering heeft de volgende beperkingen:

* Vijf geavanceerde filters per Event grid-abonnement
* 512 tekens per teken reeks waarde
* Vijf waarden voor **in** en **niet in** Opera tors

Dezelfde sleutel kan in meer dan één filter worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

* Zie [gebeurtenissen filteren voor Event grid voor](how-to-filter-events.md)meer informatie over het filteren van gebeurtenissen met Power shell en Azure cli.
* Als u wilt snel aan de slag met Event Grid, Zie [aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md).
