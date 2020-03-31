---
title: Advisor-gegevens in Azure Resource Graph
description: Query's maken voor Advisor-gegevens in Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502449"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Query voor Advisor-gegevens in Resource Graph Explorer (Azure Resource Graph)

Advisor-bronnen zijn nu aangesloten op [Azure Resource Graph.](https://azure.microsoft.com/features/resource-graph/) Dit legt de basis voor vele grootschalige klantscenario's voor Advisor-aanbevelingen. Enkele scenario's die voorheen niet mogelijk waren om op schaal te doen en nu kunnen worden bereikt met Behulp van Resource Graph zijn:
* Biedt de mogelijkheid om complexe query's uit te voeren voor al uw abonnementen in Azure-portal
* Aanbevelingen samengevat op categorietypen (zoals hoge beschikbaarheid, prestaties) en impacttypen (hoog, gemiddeld, laag)
* Alle aanbevelingen voor een bepaald aanbevelingstype
* Impact gehad resource telling per aanbevelingscategorie

![Adviseur in Azure-brongrafiekverkenner](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Advisor-brontypen in Azure Graph

Beschikbare Advisor-resourcetypen in [ResourceGraph:](https://docs.microsoft.com/azure/governance/resource-graph/)Er zijn 3 resourcetypen beschikbaar voor query's onder Advisor-bronnen. Hier vindt u de lijst met de bronnen die nu beschikbaar zijn voor query's in Resourcegraph.
* Microsoft.Advisor/configuraties
* Microsoft.Advisor/aanbevelingen
* Microsoft.Advisor/onderdrukkingen

Deze resourcetypen worden vermeld onder een nieuwe tabel met de naam AdvisorResources, die u ook opvragen in de Resource Graph Explorer in Azure-portal.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over aanbevelingen van Advisor:
* [Inleiding tot Azure Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor kosten van adviseur](advisor-cost-recommendations.md)
* [Aanbevelingen voor prestaties van adviseur](advisor-performance-recommendations.md)
* [Aanbevelingen voor beveiliging van adviseur](advisor-security-recommendations.md)
* [Aanbevelingen adviseur Operational Excellence](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
