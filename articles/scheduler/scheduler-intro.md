---
title: Wat is Azure Scheduler?
description: Maak planning en voer geautomatiseerde taken uit die services oproepen binnen of buiten Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898542"
---
# <a name="what-is-azure-scheduler"></a>Wat is Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vervangt Azure Scheduler, dat [wordt uitgeschakeld.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Als u wilt blijven werken met de taken die u in Scheduler hebt ingesteld, migreert u zo snel mogelijk [naar Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Scheduler is niet langer beschikbaar in de Azure-portal, maar de [REST API-](/rest/api/scheduler) en [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taakverzamelingen beheren.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) helpt u bij het maken van [taken](../scheduler/scheduler-concepts-terms.md) die in de cloud worden uitgevoerd door declaratief acties te beschrijven. Vervolgens worden deze acties automatisch gepland en uitgevoerd door de service. U kunt bijvoorbeeld services binnen en buiten Azure aanroepen, zoals het aanroepen van HTTP of HTTPS-eindpunten, en berichten plaatsen in Azure Storage-wachtrijen en Azure Service Bus-wachtrijen of -onderwerpen. U kunt taken direct of op een later tijdstip uitvoeren. Scheduler ondersteunt met gemak [complexe schema's en geavanceerde terugkeerpatronen](../scheduler/scheduler-advanced-complexity.md). Scheduler geeft aan wanneer taken moeten worden uitgevoerd, houdt een geschiedenis bij van de taakresultaten die u kunt bekijken en plant op voorspelbare en betrouwbare wijze de uitvoering van workloads.

Andere Azure-planfuncties maken op de achtergrond gebruik van Scheduler, zoals [Azure WebJobs](../app-service/webjobs-create.md), een onderdeel van de functie [Web Apps](https://azure.microsoft.com/services/app-service/web/) in Azure App Service. U de communicatie voor deze acties beheren met behulp van de [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/), waarmee u de communicatie voor deze acties beheren.

Hier volgen enkele scenario's waarbij Scheduler u kan helpen:

* Terugkerende app-acties uitvoeren: zoals het regelmatig gegevens verzamelen van Twitter in een feed.

* Dagelijks onderhoud uitvoeren: het dagelijks verwijderen van logboeken, het uitvoeren van back-ups en overige onderhoudstaken.

  Als beheerder wilt u bijvoorbeeld de komende negen maanden elke dag om 01.00 uur een back-up maken van uw database.

Hoewel u Scheduler gebruiken kunt om geplande workloads te maken, onderhouden uit te voeren, host Scheduler de workload niet en voert het geen code uit. De service *roept* de service of programmacode aan die elders wordt gehost, bijvoorbeeld in Azure, on-premises of bij een andere provider. Dit aanroepen doet Scheduler via HTTP, HTTPS, een opslagwachtrij, een Service Bus-wachtrij of een Service Bus-onderwerp.

Als u taken en [taakverzamelingen](../scheduler/scheduler-concepts-terms.md)wilt maken, plannen, beheren, bijwerken of verwijderen, u code, de [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/)of de [PowerShell-cmdlets azure scheduler gebruiken.](scheduler-powershell-reference.md)

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Plannen en facturering voor Azure Scheduler](scheduler-plans-billing.md)
* [Complexe schema's en geavanceerde herhaling maken met Azure Scheduler](scheduler-advanced-complexity.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)
