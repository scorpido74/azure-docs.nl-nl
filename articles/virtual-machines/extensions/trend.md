---
title: Trend Micro Deep Security installeren op een vm
description: In dit artikel wordt beschreven hoe u Trend Micro-beveiliging installeert en configureert op een VM die is gemaakt met het klassieke implementatiemodel in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: cffd2eab3a616b4d16d847d0f2e1a26655f40459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919920"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Trend Micro Deep Security as a Service installeren en configureren op een Windows VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
In dit artikel ziet u hoe u Trend Micro Deep Security as A Service installeert en configureert op een nieuwe of bestaande virtuele machine (VM) met Windows Server. Deep Security as a Service omvat bescherming tegen malware, een firewall, een inbraakpreventiesysteem en integriteitsbewaking.

De client wordt geïnstalleerd als beveiligingsextensie via de VM-agent. Op een nieuwe virtuele machine installeert u de Deep Security Agent, omdat de VM-agent automatisch wordt gemaakt door de Azure-portal.

Een bestaande VM die is gemaakt met de Azure-portal, de Azure CLI of PowerShell, heeft mogelijk geen VM-agent. Voor een bestaande virtuele machine die niet beschikt over de VM Agent, moet u deze eerst downloaden en installeren. Dit artikel behandelt beide situaties.

Als u een huidig abonnement hebt van Trend Micro voor een on-premises oplossing, u dit gebruiken om uw virtuele Azure-machines te beschermen. Als u nog geen klant bent, u zich aanmelden voor een proefabonnement. Zie de Trend Micro-blogpost [Microsoft Azure VM Agent Extension For Deep Security voor](https://go.microsoft.com/fwlink/p/?LinkId=403945)meer informatie over deze oplossing.

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>De Deep Security Agent installeren op een nieuwe virtuele machine

Met de [Azure-portal](https://portal.azure.com) u de beveiligingsextensie Trend Micro installeren wanneer u een afbeelding van de **Marketplace** gebruikt om de virtuele machine te maken. Als u één virtuele machine maakt, is het gebruik van de portal een eenvoudige manier om bescherming toe te voegen tegen Trend Micro.

Als u een item van de **Marketplace** gebruikt, wordt een wizard geopend waarmee u de virtuele machine instellen. U gebruikt het **mes Instellingen,** het derde paneel van de wizard, om de beveiligingsextensie Trend Micro te installeren.  Zie [Een virtuele machine maken met Windows maken in de Azure-portal](../windows/classic/tutorial.md)voor algemene instructies.

Wanneer u het **mes van** de wizard instellingen hebt, gaat u de volgende stappen uitvoeren:

1. Klik **op Extensies**en klik vervolgens op Extensie **toevoegen** in het volgende deelvenster.

   ![De extensie toevoegen][1]

2. Selecteer **Deep Security Agent** in het deelvenster Nieuwe **resource.** Klik in het deelvenster Deep Security Agent op **Maken.**

   ![Deep Security Agent identificeren][2]

3. Voer het **tenant-id-** en **tenantactiveringswachtwoord** voor de extensie in. Optioneel u een **beveiligingsbeleids-id**invoeren. Klik vervolgens op **OK** om de client toe te voegen.

   ![Details van de extensie opgeven][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>De Deep Security Agent installeren op een bestaande VM
Als u de agent op een bestaande virtuele machine wilt installeren, hebt u de volgende items nodig:

* De Azure PowerShell-module, versie 0.8.2 of nieuwer, geïnstalleerd op uw lokale computer. U de versie van Azure PowerShell controleren die u hebt geïnstalleerd met de opdracht **Azure get-module | format-table.** Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor instructies en een koppeling naar de nieuwste versie. Meld u aan bij `Add-AzureAccount`uw Azure-abonnement met behulp van .
* De VM-agent geïnstalleerd op de beoogde virtuele machine.

Controleer eerst of de VM-agent al is geïnstalleerd. Vul de naam van de cloudservice en de naam van de virtuele machine in en voer vervolgens de volgende opdrachten uit op een azure PowerShell-opdrachtprompt op administratorniveau. Vervang alles binnen de aanhalingstekens, inclusief de < en > tekens.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Als u de naam van de cloudservice en de virtuele machine niet kent, voert u **Get-AzureVM** uit om die informatie weer te geven voor alle virtuele machines in uw huidige abonnement.

Als de opdracht **schrijfhost** **True**retourneert, wordt de VM-agent geïnstalleerd. Als dit **Onwaar**wordt geretourneerd, raadpleegt u de instructies en een koppeling naar de download in de Azure-blogpost [VM Agent en Extensies - Deel 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Als de VM-agent is geïnstalleerd, voert u deze opdrachten uit.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Volgende stappen
Het duurt een paar minuten voordat de agent begint te draaien wanneer deze is geïnstalleerd. Daarna moet u Deep Security activeren op de virtuele machine, zodat deze kan worden beheerd door een Deep Security Manager. Zie de volgende artikelen voor aanvullende instructies:

* Trend's artikel over deze oplossing, [Instant-On Cloud Security voor Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* Een [voorbeeld van Windows PowerShell-script](https://go.microsoft.com/fwlink/?LinkId=404100) om de virtuele machine te configureren
* [Instructies](https://go.microsoft.com/fwlink/?LinkId=404099) voor het monster

## <a name="additional-resources"></a>Aanvullende bronnen
[Inloggen op een virtuele machine met Windows Server]

[Azure VM-extensies en -functies]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Inloggen op een virtuele machine met Windows Server]:../windows/classic/connect-logon.md
[Azure VM-extensies en -functies]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
