---
title: 'Gesplitste gegevens: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module gegevens splitsen in Azure Machine Learning om een gegevensset in twee verschillende sets te verdelen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b707971f0b150bce63b4dd0ffa4e55dc3b3d6bac
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313953"
---
# <a name="split-data-module"></a>Module voor splitsen van gegevens

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een gegevensset in twee verschillende sets te verdelen.

Deze module is vooral nuttig wanneer u gegevens moet scheiden in trainings-en test sets. U kunt de manier aanpassen waarop gegevens ook worden gedeeld. Sommige opties ondersteunen het wille keurig maken van gegevens; andere zijn aangepast aan een bepaald gegevens type of model type.

## <a name="how-to-configure"></a>Configureren

> [!TIP]
> Voordat u de Splits modus kiest, moet u alle opties lezen om te bepalen welk type splitsing u nodig hebt.
> Als u de Splits modus wijzigt, kunnen alle andere opties opnieuw worden ingesteld.

1. Voeg de module **gegevens splitsen** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden onder **gegevens transformatie**in de categorie voor **Beeld en splitsen** .

2. **Splits modus**: Kies een van de volgende modi, afhankelijk van het type gegevens dat u hebt, en hoe u deze wilt delen. Elke Splits modus heeft verschillende opties. Klik op de volgende onderwerpen voor gedetailleerde instructies en voor beelden. 

    - **Rijen splitsen**: gebruik deze optie als u de gegevens wilt splitsen in twee delen. U kunt opgeven welk percentage van de gegevens in elke splitsing moet worden geplaatst, maar standaard worden de gegevens gedeeld 50-50.

        U kunt ook de selectie van rijen in elke groep wille keurig opwaarderen en stratified-steek proeven gebruiken. In stratified-steek proeven moet u één kolom met gegevens selecteren waarvoor u wilt dat waarden gelijkmatig worden verdeeld over de twee resultaat gegevens sets.  

    - **Reguliere expressie splitsen**  Kies deze optie als u de gegevensset wilt verdelen door één kolom te testen op een waarde.

        Als u bijvoorbeeld sentiment analyseert, kunt u op de aanwezigheid van een bepaalde product naam in een tekst veld controleren en vervolgens de gegevensset onderverdelen in rijen met de doel product naam en die zonder.

    - **Relatieve expressie splitsen**: gebruik deze optie wanneer u een voor waarde op een kolom getal wilt Toep assen. Het getal kan een datum/tijd-veld, een kolom met leeftijds-of dollar bedragen of zelfs een percentage zijn. Stel dat u uw gegevensset wilt delen, afhankelijk van de kosten van de items, het groeperen van personen op leeftijds bereiken of het scheiden van gegevens op basis van een kalender datum.

### <a name="split-rows"></a>Rijen splitsen

1.  Voeg de module [gegevens splitsen](./split-data.md) toe aan uw pijp lijn in de ontwerp functie en verbind de gegevensset die u wilt splitsen.
  
2.  Voor de **Splits modus**kiest u **rijen splitsen**. 

3.  **Het gedeelte van de rijen in de eerste uitvoer gegevensset**. Gebruik deze optie om te bepalen hoeveel rijen de eerste (linkse) uitvoer gaan gebruiken. Alle andere rijen gaan naar de tweede uitvoer (rechts).

    De ratio vertegenwoordigt het percentage rijen dat wordt verzonden naar de eerste uitvoer gegevensset, dus u moet een decimaal getal tussen 0 en 1 invoeren.
     
     Als u bijvoorbeeld 0,75 typt als de waarde, zou de gegevensset moeten worden gesplitst met behulp van een 75:25-verhouding, met 75% van de rijen die worden verzonden naar de eerste uitvoer gegevensset en 25% naar de tweede uitvoer gegevensset wordt verzonden.
  
4. Selecteer de optie **wille keurig splitsen** als u selectie van gegevens wilt wille keurig in de twee groepen. Dit is de voorkeurs optie bij het maken van gegevens sets voor training en testen.

5.  **Wille keurige Seed**: Typ een niet-negatief geheel getal om de Pseudorandom-volg orde van de exemplaren die moeten worden gebruikt, te initialiseren. Deze standaard seeding wordt gebruikt in alle modules die wille keurige getallen genereren. 

     Als u een Seed opgeeft, worden de resultaten doorgaans reproduceerbaar. Als u de resultaten van een splits bewerking moet herhalen, moet u een Seed opgeven voor de generator van wille keurige getallen. Anders wordt de wille keurige Seed standaard ingesteld op 0, wat betekent dat de initiële seedwaarde wordt opgehaald uit de systeem klok. Als gevolg hiervan kan de verdeling van gegevens enigszins verschillen telkens wanneer u een splitsing uitvoert. 

6. **Stratified splitsen**: Stel deze optie in op **True** om ervoor te zorgen dat de twee uitvoer gegevens sets een representatief voor beeld bevatten van de waarden in de kolom *Strata* of de tabel *stratificatie sleutel*. 

    Met stratified-steek proeven worden de gegevens zo verdeeld dat elke uitvoer gegevensset ongeveer hetzelfde percentage van elke doel waarde ophaalt. U kunt er bijvoorbeeld voor zorgen dat uw trainings-en test sets ongeveer worden gesaldeerd met betrekking tot het resultaat of met betrekking tot een andere kolom, zoals gender.

7. Voer de pijplijn uit.


## <a name="regular-expression-split"></a>Reguliere expressie splitsen

1.  Voeg de module [gegevens splitsen](./split-data.md) toe aan uw pijp lijn en verbind deze als invoer met de gegevensset die u wilt splitsen.  
  
2.  Selecteer voor de **Splits modus** **reguliere expressie splitsen**.

3. Typ in het vak **reguliere expressie** een geldige reguliere expressie. 
  
   De reguliere expressie moet de syntaxis van de reguliere expressie van python volgen.


4. Voer de pijplijn uit.

    Op basis van de reguliere expressie die u opgeeft, wordt de gegevensset onderverdeeld in twee sets rijen: rijen met waarden die overeenkomen met de expressie en alle resterende rijen. 

## <a name="relative-expression-split"></a>De relatieve expressie is gesplitst.

1. Voeg de module [gegevens splitsen](./split-data.md) toe aan uw pijp lijn en verbind deze als invoer met de gegevensset die u wilt splitsen.
  
2. Voor de **Splits modus**selecteert u **relatieve expressie splitsen**.
  
3. Typ in het tekstvak **relationele expressie** een expressie waarmee een vergelijkings bewerking wordt uitgevoerd op één kolom:


 - Numerieke kolom:
    - De kolom bevat getallen van elk numeriek gegevens type, met inbegrip van de datum/tijd-gegevens typen.

    - De expressie kan naar Maxi maal één kolom naam verwijzen.

    - Gebruik het ampersand teken (&) voor de en-bewerking en gebruik het sluis teken (|) voor de OR-bewerking.

    - De volgende Opera tors worden ondersteund: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - U kunt geen bewerkingen groeperen met behulp van `(` en `)`.

 - Teken reeks kolom: 
    - De volgende Opera tors worden ondersteund: `==`, `!=`



4. Voer de pijplijn uit.

    De expressie splitst de gegevensset in twee sets rijen: rijen met waarden die voldoen aan de voor waarde en alle resterende rijen.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 