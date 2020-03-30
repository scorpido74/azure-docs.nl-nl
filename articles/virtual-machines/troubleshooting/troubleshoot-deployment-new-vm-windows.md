---
title: Problemen met Windows VM-implementatie in Azure oplossen | Microsoft Documenten
description: Problemen met de implementatie van Resource Manager oplossen wanneer u een nieuwe virtuele Windows-machine in Azure maakt
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0bc363b87a9f5b2f013c0bae75a07d79a3a7a830
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981389"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Problemen met implementatie oplossen bij het maken van een nieuwe Windows-vm in Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Belangrijkste kwesties
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Zie [Problemen met de implementatie van Windows in Azure oplossen voor](troubleshoot-deploy-vm-windows.md)andere problemen met de vm-implementatie.

## <a name="collect-activity-logs"></a>Activiteitslogboeken verzamelen
Als u problemen met oplossen wilt oplossen, verzamelt u de activiteitslogboeken om de fout te identificeren die aan het probleem is gekoppeld. De volgende links bevatten gedetailleerde informatie over het te volgen proces.

[Implementatiebewerkingen bekijken](../../azure-resource-manager/templates/deployment-history.md)

[Activiteitslogboeken weergeven om Azure-resources te beheren](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Als het besturingssysteem windows gegeneraliseerd is en het wordt geüpload en/of vastgelegd met de algemene instelling, dan zullen er geen fouten zijn. Evenzo, als het BE is Windows gespecialiseerd, en het is geüpload en / of vastgelegd met de gespecialiseerde instelling, dan zullen er geen fouten.

**Uploadfouten:**

**N<sup>1</sup>:** Als het besturingssysteem is Windows gegeneraliseerd, en het wordt geüpload als gespecialiseerd, krijg je een provisioning time-out fout met de VM vast op het OOBE-scherm.

**N<sup>2</sup>:** Als het besturingssysteem is windows gespecialiseerd, en het wordt geüpload als algemeen, krijg je een provisioning fout fout met de VM vast op het OOBE-scherm, omdat de nieuwe VM wordt uitgevoerd met de oorspronkelijke computernaam, gebruikersnaam en wachtwoord.

**Afsluiting**

Om beide fouten op te lossen, gebruikt u [Add-AzVhd om de originele VHD te uploaden](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), die on-premises beschikbaar is, met dezelfde instelling als die voor het besturingssysteem (gegeneraliseerd/gespecialiseerd). Als u wilt uploaden als gegeneraliseerd, moet u eerst sysprep uitvoeren.

**Fouten vastleggen:**

**N<sup>3</sup>:** Als het besturingssysteem windows is gegeneraliseerd en het is vastgelegd als gespecialiseerd, krijgt u een time-outfout voor het inrichten van een time-out omdat de oorspronkelijke vm niet bruikbaar is omdat deze is gemarkeerd als algemeen.

**N<sup>4</sup>:** Als het besturingssysteem is gespecialiseerd in Windows en het is vastgelegd als algemeen, krijgt u een fout inbepalingsfout omdat de nieuwe vm wordt uitgevoerd met de oorspronkelijke computernaam, gebruikersnaam en wachtwoord. Ook is de originele VM niet bruikbaar omdat deze is gemarkeerd als gespecialiseerd.

**Afsluiting**

Als u beide fouten wilt oplossen, verwijdert u de huidige afbeelding uit de portal en [herovert u deze van de huidige VHD's](../windows/create-vm-specialized.md) met dezelfde instelling als die voor het besturingssysteem (gegeneraliseerd/gespecialiseerd).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Probleem: Aangepaste/galerij/marktplaatsafbeelding; toewijzingsfout
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
Als u problemen ondervindt wanneer u een gestopte Windows-vm start of het formaat van een bestaande Windows-vm in Azure wijzigt, raadpleegt u [Problemen met de implementatie van Resource Manager oplossen bij het opnieuw opstarten of opnieuw vergroten van een bestaande Virtuele Windows-machine in Azure](restart-resize-error-troubleshooting.md).


