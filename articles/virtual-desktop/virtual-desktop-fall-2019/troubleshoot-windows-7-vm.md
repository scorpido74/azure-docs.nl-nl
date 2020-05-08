---
title: Windows 7 virtuele machines Windows virtueel bureau blad-Azure
description: Problemen oplossen voor virtuele Windows 7-machines (Vm's) in een virtueel-bureaublad omgeving van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 74f2e22bcc9d75070e4f7af304f92d9c5640ca7a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615277"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Problemen met virtuele Windows 7-machines in Windows Virtual Desktop oplossen

>[!IMPORTANT]
>Deze inhoud is van toepassing op de najaar 2019-release die geen ondersteuning biedt voor Azure Resource Manager virtueel-bureaublad objecten van Windows.

Gebruik dit artikel voor het oplossen van problemen die zich voordoen bij het configureren van de virtuele machines (Vm's) voor virtuele bureau blad-sessies van Windows.

## <a name="known-issues"></a>Bekende problemen

Windows 7 op virtuele Windows-Bureau bladen biedt geen ondersteuning voor de volgende functies:

- Gevirtualiseerde toepassingen (RemoteApps)
- Omleiding van tijd zone
- Automatische DPI-schaling

Virtueel bureau blad van Windows kan alleen volledige Bureau bladen voor Windows 7 virtualiseren.

Automatische DPI-schaling wordt niet ondersteund. u kunt de resolutie op uw virtuele machine hand matig wijzigen door met de rechter muisknop op het pictogram in de Extern bureaublad-client te klikken en **oplossing**te selecteren.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Fout: geen toegang tot de Extern bureaublad gebruikers groep

Als Windows virtueel bureau blad u of de referenties van uw gebruikers in de Extern bureaublad gebruikers groep niet kan vinden, ziet u mogelijk een van de volgende fout berichten:

- "Deze gebruiker is geen lid van de Extern bureaublad gebruikers groep"
- "U moet zijn gemachtigd om u aan te melden via Extern bureaublad-services"

U kunt deze fout oplossen door de gebruiker toe te voegen aan de Extern bureaublad-gebruikers groep:

1. Open Azure Portal.
2. Selecteer de virtuele machine waarop u het fout bericht hebt gezien.
3. Selecteer **een opdracht uitvoeren**.
4. Voer de volgende opdracht uit `<username>` met vervangen door de naam van de gebruiker die u wilt toevoegen:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```