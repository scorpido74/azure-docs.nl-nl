---
title: Een script toevoegen aan een herstelplan in Azure Site Recovery
description: Meer informatie over het toevoegen van een VMM-script aan een herstelplan voor noodherstel van Hyper V VM's in VMM-clouds.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6902876e066649ae4dff4134fb8cc462f30dd0b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084877"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Een VMM-script toevoegen aan een herstelplan

In dit artikel wordt beschreven hoe u een VMM-script (System Center Virtual Machine Manager) maakt en toevoegt aan een herstelplan in [Azure Site Recovery](site-recovery-overview.md).

Plaats opmerkingen of vragen onder aan dit artikel of op het [Azure Recovery Services-forum.](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="prerequisites"></a>Vereisten

U PowerShell-scripts gebruiken in uw herstelplannen. Als u toegankelijk wilt zijn vanuit het herstelplan, moet u het script schrijven en het script in de VMM-bibliotheek plaatsen. Houd rekening met de volgende overwegingen tijdens het schrijven van het script:

* Zorg ervoor dat scripts try-catch blokken gebruiken, zodat uitzonderingen op een elegante manier worden verwerkt.
    - Als er een uitzondering optreedt in het script, wordt het script niet meer uitgevoerd en wordt de taak weergegeven als mislukt.
    - Als er een fout optreedt, wordt de rest van het script niet uitgevoerd.
    - Als er een fout optreedt wanneer u een ongeplande failover uitvoert, gaat het herstelplan door.
    - Als er een fout optreedt wanneer u een geplande failover uitvoert, wordt het herstelplan gestopt. Repareer het script, controleer of het wordt uitgevoerd zoals verwacht en voer het herstelplan opnieuw uit.
        - De `Write-Host` opdracht werkt niet in een script voor herstelplannen. Als u `Write-Host` de opdracht in een script gebruikt, mislukt het script. Als u uitvoer wilt maken, maakt u een proxyscript waarop het hoofdscript wordt uitgevoerd. Gebruik de ** \> ** opdracht om ervoor te zorgen dat alle uitvoer wordt uitgesluisd.
        - Het script is een tijd uit als het niet binnen 600 seconden terugkeert.
        - Als er iets naar STDERR is geschreven, wordt het script geclassificeerd als mislukt. Deze informatie wordt weergegeven in de details van de uitvoering van het script.

* Scripts in een herstelplan worden uitgevoerd in de context van het VMM-serviceaccount. Controleer of dit account leesmachtigingen heeft voor het externe aandeel waarop het script zich bevindt. Test het script om uit te voeren met hetzelfde niveau van gebruikersrechten als het VMM-serviceaccount.
* VMM-cmdlets worden geleverd in een Windows PowerShell-module. De module wordt geïnstalleerd wanneer u de VMM-console installeert. Als u de module in uw script wilt laden, gebruikt u de volgende opdracht in het script: 

    `Import-Module -Name virtualmachinemanager`

    Zie [Aan de slag met Windows PowerShell en VMM](https://technet.microsoft.com/library/hh875013.aspx)voor meer informatie.
* Zorg ervoor dat u ten minste één bibliotheekserver in uw VMM-implementatie hebt. Het pad voor het delen van de bibliotheek voor een VMM-server bevindt zich standaard lokaal op de VMM-server. De mapnaam is MSCVMMLibrary.

  Als het pad voor het delen van uw bibliotheek op afstand is (of als het \\lokaal is, maar niet wordt gedeeld met MSCVMMLibrary), configureert u het aandeel als volgt met libserver2.contoso.com\share\ als voorbeeld:
  
  1. Open de registereditor en ga naar **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Wijzig de waarde voor **ScriptLibraryPath** in ** \\\libserver2.contoso.com\share\\**. Geef de volledige FQDN op. Geef machtigingen voor de locatie voor delen. Dit is het wortelknooppunt van het aandeel. Ga naar het hoofdknooppunt in de bibliotheek om te controleren of het hoofdknooppunt in VMM wordt geopend. Het pad dat wordt geopend is de wortel van het pad. Dit is het pad dat u moet gebruiken in de variabele.

  1. Test het script met behulp van een gebruikersaccount dat hetzelfde niveau van gebruikersrechten heeft als het VMM-serviceaccount. Met behulp van deze gebruikersrechten wordt gecontroleerd of zelfstandige, geteste scripts op dezelfde manier worden uitgevoerd als in herstelplannen. Stel op de VMM-server het uitvoeringsbeleid als volgt in:

     a. Open de **64-bits Windows PowerShell-console** als beheerder.
     
     b. Voer **de bypass voor het beleid voor uitvoering instellen in**. Zie [De cmdlet Set-ExecutionPolicy gebruiken](https://technet.microsoft.com/library/ee176961.aspx)voor meer informatie .

     > [!IMPORTANT]
     > **Stel alleen de bypass voor set-uitvoeringbeleid** in in de 64-bits PowerShell-console. Als u deze instelt voor de 32-bits PowerShell-console, worden de scripts niet uitgevoerd.

## <a name="add-the-script-to-the-vmm-library"></a>Het script toevoegen aan de VMM-bibliotheek

Als u een VMM-bronsite hebt, u een script maken op de VMM-server. Neem vervolgens het script op in uw herstelplan.

1. Maak in het delen van de bibliotheek een nieuwe map. \<VMM-servernaam>\MSSCVMMLibrary\RPScripts. Plaats de map op de bron- en doelvmm-servers.
1. Maak het script. Geef bijvoorbeeld de naam van het script RPScript. Controleer of het script werkt zoals verwacht.
1. Plaats het script \<in de VMM-servernaam>\MSSCVMMLibrary-map op de bron- en doelvmm-servers.

## <a name="add-the-script-to-a-recovery-plan"></a>Het script toevoegen aan een herstelplan

Nadat u VM's of replicatiegroepen aan een herstelplan hebt toegevoegd en het plan hebt gemaakt, u het script aan de groep toevoegen.

1. Open het herstelplan.
1. Selecteer een item in de lijst **Stappen.** Selecteer vervolgens **Script** of **Handmatige actie**.
1. Geef op of u het script of de actie wilt toevoegen voor of na het geselecteerde item. Als u de positie van het script omhoog of omlaag wilt verplaatsen, selecteert u de knoppen **Omhoog** en **Omlaag verplaatsen.**
1. Als u een VMM-script toevoegt, selecteert u **Failover naar VMM-script**. Voer in **Scriptpad**het relatieve pad naar het aandeel in. Typ bijvoorbeeld **\RPScripts\RPScript.PS1**.
1. Als u een Azure Automation-runbook toevoegt, geeft u het automatiseringsaccount op waarin de runbook zich bevindt. Selecteer vervolgens het Azure-runbook-script dat u wilt gebruiken.
1. Als u ervoor wilt zorgen dat het script werkt zoals verwacht, voert u een testfailover van het herstelplan uit.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het uitvoeren van failovers](site-recovery-failover.md).

