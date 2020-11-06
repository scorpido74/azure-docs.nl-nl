---
title: 'Tekst voorverwerken: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de tekst module preprocess in Azure Machine Learning Designer om tekst te reinigen en te vereenvoudigen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: d512a691b76cb7cbc72b4cbcb1fc821e928ea1b0
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421223"
---
# <a name="preprocess-text"></a>Tekst voorverwerken

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik de **tekst module preprocess** om tekst op te schonen en te vereenvoudigen. Deze algemene bewerkingen voor tekst verwerking worden ondersteund:

* Verwijdering van stop woorden
* Reguliere expressies gebruiken om specifieke doel teken reeksen te zoeken en te vervangen
* Lemmatisering, waarmee meerdere verwante woorden worden omgezet naar één canonieke vorm
* Case-normalisatie
* Verwijdering van bepaalde klassen tekens, zoals cijfers, speciale tekens en reeksen van herhaalde tekens, zoals "AAAA"
* E-mail berichten en Url's identificeren en verwijderen

De **tekst module preprocess** ondersteunt momenteel alleen Engels.

## <a name="configure-text-preprocessing"></a>Tekst voorverwerken configureren  

1.  Voeg de **tekst module preprocess** toe aan uw pijp lijn in azure machine learning. U kunt deze module vinden onder **Text Analytics**.

1. Verbind een gegevensset met ten minste één kolom met tekst.

1. Selecteer de taal in de vervolg keuzelijst **taal** .

1. **Tekst kolom die moet worden opgeschoond** : Selecteer de kolom die u wilt voorverwerken.

1. **Stop woorden verwijderen** : Selecteer deze optie als u een vooraf gedefinieerde lijst met stop woord wilt Toep assen op de tekst kolom. 

    Stop woord-lijsten zijn taal afhankelijk en aanpasbaar.

1. **Lemmatisering** : Selecteer deze optie als u wilt dat woorden worden weer gegeven in de canonieke vorm. Deze optie is handig voor het verminderen van het aantal unieke instanties van andere vergelijk bare tekst tokens.

    Het lemmatisering-proces is zeer taal afhankelijk.

1. **Zinnen detecteren** : Selecteer deze optie als u wilt dat de module een teken grens markering voor een zin invoegt bij het uitvoeren van analyses.

    In deze module wordt een reeks van drie sluis tekens gebruikt `|||` om de zin af te duiden.

1. Voer optionele Zoek-en vervang bewerkingen uit met reguliere expressies.

    * **Aangepaste reguliere expressie** : Definieer de tekst die u zoekt.
    * **Aangepaste vervangende teken reeks** : Definieer één vervangende waarde.

1. **Hoofdletter gevoelig voor kleine** letters: Selecteer deze optie als u ASCII-hoofd tekens wilt omzetten in een kleine letter vorm.

    Als tekens niet zijn genormaliseerd, wordt hetzelfde woord in hoofd letters en kleine letters beschouwd als twee verschillende woorden.

1. U kunt ook de volgende typen tekens of teken reeksen uit de verwerkte uitvoer tekst verwijderen:

    * **Nummers verwijderen** : Selecteer deze optie als u wilt dat alle numerieke tekens voor de opgegeven taal worden verwijderd. Identificatie nummers zijn afhankelijk van het domein en de taal. Als de numerieke tekens integraal deel uitmaken van een bekend woord, kan het getal niet worden verwijderd.
    
    * **Speciale tekens verwijderen** : gebruik deze optie om niet-alfanumerieke speciale tekens te verwijderen.
    
    * **Dubbele tekens verwijderen** : Selecteer deze optie als u wilt dat de extra tekens in een reeks die meer dan twee keer worden herhaald, worden verwijderd. Een reeks zoals ' aaaaa ' wordt bijvoorbeeld gereduceerd tot ' AA '.
    
    * **E-mail adressen verwijderen** : Selecteer deze optie om een wille keurige volg orde van de indeling te verwijderen `<string>@<string>` .  
    * **Url's verwijderen** : Selecteer deze optie om een reeks te verwijderen die de volgende URL-voor voegsels bevat: `http` , `https` , `ftp` , `www`
    
1. **Uitbestedingen van woorden uitvouwen** : deze optie is alleen van toepassing op talen die verb-contract ANTEN gebruiken; Dit is momenteel alleen beschikbaar in het Engels. 

    Als u deze optie selecteert, kunt u bijvoorbeeld de zin *' zou niet blijven behouden '* vervangen. *"would not stay there"*

1. **Backslashes normaliseren naar slashes** : Selecteer deze optie om alle exemplaren van `\\` aan toe te wijzen `/` .

1. **Tokens splitsen op speciale tekens** : Selecteer deze optie als u woorden wilt splitsen op tekens zoals, enzovoort `&` `-` . Deze optie kan ook de speciale tekens verminderen wanneer deze meer dan twee keer wordt herhaald. 

    De teken reeks wordt bijvoorbeeld `MS---WORD` onderverdeeld in drie tokens,, `MS` `-` en `WORD` .

1. Verzend de pijp lijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 