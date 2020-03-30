---
title: 'Tekst voor preproces: naslaginformatie over modules'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Tekst vooraf verwerken in Azure Machine Learning om tekst op te schonen en te vereenvoudigen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 6e4d4c8f798418e090caeba091dec33c71f0458f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477490"
---
# <a name="preprocess-text"></a>Tekst voorverwerken

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik de module **Tekst vooraf verwerken** om tekst op te schonen en te vereenvoudigen. Het ondersteunt deze gemeenschappelijke tekstverwerkingsbewerkingen:

* Verwijdering van stopwoorden
* Reguliere expressies gebruiken om specifieke doeltekenreeksen te zoeken en te vervangen
* Lemmatization, die meerdere verwante woorden omzet in een enkele canonieke vorm
* Case normalisatie
* Verwijderen van bepaalde klassen van tekens, zoals getallen, speciale tekens en reeksen van herhaalde tekens zoals "aaaa"
* Identificatie en verwijdering van e-mails en URL's

De module **Preprocess Text** ondersteunt momenteel alleen Engels.

## <a name="configure-text-preprocessing"></a>Tekstvoorbewerking configureren  

1.  Voeg de module **Preprocess Text** toe aan uw pijplijn in Azure Machine Learning. U vindt deze module onder **Text Analytics**.

1. Sluit een gegevensset met ten minste één kolom met tekst.

1. Selecteer de taal in de vervolgkeuzelijst **Taal.**

1. **Tekstkolom om op te schonen**: Selecteer de kolom die u wilt vooraf verwerken.

1. **Stopwoorden verwijderen:** selecteer deze optie als u een vooraf gedefinieerde stopwoordlijst wilt toepassen op de tekstkolom. 

    Stopword-lijsten zijn taalafhankelijk en aanpasbaar.

1. **Lemmatization**: Selecteer deze optie als u wilt dat woorden worden weergegeven in hun canonieke vorm. Deze optie is handig voor het verminderen van het aantal unieke gebeurtenissen van anders vergelijkbare teksttokens.

    Het lemmatisatieproces is zeer taalafhankelijk..

1. **Zinnen detecteren:** selecteer deze optie als u wilt dat de module een zinsgrensmarkering invoegt bij het uitvoeren van analyses.

    Deze module maakt gebruik van `|||` een reeks van drie pijptekens om de zinsbeger te vertegenwoordigen.

1. Voer optionele find-and-replace-bewerkingen uit met behulp van reguliere expressies.

    * **Aangepaste reguliere expressie:** definieer de tekst die u zoekt.
    * **Aangepaste vervangingstekenreeks:** definieer één vervangingswaarde.

1. **Hoofdletters normaliseren in kleine letters:** selecteer deze optie als u ASCII hoofdletters wilt converteren naar de kleine letters.

    Als tekens niet worden genormaliseerd, wordt hetzelfde woord in hoofdletters en kleine letters beschouwd als twee verschillende woorden.

1. U ook de volgende typen tekens of tekenreeksen uit de verwerkte uitvoertekst verwijderen:

    * **Getallen verwijderen:** selecteer deze optie om alle numerieke tekens voor de opgegeven taal te verwijderen. Identificatienummers zijn domeinafhankelijk en taalafhankelijk. Als numerieke tekens een integraal onderdeel zijn van een bekend woord, wordt het getal mogelijk niet verwijderd.
    
    * **Speciale tekens verwijderen:** gebruik deze optie om niet-alfanumerieke speciale tekens te verwijderen.
    
    * **Dubbele tekens verwijderen:** Selecteer deze optie om extra tekens te verwijderen in alle reeksen die meer dan twee keer worden herhaald. Een reeks als "aaaaa" wordt bijvoorbeeld gereduceerd tot "aa".
    
    * **E-mailadressen verwijderen:** Selecteer deze optie `<string>@<string>`om een reeks van de indeling te verwijderen.  
    * **URL's verwijderen:** Selecteer deze optie om elke reeks te `http` `https`verwijderen `ftp`die de volgende URL-voorvoegsels bevat: , ,`www`
    
1. **Werkwoordcontracties uitvouwen:** Deze optie is alleen van toepassing op talen die werkwoordcontracties gebruiken; momenteel alleen Engels. 

    Door bijvoorbeeld deze optie te selecteren, u de zinsnede *'zou daar niet blijven'* vervangen door *'zou daar niet blijven'.*

1. **Backslashes normaliseren naar slashes:** Selecteer deze optie `\\` om `/`alle exemplaren van .

1. **Tokens splitsen op speciale tekens:** Selecteer deze optie als `&`u `-`woorden wilt breken over tekens zoals , enzovoort. Deze optie kan ook de speciale tekens verminderen wanneer deze meer dan twee keer wordt herhaald. 

    De `MS---WORD` tekenreeks wordt bijvoorbeeld gescheiden in `MS`drie `-`tokens, , en `WORD`.

1. Verzend de pijplijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 