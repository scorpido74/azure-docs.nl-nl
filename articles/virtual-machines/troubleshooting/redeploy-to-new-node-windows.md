---
title: Windows-virtuele machines opnieuw implementeren in Azure | Microsoft Documenten
description: Virtuele machines van Windows opnieuw implementeren in Azure om problemen met rdp-verbindingen te beperken.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058629"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Een Windows-virtual machine opnieuw implementeren naar een nieuw Azure-knooppunt
Als u problemen hebt ondervonden bij het oplossen van problemen met de Verbinding met Extern bureaublad (RDP) of de toegang tot de toegang tot windows-gebaseerde Azure virtual machine (VM), kan het mogelijk zijn om de VM opnieuw te implementeren. Wanneer u een VM opnieuw implementeert, schakelt Azure de VM af, verplaatst u de VM naar een nieuw knooppunt binnen de Azure-infrastructuur en schakelt u deze vervolgens weer in, waarbij u alle configuratieopties en bijbehorende resources behoudt. In dit artikel ziet u hoe u een VM opnieuw implementeert met Azure PowerShell of de Azure-portal.

> [!NOTE]
> Nadat u een vm opnieuw hebt geïmplementeerd, gaat de tijdelijke schijf verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele netwerkinterface bijgewerkt. 


## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
Zorg ervoor dat u de nieuwste Azure PowerShell 1.x op uw machine hebt geïnstalleerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie.

In het volgende voorbeeld `myVM` wordt de VM `myResourceGroup`geïmplementeerd die is vernoemd in de resourcegroep met de naam:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt bij het maken van verbinding met uw vm, u specifieke hulp vinden bij het oplossen van problemen met [RDP-verbindingen](troubleshoot-rdp-connection.md) of [gedetailleerde stappen voor het oplossen van problemen met RDP.If](detailed-troubleshoot-rdp.md)you are a Als u geen toegang hebt tot een toepassing die op uw vm wordt uitgevoerd, u ook [problemen met het oplossen van toepassingen](../windows/troubleshoot-app-connection.md)lezen.

