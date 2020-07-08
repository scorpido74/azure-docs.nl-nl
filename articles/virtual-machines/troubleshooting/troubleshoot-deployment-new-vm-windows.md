---
title: Problemen met de implementatie van Windows VM in azure oplossen | Microsoft Docs
description: Problemen met de implementatie van Resource Manager oplossen wanneer u een nieuwe virtuele Windows-machine maakt in azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: daberry
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78db1ba0eaff0dce83ed13e9f20c3c5a5b96bf9c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83120960"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Implementatie problemen oplossen bij het maken van een nieuwe Windows-VM in azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Belangrijkste problemen
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Zie problemen [met het implementeren van virtuele Windows-machines in azure oplossen](troubleshoot-deploy-vm-windows.md)voor meer informatie over de implementatie en vragen van vm's.

## <a name="collect-activity-logs"></a>Activiteiten logboeken verzamelen
Om te beginnen met het oplossen van problemen, verzamelt u de activiteiten Logboeken om de fout te identificeren die aan het probleem is gekoppeld. De volgende koppelingen bevatten gedetailleerde informatie over het proces dat moet worden gevolgd.

[Implementatiebewerkingen bekijken](../../azure-resource-manager/templates/deployment-history.md)

[Activiteiten logboeken weer geven om Azure-resources te beheren](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Als het besturings systeem Windows gegeneraliseerd is en het is geüpload en/of vastgelegd met de gegeneraliseerde instelling, zijn er geen fouten. Op dezelfde manier geldt dat als het besturings systeem Windows specialist is en het is geüpload en/of vastgelegd met de gespecialiseerde instelling, er geen fouten zijn.

**Upload fouten:**

**N<sup>1</sup>:** Als het besturings systeem Windows gegeneraliseerd is en het is geüpload als gespecialiseerd, ontvangt u een time-out voor inrichtings fout bij de virtuele machine die is vastgelopen in het OOBE-scherm.

**N<sup>2</sup>:** Als het besturings systeem Windows gespecialiseerd is en het is geüpload als gegeneraliseerd, krijgt u een inrichtings fout bij de virtuele machine die is vastgelopen in het OOBE-scherm, omdat de nieuwe virtuele machine wordt uitgevoerd met de oorspronkelijke computer naam, gebruikers naam en wacht woord.

**Afsluiting**

Als u beide fouten wilt oplossen, gebruikt u [add-AzVhd om de oorspronkelijke VHD te uploaden](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), die on-premises beschikbaar is, met dezelfde instelling als die voor het besturings systeem (gegeneraliseerd/gespecialiseerd). Als u wilt uploaden als gegeneraliseerd, moet u eerst Sysprep uitvoeren.

**Fouten vastleggen:**

**N<sup>3</sup>:** Als het besturings systeem Windows gegeneraliseerd is en het is vastgelegd als gespecialiseerd, krijgt u een time-outfout van inrichting omdat de oorspronkelijke virtuele machine niet bruikbaar is omdat deze is gemarkeerd als gegeneraliseerd.

**N<sup>4</sup>:** Als het besturings systeem Windows gespecialiseerd is en het is vastgelegd als gegeneraliseerd, ontvangt u een fout bij het inrichten, omdat de nieuwe virtuele machine wordt uitgevoerd met de oorspronkelijke computer naam, gebruikers naam en wacht woord. De oorspronkelijke virtuele machine kan ook niet worden gebruikt omdat deze is gemarkeerd als gespecialiseerd.

**Afsluiting**

Om beide fouten op te lossen, verwijdert u de huidige installatie kopie uit de portal en [legt u deze opnieuw vast vanaf de huidige vhd's](../windows/create-vm-specialized.md) met dezelfde instelling als voor het besturings systeem (gegeneraliseerd/gespecialiseerd).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Probleem: afbeelding voor aangepast/galerie/Marketplace; toewijzings fout
Deze fout treedt op wanneer de nieuwe VM-aanvraag is vastgemaakt aan een cluster dat de aangevraagde VM-grootte niet kan ondersteunen of waarvoor er geen beschik bare ruimte beschikbaar is om aan de aanvraag te voldoen.

**Oorzaak 1:** Het cluster kan de aangevraagde VM-grootte niet ondersteunen.

**Oplossing 1:**

* Voer de aanvraag opnieuw uit met een kleinere VM-grootte.
* Als de grootte van de aangevraagde virtuele machine niet kan worden gewijzigd:
  * Stop alle virtuele machines in de beschikbaarheidsset.
    Klik op **resource groepen**  >  *uw resource groep*  >  **resources**  >  *uw beschikbaarheidsset*  >  **virtual machines**  >  *de virtuele machine*wordt  >  **gestopt**.
  * Nadat alle Vm's zijn gestopt, maakt u de nieuwe virtuele machine op de gewenste grootte.
  * Start eerst de nieuwe VM en selecteer vervolgens elk van de gestopte Vm's en klik op **Start**.

**Oorzaak 2:** Het cluster heeft geen vrije resources.

**Oplossing 2:**

* Voer de aanvraag op een later tijdstip opnieuw uit.
* Als de nieuwe VM deel kan uitmaken van een andere beschikbaarheidsset
  * Maak een nieuwe virtuele machine in een andere beschikbaarheidsset (in dezelfde regio).
  * Voeg de nieuwe virtuele machine toe aan hetzelfde virtuele netwerk.

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt bij het starten van een gestopt Windows-VM of het wijzigen van het formaat van een bestaande Windows-VM in azure, raadpleegt u problemen met [de implementatie van Resource Manager oplossen met het opnieuw opstarten of wijzigen van het formaat van een bestaande virtuele machine in azure](restart-resize-error-troubleshooting.md).


