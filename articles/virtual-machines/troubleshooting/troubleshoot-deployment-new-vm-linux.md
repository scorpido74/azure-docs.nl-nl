---
title: Problemen met de implementatie van Linux VM oplossen| Microsoft Documenten
description: Problemen met de implementatie van Resource Manager oplossen wanneer u een nieuwe virtuele Linux-machine in Azure maakt
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 98c3a6b14230e30ccbb103be741595696a20c236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981406"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Problemen met de implementatie van Resource Manager oplossen met het maken van een nieuwe virtuele Linux-machine in Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Belangrijkste kwesties
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Voor andere problemen met en vragen over de VM-implementatie raadpleegt u [Troubleshoot deploying Linux virtual machine issues in Azure](troubleshoot-deploy-vm-linux.md) (Problemen met de implementatie van Linux-VM's in Azure oplossen).

## <a name="collect-activity-logs"></a>Activiteitslogboeken verzamelen
Als u problemen met oplossen wilt oplossen, verzamelt u de activiteitslogboeken om de fout te identificeren die aan het probleem is gekoppeld. De volgende links bevatten gedetailleerde informatie over het te volgen proces.

[Implementatiebewerkingen bekijken](../../azure-resource-manager/templates/deployment-history.md)

[Activiteitslogboeken weergeven om Azure-resources te beheren](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Als het besturingssysteem is Linux gegeneraliseerd, en het wordt geüpload en / of vastgelegd met de algemene instelling, dan zullen er geen fouten. Evenzo, als het OS is Linux gespecialiseerd, en het is geüpload en / of vastgelegd met de gespecialiseerde instelling, dan zullen er geen fouten.

**Uploadfouten:**

**N<sup>1</sup>:** Als het besturingssysteem is Linux gegeneraliseerd, en het wordt geüpload als gespecialiseerd, krijg je een provisioning time-out fout omdat de VM vast zit in de inrichtingfase.

**N<sup>2</sup>:** Als het besturingssysteem is Linux gespecialiseerd, en het wordt geüpload als algemeen, krijg je een provisioning fout fout omdat de nieuwe VM wordt uitgevoerd met de oorspronkelijke computer naam, gebruikersnaam en wachtwoord.

**Resolutie:**

Om beide fouten op te lossen, uploadt u de originele VHD, die beschikbaar is op locatie, met dezelfde instelling als die voor het besturingssysteem (gegeneraliseerd/gespecialiseerd). Als u wilt uploaden als algemeen, moet u eerst -deprovision uitvoeren.

**Fouten vastleggen:**

**N<sup>3</sup>:** Als het besturingssysteem is Linux gegeneraliseerd, en het is gevangen als gespecialiseerd, krijg je een provisioning time-out fout omdat de oorspronkelijke VM is niet bruikbaar omdat het is gemarkeerd als algemeen.

**N<sup>4</sup>:** Als het besturingssysteem is Linux gespecialiseerd, en het is vastgelegd als gegeneraliseerd, krijg je een provisioning fout fout omdat de nieuwe VM wordt uitgevoerd met de oorspronkelijke computer naam, gebruikersnaam en wachtwoord. Ook is de originele VM niet bruikbaar omdat deze is gemarkeerd als gespecialiseerd.

**Resolutie:**

Als u beide fouten wilt oplossen, verwijdert u de huidige afbeelding uit de portal en [herovert u deze van de huidige VHD's](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) met dezelfde instelling als die voor het besturingssysteem (gegeneraliseerd/gespecialiseerd).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Probleem: Aangepaste/ galerij/ marktplaatsafbeelding; toewijzingsfout
Deze fout ontstaat in situaties waarin de nieuwe VM-aanvraag is vastgemaakt aan een cluster dat de gevraagde VM-grootte niet kan ondersteunen of die geen vrije ruimte heeft om aan de aanvraag te voldoen.

**Oorzaak 1:** Het cluster kan de gevraagde VM-grootte niet ondersteunen.

**Oplossing 1:**

* Probeer de aanvraag opnieuw met een kleiner VM-formaat.
* Als de grootte van de gevraagde vm niet kan worden gewijzigd:
  * Stop alle VM's in de beschikbaarheidsset.
    Klik **op Resourcegroepen** > *uw resourcegroep* > **Resources** > *uw beschikbaarheidsset* > **Virtuele machines** > *uw virtuele machine* > **Stop**.
  * Nadat alle VM's stoppen, maakt u de nieuwe VM in de gewenste grootte.
  * Start eerst de nieuwe vm en selecteer vervolgens elk van de gestopte VM's en klik op **Start**.

**Oorzaak 2:** Het cluster beschikt niet over gratis resources.

**Oplossing 2:**

* Probeer het verzoek op een later tijdstip opnieuw.
* Als de nieuwe virtuele machine deel kan uitmaken van een andere beschikbaarheidsset
  * Een nieuwe virtuele machine maken in een andere beschikbaarheidsset (in dezelfde regio).
  * Voeg de nieuwe vm toe aan hetzelfde virtuele netwerk.

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt wanneer u een gestopte Linux-vm start of het formaat van een bestaande Linux-vm in Azure wijzigt, raadpleegt u [Problemen met de implementatie van Resource Manager oplossen bij het opnieuw opstarten of opnieuw vergroten van een bestaande Virtuele Linux-machine in Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

