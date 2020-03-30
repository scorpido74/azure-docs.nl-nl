---
title: VM-problemen opnieuw opstarten of opnieuw wijzigen in Azure | Microsoft Documenten
description: Problemen met de implementatie van Resource Manager oplossen bij het opnieuw opstarten of opnieuw wijzigen van een bestaande virtuele machine in Azure
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
ms.openlocfilehash: a6532558107463311c4225b9855bc4cd3f19eed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965609"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Probleemoplossing voor implementatieproblemen bij het opnieuw opstarten van een bestaande virtuele Windows-machine in Azure of bij het wijzigen van het formaat van die VM
Wanneer u een gestopte Azure Virtual Machine (VM) probeert te starten of het formaat van een bestaande Azure-vm wilt wijzigen, is de algemene fout die u tegenkomt een toewijzingsfout. Deze fout ontstaat wanneer het cluster of de regio geen resources beschikbaar heeft of de gevraagde VM-grootte niet kan ondersteunen.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Activiteitslogboeken verzamelen
Als u problemen met oplossen wilt oplossen, verzamelt u de activiteitslogboeken om de fout te identificeren die aan het probleem is gekoppeld. De volgende links bevatten gedetailleerde informatie over het proces:

[Implementatiebewerkingen bekijken](../../azure-resource-manager/templates/deployment-history.md)

[Activiteitslogboeken weergeven om Azure-resources te beheren](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Probleem: Er is een fout opgetreden bij het opstarten van een gestopte VM
U probeert een gestopte VM te starten, maar krijgt een toewijzingsfout.

### <a name="cause"></a>Oorzaak
Het verzoek om de gestopte VM te starten, moet worden geprobeerd bij het oorspronkelijke cluster dat de cloudservice host. Het cluster heeft echter geen vrije ruimte om aan de aanvraag te voldoen.

### <a name="resolution"></a>Oplossing
* Stop alle VM's in de beschikbaarheidsset en start elke vm opnieuw.
  
  1. Klik **op Resourcegroepen** > *uw resourcegroep* > **Resources** > *uw beschikbaarheidsset* > **Virtuele machines** > *uw virtuele machine* > **Stop**.
  2. Nadat alle VM's zijn gestopt, selecteert u elk van de gestopte VM's en klikt u op Start.
* Probeer de aanvraag voor opnieuw starten op een later tijdstip opnieuw.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Probleem: Er is een fout opgetreden bij het wijzigen van het formaat van een bestaande VM
U probeert het formaat van een bestaande virtuele machine te wijzigen, maar krijgt een toewijzingsfout.

### <a name="cause"></a>Oorzaak
Het verzoek om het formaat van de VM te wijzigen, moet worden geprobeerd bij het oorspronkelijke cluster dat de cloudservice host. Het cluster ondersteunt echter niet de gevraagde VM-grootte.

### <a name="resolution"></a>Oplossing
* Probeer de aanvraag opnieuw met een kleiner VM-formaat.
* Als de grootte van de gevraagde vm niet kan worden gewijzigd:
  
  1. Stop alle VM's in de beschikbaarheidsset.
     
     * Klik **op Resourcegroepen** > *uw resourcegroep* > **Resources** > *uw beschikbaarheidsset* > **Virtuele machines** > *uw virtuele machine* > **Stop**.
  2. Nadat alle VM's stoppen, wijzigt u het formaat van de gewenste VM naar een groter formaat.
  3. Selecteer de grootte van de virtuele machine en klik op **Start**en start vervolgens elk van de gestopte VM's.

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt bij het maken van een nieuwe Windows-vm in Azure, raadpleegt u [Problemen met implementatie oplossen bij het maken van een nieuwe virtuele Windows-machine in Azure.](../windows/troubleshoot-deployment-new-vm.md)

