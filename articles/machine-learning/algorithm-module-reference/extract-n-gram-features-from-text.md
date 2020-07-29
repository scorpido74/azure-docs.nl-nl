---
title: N-gram-functies uit de naslag gids voor tekst modules extra heren
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module N-gram uitpakken in het Azure Machine Learning om tekst gegevens te parametriseer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: efe09c1d516b37c23b024e07ae387772fa7e5992
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477609"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>N-gram-functies uit de naslag gids voor tekst modules extra heren

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven. Gebruik de module N-gram functies van de tekst modules uitpakken om ongestructureerde tekst gegevens te *parametriseer* . 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Configuratie van de extra heren N-gram-functies uit de tekst module

De module ondersteunt de volgende scenario's voor het gebruik van een n-gram-woorden lijst:

* [Een nieuwe n-gram-woorden lijst maken](#create-a-new-n-gram-dictionary) op basis van een kolom met vrije tekst.

* [Gebruik een bestaande set tekst functies](#use-an-existing-n-gram-dictionary) om een vrije-tekst kolom te parametriseer.

* [Score of publiceer een model](#score-or-publish-a-model-that-uses-n-grams) dat gebruikmaakt van n-gram.

### <a name="create-a-new-n-gram-dictionary"></a>Een nieuwe n-gram-woorden lijst maken

1.  Voeg de extra N-gram-functies uit de tekst module toe aan uw pijp lijn en verbind de gegevensset met de tekst die u wilt verwerken.

1.  Gebruik de **tekst kolom** om een kolom van het teken reeks type te kiezen die de tekst bevat die u wilt ophalen. Omdat de resultaten uitgebreid zijn, kunt u slechts één kolom tegelijk verwerken.

1. Stel de **woordenlijst modus** in op **maken** om aan te geven dat u een nieuwe lijst met n-gram-functies maakt. 

1. Stel **n-gram grootte** in om de *maximale* grootte van het N-gram op te geven dat moet worden uitgepakt en opgeslagen. 

    Als u bijvoorbeeld 3, unigrams, bigrams en trigrams invoert, worden deze gemaakt.

1. Met de **functie weging** wordt aangegeven hoe u de document functie Vector bouwt en hoe u de woorden lijst uit documenten ophaalt.

    * **Binair gewicht**: wijst een binaire aanwezigheids waarde toe aan het geëxtraheerde n-gram. De waarde voor elke n-gram is 1 wanneer deze bestaat in het document en 0 anders.

    * **TF-gewicht**: Hiermee wordt een term frequentie (TF)-Score toegewezen aan de geëxtraheerde n-gram. De waarde voor elke n-gram is de frequentie van het voorval in het document.

    * **IDF Weight**: wijst een inverse document frequentie (IDF)-Score toe aan de geëxtraheerde n-gram. De waarde voor elke n-gram is het logboek van de verzameling grootte gedeeld door de frequentie van het exemplaar in het hele verzameling.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF Weight**: wijst een periode/inverse document frequentie (TF/IDF)-Score toe aan het geëxtraheerde n-gram. De waarde voor elke n-gram is de TF-Score vermenigvuldigd met de IDF-Score.

1. Stel **minimale woord lengte** in op het minimum aantal letters dat kan worden gebruikt in *één woord* in een n-gram.

1. Gebruik de **maximale woord lengte** om het maximum aantal letters in te stellen dat kan worden gebruikt in *één woord* in een n-gram.

    Standaard zijn Maxi maal 25 tekens per woord of Token toegestaan.

1. Gebruik **minimale n-gram document absolute frequentie** om de minimum aantal instanties in te stellen die vereist zijn voor een n-gram om te worden opgenomen in de n-gram-woorden lijst. 

    Als u bijvoorbeeld de standaard waarde 5 gebruikt, moet elke n-gram ten minste vijf keer worden weer gegeven in de verzameling die moet worden opgenomen in de n-gram-woorden lijst. 

1.  Stel de maximale verhouding van een **n-gram document** in op de maximum ratio van het aantal rijen dat een bepaald n-gram bevat, over het aantal rijen in de algemene verzameling.

    Een verhouding van 1 zou bijvoorbeeld aangeven dat, zelfs als een specifieke n-gram aanwezig is in elke rij, het n-gram kan worden toegevoegd aan de woorden lijst n-gram. Normaal gesp roken wordt een woord dat in elke rij voor komt, beschouwd als een ruis woord en zou het worden verwijderd. Als u wilt filteren op domein afhankelijke ruis woorden, verkleint u deze verhouding.

    > [!IMPORTANT]
    > Het aantal exemplaren van bepaalde woorden is niet uniform. Het is afhankelijk van document tot document. Als u bijvoorbeeld klant opmerkingen van een specifiek product analyseert, kan de product naam zeer hoge frequentie zijn en sluiten op een ruis woord, maar een belang rijke term in andere contexten zijn.

1. Selecteer de optie **r-gram-functie vectoren normaliseren** om de functie vectoren te normaliseren. Als deze optie is ingeschakeld, wordt elke n-gram functie Vector gedeeld door de L2-norm.

1. Verzend de pijp lijn.

### <a name="use-an-existing-n-gram-dictionary"></a>Een bestaande n-gram-woorden lijst gebruiken

1.  Voeg de extra N-gram-functies uit de module tekst toe aan uw pijp lijn en verbind de gegevensset met de tekst die u wilt verwerken naar de poort van de **gegevensset** .

1.  Gebruik de **tekst kolom** om de tekst kolom te selecteren die de tekst bevat die u wilt parametriseer. Standaard selecteert de module alle kolommen van het type **teken reeks**. Voor de beste resultaten moet u één kolom tegelijk verwerken.

1. Voeg de opgeslagen gegevensset met een eerder gegenereerde n-gram woorden lijst toe en verbind deze met de woordenlijst poort van de **invoer** . U kunt ook de **Resultaten woordenlijst** van een upstream-exemplaar van de functie voor het uitpakken van N-gram-functies uit de tekst module verbinden.

1. Voor de **woordenlijst modus**selecteert u de optie **alleen-lezen** in de vervolg keuzelijst.

   De optie **ReadOnly** vertegenwoordigt de invoer verzameling voor de woordenlijst van de invoer. In plaats van term frequenties te berekenen op basis van de nieuwe tekst gegevensset (aan de linkerkant), worden de n-gram-gewichten van de invoer woordenlijst toegepast als is.

   > [!TIP]
   > Gebruik deze optie wanneer u een beoordeling van een tekst classificatie uitvoert.

1.  Zie de beschrijvingen van eigenschappen in de [vorige sectie](#create-a-new-n-gram-dictionary)voor alle andere opties.

1.  Verzend de pijp lijn.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Een model dat gebruikmaakt van n-gram beoordelen of publiceren

1.  Kopieer de **functies N-gram van de tekst module extra heren** uit de stroom van de training naar de gegevens stroom voor scores.

1.  Verbind de **Resultaten woordenlijst** uitvoer van de trainings gegevensstroom om de **vocabulaire** op de score gegevensstroom te kunnen invoeren.

1.  Wijzig in de Score werk stroom de uitpakken N-gram functies uit de tekst module en stel de para meter voor de **woordenlijst modus** in op **alleen-lezen**. Alle andere taken blijven.

1.  Als u de pijp lijn wilt publiceren, slaat u **resultaat woordenlijst** op als gegevensset.

1.  Verbind de opgeslagen gegevensset met de functie N-gram functies van de module extra heren in uw score diagram.

## <a name="results"></a>Resultaten

Met de functie N-gram functies van de tekst module extra heren worden twee typen uitvoer gemaakt: 

* **Resulterende gegevensset**: deze uitvoer is een samen vatting van de geanalyseerde tekst in combi natie met het n-gram dat is geëxtraheerd. Kolommen die u niet hebt geselecteerd in de optie **tekst kolom** , worden door gegeven aan de uitvoer. Voor elke kolom met tekst die u analyseert, genereert de module deze kolommen:

  * **Matrix van n-gram-exemplaren**: de module genereert een kolom voor elke n-gram die is gevonden in de totale verzameling en voegt een score toe aan elke kolom om het gewicht van de n-gram voor die rij aan te geven. 

* **Resultaten woordenlijst**: de woorden lijst bevat de werkelijke n-gram-woorden lijst, samen met de term frequentie scores die worden gegenereerd als onderdeel van de analyse. U kunt de gegevensset opslaan voor hergebruik met een andere set invoer of voor een latere update. U kunt ook de woorden lijst voor model lering en scores opnieuw gebruiken.

### <a name="result-vocabulary"></a>Resultaten woordenlijst

De vocabulaire bevat de n-gram woordenlijst met de term frequentie scores die worden gegenereerd als onderdeel van de analyse. De VG-en IDF-scores worden gegenereerd, ongeacht andere opties.

+ **Id**: een id die voor elke unieke n-gram wordt gegenereerd.
+ **NGram**: de n-gram. Spaties of andere woord scheidings tekens worden vervangen door het onderstrepings teken.
+ **VG**: de term frequentie score voor de n-gram in de oorspronkelijke verzameling.
+ **IDF**: de inverse-frequentie score voor de n-gram in de oorspronkelijke verzameling.

U kunt deze gegevensset hand matig bijwerken, maar mogelijk fouten introduceren. Bijvoorbeeld:

* Er treedt een fout op als de module dubbele rijen met dezelfde sleutel in de ingevoerde vocabulaire vindt. Zorg ervoor dat er geen twee rijen in de woorden lijst hetzelfde woord hebben.
* Het invoer schema van de woordenlijst gegevens sets moet exact overeenkomen, inclusief kolom namen en kolom typen. 
* De kolom **id** en de kolom **DF** moeten van het type geheel getal zijn. 
* De kolom **IDF** moet van het type float zijn.

> [!Note]
> Koppel de gegevens uitvoer niet rechtstreeks aan de Train model-module. U moet vrije-tekst kolommen verwijderen voordat ze in het Train-model worden ingevoerd. Anders worden de vrije-tekst kolommen behandeld als categorische-functies.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.
