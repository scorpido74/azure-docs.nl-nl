---
title: 'Gesplitste gegevens: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Gesplitste gegevens in Azure Machine Learning om een gegevensset in twee verschillende sets te verdelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9eba6f2c47629b708dde4a5a2888b76dbd24b4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455890"
---
# <a name="split-data-module"></a>Module Gesplitste gegevens

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik de module Gesplitste gegevens om een gegevensset in twee afzonderlijke sets te verdelen.

Deze module is handig wanneer u gegevens moet scheiden in trainings- en testsets. U ook de manier aanpassen waarop gegevens worden verdeeld. Sommige opties ondersteunen randomisatie van gegevens. Andere zijn afgestemd op een bepaald gegevenstype of modeltype.

## <a name="configure-the-module"></a>De module configureren

> [!TIP]
> Voordat u de splitsingsmodus kiest, leest u alle opties om het type splitsing te bepalen dat u nodig hebt.
> Als u de splitsingsmodus wijzigt, kunnen alle andere opties worden gereset.

1. Voeg de module **Gesplitste gegevens** toe aan uw pijplijn in de ontwerper. U deze module vinden onder **Gegevenstransformatie**, in de categorie **Voorbeeld en Splitsen.**

1. **Splitsmodus**: Kies een van de volgende modi, afhankelijk van het type gegevens dat u hebt en hoe u deze wilt verdelen. Elke splitsingsmodus heeft verschillende opties.

   - **Gesplitste rijen:** gebruik deze optie als u de gegevens in twee delen wilt verdelen. U het percentage gegevens opgeven dat in elke splitsing moet worden geplaatst. Standaard worden de gegevens 50/50 gedeeld.

     U ook de selectie van rijen in elke groep randomiseren en gestratificeerde steekproeven gebruiken. In gelaagde steekproeven moet u één kolom met gegevens selecteren waarvoor u wilt dat waarden gelijk worden verdeeld over de twee resultaatgegevenssets.  

   - **Gedeeld reguliere expressie:** kies deze optie wanneer u uw gegevensset wilt delen door één kolom op een waarde te testen.

     Als u bijvoorbeeld het sentiment analyseert, u controleren op de aanwezigheid van een bepaalde productnaam in een tekstveld. U de gegevensset vervolgens verdelen in rijen met de doelproductnaam en -rijen zonder de doelproductnaam.

   - **Relatieve expressiesplitsing**: gebruik deze optie wanneer u een voorwaarde wilt toepassen op een getalkolom. Het getal kan een datum/tijdveld zijn, een kolom met leeftijds- of dollarbedragen of zelfs een percentage. U bijvoorbeeld uw gegevensset verdelen op basis van de kosten van de items, mensen groeperen op leeftijdscategorieën of gegevens scheiden op basis van een kalenderdatum.

### <a name="split-rows"></a>Rijen splitsen

1. Voeg de module [Gesplitste gegevens](./split-data.md) toe aan uw pijplijn in de ontwerper en verbind de gegevensset die u wilt splitsen.
  
1. Selecteer **Rijen splitsen**voor de **modus Splitsen**. 

1. **Fractie van rijen in de eerste uitvoergegevensset**: Gebruik deze optie om te bepalen hoeveel rijen naar de eerste (linker) uitvoer gaan. Alle andere rijen gaan in de tweede (rechterkant) uitgang.

   De verhouding vertegenwoordigt het percentage rijen dat naar de eerste uitvoergegevensset wordt verzonden, dus u moet een decimaal getal invoeren tussen 0 en 1.
     
   Als u bijvoorbeeld **0,75** als waarde invoert, wordt de gegevensset 75/25 gesplitst. In deze splitsing wordt 75 procent van de rijen naar de eerste uitvoergegevensset verzonden. De resterende 25 procent wordt naar de tweede uitvoergegevensset verzonden.
  
1. Selecteer de optie **Randomized split** als u de selectie van gegevens in de twee groepen wilt randomiseren. Dit is de voorkeursoptie wanneer u trainings- en testgegevenssets maakt.

1. **Random Seed**: Voer een niet-negatieve gehele waarde in om de pseudowillekeurige reeks van te gebruiken instanties te starten. Dit standaardzaad wordt gebruikt in alle modules die willekeurige getallen genereren. 

   Het opgeven van een zaadmaakt de resultaten reproduceerbaar. Als u de resultaten van een gesplitste bewerking moet herhalen, moet u een zaadje opgeven voor de random number generator. Anders wordt het willekeurige zaad standaard ingesteld op **0,** wat betekent dat de initiële zaadwaarde wordt verkregen uit de systeemklok. Als gevolg hiervan kan de verdeling van gegevens elke keer dat u een splitsing uitvoert, iets anders zijn. 

1. **Gestratificeerde splitsing**: Stel deze optie in op **True** om ervoor te zorgen dat de twee uitvoergegevenssets een representatief voorbeeld van de waarden in de *kolom strata* of *de kolom stratificatie*bevatten . 

   Met gestratificeerde steekproeven worden de gegevens zodanig verdeeld dat elke uitvoergegevensset ongeveer hetzelfde percentage van elke doelwaarde krijgt. U er bijvoorbeeld voor zorgen dat uw trainings- en testsets ruwweg in evenwicht zijn met betrekking tot de uitkomst of een andere kolom (zoals geslacht).

1. Verzend de pijplijn.


## <a name="select-a-regular-expression"></a>Een reguliere expressie selecteren

1. Voeg de module [Gesplitste gegevens](./split-data.md) toe aan uw pijplijn en sluit deze aan als invoer op de gegevensset die u wilt splitsen.  
  
1. Selecteer Voor **de modus Splitsen**de optie Regelmatige **expressiesplitsing**.

1. Voer **in** het vak Reguliere expressie een geldige reguliere expressie in. 
  
   De reguliere expressie moet de syntaxis van Python volgen voor reguliere expressies.

1. Verzend de pijplijn.

   Op basis van de reguliere expressie die u verstrekt, is de gegevensset verdeeld in twee sets rijen: rijen met waarden die overeenkomen met de expressie en alle resterende rijen. 

In de volgende voorbeelden wordt uitgelegd hoe u een gegevensset verdelen met de optie **Reguliere expressie.** 

### <a name="single-whole-word"></a>Eén geheel woord 

In dit voorbeeld worden in de eerste `Gryphon` gegevensset `Text`alle rijen weergegeven die de tekst in de kolom bevatten . Het zet andere rijen in de tweede uitvoer van **Split Data**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Subtekenreeks

In dit voorbeeld wordt in een bepaalde positie in de tweede kolom van de gegevensset naar de opgegeven tekenreeks gezocht. De positie wordt hier aangeduid met de indexwaarde van 1. De overeenkomst is hoofdlettergevoelig.

```text
(\1) ^[a-f]
```

De eerste resultaatgegevensset bevat alle rijen waarin de indexkolom `d`begint `e` `f`met een van deze tekens: `a`, `b`, `c`, , . Alle andere rijen zijn gericht op de tweede uitvoer.

## <a name="select-a-relative-expression"></a>Een relatieve expressie selecteren

1. Voeg de module [Gesplitste gegevens](./split-data.md) toe aan uw pijplijn en sluit deze aan als invoer op de gegevensset die u wilt splitsen.
  
1. Selecteer **Relatieve expressie**voor de **modus Splitsen**.
  
1. Voer in het vak **Relationele expressie** een expressie in die een vergelijkingsbewerking uitvoert op één kolom.

   Voor **numerieke kolom**:
   - De kolom bevat getallen van elk numeriek gegevenstype, inclusief datum- en tijdgegevenstypen.
   - De expressie kan verwijzen naar maximaal één kolomnaam.
   - Gebruik de ampersand `&`karakter, , voor de en operatie. Gebruik het pijpteken, `|`voor de OR-bewerking.
   - De volgende marktdeelnemers `<`worden `>` `<=`ondersteund: , , , `>=` `==`, `!=`.
   - U bewerkingen niet `(` `)`groeperen met en.
   
   Voor **tekenreekskolom**:
   - De volgende operatoren `==`worden `!=`ondersteund: , .

1. Verzend de pijplijn.

   De expressie verdeelt de gegevensset in twee reeksen rijen: rijen met waarden die voldoen aan de voorwaarde en alle resterende rijen.

In de volgende voorbeelden wordt uitgelegd hoe u een gegevensset verdeelt met de optie **Relatieve expressie** in de module **Gesplitste gegevens.**  

### <a name="calendar-year"></a>Kalenderjaar

Een veelvoorkomend scenario is om een gegevensset door jaren te verdelen. Met de volgende expressie worden alle rijen `Year` geselecteerd `2010`waarin de waarden in de kolom groter zijn dan .

```text
\"Year" > 2010
```

De datumexpressie moet rekening houden met alle datumdelen die zijn opgenomen in de gegevenskolom. De notatie van datums in de gegevenskolom moet consistent zijn. 

In een datumkolom die de `mmddyyyy`indeling gebruikt, moet de expressie bijvoorbeeld als volgt zijn:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Kolomindex

De volgende expressie laat zien hoe u de kolomindex gebruiken om alle rijen in de eerste kolom van de gegevensset te selecteren die waarden bevatten die lager zijn dan of gelijk zijn aan 30, maar niet gelijk zijn aan 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
