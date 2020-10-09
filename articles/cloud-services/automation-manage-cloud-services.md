---
title: Azure Cloud Services beheren met Azure Automation | Microsoft Docs
description: Meer informatie over hoe de Azure Automation-Service kan worden gebruikt voor het beheren van Azure Cloud Services op schaal.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72439069"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Azure Cloud Services beheren met Azure Automation
In deze hand leiding vindt u informatie over de Azure Automation-Service en hoe u deze kunt gebruiken om het beheer van uw Azure-Cloud Services te vereenvoudigen.

## <a name="what-is-azure-automation"></a>Wat is Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) is een Azure-service voor het vereenvoudigen van het beheer van Clouds met behulp van proces automatisering. Het gebruik van Azure Automation, langlopende, hand matige, fout gevoelige en regel matig herhaalde taken kunnen automatisch worden uitgevoerd om de betrouw baarheid, efficiëntie en tijd voor uw organisatie te verg Roten.

Azure Automation biedt een zeer betrouw bare en Maxi maal beschik bare engine voor werk stroom uitvoering die kan worden geschaald om te voldoen aan uw behoeften naarmate uw organisatie groeit. In Azure Automation kunnen processen hand matig worden gestart door systemen van derden of op geplande intervallen, zodat taken precies worden uitgevoerd wanneer dat nodig is.

Verlaag operationele overhead en maak IT/DevOps-personeel meer werk dat bedrijfs waarde kan toevoegen door te verplaatsen van uw cloud Beheer taken zodat deze automatisch door Azure Automation worden uitgevoerd.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hoe kan Azure Automation helpen bij het beheren van Azure Cloud Services?
Azure Cloud Services kunnen worden beheerd in Azure Automation met behulp van de Power shell-cmdlets die beschikbaar zijn in de [Azure PowerShell-hulpprogram ma's](/powershell/). Azure Automation heeft de volgende Power shell-cmdlets voor Cloud Services beschikbaar in het vak, zodat u al uw beheer taken voor de Cloud service in de service kunt uitvoeren. U kunt deze cmdlets ook in Azure Automation koppelen met de cmdlets voor andere Azure-Services, om complexe taken in Azure-Services en systemen van derden te automatiseren.

Een voor beeld van het gebruik van Azure Automation voor het beheren van Azure Cloud Services omvatten:

* [Continue implementatie van een Cloud service wanneer cscfg of cspkg wordt bijgewerkt in Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Cloud service-exemplaren parallel opnieuw opstarten, één upgrade domein tegelijk](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van Azure Automation hebt geleerd en hoe u deze kunt gebruiken om Azure Cloud Services te beheren, volgt u deze koppelingen voor meer informatie over Azure Automation.

* [Overzicht van Azure Automation](../automation/automation-intro.md)
* [Mijn eerste runbook](../automation/automation-first-runbook-graphical.md)
