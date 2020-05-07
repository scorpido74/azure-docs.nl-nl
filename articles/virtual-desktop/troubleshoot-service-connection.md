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
ms.openlocfilehash: a6298b3a9c5769b1d82f89956736b451935b2c5d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612636"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtual Desktop service-verbindingen

>[!IMPORTANT]
>Deze inhoud is van toepassing op de lente 2020-update met Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)als u de versie van het Windows-bureau blad van Virtual Desktop 2019 zonder Azure Resource Manager objecten gebruikt.
>
> De Windows Virtual Desktop lente 2020-update is momenteel beschikbaar als open bare preview. Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aangeraden deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Gebruik dit artikel voor het oplossen van problemen met Windows-client verbindingen met virtueel bureau blad.

## <a name="provide-feedback"></a>Feedback geven

U kunt ons feedback geven en de Windows Virtual Desktop-service bespreken met het product team en andere Active community-leden op de [technische community van het Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Gebruiker maakt verbinding, maar er wordt niets weer gegeven (geen feed)

Een gebruiker kan Extern bureaublad-clients starten en kan worden geverifieerd, maar de gebruiker ziet geen pictogrammen in de feed voor webdetectie.

Controleer of de gebruiker die de problemen rapporteert, is toegewezen aan toepassings groepen met behulp van deze opdracht regel:

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

Controleer of de gebruiker zich aanmeldt met de juiste referenties.

Als de WebClient wordt gebruikt, controleert u of er geen problemen met de referenties in de cache zijn.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Windows 10 Enter prise-virtuele machines voor meerdere sessies reageren niet

Als een virtuele machine niet reageert en u geen toegang via RDP hebt, moet u deze met de diagnostische functie oplossen door de status van de host te controleren.

Voer de volgende cmdlet uit om de status van de host te controleren:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -ResourceGroupName <resourcegroupname>| Format-List Name, LastHeartBeat, AllowNewSession, Status
```

Als de status van de `NoHeartBeat`host is, betekent dit dat de virtuele machine niet reageert en de agent niet kan communiceren met de virtueel-bureaublad service van Windows.

```powershell
Name            : 0301HP/win10pd-0.contoso.com 
LastHeartBeat   : 4/8/2020 1:48:35 AM 
AllowNewSession : True 
Status          : Available 

Name            : 0301HP/win10pd-1.contoso.com 
LastHeartBeat   : 4/8/2020 1:45:44 AM 
AllowNewSession : True 
Status          : NoHeartBeat
```

Er zijn enkele dingen die u kunt doen om de status van de HeartBeat te herstellen.

### <a name="update-fslogix"></a>FSLogix bijwerken

Als uw FSLogix niet up-to-date is, met name als het versie 2.9.7205.27375 van frxdrvvt. sys is, kan dit leiden tot een deadlock. Zorg ervoor dat u [FSLogix bijwerkt naar de nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2084562).

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie [omgeving en hostgroep maken](troubleshoot-set-up-issues.md)om problemen op te lossen tijdens het maken van een virtuele Windows-desktop omgeving en-hostgroep in een Windows-omgeving voor virtueel bureau blad.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
