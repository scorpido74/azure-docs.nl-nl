---
title: Draaittransformatie van gegevensstroom toewijzen
description: Gegevens draaien van rijen naar kolommen met azure data factory-toewijzing van gegevensstroomdraaitransformatie
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a2276f5714db427586dbd56027e51c167b8c604f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413599"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure-draaitransformatie voor gegevensfabrieken

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Gebruik Pivot in ADF-gegevensstroom als aggregatie waarbij een of meer groeperingskolommen zijn afzonderlijke rijwaarden omzetten in afzonderlijke kolommen. In wezen u rijwaarden in nieuwe kolommen draaien (gegevens omzetten in metagegevens).

![Draaiopties](media/data-flow/pivot1.png "draai1")

## <a name="group-by"></a>Groeperen op

![Draaiopties](media/data-flow/pivot2.png "draaipunt 2")

Stel eerst de kolommen in die u wilt groeperen voor uw spilaggregatie. U hier meer dan 1 kolom instellen met het + teken naast de kolomlijst.

## <a name="pivot-key"></a>Draaitoets

![Draaiopties](media/data-flow/pivot3.png "draaipunt 3")

De draaisleutel is de kolom die ADF van rij naar kolom draait. Standaard draait elke unieke waarde in de gegevensset voor dit veld naar een kolom. U echter optioneel de waarden uit de gegevensset invoeren die u wilt draaien naar kolomwaarden. Dit is de kolom die de nieuwe kolommen bepaalt die worden gemaakt.

## <a name="pivoted-columns"></a>Gedraaide kolommen

![Draaiopties](media/data-flow/pivot4.png "draaipunt 4")

Ten slotte kiest u de aggregatie die u wilt gebruiken voor de draaibare waarden en hoe u de kolommen wilt weergeven in de nieuwe uitvoerprojectie van de transformatie.

(Optioneel) U een naamgevingspatroon instellen met een voorvoegsel, midden en achtervoegsel dat moet worden toegevoegd aan elke nieuwe kolomnaam van de rijwaarden.

Als u bijvoorbeeld "Verkoop" door "Regio" draait, zou dit resulteren in nieuwe kolomwaarden van elke verkoopwaarde, d.w.z. "25", "50", "1000", enz. Als u echter een voorvoegselwaarde van 'Verkoop' instelt, voegt elke kolomwaarde 'Verkoop' toe aan het begin van de waarde.

![Draaiopties](media/data-flow/pivot5.png "draaipunt 5")

Als u de kolomregeling instelt op 'Normaal' worden alle gedraaide kolommen gegroepeerd met hun samengevoegde waarden. Als u de kolommenregeling instelt op 'Lateral' wordt afwisselend kolom en waarde.

### <a name="aggregation"></a>Aggregatie

Als u de aggregatie wilt instellen die u wilt gebruiken voor de draaiwaarden, klikt u op het veld onder aan het deelvenster Draaibare kolommen. U gaat de ADF-gegevensstroomexpressiebouwer in waar u een aggregatie-expressie maken en een beschrijvende aliasnaam opgeven voor uw nieuwe samengevoegde waarden.

Gebruik de af-gegevensstroomexpressietaal om de draaikolomtransformaties https://aka.ms/dataflowexpressionsin de opbouwfunctie voor expressies te beschrijven: .

## <a name="pivot-metadata"></a>Pivot-metagegevens

De Pivot-transformatie levert nieuwe kolomnamen op die dynamisch zijn op basis van uw binnenkomende gegevens. De draaitoets produceert de waarden voor elke nieuwe kolomnaam. Als u geen afzonderlijke waarden opgeeft en dynamische kolomnamen wilt maken voor elke unieke waarde in uw draaisleutel, wordt de metagegevens niet weergegeven in Inspecten en is er geen kolomweergave voor de sinktransformatie. Als u waarden instelt voor de draaitoets, kan ADF de nieuwe kolomnamen bepalen en zijn die kolomnamen voor u beschikbaar in de toewijzing voor inspecteren en zinken.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Een nieuw model genereren op basis van dynamische kolommen

Pivot genereert dynamisch nieuwe kolomnamen op basis van rijwaarden. U deze nieuwe kolommen omzetten in metagegevens waarnaar later in uw gegevensstroom kan worden verwezen. Klik hiervoor op het tabblad Gegevensvoorbeeld. Alle nieuwe kolommen die door de transformatie van uw Draai draaien worden gegenereerd, worden weergegeven met een pictogram 'zwevend' in de tabelkoptekst. Klik op de knop 'Kaartdrifted' om die nieuwe kolommen om te zetten in metadata, waardoor ze deel uitmaken van het model van uw gegevensstroom.

![Kolommen draaien](media/data-flow/newpivot1.png "Zwevende draaikolommen toewijzen")

### <a name="landing-new-columns-in-sink"></a>Landing nieuwe kolommen in Sink

Zelfs met dynamische kolomnamen in Pivot u uw nieuwe kolomnamen en -waarden nog steeds in uw bestemmingsarchief laten zinken. Stel gewoon "Schema Drift toestaan" in op in uw Sink-instellingen. U ziet de nieuwe dynamische namen niet in de metagegevens van uw kolom, maar met de optie schemadrift u de gegevens landen.

### <a name="view-metadata-in-design-mode"></a>Metagegevens weergeven in ontwerpmodus

Als u de nieuwe kolomnamen als metagegevens wilt weergeven in Inspecteren en u wilt dat de kolommen expliciet worden weergegeven aan de transformatie van sink, stelt u expliciete waarden in op het tabblad Draaisleutel.

### <a name="how-to-rejoin-original-fields"></a>Opnieuw lid worden van oorspronkelijke velden
De pivot-transformatie projecteert alleen de kolommen die worden gebruikt in de actie aggregatie, groepering en draaipunt. Als u de andere kolommen uit de vorige stap in uw stroom wilt opnemen, gebruikt u een nieuwe branch uit de vorige stap en gebruikt u het zelfsluitpatroon om de stroom te verbinden met de oorspronkelijke metagegevens.

## <a name="next-steps"></a>Volgende stappen

Probeer de [draaibare transformatie om](data-flow-unpivot.md) kolomwaarden om te zetten in rijwaarden. 
