---
title: VM wordt opnieuw gestart of het formaat van problemen in azure wordt gewijzigd | Microsoft Docs
description: Problemen met de implementatie van Resource Manager oplossen met het opnieuw opstarten of wijzigen van het formaat van een bestaande virtuele machine in azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd05f9f7db0db22759c5e19dbfb59cc377e63f4d
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155470"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Implementatie problemen oplossen met het opnieuw opstarten of wijzigen van het formaat van een bestaande Windows-VM in azure
Wanneer u probeert een gestopt Azure virtual machine (VM) te starten of de grootte van een bestaande Azure-VM te wijzigen, treedt er een toewijzings fout op. Deze fout treedt op wanneer het cluster of de regio geen resources beschikbaar heeft of de aangevraagde VM-grootte niet kan ondersteunen.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Activiteiten logboeken verzamelen
Om te beginnen met het oplossen van problemen, verzamelt u de activiteiten Logboeken om de fout te identificeren die aan het probleem is gekoppeld. De volgende koppelingen bevatten gedetailleerde informatie over het proces:

[Implementatiebewerkingen bekijken](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Activiteiten logboeken weer geven om Azure-resources te beheren](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Probleem: Fout bij het starten van een gestopt VM
U probeert een gestopt VM te starten, maar een toewijzings fout op te halen.

### <a name="cause"></a>Oorzaak
De aanvraag voor het starten van de gestopte VM moet worden geprobeerd op het oorspronkelijke cluster waarop de Cloud service wordt gehost. Het cluster beschikt echter niet over beschik bare ruimte voor het uitvoeren van de aanvraag.

### <a name="resolution"></a>Oplossing
* Stop alle virtuele machines in de beschikbaarheidsset en start elke VM opnieuw op.
  
  1. Klik op **resource groepen** > *uw resource groep* >  > omuw > *beschikbaarheidsset*virtualmachines > *uw virtuele machine in te stellen. Stoppen.*   > 
  2. Nadat alle virtuele machines zijn gestopt, selecteert u elk van de gestopte Vm's en klikt u op starten.
* Probeer de aanvraag op een later tijdstip opnieuw te starten.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Probleem: Fout bij het wijzigen van de grootte van een bestaande virtuele machine
U probeert het formaat van een bestaande virtuele machine te wijzigen, maar een toewijzings fout op te halen.

### <a name="cause"></a>Oorzaak
De aanvraag voor het wijzigen van de grootte van de virtuele machine moet worden geprobeerd op het oorspronkelijke cluster dat als host fungeert voor de Cloud service. Het cluster biedt echter geen ondersteuning voor de aangevraagde VM-grootte.

### <a name="resolution"></a>Oplossing
* Voer de aanvraag opnieuw uit met een kleinere VM-grootte.
* Als de grootte van de aangevraagde virtuele machine niet kan worden gewijzigd:
  
  1. Stop alle virtuele machines in de beschikbaarheidsset.
     
     * Klik op **resource groepen** > *uw resource groep* >  > omuw > *beschikbaarheidsset*virtualmachines > *uw virtuele machine in te stellen. Stoppen.*   > 
  2. Nadat alle virtuele machines zijn gestopt, wijzigt u het formaat van de gewenste VM in een grotere grootte.
  3. Selecteer de virtuele machine waarvan het formaat is gewijzigd en klik op **Start**en start vervolgens elk van de gestopte vm's.

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt bij het maken van een nieuwe Windows-VM in azure, raadpleegt u [implementatie problemen oplossen met het maken van een nieuwe virtuele Windows-machine in azure](../windows/troubleshoot-deployment-new-vm.md).

