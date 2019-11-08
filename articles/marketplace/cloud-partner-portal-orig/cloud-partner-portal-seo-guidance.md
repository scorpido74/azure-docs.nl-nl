---
title: SEO-richt lijnen voor Azure Marketplace
description: Biedt hulp bij het maximaliseren van de optimalisatie van zoek machines (SEO).
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: pabutler
ms.openlocfilehash: 7115798faadc3209413d22a384433417ec0ddff0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819585"
---
# <a name="azure-marketplace-seo-guidance"></a>SEO-richt lijnen voor Azure Marketplace

In dit artikel wordt uitgelegd hoe u de vind baarheid van uw aanbod kunt optimaliseren via de zoek functionaliteit in [Azure Marketplace](https://azuremarketplace.microsoft.com) en [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Algemene uitleg van algoritme

Micro soft marketplaces gebruiken Azure Cognitive Search om de zoek mogelijkheden van de site uit te scha kelen. De algoritme is gebaseerd op de term frequentie – inverse document frequentie ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). De standaard- [lucene Analyzer](https://lucene.apache.org/core/) wordt gebruikt.

In het algemeen zijn dit alle tekst velden, categorieën en branches en opgenomen in het gewicht van de relevantie. Gespecialiseerde termen die niet veelvuldig worden gebruikt door apps, maar die vaak in uw app voor komen, genereren een hogere overeenkomst met zoeken. Met inbegrip van termen als ' VM ' zou u dus weinig voor deel kunnen bieden, terwijl ' Azure Search ' veel meer specialistisch is.
Hieronder vindt u de meest relevante velden waarmee u rekening moet houden.

 
|  Veld                   | Relevantie | Richtlijnen                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Naam van aanbieding               |  Hoog      | Nauw keurig of nauw keurig voor een volledige overeenkomst met de zoek query resulteert in een hoge rang schikking.                       |
| Naam van de uitgever           |  Hoog      | Nauw keurig of nauw keurig voor een volledige overeenkomst met de zoek query resulteert in een hoge rang schikking.                       |
| Korte beschrijving        |  Middelgroot    | Als de naam van de apps en de naam van de uitgever bijna een hoge prioriteit heeft, is het mogelijk niet het meest relevant. In dit geval is een korte beschrijving essentieel. Zorg ervoor dat de tekst beknopt en op het punt wordt bewaard. Tref woorden en verwachte zoek termen moeten worden opgenomen voor het beste resultaat.  Dit is bijvoorbeeld de beste verkoop POS die volledig op Dynamics 365 is gebouwd, is minder effectief dan Retail POS (verkoop punt) voor Dynamics 365.  | 
| Lange beschrijving         |  Laag       | Beschrijving biedt een manier om meer dieper te gaan. De meest efficiënte beschrijvingen zijn van redelijke lengte en tref woorden worden gebruikt.  Een op-de-punt beschrijvingen die gebruikmaken van tref woorden, profiteren van meer dan lange, langdurige tekst. Zorg ervoor dat de belang rijke voor waarden, zoals IoT, aanwezig zijn in de beschrijving.  |
| Productcategorieën       | Middelgroot     |  Product categorieën worden bepaald door een combi natie van Publisher-keuzes en micro soft. Selecteer deze categorieën op de juiste manier zodat gebruikers de apps in de juiste categorie eenvoudig kunnen vinden. |
|  |  |  |


## <a name="other-tips"></a>Andere tips

-   Met zoek suggesties krijgt u een zware gebruikers activiteit. Hiermee worden prioriteiten in overeenstemming met de app-naam/Uitgever gegeven. Een korte beschrijving wordt het sleutel veld voor wanneer de zoek term niet exact overeenkomt met de naam van de uitgever/app.
-   Documenten die worden gedownload, worden niet opgenomen in de zoek functie voor gewichten.
-   De werkelijke aanschaf en het gebruik van uw apps heeft ook invloed op de zoek classificatie. Bijvoorbeeld: twee equivalente apps waarbij één veel meer gebruikers heeft, krijgen een hogere prioriteit.
