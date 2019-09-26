---
title: Wat is Azure Scheduler? | Microsoft Docs
description: Informatie over het maken, plannen en uitvoeren van geautomatiseerde taken die services binnen of buiten Azure aanroepen
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2f418a78f80d65cbb784685804a4cc6790c28b99
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300907"
---
# <a name="what-is-azure-scheduler"></a>Wat is Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) helpt u bij het maken van [taken](../scheduler/scheduler-concepts-terms.md) die in de cloud worden uitgevoerd door declaratief acties te beschrijven. Vervolgens worden deze acties automatisch gepland en uitgevoerd door de service. U kunt bijvoorbeeld services binnen en buiten Azure aanroepen, zoals het aanroepen van HTTP of HTTPS-eindpunten, en berichten plaatsen in Azure Storage-wachtrijen en Azure Service Bus-wachtrijen of -onderwerpen. U kunt taken direct of op een later tijdstip uitvoeren. Scheduler ondersteunt met gemak [complexe schema's en geavanceerde terugkeerpatronen](../scheduler/scheduler-advanced-complexity.md). Scheduler geeft aan wanneer taken moeten worden uitgevoerd, houdt een geschiedenis bij van de taakresultaten die u kunt bekijken en plant op voorspelbare en betrouwbare wijze de uitvoering van workloads.

Hoewel u Scheduler gebruiken kunt om geplande workloads te maken, onderhouden uit te voeren, host Scheduler de workload niet en voert het geen code uit. De service *roept* de service of programmacode aan die elders wordt gehost, bijvoorbeeld in Azure, on-premises of bij een andere provider. Dit aanroepen doet Scheduler via HTTP, HTTPS, een opslagwachtrij, een Service Bus-wachtrij of een Service Bus-onderwerp. Voor het maken, beheren en plannen van taken kunt u [Azure Portal](../scheduler/scheduler-get-started-portal.md), code, [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/) of [naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) gebruiken. U kunt taken en [taakverzamelingen](../scheduler/scheduler-concepts-terms.md) bijvoorbeeld programmatisch maken, weergeven, bijwerken, beheren of verwijderen met scripts of in Azure Portal.

Andere Azure-planfuncties maken op de achtergrond gebruik van Scheduler, zoals [Azure WebJobs](../app-service/webjobs-create.md), een onderdeel van de functie [Web Apps](https://azure.microsoft.com/services/app-service/web/) in Azure App Service. U kunt de communicatie voor deze acties beheren met de [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/). helpt met het beheer van de communicatie voor deze acties.

Hier volgen enkele scenario's waarbij Scheduler u kan helpen:

* **Terugkerende app-acties uitvoeren**: bijvoorbeeld regelmatig gegevens verzamelen van Twitter in een feed.

* **Dagelijks onderhoud uitvoeren**: bijvoorbeeld het dagelijks verwijderen van logboeken, het uitvoeren van back-ups en overige onderhoudstaken. 

  Als beheerder wilt u bijvoorbeeld de komende negen maanden elke dag om 01.00 uur een back-up maken van uw database.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Scheduler in Azure Portal](scheduler-get-started-portal.md)
* Meer informatie over [abonnementen en facturering voor Azure Scheduler](scheduler-plans-billing.md)
* Informatie over [het bouwen van complexe schema's en geavanceerde terugkeerpatronen met Azure Scheduler](scheduler-advanced-complexity.md)