---
title: Transformatie sorteren in de toewijzingsgegevensstroom
description: Sorteertransformatie voor het sorteren van azure-gegevensfabriek
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606322"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transformatie sorteren in de toewijzingsgegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de sorteertransformatie u de binnenkomende rijen in de huidige gegevensstroom sorteren. U afzonderlijke kolommen kiezen en sorteren in oplopende of aflopende volgorde.

> [!NOTE]
> Gegevensstromen worden uitgevoerd op sparkclusters die gegevens over meerdere knooppunten en partities distribueren. Als u ervoor kiest om uw gegevens opnieuw te verdelen in een volgende transformatie, u uw sortering verliezen als gevolg van het opnieuw herschikken van gegevens.

## <a name="configuration"></a>Configuratie

![Sorteerinstellingen](media/data-flow/sort.png "Sorteren")

**Geval ongevoelig:** Of u de aanvraag wilt negeren bij het sorteren van tekenreeksen of tekstvelden

**Alleen binnen partities sorteren:** Als gegevensstromen worden uitgevoerd op spark, wordt elke gegevensstroom verdeeld in partities. Met deze instelling worden gegevens alleen gesorteerd binnen de binnenkomende partities in plaats van de hele gegevensstroom te sorteren. 

**Sorteervoorwaarden:** Kies op welke kolommen u sorteert en in welke volgorde de sortering plaatsvindt. De volgorde bepaalt de sorteerprioriteit. Kies of nulls aan het begin of einde van de gegevensstroom wordt weergegeven.

### <a name="computed-columns"></a>Berekende kolommen

Als u een kolomwaarde wilt wijzigen of extraheren voordat u de sortering toepast, zweeft u boven de kolom en selecteert u 'berekende kolom'. Hiermee wordt de opbouwfunctie voor expressies geopend om een expressie voor de sorteerbewerking te maken in plaats van een kolomwaarde te gebruiken.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntaxis

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Voorbeeld

![Sorteerinstellingen](media/data-flow/sort.png "Sorteren")

Het gegevensstroomscript voor de bovenstaande sorteerconfiguratie bevindt zich in het onderstaande codefragment.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Volgende stappen

Na het sorteren u de [aggregaattransformatie](data-flow-aggregate.md) gebruiken
