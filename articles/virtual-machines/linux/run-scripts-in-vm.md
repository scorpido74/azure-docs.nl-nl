---
title: Scripts uitvoeren in een Azure Linux VM
description: In dit onderwerp wordt beschreven hoe u scripts in een virtuele machine uitvoert
services: automation
ms.service: virtual-machines-linux
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: article
ms.openlocfilehash: 9dbfb3e76fe1c0e80dd8020b79f4edd6d32a24a3
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758597"
---
# <a name="run-scripts-in-your-linux-vm"></a>Scripts uitvoeren in uw Linux-VM

Als u taken wilt automatiseren of problemen wilt oplossen, moet u mogelijk opdrachten uitvoeren in een vm. Het volgende artikel geeft een kort overzicht van de functies die beschikbaar zijn voor het uitvoeren van scripts en opdrachten binnen uw VM's.

## <a name="custom-script-extension"></a>Aangepaste scriptextensie

De [Custom Script-extensie](../extensions/custom-script-linux.md) wordt voornamelijk gebruikt voor de configuratie na implementatie en software-installatie.

* Scripts downloaden en uitvoeren in virtuele Azure-machines.
* Kan worden uitgevoerd met Azure Resource Manager-sjablonen, Azure CLI, REST API, PowerShell of Azure-portal.
* Scriptbestanden kunnen worden gedownload van Azure-opslag of GitHub of vanaf uw pc worden geleverd wanneer ze worden uitgevoerd vanaf de Azure-portal.
* Voer PowerShell-script uit in Windows-machines en Bash-script in Linux-machines.
* Handig voor configuratie na implementatie, software-installatie en andere configuratie- of beheertaken.

## <a name="run-command"></a>Uitvoeren, opdracht

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

* [Aangepaste scriptextensie](../extensions/custom-script-linux.md)
* [Run-opdracht](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Seriële console](serial-console.md)