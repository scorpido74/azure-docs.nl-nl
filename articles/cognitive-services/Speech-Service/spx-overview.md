---
title: SPX-Speech-Service
titleSuffix: Azure Cognitive Services
description: SPX is een opdracht regel programma voor het gebruik van de spraak service zonder code te schrijven. SPX vereist mini maal instellen en het is eenvoudig om snel te experimenteren met de belangrijkste functies van de speech-service om te zien of aan uw gebruiks aanvragen kan worden voldaan.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.openlocfilehash: b473bdc516c59b55eeb44f227352497142a4383b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202280"
---
# <a name="what-is-spx"></a>Wat is SPX?

SPX is een opdracht regel programma voor het gebruik van de spraak service zonder code te schrijven. SPX vereist een minimale installatie en het is eenvoudig om snel te experimenteren met de belangrijkste functies van de speech-service om te zien of aan uw gebruiks aanvragen kan worden voldaan. Binnen enkele minuten kunt u eenvoudige test werk stromen uitvoeren zoals batch Speech-Recognition van een map met bestanden, of tekst-naar-spraak op een verzameling teken reeksen uit een bestand. Meer dan eenvoudige werk stromen, SPX is gereed voor productie en kan worden geschaald om grotere processen uit te voeren met geautomatiseerde `.bat` of shell-scripts.

Het meren deel van de primaire functies in de Speech SDK is beschikbaar in SPX, maar sommige geavanceerde functies en aanpassingen zijn vereenvoudigd in SPX. Houd rekening met de volgende richt lijnen om te bepalen wanneer u SPX of de SDK wilt gebruiken.

Gebruik SPX wanneer:
* U wilt experimenteren met functies van de spraak service met minimale setup en zonder code
* U hebt relatief eenvoudige vereisten voor een productie toepassing met behulp van de speech-service

Gebruik de SDK wanneer:
* U de functionaliteit van de spraak service wilt integreren in een specifieke taal of een specifiek platform (bijvoorbeeld C#, Python, C++)
* U hebt complexe vereisten waarvoor geavanceerde service aanvragen nodig zijn of aangepaste gedragingen ontwikkelen, waaronder het streamen van reacties

## <a name="core-features"></a>Kern functies

* Spraak herkenning: Converteer spraak naar tekst van audio bestanden of rechtstreeks vanuit een microfoon of transcribeer een opgenomen gesprek.

* Spraak-synthese: Converteer tekst naar spraak met behulp van invoer uit tekst bestanden of voer rechtstreeks vanaf de opdracht regel in. Pas de kenmerken van spraak uitvoer aan met behulp van [SSML-configuraties](speech-synthesis-markup.md)en [standaard-of Neural stemmen](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Spraak omzetting: Vertaal audio in een bron taal naar tekst in een doel taal.

* Voer uit op Azure Compute-resources: verzend SPX-opdrachten om te worden uitgevoerd op een Azure Remote Compute-resource met behulp van `spx webjob` .

## <a name="get-started"></a>Aan de slag

Als u aan de slag wilt met SPX, raadpleegt u het artikel over de [basis beginselen](spx-basics.md). In dit artikel leest u hoe u enkele eenvoudige opdrachten in SPX uitvoert, en worden er ook meer geavanceerde opdrachten weer gegeven voor het uitvoeren van batch bewerkingen voor spraak naar tekst en tekst naar spraak. Na het lezen van het artikel over de basis beginselen moet u voldoende kennis hebben van de SPX-syntaxis om een aantal aangepaste opdrachten te schrijven, of om eenvoudige spraak bewerkingen te automatiseren.

## <a name="next-steps"></a>Volgende stappen

- [SPX-basisbeginselen](spx-basics.md)
- Als uw gebruiks voorbeeld complexer is, kunt u [de spraak-SDK downloaden](speech-sdk.md)
