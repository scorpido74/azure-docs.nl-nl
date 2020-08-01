---
title: Updates en patches voor uw virtuele machines in Azure Automation beheren
description: In dit artikel leest u hoe u Updatebeheer kunt gebruiken voor het beheren van updates en patches voor uw Azure-en niet-Azure-Vm's.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: e4f630931e3cd79a46a539955b45d72e5b573271
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450182"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Updates en patches voor uw virtuele machines beheren

Software-updates in Azure Automation Updatebeheer biedt een set hulpprogram ma's en resources waarmee u de complexe taak voor het bijhouden en Toep assen van software-updates op machines in Azure en hybride Cloud kunt beheren. Er is een effectief beheer proces van software-updates nodig om operationele efficiëntie te behouden, beveiligings problemen op te lossen en de Risico's van verhoogde beveiligings Risico's voor Cyber-aanvallen te verminderen. Wegens de veranderende aard van technologie en het permanent opduiken van nieuwe veiligheidsbedreigingen, vergt effectief beheer van software-updates consistente en continue aandacht.

> [!NOTE]
> Updatebeheer ondersteunt de implementatie van updates voor de eerste partij en het vooraf downloaden ervan. Deze ondersteuning vereist wijzigingen op de systemen die worden bijgewerkt. Zie [Windows Update instellingen configureren voor Azure Automation updatebeheer](update-mgmt-configure-wuagent.md) voor meer informatie over het configureren van deze instellingen op uw systemen.

Voordat u probeert updates voor uw virtuele machines te beheren, moet u ervoor zorgen dat u Updatebeheer hebt ingeschakeld met een van de volgende methoden:

* [Updatebeheer inschakelen vanaf een Automation-account](update-mgmt-enable-automation-account.md)
* [Updatebeheer in te scha kelen door te bladeren door de Azure Portal](update-mgmt-enable-portal.md)
* [Updatebeheer inschakelen vanuit een runbook](update-mgmt-enable-runbook.md)
* [Updatebeheer inschakelen vanaf een virtuele Azure-machine](update-mgmt-enable-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Het bereik voor de implementatie beperken

Updatebeheer gebruikt een scope configuratie in de werk ruimte om de computers te richten op het ontvangen van updates. Zie [limiet updatebeheer-implementatie bereik](update-mgmt-scope-configuration.md)voor meer informatie.

## <a name="compliance-assessment"></a>Nalevings beoordeling

Voordat u software-updates op uw computers implementeert, controleert u de resultaten van de update nalevings beoordeling voor ingeschakelde machines. Voor elke software-update wordt de nalevings status vastgelegd en nadat de evaluatie is voltooid, wordt deze in bulk verzameld en doorgestuurd naar Azure Monitor Logboeken.

Op een Windows-computer wordt de compatibiliteits scan standaard elke 12 uur uitgevoerd. Naast de geplande scan wordt de controle op update vereisten binnen 15 minuten van de Log Analytics-agent voor Windows opnieuw gestart, vóór de installatie van de update en na de installatie van de update gestart. Het is ook belang rijk om onze aanbevelingen te lezen over [het configureren van de Windows Update-client](update-mgmt-configure-wuagent.md) met updatebeheer om problemen te voor komen die ervoor zorgen dat deze niet goed kunnen worden beheerd.

Voor een Linux-computer wordt standaard elk uur de compatibiliteits scan uitgevoerd. Als de Log Analytics-agent voor Linux opnieuw is gestart, wordt een nalevings scan binnen 15 minuten gestart.

De compliantie resultaten worden weer gegeven in Updatebeheer voor elke computer die wordt beoordeeld. Voor een nieuwe machine die is ingeschakeld voor beheer, kan het tot 30 minuten duren voordat er in het dash board bijgewerkte gegevens worden weer gegeven.

Bekijk de [controle van software-updates](update-mgmt-view-update-assessments.md) voor meer informatie over het weer geven van compliantie resultaten.

## <a name="deploy-updates"></a>Updates implementeren

Na het controleren van de nalevings resultaten is de implementatie fase software-update het proces van het implementeren van software-updates. Als u updates wilt installeren, plant u een implementatie die wordt uitgelijnd met uw release planning en service venster. U kunt kiezen welke typen updates moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

Bekijk [software-updates implementeren](update-mgmt-deploy-updates.md) voor meer informatie over het plannen van een update-implementatie.

## <a name="review-update-deployments"></a>Update-implementaties controleren

Nadat de implementatie is voltooid, controleert u het proces om te bepalen of de update-implementatie is geslaagd per computer of doel groep. Zie [Implementatie status controleren](update-mgmt-deploy-updates.md#check-deployment-status) voor meer informatie over hoe u de implementatie status kunt controleren.

## <a name="next-steps"></a>Volgende stappen

* Zie [waarschuwingen maken voor updatebeheer](update-mgmt-configure-alerts.md)voor meer informatie over het maken van waarschuwingen om u te waarschuwen over update-implementatie resultaten.

* U kunt [Azure monitor logboeken opvragen](update-mgmt-query-logs.md) om update-evaluaties, implementaties en andere gerelateerde beheer taken te analyseren. Het bevat vooraf gedefinieerde query's waarmee u aan de slag kunt gaan.