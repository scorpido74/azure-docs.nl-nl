---
title: Dynamisch woordenboek - Api voor vertalertekst
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de functie dynamische woordenboek van de Azure Cognitive Services Translator Text API gebruikt.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446713"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Een dynamisch woordenboek gebruiken

Als u de vertaling die u wilt toepassen op een woord of een woordaluist al weet, u deze als opmerking in de aanvraag opgeven. Het dynamische woordenboek is alleen veilig voor samengestelde naamwoorden zoals eigen namen en productnamen.

**Syntaxis:**

<mstrans:dictionary translation="vertaling van zin">zin</mstrans:dictionary>

**Vereisten:**

* De `From` `To` talen en talen moeten Engels en een andere ondersteunde taal bevatten. 
* U moet `From` de parameter opnemen in uw API-vertaalaanvraag in plaats van de functie automatisch detecteren te gebruiken. 

**Voorbeeld: en-de:**

Broninvoer:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Doeluitvoer:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Deze functie werkt op dezelfde manier met en zonder HTML-modus.

Gebruik de functie spaarzaam. Een betere manier om vertaling aan te passen is door aangepaste vertaler te gebruiken. Custom Translator maakt optimaal gebruik van context en statistische waarschijnlijkheden. Als u trainingsgegevens hebt of maken die uw werk of woordgroep in context weergeven, krijgt u veel betere resultaten. Meer informatie over Custom Translator [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)vindt u op .
