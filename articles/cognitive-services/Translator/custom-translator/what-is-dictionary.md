---
title: Wat is een woordenboek? - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Een woordenboek is een uitgelijnd document dat een lijst met zinnen of zinnen (en hun vertalingen) opgeeft die u altijd wilt dat Microsoft Translator op dezelfde manier vertaalt. Woordenboeken worden soms ook wel woordenlijsten of term bases.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 36b449c4c4ca30eb658c9519ce8e870a4f1fab32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71970724"
---
# <a name="what-is-a-dictionary"></a>Wat is een woordenboek?

Een woordenboek is een uitgelijnd paar documenten dat een lijst met zinnen of zinnen en de bijbehorende vertalingen opgeeft. Gebruik een woordenboek in uw training wanneer u wilt dat Microsoft Translator altijd exemplaren van de bronzin of zin vertaalt met behulp van de vertaling die u in het woordenboek hebt opgegeven. Woordenboeken worden ook wel woordenlijsten of term bases genoemd. U denken aan het woordenboek als een brute kracht kopiëren en vervangen voor alle termen die u lijst. Bovendien bouwt en maakt de Microsoft Custom Translator-service gebruik van eigen woordenboeken voor algemeen gebruik om de kwaliteit van de vertaling te verbeteren. Echter, een klant verstrekt woordenboek neemt precedent en zal eerst worden gezocht om te zoeken naar woorden of zinnen.

Woordenboeken werken alleen voor projecten in taalparen die een volledig ondersteund Algemeen neuraal netwerkmodel van Microsoft achter zich hebben. [Bekijk de volledige lijst met talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Woordlijst
Wanneer u een woordenlijst opneemt in het trainen van uw model, wordt elk woord of woordgroep dat wordt vermeld, vertaald op de manier die u hebt opgegeven. De rest van de zin wordt vertaald zoals gewoonlijk. U een woordenlijst gebruiken om zinnen op te geven die niet moeten worden vertaald door dezelfde onvertaalde woordgroep in het bron- en doelbestand in het woordenboek op te geven.

## <a name="sentence-dictionary"></a>Zinswoordenboek
Met het zinswoordenboek u een exacte doelvertaling opgeven voor een bronzin. Als er een zinswoordenboekovereenkomst optreedt, moet de volledige ingediende zin overeenkomen met de vermelding van het bronwoordenboek.  Als slechts een deel van de zin overeenkomt, komt de vermelding niet overeen.  Wanneer een overeenkomst wordt gedetecteerd, wordt de doelvermelding van het zinswoordenboek geretourneerd.

## <a name="dictionary-only-trainings"></a>Trainingen alleen voor woordenboeken
U een model trainen met alleen woordenboekgegevens. Selecteer hiervoor alleen het woordenboekdocument (of meerdere woordenboekdocumenten) dat u wilt opnemen en tik op Model maken. Aangezien dit een woordenboek-only training is, is er geen minimum aantal trainingszinnen vereist. Uw model zal meestal de opleiding veel sneller voltooien dan een standaard training.  De resulterende modellen gebruiken de Microsoft-basislijnmodellen voor vertaling met de toevoeging van de woordenboeken die u hebt toegevoegd.  U krijgt geen testrapport.

>[!Note]
>Custom Translator brengt woordenboekbestanden niet uit lijnen, dus het is belangrijk dat er een gelijk aantal bron- en doelzinnen/zinnen in uw woordenboekdocumenten zijn en dat ze precies zijn uitgelijnd.

## <a name="recommendations"></a>Aanbevelingen

- Woordenboeken zijn geen vervanging voor het trainen van een model met behulp van trainingsgegevens. Het wordt aanbevolen om ze te vermijden en laat het systeem leren van uw trainingsgegevens. Wanneer zinnen of samengestelde zelfstandige naamwoorden echter moeten worden weergegeven zoals deze is, gebruikt u een woordenboek.
- De zin woordenboek moet spaarzaam worden gebruikt. Houd er dus rekening mee dat wanneer een zin binnen een zin wordt vervangen, de context binnen die zin verloren gaat of beperkt is voor het vertalen van de rest van de zin. Het resultaat is dat terwijl de zin of het woord binnen de zin volgens het verstrekte woordenboek zal vertalen, de algemene vertaalkwaliteit van de zin vaak zal lijden.
- Het woordwoordenboek werkt goed voor samengestelde naamwoorden zoals productnamen ("Microsoft SQL Server"), de juiste namen ("Stad Hamburg"), of functies van het product ("draaitabel"). Het werkt niet even goed voor werkwoorden of bijvoeglijke naamwoorden, omdat deze meestal zeer verbogen in de bron of in de doeltaal. Best practices is om woorden woordenboek vermeldingen te voorkomen voor alles behalve samengestelde naamwoorden.
- Bij het gebruik van een woordwoordwoordenboek zijn hoofdletters en interpunctie belangrijk. Woordenboekvermeldingen komen alleen overeen met woorden en zinnen in de invoerzin die precies dezelfde hoofdletters en interpunctie gebruiken als opgegeven in het bronwoordenboekbestand. Ook de vertalingen weerspiegelen de hoofdletters en interpunctie in het doelwoordenboekbestand. Als u bijvoorbeeld een Engels naar Spaans systeem hebt getraind dat een woordwoordenlijst gebruikt waarin 'VS' in het bronbestand wordt opgegeven en 'EE'. UU." in het doelbestand. Wanneer u een vertaling aanvraagt van een zin die het woord "ons" bevat (niet gekapitaliseerd), komt dit NIET overeen met het woordenboek. Maar als u om vertaling van een zin die het woord "US" (gekapitaliseerd) bevat dan zou het overeenkomen met het woordenboek en de vertaling zou bevatten "EE. UU." Houd er rekening mee dat de hoofdletters en interpunctie in de vertaling anders kunnen zijn dan opgegeven in het doelbestand van het woordenboek en kunnen afwijken van de hoofdletters en interpunctie in de bron. Het volgt de regels van de doeltaal.
- Als een woord meer dan eens in een woordenboekbestand wordt weergegeven, gebruikt het systeem altijd de laatste vermelding die wordt verstrekt. Daarom mag uw woordenboek niet meerdere vertalingen van hetzelfde woord bevatten.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over [richtlijnen voor documentindelingen](document-formats-naming-convention.md).
