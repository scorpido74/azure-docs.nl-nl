---
title: Trans formatie voor het opzoeken van gegevens stromen toewijzen
description: Zoek transformatie voor het toewijzen van gegevens stromen Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2020
ms.openlocfilehash: 2216e1bf058eef486dbfefba24d52bdc6bdb232f
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164675"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Zoek transformatie voor het toewijzen van gegevens stromen Azure Data Factory

Gebruik lookup om referentie gegevens van een andere bron toe te voegen aan uw gegevens stroom. Voor de opzoek transformatie is een gedefinieerde bron vereist die naar de referentie tabel verwijst en die overeenkomt met de sleutel velden.

![Zoek transformatie](media/data-flow/lookup1.png "Opzoeken")

Selecteer de belangrijkste velden die u wilt vergelijken tussen de binnenkomende stroom velden en de velden uit de referentie bron. U moet eerst een nieuwe bron op het ontwerp canvas voor gegevens stromen hebben gemaakt om te gebruiken als de rechter zijde voor de zoek actie.

Als er overeenkomsten worden gevonden, worden de resulterende rijen en kolommen van de referentie bron toegevoegd aan uw gegevens stroom. U kunt kiezen welke velden van belang u wilt opnemen in uw Sink aan het einde van de gegevens stroom. U kunt ook een trans formatie selecteren na uw lookup om de lijst met velden te verwijderen, zodat alleen de velden van beide stromen worden bewaard die u wilt behouden.

De zoek transformatie voert het equivalent van een left outer join uit. Daarom ziet u alle rijen uit uw left-bron combi neren met overeenkomsten aan de rechter kant. Als u meerdere overeenkomende waarden voor uw lookup hebt, of als u de opzoek expressie wilt aanpassen, kunt u het beste overschakelen naar een koppelings transformatie en een cross-koppeling gebruiken. Zo voor komt u mogelijke Cartesisch product fouten bij de uitvoering.

## <a name="match--no-match"></a>Overeenkomst/geen overeenkomst

Na uw opzoek transformatie kunt u volgende trans formaties gebruiken om de resultaten van elke overeenkomende rij te controleren met behulp van de expressie functie `isMatch()` om verdere keuzes in uw logica te maken op basis van het feit of de zoek actie heeft geleid tot een overeenkomende rij of niet.

![Zoek patroon](media/data-flow/lookup111.png "Zoek patroon")

Nadat u de lookup-trans formatie hebt gebruikt, kunt u een voorwaardelijke Splits Transform-splitsing toevoegen aan de functie ```isMatch()```. In het bovenstaande voor beeld lopen overeenkomende rijen via de stroom van de bovenste stroom en niet-overeenkomende rijen door de ```NoMatch``` stroom.

## <a name="first-or-last-value"></a>Eerste of laatste waarde

De opzoek transformatie wordt geïmplementeerd als een left outer join. Wanneer u meerdere overeenkomsten van uw zoek opdracht hebt, wilt u mogelijk de meerdere overeenkomende rijen verminderen door de eerste overeenkomende rij, de laatste overeenkomst of een wille keurige rij te kiezen.

### <a name="option-1"></a>optie 1

![Zoek opdracht in één rij](media/data-flow/singlerowlookup.png "Zoek opdracht in één rij")

* Met meerdere rijen overeenkomen: laat het veld leeg om één overeenkomende rij te retour neren
* Zoeken op: Selecteer eerst, laatste of wille keurige overeenkomst
* Sorteer voorwaarden: als u eerste of laatste selecteert, vereist ADF dat uw gegevens worden besteld, zodat er sprake is van logica achter de eerste en laatste

> [!NOTE]
> Gebruik alleen de eerste of laatste optie van uw selector voor één rij als u wilt bepalen welke waarde moet worden gebruikt om de zoek actie te herstellen. Het gebruik van een wille keurige zoek actie in meerdere rijen gaat sneller.

### <a name="option-2"></a>Optie 2

U kunt dit ook doen met behulp van een statistische trans formatie na de zoek actie. In dit geval wordt een geaggregeerde trans formatie met de naam ```PickFirst``` gebruikt om de eerste waarde uit de zoek overeenkomsten te kiezen.

![Aggregatie opzoeken](media/data-flow/lookup333.png "Aggregatie opzoeken")

![Eerst opzoeken](media/data-flow/lookup444.png "Eerst opzoeken")

## <a name="optimizations"></a>Optimalisaties

In Data Factory worden gegevens stromen uitgevoerd in een uitgebreide Spark-omgeving. Als uw gegevensset kan worden aangepast in de geheugen ruimte van het worker-knoop punt, kunnen we de prestaties van de zoek opdracht optimaliseren.

![Broadcast-koppeling](media/data-flow/broadcast.png "Broadcast-koppeling")

### <a name="broadcast-join"></a>Broadcast-koppeling

Selecteer links en/of de broadcast toevoegen aan de rechter kant om de ADF aan te vragen om de volledige gegevensset te pushen van beide zijden van de opzoek relatie naar het geheugen. Voor kleinere gegevens sets kan dit de prestaties van uw zoek opdrachten aanzienlijk verbeteren.

### <a name="data-partitioning"></a>Gegevenspartitionering

U kunt ook het partitioneren van uw gegevens opgeven door ' set partitioning ' te selecteren op het tabblad Optimize van de zoek transformatie om sets gegevens te maken die beter in het geheugen kunnen passen per werk nemer.

## <a name="next-steps"></a>Volgende stappen

* Met trans formaties koppelen [en maken](data-flow-exists.md) [worden](data-flow-join.md) soort gelijke taken uitgevoerd in gegevens stromen voor ADF-toewijzing. Bekijk de volgende trans formaties.
* Een [voorwaardelijke splitsing](data-flow-conditional-split.md) met ```isMatch()``` gebruiken om rijen te splitsen op overeenkomende en niet-overeenkomende waarden
