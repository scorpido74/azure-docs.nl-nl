---
title: N-gramfuncties extraheren uit naslaginformatie over tekstmodule
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Module N-Gram extraheren in de Azure Machine Learning om tekstgegevens te verbeteren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: efe09c1d516b37c23b024e07ae387772fa7e5992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477609"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>N-gramfuncties extraheren uit naslaginformatie over tekstmodule

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview). Gebruik de module N-gram extraheren uit de tekstmodule om ongestructureerde tekstgegevens *te verwijderen.* 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Configuratie van de n-gramfuncties uit tekstmodule extraheren

De module ondersteunt de volgende scenario's voor het gebruik van een n-gramwoordenboek:

* [Maak een nieuwe n-gram woordenboek](#create-a-new-n-gram-dictionary) uit een kolom met vrije tekst.

* [Gebruik een bestaande set tekstfuncties](#use-an-existing-n-gram-dictionary) om een gratis tekstkolom te maken.

* [Score of publiceer een model](#score-or-publish-a-model-that-uses-n-grams) dat n-grammen gebruikt.

### <a name="create-a-new-n-gram-dictionary"></a>Een nieuwe n-gramwoordenboek maken

1.  Voeg de n-gramfuncties uit de tekstmodule extraheren toe aan uw pijplijn en verbind de gegevensset met de tekst die u wilt verwerken.

1.  Gebruik **tekstkolom** om een kolom van tekenreekstype te kiezen die de tekst bevat die u wilt extraheren. Omdat de resultaten breed zijn, u slechts één kolom tegelijk verwerken.

1. Stel **de woordenschatmodus** in **om te maken** om aan te geven dat u een nieuwe lijst met n-gramfuncties maakt. 

1. Stel **de grootte van N-Gram's** in om de *maximale* grootte van de n-grammen aan te geven om te extraheren en op te slaan. 

    Als u bijvoorbeeld 3 unigrammen, bigrammen en trigrammen invoert, worden er unigrammen en trigrammen gemaakt.

1. **De wegingsfunctie** bepaalt hoe u de vector van de documentfunctie moet maken en hoe u woordenschat uit documenten extraheren.

    * **Binair gewicht**: Wijst een binaire aanwezigheidswaarde toe aan de geëxtraheerde n-grammen. De waarde voor elke n-gram is 1 wanneer deze in het document voorkomt en 0 anders.

    * **TF Gewicht**: Wijst een term frequentie (TF) score toe aan de geëxtraheerde n-grammen. De waarde voor elke n-gram is de gebeurtenisfrequentie in het document.

    * **IDF Gewicht**: Wijst een omgekeerde documentfrequentie (IDF) score toe aan de geëxtraheerde n-grammen. De waarde voor elke n-gram is het logboek van corpusgrootte dat door zijn voorkomende frequentie in het gehele corpus wordt gedeeld.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF Gewicht**: Wijst een term frequentie/omgekeerde documentfrequentie (TF/IDF) score toe aan de geëxtraheerde n-grammen. De waarde voor elke n-gram is de TF-score vermenigvuldigd met de IDF-score.

1. **Stel De minimumwoordlengte** in op het minimumaantal letters dat in *een enkel woord* in een n-gram kan worden gebruikt.

1. Gebruik **Maximale woordlengte** om het maximumaantal letters in te stellen dat in een *enkel woord* in een n-gram kan worden gebruikt.

    Standaard zijn maximaal 25 tekens per woord of token toegestaan.

1. Gebruik **minimum n-gram document absolute frequentie** om de minimale voorvallen die nodig zijn voor een n-gram worden opgenomen in de n-gram woordenboek in te stellen. 

    Als u bijvoorbeeld de standaardwaarde van 5 gebruikt, moet elke n-gram ten minste vijf keer in het corpus worden weergegeven om in het n-gramwoordenboek te worden opgenomen. 

1.  Stel **de maximale n-gram-documentverhouding** in op de maximale verhouding van het aantal rijen dat een bepaalde n-gram bevat, boven het aantal rijen in het totale corpus.

    Een verhouding van 1 geeft bijvoorbeeld aan dat, zelfs als er in elke rij een specifieke n-gram aanwezig is, de n-gram kan worden toegevoegd aan het n-gramwoordenboek. Meer typisch, een woord dat optreedt in elke rij zou worden beschouwd als een ruis woord en zou worden verwijderd. Als u domeinafhankelijke ruiswoorden wilt filteren, probeert u deze verhouding te verkleinen.

    > [!IMPORTANT]
    > De mate van voorkomen van bepaalde woorden is niet uniform. Het varieert van document tot document. Als u bijvoorbeeld opmerkingen van klanten over een specifiek product analyseert, is de productnaam mogelijk zeer hoog en dicht bij een ruiswoord, maar een belangrijke term in andere contexten.

1. Selecteer de optie **N-gram-functievectoren normaliseren** om de functievectoren te normaliseren. Als deze optie is ingeschakeld, wordt elke n-gram-functievector gedeeld door de L2-norm.

1. Verzend de pijplijn.

### <a name="use-an-existing-n-gram-dictionary"></a>Een bestaand n-gramwoordenboek gebruiken

1.  Voeg de N-Gram-onderdelen uit tekstmodule extraheren toe aan uw pijplijn en verbind de gegevensset met de tekst die u wilt verwerken met de **poort Gegevensset.**

1.  Gebruik **tekstkolom** om de tekstkolom te selecteren die de tekst bevat die u wilt opdoen. Standaard selecteert de module alle kolommen van **teksttekenreeks**. Voor de beste resultaten verwerkt u één kolom tegelijk.

1. Voeg de opgeslagen gegevensset toe die een eerder gegenereerde n-gramwoordenboek bevat en sluit deze aan op de **woordenschatpoort invoer.** U ook de **woordenschatuitvoer van het resultaat** van een upstream-instantie van de module N-Gram-onderdelen extraheren uit tekst aansluiten.

1. Selecteer **bij de woordenschatmodus**de optie **Alleen-update** lezen in de vervolgkeuzelijst.

   De optie **ReadOnly** vertegenwoordigt het invoercorpus voor de invoerwoordenschat. In plaats van termfrequenties uit de nieuwe tekstgegevensset (aan de linkerkant) te berekenen, worden de n-gramgewichten uit de invoerwoordenschat toegepast zoals het is.

   > [!TIP]
   > Gebruik deze optie wanneer u een tekstclassificatie scoort.

1.  Zie de eigenschappenbeschrijvingen in de [vorige sectie](#create-a-new-n-gram-dictionary)voor alle andere opties.

1.  Verzend de pijplijn.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Een model scoren of publiceren dat n-grammen gebruikt

1.  Kopieer de **n-gramfuncties extraheren uit de tekstmodule** van de trainingsgegevensstroom naar de gegevensstroom voor scores.

1.  Verbind de **resultaatwoordenschatuitvoer** van de trainingsgegevensstroom **met invoerwoordenschat** op de gegevensstroom voor scores.

1.  Wijzig in de scoringswerkstroom de module N-gram extraheren uit de tekstmodule en stel de parameter **Woordenschatmodus** in op **Alleen lezen**. Laat al het andere hetzelfde.

1.  Als u de pijplijn wilt publiceren, slaat u **Resultaatwoordenschat** op als gegevensset.

1.  Verbind de opgeslagen gegevensset met de module N-gram extraheren uit de tekstmodule in uw scoregrafiek.

## <a name="results"></a>Resultaten

Met de module N-gram elementen uit tekst worden twee typen uitvoer uitgevoerd: 

* **Resultaatgegevensset**: Deze uitvoer is een samenvatting van de geanalyseerde tekst in combinatie met de n-grammen die zijn geëxtraheerd. Kolommen die u niet hebt geselecteerd in de optie **Tekstkolom,** worden doorgegeven aan de uitvoer. Voor elke kolom tekst die u analyseert, genereert de module de volgende kolommen:

  * **Matrix van n-gram gebeurtenissen**: De module genereert een kolom voor elke n-gram gevonden in het totale corpus en voegt een score in elke kolom om het gewicht van de n-gram voor die rij aan te geven. 

* **Resultaat woordenschat**: De woordenschat bevat de werkelijke n-gram woordenboek, samen met de term frequentie scores die worden gegenereerd als onderdeel van de analyse. U de gegevensset opslaan voor hergebruik met een andere set ingangen of voor een latere update. U ook de woordenschat hergebruiken voor modelleren en scoren.

### <a name="result-vocabulary"></a>Resultaat woordenschat

De woordenschat bevat de n-gram woordenboek met de term frequentie scores die worden gegenereerd als onderdeel van de analyse. De DF- en IDF-scores worden gegenereerd, ongeacht andere opties.

+ **ID:** een id gegenereerd voor elke unieke n-gram.
+ **NGram**: De n-gram. Spaties of andere woordafscheiders worden vervangen door het teken underscore.
+ **DF**: De term frequentiescore voor de n-gram in het oorspronkelijke corpus.
+ **IDF**: De omgekeerde documentfrequentiescore voor de n-gram in het oorspronkelijke corpus.

U deze gegevensset handmatig bijwerken, maar u fouten introduceren. Bijvoorbeeld:

* Er wordt een fout gegenereerd als de module dubbele rijen met dezelfde sleutel in de invoerwoordenschat vindt. Zorg ervoor dat geen twee rijen in de woordenschat hetzelfde woord hebben.
* Het invoerschema van de woordenschatgegevenssets moet exact overeenkomen, inclusief kolomnamen en kolomtypen. 
* De **kolom ID** en de kolom **DF** moeten van het gehele getaltype zijn. 
* De **IDF-kolom** moet van het zwevende type zijn.

> [!Note]
> Sluit de gegevensuitvoer niet rechtstreeks aan op de module Treinmodel. U moet gratis tekstkolommen verwijderen voordat ze in het treinmodel worden ingevoerd. Anders worden de vrije tekstkolommen behandeld als categorische kenmerken.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning.
