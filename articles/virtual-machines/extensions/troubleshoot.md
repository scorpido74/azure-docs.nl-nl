---
title: Problemen met Windows VM-extensiefouten oplossen
description: Meer informatie over het oplossen van azure Windows VM-extensiefouten
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bc99a9c9e9ff985730ec97dbacd1d7c1de06a45e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073656"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Problemen met Azure Windows VM-extensiefouten oplossen
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Extensiestatus weergeven
Azure Resource Manager-sjablonen kunnen worden uitgevoerd vanuit Azure PowerShell. Zodra de sjabloon is uitgevoerd, kan de status van de extensie worden weergegeven vanuit Azure Resource Explorer of de opdrachtregelgereedschappen.

Hier volgt een voorbeeld:

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

Hier volgt een voorbeeld van uitvoer:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Fouten in extensieoplossend optreden
### <a name="rerun-the-extension-on-the-vm"></a>De extensie op de VM opnieuw uitvoeren
Als u scripts op de VM uitvoert met behulp van aangepaste scriptextensie, u soms een fout tegenkomen waarbij VM is gemaakt, maar het script is mislukt. Onder deze omstandigheden is de aanbevolen manier om te herstellen van deze fout het verwijderen van de extensie en het opnieuw uitvoeren van de sjabloon.
Opmerking: In de toekomst zou deze functionaliteit worden verbeterd om de noodzaak voor het verwijderen van de extensie te verwijderen.

#### <a name="remove-the-extension-from-azure-powershell"></a>De extensie verwijderen uit Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Zodra de extensie is verwijderd, kan de sjabloon opnieuw worden uitgevoerd om de scripts op de VM uit te voeren.

