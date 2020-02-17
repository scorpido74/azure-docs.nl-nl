---
title: Toewijzings type voor het persoonlijk bureau blad van Windows Virtual Desktop-Azure
description: Het toewijzings type configureren voor een Windows virtueel bureau blad-hostgroep voor persoonlijk bureau blad.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
ms.openlocfilehash: 8065be841b18fa0f8706a3bea3739ee2cb013323
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367598"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Het toewijzings type voor de hostgroep voor persoonlijk bureau blad configureren

U kunt het toewijzings type van uw persoonlijke bureau blad-hostgroep zodanig configureren dat uw Windows Virtual Desktop-omgeving beter aansluit bij uw behoeften. In dit onderwerp wordt uitgelegd hoe u een automatische of directe toewijzing voor uw gebruikers kunt configureren.

>[!NOTE]
> De instructies in dit artikel zijn alleen van toepassing op Personal Desktop-hostgroepen, geen gegroepeerde hostgroepen, omdat gebruikers in gegroepeerde hostgroepen niet zijn toegewezen aan specifieke sessie hosts.

## <a name="configure-automatic-assignment"></a>Automatische toewijzing configureren

Automatische toewijzing is het standaard toewijzings type voor nieuwe Personal Desktop-hostgroepen die zijn gemaakt in uw Windows Virtual Desktop-omgeving. Voor het automatisch toewijzen van gebruikers is geen specifieke sessiehost vereist.

Als u gebruikers automatisch wilt toewijzen, moet u ze eerst toewijzen aan de groep persoonlijke bureau blad-hosts zodat ze het bureau blad in hun feed kunnen zien. Wanneer een toegewezen gebruiker het bureau blad start in hun feed, claimt deze een beschik bare sessiehost als ze nog niet zijn verbonden met de hostgroep, waardoor het toewijzings proces wordt voltooid.

Voordat u begint, moet u [de Power shell-module van Windows virtueel bureau blad downloaden en importeren](/powershell/windows-virtual-desktop/overview/) als u dat nog niet hebt gedaan. 

> [!NOTE]
> Zorg ervoor dat u de Windows Virtual Desktop Power shell-module versie 1.0.1534.2001 of hoger hebt geïnstalleerd voordat u deze instructies volgt.

Daarna voert u de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Als u een hostgroep wilt configureren om gebruikers automatisch toe te wijzen aan Vm's, voert u de volgende Power shell-cmdlet uit:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Voer de volgende Power shell-cmdlet uit om een gebruiker toe te wijzen aan de groep persoonlijke bureau blad-hosts:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Directe toewijzing configureren

In tegens telling tot automatische toewijzing moet u, wanneer u directe toewijzing gebruikt, de gebruiker toewijzen aan de Personal Desktop-hostgroep en een specifieke sessiehost voordat ze verbinding kunnen maken met hun persoonlijke bureau blad. Als de gebruiker alleen is toegewezen aan een hostgroep zonder een sessietime-toewijzing, hebben ze geen toegang tot bronnen.

Voer de volgende Power shell-cmdlet uit om een hostgroep zo te configureren dat deze directe toewijzing van gebruikers aan sessiehost vereist:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Voer de volgende Power shell-cmdlet uit om een gebruiker toe te wijzen aan de groep persoonlijke bureau blad-hosts:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Als u een gebruiker aan een specifieke sessiehost wilt toewijzen, voert u de volgende Power shell-cmdlet uit:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Volgende stappen

Nu u het persoonlijke bureau blad-toewijzings type hebt geconfigureerd, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om het te testen als onderdeel van een gebruikers sessie. In de volgende twee procedures wordt uitgelegd hoe u verbinding maakt met een sessie met de client van uw keuze:

- [Verbinding maken met de Windows-desktop-client](connect-windows-7-and-10.md)
- [Verbinding maken met de webclient](connect-web.md)
