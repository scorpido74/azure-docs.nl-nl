---
title: Schemadrift in kaartgegevensstroom
description: Veerkrachtige gegevensstromen bouwen in Azure Data Factory met Schema Drift
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2019
ms.openlocfilehash: 9daf7973a2e48f866a8d0b93a682851d31dc3af7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928541"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Schemadrift in kaartgegevensstroom

Schemadrift is het geval wanneer uw bronnen vaak metagegevens wijzigen. Velden, kolommen en typen kunnen on the fly worden toegevoegd, verwijderd of gewijzigd. Zonder verwerking voor schemadrift wordt uw gegevensstroom kwetsbaar voor upstream gegevensbronwijzigingen. Typische ETL-patronen mislukken wanneer binnenkomende kolommen en velden veranderen omdat ze meestal zijn gekoppeld aan die bronnamen.

Om te beschermen tegen schemadrift, is het belangrijk om de faciliteiten in een dataflow-tool te hebben waarmee u als Data Engineer:

* Bronnen definiëren met veranderlijke veldnamen, gegevenstypen, waarden en groottes
* Transformatieparameters definiëren die kunnen werken met gegevenspatronen in plaats van hardgecodeerde velden en waarden
* Expressies definiëren die patronen begrijpen die overeenkomen met binnenkomende velden, in plaats van benoemde velden te gebruiken

Azure Data Factory ondersteunt native flexibele schema's die van uitvoering naar uitvoering veranderen, zodat u algemene gegevenstransformatielogica bouwen zonder dat u uw gegevensstromen opnieuw hoeft te compileren.

U moet een architecturale beslissing nemen in uw gegevensstroom om schemadrift in uw stroom te accepteren. Wanneer u dit doet, u beschermen tegen schemawijzigingen van de bronnen. U verliest echter vroege binding van uw kolommen en typen in uw gegevensstroom. Azure Data Factory behandelt schemadriftstromen als laatbindende stromen, dus wanneer u uw transformaties bouwt, zijn de zwevende kolomnamen niet beschikbaar in de schemaweergaven gedurende de hele stroom.

## <a name="schema-drift-in-source"></a>Schemadrift in bron

Kolommen die in uw gegevensstroom van uw brondefinitie komen, worden gedefinieerd als 'zwevend' wanneer ze niet aanwezig zijn in uw bronprojectie. U de bronprojectie bekijken vanaf het projectietabblad in de brontransformatie. Wanneer u een gegevensset voor uw bron selecteert, neemt ADF automatisch het schema uit de gegevensset en maakt een project uit die schemadefinitie van gegevensset.

In een brontransformatie wordt schemadrift gedefinieerd als leeskolommen die niet zijn gedefinieerd in het gegevenssetschema. Als u schemadrift wilt inschakelen, schakelt u **Schemadrift** toestaan in uw brontransformatie in.

![Drijvende bron van schema](media/data-flow/schemadrift001.png "Drijvende bron van schema")

Wanneer schemadrift is ingeschakeld, worden alle binnenkomende velden tijdens de uitvoering uit uw bron gelezen en door de hele stroom naar de gootsteen doorgegeven. Standaard komen alle nieuw gedetecteerde kolommen, bekend als *zwevende kolommen,* als een tekenreeksgegevenstype. Als u wilt dat uw gegevensstroom automatisch gegevenstypen van zwevende kolommen afstelt, schakelt u **Kolomtypen Infer** in uw broninstellingen in.

## <a name="schema-drift-in-sink"></a>De afwijking van het schema in gootsteen

In een sinktransformatie is schemadrift wanneer u extra kolommen schrijft bovenop wat is gedefinieerd in het sinkgegevensschema. Als u schemadrift wilt inschakelen, schakelt u **Schemadrift** toestaan in uw gootsteentransformatie in.

![De afwijkingsgootsteen van het schema](media/data-flow/schemadrift002.png "De afwijkingsgootsteen van het schema")

Als schemadrift is ingeschakeld, controleert u of de schuifregelaar **Automatisch toewijzen** op het tabblad Toewijzing is ingeschakeld. Met deze schuifregelaar aan worden alle binnenkomende kolommen naar uw bestemming geschreven. Anders moet u op regels gebaseerde toewijzing gebruiken om zwevende kolommen te schrijven.

![Automatische toewijzing van sink](media/data-flow/automap.png "Automatische toewijzing van sink")

## <a name="transforming-drifted-columns"></a>Zwevende kolommen transformeren

Wanneer uw gegevensstroom kolommen heeft afgedwaald, u deze openen in uw transformaties met de volgende methoden:

* Gebruik `byPosition` de `byName` expressies en expressies om expliciet naar een kolom te verwijzen op naam of positienummer.
* Een kolompatroon toevoegen in een afgeleide kolom of aggregaattransformatie die overeenkomt met een combinatie van naam, stroom, positie of type
* Op regels gebaseerde toewijzing toevoegen aan een transformatie Selecteren of Gootsteen om zwevende kolommen via een patroon af te koppelen aan kolommen aliassen

Zie [Kolompatronen in de toewijzingsgegevensstroom](concepts-data-flow-column-pattern.md)voor meer informatie over het implementeren van kolompatronen.

### <a name="map-drifted-columns-quick-action"></a>Snel actie van zwevende kolommen toewijzen

Als u expliciet wilt verwijzen naar zwevende kolommen, u snel toewijzingen voor deze kolommen genereren via een snelle actie voor gegevensvoorbeeld. Zodra [de foutopsporingsmodus](concepts-data-flow-debug-mode.md) is ingeschakeld, gaat u naar het tabblad Gegevensvoorbeeld en klikt u op **Vernieuwen** om een gegevensvoorbeeld op te halen. Als gegevensfabriek detecteert dat er zwevende kolommen bestaan, u op **Drifted** toewijzen klikken en een afgeleide kolom genereren waarmee u alle zwevende kolommen in schemaweergaven stroomafwaarts verwijzen.

![Kaart dreef](media/data-flow/mapdrifted1.png "Kaart dreef")

In de gegenereerde afgeleide kolomtransformatie wordt elke zwevende kolom toegewezen aan de gedetecteerde naam en het gegevenstype. In het bovenstaande voorbeeld van gegevens wordt de kolom 'movieId' gedetecteerd als een geheel getal. Nadat **op Map Drifted** is geklikt, wordt movieId `toInteger(byName('movieId'))` gedefinieerd in de afgeleide kolom als en opgenomen in schemaweergaven in downstreamtransformaties.

![Kaart dreef](media/data-flow/mapdrifted2.png "Kaart dreef")

## <a name="next-steps"></a>Volgende stappen
In de [expressiontaal gegevensstroom](data-flow-expression-functions.md)vindt u extra faciliteiten voor kolompatronen en schemadrift, waaronder 'byName' en 'byPosition'.
