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
ms.openlocfilehash: 6f35bd5f28ae1512726c242cf004e1b97f81b1f1
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74776636"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Veelvoorkomende fouten in de Azure seriële console
Er zijn een aantal bekende fouten in de Azure Serial console. Dit is een lijst met deze fouten en de stappen voor het oplossen ervan.

## <a name="common-errors"></a>Algemene fouten

Fout                             |   Oplossing
:---------------------------------|:--------------------------------------------|
"Voor Azure Serial console moeten diagnostische gegevens over opstarten zijn ingeschakeld. Klik hier om diagnostische gegevens over opstarten voor uw virtuele machine te configureren. ![Diagnostische gegevens over opstarten](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Zorg ervoor dat [Diagnostische gegevens over opstarten](boot-diagnostics.md) zijn ingeschakeld voor de virtuele machine of de VM-schaalset. Als u seriële console gebruikt op een exemplaar van een virtuele-machine schaalset, moet u ervoor zorgen dat uw exemplaar het meest recente model heeft.
"Voor Azure Serial console moet een virtuele machine worden uitgevoerd. Gebruik de knop Start hierboven om de virtuele machine te starten. ![Fout bij toewijzing ongedaan gemaakt](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | De VM of het exemplaar van de virtuele-machine schaalset moet de status gestart hebben om toegang te krijgen tot de seriële console (uw VM mag niet worden gestopt of gespreid). Zorg ervoor dat de virtuele machine of het exemplaar van de VM-schaalset wordt uitgevoerd en probeer het opnieuw.
' Azure Serial console is niet ingeschakeld voor dit abonnement, neemt u contact op met de abonnements beheerder om in te scha kelen. ' ![Fout bij het uitschakelen van het abonnement](./media/virtual-machines-serial-console/virtual-machines-serial-console-subscription-disabled-error.png) | De seriële Azure-console kan worden uitgeschakeld op abonnements niveau. Als u een abonnements beheerder bent, kunt u [de Azure Serial console inschakelen en uitschakelen](./serial-console-enable-disable.md). Als u geen abonnements beheerder bent, kunt u contact met de beheerder van uw abonnement vinden voor de volgende stappen.
Er is een ' verboden ' antwoord aangetroffen bij het openen van het opslag account voor diagnostische gegevens over opstarten van deze virtuele machine. ![Firewall fout van opslag account](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Zorg ervoor dat diagnostische gegevens over opstarten geen account firewall hebben. Er is een toegankelijke opslag account voor diagnostische gegevens over opstarten nodig om de seriële console te laten functioneren. Seriële console per ontwerp kan niet werken met firewalls voor opslag accounts die zijn ingeschakeld in het opslag account voor diagnostische gegevens over opstarten.
U beschikt niet over de vereiste machtigingen om deze virtuele machine met de seriële console te gebruiken. Zorg ervoor dat u ten minste machtigingen voor de rol Inzender voor virtuele machines hebt.| Voor toegang tot de seriële console hebt u toegang op Inzender niveau of hoger nodig op uw VM of virtuele-machine schaalset. Zie de [pagina overzicht](serial-console-overview.md)voor meer informatie.
Het opslag account dat wordt gebruikt voor diagnostische gegevens over opstarten op deze VM is niet gevonden. Controleer of de diagnostische gegevens over opstarten zijn ingeschakeld voor deze VM. dit opslag account is niet verwijderd en u hebt toegang tot dit opslag account. | Controleer of u het opslag account voor diagnostische gegevens over opstarten niet hebt verwijderd voor uw VM of virtuele-machine schaalset
Het inrichten voor deze VM is nog niet voltooid. Controleer of de virtuele machine volledig is geïmplementeerd en voer de verbinding met de seriële console opnieuw uit. | Uw VM of schaalset voor virtuele machines kan nog steeds worden ingericht. Wacht enige tijd en probeer het opnieuw.
U beschikt niet over de vereiste machtigingen om te schrijven naar het opslag account voor diagnostische gegevens over opstarten voor deze VM. Zorg ervoor dat u ten minste over de VM-Inzender machtigingen beschikt voor. | Voor Seriële console toegang is toegang op Inzender niveau vereist voor het opslag account voor diagnostische gegevens over opstarten. Zie de [pagina overzicht](serial-console-overview.md)voor meer informatie.
Kan de resource groep niet bepalen voor het opslag account voor diagnostische gegevens over opstarten *&lt;STORAGEACCOUNTNAME&gt;* . Controleer of de diagnostische gegevens over opstarten zijn ingeschakeld voor deze VM en of u toegang tot dit opslag account hebt. | Voor Seriële console toegang is toegang op Inzender niveau vereist voor het opslag account voor diagnostische gegevens over opstarten. Zie de [pagina overzicht](serial-console-overview.md)voor meer informatie.
Er is een fout opgetreden tijdens de verbinding met de seriële console met de virtuele machine: ' ongeldige aanvraag ' (400) | Dit kan gebeuren als de URI van de opstart diagnose onjuist is. Zo is bijvoorbeeld ' http://' gebruikt in plaats van ' https://'. De diagnostische URI voor opstarten kan worden opgelost met deze opdracht: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
De WebSocket is gesloten of kan niet worden geopend. | Mogelijk moet u firewall toegang toevoegen aan `*.console.azure.com`. Een meer gedetailleerde maar meer benadering is het toestaan van firewall toegang tot de [IP-bereiken van het Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653), die regel matig worden gewijzigd.
Seriële console werkt niet met een opslag account met behulp van Azure Data Lake Storage Gen2 met hiërarchische naam ruimten. | Dit is een bekend probleem met hiërarchische naam ruimten. Als u wilt beperken, moet u ervoor zorgen dat het opslag account voor diagnostische gegevens over opstarten van de virtuele machine niet is gemaakt met behulp van Azure Data Lake Storage Gen2. Deze optie kan alleen worden ingesteld bij het maken van een opslag account. Mogelijk moet u een afzonderlijk opslag account voor diagnostische gegevens over opstarten maken zonder dat Azure Data Lake Storage Gen2 ingeschakeld om dit probleem te verhelpen.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Azure Serial console voor Linux vm's](./serial-console-linux.md)
* Meer informatie over de [Azure Serial console voor Windows-vm's](./serial-console-windows.md)