---
title: Gebeurtenisfiltering voor Azure Event Grid
description: Beschrijft hoe u gebeurtenissen filtert bij het maken van een Azure Event Grid-abonnement.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: f9fca0a9fefb5959747a4492139ae422a118db02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70390181"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Inzicht in gebeurtenisfiltering voor eventgrid-abonnementen

In dit artikel worden de verschillende manieren beschreven waarop u filteren welke gebeurtenissen naar uw eindpunt worden verzonden. Wanneer u een gebeurtenisabonnement maakt, hebt u drie opties voor het filteren:

* Gebeurtenistypen
* Onderwerp begint met of eindigt met
* Geavanceerde velden en operatoren

## <a name="event-type-filtering"></a>Filtering van gebeurtenistype

Standaard worden alle [gebeurtenistypen](event-schema.md) voor de gebeurtenisbron naar het eindpunt verzonden. U besluiten om alleen bepaalde gebeurtenistypen naar uw eindpunt te sturen. U bijvoorbeeld een melding krijgen van updates voor uw resources, maar niet op de hoogte worden gesteld van andere bewerkingen, zoals verwijderingen. Filter in dat geval `Microsoft.Resources.ResourceWriteSuccess` op het gebeurtenistype. Geef een array met de `All` gebeurtenistypen of geef op om alle gebeurtenistypen voor de gebeurtenisbron op te halen.

De syntaxis van JSON voor filteren op gebeurtenistype is:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Onderwerpfiltering

Geef voor eenvoudige filtering op onderwerp een begin- of eindwaarde voor het onderwerp op. U bijvoorbeeld opgeven met `.txt` welke het onderwerp eindigt om alleen gebeurtenissen te krijgen die betrekking hebben op het uploaden van een tekstbestand naar een opslagaccount. U het onderwerp filteren `/blobServices/default/containers/testcontainer` om alle gebeurtenissen voor die container op te halen, maar niet met andere containers in het opslagaccount.

Wanneer u gebeurtenissen publiceert naar aangepaste onderwerpen, maakt u onderwerpen voor uw evenementen waarmee abonnees gemakkelijk kunnen weten of ze geïnteresseerd zijn in het evenement. Abonnees gebruiken de eigenschap onderwerp om gebeurtenissen te filteren en te routeren. Overweeg het pad toe te voegen voor de plaats waar de gebeurtenis is gebeurd, zodat abonnees kunnen filteren op segmenten van dat pad. Met het pad kunnen abonnees gebeurtenissen in beperkte of brede lijnen filteren. Als u een pad `/A/B/C` met drie segmenten opgeeft zoals in `/A` het onderwerp, kunnen abonnees filteren op het eerste segment om een brede set gebeurtenissen te krijgen. Die abonnees krijgen evenementen `/A/B/C` met `/A/D/E`onderwerpen als of . Andere abonnees kunnen `/A/B` filteren op een smallere set gebeurtenissen te krijgen.

De syntaxis JSON voor filteren op onderwerp is:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Geavanceerd filteren

Als u wilt filteren op waarden in de gegevensvelden en de vergelijkingsoperator wilt opgeven, gebruikt u de geavanceerde filteroptie. Bij geavanceerde filtering geeft u het volgende op:

* type operator - Het type vergelijking.
* toets - Het veld in de gebeurtenisgegevens die u gebruikt voor het filteren. Het kan een getal zijn, booleaanof touw.
* waarde of waarden - De waarde of waarden die moeten worden vergeleken met de sleutel.

Als u één filter met meerdere waarden opgeeft, wordt een **OK-bewerking** uitgevoerd, zodat de waarde van het sleutelveld een van deze waarden moet zijn. Hier volgt een voorbeeld:

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

Als u meerdere verschillende filters opgeeft, wordt een **AND-bewerking** uitgevoerd, zodat aan elke filtervoorwaarde moet worden voldaan. Hier volgt een voorbeeld: 

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

De beschikbare operatoren voor nummers zijn:

* AantalGroter dan
* NumberGreaterThanOrEquals NumberGreaterThanOrEquals NumberGreaterThanOrEquals NumberGreater
* Numberlessthan
* Numberlessthanorequals
* NumberIn
* NummerNotin

De beschikbare operator voor booleleans is: BoolEquals

De beschikbare operatoren voor tekenreeksen zijn:

* Tekenreeksbevat
* StringBeginsWith
* StringEndsWith
* Stringin
* StringNotin

Alle snaarvergelijkingen zijn case-insensitve.

### <a name="key"></a>Sleutel

Gebruik de volgende waarden voor gebeurtenissen in het schema gebeurtenisraster voor gebeurtenissen:

* Id
* Onderwerp
* Onderwerp
* EventType (EventType)
* Gegevensversie
* Gebeurtenisgegevens (zoals Data.key1)

Voor gebeurtenissen in het cloudgebeurtenissenschema gebruikt u de volgende waarden voor de sleutel:

* EventId (EventId)
* Bron
* EventType (EventType)
* EventTypeVersie
* Gebeurtenisgegevens (zoals Data.key1)

Gebruik voor het aangepaste invoerschema de velden gebeurtenisgegevens (zoals Data.key1).

### <a name="values"></a>Waarden

De waarden kunnen zijn:

* getal
* tekenreeks
* booleaans
* matrix

### <a name="limitations"></a>Beperkingen

Geavanceerde filtering heeft de volgende beperkingen:

* Vijf geavanceerde filters per gebeurtenisrasterabonnement
* 512 tekens per tekenreekswaarde
* Vijf waarden voor **in** en **niet in** operators

Dezelfde sleutel kan in meer dan één filter worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

* Zie [Gebeurtenissen filteren voor gebeurtenisraster voor](how-to-filter-events.md)meer informatie over het filteren van gebeurtenissen met PowerShell en Azure CLI.
* Zie [Aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md)om snel aan de slag te gaan met gebeurtenisraster.
