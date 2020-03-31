---
title: Referentie van de Feature Hashing-module
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de functiehashing-module in de Azure Machine Learning om tekstgegevens te verbeteren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7178417a5c20afe5b1ed02bc526ec174704962df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456247"
---
# <a name="feature-hashing-module-reference"></a>Referentie van de Feature Hashing-module

In dit artikel wordt een module beschreven die is opgenomen in Azure Machine Learning designer (preview).

Gebruik de module Feature Hashing om een stroom Engelse tekst om te zetten in een reeks geheelgetalfuncties. U deze gehashte functieset vervolgens doorgeven aan een machine learning-algoritme om een tekstanalysemodel te trainen.

De functie hashing functionaliteit die in deze module is gebaseerd op de nimbusml kader. Zie [de klasse NgramHash voor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest)meer informatie.

## <a name="what-is-feature-hashing"></a>Wat is functie hashing?

Feature hashing werkt door het omzetten van unieke tokens in gehele getallen. Het werkt op de exacte tekenreeksen die u als input levert en voert geen taalanalyse of voorbewerking uit. 

Neem bijvoorbeeld een reeks eenvoudige zinnen zoals deze, gevolgd door een sentimentscore. Stel dat u deze tekst wilt gebruiken om een model te maken.

|Gebruikerstekst|Sentiment|
|--------------|---------------|
|Ik hield van dit boek|3|
|Ik haatte dit boek.|1|
|Dit boek was geweldig|3|
|Ik hou van boeken|2|

Intern maakt de module Feature Hashing een woordenboek van n-grammen. Bijvoorbeeld, de lijst van bigrammen voor deze dataset zou iets als volgt zijn:

|Term (bigrammen)|Frequency|
|------------|---------------|
|Dit boek|3|
|Ik hield van|1|
|Ik haatte het.|1|
|Ik hou van|1|

U de grootte van de n-grammen met behulp van de **N-gram** eigenschap. Als u kiest voor bigrams, worden unigrammen ook berekend. Het woordenboek zou ook enkele termen als deze:

|Term (unigrammen)|Frequency|
|------------|---------------|
|boek|3|
|I|3|
|boeken|1|
|Was|1|

Nadat het woordenboek is gemaakt, zet de module Feature Hashing de woorden van het woordenboek om in hashwaarden. Vervolgens wordt berekend of een functie in elk geval is gebruikt. Voor elke rij tekstgegevens wordt in de module een reeks kolommen uitgevoerd, één kolom voor elke gehashte functie.

Na hashing zien de functiekolommen er bijvoorbeeld ongeveer als volgt uit:

|Waardering|Hashing-functie 1|Hashing functie 2|Hashing-functie 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Als de waarde in de kolom 0 is, bevat de rij de gehashte functie niet.
* Als de waarde 1 is, bevat de rij de functie.

Met feature hashing u tekstdocumenten van variabele lengte weergeven als numerieke functievectoren van gelijke lengte om de dimensionaliteit te verminderen. Als u de tekstkolom voor training hebt geprobeerd te gebruiken zoals deze is, wordt deze behandeld als een categorische functiekolom met veel verschillende waarden.

Numerieke uitvoer maakt het ook mogelijk om veelgebruikte machine learning-methoden te gebruiken, waaronder classificatie, clustering en het ophalen van informatie. Omdat lookup-bewerkingen integer hashes kunnen gebruiken in plaats van vergelijkingen, is het verkrijgen van de functiegewichten ook veel sneller.

## <a name="configure-the-feature-hashing-module"></a>De Module Feature Hashing configureren

1.  Voeg de module Feature Hashing toe aan uw pijplijn in de ontwerper.

1. Verbind de gegevensset met de tekst die u wilt analyseren.

    > [!TIP]
    > Omdat functiehashing geen lexicale bewerkingen uitvoert, zoals stemming of afgekaptheid, u soms betere resultaten behalen door tekst voor te bewerken voordat u functiehashing toepast. 

1. **Doelkolommen** instellen op de tekstkolommen die u wilt converteren naar gehashte functies. Houd er rekening mee dat:

    * De kolommen moeten het tekenreeksgegevenstype zijn.
    
    * Het kiezen van meerdere tekstkolommen kan een aanzienlijke invloed hebben op de functiedimensionaliteit. Het aantal kolommen voor een hash van 10 bits gaat bijvoorbeeld van 1024 voor een enkele kolom naar 2048 voor twee kolommen.

1. Gebruik **Hashing bitsize** om het aantal bits op te geven dat u moet gebruiken wanneer u de hashtabel maakt.
    
    De standaardbitgrootte is 10. Voor veel problemen is deze waarde voldoende. U hebt mogelijk meer ruimte nodig om botsingen te voorkomen, afhankelijk van de grootte van de n-gramwoordenschat in de trainingstekst.
    
1. Voer **voor N-grammen**een getal in dat de maximale lengte van de n-grammen definieert die aan het trainingswoordenboek moet worden toegevoegd. Een n-gram is een opeenvolging van *n* woorden, behandeld als een unieke eenheid.

    Als u bijvoorbeeld 3 unigrammen, bigrammen en trigrammen invoert, worden er unigrammen en trigrammen gemaakt.

1. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Nadat de verwerking is voltooid, wordt in de module een getransformeerde gegevensset uitgevoerd waarin de oorspronkelijke tekstkolom is geconverteerd naar meerdere kolommen. Elke kolom vertegenwoordigt een functie in de tekst. Afhankelijk van hoe belangrijk het woordenboek is, kan de resulterende gegevensset groot zijn:

|Kolomnaam 1|Kolomtype 2|
|-------------------|-------------------|
|GEBRUIKERSTEKST|Kolom Oorspronkelijke gegevens|
|Sentiment|Kolom Oorspronkelijke gegevens|
|USERTEXT - Hashing functie 1|Gehashte functiekolom|
|USERTEXT - Hashing functie 2|Gehashte functiekolom|
|USERTEXT - Hashing functie n|Gehashte functiekolom|
|USERTEXT - Hashing functie 1024|Gehashte functiekolom|

Nadat u de getransformeerde gegevensset hebt gemaakt, u deze gebruiken als invoer voor de module Treinmodel.
 
## <a name="best-practices"></a>Aanbevolen procedures

De volgende aanbevolen procedures kunnen u helpen het meeste uit de module Feature Hashing te halen:

* Voeg een preprocesstekstmodule toe voordat u Feature Hashing gebruikt om de invoertekst vooraf te verwerken. 

* Voeg een module Kolommen selecteren toe nadat de module Feature Hashing de tekstkolommen uit de uitvoergegevensset wilt verwijderen. U hebt de tekstkolommen niet nodig nadat de hashing-functies zijn gegenereerd.
    
* Overweeg deze voorbewerkingsopties voor tekst te gebruiken om de resultaten te vereenvoudigen en de nauwkeurigheid te verbeteren:

    * Woord breken
    * Woordverwijdering stoppen
    * Case normalisatie
    * Verwijdering van interpunctie en speciale tekens
    * Stemming  

De optimale set van voorbewerkingsmethoden toe te passen in elke oplossing is afhankelijk van domein, woordenschat, en zakelijke behoefte. pijplijn met uw gegevens om te zien welke tekstverwerkingsmethoden het meest effectief zijn.

## <a name="next-steps"></a>Volgende stappen
            
Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning 
