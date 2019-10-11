---
title: Zoek transformatie voor het toewijzen van gegevens stromen Azure Data Factory
description: Zoek transformatie voor het toewijzen van gegevens stromen Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: d762bddbe098e30cbf9e9c02da3c06073a358b12
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249255"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Zoek transformatie voor het toewijzen van gegevens stromen Azure Data Factory

Gebruik lookup om referentie gegevens van een andere bron toe te voegen aan uw gegevens stroom. Voor de opzoek transformatie is een gedefinieerde bron vereist die naar de referentie tabel verwijst en die overeenkomt met de sleutel velden.

Opzoek ![transformatie](media/data-flow/lookup1.png "zoeken")

Selecteer de belangrijkste velden die u wilt vergelijken tussen de binnenkomende stroom velden en de velden uit de referentie bron. U moet eerst een nieuwe bron op het ontwerp canvas voor gegevens stromen hebben gemaakt om te gebruiken als de rechter zijde voor de zoek actie.

Als er overeenkomsten worden gevonden, worden de resulterende rijen en kolommen van de referentie bron toegevoegd aan uw gegevens stroom. U kunt kiezen welke velden van belang u wilt opnemen in uw Sink aan het einde van de gegevens stroom. U kunt ook een trans formatie selecteren na uw lookup om de lijst met velden te verwijderen, zodat alleen de velden van beide stromen worden bewaard die u wilt behouden.

De zoek transformatie voert het equivalent van een left outer join uit. Daarom ziet u alle rijen uit uw left-bron combi neren met overeenkomsten aan de rechter kant. Als u meerdere overeenkomende waarden voor uw lookup hebt, of als u de opzoek expressie wilt aanpassen, kunt u het beste overschakelen naar een koppelings transformatie en een cross-koppeling gebruiken. Zo voor komt u mogelijke Cartesisch product fouten bij de uitvoering.

## <a name="match--no-match"></a>Overeenkomst/geen overeenkomst

Na uw opzoek transformatie kunt u volgende trans formaties gebruiken om de resultaten van elke overeenkomende rij te controleren met behulp van de expressie functie `isMatch()` om verdere keuzes te maken in uw logica, op basis van het feit of de zoek actie heeft geleid tot een overeenkomende rij of niet.

## <a name="optimizations"></a>Optimalisaties

In Data Factory worden gegevens stromen uitgevoerd in een uitgebreide Spark-omgeving. Als uw gegevensset kan worden aangepast in de geheugen ruimte van het worker-knoop punt, kunnen we de prestaties van de zoek opdracht optimaliseren.

Broadcast ![toevoegen aan](media/data-flow/broadcast.png "uitzending")

### <a name="broadcast-join"></a>Broadcast-koppeling

Selecteer links en/of de broadcast toevoegen aan de rechter kant om de ADF aan te vragen om de volledige gegevensset te pushen van beide zijden van de opzoek relatie naar het geheugen. Voor kleinere gegevens sets kan dit de prestaties van uw zoek opdrachten aanzienlijk verbeteren.

### <a name="data-partitioning"></a>Gegevenspartitionering

U kunt ook het partitioneren van uw gegevens opgeven door ' set partitioning ' te selecteren op het tabblad Optimize van de zoek transformatie om sets gegevens te maken die beter in het geheugen kunnen passen per werk nemer.

## <a name="next-steps"></a>Volgende stappen

Met trans formaties koppelen [en maken](data-flow-exists.md) [worden](data-flow-join.md) soort gelijke taken uitgevoerd in gegevens stromen voor ADF-toewijzing. Bekijk de volgende trans formaties.
