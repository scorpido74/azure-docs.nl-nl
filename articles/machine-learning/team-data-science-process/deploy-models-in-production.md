---
title: Implementeren van modellen in productie - Team Data Science Process
description: Klik hier voor meer informatie over het implementeren van modellen voor productie, zodat ze een actieve rol bij het maken van zakelijke beslissingen te nemen.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722234"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Modellen implementeren naar productie om af te spelen een actieve rol bij het maken van zakelijke beslissingen te nemen

Productie-implementatie kunt een model voor het een actieve rol spelen in een bedrijf. Voorspellingen op basis van een geïmplementeerd model kunnen worden gebruikt voor zakelijke beslissingen te nemen.

## <a name="production-platforms"></a>Productieplatforms

Er zijn verschillende benaderingen en platforms modellen in productie te plaatsen. Hier zijn enkele opties:

- [Het implementeren van modellen met Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implementatie van een model in SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Voorafgaand aan de implementatie heeft een zorgen dat de latentie van het model scoren laag genoeg is voor gebruik in productie is.
>

>[!NOTE]
>Zie voor een implementatie met behulp van Azure Machine Learning Studio, [een Azure Machine Learning-webservice implementeren](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B-testen

Wanneer er meerdere modellen in productie zijn, kan [een/B-test](https://en.wikipedia.org/wiki/A/B_testing) worden gebruikt om de prestaties van het model te vergelijken. 
 
## <a name="next-steps"></a>Volgende stappen

Scenario's die laten zien van alle de stappen in het proces voor het **specifieke scenario's** worden ook gegeven. Ze worden weergegeven en die is gekoppeld met miniaturen beschrijvingen in de [voorbeeld walkthroughs](walkthroughs.md) artikel. Ze laten zien hoe u naar de cloud, on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen. 
