---
title: Azure Serial Console-fouten | Microsoft Documenten
description: Veelvoorkomende fouten in de Azure Serial Console
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
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060693"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Veelvoorkomende fouten in de Azure Serial Console
Er zijn een aantal bekende fouten in de Azure Serial Console. Dit is een lijst van die fouten en mitigatie stappen voor hen.

## <a name="common-errors"></a>Algemene fouten

Fout                             |   Oplossing
:---------------------------------|:--------------------------------------------|
"Azure Serial Console vereist dat opstartdiagnoses worden ingeschakeld. Klik hier om opstartdiagnostiek voor uw virtuele machine te configureren." | Controleer of de vm- of virtuele machineschaalset [opstartdiagnose](boot-diagnostics.md) heeft ingeschakeld. Als u seriële console gebruikt op een instantie met virtuele machineschaal, moet u ervoor zorgen dat uw instantie het nieuwste model heeft.
"Azure Serial Console vereist dat er een virtuele machine wordt uitgevoerd. Gebruik de knop Start hierboven om uw virtuele machine te starten."  | De vm- of virtuele machineschaalsetinstantie moet in een gestarte status zijn om toegang te krijgen tot de seriële console (uw vm mag niet worden gestopt of worden verplaatst). Zorg ervoor dat uw vm- of virtuele machineschaalsetinstantie wordt uitgevoerd en probeer het opnieuw.
"Azure Serial Console is niet ingeschakeld voor dit abonnement, neem contact op met uw abonnementsbeheerder om in te schakelen." | De Azure Serial Console kan op abonnementsniveau worden uitgeschakeld. Als u een abonnementsbeheerder bent, u [de Azure Serial Console in- en uitschakelen.](./serial-console-enable-disable.md) Als u geen abonnementsbeheerder bent, moet u contact opnemen met uw abonnementsbeheerder voor de volgende stappen.
Een "Verboden" reactie werd ondervonden bij de toegang tot deze VM's boot diagnostische opslag account. | Zorg ervoor dat opstartdiagnoses geen accountfirewall heeft. Een toegankelijk opstartdiagnostisch opslagaccount is noodzakelijk om de seriële console te laten functioneren. Seriële console by design kan niet werken met firewalls voor opslagaccount die zijn ingeschakeld op het opslagaccount voor opstartdiagnostiek.
U beschikt niet over de vereiste machtigingen om deze vm te gebruiken met de seriële console. Zorg ervoor dat u ten minste machtigingen voor de rolvan virtuele machines inzender hebt.| Voor de toegang tot de seriële console moet u toegang hebben tot het niveau van de bijdrage rtribuaf of hoger op uw VM- of virtuele machineschaalset. Zie voor meer informatie de [overzichtspagina.](serial-console-overview.md)
Het opslagaccount '' dat wordt gebruikt voor opstartdiagnostiek op deze VM kon niet worden gevonden. Controleer of opstartdiagnoses is ingeschakeld voor deze vm, dit opslagaccount is niet verwijderd en u hebt toegang tot dit opslagaccount. | Controleer nogmaals of u het opslagaccount voor opstartdiagnostiek voor uw VM- of virtuele machineschaalset niet hebt verwijderd
De seriële consoleverbinding met de VM is fout opgetreden: 'Bad Request' (400) | Dit kan gebeuren als uw boot diagnostiek URI onjuist is. Er is bijvoorbeeld "http://" gebruikt in plaats van "https://". De opstartdiagnostiek URI kan met deze opdracht worden opgelost:`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
U beschikt niet over de vereiste machtigingen om naar het opslagaccount voor opstartdiagnostiek voor deze vm te schrijven. Zorg ervoor dat u ten minste vm-inzendermachtigingen hebt | Toegang tot seriële console vereist toegang op het opslagaccount voor opstartdiagnostiek op basisniveau. Zie voor meer informatie de [overzichtspagina.](serial-console-overview.md)
Kan de brongroep voor het opslagaccount * &lt;STORAGEACCOUNTNAME&gt;* voor opstartdiagnoses niet bepalen . Controleer of opstartdiagnoses zijn ingeschakeld voor deze virtuele machine en of u toegang hebt tot dit opslagaccount. | Toegang tot seriële console vereist toegang op het opslagaccount voor opstartdiagnostiek op basisniveau. Zie voor meer informatie de [overzichtspagina.](serial-console-overview.md)
De inrichting voor deze VM is nog niet gelukt. Zorg ervoor dat de VM volledig is geïmplementeerd en probeer de seriële consoleverbinding opnieuw. | Uw VM- of virtuele machineschaalset is mogelijk nog steeds ingericht. Wacht wat tijd en probeer het opnieuw.
De websocket is gesloten of kan niet worden geopend. | Mogelijk moet u firewalltoegang `*.console.azure.com`toevoegen aan . Een meer gedetailleerde, maar langere aanpak is om firewall toegang tot de [Microsoft Azure Datacenter IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653), die vrij regelmatig veranderen.
Seriële console werkt niet met een opslagaccount met Azure Data Lake Storage Gen2 met hiërarchische naamruimten. | Dit is een bekend probleem met hiërarchische naamruimten. Controleer ervoor dat het opslagaccount voor opstartdiagnostiek van uw VM niet wordt gemaakt met Azure Data Lake Storage Gen2. Deze optie kan alleen worden ingesteld bij het aanmaken van een opslagaccount. Mogelijk moet u een apart opslagaccount voor opstartdiagnostiek maken zonder dat Azure Data Lake Storage Gen2 dit probleem kan beperken.
Er is een fout opgetreden in de seriële consoleverbinding met de VM: 'Verboden'(SubscriptionNotEnabled) - Naam van abonnement niet gedefinieerd, id-abonnement-id \<> is niet ingeschakeld, niet gedefinieerd | Dit probleem kan optreden als het abonnement waarin een gebruiker zijn Cloud Shell-opslagaccount heeft gemaakt, is uitgeschakeld. Om Cloud Shell te beperken en de stappen uit te [voeren die nodig zijn](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) om een back-upaccount voor Cloud Shell in het huidige abonnement opnieuw in te richten.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Azure Serial Console voor Linux VM's](./serial-console-linux.md)
* Meer informatie over de [Azure Serial Console voor Windows VM's](./serial-console-windows.md)