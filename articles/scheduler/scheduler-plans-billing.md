---
title: Abonnementen en facturering
description: Meer informatie over abonnementen en facturering voor Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898480"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Plannen en facturering voor Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vervangt Azure Scheduler, dat [wordt uitgeschakeld.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Als u wilt blijven werken met de taken die u in Scheduler hebt ingesteld, migreert u zo snel mogelijk [naar Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Scheduler is niet langer beschikbaar in de Azure-portal, maar de [REST API-](/rest/api/scheduler) en [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taakverzamelingen beheren.

## <a name="job-collection-plans"></a>Plannen voor het verzamelen van vacatures

In Azure Scheduler bevat een taakverzameling een specifiek aantal taken. De taakverzameling is de factureerbare entiteit en wordt geleverd in standaard-, P10 Premium- en P20 Premium-abonnementen, die hier worden beschreven: 

| Taakverzamelingsplan | Maximale taken per verzameling | Maximale herhaling | Maximale taakverzamelingen per abonnement | Limieten | 
|:--- |:--- |:--- |:--- |:--- |
| **Standaard** | 50 banen per collectie | Eén per minuut. Kan niet vaker taken uitvoeren dan één per minuut. | Elk Azure-abonnement kan maximaal 100 standaardtaakverzamelingen hebben. | Toegang tot de volledige functieset van Scheduler | 
| **P10 Premium** | 50 banen per collectie | Eén per minuut. Kan niet vaker taken uitvoeren dan één per minuut. | Elk Azure-abonnement kan maximaal 10.000 P10 Premium-taakverzamelingen hebben. Voor meer collecties <a href="mailto:wapteams@microsoft.com">u contact met ons opnemen.</a> | Toegang tot de volledige functieset van Scheduler |
| **P20 Premium** | 1000 banen per collectie | Eén per minuut. Kan niet vaker taken uitvoeren dan één per minuut. | Elk Azure-abonnement kan maximaal 5.000 P20 Premium-taakverzamelingen hebben. Voor meer collecties <a href="mailto:wapteams@microsoft.com">u contact met ons opnemen.</a> | Toegang tot de volledige functieset van Scheduler |
|||||| 

## <a name="pricing"></a>Prijzen

Zie [Scheduler Pricing voor](https://azure.microsoft.com/pricing/details/scheduler/)prijsdetails.

## <a name="upgrade-or-downgrade-plans"></a>Upgrade- of downgradeplannen

U op elk gewenst moment een taakverzamelingsplan upgraden of downgraden in de standaard-, P10 Premium- en P20 Premium-abonnementen.

## <a name="active-status-and-billing"></a>Actieve status en facturering

Taakverzamelingen zijn altijd actief, tenzij uw volledige Azure-abonnement tijdelijk wordt uitgeschakeld vanwege factureringsproblemen. En hoewel u alle taken in een taakverzameling uitschakelen via één bewerking, wordt met deze actie de factureringsstatus van de taakverzameling niet gewijzigd, zodat de taakverzameling *nog steeds* in rekening wordt gebracht. Lege taakverzamelingen worden als actief beschouwd en worden gefactureerd.

Als u wilt dat er geen kosten in rekening worden gebracht, moet u de taakverzameling verwijderen.

## <a name="standard-billable-units"></a>Standaard factureerbare eenheden

Een standaard factureerbare eenheid kan maximaal 10 standaardtaakverzamelingen hebben. Aangezien een standaardtaakverzameling maximaal 50 taken per verzameling kan hebben, kan uw Azure-abonnement met één standaardfactureringseenheid maximaal 500 banen hebben of tot bijna 22 *miljoen* taakuitvoeringen per maand. In deze lijst wordt uitgelegd hoe u wordt gefactureerd op basis van verschillende aantallen standaardtaakverzamelingen:

* Als u tussen 1 en 10 standaardtaakverzamelingen hebt, wordt u gefactureerd voor één standaardfactureringseenheid. 

* Als u tussen de 11 en 20 standaardtaakverzamelingen hebt, wordt u gefactureerd voor twee standaardfactureringseenheden. 

* Als u tussen de 21 en 30 standaardtaakverzamelingen hebt, wordt u gefactureerd voor drie standaardfactureringseenheden, enzovoort.

## <a name="p10-premium-billable-units"></a>P10 premium factureerbare eenheden

Een P10 premium factureerbare eenheid kan maximaal 10.000 P10 Premium-taakverzamelingen hebben. Aangezien een P10 Premium-taakverzameling maximaal 50 banen per verzameling kan hebben, kan uw Azure-abonnement met één Premium-factureringseenheid voor P10 maximaal 500.000 banen hebben, of tot bijna 22 *miljard* taakuitvoeringen per maand. 

P10 Premium-taakverzamelingen bieden dezelfde mogelijkheden als standaardtaakverzamelingen, maar bieden een prijsonderbreking voor apps waarvoor veel taakverzamelingen nodig zijn en meer schaalbaarheid bieden. In deze lijst wordt uitgelegd hoe u wordt gefactureerd op basis van verschillende aantallen P10 Premium-taakverzamelingen:

* Als u tussen 1 en 10.000 P10 Premium-taakverzamelingen hebt, wordt u gefactureerd voor één P10 premium factureringseenheid. 

* Als u tussen de 10.001 en 20.000 P10 Premium-taakverzamelingen hebt, wordt u gefactureerd voor 2 P10-factureringseenheden, enzovoort.

## <a name="p20-premium-billable-units"></a>P20 premium factureerbare eenheden

Een P20 premium factureerbare eenheid kan maximaal 5.000 P20 Premium-taakverzamelingen hebben. Aangezien een P20 Premium-taakverzameling maximaal 1.000 banen per taakverzameling kan hebben, kan uw Azure-abonnement met één Premium-factureringseenheid voor P20 maximaal 5.000.000 banen hebben of tot bijna 220 *miljard* taakuitvoeringen per maand.

P20 Premium-taakverzamelingen bieden dezelfde mogelijkheden als P10 Premium-taakverzamelingen, maar ondersteunen ook een groter aantal taken per verzameling en een groter totaal aantal taken in het algemeen dan P10 Premium, wat meer schaalbaarheid biedt.

## <a name="plan-comparison"></a>Planvergelijking

* Als u meer dan 100 standaardtaakverzamelingen hebt (10 standaard factureringseenheden), u een betere deal krijgen door alle taakverzamelingen in een Premium-abonnement te hebben.

* Als u één standaardtaakverzameling en één Premium-taakverzameling hebt, wordt u gefactureerd voor één standaard factureringseenheid *en* één premium factureringseenheid.

  De Scheduler-servicerekeningen op basis van het aantal actieve taakverzamelingen dat standaard of premium is.

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)