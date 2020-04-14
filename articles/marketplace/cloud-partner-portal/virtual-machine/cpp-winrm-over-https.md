---
title: Windows Remote Management via HTTPS voor Azure | Azure Marketplace
description: Hier wordt uitgelegd hoe u een door Azure gehoste, op Windows gebaseerde VM configureert, zodat deze op afstand kan worden beheerd met PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: b2a4bb107309894a7180e0a4585cdba6f04d1bee
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273032"
---
# <a name="windows-remote-management-over-https"></a>Windows Remote Management via HTTPS

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een Azure Virtual Machine-aanbieding maken](https://aka.ms/CreateAzureVMoffer) om uw gemigreerde aanbiedingen te beheren.

In dit gedeelte wordt uitgelegd hoe u een vm met Azure-gehoste, Windows-gebaseerde vm configureert, zodat deze op afstand kan worden beheerd en geïmplementeerd met PowerShell.  Om PowerShell-remoting in te schakelen, moet de doel-VM een Https-eindpunt (Windows Remote Management) (WinRM) blootleggen.  Zie [Opdrachten op afstand uitvoeren](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands)voor meer informatie over PowerShell-remoren.  Zie [Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal)voor meer informatie over WinRM.

Als u een VM hebt gemaakt met een van de 'klassieke' Azure-benaderingen, ofwel de Azure Service Manager Portal of de afgeschafte [Azure Service Management API,](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))wordt deze automatisch geconfigureerd met een WinRM-eindpunt.  Als u echter een VM maakt met behulp van een van de volgende 'moderne' Azure-benaderingen, wordt uw VM *niet* geconfigureerd voor WinRM via HTTPS.

- Met behulp van de [Azure-portal](https://portal.azure.com/), meestal vanaf een goedgekeurde basis, zoals beschreven in de sectie [Een VHD-compatibele VHD maken](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [De Azure Resource Manager-sjablonen gebruiken](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- De opdracht Shell Azure PowerShell of Azure CLI gebruiken.  Zie Bijvoorbeeld [Quickstart: Maak een virtuele Windows-machine in Azure met PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) en [Quickstart: maak een virtuele Linux-machine met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Dit WinRM-eindpunt is ook vereist om de certificeringstoolkit uit te voeren voor het onboardingen van de VM, zoals beschreven in [Het certificeren van uw VM-afbeelding.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm)

In tegenstelling, meestal Linux VM's worden op afstand beheerd met behulp van [Azure CLI](https://docs.microsoft.com/cli/azure) of Linux commando's van een SSH console.  Azure biedt ook verschillende alternatieve methoden om scripts uit te [voeren in uw Linux VM.](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm)  Voor complexere scenario's zijn er een aantal automatiserings- en integratieoplossingen beschikbaar voor Vm's op basis van Windows of Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configureren en implementeren met WinRM

Het WinRM-eindpunt voor een VM op basis van Windows kan worden geconfigureerd in twee verschillende fasen van de ontwikkeling:

- Tijdens het maken - tijdens de implementatie van een VM op een bestaande VHD.  Dit is de voorkeursbenadering voor nieuwe aanbiedingen.  Deze aanpak vereist het maken van een Azure-certificaat, met behulp van meegeleverde Azure Resource Manager-sjablonen en het uitvoeren van aangepaste PowerShell-scripts.
- Na implementatie - op een bestaande VM die wordt gehost op Azure.  Gebruik deze aanpak als u al een VM-oplossing hebt geïmplementeerd op Azure en Windows Remote Management hiervoor moet inschakelen.  Deze aanpak vereist handmatige wijzigingen in de Azure-portal en de uitvoering van een script op de doel-VM.


## <a name="next-steps"></a>Volgende stappen
Als u een nieuwe virtuele machine maakt, u WinRM inschakelen tijdens [de implementatie van uw VM vanaf de VHD's.](./cpp-deploy-vm-vhd.md)  Anders kan WinRM worden ingeschakeld in een bestaande VM
