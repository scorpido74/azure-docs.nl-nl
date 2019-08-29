---
title: Extensies en functies van virtuele Azure-machines | Microsoft Docs
description: Meer informatie over Azure VM-extensies en hoe u deze kunt gebruiken met Azure virtual machines
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 8c63df2d9a7e398fb9b67edd3b57a3ba06cbe7a1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084329"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Extensies en functies van virtuele Azure-machines
Azure virtual machine-uitbrei dingen zijn kleine toepassingen die configuratie-en automatiserings taken na de implementatie bieden op Azure-Vm's. u kunt bestaande installatie kopieën gebruiken en deze vervolgens aanpassen als onderdeel van uw implementaties, zodat u het bedrijf van aangepaste installatie kopie maken.

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

## <a name="next-steps"></a>Volgende stappen
* Zie [Azure VM-extensies en-functies voor Linux](features-linux.md)voor meer informatie over de werking van de Linux-agent en-extensies.
* Zie [Azure VM-extensies en-functies voor Windows](features-windows.md)voor meer informatie over de werking van de Windows-gast agent en-extensies.  
* Zie [overzicht van Azure Windows virtual machine agent voor informatie](agent-windows.md)over het installeren van de Windows-gast agent.  
* Als u de Linux-agent wilt installeren, raadpleegt u [overzicht van Azure Linux Virtual Machine agent](agent-linux.md).  

