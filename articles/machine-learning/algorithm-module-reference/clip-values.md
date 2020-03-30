---
title: Waarden inperken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Clipwaarden in Azure Machine Learning om uitschieters te detecteren en hun waarden te knippen of te vervangen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 6466cea9fe04bb308a670cb03fd3de5314758142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456604"
---
# <a name="clip-values"></a>Waarden inperken

In dit artikel wordt een module van Azure Machine Learning designer (preview) beschreven.

Gebruik de module Clipwaarden om gegevenswaarden die zich boven of onder een opgegeven drempelwaarde bevinden, te identificeren en optioneel te vervangen door een gemiddelde, een constante of andere vervangingswaarde.  

U verbindt de module met een gegevensset met de getallen die u wilt knippen, kiest de kolommen waarmee u wilt werken en stelt vervolgens een drempel- of bereik van waarden en een vervangingsmethode in. De module kan alleen de resultaten uitvoeren of de gewijzigde waarden die aan de oorspronkelijke gegevensset zijn toegevoegd.

## <a name="how-to-configure-clip-values"></a>Clipwaarden configureren

Identificeer voordat u begint de kolommen die u wilt knippen en de methode die u wilt gebruiken. We raden u aan eerst een uitknipmethode te testen op een kleine subset van gegevens.

De module past dezelfde criteria en vervangingsmethode toe op **alle** kolommen die u in de selectie opneemt. Sluit daarom kolommen uit die u niet wilt wijzigen.

Als u uitknipmethoden of andere criteria op bepaalde kolommen moet toepassen, moet u voor elke set vergelijkbare kolommen een nieuw exemplaar **van clipwaarden** gebruiken.

1.  Voeg de module **Clipwaarden** toe aan uw pijplijn en sluit deze aan op de gegevensset die u wilt wijzigen. U deze module vinden onder **Gegevenstransformatie**, in de categorie **Schalen en Verkleinen.** 
  
1.  Gebruik in **Lijst met kolommen**de kolomkiezer om de kolommen te kiezen waarop **clipwaarden** worden toegepast.  
  
1.  Kies **voor Set van drempelwaarden**een van de volgende opties in de vervolgkeuzelijst. Deze opties bepalen hoe u de boven- en ondergrenzen instelt voor acceptabele waarden versus waarden die moeten worden geknipt.  
  
    - **ClipPeaks:** Wanneer u waarden vastmaakt aan pieken, geeft u alleen een bovengrens op. Waarden die groter zijn dan die grenswaarde worden vervangen.
  
    -  **ClipSubpeaks**: Wanneer u waarden op subpieken knipt, geeft u alleen een ondergrens op. Waarden die lager zijn dan die grenswaarde worden vervangen.  
  
    - **ClipPeaksAndSubpeaks**: Wanneer u waarden vastmaakt aan pieken en subpieken, u zowel de boven- als ondergrenzen opgeven. Waarden die zich buiten dat bereik bevinden, worden vervangen. Waarden die overeenkomen met de grenswaarden worden niet gewijzigd.
  
1.  Afhankelijk van uw selectie in de vorige stap u de volgende drempelwaarden instellen: 

    + **Lagere drempelwaarde**: alleen weergegeven als u **ClipSubPeaks** kiest
    + **Bovendrempel**: alleen weergegeven als u **ClipPeaks** kiest
    + **Drempel:** alleen weergegeven als u **ClipPeaksAndSubPeaks** kiest

    Kies voor elk drempeltype **Constant** of **Percentiel**.

1. Als u **Constant**selecteert, typt u de maximum- of minimumwaarde in het tekstvak. Stel dat u weet dat de waarde 999 is gebruikt als tijdelijke waarde. U **Constant** kiezen voor de bovengrens en 999 typen in **Constante waarde voor bovengrenswaarde**.
  
1. Als u **Percentiel kiest,** beperkt u de kolomwaarden tot een percentiel bereik. 

    Stel dat u alleen de waarden in het bereik van 10-80 percentiel wilt behouden en alle andere waarden wilt vervangen. U kiest **Percentiel**en typt vervolgens 10 voor **Percentielwaarde voor lagere drempelwaarde**en typt 80 voor **Percentielwaarde voor bovengrenswaarde**. 

    Zie de sectie op [percentielen](#examples-for-clipping-using-percentiles) voor enkele voorbeelden van het gebruik van percentielbereiken.  
  
1.  Definieer een vervangende waarde.

    Getallen die precies overeenkomen met de grenzen die u hebt opgegeven, worden beschouwd als binnen het toegestane bereik van waarden en worden dus niet vervangen. Alle getallen die buiten het opgegeven bereik vallen, worden vervangen door de vervangingswaarde. 
  
    + **Vervangingswaarde voor pieken**: hiermee definieert u de waarde die moet worden vervangen door alle kolomwaarden die groter zijn dan de opgegeven drempelwaarde.  
    + **Vervangingswaarde voor subpieken**: definieert de waarde die moet worden gebruikt als vervanging voor alle kolomwaarden die lager zijn dan de opgegeven drempelwaarde.  
    + Als u de optie **ClipPeaksAndSubpeaks** gebruikt, u afzonderlijke vervangingswaarden opgeven voor de bovenste en onderste geknipte waarden.  

    De volgende vervangingswaarden worden ondersteund:  
  
    -   **Drempelwaarde**: vervangt geknipte waarden door de opgegeven drempelwaarde.  
  
    -   **Gemiddelde:** Hiermee worden geknipte waarden vervangen door het gemiddelde van de kolomwaarden. Het gemiddelde wordt berekend voordat waarden worden geknipt.  
  
    -   **Mediaan**: hiermee worden geknipte waarden vervangen door de mediaan van de kolomwaarden. De mediaan wordt berekend voordat waarden worden geknipt.   
  
    -   **Vermist**. Hiermee worden geknipte waarden vervangen door de ontbrekende (lege) waarde.  
  
1.  **Indicatorkolommen toevoegen:** Selecteer deze optie als u een nieuwe kolom wilt genereren die u vertelt of de opgegeven uitknipbewerking al dan niet is toegepast op de gegevens in die rij. Deze optie is handig wanneer u een nieuwe set knip- en substitutiewaarden test.  
  
1. **Vlag overschrijven:** geef aan hoe u de nieuwe waarden wilt genereren. **Clipwaarden** maken standaard een nieuwe kolom met de piekwaarden die zijn geknipt tot de gewenste drempelwaarde. Nieuwe waarden overschrijven de oorspronkelijke kolom.  
  
    Als u de oorspronkelijke kolom wilt behouden en een nieuwe kolom met de geknipte waarden wilt toevoegen, schakelt u de selectie van deze optie uit.  
  
1.  Verzend de pijplijn.  
  
    Klik met de rechtermuisknop op de module **Clipwaarden** en selecteer **Visualiseren** of selecteer de module en schakel naar het tabblad **Uitvoer** in het rechterdeelvenster, klik op het histogrampictogram in de **poortuitvoer,** om de waarden te controleren en ervoor te zorgen dat de knipbewerking aan uw verwachtingen voldoet.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Voorbeelden voor knippen met percentiels

Als u wilt begrijpen hoe knippen door percentiels werkt, u een gegevensset met 10 rijen overwegen, die elk van de waarden 1-10 hebben.  
  
- Als u percentiel als bovengrens gebruikt, moet 90 procent van alle waarden in de gegevensset bij de waarde voor het 90e percentiel lager zijn dan die waarde.  
  
- Als u percentiel als onderste drempelwaarde gebruikt, moet bij de waarde voor het 10e percentiel 10 procent van alle waarden in de gegevensset lager zijn dan die waarde.  
  
1.  Kies **ClipPeaksAndSubPeaks**voor **set drempelwaarden**.  
  
1.  Kies **Percentiel**en voor **Percentielgetal**voor **Bovendrempel**90.  
  
1.  Kies **Ontbrekende waarde**voor **Bovenvervangende waarde**.  
  
1.  Kies **Percentiel**en voor **Percentielgetal**voor **Onderdrempel**10.  
  
1.  Kies **Ontbrekende waarde**voor **Lagere vervangingswaarde**.  
  
1.  Schakel de optie **Overschrijfvlag uit**en selecteer de optie **Indicatorkolom toevoegen**.  
  
Probeer nu dezelfde pijplijn met 60 als de bovenste percentieldrempel en 30 als de onderste percentieldrempel en gebruik de drempelwaarde als vervangingswaarde. In de volgende tabel worden deze twee resultaten vergeleken:  
  
1.  Vervangen door ontbrekende; Bovendrempel = 90; Onderdrempel = 20  
  
1.  Vervangen door drempel; Bovenste percentiel = 60; Lager percentiel = 40  
  
|Oorspronkelijke gegevens|Vervangen door ontbrekende|Vervangen door drempelwaarde|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, ONWAAR<br /><br /> 4.<br /><br /> 5, VALSE<br /><br /> 6, VALS<br /><br /> 7, VALSE<br /><br /> 8.<br /><br /> 9.<br /><br /> TRUE|4, WAAR<br /><br /> 4, WAAR<br /><br /> 4, WAAR<br /><br /> 4, WAAR<br /><br /> 5, VALSE<br /><br /> 6, VALS<br /><br /> 7, WAAR<br /><br /> 7, WAAR<br /><br /> 7, WAAR<br /><br /> 7, WAAR| 
 
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
