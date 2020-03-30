---
title: Windows 7 virtual machines Windows Virtual Desktop - Azure
description: Problemen voor virtuele windows 7-machines (VM's) oplossen in een Windows Virtual Desktop-omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127384"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Problemen met virtuele Windows 7-machines in Windows Virtual Desktop oplossen

Gebruik dit artikel om problemen op te lossen die u ondervindt bij het configureren van virtuele machines (Virtual Desktop-sessiehost) van Windows.

## <a name="known-issues"></a>Bekende problemen

Windows 7 op Windows Virtual Desktops biedt geen ondersteuning voor de volgende functies:

- Gevirtualiseerde toepassingen (RemoteApps)
- Tijdzone omleiding
- Automatische DPI-schaling

Windows Virtual Desktop kan alleen volledige bureaubladen voor Windows 7 virtualiseren.

Hoewel automatische DPI-schaling niet wordt ondersteund, u de resolutie op uw virtuele machine handmatig wijzigen door met de rechtermuisknop op het pictogram in de Extern bureaublad-client te klikken en **Resolutie**te selecteren.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Fout: heeft geen toegang tot de groep Extern bureaublad-gebruiker

Als Windows Virtual Desktop u of de referenties van uw gebruikers niet kan vinden in de groep Extern bureaublad-gebruiker, ziet u mogelijk een van de volgende foutberichten:

- "Deze gebruiker is geen lid van de groep Extern bureaublad-gebruikers"
- "U moet machtigingen krijgen om u aan te melden via Extern bureaublad-services"

Als u deze fout wilt oplossen, voegt u de gebruiker toe aan de groep Extern bureaublad-gebruiker:

1. Open Azure Portal.
2. Selecteer de virtuele machine waarop u het foutbericht hebt gezien.
3. Selecteer **Een opdracht uitvoeren**.
4. Voer de volgende `<username>` opdracht uit met vervangen door de naam van de gebruiker die u wilt toevoegen:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```