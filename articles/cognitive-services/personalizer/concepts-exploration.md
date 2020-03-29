---
title: Verkenning - Personalizer
titleSuffix: Azure Cognitive Services
description: Met exploratie is Personalizer in staat om goede resultaten te blijven leveren, zelfs als het gedrag van gebruikers verandert. Het kiezen van een verkenningsinstelling is een zakelijke beslissing over het aandeel van gebruikersinteracties om mee te verkennen, om het model te verbeteren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 0b69c1fb070431ad61858322dce461f6496c35d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490810"
---
# <a name="exploration-and-exploitation"></a>Exploratie en exploitatie

Met exploratie is Personalizer in staat om goede resultaten te blijven leveren, zelfs als het gedrag van gebruikers verandert.

Wanneer Personalizer een Rang-call ontvangt, retourneert deze een RewardActionID die:
* Gebruikt uitbuiting om het meest waarschijnlijke gebruikersgedrag op basis van het huidige machine learning-model te matchen.
* Hiermee wordt verkenning gebruikt, die niet overeenkomt met de actie met de hoogste waarschijnlijkheid in de rang.

Personalizer maakt momenteel gebruik van een algoritme genaamd *epsilon hebzuchtig* om te verkennen. 

## <a name="choosing-an-exploration-setting"></a>Een verkenningsinstelling kiezen

U configureert het percentage verkeer dat moet worden gebruikt voor verkenning op de **configuratiepagina** van de Azure-portal voor Personalizer. Met deze instelling bepaalt u het percentage rank-aanroepen dat verkenning uitvoert. 

Personalizer bepaalt of te verkennen of te exploiteren met deze waarschijnlijkheid op elke rang oproep. Dit is anders dan het gedrag in sommige A/B-frameworks die een behandeling op specifieke gebruikers-id's vergrendelen.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Aanbevolen procedures voor het kiezen van een verkenningsinstelling

Het kiezen van een verkenningsinstelling is een zakelijke beslissing over het aandeel van gebruikersinteracties om mee te verkennen, om het model te verbeteren. 

Een instelling van nul zal veel van de voordelen van Personalizer teniet doen. Met deze instelling gebruikt Personalizer geen gebruikersinteracties om betere gebruikersinteracties te ontdekken. Dit leidt tot modelstagnatie, drift en uiteindelijk lagere prestaties.

Een instelling die te hoog is, doet af aan de voordelen van het leren van gebruikersgedrag. Het instellen op 100% impliceert een constante randomisatie, en elk aangeleerd gedrag van gebruikers zou de uitkomst niet beïnvloeden.

Het is belangrijk om het gedrag van de toepassing niet te wijzigen op basis van of u ziet of Personalizer aan het verkennen of exploiteren is. Dit zou leiden tot het leren van vooroordelen die uiteindelijk zou de potentiële prestaties te verminderen.

## <a name="next-steps"></a>Volgende stappen

[Bekrachtigend leren](concepts-reinforcement-learning.md) 