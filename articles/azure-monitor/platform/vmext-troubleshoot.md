---
title: Problemen met azure log Analytics VM-extensie oplossen
description: Beschrijf de symptomen, oorzaken en oplossing voor de meest voorkomende problemen met de VM-extensie Log Analytics voor Windows- en Linux Azure-VM's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: e16531484505f055c1383aff5adb40518719d98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054583"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Problemen met de Log Analytics VM-extensie in Azure Monitor oplossen
In dit artikel u helpen bij het oplossen van fouten die u ondervinden met de VM-extensie Log Analytics voor virtuele Windows- en Linux-machines die op Microsoft Azure worden uitgevoerd, en worden mogelijke oplossingen voorgesteld om deze op te lossen.

Als u de status van de extensie wilt verifiëren, voert u de volgende stappen uit vanuit de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Klik in de Azure-portal op **Alle services**. Typ **virtuele machines**in de lijst met resources . Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Virtuele machines**.
3. Zoek en selecteer deze in uw lijst met virtuele machines.
3. Klik op de virtuele machine op **Extensies**.
4. Controleer in de lijst of de extensie Log Analytics is ingeschakeld of niet.  Voor Linux wordt de agent vermeld als **OMSAgentforLinux** en voor Windows wordt de agent vermeld als **MicrosoftMonitoringAgent.**

   ![VM-uitbreidingsweergave](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klik op de extensie om details te bekijken. 

   ![Details vm-extensie](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM-extensie oplossen

Als de *VM-extensie microsoft-bewakingsagent* niet wordt geïnstalleerd of rapporteert, u de volgende stappen uitvoeren om het probleem op te lossen.

1. Controleer of de Azure VM-agent correct is geïnstalleerd en werkt met behulp van de stappen in [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * U ook het logboekbestand van de VM-agent bekijken`C:\WindowsAzure\logs\WaAppAgent.log`
   * Als het logboek niet bestaat, is de VM-agent niet geïnstalleerd.
   * [De Azure VM-agent installeren](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. De vm-extensielogboekbestanden van Microsoft Monitoring Agent controleren in`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Zorg ervoor dat de virtuele machine PowerShell-scripts kan uitvoeren
4. Machtigingen op C:\Windows\temp zijn niet gewijzigd
5. De status van de Microsoft Monitoring Agent weergeven door het volgende te typen in een verhoogd PowerShell-venster op de virtuele machine`(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. De setuplogbestanden van microsoft-bewakingsagent controleren in`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Zie [Problemen met Windows-extensies oplossen](../../virtual-machines/extensions/oms-windows.md)voor meer informatie.

## <a name="troubleshooting-linux-vm-extension"></a>Problemen met Linux VM-extensie oplossen
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Als de *Log Analytics-agent voor Linux* VM-extensie niet wordt geïnstalleerd of rapportage, u de volgende stappen uitvoeren om het probleem op te lossen.

1. Als de status van de extensie *onbekend* is, controleert u of de Azure VM-agent is geïnstalleerd en correct werkt door het vm-agentlogboekbestand te controleren`/var/log/waagent.log`
   * Als het logboek niet bestaat, is de VM-agent niet geïnstalleerd.
   * [De Azure VM-agent installeren op Linux VM's](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Voor andere ongezonde statussen controleert u de Log Analytics-agent voor Linux VM-extensie die bestanden inen `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log``/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Als de extensiestatus in orde is, maar gegevens niet worden geüpload, controleert u de Log Analytics-agent voor Linux-logboekbestanden in`/var/opt/microsoft/omsagent/log/omsagent.log`

Zie [problemen met Linux-extensies oplossen voor](../../virtual-machines/extensions/oms-linux.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Problemen met Azure Log Analytics Linux Agent](agent-linux-troubleshoot.md)voor aanvullende richtlijnen voor probleemoplossing met betrekking tot de Log Analytics-agent voor Linux die wordt gehost op computers buiten Azure.  
