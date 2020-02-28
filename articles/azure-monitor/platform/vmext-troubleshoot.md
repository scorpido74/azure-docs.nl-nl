---
title: Problemen met de extensie Azure Log Analytics VM oplossen in Azure Monitor | Microsoft Docs
description: Beschrijf de symptomen, de oorzaken en de oplossing voor de meest voorkomende problemen met de Log Analytics VM-extensie voor Windows en Linux Azure-VM's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 88d76fc0c215653cf732ba7b827d82187d738fd9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658469"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Problemen met de Log Analytics VM-extensie in Azure Monitor oplossen
Dit artikel bevat informatie over het oplossen van fouten die u mogelijk ondervindt met de Log Analytics VM-extensie voor Windows en Linux virtuele machines die worden uitgevoerd op Microsoft Azure en duidt op mogelijke oplossingen om op te lossen.

Als u wilt controleren of de status van de extensie, moet u de volgende stappen uitvoeren vanuit de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Klik in Azure Portal op **Alle services**. Typ **virtuele machines**in de lijst met resources. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **virtuele machines**.
3. In de lijst met virtuele machines, zoek en selecteer deze.
3. Klik op de virtuele machine op **uitbrei dingen**.
4. Controleer of de extensie van Log Analytics is ingeschakeld of niet in de lijst.  Voor Linux wordt de agent vermeld als **OMSAgentforLinux** en voor Windows, de agent wordt vermeld als **MicrosoftMonitoringAgent**.

   ![Weergave van VM-extensie](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klik op de extensie voor meer informatie. 

   ![Details van de VM-extensie](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Oplossen van problemen met Azure Windows VM-extensie

Als de VM-extensie van *micro soft Monitoring Agent* niet wordt geïnstalleerd of gerapporteerd, kunt u de volgende stappen uitvoeren om het probleem op te lossen.

1. Controleer of de Azure VM-agent is geïnstalleerd en correct werkt door de stappen in [KB 2965986](https://support.microsoft.com/kb/2965986#mt1)te gebruiken.
   * U kunt ook het logboek bestand van de VM-agent bekijken `C:\WindowsAzure\logs\WaAppAgent.log`
   * Als het logboek niet bestaat, wordt de VM-agent is niet geïnstalleerd.
   * [De Azure VM-agent installeren](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Bekijk de logboek bestanden van de VM-extensie van micro soft monitoring agent in `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Zorg ervoor dat de virtuele machine kunt u PowerShell-scripts uitvoeren
4. Controleer of de machtigingen op C:\Windows\temp nog niet is gewijzigd
5. Bekijk de status van de micro soft monitoring agent door het volgende te typen in een Power shell-venster met verhoogde bevoegdheden op de virtuele machine `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Bekijk de logboek bestanden voor de installatie van micro soft monitoring agent in `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Zie [Troubleshooting Windows Extensions](../../virtual-machines/extensions/oms-windows.md)(Engelstalig) voor meer informatie.

## <a name="troubleshooting-linux-vm-extension"></a>Linux VM-extensie oplossen
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Als de *log Analytics agent voor Linux* VM-extensie niet wordt geïnstalleerd of gerapporteerd, kunt u de volgende stappen uitvoeren om het probleem op te lossen.

1. Als de extensie status *onbekend* is, controleert u of de Azure VM-agent is geïnstalleerd en goed werkt door het logboek bestand van de VM-agent te controleren `/var/log/waagent.log`
   * Als het logboek niet bestaat, wordt de VM-agent is niet geïnstalleerd.
   * [De Azure VM-agent installeren op virtuele Linux-machines](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Raadpleeg voor andere slechte statussen de Log Analytics agent voor Linux VM extension-logboek bestanden in `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` en `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Als de status van de extensie in orde is, maar de gegevens niet worden geüpload, controleert u de Log Analytics agent voor Linux-logboek bestanden in `/var/opt/microsoft/omsagent/log/omsagent.log`

Zie [Troubleshooting Linux Extensions](../../virtual-machines/extensions/oms-linux.md)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met azure log Analytics Linux-agent oplossen](agent-linux-troubleshoot.md)voor aanvullende richt lijnen voor probleem oplossing met betrekking tot de log Analytics-agent voor Linux gehost op computers buiten Azure.  
