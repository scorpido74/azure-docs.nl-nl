---
title: Surrogaatsleuteltransformatie in kaartbrengen gegevensstroom
description: De mapping dataflow surrogate key transformation van Azure Data Factory gebruiken om sequentiële sleutelwaarden te genereren
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: 6f0310f13ab23b5b3d4fba649d0faf0da4dad5b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413147"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Surrogaatsleuteltransformatie in kaartbrengen gegevensstroom 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Gebruik de surrogaatsleuteltransformatie om een verhogingssleutelwaarde toe te voegen aan elke rij gegevens. Dit is handig bij het ontwerpen van dimensietabellen in een analytisch gegevensmodel voor een sterschema. In een sterschema vereist elk lid in uw dimensietabellen een unieke sleutel die een niet-zakelijke sleutel is.

## <a name="configuration"></a>Configuratie

![Surrogaat sleutel transformeren](media/data-flow/surrogate.png "Surrogaat sleutel transformatie")

**Sleutelkolom:** De naam van de gegenereerde surrogaatsleutelkolom.

**Beginwaarde:** De laagste sleutelwaarde die wordt gegenereerd.

## <a name="increment-keys-from-existing-sources"></a>Toetsen uit bestaande bronnen verhogen

Als u uw reeks wilt starten vanaf een waarde die in een bron bestaat, gebruikt u een afgeleide kolomtransformatie na de transformatie van de surrogaatsleutel om de twee waarden samen toe te voegen:

![SK voeg Max toe](media/data-flow/sk006.png "Surrogaat sleutel transformatie voeg Max")

### <a name="increment-from-existing-maximum-value"></a>Verhoging van de bestaande maximumwaarde

Om de belangrijkste waarde met de vorige max te zaaien, zijn er twee technieken die u gebruiken op basis van waar uw brongegevens zich bevinden.

#### <a name="database-sources"></a>Databasebronnen

Gebruik een SQL-queryoptie om MAX() uit uw bron te selecteren. Bijvoorbeeld,`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Surrogaatsleutelquery](media/data-flow/sk002.png "Surrogaatsleuteltransformatiequery")

#### <a name="file-sources"></a>Bestandsbronnen

Als uw vorige maximumwaarde zich in `max()` een bestand bevindt, gebruikt u de functie in de samengevoegde transformatie om de vorige maximale waarde te krijgen:

![Surrogaatsleutelbestand](media/data-flow/sk008.png "Surrogaatsleutelbestand")

In beide gevallen moet u deelnemen aan uw binnenkomende nieuwe gegevens samen met uw bron die de vorige maximale waarde bevat.

![Surrogaatsleutel Join](media/data-flow/sk004.png "Surrogaatsleutel Join")

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Voorbeeld

![Surrogaat sleutel transformeren](media/data-flow/surrogate.png "Surrogaat sleutel transformatie")

Het script voor de gegevensstroom voor de bovenstaande surrogaatsleutelconfiguratie bevindt zich in het onderstaande codefragment.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Volgende stappen

In deze voorbeelden worden de transformaties [Join en](data-flow-join.md) [Afgeleide Kolom](data-flow-derived-column.md) gebruikt.
