---
title: Extensie en functies van virtuele Azure-machines
description: Meer informatie over wat Azure VM-extensies zijn en hoe u deze gebruiken met virtuele Azure-machines
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072982"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Extensie en functies van virtuele Azure-machines
Vm-extensies (Azure Virtual Machine) zijn kleine toepassingen die configuratie- en automatiseringstaken na implementatie op Azure VM's bieden, u bestaande afbeeldingen gebruiken en deze vervolgens aanpassen als onderdeel van uw implementaties, zodat u uit de business van aangepaste beeldgebouw.

Het Azure-platform bevat veel extensies die variëren van VM-configuratie, bewaking, beveiliging en hulpprogrammatoepassingen. Uitgevers nemen een toepassing, dan wikkel het in een extensie, en vereenvoudigen de installatie, dus alles wat je hoeft te doen is het verstrekken van verplichte parameters. 

 Er is een grote keuze aan extensies van de eerste en derde partij, als de toepassing in de extensierepository niet bestaat, u de Extensie Aangepast script gebruiken en uw VM configureren met uw eigen scripts en opdrachten.

Voorbeelden van belangrijke scenario's waarvoor extensies worden gebruikt:
* VM-configuratie u Powershell DSC (Desired State Configuration), Chef, Puppet en Custom Script Extensions gebruiken om VM-configuratieagents te installeren en uw VM te configureren. 
* AV-producten, zoals Symantec, ESET.
* VM kwetsbaarheid tool, zoals Qualys, Rapid7, HPE.
* VM- en app-monitoringtooling, zoals DynaTrace, Azure Network Watcher, Site24x7 en Stackify.

Extensies kunnen worden gebundeld met een nieuwe VM-implementatie. Ze kunnen bijvoorbeeld deel uitmaken van een grotere implementatie, toepassingen configureren op VM-voorziening of worden uitgevoerd tegen ondersteunde extensiesystemen na implementatie.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hoe kan ik vinden welke extensies beschikbaar zijn?
U beschikbare extensies bekijken in het VM-blad in de Portal, onder extensies vertegenwoordigt dit slechts een klein bedrag, voor de volledige lijst u de CLI-tools gebruiken, zie [VM-extensies voor Linux ontdekken](features-linux.md) en [VM-extensies voor Windows ontdekken](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hoe kan ik een extensie installeren?
Azure VM-extensies kunnen worden beheerd met de Azure CLI-, Azure PowerShell-sjablonen, Azure Resource Manager-sjablonen en de Azure-portal. Als u een extensie wilt proberen, u naar de Azure-portal gaan, de aangepaste scriptextensie selecteren en vervolgens een opdracht /script doorgeven en de extensies uitvoeren.

Als u dezelfde extensie wilt gebruiken die u in de sjabloon portal by CLI of Resource Manager hebt toegevoegd, raadpleegt u verschillende extensiedocumentatie, zoals [Windows Custom Script Extension](custom-script-windows.md) en Linux Custom Script [Extension](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hoe beheer ik de levenscyclus van extensietoepassingen?
U hoeft niet rechtstreeks verbinding te maken met een virtuele machine om de extensie te installeren of te verwijderen. Aangezien de levenscyclus van Azure-extensietoepassingen buiten de VM wordt beheerd en is geïntegreerd in het Azure-platform, krijgt u ook de geïntegreerde status van de extensie.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Iets anders waar ik aan zou moeten denken voor extensies?
Extensies installeren toepassingen, zoals alle toepassingen zijn er een aantal eisen, voor extensies is er een lijst van ondersteunde Windows en Linux-bestes, en u moet de Azure VM-agents geïnstalleerd. Sommige afzonderlijke VM-extensietoepassingen kunnen hun eigen milieuvereisten hebben, zoals toegang tot een eindpunt.

## <a name="troubleshoot-extensions"></a>Problemen met extensies oplossen

Informatie over probleemoplossing voor elke extensie vindt u in de sectie **Problemen en ondersteuning** in het overzicht voor de extensie. Hier is een lijst met de beschikbare informatie over probleemoplossing:

| Naamruimte | Problemen oplossen |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Azure Monitor Afhankelijkheid voor Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Azure Monitor Afhankelijkheid voor Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Azure-schijfversleuteling voor Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Azure-schijfversleuteling voor Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Aangepast script voor Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Gewenste statusconfiguratie voor Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Gewenste statusconfiguratie voor Windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [NVIDIA GPU Driver Extension voor Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [NVIDIA GPU Driver Extension voor Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Antimalware-extensie voor Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Azure-monitor voor Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Azure-monitor voor Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace voor Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Wachtwoord (VMAccess) opnieuw instellen voor Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Momentopname voor Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Momentopname voor Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Volgende stappen
* Zie [Azure VM-extensies en -functies voor Linux voor](features-linux.md)meer informatie over hoe de Linux-agent en -extensies werken.
* Zie [Azure VM-extensies en -functies voor Windows voor](features-windows.md)meer informatie over hoe de Windows Guest Agent en -extensies werken.  
* Zie Azure Windows Virtual [Machine Agent Overview](agent-windows.md)als u de Windows Guest Agent wilt installeren.  
* Zie [Azure Linux Virtual Machine Agent Overview](agent-linux.md)om de Linux-agent te installeren.  

