---
title: 'Filtergebaseerde functieselectie: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Lees hoe u de module Filtergebaseerde functieselectie in Azure Machine Learning gebruiken om de functies in een gegevensset met de grootste voorspellende kracht te identificeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c009a98931240e92527035e51fdce3f1c92f5212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477592"
---
# <a name="filter-based-feature-selection"></a>Functieselectie op basis van filters

In dit artikel wordt beschreven hoe u de module Filtergebaseerde functieselectie gebruiken in Azure Machine Learning-ontwerper (voorbeeld). Met deze module u de kolommen in uw invoergegevensset identificeren die de grootste voorspellende kracht hebben. 

In het algemeen verwijst *de selectie* van functies naar het proces van het toepassen van statistische tests op inputs, gezien een opgegeven uitvoer. Het doel is om te bepalen welke kolommen meer voorspellend zijn voor de uitvoer. De module Filtergebaseerde functieselectie biedt meerdere algoritmes voor het selecteren van functies om uit te kiezen. De module bevat correlatiemethoden zoals Pearson-correlatie en chi-kwadraatwaarden. 

Wanneer u de module Filtergebaseerde functieselectie gebruikt, geeft u een gegevensset op en identificeert u de kolom die het label of de afhankelijke variabele bevat. Vervolgens geeft u één methode op die u wilt gebruiken bij het meten van het kenmerkbelang.

De module levert een gegevensset uit die de beste functiekolommen bevat, gerangschikt op voorspellende kracht. Het levert ook de namen van de functies en hun scores uit de geselecteerde statistiek.  

## <a name="what-filter-based-feature-selection-is"></a>Wat filtergebaseerde functieselectie is  

Deze module voor functieselectie wordt "filtergebaseerd" genoemd omdat u de geselecteerde statistiek gebruikt om irrelevante kenmerken te vinden. Vervolgens filtert u overbodige kolommen uit uw model. U kiest één statistische meting die bij uw gegevens past en de module berekent een score voor elke functiekolom. De kolommen worden geretourneerd gerangschikt op hun functiescores. 

Door de juiste functies te kiezen, u mogelijk de nauwkeurigheid en efficiëntie van classificatie verbeteren. 

U gebruikt meestal alleen de kolommen met de beste scores om uw voorspellende model te bouwen. Kolommen met slechte scores voor functieselectie kunnen in de gegevensset worden achtergelaten en worden genegeerd wanneer u een model bouwt.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Een statistiek voor functieselectie kiezen

De module Filtergebaseerde functieselectie biedt verschillende statistieken voor het beoordelen van de informatiewaarde in elke kolom. In deze sectie vindt u een algemene beschrijving van elke statistiek en hoe deze wordt toegepast. In de [technische notities](#technical-notes) en in de [instructies](#how-to-configure-filter-based-feature-selection) voor het configureren van elke module vindt u aanvullende vereisten voor het gebruik van elke statistiek.

-   **Pearson correlatie**  

    Pearson's correlatie statistiek, of Pearson correlatie coëfficiënt, is ook `r` bekend in statistische modellen als de waarde. Voor twee variabelen geeft dit een waarde weer die de sterkte van de correlatie aangeeft.

    Pearson's correlatiecoëfficiënt wordt berekend door de covariantie van twee variabelen te nemen en te delen door het product van hun standaarddeviaties. Veranderingen in de schaal in de twee variabelen hebben geen invloed op de coëfficiënt.  

-   **Chi in het kwadraat**  

    De tweerichtingschi-kwadraattest is een statistische methode die meet hoe dicht verwachte waarden zijn bij de werkelijke resultaten. De methode gaat ervan uit dat variabelen willekeurig zijn en zijn ontleend aan een adequate steekproef van onafhankelijke variabelen. De resulterende chi-kwadraat statistiek geeft aan hoe ver de resultaten zijn van het verwachte (willekeurige) resultaat.  


> [!TIP]
> Als u een andere optie nodig hebt voor de aangepaste methode voor het selecteren van functies, gebruikt u de module [R-script uitvoeren.](execute-r-script.md) 

## <a name="how-to-configure-filter-based-feature-selection"></a>Filterselectie op basis van filters configureren

U kiest een standaard statistische statistiek. De module berekent de correlatie tussen een paar kolommen: de labelkolom en een functiekolom.

1.  Voeg de module Filtergebaseerde functieselectie toe aan uw pijplijn. U het vinden in de categorie **Functieselectie** in de ontwerper.

2. Sluit een invoergegevensset aan die ten minste twee kolommen bevat die potentiële functies zijn.  

    Als u ervoor wilt zorgen dat een kolom wordt geanalyseerd en een functiescore wordt gegenereerd, gebruikt u de module [Metagegevens bewerken](edit-metadata.md) om het kenmerk **IsFeature** in te stellen. 

    > [!IMPORTANT]
    > Zorg ervoor dat de kolommen die u als invoer levert potentiële functies zijn. Een kolom met één waarde heeft bijvoorbeeld geen informatiewaarde.
    >
    > Als u weet dat sommige kolommen slechte functies zouden maken, u deze verwijderen uit de kolomselectie. U de module [Metagegevens bewerken](edit-metadata.md) ook gebruiken om ze als categorisch te **markeren.** 
3.  Kies **bij Functiescoremethode**een van de volgende vastgestelde statistische methoden die u wilt gebruiken bij het berekenen van scores.  

    | Methode              | Vereisten                             |
    | ------------------- | ---------------------------------------- |
    | Pearson correlatie | Label kan tekst of numeriek zijn. Functies moeten numeriek zijn. |
    Chi in het kwadraat| Labels en functies kunnen tekst of numeriek zijn. Gebruik deze methode voor het gebruik van functie belang voor twee categorische kolommen.|

    > [!TIP]
    > Als u de geselecteerde statistiek wijzigt, worden alle andere selecties opnieuw ingesteld. Zorg er dus voor dat u deze optie eerst instelt.
4.  Selecteer de optie **Alleen op functiekolommen werken** om een score alleen te genereren voor kolommen die eerder als functies waren gemarkeerd. 

    Als u deze optie wist, maakt de module een score voor elke kolom die anders aan de criteria voldoet, tot het aantal kolommen dat is opgegeven in **Aantal gewenste functies.**  

5.  Selecteer **bij de kolom Doel**de optie **Kolomkiezer starten** om de labelkolom op naam of op de index te kiezen. (Indexen zijn één gebaseerd.)  
    Er is een labelkolom vereist voor alle methoden die statistische correlatie omvatten. De module retourneert een fout in de ontwerptijd als u geen labelkolom of meerdere labelkolommen kiest. 

6.  Voer **voor Aantal gewenste functies**het aantal functiekolommen in dat u als resultaat wilt geretourneerd:  

    - Het minimum aantal functies dat u opgeven is één, maar we raden u aan deze waarde te verhogen.  

    - Als het opgegeven aantal gewenste functies groter is dan het aantal kolommen in de gegevensset, worden alle functies geretourneerd. Zelfs functies met nul scores worden geretourneerd.  

    - Als u minder resultaatkolommen opgeeft dan er functiekolommen zijn, worden de functies gerangschikt op dalende score. Alleen de topfuncties worden geretourneerd. 

7.  Verzend de pijplijn of selecteer de module Filtergebaseerde functieselectie en selecteer **Geselecteerd uitvoeren**.


## <a name="results"></a>Resultaten

Nadat de verwerking is voltooid:

+ Als u een volledige lijst met de geanalyseerde functiekolommen en hun scores wilt bekijken, klikt u met de rechtermuisknop op de module en selecteert u **Visualiseren**.  

+ Als u de gegevensset wilt weergeven op basis van de selectiecriteria van uw functie, klikt u met de rechtermuisknop op de module en selecteert u **Visualiseren**. 

Als de gegevensset minder kolommen bevat dan u had verwacht, controleert u de module-instellingen. Controleer ook de gegevenstypen van de kolommen die als invoer worden geleverd. Als u bijvoorbeeld **Het aantal gewenste functies** instelt op 1, bevat de uitvoergegevensset slechts twee kolommen: de labelkolom en de meest gerangschikte functiekolom.


##  <a name="technical-notes"></a>Technische notities  

### <a name="implementation-details"></a>Uitvoeringsdetails

Als u Pearson-correlatie gebruikt voor een numerieke functie en een categorisch label, wordt de functiescore als volgt berekend:  

1.  Bereken voor elk niveau in de categorische kolom het voorwaardelijke gemiddelde van de numerieke kolom.  

2.  Correleer de kolom met voorwaardelijke middelen met de numerieke kolom.  

### <a name="requirements"></a>Vereisten  

-   Een functieselectiescore kan niet worden gegenereerd voor een kolom die is aangeduid als een kolom **Label** of **Score.**  

-   Als u een scoringsmethode probeert te gebruiken met een kolom van een gegevenstype dat de methode niet ondersteunt, wordt er een fout in de module gegenereerd. Of er wordt een nulscore toegewezen aan de kolom.  

-   Als een kolom logische (waar/false) waarden bevat, worden deze verwerkt als `True = 1` en `False = 0`.  

-   Een kolom kan geen functie zijn als deze is aangewezen als **label** of **score.**  

### <a name="how-missing-values-are-handled"></a>Hoe ontbrekende waarden worden verwerkt  

-   U als kolom (label) geen kolom opgeven met alle ontbrekende waarden.  

-   Als een kolom ontbrekende waarden bevat, negeert de module deze wanneer deze de score voor de kolom indeomt.  

-   Als een kolom die is aangewezen als een functiekolom alle ontbrekende waarden heeft, kent de module een nulscore toe.   


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 

