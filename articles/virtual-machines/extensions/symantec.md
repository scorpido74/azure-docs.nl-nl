---
title: Symantec-eindpuntbeveiliging installeren op een Windows-vm in Azure
description: Meer informatie over het installeren en configureren van de beveiligingsextensie Symantec Endpoint Protection op een nieuwe of bestaande Azure VM die is gemaakt met het Klassieke implementatiemodel.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: akjosh
ms.openlocfilehash: 63f9441d4df9551405c2ab2bf8c0c67d7de5753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919903"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Symantec Endpoint Protection installeren en configureren op een Windows VM
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

In dit artikel ziet u hoe u de Symantec Endpoint Protection-client installeert en configureert op een bestaande virtuele machine (VM) met Windows Server. Deze volledige client omvat services zoals virus- en spywarebeveiliging, firewall en inbraakpreventie. De client wordt geïnstalleerd als beveiligingsextensie met behulp van de VM-agent.

Als u een bestaand abonnement van Symantec hebt voor een on-premises oplossing, u dit gebruiken om uw virtuele Azure-machines te beschermen. Als u nog geen klant bent, u zich aanmelden voor een proefabonnement. Zie [Symantec Endpoint Protection op het Azure-platform van Microsoft][Symantec]voor meer informatie over deze oplossing. Deze pagina bevat ook links naar licentiegegevens en instructies voor het installeren van de client als u al een klant van Symantec bent.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Symantec-eindpuntbeveiliging installeren op een bestaande virtuele machine
Voordat u begint, hebt u het volgende nodig:

* De Azure PowerShell-module, versie 0.8.2 of hoger, op uw werkcomputer. U de versie van Azure PowerShell controleren die u hebt geïnstalleerd met de opdracht **Azure get-module | format-table.** Zie [Azure PowerShell installeren en configureren][PS]voor instructies en een koppeling naar de nieuwste versie. Meld u aan bij `Add-AzureAccount`uw Azure-abonnement met behulp van .
* De VM-agent die wordt uitgevoerd op de Azure Virtual Machine.

Controleer eerst of de VM-agent al op de virtuele machine is geïnstalleerd. Vul de naam van de cloudservice en de naam van de virtuele machine in en voer vervolgens de volgende opdrachten uit op een azure PowerShell-opdrachtprompt op administratorniveau. Vervang alles binnen de aanhalingstekens, inclusief de < en > tekens.

> [!TIP]
> Als u de naam van de cloudservice en virtuele machine niet kent, voert u **Get-AzureVM** uit om de namen voor alle virtuele machines in uw huidige abonnement weer te geven.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Als de opdracht **schrijfhost** **True**weergeeft, wordt de VM-agent geïnstalleerd. Als er **Onwaar wordt**weergegeven, raadpleegt u de instructies en een koppeling naar de download in de Azure-blogpost [VM Agent en Extensies - Deel 2][Agent].

Als de VM-agent is geïnstalleerd, voert u deze opdrachten uit om de Symantec Endpoint Protection-agent te installeren.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Ga als volgt te werk om te controleren of de beveiligingsextensie symantec is geïnstalleerd en up-to-date is:

1. Log in op de virtuele machine. Zie Aanmelden [bij een virtuele machine met Windows Server voor][Logon]instructies.
2. Klik voor Windows Server 2008 R2 op **Start > Symantec Endpoint Protection**. Typ **Symantec**voor Windows Server 2012 of Windows Server 2012 R2 en klik vervolgens op **Symantec Endpoint Protection**.
3. Breng op het tabblad **Status-Symantec Endpoint Protection** updates toe of start indien nodig opnieuw. **Status**

## <a name="additional-resources"></a>Aanvullende bronnen
[Inloggen op een virtuele machine met Windows Server][Logon]

[Azure VM-extensies en -functies][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
