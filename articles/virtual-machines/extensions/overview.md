---
title: Extensie en functies van virtuele Azure-machines
description: Meer informatie over Azure VM-extensies en hoe u deze kunt gebruiken met Azure virtual machines
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74072982"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Extensie en functies van virtuele Azure-machines
Azure virtual machine-uitbrei dingen zijn kleine toepassingen die configuratie-en automatiserings taken na de implementatie bieden op Azure-Vm's. u kunt bestaande installatie kopieën gebruiken en deze vervolgens aanpassen als onderdeel van uw implementaties, zodat u geen aangepaste installatie kopieën meer hoeft te maken.

Het Azure-platform fungeert als host voor veel uitbrei dingen die variëren van VM-configuratie, bewaking, beveiliging en hulpprogram ma's. Uitgevers maken gebruik van een toepassing en verpakken deze in een uitbrei ding en vereenvoudigen de installatie, zodat u alleen vereiste para meters hoeft op te geven. 

 Er is een grote keuze aan uitbrei dingen van de eerste en derde partij, als de toepassing in de uitbreidings opslagplaats niet bestaat, kunt u de aangepaste script extensie gebruiken en uw virtuele machine configureren met uw eigen scripts en opdrachten.

Voor beelden van belang rijke scenario's voor het gebruik van uitbrei dingen voor:
* VM-configuratie kunt u Power shell DSC (desired state Configuration), chef, puppet en aangepaste script extensies gebruiken om VM-configuratie agenten te installeren en uw virtuele machine te configureren. 
* AV-producten, zoals Symantec, ESET.
* Hulp programma voor VM-beveiligingslek, zoals Qualys, Rapid7, HPE.
* VM-en app-bewakings programma, zoals DynaTrace, Azure Network Watcher, Site24x7 en Stackify.

Uitbrei dingen kunnen worden gebundeld met een nieuwe VM-implementatie. Ze kunnen bijvoorbeeld deel uitmaken van een grotere implementatie, het configureren van toepassingen op de VM-inrichting of het uitvoeren van een ondersteunde uitbrei ding die wordt gebruikt voor de implementatie van systemen.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hoe kan ik vinden welke extensies beschikbaar zijn?
U kunt beschik bare uitbrei dingen weer geven in de VM-Blade in de portal, onder uitbrei dingen. Dit is slechts een kleine hoeveelheid. voor de volledige lijst kunt u de CLI-hulpprogram ma's gebruiken. Zie [VM-extensies detecteren voor Linux](features-linux.md) en [voor het detecteren van VM-extensies voor Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hoe kan ik een uitbrei ding installeren?
Azure VM-extensies kunnen worden beheerd met behulp van de Azure CLI, Azure PowerShell, Azure Resource Manager sjablonen en de Azure Portal. Als u een uitbrei ding wilt proberen, gaat u naar de Azure Portal, selecteert u de aangepaste script extensie, geeft u een opdracht/script door en voert u de uitbrei dingen uit.

Als u dezelfde extensie wilt toevoegen aan de portal die u hebt toegevoegd via CLI of Resource Manager-sjabloon, raadpleegt u de documentatie voor verschillende uitbrei dingen, zoals [Windows aangepaste script extensie](custom-script-windows.md) en [aangepaste script extensie voor Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>De levens cyclus van de extensie toepassing Hoe kan ik beheren?
U hoeft niet rechtstreeks verbinding te maken met een virtuele machine om de uitbrei ding te installeren of verwijderen. Omdat de levens cyclus van de Azure-extensie toepassing buiten de virtuele machine wordt beheerd en is geïntegreerd in het Azure-platform, krijgt u ook de geïntegreerde status van de uitbrei ding.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Iets anders zou ik denken te weten over uitbrei dingen?
Uitbrei dingen installeren toepassingen, zoals toepassingen die aan bepaalde vereisten voldoen, voor uitbrei dingen bevat een lijst met ondersteunde Windows-en Linux-besturings systemen en u moet de Azure VM-agents hebben geïnstalleerd. Sommige individuele VM-extensie toepassingen hebben mogelijk hun eigen omgevings vereisten, zoals toegang tot een eind punt.

## <a name="troubleshoot-extensions"></a>Problemen met extensies oplossen

Informatie voor probleem oplossing voor elke extensie vindt u in de sectie **probleem oplossing en ondersteuning** in het overzicht voor de uitbrei ding. Hier volgt een lijst met de beschik bare informatie over het oplossen van problemen:

| Naamruimte | Problemen oplossen |
|-----------|-----------------|
| micro soft. Azure. monitoring. dependencyagent. dependencyagentlinux | [Azure Monitor afhankelijkheid voor Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| micro soft. Azure. monitoring. dependencyagent. dependencyagentwindows | [Azure Monitor afhankelijkheid voor Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| micro soft. Azure. Security. azurediskencryptionforlinux | [Azure Disk Encryption voor Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| micro soft. Azure. Security. azurediskencryption | [Azure Disk Encryption voor Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| micro soft. compute. customscriptextension | [Aangepast script voor Windows](custom-script-windows.md#troubleshoot-and-support) |
| micro soft. ostcextensions. customscriptforlinux | [Configuratie van de gewenste status voor Linux](dsc-linux.md#troubleshoot-and-support) |
| micro soft. Power shell. DSC | [Configuratie van desired state voor Windows](dsc-windows.md#troubleshoot-and-support) |
| micro soft. hpccompute. nvidiagpudriverlinux | [Uitbrei ding van NVIDIA GPU-stuur programma voor Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| micro soft. hpccompute. nvidiagpudriverwindows | [Uitbrei ding voor NVIDIA GPU-stuur programma voor Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| micro soft. Azure. Security. iaasantimalware | [Uitbrei ding van antimalware voor Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| micro soft. enterprisecloud. monitoring. omsagentforlinux | [Azure Monitor voor Linux](oms-linux.md#troubleshoot-and-support)
| micro soft. enterprisecloud. monitoring. microsoftmonitoringagent | [Azure Monitor voor Windows](oms-windows.md#troubleshoot-and-support) |
| stackify. linuxagent. extension. stackifylinuxagentextension | [Stackify opnieuw traceren voor Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. micro soft. ostcextensions | [Wacht woord opnieuw instellen (VMAccess) voor Linux](vmaccess.md#troubleshoot-and-support) |
| micro soft. Recovery Services. vmsnapshot | [Moment opname voor Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| micro soft. Recovery Services. vmsnapshot | [Moment opname voor Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Volgende stappen
* Zie [Azure VM-extensies en-functies voor Linux](features-linux.md)voor meer informatie over de werking van de Linux-agent en-extensies.
* Zie [Azure VM-extensies en-functies voor Windows](features-windows.md)voor meer informatie over de werking van de Windows-gast agent en-extensies.  
* Zie [overzicht van Azure Windows virtual machine agent voor informatie](agent-windows.md)over het installeren van de Windows-gast agent.  
* Als u de Linux-agent wilt installeren, raadpleegt u [overzicht van Azure Linux Virtual Machine agent](agent-linux.md).  

