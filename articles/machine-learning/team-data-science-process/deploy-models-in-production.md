---
title: Modellen implementeren in productie - Team Data Science Process
description: Hoe modellen in productie te zetten, zodat ze een actieve rol kunnen spelen bij het nemen van zakelijke beslissingen.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3e2ef3340ca836f56176c21cf3d221f0bf172b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722234"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Modellen in productie zetten om een actieve rol te spelen bij het nemen van zakelijke beslissingen

Met de productie-implementatie kan een model een actieve rol spelen in een bedrijf. Voorspellingen van een geïmplementeerd model kunnen worden gebruikt voor zakelijke beslissingen.

## <a name="production-platforms"></a>Productieplatforms

Er zijn verschillende benaderingen en platforms om modellen in productie te nemen. Hier zijn een paar opties:

- [Waar u modellen implementeren met Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implementatie van een model in SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Voorafgaand aan de implementatie moet men ervoor zorgen dat de latentie van modelscores laag genoeg is om te gebruiken in de productie.
>

>[!NOTE]
>Zie [Een Webservice Azure Machine Learning implementeren](../studio/deploy-a-machine-learning-web-service.md)voor implementatie met Azure Machine Learning Studio.
>

## <a name="ab-testing"></a>A/B-testen

Wanneer er meerdere modellen in productie zijn, kunnen [A/B-tests](https://en.wikipedia.org/wiki/A/B_testing) worden gebruikt om de prestaties van het model te vergelijken. 
 
## <a name="next-steps"></a>Volgende stappen

Walkthroughs die alle stappen in het proces voor **specifieke scenario's** demonstreren zijn ook voorzien. Ze worden vermeld en gekoppeld aan miniatuurbeschrijvingen in het artikel [Voorbeeld walkthroughs.](walkthroughs.md) Ze illustreren hoe cloud- en on-premises tools en services kunnen worden gecombineerd in een workflow of pijplijn om een intelligente toepassing te maken. 
