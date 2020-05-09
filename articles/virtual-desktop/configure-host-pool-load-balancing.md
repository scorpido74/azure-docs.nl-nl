---
title: Virtuele Windows-bureau blad-taak verdeling configureren-Azure
description: De taakverdelings methode configureren voor een virtuele Windows-desktop omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 447de339d3ceef7aeb1c232605b0e30bbbb1e7d8
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612432"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>De taakverdelingsmethode voor Windows Virtual Desktop configureren

Als u de taakverdelings methode voor een hostgroep configureert, kunt u de virtuele Windows-bureaublad omgeving aanpassen aan uw behoeften.

>[!NOTE]
> Dit is niet van toepassing op een permanente bureau blad-hostgroep, omdat gebruikers altijd een 1:1-koppeling hebben met een sessie-host binnen de hostgroep.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u de instructies in [set up the Windows Virtual Desktop Power shell-module](powershell-module.md) hebt gevolgd om de Power shell-module te downloaden en te installeren en u aan te melden bij uw Azure-account.

## <a name="configure-breadth-first-load-balancing"></a>Gelijkmatige taak verdeling configureren

Breedte-eerste taak verdeling is de standaard configuratie voor nieuwe niet-permanente hostgroepen. Breed-First Load Balancing distribueert nieuwe gebruikers sessies over alle beschik bare sessie-hosts in de hostgroep. Bij het configureren van gelijkmatige taak verdeling kunt u een maximum aantal sessies per sessiehost instellen in de hostgroep.

Voer de volgende Power shell-cmdlet uit om een hostgroep zodanig te configureren dat de taak verdeling breed is zonder de maximale sessie limiet aan te passen:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' 
```

Daarna voert u de volgende cmdlet uit om ervoor te zorgen dat u de taak verdelings methode breed hebt ingesteld: 

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType 

Name             : hostpoolname 
LoadBalancerType : BreadthFirst
```

Voer de volgende Power shell-cmdlet uit om een hostgroep zodanig te configureren dat de taak verdeling breed is en een nieuwe maximale sessie limiet te gebruiken:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Diepte configureren-eerste taak verdeling

Diepte: voor de taak verdeling worden nieuwe gebruikers sessies gedistribueerd naar een beschik bare sessiehost met het hoogste aantal verbindingen, maar de drempel waarde voor de maximale sessie limiet is niet bereikt. Bij het configureren van diepte-eerste taak verdeling moet u een maximum aantal sessies per sessiehost instellen in de hostgroep.

Voer de volgende Power shell-cmdlet uit om een hostgroep te configureren voor het uitvoeren van diepte-eerste taak verdeling:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ### 
```

Voer de volgende cmdlet uit om ervoor te zorgen dat de instelling is bijgewerkt:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit 

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Taak verdeling configureren met de Azure Portal

U kunt ook taak verdeling configureren met de Azure Portal.

Taak verdeling configureren:

1. Meld u aan bij de https://portal.azure.comAzure Portal op. 
2. Zoek en selecteer **Windows Virtual Desktop** onder Services. 
3. Selecteer op de pagina virtueel bureau blad van Windows de optie **groepen hosten**.
4. Selecteer de naam van de hostgroep die u wilt bewerken.
5. Selecteer **Eigenschappen**.
6. Voer de **maximale sessie limiet** in het veld in en selecteer de gewenste **taakverdelings algoritme** voor deze hostgroep in de vervolg keuzelijst.
7. Selecteer **Opslaan**. Hiermee worden de nieuwe instellingen voor taak verdeling toegepast.