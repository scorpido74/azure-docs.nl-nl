---
title: Limieten, quota's en drempelwaarden in Azure Scheduler
description: Meer informatie over limieten, quota' s, standaardwaarden en gaswaarden voor Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898528"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limieten, quota en gashendeldrempels in Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vervangt Azure Scheduler, dat [wordt uitgeschakeld.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Als u wilt blijven werken met de taken die u in Scheduler hebt ingesteld, migreert u zo snel mogelijk [naar Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Scheduler is niet langer beschikbaar in de Azure-portal, maar de [REST API-](/rest/api/scheduler) en [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taakverzamelingen beheren.

## <a name="limits-quotas-and-thresholds"></a>Grenzen, quota's en drempels

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id-header

Elk verzoek tegen de Scheduler-service retourneert een antwoordkop met de naam **x-ms-request-id.** Deze koptekst bevat een ondoorzichtige waarde die de aanvraag op unieke wijze identificeert. Dus als een verzoek consistent mislukt en u hebt bevestigd dat het verzoek correct is opgemaakt, u de fout melden aan Microsoft door de waarde van de **x-ms-request-id-antwoordkop** waarde en deze gegevens op te nemen: 

* De **x-ms-request-id-waarde**
* Het tijdstip waarop het verzoek werd ingediend 
* De id's voor het Azure-abonnement, het verzamelen van banen en de taak 
* Het type bewerking dat de aanvraag heeft geprobeerd

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Plannen en facturering voor Azure Scheduler](scheduler-plans-billing.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)