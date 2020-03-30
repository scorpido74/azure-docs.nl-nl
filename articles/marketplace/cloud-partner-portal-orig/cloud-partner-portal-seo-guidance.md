---
title: Richtlijnen voor Azure Marketplace SEO
description: Biedt richtlijnen voor het maximaliseren van zoekmachine optimalisatie (SEO).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280147"
---
# <a name="azure-marketplace-seo-guidance"></a>Richtlijnen voor Azure Marketplace SEO

In dit artikel wordt uitgelegd hoe u de vindbaarheid van uw aanbieding maximaliseren via de zoekfunctionaliteit in de [Azure Marketplace](https://azuremarketplace.microsoft.com) en [AppSource.](https://appsource.microsoft.com) 


## <a name="general-explanation-of-algorithm"></a>Algemene uitleg van algoritme

Microsoft-marktplaatsen maken gebruik van Azure Cognitive Search voor het aandrijven van de zoekmogelijkheden van de site. Het algoritme is gebaseerd op term frequency-inverse document frequency[(TF-IDF).](https://en.wikipedia.org/wiki/Tf–idf) De standaard [Lucene Analyzer](https://lucene.apache.org/core/) wordt gebruikt.

In het algemeen, alle tekstvelden, categorieën en industrieën en opgenomen in de gewichtsage van de relevantie. Gespecialiseerde termen die zelden worden gebruikt door apps, maar vaak in uw app, genereren een hogere matchscore met zoeken. Dus het opnemen van termen als "VM" zou weinig voordeel bieden, terwijl "Azure search" veel meer gespecialiseerd zou zijn.
Hieronder vindt u de meest relevante velden om rekening mee te houden.

 
|  Veld                   | Urgentie | Richtlijnen                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Naam van aanbieding               |  Hoog      | Exact of dicht bij een volledige match met zoekopdracht zal hoge ranking opleveren.                       |
| Naam van de uitgever           |  Hoog      | Exact of dicht bij een volledige match met zoekopdracht zal hoge ranking opleveren.                       |
| Korte beschrijving        |  Middelgroot    | Gezien het benoemen van apps en uitgeversnamen bijna een hoge ranking garandeert, is het misschien niet de meest relevante. In dit geval is een korte beschrijving van cruciaal belang. Houd de tekst beknopt en to the point. Zoekwoorden en verwachte zoektermen moeten worden opgenomen voor het beste resultaat.  Bijvoorbeeld "Dit is de beste Retail POS volledig gebouwd op de top van Dynamics 365" is minder effectief dan "Retail POS (point of sale) voor Dynamics 365".  | 
| Lange beschrijving         |  Laag       | Beschrijving biedt een manier om dieper in te gaan. De meest effectieve beschrijvingen zijn van redelijke lengte en zoekwoorden worden gebruikt.  Een to-the-point beschrijvingen met trefwoorden zullen meer dan lange, lange tekst ten goede komen. Zorg ervoor dat belangrijke termen, zoals 'IoT', in de beschrijving aanwezig zijn.  |
| Productcategorieën       | Middelgroot     |  Productcategorieën worden bepaald door een combinatie van uitgeverskeuzes en Microsoft. Selecteer deze categorieën op de juiste manier, zodat gebruikers de apps gemakkelijk in de juiste categorie kunnen vinden. |
|  |  |  |


## <a name="other-tips"></a>Andere tips

-   Zoeken suggereert krijgt zware gebruikersactiviteit. Het geeft prioriteit aan overeenkomsten met de naam/uitgever van de app. Korte beschrijving wordt het belangrijkste veld voor wanneer de zoekterm niet exact overeenkomt met de naam uitgever/app.
-   Documenten om te downloaden zijn niet opgenomen in het zoekgewicht.
-   Uw apps werkelijke acquisitie en gebruik zal invloed hebben op de rangschikking van de zoekopdracht ook. Bijvoorbeeld, twee gelijkwaardige apps waar men heeft veel meer gebruikers krijgt een hogere ranking.
