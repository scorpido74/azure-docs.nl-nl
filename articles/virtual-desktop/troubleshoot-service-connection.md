---
title: Problemen met de serviceverbinding Windows Virtual Desktop oplossen - Azure
description: Problemen oplossen wanneer u clientverbindingen instelt in een Windows Virtual Desktop-tenantomgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127461"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtual Desktop-serviceverbindingen

Gebruik dit artikel om problemen met Windows Virtual Desktop-clientverbindingen op te lossen.

## <a name="provide-feedback"></a>Feedback geven

U ons feedback geven en de Windows Virtual Desktop Service bespreken met het productteam en andere actieve communityleden van de [Windows Virtual Desktop Tech Community.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Gebruiker maakt verbinding, maar er wordt niets weergegeven (geen feed)

Een gebruiker kan Extern bureaublad-clients starten en kan zich verifiëren, maar de gebruiker ziet geen pictogrammen in de webdetectiefeed.

Controleer met de gebruiksregel of de gebruiker die de problemen rapporteert, aan toepassingsgroepen is toegewezen:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Controleer of de gebruiker zich aanmeldt met de juiste referenties.

Als de webclient wordt gebruikt, bevestigt u dat er geen problemen met referenties in de cache zijn.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Virtuele machines met meerdere sessies van Windows 10 Enterprise reageren niet

Als een virtuele machine niet reageert en u deze niet openen via RDP, moet u deze probleemoplossen met de diagnostische functie door de status van de host te controleren.

Voer deze cmdlet uit om de status van de host te controleren:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Als de status `NoHeartBeat`van de host is, betekent dit dat de VM niet reageert en de agent niet kan communiceren met de Windows Virtual Desktop-service.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Er zijn een paar dingen die je doen om de NoHeartBeat-status op te lossen.

### <a name="update-fslogix"></a>Update FSLogix

Als uw FSLogix niet up-to-date is, vooral als het versie 2.9.7205.27375 van frxdrvvt.sys is, kan dit een impasse veroorzaken. Zorg ervoor dat [fslogix wordt bijgewerkt naar de nieuwste versie.](https://go.microsoft.com/fwlink/?linkid=2084562)

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Onderhoudstaak bgTaskRegistrations uitschakelen

Als het bijwerken van FSLogix niet werkt, kan het probleem zijn dat een BiSrv-component systeembronnen uithaalt tijdens een wekelijkse onderhoudstaak. Schakel de onderhoudstaak tijdelijk uit door de BgTaskRegistrationMaintenanceTask uit te schakelen met een van de twee methoden:

- Ga naar het menu Start en zoek naar **Taakplanner**. Navigeer naar **Taakplannerbibliotheek** > **Microsoft** > **Windows** > **BrokerInfrastructure**. Zoek naar een taak met de naam **BgTaskRegistrationMaintenanceTask**. Wanneer u deze vindt, klikt u er met de rechtermuisknop op en selecteert **u Uitschakelen** in het vervolgkeuzemenu.
- Open een opdrachtregelmenu als beheerder en voer de volgende opdracht uit:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van probleemoplossing voor het oplossen van problemen met Windows Virtual Desktop en de escalatietracks.
- Zie [Tenant en hostpool maken](troubleshoot-set-up-issues.md)als u problemen wilt oplossen tijdens het maken van een tenant en hostpool in een Windows Virtual Desktop-omgeving.
- Zie Configuratie van de [virtuele machine sessiehost](troubleshoot-vm-configuration.md)voor het oplossen van problemen tijdens het configureren van een virtuele machine (VM) in Windows Virtual Desktop.
- Zie [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)voor het oplossen van problemen bij het gebruik van PowerShell met Windows Virtual Desktop.
- Zie [Zelfstudie: Problemen met resourcebeheersjabloonimplementaties](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)oplossen.
