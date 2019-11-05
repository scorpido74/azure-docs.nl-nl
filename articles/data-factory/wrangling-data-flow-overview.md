---
title: Wrangling-gegevens stromen in Azure Data Factory | Microsoft Docs
description: Een overzicht van de wrangling-gegevens stromen in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 05119adfa5b254297fd656854a027ede05c5ef26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518578"
---
# <a name="what-are-wrangling-data-flows"></a>Wat zijn wrangling-gegevens stromen?

Organisaties moeten gegevens voorbereiding en wrangling voor nauw keurige analyse van complexe gegevens die steeds elke dag blijven groeien. Het voorbereiden van gegevens is vereist zodat organisaties de gegevens in verschillende bedrijfs processen kunnen gebruiken en de tijd tot waarde kan verlagen.

Met Wrangling-gegevens stromen in Azure Data Factory kunt u de gegevens voorbereiding voor code gratis uitvoeren op de Cloud schaal. Wrangling-gegevens stromen kunnen worden geïntegreerd met [Power query online](https://docs.microsoft.com/power-query/) en er zijn Power query M-functies beschikbaar voor Data Factory gebruikers.

Wrangling-gegevens stroom vertaalt M die is gegenereerd door Power Query online Mashup-Editor in Spark-code voor het uitvoeren van Cloud schalen.

Wrangling-gegevens stromen zijn vooral nuttig voor gegevens technici of ' burger data Integrators '.

## <a name="use-cases"></a>Gebruiksvoorbeelden

### <a name="fast-interactive-data-exploration-and-preparation"></a>Snel interactieve gegevens verkennen en voorbereiden

Meerdere gegevens technici en burger data integrators kunnen gegevens sets interactief verkennen en voorbereiden op Cloud schaal. Dankzij de toename van het volume, de verscheidenheid en de snelheid van gegevens op het meren deel, hebben gebruikers een efficiënte manier nodig om gegevens sets te verkennen en voor te bereiden. U moet bijvoorbeeld een gegevensset maken die alle demografische gegevens van de klant voor nieuwe klanten sinds 2017 bevat. U bent niet toegewezen aan een bekend doel. U kunt gegevens sets verkennen, wrangling en bereidt om te voldoen aan een vereiste voordat u deze in het Lake publiceert. Wrangling-gegevens stromen worden vaak gebruikt voor minder formele analyse scenario's. De bereid-gegevens sets kunnen worden gebruikt voor het uitvoeren van trans formaties en machine learning bewerkingen stroomafwaarts.

### <a name="code-free-agile-data-preparation"></a>Code-gratis flexibele gegevens voorbereiding

Burger data integrators best Eden meer dan 60% van de tijd om gegevens te zoeken en voor te bereiden. Ze zijn op zoek naar een gratis manier om de operationele productiviteit te verbeteren. Met burger data integrators kunnen gegevens worden verrijkt, gevormd en gepubliceerd met behulp van bekende hulpprogram ma's, zoals Power Query online, op een schaal bare manier, waardoor hun productiviteit aanzienlijk wordt verbeterd. Met Wrangling data flow in Azure Data Factory kunt u met de vertrouwde Power Query online Mashup-Editor voor burger data integrators snel fouten corrigeren, gegevens normaliseren en gegevens van hoge kwaliteit produceren ter ondersteuning van zakelijke beslissingen.

### <a name="data-validation"></a>Gegevens validatie

Scan uw gegevens visueel op een code-Free manier om uitschieters en afwijkingen te verwijderen en deze aan een vorm voor snelle analyse te voldoen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een wrangling-gegevens stroom](wrangling-data-flow-tutorial.md).