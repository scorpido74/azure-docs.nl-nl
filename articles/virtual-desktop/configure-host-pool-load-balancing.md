---
title: Taakverdeling voor Windows Virtual Desktop configureren - Azure
description: De taakverdelingsmethode configureren voor een Windows Virtual Desktop-omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128305"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>De taakverdelingsmethode voor Windows Virtual Desktop configureren

Als u de taakverdelingsmethode voor een hostgroep configureert, u de Windows Virtual Desktop-omgeving aanpassen aan uw behoeften.

>[!NOTE]
> Dit geldt niet voor een permanente groep hostop je bureaublad, omdat gebruikers altijd een 1:1-toewijzing hebben voor een sessiehost in de hostgroep.

## <a name="configure-breadth-first-load-balancing"></a>Breedte-eerste taakverdeling configureren

Breedte-first load balancing is de standaardconfiguratie voor nieuwe niet-permanente hostpools. Breedte-first load balancing verdeelt nieuwe gebruikerssessies over alle beschikbare sessiehosts in de hostpool. Wanneer u de taakverdeling breedte-eerste configureert, u een maximale sessielimiet per sessiehost instellen in de hostgroep.

Download en importeer eerst [de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) om te gebruiken in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer daarna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Voer de volgende PowerShell-cmdlet uit om een hostgroep te configureren om de taakverdeling voor de breedte-eerste te uitvoeren zonder de maximale sessielimiet aan te passen:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Voer de volgende PowerShell-cmdlet uit om een hostgroep te configureren om de taakverdeling voor breedte-eerste te implementeren en een nieuwe maximale sessielimiet te gebruiken:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Diepte-first load balancing configureren

Diepte-first load balancing distribueert nieuwe gebruikerssessies naar een beschikbare sessiehost met het hoogste aantal verbindingen, maar heeft de maximale sessielimietdrempel niet bereikt. Wanneer u diepte-first load balancing configureert, **moet** u een maximale sessielimiet per sessiehost instellen in de hostgroep.

Voer de volgende PowerShell-cmdlet uit om een hostpool te configureren om eerst een diepte-eerste loadbalancing uit te voeren:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
