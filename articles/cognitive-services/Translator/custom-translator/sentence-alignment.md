---
title: Zinkoppelen en uitlijnen - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Tijdens de training worden zinnen die in parallelle documenten aanwezig zijn gekoppeld of uitgelijnd. Custom Translator leert vertalingen een zin per keer, door het lezen van een zin, de vertaling van deze zin. Dan lijnt het woorden en zinnen in deze twee zinnen aan elkaar.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370139"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Zinskoppeling en uitlijning in parallelle documenten

Tijdens de training worden zinnen die in parallelle documenten aanwezig zijn gekoppeld of uitgelijnd. Custom Translator rapporteert het aantal zinnen dat het kon koppelen als de uitgelijnde zinnen in elk van de gegevenssets.

## <a name="pairing-and-alignment-process"></a>Koppelings- en uitlijningsproces

Custom Translator leert vertalingen van zinnen per zin per keer. Het leest een zin uit de bron, en dan de vertaling van deze zin van doel. Dan lijnt het woorden en zinnen in deze twee zinnen aan elkaar. Dit proces maakt het mogelijk om een kaart van de woorden en zinnen in een zin te maken met de equivalente woorden en zinnen in de vertaling van deze zin. Uitlijning probeert ervoor te zorgen dat het systeem traint op zinnen die vertalingen van elkaar zijn.

## <a name="pre-aligned-documents"></a>Vooraf uitgelijnde documenten

Als u weet dat u parallelle documenten hebt, u de uitlijning van de zin overschrijven door vooraf uitgelijnde tekstbestanden te leveren. U alle zinnen uit beide documenten extraheren in tekstbestand, `.align` één zin per regel ordenen en uploaden met een extensie. De `.align` extensie geeft aan dat custom translator de uitlijning van de zin moet overslaan.

Voor de beste resultaten, probeer ervoor te zorgen dat u een zin per regel in uw bestanden.Heb geen newline tekens binnen een zin als dit zal leiden tot slechte uitlijningen.

## <a name="suggested-minimum-number-of-sentences"></a>Voorgesteld minimumaantal zinnen

Om een training te laten slagen, geeft de onderstaande tabel het minimumaantal zinnen weer dat vereist is voor elk documenttype.Deze beperking is een vangnet om ervoor te zorgen dat uw parallelle zinnen voldoende unieke woordenschat bevatten om een vertaalmodel succesvol te trainen. De algemene richtlijn is met meer in-domein parallelle zinnen van menselijke vertaling kwaliteit moet produceren van hogere kwaliteit modellen.

| Documenttype   | Voorgestelde minimumaantal zinnen | Maximum aantal zinnen |
|------------|--------------------------------------------|--------------------------------|
| Training   | 10.000                                     | Geen bovengrens                 |
| Tuning     | 500                                      | 2500       |
| Testen    | 500                                      | 2500  |
| Woordenlijst | 0                                          | Geen bovengrens                 |

> [!NOTE]
> - Training zal niet starten en zal mislukken als de 10.000 minimale zin tellen voor training niet wordt voldaan. 
> - Tuning en testen zijn optioneel. Als u ze niet verstrekt, verwijdert het systeem een passend percentage uit Training om te gebruiken voor validatie en testen. 
> - U een model trainen met alleen woordenboekgegevens. Raadpleeg [Wat is Woordenboek](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van een [woordenboek](what-is-dictionary.md) in Aangepaste vertaler.
