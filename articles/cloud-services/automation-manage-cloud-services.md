---
title: Azure Cloud Services beheren met Azure Automation | Microsoft Documenten
description: Meer informatie over hoe de Azure Automation-service kan worden gebruikt om Azure-cloudservices op schaal te beheren.
services: cloud-services, automation
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 482fcf7d100a90d9527f510382c5dafb4f67adfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72439069"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Azure Cloud Services beheren met Azure Automation
In deze handleiding wordt u kennis laten maken met de Azure Automation-service en hoe deze kan worden gebruikt om het beheer van uw Azure-cloudservices te vereenvoudigen.

## <a name="what-is-azure-automation"></a>Wat is Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) is een Azure-service voor het vereenvoudigen van cloudbeheer door procesautomatisering. Met Azure Automation kunnen langlopende, handmatige, foutgevoelige en vaak herhaalde taken worden geautomatiseerd om de betrouwbaarheid, efficiëntie en tijd tot waarde voor uw organisatie te verhogen.

Azure Automation biedt een zeer betrouwbare en zeer beschikbare workflow uitvoeringsengine die wordt geschaald om aan uw behoeften te voldoen naarmate uw organisatie groeit. In Azure Automation kunnen processen handmatig, door systemen van derden of met geplande intervallen worden gestart, zodat taken precies plaatsvinden wanneer dat nodig is.

Verlaag de operationele overhead en maak IT/DevOps-medewerkers vrij om zich te concentreren op werk dat bedrijfswaarde toevoegt door uw cloudbeheertaken te verplaatsen om automatisch te worden uitgevoerd door Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hoe kan Azure Automation helpen bij het beheren van Azure-cloudservices?
Azure-cloudservices kunnen worden beheerd in Azure Automation met behulp van de PowerShell-cmdlets die beschikbaar zijn in de [Azure PowerShell-hulpprogramma's.](/powershell/) Azure Automation heeft deze cloudservice PowerShell-cmdlets uit de doos beschikbaar, zodat u al uw cloudservicebeheertaken binnen de service uitvoeren. U deze cmdlets ook koppelen in Azure Automation met de cmdlets voor andere Azure-services, om complexe taken te automatiseren in Azure-services en systemen van derden.

Enkele voorbeelden van Azure Automation voor het beheer van Azure Cloud Services zijn:

* [Continue implementatie van een Cloud Service wanneer cscfg of cspkg wordt bijgewerkt in Azure Blob-opslag](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [CloudService-exemplaren parallel opnieuw opstarten, één upgradedomein tegelijk](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van Azure Automation hebt geleerd en hoe deze kan worden gebruikt om Azure-cloudservices te beheren, volgt u deze koppelingen voor meer informatie over Azure Automation.

* [Overzicht van Azure Automation](../automation/automation-intro.md)
* [Mijn eerste runbook](../automation/automation-first-runbook-graphical.md)
