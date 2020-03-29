---
title: Niet-ondersteunde taalimplementaties - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u niet-ondersteunde taalparen implementeert in Aangepaste vertaler van Azure Cognitive Services.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837455"
---
# <a name="unsupported-language-deployments"></a>Implementaties in niet-ondersteunde talen

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Met de aanstaande pensionering van de Microsoft Translator Hub zal Microsoft alle modellen die momenteel via de Hub worden geïmplementeerd, niet implementeren. Velen van jullie hebben modellen geïmplementeerd in de Hub waarvan de taalparen niet worden ondersteund in Aangepaste vertaler.  We willen niet dat gebruikers in deze situatie geen beroep doen op het vertalen van hun inhoud.

We hebben nu een proces waarmee u uw niet-ondersteunde modellen implementeren via de aangepaste vertaler.  Dit proces stelt u in staat om inhoud te blijven vertalen met behulp van de nieuwste V3 API.  Deze modellen worden gehost totdat u ervoor kiest ze uit te zetten of het taalpaar beschikbaar komt in Aangepaste vertaler.  In dit artikel wordt uitgelegd hoe het proces is om modellen met niet-ondersteunde taalparen te implementeren.

## <a name="prerequisites"></a>Vereisten

Om ervoor te doen dat uw modellen kandidaat zijn voor implementatie, moeten ze voldoen aan de volgende criteria:
* Het project met het model moet met behulp van het migratiehulpprogramma zijn gemigreerd van de Hub naar de aangepaste vertaler.  Het proces voor het migreren van projecten en werkruimten is [hier](how-to-migrate.md)te vinden.
* Het model moet zich in de geïmplementeerde status begeven wanneer de migratie plaatsvindt.  
* Het taalpaar van het model moet een niet-ondersteund taalpaar zijn in Aangepaste vertaler.  Taalparen waarin een taal wordt ondersteund van of naar het Engels, maar het paar zelf geen Engels bevat, zijn kandidaten voor niet-ondersteunde taalimplementaties.  Een Hub-model voor een Frans- tot Duitstalig paar wordt bijvoorbeeld beschouwd als een niet-ondersteund taalpaar, ook al worden Frans naar Engels en Engels naar Duits ondersteund.

## <a name="process"></a>Proces
Zodra u modellen hebt gemigreerd vanuit de Hub die kandidaat zijn voor implementatie, u ze vinden door naar de pagina **Instellingen** voor uw werkruimte te gaan en naar het einde van de pagina te schuiven waar u een sectie **Niet-ondersteunde translatorhub-trainings** ziet.  Deze sectie wordt alleen weergegeven als u projecten hebt die voldoen aan de hierboven genoemde voorwaarden.

![Migreren vanuit Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

Op de selectiepagina voor selectie van de selectie **van niet-ondersteunde vertalershubtrainingen** bevat het tabblad **Niet-aangevraagde trainings** modellen die in aanmerking komen voor implementatie.  Selecteer de modellen die u wilt implementeren en dien een aanvraag in.   Vóór de implementatiedeadline van 30 april u zoveel modellen selecteren als u wilt voor implementatie.
 
![Migreren vanuit Hub](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

Zodra het model is ingediend, is het niet meer beschikbaar op het tabblad **Niet-aangevraagde trainings** en wordt het in plaats daarvan weergegeven op het tabblad **Trainings aanvragen.**  U uw gevraagde trainingen op elk gewenst moment bekijken.

![Migreren vanuit Hub](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Volgende stappen

De modellen die u hebt geselecteerd voor implementatie, worden opgeslagen zodra de hub buiten gebruik is gesteld en alle modellen niet zijn geïmplementeerd.  U hebt tot 24 mei de tijd om aanvragen in te dienen voor implementatie van niet-ondersteunde modellen.  We zullen deze modellen implementeren op 15 juni, waarna ze toegankelijk zijn via de Translator V3 API.  Bovendien zullen ze beschikbaar zijn via de V2 API tot 1 juli.  

Voor meer informatie over belangrijke data in de afschrijving van de Hub check [hier](https://www.microsoft.com/translator/business/hub/).
Zodra deze is geïmplementeerd, zijn er normale hostingkosten van toepassing.  Zie [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) voor meer informatie.  

In tegenstelling tot standaard Custom Translator-modellen zijn Hub-modellen alleen beschikbaar in één regio, dus er zijn geen hostingkosten voor meerdere regio's van toepassing.  Zodra u is geïmplementeerd, u uw Hub-model op elk gewenst moment verwijderen via het gemigreerde aangepaste vertalerproject.

## <a name="next-steps"></a>Volgende stappen

- [Train een model.](how-to-train-model.md)
- Gebruik uw geïmplementeerde aangepaste vertaalmodel via [Microsoft Translator Text API V3.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)
