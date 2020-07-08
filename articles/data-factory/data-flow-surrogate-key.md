---
title: Surrogaat sleutel transformatie bij toewijzing van gegevens stroom
description: De trans formatie van de gegevens stroom surrogaat van Azure Data Factory gebruiken om sequentiële sleutel waarden te genereren
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606297"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Surrogaat sleutel transformatie bij toewijzing van gegevens stroom 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de trans formatie van de surrogaat sleutel om een incrementele sleutel waarde toe te voegen aan elke rij gegevens. Dit is handig bij het ontwerpen van dimensie tabellen in een ster schema analytisch gegevens model. In een ster schema is voor elk lid in de dimensie tabellen een unieke sleutel vereist die een niet-bedrijfs sleutel is.

## <a name="configuration"></a>Configuratie

![Surrogaat sleutel transformatie](media/data-flow/surrogate.png "Surrogaat sleutel transformatie")

**Sleutel kolom:** De naam van de gegenereerde surrogaat sleutel kolom.

**Begin waarde:** De laagste sleutel waarde die wordt gegenereerd.

## <a name="increment-keys-from-existing-sources"></a>Sleutels van bestaande bronnen stapsgewijs verhogen

Als u de volg orde wilt starten vanuit een waarde die voor komt in een bron, gebruikt u een afgeleide kolom transformatie na de trans formatie van de surrogaat sleutel om de twee waarden samen toe te voegen:

![SK toevoegen Max](media/data-flow/sk006.png "Aantal trans formatie van surrogaat sleutel maximum toevoegen")

### <a name="increment-from-existing-maximum-value"></a>Toename van de bestaande maximum waarde

Als u de sleutel waarde met het vorige maximum wilt seeden, zijn er twee methoden die u kunt gebruiken op basis van waar de bron gegevens zich bevinden.

#### <a name="database-sources"></a>Database bronnen

Gebruik een SQL-query optie om MAX () te selecteren in de bron. Bijvoorbeeld:`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Surrogaat sleutel query](media/data-flow/sk002.png "Query voor surrogaat sleutel transformatie")

#### <a name="file-sources"></a>Bestands bronnen

Als uw vorige maximum waarde zich in een bestand bevindt, gebruikt u de `max()` functie in de cumulatieve trans formatie om de vorige maximum waarde op te halen:

![Surrogaat sleutel bestand](media/data-flow/sk008.png "Surrogaat sleutel bestand")

In beide gevallen moet u uw inkomende nieuwe gegevens samen voegen met de bron die de vorige maximum waarde bevat.

![Surrogaat sleutel toevoegen](media/data-flow/sk004.png "Surrogaat sleutel toevoegen")

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Voorbeeld

![Surrogaat sleutel transformatie](media/data-flow/surrogate.png "Surrogaat sleutel transformatie")

Het gegevens stroom script voor de configuratie van de bovenstaande surrogaat sleutel bevindt zich in het onderstaande code fragment.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Volgende stappen

In deze voor beelden [worden](data-flow-join.md) de combi neren en [afgeleide kolom](data-flow-derived-column.md) transformaties gebruikt.
