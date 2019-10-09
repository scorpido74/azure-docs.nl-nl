---
title: Zoek transformatie voor het toewijzen van gegevens stromen Azure Data Factory
description: Zoek transformatie voor het toewijzen van gegevens stromen Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029343"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Zoek transformatie voor het toewijzen van gegevens stromen Azure Data Factory



Gebruik lookup om referentie gegevens van een andere bron toe te voegen aan uw gegevens stroom. Voor de opzoek transformatie is een gedefinieerde bron vereist die naar de referentie tabel verwijst en die overeenkomt met de sleutel velden.

Opzoek ![transformatie](media/data-flow/lookup1.png "zoeken")

Selecteer de belangrijkste velden die u wilt vergelijken tussen de binnenkomende stroom velden en de velden uit de referentie bron. U moet eerst een nieuwe bron op het ontwerp canvas voor gegevens stromen hebben gemaakt om te gebruiken als de rechter zijde voor de zoek actie.

Als er overeenkomsten worden gevonden, worden de resulterende rijen en kolommen van de referentie bron toegevoegd aan uw gegevens stroom. U kunt kiezen welke velden van belang u wilt opnemen in uw Sink aan het einde van de gegevens stroom.

## <a name="match--no-match"></a>Overeenkomst/geen overeenkomst

Na uw opzoek transformatie kunt u volgende trans formaties gebruiken om de resultaten van elke overeenkomende rij te controleren met behulp van de expressie functie `isMatch()` om verdere keuzes te maken in uw logica, op basis van het feit of de zoek actie heeft geleid tot een overeenkomende rij of niet.

## <a name="optimizations"></a>Optimalisaties

In Data Factory worden gegevens stromen uitgevoerd in een uitgebreide Spark-omgeving. Als uw gegevensset kan worden aangepast in de geheugen ruimte van het worker-knoop punt, kunnen we de prestaties van de zoek opdracht optimaliseren.

Broadcast ![toevoegen aan](media/data-flow/broadcast.png "uitzending")

### <a name="broadcast-join"></a>Broadcast-koppeling

Selecteer links en/of de broadcast toevoegen aan de rechter kant om de ADF aan te vragen om de volledige gegevensset te pushen van beide zijden van de opzoek relatie naar het geheugen.

### <a name="data-partitioning"></a>Gegevenspartitionering

U kunt ook het partitioneren van uw gegevens opgeven door ' set partitioning ' te selecteren op het tabblad Optimize van de zoek transformatie om sets gegevens te maken die beter in het geheugen kunnen passen per werk nemer.

## <a name="next-steps"></a>Volgende stappen

Met trans formaties koppelen [en maken](data-flow-exists.md) [worden](data-flow-join.md) soort gelijke taken uitgevoerd in gegevens stromen voor ADF-toewijzing. Bekijk de volgende trans formaties.
