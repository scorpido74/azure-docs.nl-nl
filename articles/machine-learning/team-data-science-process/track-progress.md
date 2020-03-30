---
title: Voortgang van Team Data Science Process-projecten bijhouden
description: Hoe data science group managers, teamleads en projectleads de voortgang van een data science-project kunnen volgen.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864193"
---
# <a name="track-the-progress-of-data-science-projects"></a>De voortgang van data science-projecten bijhouden

Data science group managers, team leads, en project leads kunnen bijhouden van de voortgang van hun projecten.  Managers willen weten wat er is gedaan, wie het werk heeft gedaan en wat er nog werkt.   Het managen van verwachtingen is een belangrijk element van succes.

## <a name="azure-devops-dashboards"></a>Azure DevOps-dashboards

Als u Azure DevOps gebruikt, u dashboards maken om de activiteiten en werkitems te volgen die zijn gekoppeld aan een bepaald Agile-project. Zie [Dashboards, rapporten en widgets](/azure/devops/report/dashboards/)voor meer informatie over dashboards.

Zie de volgende snelstarts voor instructies voor het maken en aanpassen van dashboards en widgets in Azure DevOps:

- [Dashboards toevoegen en beheren](/azure/devops/report/dashboards/dashboards)
- [Widgets toevoegen aan een dashboard](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Voorbeelddashboard

Hier is een eenvoudig voorbeelddashboard dat de sprintactiviteiten van een Agile data science-project bijhoudt, inclusief het aantal commits voor bijbehorende repositories. 

- De **afteltegel** toont het aantal dagen dat overblijft in de huidige sprint. 

- De twee **codetegels** geven het aantal commits weer in de twee projectrepositories van de afgelopen zeven dagen. 

- **Werkitems voor TDSP Customer Project** toont de resultaten van een query voor alle werkitems en hun status. 

- Een **cumulatief stroomdiagram** (CFD) geeft het aantal gesloten en actieve werkitems weer.

- De **burndown grafiek** toont werk nog te voltooien tegen de resterende tijd in de sprint.

- De **burn-up grafiek** toont voltooid werk in vergelijking met de totale hoeveelheid werk in de sprint.

![Dashboard](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Volgende stappen

[Walkthroughs die het Team Data Science Process uitvoeren,](walkthroughs.md) bevat walkthroughs die alle processtappen demonstreren. De gekoppelde scenario's illustreren hoe u de cloud- en on-premise resources beheren in intelligente toepassingen. 
