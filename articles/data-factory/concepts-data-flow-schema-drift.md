---
title: Schema-drift in toewijzing van gegevens stroom
description: Flexibele gegevens stromen bouwen in Azure Data Factory met schema-drift
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2019
ms.openlocfilehash: 9daf7973a2e48f866a8d0b93a682851d31dc3af7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928541"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Schema-drift in toewijzing van gegevens stroom

Schema-drift is het geval waarin uw bronnen vaak meta gegevens wijzigen. Velden, kolommen en, typen kunnen worden toegevoegd, verwijderd of gewijzigd. Zonder de verwerking van schema drift wordt uw gegevens stroom kwetsbaar voor wijzigingen in de gegevens bron stroomopwaarts. Gebruikelijke ETL-patronen mislukken wanneer binnenkomende kolommen en velden worden gewijzigd omdat ze vaak aan deze bron namen worden gekoppeld.

Als u wilt beveiligen tegen schema drift, is het belang rijk dat u beschikt over de mogelijkheden van een hulp programma voor gegevens stromen zodat u, als een technicus, de volgende handelingen kunt uitvoeren:

* Bronnen definiëren die zijn ververanderd veld namen, gegevens typen, waarden en grootten
* Transformatie parameters definiëren die kunnen werken met gegevens patronen in plaats van in code vastgelegde velden en waarden
* Expressies definiëren die inzicht krijgen in patronen die overeenkomen met binnenkomende velden, in plaats van met behulp van benoemde velden

Azure Data Factory systeem eigen ondersteuning biedt voor flexibele schema's die van uitvoering naar uitvoering veranderen, zodat u een generieke gegevens transformatie logica kunt bouwen zonder dat u uw gegevens stromen opnieuw hoeft te compileren.

U moet in uw gegevens stroom een architectuur besluit nemen om schema drift in uw stroom te accepteren. Wanneer u dit doet, kunt u zich beschermen tegen schema wijzigingen van de bronnen. U verliest echter een vroege binding van uw kolommen en typen in uw gegevens stroom. Azure Data Factory behandel schema-drijf stromen als late binding stromen, dus wanneer u de trans formaties bouwt, zijn de geplaatste kolom namen niet voor u beschikbaar in de schema weergaven in de hele stroom.

## <a name="schema-drift-in-source"></a>Schema-drift in bron

Kolommen die worden opgenomen in uw gegevens stroom van uw bron definitie worden gedefinieerd als ' overgelopen ' wanneer ze niet aanwezig zijn in de bron projectie. U kunt de bron projectie bekijken op het tabblad projectie van de bron transformatie. Wanneer u een gegevensset voor uw bron selecteert, neemt ADF automatisch het schema uit de gegevensset en maakt een project van die schema definitie van de gegevensset.

In een bron transformatie wordt schema-drift gedefinieerd als het lezen van kolommen die uw gegevensset-schema niet definiëren. Als u schema-drift wilt inschakelen, schakelt u **schema-drift toestaan** in uw bron transformatie in.

![Bron van schema-drift](media/data-flow/schemadrift001.png "Bron van schema-drift")

Wanneer schema-drift is ingeschakeld, worden alle binnenkomende velden tijdens de uitvoering van de bron gelezen en door de hele stroom aan de Sink door gegeven. Standaard worden alle nieuw gedetecteerde kolommen, ook wel *gedrijfte kolommen*, binnenkomen als teken reeks gegevens type. Als u wilt dat uw gegevens stroom automatisch gegevens typen van geplaatste kolommen afleiden, controleert u overlopende **kolom typen** in de bron instellingen.

## <a name="schema-drift-in-sink"></a>Schema-drift in Sink

In een Sink-trans formatie is schema drift wanneer u extra kolommen schrijft boven op wat is gedefinieerd in het sink-gegevens schema. Als u schema-drift wilt inschakelen, controleert u **schema-drift toestaan** in uw Sink-trans formatie.

![Sink voor schema-drift](media/data-flow/schemadrift002.png "Sink voor schema-drift")

Als schema-drift is ingeschakeld, zorgt u ervoor dat de schuif regelaar **automatische toewijzing** op het tabblad toewijzing is ingeschakeld. Met deze schuif regelaar aan worden alle binnenkomende kolommen naar uw bestemming geschreven. Anders moet u op regels gebaseerde toewijzing gebruiken om gedrijfde kolommen te schrijven.

![Automatische toewijzing van sinks](media/data-flow/automap.png "Automatische toewijzing van sinks")

## <a name="transforming-drifted-columns"></a>Gedrijfte kolommen transformeren

Wanneer de gegevens stroom een geplaatste kolom heeft, kunt u deze in uw trans formaties gebruiken met de volgende methoden:

* Gebruik de expressies `byPosition` en `byName` om expliciet te verwijzen naar een kolom met de naam of het positie nummer.
* Een kolom patroon toevoegen aan een afgeleide kolom of aggregatie transformatie die overeenkomt met een combi natie van naam, stroom, positie of type
* Op een regel gebaseerde toewijzing in een SELECT-of sink-trans formatie toevoegen om gedrijfte kolommen te vergelijken met kolommen aliassen via een patroon

Zie voor meer informatie over het implementeren van kolom patronen [kolom patronen in gegevens stroom toewijzen](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Snelle actie voor het toewijzen van gedrijfte kolommen

Als u expliciete verwijzingen naar gerefereerde kolommen wilt maken, kunt u snel toewijzingen voor deze kolommen genereren via een snelle actie voor het voor beeld van gegevens. Zodra de [foutopsporingsmodus](concepts-data-flow-debug-mode.md) is ingeschakeld, gaat u naar het tabblad voor beeld van gegevens en klikt u op **vernieuwen** om een voor beeld van de gegevens op te halen. Als data factory detecteert dat er geplaatste kolommen bestaan, kunt u op geplaatste **kaart** klikken en een afgeleide kolom genereren waarmee u kunt verwijzen naar alle geplaatste kolommen in schema weergaven downstream.

![Toegewezen kaart](media/data-flow/mapdrifted1.png "Toegewezen kaart")

In de gegenereerde afgeleide kolom transformatie wordt elke geplaatste kolom toegewezen aan de gedetecteerde naam en het gegevens type. In het bovenstaande voor beeld van de gegevens wordt de kolom ' movieId ' gedetecteerd als een geheel getal. Nadat de **toewijzing** is geklikt, wordt movieId gedefinieerd in de afgeleide kolom als `toInteger(byName('movieId'))` en opgenomen in schema weergaven in downstream-trans formaties.

![Toegewezen kaart](media/data-flow/mapdrifted2.png "Toegewezen kaart")

## <a name="next-steps"></a>Volgende stappen
In de [taal van de data flow-expressie](data-flow-expression-functions.md)vindt u aanvullende voorzieningen voor kolom patronen en schema-drift, waaronder "byName" en "byPosition".
