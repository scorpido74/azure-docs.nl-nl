---
title: Wat zijn parallelle documenten? - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Parallelle documenten zijn paren van documenten waar een is de vertaling van de andere. Een document in het paar bevat zinnen in de brontaal en het andere document bevat deze zinnen vertaald in de doeltaal.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d7c38a44e3111a319e4146b3c9b71a22b0d31bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72675470"
---
# <a name="what-are-parallel-documents"></a>Wat zijn parallelle documenten?

Parallelle documenten zijn paren van documenten waar een is de vertaling van de andere. Een document in het paar bevat zinnen in de brontaal en het andere document bevat deze zinnen vertaald in de doeltaal.
Het maakt niet uit welke taal is gemarkeerd als "bron" en welke taal is gemarkeerd als "doel" - een parallel document kan worden gebruikt om een vertaalsysteem in beide richtingen te trainen.

## <a name="requirements"></a>Vereisten

Je hebt minimaal 10.000 unieke uitgelijnde parallelle zinnen nodig om een systeem te trainen. Deze beperking is een vangnet om ervoor te zorgen dat uw parallelle zinnen voldoende unieke woordenschat bevatten om een vertaalmodel succesvol te trainen. Als best practice voegt u voortdurend meer parallelle inhoud toe en u zich omscholen om de kwaliteit van uw vertaalsysteem te verbeteren. Raadpleeg [de uitlijning van zinnen.](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment)

Microsoft vereist dat documenten die naar de aangepaste vertaler zijn geüpload, niet in strijd zijn met het auteursrecht of de intellectuele eigenschappen van een derde partij. Zie voor meer informatie de [gebruiksvoorwaarden.](https://azure.microsoft.com/support/legal/cognitive-services-terms/)
Het uploaden van een document met behulp van de portal verandert niets aan de eigendom van het intellectuele eigendom in het document zelf.

## <a name="use-of-parallel-documents"></a>Gebruik van parallelle documenten

Parallelle documenten worden door het systeem gebruikt:

1.  Om te leren hoe woorden, zinnen en zinnen vaak in kaart worden gebracht tussen de twee talen.

2.  Om te leren hoe u de juiste context verwerken, afhankelijk van de omringende zinnen. Een woord kan niet altijd vertalen naar exact hetzelfde woord in de andere taal.

Als een best practice, zorg ervoor dat er een 1:1 zin correspondentie tussen de bron en doel taal versies van de documenten.

Als uw project domeinspecifiek is (categorie), moeten uw documenten consistent zijn in terminologie binnen die categorie. De kwaliteit van het resulterende vertaalsysteem is afhankelijk van het aantal zinnen in uw documentset en de kwaliteit van de zinnen. Hoe meer voorbeelden uw documenten bevatten met diverse toepassingen voor een woord dat specifiek is voor uw categorie, hoe beter het systeem kan doen tijdens de vertaling.

Geüploade documenten zijn privé voor elke werkruimte en kunnen worden gebruikt in zoveel projecten of trainingen als u wilt. Zinnen die uit uw documenten worden gehaald, worden afzonderlijk opgeslagen in uw opslagplaats als gewone Unicode-tekstbestanden en zijn beschikbaar voor u verwijderen. Gebruik de aangepaste vertaler niet als documentopslagplaats, u de documenten die u hebt geüpload niet downloaden in de indeling die u hebt geüpload.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van een [woordenboek](what-is-dictionary.md) in Aangepaste vertaler.
