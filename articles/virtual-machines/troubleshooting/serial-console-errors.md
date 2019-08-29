---
title: Fouten in azure Serial console | Microsoft Docs
description: Veelvoorkomende fouten in de Azure seriële console
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: a997675a2f7a280e8311e2bba7dca21de82a086e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129664"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Veelvoorkomende fouten in de Azure seriële console
Er zijn een aantal bekende fouten in de Azure Serial console. Dit is een lijst met deze fouten en de stappen voor het oplossen ervan.

## <a name="common-errors"></a>Algemene fouten

Fout                            |   Oplossing
:---------------------------------|:--------------------------------------------|
Kan niet worden opgehaald van de instellingen voor diagnostische gegevens over opstarten voor  *&lt;VMNAME&gt;* . Voor het gebruik van de seriële console of dat de diagnostische gegevens over die opstarten is ingeschakeld voor deze virtuele machine. ![Diagnostische gegevens over opstarten](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Zorg ervoor dat [Diagnostische gegevens over opstarten](boot-diagnostics.md) zijn ingeschakeld voor de virtuele machine of de VM-schaalset. Als u seriële console gebruikt op een exemplaar van een virtuele-machine schaalset, moet u ervoor zorgen dat uw exemplaar het meest recente model heeft.
De virtuele machine is gestopt toewijzing ongedaan gemaakt. Start de virtuele machine en probeer de seriële console-verbinding. ![Fout bij toewijzing ongedaan gemaakt](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | De virtuele machine of het exemplaar van de VM-schaalset moet de status gestart hebben om toegang te krijgen tot de seriële console. Start de virtuele machine of het exemplaar van de VM-schaalset en probeer het opnieuw.
Een antwoord 'Verboden' is opgetreden bij het openen van deze virtuele machine opstarten diagnostische storage-account. ![Firewall fout van opslag account](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Zorg ervoor dat diagnostische gegevens over opstarten geen account firewall hebben. Een toegankelijke opstarten diagnostische storage-account is nodig voor de seriële console van functie.
U beschikt niet over de vereiste machtigingen om deze virtuele machine met de seriële console te gebruiken. Zorg ervoor dat u ten minste beschikken over machtigingen van de rol Inzender voor virtuele machines.| Voor toegang tot de seriële console hebt u toegang op Inzender niveau of hoger nodig op uw VM of virtuele-machine schaalset. Zie de [pagina overzicht](serial-console-overview.md)voor meer informatie.
Het opslag account dat wordt gebruikt voor diagnostische gegevens over opstarten op deze VM is niet gevonden. Controleer of de diagnostische gegevens over opstarten zijn ingeschakeld voor deze VM. dit opslag account is niet verwijderd en u hebt toegang tot dit opslag account. | Controleer of u het opslag account voor diagnostische gegevens over opstarten niet hebt verwijderd voor uw VM of virtuele-machine schaalset
Het inrichten voor deze VM is nog niet voltooid. Controleer of de virtuele machine volledig is geïmplementeerd en voer de verbinding met de seriële console opnieuw uit. | Uw VM of schaalset voor virtuele machines kan nog steeds worden ingericht. Wacht enige tijd en probeer het opnieuw.
U beschikt niet over de vereiste machtigingen om te schrijven naar het opslag account voor diagnostische gegevens over opstarten voor deze VM. Zorg ervoor dat u ten minste over de VM-Inzender machtigingen beschikt voor. | Voor Seriële console toegang is toegang op Inzender niveau vereist voor het opslag account voor diagnostische gegevens over opstarten. Zie de [pagina overzicht](serial-console-overview.md)voor meer informatie.
Kan niet bepalen van de resourcegroep voor het opslagaccount voor diagnostische gegevens van opstarten  *&lt;STORAGEACCOUNTNAME&gt;* . Controleer of u diagnostische gegevens over opstarten is ingeschakeld voor deze virtuele machine en u toegang hebt tot dit opslagaccount wordt gebruikt. | Voor Seriële console toegang is toegang op Inzender niveau vereist voor het opslag account voor diagnostische gegevens over opstarten. Zie de [pagina overzicht](serial-console-overview.md)voor meer informatie.
Web socket is gesloten of kan niet worden geopend. | Mogelijk moet u toegang tot de firewall toevoegen `*.console.azure.com`aan. Een meer gedetailleerde maar meer benadering is het toestaan van firewall toegang tot de [IP-bereiken van het Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653), die regel matig worden gewijzigd.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Azure Serial console voor Linux vm's](./serial-console-linux.md)
* Meer informatie over de [Azure Serial console voor Windows-vm's](./serial-console-windows.md)