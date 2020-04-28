---
title: Virtuele Windows-machines opnieuw implementeren in azure | Microsoft Docs
description: Windows virtual machines opnieuw implementeren in azure om problemen met de RDP-verbinding te verhelpen.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 36af0eeb43fb209ed65f950576f2dc9e97ec3633
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71058629"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Een Windows-virtual machine opnieuw implementeren naar een nieuw Azure-knooppunt
Als u problemen ondervindt met het oplossen van problemen met de probleem oplossing voor Extern bureaublad (RDP) of toegang tot een toepassing op een virtuele Windows-machine (VM), is het mogelijk dat u de VM opnieuw implementeert. Wanneer u een virtuele machine opnieuw implementeert, wordt de VM door Azure afgesloten, wordt de virtuele machine verplaatst naar een nieuw knoop punt in de Azure-infra structuur en wordt deze weer ingeschakeld en worden alle configuratie opties en bijbehorende resources bewaard. In dit artikel wordt beschreven hoe u een virtuele machine opnieuw implementeert met behulp van Azure PowerShell of de Azure Portal.

> [!NOTE]
> Nadat u een virtuele machine opnieuw hebt geïmplementeerd, gaat de tijdelijke schijf verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele netwerk interface, bijgewerkt. 


## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
Zorg ervoor dat u de nieuwste Azure PowerShell 1. x op uw computer hebt geïnstalleerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie.

In het volgende voor beeld wordt de VM `myVM` geïmplementeerd met de naam in `myResourceGroup`de resource groep met de naam:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt met het verbinding maken met uw virtuele machine, vindt u specifieke hulp bij het [oplossen van problemen met RDP-verbindingen](troubleshoot-rdp-connection.md) of [gedetailleerde probleemoplossings stappen voor RDP](detailed-troubleshoot-rdp.md). Als u geen toegang krijgt tot een toepassing die wordt uitgevoerd op uw virtuele machine, kunt u ook [problemen met het oplossen van toepassingen](../windows/troubleshoot-app-connection.md)lezen.

