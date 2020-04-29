---
title: Advisor-gegevens in azure resource Graph
description: Query's voor Advisor-gegevens maken in azure resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502449"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Query's uitvoeren voor Advisor-gegevens in resource Graph Explorer (Azure resource Graph)

Advisor-resources zijn nu onboarding naar [Azure resource Graph](https://azure.microsoft.com/features/resource-graph/). Dit legt voor een groot aantal klanten scenario's op schaal voor Advisor-aanbevelingen. Er zijn enkele scenario's die niet mogelijk waren voor op de juiste schaal en nu kunnen worden bereikt met resource grafiek:
* Biedt de mogelijkheid om complexe query's uit te voeren voor al uw abonnementen in Azure Portal
* Aanbevelingen die worden samenvatten op categorie typen (zoals hoge Beschik baarheid, prestaties) en impact typen (hoog, gemiddeld, laag)
* Alle aanbevelingen voor een bepaald type aanbeveling
* Categorie aantal beïnvloede resources per aanbeveling

![Advisor in azure resource Graph Explorer](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Advisor-resource typen in azure Graph

Beschik bare Advisor-resource typen in [resource grafiek](https://docs.microsoft.com/azure/governance/resource-graph/): er zijn drie resource typen beschikbaar voor het uitvoeren van Query's in Advisor-resources. Hier volgt een lijst met de resources die nu beschikbaar zijn voor het uitvoeren van query's in resource grafiek.
* Micro soft. Advisor/configuraties
* Micro soft. Advisor/aanbevelingen
* Micro soft. Advisor/onderdrukkingen

Deze resource typen worden weer gegeven onder een nieuwe tabel met de naam AdvisorResources, die u ook kunt opvragen in de resource Graph Explorer in Azure Portal.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Azure Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor Advisor-kosten](advisor-cost-recommendations.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
