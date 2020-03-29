---
title: Hoge beschikbaarheid en betrouwbaarheid
description: Meer informatie over hoge beschikbaarheid en betrouwbaarheid in Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 20c2054e168a9b17d9b4ab159cfefbf607ab6d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898565"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Hoge beschikbaarheid en betrouwbaarheid voor Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vervangt Azure Scheduler, dat [wordt uitgeschakeld.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Als u wilt blijven werken met de taken die u in Scheduler hebt ingesteld, migreert u zo snel mogelijk [naar Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Scheduler is niet langer beschikbaar in de Azure-portal, maar de [REST API-](/rest/api/scheduler) en [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taakverzamelingen beheren.

Azure Scheduler biedt zowel [hoge beschikbaarheid](https://docs.microsoft.com/azure/architecture/framework/#resiliency) als betrouwbaarheid voor uw taken. Zie [SLA voor Scheduler voor](https://azure.microsoft.com/support/legal/sla/scheduler)meer informatie .

## <a name="high-availability"></a>Hoge beschikbaarheid

Azure Scheduler is [zeer beschikbaar] en maakt gebruik van zowel georedundante service-implementatie als georegionale taakreplicatie.

### <a name="geo-redundant-service-deployment"></a>Georedundante service-implementatie

Azure Scheduler is beschikbaar in bijna [elke geografische regio die vandaag wordt ondersteund door Azure.](https://azure.microsoft.com/global-infrastructure/regions/#services) Als een Azure-datacenter in een gehoste regio dus niet meer beschikbaar is, u Azure Scheduler nog steeds gebruiken omdat de failovermogelijkheden van de service Scheduler beschikbaar maken vanuit een ander datacenter.

### <a name="geo-regional-job-replication"></a>Georegionale taakreplicatie

Uw eigen taken in Azure Scheduler worden gerepliceerd in Azure-regio's. Als een regio dus een storing heeft, mislukt Azure Scheduler en zorgt u ervoor dat uw taak wordt uitgevoerd vanuit een ander datacenter in de gekoppelde geografische regio.

Als u bijvoorbeeld een taak maakt in South Central US, repliceert Azure Scheduler die taak automatisch in North Central US. Als er een fout optreedt in South Central US, voert Azure Scheduler de taak uit in North Central US. 

![Georegionale taakreplicatie](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure Scheduler zorgt er ook voor dat uw gegevens binnen dezelfde maar bredere geografische regio blijven, voor het geval er een storing optreedt in Azure. U hoeft uw taken dus niet te dupliceren wanneer u alleen maar hoge beschikbaarheid wilt. Azure Scheduler biedt automatisch een hoge beschikbaarheid voor uw taken.

## <a name="reliability"></a>Betrouwbaarheid

Azure Scheduler garandeert zijn eigen hoge beschikbaarheid, maar hanteert een andere benadering van door gebruikers gemaakte taken. Stel dat uw taak een HTTP-eindpunt aanroept dat niet beschikbaar is. Azure Scheduler probeert uw taak nog steeds succesvol uit te voeren door u alternatieve manieren te bieden voor het afhandelen van fouten: 

* Beleid voor nieuwe try instellen.
* Alternatieve eindpunten instellen.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Beleid opnieuw proberen

Met Azure Scheduler u beleid voor nieuwe try instellen. Als een taak mislukt, wordt de taak standaard nog vier keer opnieuw geprobeerd met intervallen van 30 seconden. U dit beleid voor nieuwe try's agressiever maken, zoals 10 keer met intervallen van 30 seconden of minder agressief, zoals twee keer met dagelijkse intervallen.

Stel dat u een wekelijkse taak maakt die een HTTP-eindpunt aanroept. Als het HTTP-eindpunt een paar uur niet beschikbaar is wanneer uw taak wordt uitgevoerd, wilt u mogelijk niet nog een week wachten voordat de taak opnieuw wordt uitgevoerd, wat gebeurt omdat het standaardbeleid voor opnieuw proberen in dit geval niet werkt. U dus het standaardbeleid voor nieuwe pogingen wijzigen, zodat nieuwe pogingen bijvoorbeeld om de drie uur plaatsvinden in plaats van elke 30 seconden. 

Zie [Opnieuw proberenBeleid](scheduler-concepts-terms.md#retrypolicy)voor meer informatie over het instellen van een beleid voor nieuwe proeven.

### <a name="alternate-endpoints"></a>Alternatieve eindpunten

Als uw Azure Scheduler-taak een eindpunt aanroept dat onbereikbaar is, zelfs nadat u het beleid voor nieuwe poging hebt gevolgd, valt Scheduler terug op een alternatief eindpunt dat dergelijke fouten kan verwerken. Dus als u dit eindpunt instelt, roept Scheduler dat eindpunt op, waardoor uw eigen taken zeer beschikbaar zijn wanneer er fouten optreden.

In dit diagram ziet u bijvoorbeeld hoe Scheduler het beleid voor het opnieuw proberen volgt wanneer u een webservice in New York aanroept. Als de pogingen mislukken, controleert Scheduler op een alternatief eindpunt. Als het eindpunt bestaat, begint Scheduler aanvragen naar het alternatieve eindpunt te verzenden. Hetzelfde beleid voor het opnieuw proberen geldt voor zowel de oorspronkelijke actie als de alternatieve actie.

![Schedulergedrag met beleid opnieuw proberen en alternatief eindpunt](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Het actietype voor de alternatieve actie kan afwijken van de oorspronkelijke actie. Hoewel de oorspronkelijke actie bijvoorbeeld een HTTP-eindpunt aanroept, kan de alternatieve actie fouten registreren met behulp van een opslagwachtrij, servicebuswachtrij of servicebusonderwerpactie.

Zie [Foutactie](scheduler-concepts-terms.md#error-action)voor meer informatie over het instellen van een alternatief eindpunt.

## <a name="next-steps"></a>Volgende stappen

* [Concepten, terminologie en entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)
* [Limieten, quota, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)
