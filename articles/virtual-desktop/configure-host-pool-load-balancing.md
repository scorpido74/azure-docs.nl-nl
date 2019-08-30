---
title: De taakverdelings methode voor virtuele Windows-Bureau bladen configureren-Azure
description: De taakverdelings methode configureren voor een virtuele Windows-desktop omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: e1f1ea10dc68e501cfac7ef0cf0383ce78e8f380
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163754"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>De taakverdelings methode voor virtuele Windows-bureau blad-preview configureren

Als u de taakverdelings methode voor een hostgroep configureert, kunt u de Windows-voorbeeld omgeving voor virtueel bureau blad aanpassen zodat deze beter aansluit bij uw behoeften.

>[!NOTE]
> Dit is niet van toepassing op een permanente bureau blad-hostgroep, omdat gebruikers altijd een 1:1-koppeling hebben met een sessie-host binnen de hostgroep.

## <a name="configure-breadth-first-load-balancing"></a>Gelijkmatige taak verdeling configureren

Breedte-eerste taak verdeling is de standaard configuratie voor nieuwe niet-permanente hostgroepen. Breed-First Load Balancing distribueert nieuwe gebruikers sessies over alle beschik bare sessie-hosts in de hostgroep. Bij het configureren van gelijkmatige taak verdeling kunt u een maximum aantal sessies per sessiehost instellen in de hostgroep.

[Down load en Importeer eerst de Windows Virtual Desktop Power shell-module](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) voor gebruik in uw Power shell-sessie als u dat nog niet hebt gedaan. Daarna voert u de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Voer de volgende Power shell-cmdlet uit om een hostgroep zodanig te configureren dat de taak verdeling breed is zonder de maximale sessie limiet aan te passen:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Voer de volgende Power shell-cmdlet uit om een hostgroep zodanig te configureren dat de taak verdeling breed is en een nieuwe maximale sessie limiet te gebruiken:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Diepte configureren-eerste taak verdeling

Diepte: voor de taak verdeling worden nieuwe gebruikers sessies gedistribueerd naar een beschik bare sessiehost met het hoogste aantal verbindingen, maar de drempel waarde voor de maximale sessie limiet is niet bereikt. Bij het configureren van diepte-eerste taak verdeling **moet** u een maximum aantal sessies per sessiehost instellen in de hostgroep.

Voer de volgende Power shell-cmdlet uit om een hostgroep te configureren voor het uitvoeren van diepte-eerste taak verdeling:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
