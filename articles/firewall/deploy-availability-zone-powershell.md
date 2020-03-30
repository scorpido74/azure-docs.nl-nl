---
title: Azure Firewall met beschikbaarheidszones implementeren met PowerShell
description: In dit artikel leert u hoe u een Azure Firewall met beschikbaarheidszones implementeert met de Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195914"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Azure Firewall met beschikbaarheidszones implementeren met behulp van Azure PowerShell

Azure Firewall kan tijdens de implementatie worden geconfigureerd om meerdere beschikbaarheidszones te omvatten voor een grotere beschikbaarheid.

Met deze functie worden de volgende scenario's mogelijk:

- U de beschikbaarheid verhogen tot 99,99% uptime. Zie de SLA (Azure Firewall [Service Level Agreement) voor](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)meer informatie. De SLA van 99,99% wordt aangeboden wanneer twee of meer beschikbaarheidszones worden geselecteerd.
- U Azure Firewall ook koppelen aan een specifieke zone alleen om nabijheidsredenen, met behulp van de servicestandaard 99,95% SLA.

Zie [Wat is Azure Firewall voor](overview.md) meer informatie over azure firewall-beschikbaarheidszones?

In het volgende Azure PowerShell-voorbeeld ziet u hoe u een Azure Firewall met beschikbaarheidszones implementeren.

## <a name="create-a-firewall-with-availability-zones"></a>Een firewall maken met beschikbaarheidszones

In dit voorbeeld wordt een firewall ingesteld in de zones 1, 2 en 3.

Wanneer het standaard openbare IP-adres wordt gemaakt, wordt geen specifieke zone opgegeven. Hierdoor wordt standaard een zoneredundant IP-adres gemaakt. Standaard openbare IP-adressen kunnen worden geconfigureerd in alle zones of in één zone.

Het is belangrijk om te weten, want je geen firewall hebben in zone 1 en een IP-adres in zone 2. Maar u een firewall in zone 1 en IP-adres in alle zones, of een firewall en een IP-adres in dezelfde enkele zone voor nabijheid doeleinden.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
