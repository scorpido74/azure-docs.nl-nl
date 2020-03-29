---
title: Scripts uitvoeren in een Azure Windows VM
description: In dit onderwerp wordt beschreven hoe u scripts uitvoert in een virtuele Windows-machine
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e10cab5261d6bd970135273bd4632b7c916641de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67476764"
---
# <a name="run-scripts-in-your-windows-vm"></a>Scripts uitvoeren in uw Windows-VM

Als u taken wilt automatiseren of problemen wilt oplossen, moet u mogelijk opdrachten uitvoeren in een vm. Het volgende artikel geeft een kort overzicht van de functies die beschikbaar zijn voor het uitvoeren van scripts en opdrachten binnen uw VM's.

## <a name="custom-script-extension"></a>Aangepaste scriptextensie

De [Custom Script-extensie](../extensions/custom-script-windows.md) wordt voornamelijk gebruikt voor de configuratie na implementatie en software-installatie.

* Scripts downloaden en uitvoeren in virtuele Azure-machines.
* Kan worden uitgevoerd met Azure Resource Manager-sjablonen, Azure CLI, REST API, PowerShell of Azure-portal.
* Scriptbestanden kunnen worden gedownload van Azure-opslag of GitHub of vanaf uw pc worden geleverd wanneer ze worden uitgevoerd vanaf de Azure-portal.
* Voer PowerShell-script uit in Windows-machines en Bash-script in Linux-machines.
* Handig voor configuratie na implementatie, software-installatie en andere configuratie- of beheertaken.

## <a name="run-command"></a>Voer de opdracht  uit

De functie [Opdracht uitvoeren](run-command.md) maakt virtueel machine- en toepassingsbeheer en probleemoplossing met behulp van scripts mogelijk en is beschikbaar, zelfs wanneer de machine niet bereikbaar is, bijvoorbeeld als de gastfirewall de RDP- of SSH-poort niet heeft geopend.

* Scripts uitvoeren in virtuele Azure-machines.
* Kan worden uitgevoerd met [Azure-portal,](run-command.md) [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)of [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Voer snel een script uit en bekijk de uitvoer en herhaal indien nodig in de Azure-portal.
* Script kan direct worden getypt of u een van de ingebouwde scripts uitvoeren.
* Voer PowerShell-script uit in Windows-machines en Bash-script in Linux-machines.
* Handig voor virtueel machine- en toepassingsbeheer en voor het uitvoeren van scripts in virtuele machines die onbereikbaar zijn.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

De [Hybride Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) biedt algemeen machine-, toepassings- en omgevingsbeheer met aangepaste scripts van de gebruiker die zijn opgeslagen in een Automatiseringsaccount.

* Scripts uitvoeren in Azure- en niet-Azure-machines.
* Kan worden uitgevoerd met Azure portal, Azure CLI, REST API, PowerShell, webhook.
* Scripts die zijn opgeslagen en beheerd in een automatiseringsaccount.
* PowerShell-, PowerShell-werkstroom-, Python- of grafische runbooks uitvoeren
* Geen tijdslimiet voor de looptijd van het script.
* Meerdere scripts kunnen gelijktijdig worden uitgevoerd.
* Volledige scriptuitvoer wordt geretourneerd en opgeslagen.
* Werkgeschiedenis beschikbaar voor 90 dagen.
* Scripts kunnen worden uitgevoerd als Lokaal systeem of met door de gebruiker verstrekte referenties.
* Vereist [handmatige installatie](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Seriële console

De [serial console](serial-console.md) biedt directe toegang tot een VM, vergelijkbaar met het hebben van een toetsenbord aangesloten op de VM.

* Opdrachten uitvoeren in virtuele Azure-machines.
* Kan worden uitgevoerd met behulp van een op tekst gebaseerde console naar de machine in de Azure-portal.
* Log in bij de machine met een lokaal gebruikersaccount.
* Handig wanneer toegang tot de virtuele machine nodig is, ongeacht de status van het netwerk of het besturingssysteem van de machine.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende functies die beschikbaar zijn voor het uitvoeren van scripts en opdrachten binnen uw VM's.

* [Aangepaste scriptextensie](../extensions/custom-script-windows.md)
* [Uitvoeren, opdracht](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Seriële console](serial-console.md)
