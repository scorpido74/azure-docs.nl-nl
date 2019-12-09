---
title: Draai transformatie voor gegevens stroom toewijzen
description: Gegevens uit rijen naar kolommen draaien met Azure Data Factory de draai transformatie van de gegevens stroom koppelen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 8f23b5e61e1aee83172a12466fac8d5b5003fea8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930289"
---
# <a name="azure-data-factory-pivot-transformation"></a>Trans formatie van Azure data factory Pivot


Gebruik Pivot in ADF-gegevens stroom als aggregatie waarbij een of meer groeperings kolommen de afzonderlijke rijwaarden in afzonderlijke kolommen hebben getransformeerd. In wezen kunt u rij-waarden in nieuwe kolommen draaien (gegevens omzetten in meta gegevens).

![Opties voor draai diagram](media/data-flow/pivot1.png "Draai tabel 1")

## <a name="group-by"></a>Groeperen op

![Opties voor draai diagram](media/data-flow/pivot2.png "Draai tabel 2")

Stel eerst de kolommen in waarop u wilt groeperen voor de aggregatie van de draai tabel. U kunt hier meer dan 1 kolom instellen met het plus teken naast de kolom lijst.

## <a name="pivot-key"></a>Draai sleutel

![Opties voor draai diagram](media/data-flow/pivot3.png "Draai tabel 3")

De Pivot-toets is de kolom die ADF van rij naar kolom draait. Standaard wordt elke unieke waarde in de gegevensset voor dit veld naar een kolom gedraaid. U kunt eventueel ook de waarden opgeven uit de gegevensset die u wilt draaien naar kolom waarden. Dit is de kolom die bepaalt welke nieuwe kolommen er worden gemaakt.

## <a name="pivoted-columns"></a>Gedraaide kolommen

![Opties voor draai diagram](media/data-flow/pivot4.png "Draai tabel 4")

Ten slotte kiest u de aggregatie die u wilt gebruiken voor de gedraaide waarden en hoe u wilt dat de kolommen worden weer gegeven in de nieuwe uitvoer projectie van de trans formatie.

Beschrijving U kunt een naamgevings patroon met een voor voegsel, Midden en achtervoegsel instellen dat moet worden toegevoegd aan elke nieuwe kolom naam uit de rijwaarden.

Bijvoorbeeld: het draaien van ' verkoop ' op ' regio ' zou resulteren in nieuwe kolom waarden van elke verkoop waarde, dat wil zeggen ' 25 ', ' 50 ', ' 1000 ' enzovoort. Als u echter de waarde ' verkopen-' instelt voor het voor voegsel, zou elke kolom waarde ' verkoop-' aan het begin van de waarde toevoegen.

![Opties voor draai diagram](media/data-flow/pivot5.png "Draai tabel 5")

Als u de rang schikking van de kolom instelt op normaal, worden alle gedraaide kolommen gegroepeerd met de geaggregeerde waarden. Als u de rang schikking van de kolommen instelt op ' zijdelings ', wordt er een alternatief voor de kolom en waarde.

### <a name="aggregation"></a>Aggregatie

Als u de aggregatie wilt instellen die u wilt gebruiken voor de draai waarden, klikt u op het veld onder aan het deel venster gedraaide kolommen. U gaat de opbouw functie voor de ADF-gegevensstroom expressie invoeren, waar u een aggregatie-expressie kunt maken en een beschrijvende alias naam voor de nieuwe geaggregeerde waarden opgeeft.

Gebruik de taal van de ADF-gegevens stroom om de gedraaide kolom transformaties in de opbouw functie voor expressies te beschrijven: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Meta gegevens van draai tabel

Met de draai transformatie worden nieuwe kolom namen geproduceerd die dynamisch zijn op basis van uw binnenkomende gegevens. De sleutel Pivot produceert de waarden voor elke nieuwe kolom naam. Als u geen afzonderlijke waarden opgeeft en dynamische kolom namen wilt maken voor elke unieke waarde in uw draai tabel, worden de meta gegevens in de gebruikers interface niet weer gegeven en wordt er geen kolom door gegeven aan de Sink-trans formatie. Als u waarden voor de draai sleutel instelt, kan ADF de nieuwe kolom namen bepalen en kunnen de kolom namen voor u beschikbaar zijn in de inspectie-en Sink-toewijzing.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Een nieuw model genereren op basis van dynamische kolommen

Met Pivot worden nieuwe kolom namen dynamisch gegenereerd op basis van rijwaarden. U kunt deze nieuwe kolommen omzetten in meta gegevens waarnaar later in uw gegevens stroom kan worden verwezen. Hiertoe klikt u op het tabblad voor beeld van gegevens. Alle nieuwe kolommen die door de draai tabel transformatie worden gegenereerd, worden weer gegeven met een pictogram met een ' gedrijfte ' in de tabelkop. Klik op de knop voor het toewijzen van een kaart om deze nieuwe kolommen in meta gegevens in te scha kelen, zodat ze deel uitmaken van het model van de gegevens stroom.

![Draai kolommen](media/data-flow/newpivot1.png "Geplaatste draai kolommen toewijzen")

### <a name="landing-new-columns-in-sink"></a>Nieuwe kolommen in Sink-overloop

Zelfs met dynamische kolom namen in de draai tabel, kunt u nog steeds uw nieuwe kolom namen en-waarden in uw doel archief opvangen. Stel schema-drift toestaan in op in uw Sink-instellingen. De nieuwe dynamische namen worden niet weer gegeven in de meta gegevens van de kolom, maar met de optie schema-drift kunt u de gegevens in het land brengen.

### <a name="view-metadata-in-design-mode"></a>Meta gegevens weer geven in de ontwerp modus

Als u de nieuwe kolom namen als meta gegevens wilt bekijken en wilt zien dat de kolommen expliciet worden door gegeven aan de Sink-trans formatie, stelt u expliciete waarden in op het tabblad van de draai tabel.

### <a name="how-to-rejoin-original-fields"></a>De oorspronkelijke velden opnieuw samen voegen
De draai transformatie projecteert alleen de kolommen die worden gebruikt in de actie aggregatie, groepering en Pivot. Als u de andere kolommen uit de vorige stap in de stroom wilt opnemen, gebruikt u een nieuwe vertakking uit de vorige stap en gebruikt u het self-deelname patroon om de stroom met de oorspronkelijke meta gegevens te verbinden.

## <a name="next-steps"></a>Volgende stappen

Voer de [trans formatie van het draai diagram](data-flow-unpivot.md) uit om kolom waarden om te zetten in rijwaarden. 
