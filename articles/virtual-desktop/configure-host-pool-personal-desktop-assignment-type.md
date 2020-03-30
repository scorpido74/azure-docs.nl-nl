---
title: Type persoonlijke bureaubladtoewijzing van Windows Virtual Desktop - Azure
description: Het toewijzingstype configureren voor een persoonlijke bureaubladhostgroep van Windows Virtual Desktop.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128286"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Het toewijzingstype voor persoonlijke bureaubladhosttypen configureren

U het toewijzingstype van uw persoonlijke bureaubladhostgroep configureren om uw Windows Virtual Desktop-omgeving aan te passen aan uw behoeften. In dit onderwerp laten we u zien hoe u automatische of directe toewijzing voor uw gebruikers configureren.

>[!NOTE]
> De instructies in dit artikel zijn alleen van toepassing op persoonlijke hostpools op het bureaublad, niet op samengevoegde hostpools, omdat gebruikers in samengevoegde hostpools niet zijn toegewezen aan specifieke sessiehosts.

## <a name="configure-automatic-assignment"></a>Automatische toewijzing configureren

Automatische toewijzing is het standaardtoewijzingstype voor nieuwe persoonlijke bureaubladhostgroepen die zijn gemaakt in uw Windows Virtual Desktop-omgeving. Voor het automatisch toewijzen van gebruikers is geen specifieke sessiehost vereist.

Als u gebruikers automatisch wilt toewijzen, wijst u ze eerst toe aan de persoonlijke groep bureaubladhost, zodat ze het bureaublad in hun feed kunnen zien. Wanneer een toegewezen gebruiker het bureaublad in zijn feed start, claimt hij of zij een beschikbare sessiehost als deze nog geen verbinding heeft gemaakt met de hostgroep, die het toewijzingsproces voltooit.

Download en [importeer de Windows Virtual Desktop PowerShell-module voordat](/powershell/windows-virtual-desktop/overview/) u begint met het downloaden en importeren van de Windows Virtual Desktop PowerShell-module als u dat nog niet hebt gedaan. 

> [!NOTE]
> Controleer of u Windows Virtual Desktop PowerShell-module versie 1.0.1534.2001 of hoger hebt geïnstalleerd voordat u deze instructies volgt.

Voer daarna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Als u een hostgroep wilt configureren om gebruikers automatisch aan VM's toe te wijzen, voert u de volgende PowerShell-cmdlet uit:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Voer de volgende PowerShell-cmdlet uit om een gebruiker toe te wijzen aan de persoonlijke hostgroep voor desktops:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Directe toewijzing configureren

In tegenstelling tot automatische toewijzing moet u de gebruiker bij het gebruik van directe toewijzing zowel aan de persoonlijke bureaubladhostgroep als aan een specifieke sessiehost toewijzen voordat hij verbinding kan maken met hun persoonlijke bureaublad. Als de gebruiker alleen is toegewezen aan een hostgroep zonder sessiehosttoewijzing, hebben ze geen toegang tot bronnen.

Voer de volgende PowerShell-cmdlet uit om een hostgroep te configureren waarvoor gebruikers rechtstreeks moeten worden toegewezen aan sessiehosts:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Voer de volgende PowerShell-cmdlet uit om een gebruiker toe te wijzen aan de persoonlijke hostgroep voor desktops:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Als u een gebruiker wilt toewijzen aan een specifieke sessiehost, voert u de volgende PowerShell-cmdlet uit:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Volgende stappen

Nu u het type persoonlijke bureaubladtoewijzing hebt geconfigureerd, u zich aanmelden bij een Windows Virtual Desktop-client om deze te testen als onderdeel van een gebruikerssessie. Deze volgende twee How-tos vertellen u hoe u verbinding maken met een sessie met behulp van de client van uw keuze:

- [Verbinding maken met de Windows Desktop-client](connect-windows-7-and-10.md)
- [Verbinding maken met de webclient](connect-web.md)
