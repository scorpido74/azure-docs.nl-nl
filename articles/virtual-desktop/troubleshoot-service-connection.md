---
title: Problemen met de service verbinding oplossen Windows virtueel bureau blad-Azure
description: Problemen oplossen bij het instellen van client verbindingen in een Windows Virtual Desktop-Tenant omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127461"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtual Desktop service-verbindingen

Gebruik dit artikel voor het oplossen van problemen met Windows-client verbindingen met virtueel bureau blad.

## <a name="provide-feedback"></a>Feedback geven

U kunt ons feedback geven en de Windows Virtual Desktop-service bespreken met het product team en andere Active community-leden op de [technische community van het Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Gebruiker maakt verbinding, maar er wordt niets weer gegeven (geen feed)

Een gebruiker kan Extern bureaublad-clients starten en kan worden geverifieerd, maar de gebruiker ziet geen pictogrammen in de feed voor webdetectie.

Controleer of de gebruiker die de problemen rapporteert, is toegewezen aan toepassings groepen met behulp van deze opdracht regel:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Controleer of de gebruiker zich aanmeldt met de juiste referenties.

Als de WebClient wordt gebruikt, controleert u of er geen problemen met de referenties in de cache zijn.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 Enter prise-virtuele machines voor meerdere sessies reageren niet

Als een virtuele machine niet reageert en u geen toegang via RDP hebt, moet u deze met de diagnostische functie oplossen door de status van de host te controleren.

Voer de volgende cmdlet uit om de status van de host te controleren:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Als de status van de host `NoHeartBeat`is, betekent dit dat de virtuele machine niet reageert en de agent niet kan communiceren met de virtueel-bureaublad service van Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Er zijn enkele dingen die u kunt doen om de status van de HeartBeat te herstellen.

### <a name="update-fslogix"></a>FSLogix bijwerken

Als uw FSLogix niet up-to-date is, met name als het versie 2.9.7205.27375 van frxdrvvt. sys is, kan dit leiden tot een deadlock. Zorg ervoor dat u [FSLogix bijwerkt naar de nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>BgTaskRegistrationMaintenanceTask uitschakelen

Als het bijwerken van FSLogix niet werkt, is het probleem mogelijk dat een BiSrv-onderdeel systeem bronnen uitgeput tijdens een wekelijkse onderhouds taak. Schakel de onderhouds taak tijdelijk uit door de BgTaskRegistrationMaintenanceTask uit te scha kelen met een van de volgende twee methoden:

- Ga naar het menu Start en zoek naar **taak planner**. Navigeer naar de bibliotheek van de **taak planner** > **micro soft** > **Windows** > **BrokerInfrastructure**. Zoek naar een taak met de naam **BgTaskRegistrationMaintenanceTask**. Wanneer u het bestand hebt gevonden, klikt u er met de rechter muisknop op en selecteert u **uitschakelen** in de vervolg keuzelijst.
- Open een opdracht regel menu als beheerder en voer de volgende opdracht uit:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie [Tenant en hostgroep maken](troubleshoot-set-up-issues.md)voor informatie over het oplossen van problemen bij het maken van een Tenant en een hostgroep in een virtueel-bureaublad omgeving van Windows.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
