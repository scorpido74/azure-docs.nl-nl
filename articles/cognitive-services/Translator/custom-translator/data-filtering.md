---
title: Gegevensfiltering - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Wanneer u documenten indient die moeten worden gebruikt voor het trainen van een aangepast systeem, ondergaan de documenten een reeks verwerkings- en filterstappen om u voor te bereiden op de training.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595904"
---
# <a name="data-filtering"></a>Gegevens filteren

Wanneer u documenten indient die moeten worden gebruikt voor het trainen van een aangepast systeem, ondergaan de documenten een reeks verwerkings- en filterstappen om u voor te bereiden op de training. Deze stappen worden hier uitgelegd. De kennis van het filteren kan u helpen inzicht te krijgen in het aantal zinnen dat wordt weergegeven in aangepaste vertalers en de stappen die u zelf nemen om de documenten voor te bereiden op training met aangepaste vertaler.

## <a name="sentence-alignment"></a>Uitlijning van zinnen
Als uw document zich niet in de XLIFF-, TMX- of ALIGN-indeling bevindt, stemt Custom Translator de zinnen van uw bron en richt u documenten op elkaar, zin voor zin. Vertaler voert geen documentuitlijning uit – het volgt uw naamgeving van de documenten om het overeenkomende document van de andere taal te vinden. In het document probeert Aangepaste vertaler de bijbehorende zin in de andere taal te vinden. Het maakt gebruik van document markup zoals ingesloten HTML-tags om te helpen met de uitlijning.  

Als u een grote discrepantie ziet tussen het aantal zinnen in de bron- en doelzijdedocumenten, is uw document mogelijk niet parallel geweest of kan uw document om andere redenen niet worden uitgelijnd. Het document wordt gekoppeld aan een groot verschil (>10%) van zinnen aan elke kant rechtvaardigen een tweede blik om ervoor te zorgen dat ze inderdaad parallel. Aangepaste vertaler toont een waarschuwing naast het document als het aantal zinnen verdacht verschilt.  


## <a name="deduplication"></a>Ontdubbeling
Custom Translator verwijdert de zinnen die aanwezig zijn in test- en tuningdocumenten uit trainingsgegevens.De verwijdering gebeurt dynamisch binnen van de trainingsrun, niet in de gegevensverwerkingsstap. Custom Translator rapporteert het aantal zinnen aan u in het projectoverzicht voordat deze wordt verwijderd.  

## <a name="length-filter"></a>Lengtefilter
* Verwijder zinnen met slechts één woord aan beide zijden.
* Verwijder zinnen met meer dan 100 woorden aan weerszijden.Chinees, Japans, Koreaans zijn vrijgesteld.
* Verwijder zinnen met minder dan 3 tekens. Chinees, Japans, Koreaans zijn vrijgesteld.
* Verwijder zinnen met meer dan 2000 tekens voor Chinees, Japans, Koreaans.
* Verwijder zinnen met alfatekens van minder dan 1%.
* Verwijder woordenboekvermeldingen met meer dan 50 woorden.

## <a name="white-space"></a>Witte ruimte
* Vervang elke reeks witruimtetekens, inclusief tabbladen en CR/LF-sequenties, door één spatieteken.
* Voorloop- of achterloopruimte in de zin verwijderen

## <a name="sentence-end-punctuation"></a>Zin einde interpunctie
Vervang meerdere zinseindeleestekens door één instantie.  

## <a name="japanese-character-normalization"></a>Japanse karakternormalisering
Volledige breedteletters en cijfers converteren naar tekens met een halve breedte.

## <a name="unescaped-xml-tags"></a>Onontsnapte XML-tags
Filteren transformeert onontsnapte tags in ontsnapte tags:
* `&lt;`Wordt`&amp;lt;`
* `&gt;`Wordt`&amp;gt;`
* `&amp;`Wordt`&amp;amp;`

## <a name="invalid-characters"></a>Ongeldige tekens
Aangepaste vertaler verwijdert zinnen die Unicode-teken U+FFFD bevatten. Het teken U+FFFD geeft een mislukte coderingsconversie aan.

## <a name="next-steps"></a>Volgende stappen

- [Train een model](how-to-train-model.md) in Custom Translator.
